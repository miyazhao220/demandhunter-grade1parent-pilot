# 小学一年级家长机会雷达 Alpha

这是当前可分享的一年级家长机会雷达静态发布包。本版本不重新采集评论数据，基于既有评论样本、全量语义降噪和全量供给调研提升阅读、协作和后续迭代效率。

线上页面：<https://miyazhao220.github.io/demandhunter-grade1parent-pilot/>

## 文件

- `index.html`：可直接打开或部署的机会雷达页面。
- `grade1-data.json`：页面读取的评论证据、主题、需求簇、子需求点和台账数据。
- `grade1-asset-v1.json`：规范化 Canonical Asset，证据只保存一份。
- `grade1-supply-assets-v1.json`：完整供给资产，包含评论供给证据、官方来源、供给实体和需求—供给关系。
- `grade1-supply-catalog.json`：兼容旧流程的官方供给摘要；详细供给以 `grade1-supply-assets-v1.json` 为准。
- `grade1-release-manifest.json`：当前发布版本、资产计数、流水线、文件大小及 SHA-256。
- `sub-need-points-v005.csv`：需求簇下的子需求点明细。
- `count-comparison-v004.csv`：旧值到新值的计数对比。
- `self-check-sample-v004.json`：抽样自检样本。

## 本地预览

从 `opportunity-radar` 目录启动静态服务：

```bash
python3 -m http.server 8106
```

访问：

```text
http://localhost:8106/parent-radar-pilot/published/grade1/
```

## 发布前校验

从 `parent-radar-pilot` 目录执行：

```bash
node scripts/prepare-grade1-release.mjs --verify-only
```

重新生成发布清单：

```bash
node scripts/prepare-grade1-release.mjs \
  --release-id grade1-alpha-v018-supply \
  --data-tag v018
```

生成可部署目录：

```bash
node scripts/prepare-grade1-release.mjs \
  --verify-only \
  --deploy-dir /tmp/demandhunter-grade1-deploy
```

## 当前页面结构

页面使用渐进展开结构，避免首屏堆满证据：

1. 首屏展示小学一年级家长的 13 个主题域。
2. 点击主题、需求簇和子需求后，查看「谁 / 什么场景 / 遇到什么问题 / 目前使用什么办法 / 现有供给 / 供给缺口 / 还缺什么验证」。
3. 在「关联需求簇」中点击需求簇，展开该簇下的子需求点。
4. 点击子需求点后，查看该子需求点的机会假设卡片、评论数、评论原文、作者、平台、搜索词、点赞/回复和原帖链接。
5. URL hash 深链可回到当前展开位置，方便定位同一张卡片。
6. 评论默认展示 6 条，可继续加载更多或展开全部。

## 证据口径

- 只把评论/回复作为用户证据；帖子/视频标题只作为评论上下文。
- 主计数只包含直接需求证据；机构/B2B/软广帖下疑似家长咨询可作为间接需求信号，不进入主计数。
- 营销/引流、同行互评、纯互动、纯表情、无意义短评、单纯夸赞等不进入需求证据。
- 短评论语义不足时，必须结合评论者身份和原帖语境判断；仍不确定则进入未匹配池，不强行归簇。
- 排序优先看证据强度，点赞数只在同档内排序，避免营销互赞抬高噪声。
- 一级机会卡不再展示与需求簇重复的全量评论；评论原文只在需求簇/子需求点展开后查看。

## 采样范围

- 数据来自 TikHub 接口获取的小红书和抖音公开评论样本。
- 检索词 36 个，覆盖一年级家长、幼小衔接、作业、拼音、识字、阅读、数学、英语启蒙、注意力、托管、兴趣班、学习机、学习桌、护眼灯、家校沟通等方向。
- 每平台每检索词搜索第一页，每词最多 5 个帖子，每帖最多 20 条评论，帖子最低评论数 5。
- 小红书发布时间筛选为半年内，排序为 general，内容类型不限；抖音 `publish_time=180`，`sort_type=0`，`content_type=0`。
- 当前不是按评论数倒序采样，也未拆分小红书图文/视频；这是后续采样迭代项。

## 当前需求计数

- 原始评论：3,821
- 主需求证据：548
- 间接需求：185
- 未匹配：707
- 一般观点：679
- 拦截：1,445
- 人群不符：257
- 主计数口径自检：主题证据总数 = 簇证据总数 = 平台去重后证据总数 = 548。

## 当前供给计数

- 已扫描子需求：167
- 找到产品、服务或方法供给：150
- 尚无可靠供给：17
- 已核验官方/权威供给：33
- 官方/权威来源：32
- 评论供给证据：869
- 需求—供给关系：647

页面将供给拆为：

- 用户已在用/使用过
- 用户正在找/考虑
- 已核验产品、服务与方法
- 负向反馈与反例
- 未覆盖环节

## 人工校准资产

人工校准文件位于：

```text
../grade1-calibration/human-review/
```

关键文件：

- `grade1-calibration-human-review-merged.csv`：婷婷和伟峰标注合并后的样本。
- `grade1-calibration-human-review-summary.json`：人工标注统计摘要。
- `grade1-calibration-rule-delta.json`：本轮人工校准沉淀出的规则变化。
- `grade1-cluster-quality-summary.json`：按簇汇总的抽样质量摘要和 precision proxy。

本轮人工校准结论：当前最大风险是「簇归属错误」，其次是营销/互动/夸赞噪声。因此后续加工脚本要优先改进评论语义路由和前置过滤，而不是只调整页面展示。

## 版本说明

- Alpha 当前形态：一级机会卡片 + 需求簇 + 子需求点 + 子需求机会假设卡 + 评论证据 + 深链。
- 已移除顶部 Intent Filter、心智漏斗可见标签、AI 初分/待人工 review 标签、三级灰字摘要、内容强/产品强/资料噪声筛选按钮等易干扰判断的展示。
- 供给侧以 `grade1-supply-assets-v1.json` 为单一事实资产；官方来源只证明公开能力，评论证据才证明用户的寻找、使用或反馈关系，均不直接等于产品效果结论。
