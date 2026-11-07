# Claude Code 生产级管控：成本、审计与权限实战

把 Claude Code 从试玩搬进团队流水线，关键不在于它能做什么，而在于你能否看得清、管得住、用得安心。成本要能算，行动要留痕，权限要收紧，危险操作要提前拦住。下面是一套在真实项目里可直接落地的组合拳。

成本与耗时，先量化再优化
先让每次调用都有账目。开启 JSON 输出，解析 total_cost_usd，直接写进日志，后续就能按 PR、按项目做统计。一次审查下来，API 耗时和墙上耗时往往差很多，这个差距就是等待和思考的成本。
[PRE0]

result=$(claude -p "review this PR" --output-format json)

cost=$(echo "$result" | jq -r '.total_cost_usd')

echo "PR_REVIEW_COST: $cost" >> /var/log/claude-costs.log
[PRE1]

Total cost:           $0.55

Total duration (API): 6m 19.7s

Total duration (wall): 6h 33m 10.2s

当你要排查为什么贵、为什么慢时，debug 模式能给出完整时间线：会话 ID、模型版本、每轮 Token 消耗、工具调用的模式与路径、耗时与预估费用，一目了然。需要实时消费事件就用 stream-json 配合 jq，把每次交互变成可订阅的数据流。
[PRE2]

claude --debug -p "列出当前目录的文件"
[PRE3]

[DEBUG] Starting session: abc123

[DEBUG] Model: claude-sonnet-4-6

[DEBUG] Turn 1

[DEBUG] User message: "列出当前目录的文件"

[DEBUG] Tokens: input=150, output=0

[DEBUG] Tool call: Glob

[DEBUG]   pattern: "*"

[DEBUG]   path: "."

[DEBUG] Tool result: Glob

[DEBUG]   files: ["file1.js", "file2.py", ...]

[DEBUG]   duration: 45ms

[DEBUG] Turn 1 complete

[DEBUG] Total tokens: 500

[DEBUG] Estimated cost: $0.0015
[PRE4]

claude -p "分析 src/ 目录的架构问题" --output-format stream-json | jq '.'

工具调用必须可审计
PostToolUse 钩子是最轻量的审计入口。每次工具执行完，把工具名、时间戳、输入参数写进本地审计文件，形成不可篡改的操作轨迹。配合配置文件统一生效，无需改业务代码。
[PRE5]

#!/bin/bash

# .claude/hooks/audit-log.sh

INPUT=$(cat)

TOOL=$(echo "$INPUT" | jq -r '.tool_name')

TIMESTAMP=$(date -u +"%Y-%m-%dT%H:%M:%SZ")

echo "$TIMESTAMP | $TOOL | $(echo "$INPUT" | jq -c '.tool_input')" >> .claude/audit.log

exit 0
[PRE6]

{

    "hooks": {

        "PostToolUse": [{

            "matcher": "*",

            "hooks": [{"type": "command", "command": "bash .claude/hooks/audit-log.sh"}]

        }]

    }

}

权限遵循最小可用原则
CI/CD 里经常只需要只读。明确限定允许的工具 Read、Grep、Glob，并用 --max-turns 封顶轮数，避免漫游。需要执行命令时，不要开放整个 Bash，而是白名单到具体模式，比如只允许 npm run test * 和 npm run lint。
[PRE7]

# 场景 1：CI/CD 流水线中的代码审查（仅需要读取权限）

claude -p "审查 PR 变更" \
  --allowedTools "Read" "Grep" "Glob" \
  --max-turns 5

# 场景 2：允许特定的 Bash 命令模式

claude -p "运行测试并分析结果" \
  --allowedTools "Read" "Grep" "Bash(npm run test *)" "Bash(npm run lint)"

更细粒度的控制用 permissions 配置。allow 列表放必须执行的命令，deny 列表把拉取外网、写环境变量、强制推送等高危行为直接封掉，ask 列表用于需要人工确认的动作，比如普通 git push。
[PRE8]

{

  "permissions": {

    "allow": [

      "Bash(npm run lint)",

      "Bash(npm run test *)",

      "Bash(git commit *)"

    ],

    "deny": [

      "Bash(curl *)",

      "Bash(wget *)",

      "Read(./.env)",

      "Read(./.env.*)",

      "Read(./secrets/**)",

      "Bash(git push --force *)"

    ],

    "ask": [

      "Bash(git push *)"

    ]

  }

}

敏感文件默认拒绝是底线。无论环境如何，.env、secrets 目录、.pem、.key 都应该在 deny 列表里，杜绝误读泄露。
[PRE9]

{

  "permissions": {

    "deny": [

      "Read(.env)",

      "Read(.env.*)",

      "Read(./secrets/**)",

      "Read(**/*.pem)",

      "Read(**/*.key)"

    ]

  }

}

主动拦截危险命令
钩子还能做运行时风控。对 Bash 调用做正则校验，一旦匹配到 rm -rf /、sudo rm、chmod 777、curl | bash 这类高危模式，直接返回 block 并给出原因，从源头阻断事故。
[PRE10]

#!/bin/bash

# .claude/hooks/security-check.sh

INPUT=$(cat)

TOOL=$(echo "$INPUT" | jq -r '.tool_name')

TOOL_INPUT=$(echo "$INPUT" | jq -r '.tool_input')

if [ "$TOOL" = "Bash" ]; then

    COMMAND=$(echo "$TOOL_INPUT" | jq -r '.command')

    # 正则匹配高危命令模式

    if echo "$COMMAND" | grep -qE 'rm\s+-rf\s+/|sudo\s+rm|chmod\s+777|curl.*\|\s*bash'; then

        echo '{"decision": "block", "reason": "危险命令被安全策略拦截"}' >&2

        exit 2

    fi

fi

exit 0

把成本可观测、行为可审计、权限可收敛、安全可阻断四件事做到位，Claude Code 才能在团队里长期、稳定、可信地运行。