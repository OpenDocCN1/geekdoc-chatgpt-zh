# 深度解析 Requests 库：响应处理与进阶请求技巧

在上一节我们初步接触了 Requests 发送基础请求的方法，今天我们将重点攻克**响应对象的深度解析**与**进阶 HTTP 请求的构建**。掌握这些内容，能让你从“会用”进阶到“精用”，轻松应对编码乱码、状态码判断、Header 伪装及 API 对接等实战场景。

---

## 4.3 响应对象的深度解析

拿到服务器返回的 `Response` 对象后，如何精准提取所需数据？我们分三个维度拆解：内容获取、状态码校验、Headers 读取。

### 4.3.1 三种获取响应内容的姿势

Requests 提供了三种不同层级的内容访问方式，适用场景截然不同：

1.  **`response.text`（字符串模式）**：库自动根据 HTTP 头部推断编码（或 fallback 到 ISO-8859-1）解码后的 Unicode 字符串。**最常用**，适合直接解析 HTML、XML 等文本。
2.  **`response.content`（字节流模式）**：返回原始的二进制字节序列，**不做任何解码**。处理图片、视频、文件下载，或遇到编码检测失败需手动 `decode('gbk')` 时必选。
3.  **`response.raw`（原始 Socket 流）**：开启 `stream=True` 后可用，允许分块读取原始 TCP 流（如 `raw.read(15)`）。适合大文件流式处理、断点续传，避免一次性加载撑爆内存。

> **实战对比**：以访问 `w3school.com.cn` 为例：
> *   `text` 直接输出可读的 HTML 标签；
> *   `content` 显示原始字节，肉眼可见 `\r\n` 等转义符；
> *   `raw.read(15)` 仅吐出前 15 字节的原始报文头片段。

**JSON 数据的特殊处理**：
别用 `json.loads(response.text)` 了，直接调 **`response.json()`** 一步到位。它会自动完成“字节流 -> 字符串 -> 字典/列表”的反序列化，返回类型为 `dict`，且内部已做异常保护。实测 `httpbin.org/user-agent` 接口，`text` 返回 `str`，`json()` 直接给出 `{'user-agent': 'python-requests/...'}` 字典。

---

### 4.3.2 优雅地判断请求成败：别再写 `if code == 200` 了

手动对比状态码不仅繁琐，还易遗漏 201、204 等成功码。Requests 内置了两个语义化常量：

*   `requests.codes.ok` (值为 200)
*   `requests.codes.all_good` (别名，功能完全一致)

**推荐写法**：
```python
if response.status_code == requests.codes.ok:
    print("请求成功")
# 或更简洁地利用布尔值特性
if response.status_code == requests.codes.all_good:
    ...
```

**实战验证**：
请求正常页面返回 `200` + `True`；请求 404 页面返回 `404` + `False`。

#### 进阶：让程序主动抛出异常——`raise_for_status()`

当状态码落入 **4xx/5xx** 区间时，调用 `response.raise_for_status()` 会直接抛出 `requests.exceptions.HTTPError`，并附带详细错误上下文（如 `404 Client Error: Not Found for url: ...`）。这在爬虫框架中极其有用，配合 `try...except` 即可实现统一的错误重试/记录逻辑，省去大量 `if/else` 判断。

---

### 4.3.3 读取响应头：字典与 `.get()` 双模式

`response.headers` 本质是一个**不区分大小写的特殊字典**（`CaseInsensitiveDict`）。

*   **字典取值**：`headers['Content-Type']`、`headers['Server']`。键名大小写随意，但建议规范写法。
*   **安全取值**：`headers.get('Content-Length')`。键不存在时返回 `None` 而不报 `KeyError`，更健壮。

**常见字段速查表 (MIME Type)**：
| Content-Type 值 | 含义 |
| :--- | :--- |
| `text/html` | HTML 文档 |
| `application/json` | JSON 数据 |
| `image/jpeg/png/gif` | 图片资源 |
| `text/plain` | 纯文本 |

实测 W3School 返回：`Content-Type: text/html`，`Content-Length: 4043`，`Server` 字段暴露服务器型号。

---

