---
name: jingli-home-ai-design-skill
displayName: 晶鲤家装设计AI
description: 通用Agent可安装的家装设计Skill。Agent先识别用户意图并提取参数，再调用远程MCP服务完成真实户型解析、设计方案、效果图生成与TOS上传。
version: 0.3.0
alwaysApply: false
keywords:
  - 晶鲤家装设计AI
  - 家装
  - 装修
  - 户型解析
  - 效果图
  - 报价
  - MCP
  - Gemini
  - TOS
---

## 目标
让通用 Agent 在用户沟通中自动完成：
1. 识别用户家装意图
2. 提取并补齐参数
3. 按规则调用远程 MCP 工具
4. 返回结构化设计结果与可访问资产链接

远程 MCP 地址：`https://misc.wjkj.com/mcp/`

## Agent 工作流（必须遵循）
1. 先做意图识别：判断用户处于“需求梳理 / 户型解析 / 方案设计 / 效果图 / 报价 / 全流程”。
2. 做参数收集：缺参数先追问，不可臆造。
3. 工具编排调用：严格按下方映射顺序执行。
4. 结果回传：返回结构化结论 + 风险说明 + 资产URL。

## 意图到工具映射
1. 需求梳理
- 工具：`build_design_brief`
- 必要参数：`user_requirements`
- 推荐补齐：`budget_range`, `family_profile`, `preferred_style`, `forbidden_items`

2. 户型解析
- 工具顺序：`upload_floorplan_to_tos` -> `parse_floorplan`
- 输入支持：`image_url` 或 `image_base64`
- 规则：解析必须真实调用，不得伪造。

3. 方案草案
- 工具顺序：`match_products` -> `compose_design_scheme`
- 规则：商品必须来自工具返回，不可杜撰 SKU/价格。

4. 效果图
- 工具顺序：`generate_render_prompt` -> `generate_design_render` -> `upload_render_to_tos`
- 规则：效果图必须真实生成并上传TOS，返回 `object_key` 和访问URL。

5. 报价
- 工具：`generate_quote`
- 规则：必须标记“草案/预估/待复核”。

6. 全流程
- 工具：`generate_full_design_package`
- 场景：用户希望快速得到完整结果包。

## 参数提取规则
Agent需优先从对话抽取这些字段：
- `user_requirements`: 用户原始目标与偏好
- `budget_range`: 预算区间
- `family_profile`: 家庭结构
- `preferred_style`: 风格偏好
- `forbidden_items`: 禁忌项
- `room_name`: 重点空间（默认客厅）
- `camera_view`: 镜头视角（可选）
- `project_id`: 项目标识（若无可由Agent生成）

若缺关键参数：
- 户型图缺失：提示用户上传图片
- 预算缺失：提示给区间
- 风格缺失：提供选项（现代/奶油/原木/轻奢/新中式）

## 绝对约束
1. 不得臆造面积、空间结构、商品价格、施工量。
2. `parse_floorplan` / `generate_design_render` / `upload_render_to_tos` / `upload_floorplan_to_tos` 禁止 mock。
3. 用户未确认前，不调用 `save_scheme_to_project`。

## 输出规范
每次结果应包含：
1. 已完成步骤
2. 关键结构化结果摘要
3. 风险/待复核项
4. 资产链接（户型图/效果图上传链接）
5. 下一步建议

## 降级策略
1. MCP不可达：明确提示服务不可用并建议稍后重试。
2. Gemini调用失败：返回失败原因并建议重试或降级到任务书/方案层。
3. TOS上传失败：保留本地生成结果摘要并提示上传重试。

## 推荐首轮对话模板
当用户首次使用时，Agent可说：
“我可以帮你做晶鲤家装设计AI全流程。请告诉我：户型图（上传）、预算范围、家庭结构、偏好风格、禁忌项。若你愿意，我也可以先从需求梳理开始。”
