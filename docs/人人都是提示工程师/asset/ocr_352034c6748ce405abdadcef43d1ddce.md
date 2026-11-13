```
func main() {
    // 连接 MySQL 数据库
    db, err := sql.Open("mysql", "root:123456@tcp(localhost:3306)/
    mydatabase")
    if err != nil {
        panic(err.Error())
    }
    defer db.Close()

    // 查询公司名字信息
    rows, err := db.Query("SELECT name FROM company")
    if err != nil {
        panic(err.Error())
    }
    defer rows.Close()

    // 将结果转换为列表
    var stock_list []string
    for rows.Next() {
        var name string
        err = rows.Scan(&name)
        if err != nil {
            panic(err.Error())
        }
        stock_list = append(stock_list, name)
    }

    // 输出结果
    fmt.Println(stock_list)
}
```

注意：安装 Go 语言之前，需要先安装 Go 语言的 MySQL 驱动程序 “go-sql-driver”。