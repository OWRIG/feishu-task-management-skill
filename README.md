# Feishu Task Management Skill

一个面向类 OpenClaw agent 的飞书任务管理 skill，内置可执行 toolkit，用来完成：

- 飞书任务创建、查询、更新、完成、恢复、删除
- 任务成员添加与移除
- 基于本地成员表的成员解析
- 应用身份成员同步
- 用户身份优先的任务操作

这个仓库的目标不是“只给一份提示词”，而是把 skill、文档、脚本和本地数据结构一起打包成一个可分发、可安装、可验证的完整能力单元。

## Why

直接让 AI 临时拼飞书 API 请求有几个典型问题：

- 鉴权和权限边界容易出错
- 成员解析容易歧义
- 写操作安全规则难以稳定复用
- skill 单独分发时，脚本和配置经常丢失

这个项目把这些问题收敛成两层：

- `feishu-task-management/`
  skill 本体，负责工作流、规则和上下文组织
- `feishu-task-management/toolkit/`
  本地 Python toolkit，负责任务执行、配置引导、成员同步和数据落盘

## Features

- 自包含 skill bundle：skill 和 toolkit 在同一个目录层级下分发
- 交互式 `guide`：一步完成基础配置、可选 OAuth 配置、可选成员同步
- 混合认证模型：成员同步走应用身份，任务接口优先走用户身份
- 本地成员映射：支持成员表和人工别名表
- 更安全的任务写操作：优先使用固定 CLI，而不是自由拼 HTTP 请求

## Project Layout

```text
feishu-task-management/
├── SKILL.md
├── references/
└── toolkit/
    ├── config/
    ├── data/
    ├── scripts/
    ├── src/
    └── tests/
```

核心入口：

- skill: `feishu-task-management/SKILL.md`
- config guide: `feishu-task-management/toolkit/scripts/feishu_config.py`
- members CLI: `feishu-task-management/toolkit/scripts/feishu_members.py`
- task CLI: `feishu-task-management/toolkit/scripts/feishu_task.py`

## Quick Start

从仓库根目录执行：

```bash
python3 feishu-task-management/toolkit/scripts/feishu_config.py guide
python3 feishu-task-management/toolkit/scripts/feishu_config.py validate
python3 feishu-task-management/toolkit/scripts/feishu_members.py sync
```

然后你就可以开始操作任务，例如：

```bash
python3 feishu-task-management/toolkit/scripts/feishu_task.py list
python3 feishu-task-management/toolkit/scripts/feishu_task.py create --summary "测试任务"
```

## How To Use The Toolkit

如果你是第一次使用，先看这份文档：

- [Toolkit Usage Guide](/Users/haojun/ai-team-base/feishu-task/docs/toolkit-usage.md)

这份文档会讲清楚：

- `guide` 每一步做什么
- `App ID` 和 `App Secret` 去哪里获取
- OAuth 回调 URL 为什么要粘贴完整地址
- 成员同步和成员别名怎么使用
- 常用 toolkit 命令怎么跑

## Auth Model

当前默认是混合模式：

- 通讯录和成员同步使用 `tenant_access_token`
- 任务接口优先使用 `user_access_token`
- 如果没有用户 token，任务接口回退到应用身份

这样做是因为飞书通讯录接口和任务接口对 token 类型的支持不完全一致。这个拆分能同时保证：

- 成员同步稳定
- 任务创建和更新更接近“用户本人在操作”

## Who This Is For

这个项目更适合下面这类场景：

- 想给 Agent 增加飞书任务管理能力
- 希望把飞书任务操作约束成固定 CLI，而不是临时拼 OpenAPI
- 需要在 skill 分发时把脚本和本地数据结构一起带走
- 主要面向中文团队和中国区飞书环境

## Current Scope

当前 v1 已覆盖：

- create
- get
- list
- update
- delete
- complete
- reopen
- add-members
- remove-members

当前暂不覆盖：

- reminders
- tasklists
- dependencies
- repeat rules
- custom complete
- attachments

## Documentation

- [Toolkit Usage Guide](/Users/haojun/ai-team-base/feishu-task/docs/toolkit-usage.md)
- [English README](/Users/haojun/ai-team-base/feishu-task/README.en.md)
- [Skill](/Users/haojun/ai-team-base/feishu-task/feishu-task-management/SKILL.md)

## Status

这个项目当前已经具备可运行的本地 toolkit、测试和 skill 校验链路，适合继续在真实飞书应用下迭代。
