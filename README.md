# jingli-home-ai-design-skill-package

给通用 Agent 安装使用的 Skill 包（元数据仓库）。

## 文件
- `skill.json`
- `SKILL.md`

## 目标
- 不存放服务端密钥和业务执行代码
- 仅告诉 Agent：工具叫什么、如何调用、调用顺序和规则
- 指向已部署的远程 MCP 服务

## 当前远程服务
`skill.json` 默认 `mcp_server.default_base_url`：
`https://misc.wjkj.com/mcp`

## 安装示例
对通用 Agent 说：

> 帮我安装 skill：晶鲤焕新家AI设计，仓库地址：<这个skill仓库地址>
