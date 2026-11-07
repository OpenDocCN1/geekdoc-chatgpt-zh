采用语义化标签 header、main、footer 搭建页面骨架，借助 CSS3 完成渐变底色、圆角控件、进度条动效以及封面旋转等视觉表现。

以 HTML5 的 <audio> 作为播放核心，通过 JavaScript 接管播放状态、监听交互事件并即时刷新 UI，像播放时长、进度条位置、状态图标等都同步更新。

整体拆分为三层：负责绘制界面的渲染层、负责业务流程的控制层、负责数据存放的管理层，播放清单以 JSON 形式载入并支持循环模式切换。

页面采用响应式设计，能在电脑和手机上自适应，文字清晰易读，操作区划分明确。若遇到加载失败或播放异常，系统会给出友好提示。整体呈现科技感，代码规范，适合 CodeBuddy 自动生成并配合 AI 持续优化。

图 8-4 示意了借助大语言模型细化提示词的完整流程。

[PRE0]

图 8-4 大语言模型构建详细提示词的过程

**8.2.2**
**CodeBuddy 实战：快速搭起在线音乐播放器**

下面演示如何用 CodeBuddy 完成音乐播放器网页的生成，步骤清晰可复现。

第一步，把准备好的提示词粘贴进 CodeBuddy 并挑选合适的模型，如图 8-5 所示。

[PRE1]

图 8-5 输入提示词并选择模型

提交后 CodeBuddy 进入深度思考阶段，开始产出关键代码。图 8-6 记录了其思考与生成路径，共包含 7 个核心动作：搭建 HTML 结构、撰写 CSS 样式、创建数据管理模块、创建 UI 渲染模块、创建播放器逻辑控制模块、创建应用主模块、添加默认封面图。

[PRE2]

图 8-6 CodeBuddy 生成代码及思考的过程

随后工具按顺序执行这 7 个动作并落地为文件，过程见图 8-7。

[PRE3]

图 8-7 CodeBuddy 执行 7 个关键步骤

最终得到的代码逻辑高度模块化，依赖关系一目了然，如图 8-8 所示，充分体现 CodeBuddy 在工程化组织与系统开发上的优势。

[PRE4]

图 8-8 代码逻辑架构

把生成的项目用 CodeBuddy IDE 打开，index.html 的源码展示在图 8-9。

[PRE5]

图 8-9 index.html 代码

同时还自动产出使用说明文档 README.md，图 8-10 展示了它的内容，涵盖功能清单、结构说明、关键函数与实现要点。

直接在浏览器中打开 index.html 即可预览播放器。示例里填写的音频链接仅为占位，实际使用时需要换成真实可访问的地址。

[PRE6]

图 8-10 使用说明文档

**8.2.3**
**功能演示与核心逻辑拆解**

下面先看播放器实际效果，再剖析关键实现。

1）打开 index.html，主界面如图 8-11。界面简洁，按钮丰富，整体与提示词描述高度一致。

[PRE7]

图 8-11 音乐播放器主界面

2）点击“上一曲”“下一曲”可以直接切换曲目自动播放，见图 8-12。

[PRE8]

图 8-12 单击“下一曲”按钮播放音乐

3）右上角的“月亮”图标可一键切换白昼主题，页面同时提供深夜与白天两种配色，人性化程度高。切换后的效果见图 8-13。

[PRE9]

图 8-13 主界面切换为白天风格

目前播放还无法正常进行，因为示例中的 5 首曲目由大语言模型生成，data.js 里只写了虚拟链接。图 8-14 展示了这些占位链接。

[PRE10]

图 8-14 data.js 代码中的虚拟链接

假设本地 music 文件夹下已存放 5 首本地歌曲，如图 8-15。歌曲均由大语言模型结合贵州旅游元素创作，图 8-16 展示了其中一首《黔行律动》。读者可把 data.js 中的虚拟地址替换为本地文件路径即可。

[PRE11]

▲图 8-15 本地歌曲

[PRE12]

图 8-16 大语言模型原创歌曲《黔行律动》

**8.2.4**
**上下文联动：让播放器读到本地音乐**

本节利用 CodeBuddy 的上下文交互能力，让代码读取本地音源并完成真实播放。

1）在 CodeBuddy 右下角对话框点按“Add Context”关联外部资源，如图 8-17。

[PRE13]

图 8-17 单击“Add Context”按钮

2）打开菜单选择“File & Folders”，如图 8-18。随后关联 data.js 与 player.js，分别对应本地音乐地址与播放逻辑，如图 8-19。

[PRE14]

▲图 8-18 选择“File & Folders”

[PRE15]

图 8-19 选择需要优化的文件

3）输入优化提示词，请求 CodeBuddy 对代码进行改造，如图 8-20。

[PRE16]

图 8-20 输入优化提示词

4）经过思考后，CodeBuddy 完成优化并额外生成测试文件 test-audio.html，图 8-21。可在此页本地校验播放，并给出启动服务与端口建议。

[PRE17]

图 8-21 生成测试代码

[PRE18]

**注意**

代码生成完成后，CodeBuddy 会弹出成功提示，如图 8-22。

[PRE19]

图 8-22 代码生成成功提示

5）打开测试页面，5 首音乐均可正常播放。图 8-23 显示第一首曲目播放至中段的状态。

[PRE20]

图 8-23 测试代码，音乐能正常播放

