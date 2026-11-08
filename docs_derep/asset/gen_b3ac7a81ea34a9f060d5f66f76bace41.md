[PRE0]

请运行下面这段代码，并把所有返回结果中的回复内容完整展示出来。

[PRE1]

下面是实际的输出结果：

[PRE2]

前面三步已经把一个可用的 AI Agent 角色搭建完整。不过当前写法存在明显局限：所有配置都是固定的，无法传入不同的主题来驱动检索。我们希望让 Agent 能接收外部主题参数，针对指定方向进行网络搜集。

于是我们将流程封装成 search_crew(topic) 方法，让研究员 Agent 依据传入的 topic 主动在网上检索信息并完成汇总。具体实现如下。

from crewai import Agent, Task, Crew, Process

def search_crew(topic):

    researcher = Agent(

        role = "Internet Research",

        goal = f"Perform research on the {topic}, and find and explore about {topic}.",

        verbose = True,

        llm=llm,

        backstory = """You are an expert Internet Researcher

        Who knows how to search the internet for detailed content on {topic}

        Include any code examples with documentation"""

    )

    task_search = Task(

        description="""Search for all the details about the {topic}

        Your final answer MUST be a consolidated content that can be used for blogging

        This content should be well organized, and should be very easy to read""",

        expected_output='A comprehensive 10000 words information about {topic}',

        tools=[search_tool],

        agent=researcher)

    crew = Crew(

        agents=[researcher],

        tasks=[task_search],

        verbose=True

    )

    result = crew.kickoff()

    return result

调用下面代码即可启动刚才定义的主题搜索流程。

[PRE3]

运行后的日志输出如下：

2024-08-14 14:51:53,709 - 29528 - __init__.py:__init__:531 - WARNING: Overriding of current TracerProvider is not allowed

[2024-08-14 14:51:53][DEBUG]: == Working Agent: Internet Research