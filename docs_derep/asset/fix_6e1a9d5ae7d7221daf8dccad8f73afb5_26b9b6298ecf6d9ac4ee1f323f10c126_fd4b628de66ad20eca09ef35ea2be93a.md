# QwQ-32B 企业 RAG 交付实操拆解

从章节原型到可交付的商业版本，重点在于把骨架固定、验证闭环、目录规范、启动可配置。下面的材料按照这一顺序呈现。

最基础的占位是应用包的初始化。
[PRE0]python

# rag-qwq32b-enterprise/app/__init__.py

"""

app 包初始化文件

"""

接口可用性主要靠路由层冒烟测试覆盖。测试脚本借助 FastAPI 内置的客户端，分别对检索端点和生成端点做检查。检索测试采用 pytest 参数化，选取财务、合同法律、HR 制度三类典型查询；生成测试则用一条复合财务问题验证整个链路是否返回完整字段。
[PRE1]python

# rag-qwq32b-enterprise/tests/test_app_routes.py

import pytest

from fastapi.testclient import TestClient

from app.main import app

client=TestClient(app)

@pytest.mark.parametrize("query", ["finance", "legal contract", "hr policy"])

def test_search_docs(query):

    resp=client.get(f"/api/search?query={query}")

    assert resp.status_code == 200

    data=resp.json()

    assert data["query"] == query

    assert "results" in data

def test_generate_rag_response():

    resp=client.get("/api/generate?query=Some complex finance query")

    assert resp.status_code == 200

    data=resp.json()

    assert "query" in data

    assert "response" in data

运行结果显示四项用例全部通过，耗时约 0.4 秒。
[PRE2]

============================= test session starts =============================

collected 2 items

test_app_routes.py::test_search_docs[finance] PASSED

test_app_routes.py::test_search_docs[legal contract] PASSED

test_app_routes.py::test_search_docs[hr policy] PASSED

test_app_routes.py::test_generate_rag_response PASSED

============================== 4 passed in 0.41s ===============================

项目目录沿用常规交付划分，数据、模型、服务、应用、部署、测试、容器与说明文档各自分区，便于后续替换。
[PRE3]

rag-qwq32b-enterprise/

├── data/

├── model/

├── services/

├── app/

├── deployment/

│   ├── deploy_enterprise.py    <-- 本次示例重点

│   └── enterprise_config.yaml  <-- 客户定制参数

├── tests/

├── docker/

└── README.md

交付落地的入口在 deployment 目录。脚本说明了其定位：把第 11 章的 QwQ-32B 大规模检索生成系统做成可定制的企业版本，并演示配置读取、服务启动与最小化自检。
[PRE4]

"""

部署脚本：将第 11 章的 QwQ-32B 大规模 RAG 检索生成系统进行企业级定制化交付。

演示如何加载用户配置、启动服务并进行最小化测试验证。

"""

实现思路是先收敛依赖，再通过 YAML 读取关键参数并映射到进程环境，主程序即可直接消费，随后按配置起服务并执行简易自检。
[PRE5]python

import os

import yaml

import subprocess

import requests

import time

import random

import string

# 导入先前开发的模块

from app.main import run as run_app

# 也可import retrieval_service, generation_service等

def load_enterprise_config(config_file="./enterprise_config.yaml"):

    """

    加载企业定制化配置，并写入环境变量或文件

    """

    with open(config_file, "r", encoding="utf-8") as f:

        data=yaml.safe_load(f)

    return data

def set_environment_vars(enterprise_cfg: dict):

    """

    将配置写入环境变量，以便app.main读取

    """

    env=enterprise_cfg.get("environment", "production")

    host=enterprise_cfg.get("host", "0.0.0.0")

    port=enterprise_cfg.get("port", 9000)

    qwq_api_key=enterprise_cfg.get("qwq32b_api_key", "sk-xxx")

    os.environ["APP_ENV"]=env

整体来看，目录轻量化、验证完备、启动依赖外部配置。将 enterprise_config.yaml 中的运行环境、监听地址和模型密钥替换为客户自己的取值，即可验证检索与生成的完整链路，形成闭环交付。