音乐播放的关键实现见清单 8-2。整体依赖 <audio> 的内置机制完成。具体流程为：

• 通过 document.createElement('audio') 动态创建音频对象，并把 src 指向指定文件，例如 music/music-01.mp3。
• 设置 audio.controls = true，让浏览器自带播放、暂停、音量等控件，用户可直接操作。

底层上，HTML5 <audio> 支持 MP3、WAV、OGG 等格式，核心方法是 audio.play()。用户触发播放时，浏览器调用该方法把音频流送入系统输出。

代码还监听了 canplaythrough 确认可流畅播放，并捕获 error 事件处理失败情况。因此播放逻辑主要归功于 HTML5 音频 API 的 audio.play()。

**清单 8-2** **音乐播放功能的关键代码**
// 测试每个音频文件

audioFiles.forEach((file, index) => {

const testItem = document.createElement('div');

testItem.className = 'test-item';

const testTitle = document.createElement('h3');

testTitle.textContent = `测试文件: ${file}`;

testItem.appendChild(testTitle);

const statusDiv = document.createElement('div');

statusDiv.id = `status-${index}`;

statusDiv.textContent = '检查中...';

testItem.appendChild(statusDiv);

const audio = document.createElement('audio');

audio.controls = true;

audio.src = file;

testItem.appendChild(audio);

resultsDiv.appendChild(testItem);

// 检查文件是否存在

fetch(file, { method: 'HEAD' })

.then(response => {

if (response.ok) {

statusDiv.textContent = '√ 文件存在';

statusDiv.className = 'success';

// 监听音频加载事件

audio.addEventListener('canplaythrough', () => {

const status = document.createElement('div');

status.textContent = '√ 音频可以播放';

status.className = 'success';

testItem.appendChild(status);

});

audio.addEventListener('error', (e) => {

const status = document.createElement('div');

status.textContent = `× 音频加载错误: ${e.message}`;

status.className = 'error';

testItem.appendChild(status);

// 添加详细错误信息

const errorDetails = document.createElement('div');

errorDetails.textContent = `音频错误代码: ${audio.error ? audio.error.code : '未知'}`;

errorDetails.className = 'error';

testItem.appendChild(errorDetails);

});

// 预加载音频

audio.load();

} else {

statusDiv.textContent = '× 文件不存在';

statusDiv.className = 'error';

}

})

.catch(error => {

statusDiv.textContent = `× 检查失败: ${error.message}`;

statusDiv.className = 'error';

});

});

// 检查音乐文件夹下的所有文件

const checkMusicFolder = document.createElement('div');

checkMusicFolder.innerHTML = '<h2>music 文件夹内容检查</h2>';

resultsDiv.appendChild(checkMusicFolder);

fetch('music/', { method: 'GET' })

.then(response => {

if (response.ok) {

const status = document.createElement('div');

status.textContent = '√ music 文件夹可以访问';

status.className = 'success';

checkMusicFolder.appendChild(status);

} else {

const status = document.createElement('div');

status.textContent = '× music 文件夹无法访问';

status.className = 'error';

checkMusicFolder.appendChild(status);

}

})

.catch(error => {

const status = document.createElement('div');

status.textContent = `× 检查失败: ${error.message}`;

status.className = 'error';

checkMusicFolder.appendChild(status);

});

6）继续对话，通过图 8-24 的提示词进一步优化。CodeBuddy 给出如下改进思路：

[PRE21]

图 8-24 优化网页系统代码的提示词及生成内容

• 分析后发现音频加载与播放逻辑是主要痛点，优化播放器代码，改进文件校验与错误处理，详见 player.js 中的播放方法。
• 发现音频事件绑定存在重复，需要优化绑定逻辑避免冲突。
• 优化 app.js 的音频检测流程，提升友好度与错误提示。
• 为验证效果，创建测试脚本 test-player.html 验证播放功能。

具体优化过程见图 8-25。

[PRE22]

图 8-25 优化内容

打开优化后的 index.html，运行效果如图 8-26。本书完整代码建议前往开源地址下载学习。

[PRE23]

图 8-26 成功运行音乐播放器

随机播放、顺序播放、上一曲、下一曲等功能均已正常工作，如图 8-27。

[PRE24]

图 8-27 成功实现“下一曲”按钮功能

优化后的 player.js 代码如图 8-28，可作为播放器逻辑控制模块使用。完整源码请参考配套资源。

[PRE25]

图 8-28 优化后的 player.js 文件代码 [PRE26]

**注意**

开发过程中，若上一项目已生成完毕并需启动新项目，建议新建工程，否则旧代码会被覆盖。请养成及时备份的习惯。

**8.2.5**
**下一步升级方向**

若要进一步提升智能化与体验，可从三方面入手。

1）**引入 AI 推荐**。基于用户播放习惯与历史偏好，结合深度学习实现个性化推荐，构建智能播放生态，并可尝试用大语言模型创作歌曲。

2）**增加语音交互**。借助 Web Speech API 实现语音指令，如“播放下一首”“暂停”，交互更自然。

3）**扩展可视化**。利用 Web Audio API 渲染实时频谱与波形，增强听觉视觉结合。

此外，可接入 CodeBuddy 云端平台，实现跨终端同步与多用户协作编辑，让播放器从单一工具演化为持续进化的 AI 多媒体平台。

## **8.3**
## **本章小结**