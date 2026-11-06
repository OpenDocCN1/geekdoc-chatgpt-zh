| 方法名 | 意义 | 参数 |
|---|---|---|
| unlink | 删除文件 | { filePath:要删除的文件路径 success:查询成功的回调，如果文件或目录存在，则会查询成功 fail:查询失败，文件不存在会回调 complete:查询完成的回调 } |
| unzip | 将压缩文件进行解压缩 | { zipFilePath:压缩文件路径 targetPath:解压后的文件路径 success:查询成功的回调，如果文件或目录存在，则会查询成功 fail:查询失败，文件不存在会回调 complete:查询完成的回调 } |
| writeFile | 进行写文件 | { filePath:文件路径 data:写入的数据 encoding:编码方式 success:查询成功的回调，如果文件或目录存在，则会查询成功 fail:查询失败，文件不存在会回调 complete:查询完成的回调 } |

设置文件编码的 encoding 属性可选值，如表 7-21 所示。

# 表 7-21 编码方式

| encoding 属性可选值 |
|---|
| ascii |
| base64 |
| binary |
| hex |
| utf16le |
| utf8 |
| latin1 |

需要注意的是，尽管小程序开发框架提供了丰富的文件操作接口，但是不能滥用存储，小程序最重要的特点就在于“小巧”，在设计功能时，要时刻遵循小巧、快捷的原则。