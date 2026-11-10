# 企业级 QwQ-32B RAG 交付全记录

这套 rag-qwq32b-enterprise 就是把第 11 章的大规模检索增强生成系统做成可交付的企业方案。整体思路很清晰：先把应用骨架定下来，用自动化测试锁住接口行为，再给出标准目录和一键部署脚本，让客户配置即用。

先看项目初始化的命名空间。

[PRE0]python

# rag-qwq32b-enterprise/app/__init__.py

"""

app 包初始化文件

"""

接着是接口层面的质量门禁。测试文件用 FastAPI 的 TestClient 直接打两条核心路由，一条检索、一条生成，用参数化把 finance、legal contract、hr policy 三类典型场景一起跑通。

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

跑完的效果很干净。

[PRE2]

============================= test session starts =============================

collected 2 items

test_app_routes.py::test_search_docs[finance] PASSED

test_app_routes.py::test_search_docs[legal contract] PASSED

test_app_routes.py::test_search_docs[hr policy] PASSED

test_app_routes.py::test_generate_rag_response PASSED

============================== 4 passed in 0.41s ===============================

目录结构也按企业交付的习惯做了分离，数据、模型、服务、应用、部署、测试、镜像全都有。

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

部署这块是本次的重点，脚本的定位是把研发好的系统做成客户可配置的交付物。

[PRE4]

"""

部署脚本：将第 11 章的 QwQ-32B 大规模 RAG 检索生成系统进行企业级定制化交付。

演示如何加载用户配置、启动服务并进行最小化测试验证。

"""

实现上先收拢常用依赖，再把配置读出来写入环境变量，让主程序可以直接消费。

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

整体下来就是：初始化清晰、接口可测、目录规范、部署可配置。把 enterprise_config.yaml 换掉就能适配不同客户的环境、端口和模型密钥，服务启动后用最小化自测确认检索和生成都正常，交付闭环就完成了。