response = requests.post(url, headers=self.headers, json=payload)

        data = self._handle_response(response)

        return data["context_id"]

    def get_context(self, context_id: str) -> MCPContext:

        """获取指定上下文"""

        url = f"{self.server_url}/mcp/v1/context/{context_id}"

        response = requests.get(url, headers=self.headers)

        data = self._handle_response(response)

        return MCPContext(

            context_id=context_id,

            model_id=data["model_id"],

            data=data["data"],

            created_at=data["created_at"]

        )

    def update_context(self, context_id: str, context_data: Dict[str, Any], append: bool = True) -> bool:

        """更新上下文数据"""

        url = f"{self.server_url}/mcp/v1/context"

        payload = {

            "context_id": context_id,

            "context_data": context_data,

            "append": append

        }

        response = requests.put(url, headers=self.headers, json=payload)

        self._handle_response(response)

        return True

    def delete_context(self, context_id: str) -> bool:

        """删除上下文"""

        url = f"{self.server_url}/mcp/v1/context/{context_id}"

        response = requests.delete(url, headers=self.headers)

        self._handle_response(response)

        return True

    def get_model_contexts(self, model_id: str) -> Dict[str, MCPContext]:

        """获取指定模型的所有上下文"""

        url = f"{self.server_url}/mcp/v1/model/{model_id}/contexts"

        response = requests.get(url, headers=self.headers)

        data = self._handle_response(response)

        contexts = {}

        for ctx_id, ctx_data in data["contexts"].items():

            contexts[ctx_id] = MCPContext(

                context_id=ctx_id,

                model_id=ctx_data["model_id"],

                data=ctx_data["data"],

                created_at=ctx_data["created_at"]

            )

        return contexts

    def get_stats(self) -> MCPStats:

        """获取服务统计信息"""

        url = f"{self.server_url}/mcp/v1/stats"

        response = requests.get(url, headers=self.headers)

        data = self._handle_response(response)

        return MCPStats(data)

    def health_check(self) -> Dict[str, Any]:

        """健康检查"""

        url = f"{self.server_url}/health"

        response = requests.get(url)

        return self._handle_response(response)

以上实现演示了一个对接MCP服务的Python客户端类。它把增删查改等完整操作都封装好了，主要用来在MCP服务端里管理上下文。

### 4. 部署与运行

整体上线可分为三个步骤：

(1) 启动Redis服务：

[PRE0]

(2) 启动MCP服务：

[PRE1]

(3) 使用客户端SDK。

### 【示例9.4】 mcp_client_SDK

[PRE2]

## 9.4 单机MCP客户端搭建

MCP协议的核心是模型上下文的交互，配合Python SDK就能快速完成单机客户端的搭建。下面会按顺序讲清具体的建设流程。

### 1. 环境准备

此阶段需先准备好运行环境，Python建议3.8及以上，并安装MCP的Python SDK：

[PRE3]

### 2. 基本客户端结构设计

一个基础可用的MCP客户端通常包含以下要点：

- 协议连接管理。
- 上下文数据处理。
- 消息序列化/反序列化。
- 错误处理机制。

### 3. 客户端实现代码

【示例9.5】mcp_client_achieve.py