[PRE0]

**热搜新闻快读插件：DeepSeek驱动的摘要与情绪洞察**

代码实现的核心逻辑可以拆成以下几块：

- **热搜数据获取**：目前用 `fetchTrendingNews()` 做模拟拉取，演示环境下先返回假数据。落地时可直接换成 NewsAPI 等第三方新闻接口，接入真实热榜流。
- **内容精简与情绪判断**：调用 DeepSeek 的 `summarizeNews()` 对长文本做提炼，减轻阅读负担。紧接着对同一篇内容做情绪识别，将倾向归类为积极、消极或中性，让用户快速感知新闻基调。
- **API 调用层**：`DeepSeekAPI` 内部统一通过 `enqueue()` 发起异步请求，分别处理 `SummaryResponse` 和 `SentimentAnalysisResponse` 两种返回类型，保证摘要和情绪分析解耦。
- **界面呈现**：摘要结果写入 `TextView` 直接展示，情绪分析结果可作为 UX 优化的依据，按需显式呈现或用于后端策略。

实际运行效果：

[PRE1]

该方案已把 DeepSeek 接入插件流程，完成从原始新闻到精简摘要、再到情绪标签的全链路闭环。用户能迅速掌握热搜要点并感知其情感导向。借助 DeepSeek 简洁且能力强的接口，新闻处理效率明显提升，使用体验也更顺滑。后续还能在此基础上扩展个性化推荐、推送提醒等功能。

##### 10.3.3 iOS游戏攻略助手插件实战

本节演示如何打造一款基于 iOS 的游戏攻略助手插件。整体思路是借助 DeepSeek API，为玩家提供实时攻略、技巧提炼和策略建议，帮助理解游戏机制、提升游玩体验。插件可抓取游戏相关信息，结合玩家画像做个性化推荐，并据此给出最优策略。核心能力包含：

1. 实时拉取最新游戏攻略素材。
2. 对攻略文本进行解析并输出简短概括。
3. 利用情感分析推断攻略难度及整体情绪倾向。
4. 基于玩家偏好与历史行为推荐相匹配的攻略内容。

**【例 10-6】** 用于 iOS 游戏攻略助手插件。

[PRE2]

## swift

func summarizeAndAnalyzeGameGuide(title: String, content: String) {

    let guide = GameGuide(title: title, content: content)

    // 调用 DeepSeek API 进行摘要

    deepSeekAPI.summarizeContent(content) { summaryResponse in

        if let summary = summaryResponse.summary {

            self.updateUIWithSummary(summary: summary)

            // 进行情感分析

            self.analyzeSentiment(content: content)

        }

    }

}

// 更新 UI 并展示摘要

func updateUIWithSummary(summary: String) {

    DispatchQueue.main.async {

        self.guidetextView.text = summary

    }

}

// 对游戏攻略进行情感分析

func analyzeSentiment(content: String) {

    deepSeekAPI.analyzeSentiment(content) { sentimentResponse in

        if let sentiment = sentimentResponse.sentiment {

            self.updateUIWithSentiment(sentiment: sentiment)

        }

    }

}

// 更新 UI 并展示情感分析结果

func updateUIWithSentiment(sentiment: String) {

    DispatchQueue.main.async {