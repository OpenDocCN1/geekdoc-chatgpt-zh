```
radius: 3,
lineWidth: 1
}
}
},
line: {
lineWidth: 1,
states: {
hover: {
lineWidth: 1
}
},
marker: {
enabled: false,
states: {
hover: {
enabled: true,
symbol: 'circle',
radius: 3,
lineWidth: 1
}
}
}
},
series: [{
marker: {
symbol: 'square'
},
data: data,
step: step,
}],
navigation: {
menuItemStyle: {
fontSize: '10px'
}
}
});
}
```

(2) 通过Chrome浏览器打开历史数据示例程序HistoryDemo- Web\\index.html, 查看光照度实时数据信息及历史数据曲线信息, 通