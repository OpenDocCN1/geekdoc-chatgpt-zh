[PRE0]

图 3-4 程序运行后桌面示意

桌面上的零碎文件已按后缀名完成自动归类收纳。这段脚本支持重复使用，不必每次新增文件就手动归位，只要在需要整理桌面时运行一次即可，操作是不是很省事。

## 3.2 资料收集自动化：AI 编程帮你一键抓取网络文档

在信息井喷的当下，获取资料的渠道越来越多，但怎么高效筛选、归档、保存有价值的内容仍是难题。若每天花大量时间去搜索和整理，不仅效率低下，还容易遗漏关键信息。

有没有更聪明的解决方式？当然有。下面将介绍如何借助 AI 编程实现网络文档的自动化抓取，让资料采集更高效、更省心。

### 3.2.1 用 AI 编程提升抓取效率

数字化环境里，学习、工作和兴趣拓展都离不开最新资料的获取。互联网上充斥着在线课程、电子书、论文、行业报告等海量内容，靠手工检索整理无异于用勺子舀干一汪湖水，耗时又令人崩溃。

AI 编程正好可以补位，它就像一位资料管理的助手，帮你完成抓取、分类和存储。其核心价值主要体现在以下三点。

**1）效率倍增：**自动化脚本几分钟就能完成手工数小时甚至数天的工作量。
**2）精准可靠：**程序逻辑严谨，杜绝人为失误，资料库始终保持最新。
**3）灵活通用：**可按需求定制脚本，适用于教育、科研、商业等不同场景。

以市场专员小李为例，他用 Python 写了个抓取脚本，自动采集行业报告和相关文章，并实现实时更新。过去要耗费大量时间手动检索，现在整理好的资料会自动推送到眼前，工作效率大幅提升，也腾出了时间去做更重要的决策。

### 3.2.2 案例：自动抓取网络文档

本案例演示 AI 编程自动抓取网络文档的完整流程，如图 3-5 所示。

[PRE1]

图 3-5 流程示意

下面具体说明操作要点。

**1. 梳理向 AI 提问的需求**

先把需求清晰地告诉 AI。可以围绕背景、目标、现状与挑战，以及对 AI 回答格式的要求进行结构化整理。

**(1) 背景问题。**
· 作为刚入职的市场专员，我每天需大量阅读【新闻和行业报告】，以跟踪市场动态。
· 面对海量信息源，【手动搜索和整理资料】不仅【费时】，还容易【遗漏关键信息】。

**(2) 目标需求。**
· 我希望借助【AI 编程】搭建一个【自动抓取工具】，通过预设【关键词】从指定网站【自动抓取相关文章和报告】。
· 因本人无编程基础，希望获得【简化步骤指导】，能【用 Python 脚本实现自动抓取功能】。

**(3) 现状与挑战。**
· 每天需在【多个网站手动查找并整理资料】，【效率低】，【难以满足对特定行业信息的精细需求】。
· 市面上新闻应用和订阅服务虽多，仍无法提供真正【定制化信息】。

**(4) 具体要求。**
请以【1、2、3 步骤】形式，【简明清晰】地说明如何通过 Python 脚本【自动抓取网络文档】，并用【关键词筛选】获取相关内容。

**2. 执行 AI 回答中的步骤**

按 AI 给出的方案实践即可。

**(1) 安装必要软件与库。**
安装 Python 可略过，有需要可参考前文。
安装依赖库：打开命令提示符或终端，执行如下命令安装所需 Python 库。

Bash
pip install requests beautifulsoup4 pandas openpyxl

因库已提前安装，此处直接显示版本信息，如图 3-6 所示。

[PRE2]

图 3-6 程序结果示意

**(2) 创建 Python 脚本。**
先打开文本编辑器，复制并粘贴 AI 提供的 Python 代码。该代码会依据预设关键词从指定站点抓取文章和报告。

## Python
import requests
from bs4 import BeautifulSoup
import pandas as pd
from urllib.parse import urlencode, urljoin
BASE_URL = "https://▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓"
KEYWORDS = ["市场动态", "行业报告"]
def fetch_articles(keyword):
    url = f"{BASE_URL}?{urlencode({'tn': 'news', 'word': keyword})}"
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')
    return [
        {
            '标题': item.select_one('a.c-title').get_text(strip=True),
            '链接': urljoin(BASE_URL, item.select_one('a.c-title')['href']),
            '摘要': item.select_one('p.c-summary').get_text(strip=True) if item.select_one('p.c-summary') else 'N/A'
        }
        for item in soup.select('li.c-single-item')
    ]