## 4.4 进阶 HTTP 请求构建：模拟真实用户行为

基础 `get(url)` 无法满足登录态维持、反爬绕过、API 对接等需求，需注入 `cookies`、`headers`、参数等元数据。

### 4.4.1 Cookie 交互：维持会话状态

**获取**：`response.cookies` 返回 `RequestsCookieJar`（类字典对象），可通过 `cookies['session_id']` 取值。
**发送**：在 `requests.get(url, cookies=my_cookie_dict)` 中传入字典。

**实战演练**：向 `httpbin.org/cookies` 发送 `{'flavor': 'chocolate'}`，响应体会原样返回该 Cookie，证明服务端已成功识别客户端身份。*这是实现“免登录爬取”的核心基础。*

### 4.4.2 自定义 Headers：伪装浏览器身份

默认 `User-Agent` 为 `python-requests/x.x.x`，极易被反爬系统拦截。**伪装成主流浏览器是爬虫生存第一课**。

```python
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36'
}
response = requests.get(url, headers=headers)
```

**实测对比**：
*   无伪装：`httpbin.org/user-agent` 返回 `python-requests/2.31.0`。
*   伪装后：返回完整的 Chrome 120 UA 字符串，服务端误判为真人访问。

> **Pro Tip**：除 UA 外，`Referer`、`Accept-Language`、`Cookie` 等头字段常需组合伪造，构建完整的“浏览器指纹”。

### 4.4.3 对接 RESTful API：参数化请求与 JSON 响应

Requests 原生支持 REST 风格调用，核心在于 **`params` 参数自动拼接 Query String**。

**案例：腾讯天气 API 查询成都 24h 预报**
接口地址：`https://wis.qq.com/weather/common`
关键参数：
*   `weather_type`: `forecast_24h`
*   `province`: `四川`
*   `city`: `成都`

**代码范式**：
```python
params = {
    'weather_type': 'forecast_24h',
    'province': '四川',
    'city': '成都'
}
resp = requests.get('https://wis.qq.com/weather/common', params=params)
data = resp.json()  # 直接拿结构化字典
```
`params` 会自动处理 URL 编码（中文转 `%Ex`），生成标准 URL：`.../common?weather_type=forecast_24h&province=%E5%9B%9B%E5%B7%9D&city=%E6%88%90%E9%83%BD`，极大简化了手动拼接的麻烦与报错率。

---

## 核心要点速记卡

| 场景 | 关键属性/方法 | 避坑指南 |
| :--- | :--- | :--- |
| **文本解析** | `r.text` | 编码不对用 `r.encoding = 'gbk'` 强制指定后再取 text |
| **二进制/下载** | `r.content` | 大文件必开 `stream=True` 配合 `r.iter_content()` 分块写入 |
| **JSON 接口** | `r.json()` | 非 JSON 响应会抛 `JSONDecodeError`，需捕获 |
| **成功判定** | `r.status_code == requests.codes.ok` | 别硬编码 200；`raise_for_status()` 适合全局异常拦截 |
| **读 Header** | `r.headers.get('Key')` | 键名不区分大小写；获取 Cookie 用 `r.cookies` |
| **发 Cookie** | `requests.get(..., cookies=dict)` | 复杂场景建议用 `Session` 对象自动托管 |
| **伪装 UA** | `requests.get(..., headers={'User-Agent': '...'})` | 维护一个 UA 池轮换，降低封禁风险 |
| **API 参数** | `requests.get(..., params=dict)` | 自动 URL Encode，防手动拼接出错 |

---

**下一站预告**：掌握了 GET 请求的进阶用法，**POST 请求的表单提交、文件上传、JSON 体发送**，以及核心神器 **`Session` 会话保持**（自动处理 Cookie、连接复用、默认 Header）将登场。那是编写工业级爬虫、自动化测试脚本的分水岭，敬请期待！

[PRE0]
[PRE1]
[PRE2]
[PRE3]
[PRE4]
[PRE5]
[PRE6]
[PRE7]
[PRE8]
[PRE9]
[PRE10]
[PRE11]
[PRE12]
[PRE13]
[PRE14]
[PRE15]
[PRE16]
[PRE17]
[PRE18]