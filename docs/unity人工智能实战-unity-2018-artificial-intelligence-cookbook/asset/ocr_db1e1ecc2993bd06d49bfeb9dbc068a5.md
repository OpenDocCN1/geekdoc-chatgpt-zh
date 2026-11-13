```
RacingRival[] rivals;
}
```

# 10. 在橡皮筋系统中为每个竞速者指定随机位置。我们在本例中使用的是一个环形的橡皮筋：

```
void Start()
{
    ghost = FindObjectOfType<RacingCenter>();
    rivals = FindObjectsOfType<RacingRival>();
    foreach (RacingRival r in rivals)
    {
        if (ReferenceEquals(r, ghost))
        continue;
        r.randomPos = Random.insideUnitSphere;
        r.randomPos.y = ghost.transform.position.y;
    }
}
```

# 运行原理

上层的 AI 橡皮筋系统指定竞速者要占用的位置，每个竞速者有其自己的行为来调整速度，尤其是无敌竞速者。而 agent 作为众多橡皮筋的中心，如果玩家的速度超出其阈值，agent 就去适应玩家，否则 agent 就保持原样，来回变道。