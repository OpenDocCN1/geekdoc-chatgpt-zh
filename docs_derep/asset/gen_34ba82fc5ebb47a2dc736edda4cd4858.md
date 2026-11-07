[PRE0]

# Conda 免激活执行方案

sanitized_command = self._sanitize_command(command)

# 拼接在 Conda 环境中执行的命令模板
# 借助 conda run -n env_name command 实现不进入环境直接运行

conda_command = (f"conda run -n {shlex.quote(env_name)}"

                 f"{sanitized_command}")

return await self.execute(conda_command)

async def _handle_cd_command(self, command: str) -> CLIResult:

    """
    解析 cd 指令并更新当前工作路径。

    参数：

        command (str): 用户输入的 cd 指令字符串。

    返回：

        CLIResult: 包含切换结果的输出对象。
    """

    try:

        parts = shlex.split(command)

        if len(parts) < 2:

            # 未提供路径时默认回到 home 目录
            new_path = os.path.expanduser("~")

        else:

            new_path = os.path.expanduser(parts[1])

        # 对相对路径做转换
        if not os.path.isabs(new_path):

            new_path = os.path.join(self.current_path, new_path)

        new_path = os.path.abspath(new_path)

        if os.path.isdir(new_path):

            self.current_path = new_path

            return CLIResult(

                output=f"已切换到目录 {self.current_path}", error="")

        else:

            return CLIResult(output="", error=f"目录不存在：{new_path}")

    except Exception as e:

        return CLIResult(output="", error=str(e))

@staticmethod

def _sanitize_command(command: str) -> str:

    """
    对输入指令进行清洗，保证执行安全

    参数：

        command (str): 待清洗的原始指令

    返回：

        str: 清洗后的指令
    """

    # 示例：拦截高危指令
    dangerous_commands = ["rm", "sudo", "shutdown", "reboot"]

    try:

        parts = shlex.split(command)

        if any(cmd in dangerous_commands for cmd in parts):

            raise ValueError("禁止使用危险命令。")

    except Exception:

        # shlex 解析失败时做兜底字符串检查
        if any(cmd in command for cmd in dangerous_commands):

            raise ValueError("禁止使用危险命令。")

    # 可在此扩展更多安全校验
    return command

async def close(self):

    # 终止可能存在的后台终端进程
    async with self.lock:

        if self.process:

            self.process.terminate()

            try:

                await asyncio.wait_for(self.process.wait(), timeout=5)

            except asyncio.TimeoutError:

                self.process.kill()

                await self.process.wait()

            finally:

                self.process = None

async def __aenter__(self):

    # 进入异步上下文
    return self

async def __aexit__(self, exc_type, exc_val, exc_tb):

    # 离开上下文时一并关闭终端
    await self.close()

### 10.4.3 计划管理工具

planning.py 提供了面向复杂任务的分步规划能力，核心模块集中在计划的创建与维护上。

主要能力包括：

- 1. 创建计划 create：支持一次性建立包含多步骤的计划，并完成初始状态初始化。
- 2. 更新计划 update：可修改计划标题或步骤内容，同时保留已有步骤的状态。
- 3. 列出所有计划 list：展示当前全部计划及其完成进度概览。
- 4. 获取计划详情 get：返回指定计划的完整信息，含各步骤状态与备注。
- 5. 设置活跃计划 set_active：将某个计划设为当前工作对象，便于后续操作定位。
- 6. 标记步骤状态 mark_step：为步骤更新状态，选项为未开始、进行中、已完成、阻塞，并可附加备注。
- 7. 删除计划 delete：移除指定计划，若删除的是活跃计划则同步清除活跃标记。

每个计划都由唯一的 plan_id、标题、步骤列表以及状态和备注组成。模块内部做了完善的校验与异常处理，对参数合法性进行检查，并在更新时智能保留历史状态。