# 孕期陪护机器人 "孕小宝" 💖

一个为爱而生的、基于 n8n 和 AI 大语言模型的飞书孕期陪护机器人。它不仅仅是一个程序，更是家庭在迎接新生命过程中的一位智能、温暖且可靠的伙伴。

[![Status](https://img.shields.io/badge/status-V1.0_上线-brightgreen.svg)](https://shields.io/)
[![Tech Stack](https://img.shields.io/badge/Tech-n8n_|_MySQL_|_AI-blue.svg)](https://shields.io/)
[![Platform](https://img.shields.io/badge/Platform-飞书_(Feishu)-informational.svg)](https://shields.io/)

---

## ✨ 项目简介

“孕小宝”旨在解决准妈妈在孕期中常见的信息焦虑、记录繁琐、以及家人关心但“使不上劲”的痛点。通过自然语言交互，它将专业的孕期知识、个性化的数据追踪和家庭成员间的温情互动无缝地融合在飞书群聊中。

**核心设计理念：**
*   **以孕妇为中心**: 所有功能围绕准妈妈的健康、舒适和情感需求。
*   **自然交互**: 彻底抛弃死板的指令，通过对话完成所有任务。
*   **权威且温暖**: 结合权威医学知识与个性化档案，提供专业且充满关怀的建议。
*   **家庭协同**: 让所有家人都能轻松参与，科学陪护。

## 🚀 V1.0 核心功能

*   **自然语言理解 (NLU)**: 用户无需学习任何指令，通过日常对话即可完成问答、记录、查询等所有操作。
*   **深度个性化问答**: 结合数据库中存储的**核心档案**（过敏史、病史）、**动态记录**（体重、症状）和**产检报告**，提供真正“千人千面”的智能解答。
*   **自动化信息管理**: 能够从对话中智能提取关键信息（如体重数值、不适症状、医嘱、日程安排）并自动存入数据库相应的数据表。
*   **多模态分析 (OCR)**: 支持用户上传化验单、报告单等图片，能自动调用 OCR 工具识别内容、提取关键指标并进行归档。
*   **紧急情况预警**: 内置高危关键词识别机制，一旦触发（如“破水”、“出血”），能立即在群内发送加急告警并@紧急联系人。
*   **多角色语气自适应**: 能识别提问者的身份（孕妇本人、丈夫、长辈），并自动切换到最合适的沟通语气（温暖共情、专业指导、通俗关怀）。

## 🔧 技术架构

本项目采用先进的**多层工作流解耦架构**，确保了系统的高可用性、可维护性和逻辑清晰性。

```mermaid
graph TD
    subgraph 用户端
        A[家庭成员在飞书群聊中发言]
    end

    subgraph 网络与调度层
        B(ngrok 公网地址) --> C{Webhook<br>机器人消息分发.json}
    end
    
    subgraph 核心逻辑层 (n8n Workflows)
        subgraph 思考模块 (The Brain)
            F[cj-消息回复.json] -- 调用工具 --> G[飞书-图片 OCR.json]
            F -- 调用工具 --> H[MySQL 数据库]
        end

        subgraph 表达模块 (The Mouth)
            E[群聊-cj.json]
        end
    end

    subgraph 外部服务
        I[大语言模型 API<br>(Google Gemini, DeepSeek)]
        H
    end

    A -- Webhook --> B
    C -- 根据 Chat ID 分发 --> E
    E -- 触发执行 --> F
    F -- 思考结果 --> E
    E -- 格式化并调用 --> I
    E -- 最终回复 --> A
```

**技术栈详情:**
*   **核心编排引擎**: [**n8n**](https://n8n.io/) - 使用其强大的工作流和 Agent 节点构建所有核心逻辑。
*   **数据库**: [**MySQL**](https://www.mysql.com/) - 用于持久化存储所有结构化数据，包括用户档案、各类记录和报告。
*   **AI 大语言模型**:
    *   [**Google Gemini**](https://ai.google/gemini/) - 作为“核心大脑”，负责主要的推理、意图理解和工具调用。
    *   [**DeepSeek**](https://www.deepseek.com/) - 作为“消息格式化”模型，负责将大脑的思考结果转换成严格的飞书消息JSON。
*   **协作平台**: [**飞书 (Feishu)**](https://www.feishu.cn/) - 作为机器人最终的用户交互界面。
*   **图像识别**: [**Tesseract.js**](https://tesseract.projectnaptha.com/) - 通过 n8n 节点在本地完成对化验单等图片的 OCR 识别。
*   **本地开发网络**: [**ngrok**](https://ngrok.com/) - 用于将本地运行的 n8n 服务暴露到公网，以便接收飞书的 Webhook 回调。

## 📚 项目文件结构

```
YunbaoProject/
├── n8n_workflows/
│   ├── 机器人消息分发.json
│   ├── 群聊-cj.json
│   ├── cj-消息回复.json
│   └── 飞书-图片 OCR.json
├── docs/
│   ├── PRD_V1.0.md
│   ├── 数据库使用规范文档.md
│   ├── 技术架构设计文档.md
│   ├── 关键提示词库.md
│   └── 用户手册.md
└── README.md
```

## ⚙️ 安装与部署指南

1.  **环境准备**:
    *   确保您的本地计算机已安装 [Docker](https://www.docker.com/) 和 [Docker Compose](https://docs.docker.com/compose/)。
    *   安装一个 MySQL 客户端（如 DBeaver, Navicat）。
    *   注册并安装 [ngrok](https://ngrok.com/download)。

2.  **数据库初始化**:
    *   启动本地 MySQL 服务。
    *   创建一个新的数据库，命名为 `data`。
    *   执行 `docs/数据库使用规范文档.md` 中定义的所有 `CREATE TABLE` 语句，完成数据表创建。
    *   **关键步骤**: 手动向 `Base` 表和 `FamilyMembers` 表中插入您和妻子的初始档案信息，确保 `feishu_id` 和 `is_emergency_contact` 等字段正确无误。

3.  **n8n 环境配置**:
    *   使用 Docker 运行 n8n，并挂载本地目录以持久化数据：
        ```bash
        docker run -it --rm --name n8n -p 5678:5678 -v ~/.n8n:/home/node/.n8n n8nio/n8n
        ```
    *   访问 `http://localhost:5678`，进入 n8n 工作台。
    *   在 n8n 中手动导入 `n8n_workflows/` 目录下的所有 `.json` 工作流文件。
    *   **关键步骤**: 进入 **Credentials** 菜单，配置以下所有服务的访问凭据：
        *   Feishu (App ID & App Secret)
        *   MySQL (Host, User, Password, Database)
        *   Google Gemini API Key
        *   DeepSeek API Key

4.  **网络与激活**:
    *   在终端启动 ngrok，将 n8n 的端口暴露到公网：
        ```bash
        ngrok http 5678
        ```
    *   复制 ngrok 生成的 `https://` 开头的公网地址。
    *   进入飞书开发者后台，找到您的机器人应用，在“事件订阅”中，将“请求地址 URL”更新为 ngrok 的地址 + `机器人消息分发.json` Webhook节点的路径。
    *   回到 n8n 工作台，将 `机器人消息分发` 这个工作流的状态切换为 **Active**。

## 📖 使用方法

请参考 `docs/用户手册.md`。核心交互方式为**在飞书群中直接@孕小宝并用自然语言对话**。

## 🌱 未来路线图 (V2.0+)

*   [ ] **主动服务增强**: 实现定时的产检/用药提醒、每周的“宝宝成长播报”和营养建议推送。
*   [ ] **数据可视化**: 定期生成体重、症状频率等图表，并通过私聊发送。
*   [ ] **情感互动升级**: 增加更多趣味互动，如节日祝福、胎教音乐推荐等。
*   [ ] **知识库扩展**: 接入并整合更多外部权威医疗知识源，打造更专业的知识库。

---

**谨以此项目，献给我挚爱的妻子，愿你的孕期充满阳光与喜悦。**