all_articles = [article for kw in KEYWORDS for article in fetch_articles(kw)]
pd.DataFrame(all_articles).to_excel("output.xlsx", index=False) if all_articles else print("未找到相关文章。")

随后根据目标网站修改 base_url 与选择器。例如抓取百度新闻保留当前设置，若换站则需查看 HTML 结构并调整选择器。

最后将文件保存为 fetch_articles.py。

**3. 运行文档抓取脚本**

定位到脚本所在目录后执行抓取。操作步骤如下。

**(1) 打开命令提示符或终端**，切换到保存 fetch_articles.py 的位置。可使用 cd 命令，如“cd desktop/秒懂 AI/第三章/3.2”，如图 3-7 所示。

[PRE3]

图 3-7 文件导航示意

**(2) 输入命令运行脚本。**

Bash
python fetch_articles.py

执行完成后，可在指定位置找到生成的新 Excel 文件，其中包含抓取的文档信息，如图 3-8 所示。

[PRE4]

图 3-8 程序运行结果示意

运行成功，程序输出如图 3-9 所示。

[PRE5]

图 3-9 程序运行成功示意

使用注意点有两条。
· 首次运行前请备份 Excel 文件，避免有用数据被覆盖。
· 确保具备从目标网站抓取数据的权限，并遵守其服务条款与版权规定，部分站点禁止未经许可的数据抓取。

## 3.3 用 AI 编程快速提炼文章精华与关键词

信息量持续攀升时，如何迅速抓住文章核心成为刚需。AI 技术的发展为此提供了高效解法——自动提炼摘要与关键词。下面将深入探讨 AI 编程在该场景的应用，并说明如何在实际中借助工具提升效率。

### 3.3.1 AI 助力多领域：三大场景智能升级

在工作和学习中，AI 编程自动提炼摘要已成为提效利器。教师评审论文、学生备考、企业分析市场，均能发挥明显作用。

**(1) 教师论文评审。**
教师每学期需审阅大量论文，内容繁杂耗时。借助 AI 编程可快速提取核心论点、研究方法与结论，形成精炼摘要，一眼掌握重点。同时还能以清晰结构呈现关键数据，评审时间大幅缩短，避免信息过载。

**(2) 学生学习助手。**
备考或写报告时，学生常被海量信息困扰。AI 编程能高效整理资料，精准提炼要点。例如从几十页教材中抽取核心概念、关键公式并自动归纳知识点，帮助快速理解难点。对临时冲刺的同学而言，相当于多了一位高质量私教，复习周期缩短，效果提升。

**(3) 运营者实时热点分析。**
市场瞬息万变，品牌管理者需要持续关注趋势并快速调整策略。AI 编程可实时分析社交媒体、行业报告与用户反馈，提供数据驱动的决策支持。它能自动监测热门话题、洞察竞品动态、预测消费趋势，助力企业迅速调整营销方向，抢占先机。

即便不懂编程，也能借助 Python 脚本或在线服务实现文本读取、摘要生成与关键词提取，让工作更智能、错误更少。

### 3.3.2 案例：自动提炼学习资料的核心内容

本案例介绍如何通过 AI 编程自动提炼学习资料的核心要点，流程如图 3-10 所示。

[PRE6]

图 3-10 流程示意

假设一个场景：学生面对大量学习资料，手动整理要点既耗时又易遗漏，尤其在备考期，效率尤为关键，传统笔记与搜索工具已不够用。

此时 AI 编程登场，可快速提炼核心要点与关键词，让复习与写作更有针对性。即便零编程基础，也能轻松完成精准提炼。

借助 AI 编程，学生可迅速定位文献重点，高效整理笔记，为考试与报告做好准备，从而节省时间、提升成绩，告别熬夜复习。

**1. 梳理向 AI 提问的问题**

将需求结构化后向 AI 提问，包含背景、目标、现状与挑战及格式要求。

**(1) 背景问题。**
· 作为一名学生，我每天需处理大量【学习资料和研究文献】，尤其在考试或写报告时，信息过载难以高效整理。
· 手工查找与整理关键内容【费时费力】，易【遗漏重要信息】，备考期间时间紧张，效率至关重要。

