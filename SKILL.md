# 晶鲤焕新家AI设计

## 1. Skill 简介
本 Skill 面向家装设计与报价场景，提供可被通用 Agent 与专用 Agent 调用的原子工具与全链路工具。核心能力覆盖：真实户型图解析、设计任务书、风格方案、商品匹配、真实效果图生成、真实TOS上传、草案报价、项目回写预留。

## 2. 适用场景
- 用户上传户型图，希望拿到可讨论的空间结构与设计方向。
- 用户仅提供文字需求，希望先形成结构化设计任务书。
- 需要先快速出概念效果图，再沉淀报价草案。
- 企业内部希望把能力编排进 CRM/报价单/项目系统。

## 3. 触发词与触发条件
- 触发词：户型解析、设计任务书、风格方案、效果图、报价、回写项目。
- 触发条件：
  - 出现户型图输入（url/base64）时，优先触发 `parse_floorplan`。
  - 仅有文字需求时，优先触发 `build_design_brief`。
  - 需求不完整时，Agent必须先与用户澄清家庭结构、预算、风格偏好、禁忌项，再调用工具。

## 4. 工具调用映射
- 户型图解析: `parse_floorplan`
- 任务书生成: `build_design_brief`
- 商品匹配: `match_products`
- 方案整合: `compose_design_scheme`
- 渲染提示词: `generate_render_prompt`
- 效果图生成: `generate_design_render`
- 户型图上传TOS: `upload_floorplan_to_tos`
- 上传TOS: `upload_render_to_tos`
- 草案报价: `generate_quote`
- 回写项目: `save_scheme_to_project`
- 快速全链路: `generate_full_design_package`

## 5. 必须遵循的调用顺序
1. Agent先通过对话收集并确认需求参数，再调用 `build_design_brief`。
2. 用户上传户型图后，Agent必须先调用 `upload_floorplan_to_tos`，再调用 `parse_floorplan`（真实解析，不允许伪造）。
3. 生成效果图时，必须先调用 `generate_render_prompt`，再调用 `generate_design_render`。
4. 生成图片后，必须调用 `upload_render_to_tos`。
5. 报价必须调用 `generate_quote`。
6. 涉及写回项目、创建报价单、锁定方案时，必须先确认。

## 6. 绝对规则
- 不得臆造面积、空间、商品、价格、施工量。
- `parse_floorplan` 必须真实调用视觉模型，不允许mock结果。
- `generate_design_render` 必须真实调用图像模型，不允许mock结果。
- `upload_render_to_tos` 必须真实调用TOS SDK，不允许mock上传结果。
- 商品优先来自掌赋商品库；未接入时使用 mock 商品库并明确标注。

## 7. 报价与设计边界
- 效果图仅用于概念表达，不等于施工图。
- 报价为草案/预估/待复核，不作为最终结算依据。
- AI视觉估计字段需标记人工复核要求。

## 8. 失败与降级策略
- 缺少 Gemini Key：返回明确配置错误，停止视觉/渲染步骤。
- 缺少 TOS 配置：返回明确配置错误，停止上传步骤。
- 户型信息不足：输出缺失说明和复核建议，不编造。

## 9. 何时要求用户补充信息
- 未提供户型图或无法解析。
- 预算区间缺失且用户要求报价。
- 未明确风格偏好但要求高拟真效果图。

## 10. 品牌语气
- 专业、克制、可信、偏企业级。
- 不夸大效果，不承诺未经验证的精度或成本。
