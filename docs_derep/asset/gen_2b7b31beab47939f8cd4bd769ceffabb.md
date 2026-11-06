async def main(args:Args)->Output:

    params=args.params

    lines=params['input'].strip().split('\n')

    # 初始化两个数组

    male_lines=[]

    female_lines=[]

    # 遍历每一行，根据前缀将内容添加到对应的数组中

    for line in lines:

        if line.startswith('男:'):

            male_lines.append(line[2:])

        elif line.startswith('女:'):

            female_lines.append(line[2:])

    ret:Output={

        "key0":male_lines,

        "key1":female_lines

    }

    return ret

[PRE0]python

async def main(args: Args) -> Output:

    params = args.params

    lines = params['input'].strip().split('\n')

    # 初始化两个数组

    male_lines = []

    female_lines = []

    # 遍历每一行，根据前缀将对白添加到对应的数组中

    for line in lines:

        if line.startswith('男：'):

            male_lines.append(line[2:])

        elif line.startswith('女：'):

            female_lines.append(line[2:])

    ret: Output = {

        "key0": male_lines,

        "key1": female_lines

    }

    return ret

[PRE1]javascript

async function main({params}:Args):Promise<Output>{

    console.log("Input params:",JSON.stringify(params,null,2));

    const mergedUrls = [];

    // 直接使用 params.audio 和 params.audio_1，它们已经是数组

    const audioUrls=params.audio||[];

    const audioUrls1=params.audio_1||[];

    const maxLength=Math.max(audioUrls.length,audioUrls1.length);

    console.log("audioUrls length:",audioUrls.length);

    console.log("audioUrls1 length:",audioUrls1.length);

    for (let i=0;i<maxLength;i++){

        if(i<audioUrls.length&&audioUrls[i]&&audioUrls[i].url){

            mergedUrls.push(audioUrls[i].url);

        }

        if(i<audioUrls1.length&&audioUrls1[i]&&audioUrls1[i].url){

            mergedUrls.push(audioUrls1[i].url);

        }

    }

    console.log("Merged URLs:",mergedUrls);

    const ret={

        "key0":mergedUrls,

    };

    return ret;

}

[PRE2]
你作为资深广播栏目策划，请以下方提供的文章原文为依据，撰写一篇不超过300字的精炼栏目简介，力求简洁凝练，同时提供一段可用于AI绘图的封面图生成描述，要求画面能直观呈现节目主题并具备艺术表现力。

== 文章内容 ==

{{input}}

===end===

请务必严格按照下列结构输出，勿输出任何额外内容：

{
"content":"（这里是节目简介）"
"cover_prompt":"（这里是封面图片提示词）"
}

[PRE3]

{{prompt}}