**(2) 目标需求。**
· 希望有一种【高效且智能的方法】，能【快速提炼学习资料中的核心要点和关键词】，短时间内掌握大量信息。
· 使复习与写作更有【针对性】，提高效率。

**(3) 现状与挑战。**
· 目前依赖【传统笔记】或简单搜索工具整理资料，【效率低下】，难以应对信息过载。
· 手工筛选【耗时】，易【遗漏关键内容】，影响学习效果，且传统方式灵活性不足，难以满足个性化需求。

**(4) 具体要求。**
请以【1、2、3 步骤】形式，【简明清晰】地说明如何【实现自动提炼资料中的核心要点和关键词】，即使我没有编程基础。

**2. 执行 AI 回答中的步骤**

按 AI 指导操作。

**(1) 安装必要软件与库。**
安装 Python 可跳过。
在命令提示符或终端执行以下命令安装依赖库。

Bash
pip install nltk spacy pytextrank pandas openpyxl
python -m spacy download zh_core_web_sm
python -m spacy download en_core_web_sm

程序运行结果如图 3-11 和图 3-12 所示。

[PRE7]

▲图 3-11 程序结果示意（1）

[PRE8]

▲图 3-12 程序结果示意（2）

**(2) 创建 Python 脚本。**
打开文本编辑器，复制并粘贴以下 Python 代码。该代码会读取学习资料，提取核心内容并生成关键词与摘要。

## Python
import spacy
from spacy import displacy
from collections import Counter
import en_core_web_sm  # 处理英文文本
# import zh_core_web_sm  # 处理中文文本（如适用）
from pytextrank import TextRank
import pandas as pd
def extract_keywords_and_summary(text, lang='en'):
    """
    提取文本的关键词和摘要。
    参数:
    text (str): 要处理的文本
    lang (str): 语言 ('en' 表示英文，'zh' 表示中文)
    返回:
    tuple[list, str]: 关键词列表和摘要字符串
    """
    # 选择合适的 NLP 语言模型
    if lang == 'en':
        nlp = en_core_web_sm.load()
    else:
        raise ValueError("当前仅支持英文 ('en')，如需支持中文，请解开 zh_core_web_sm 的导入注释。")
    # 添加 TextRank 组件进行关键词提取
    tr = TextRank()
    nlp.add_pipe(tr.PipelineComponent, name="textrank", last=True)
    # 处理文本
    doc = nlp(text)
    # 提取前 5 个关键词
    keywords = [phrase.text for phrase in doc._.phrases[:5]]
    # 生成摘要（限制 3 句话）
    summary = " ".join([sent.text for sent in doc._.textrank.summary(limit_sentences=3)])
    return keywords, summary
# 读取文本文件（请替换为你的实际路径）
file_path = "C:\\Users\\YourUsername\\Documents\\study_material.txt"
try:
    with open(file_path, "r", encoding="utf-8") as file:
        text = file.read()
    # 提取关键词和摘要
    keywords, summary = extract_keywords_and_summary(text, lang='en')  # 选择'zh'处理中文
    # 输出结果
    print("关键词:", keywords)
    print("摘要:", summary)
    # 保存结果到 Excel
    output_path = "C:\\Users\\YourUsername\\Documents\\extracted_info.xlsx"
    df = pd.DataFrame({'关键词': keywords, '摘要': [summary]})
    df.to_excel(output_path, index=False)
    print(f"结果已保存至 {output_path}")
except FileNotFoundError:
    print(f"错误：未找到文件 {file_path}")
except Exception as e:
    print(f"处理过程中发生错误: {e}")

将代码保存至对应文件夹。“/Users/YourUsername/Documents/extracted_info.xlsx” 为示例路径，请改为自己的路径。

将文件命名为 extract_info.py，确保后缀为 .py。

**3. 运行自动提炼文章摘要与关键词脚本**

定位脚本目录后执行。

**(1) 打开命令提示符或终端**，导航至保存 extract_info.py 的位置，如图 3-13 所示。可使用 cd 命令，如“cd Desktop/秒懂 AI/第三章/3.3”。

Bash
cd Desktop/秒懂 AI/第三章/3.3

[PRE9]

图 3-13 文件导航示意

**(2) 输入命令运行脚本。**

Bash
python extract_info.py