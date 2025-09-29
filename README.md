![横幅图片](images/n8n-and-n8nhackers.png)

# n8n-nodes-brightdata

这是一个 n8n 社区节点。它可以帮助你在 n8n 中使用 [Bright Data APIs](https://docs.brightdata.com/scraping-automation/introduction)。

如果你有任何问题或建议，请[联系我](mailto:support@n8nhackers.com)。

[n8n](https://n8n.io/) 是一个[公平代码许可](https://docs.n8n.io/reference/license/)的工作流自动化平台。

## 安装

请按照 n8n 社区节点文档中的[安装指南](https://docs.n8n.io/integrations/community-nodes/installation/)进行操作。

## 兼容性

该节点在 n8n 版本 1.81.4 上开发与测试。

## 资源

当前节点支持以下资源：

### Marketplace Dataset（数据集市场）

Bright Data 的 Marketplace DataSet API 提供一个集中平台，汇集多个领域的持续更新、合规来源的数据集，并提供灵活的定制与获取方案。

可用操作：

- Deliver Snapshot（交付快照）
- Filter Dataset（过滤数据集）
- Get Dataset Metadata（获取数据集元数据）
- Get Snapshot Content（获取快照内容）
- Get Snapshot Metadata（获取快照元数据）
- Get Snapshot Parts（获取快照分片）
- List Datasets（列出数据集）

适用于：

- 访问 120+ 领域的数据集
- 高质量、合规来源的数据，确保准确与合规性
- 基于公开信息的定期更新
- 即用型与按需方案
- 灵活购买与全面定制
- 适合社交媒体、房地产、B2B 数据与 AI 训练等多种领域

### Web Unlocker

Bright Data 的 Web Unlocker API 通过管理代理与避免封锁，让数据采集更轻松。

只需发送带目标网站的 API 请求，即可获得干净的 HTML/JSON 响应。

Web Unlocker 会处理：

- 最优代理选择
- 自定义请求头
- 指纹管理
- 验证码（CAPTCHA）

适用于：

- 抓取任何网站且避免封禁
- 模拟真实用户行为
- 无自建代理基础设施的团队
- 仅为成功请求付费
- 难以抓取的[高级域名](https://docs.brightdata.com/scraping-automation/web-unlocker/features#current-list-of-premium-domains)（如 MediaMarkt、Hermes 等）

请按照[文档](https://docs.brightdata.com/scraping-automation/web-unlocker/introduction)配置该 API。

#### 使用方法

- Resource：选择 Unlocker API
  - Operation：选择 “Send a Request”
    - Properties：
      - zone：在你已有的 Zone 名称中选择
      - url：使用 Web Unlocker API 访问的目标 URL
      - method：请求目标 URL 的方法
      - format：响应格式。raw 为目标站点的原始响应；JSON 便于处理输出
      - country：访问目标 URL 所使用的国家

### TL;DR - Web Unlocker

![获取每日优惠](images/workflow-sample.png?raw=true "Get deals of the day")

不想看说明？在 n8n 设置 / 社区节点中安装 n8n-nodes-brightdata。复制 [Get deals of the day](https://raw.githubusercontent.com/n8nhackers/n8n-nodes-brightdata/refs/heads/main/use-cases/workflow-sample.json "Get deals of the day") 的 URL，并在 n8n 的 “Import from URL” 菜单中粘贴导入。

该 n8n 工作流会自动收集并发送 MediaMarkt 的“今日优惠”，可按用户偏好定制，并通过邮件发送这些优惠。

该工作流适用于基于用户输入自动推荐商品优惠，并通过个性化邮件发送最佳优惠列表。

#### 工作流概览：

1. 用户通过表单互动：
   - 用户提交表单，选择感兴趣的类别（如家电、手机等）并提供邮箱地址。工作流将基于输入收集个性化优惠。
2. 数据提取：
   - 表单提交后，工作流调用 **Bright Data** 社区节点，使用代理抓取 MediaMarkt 页面（`https://www.mediamarkt.es/es/campaign/campanas-y-ofertas`）。数据以 JSON 格式返回。
3. HTML 内容抽取：
   - 从网页的原始 HTML 中抽取关键内容（如标题和主体），为后续推荐处理提供依据。
4. 使用 OpenAI 生成推荐：
   - 将抽取的数据传入 **OpenAI**（GPT-4o-mini），生成推荐优惠清单。包含按照用户选择的类别进行分类，并必要时过滤或翻译内容。每条推荐包含名称、描述、价格与链接等。
5. 数据结构化：
   - 使用 **SplitOut** 将推荐的优惠列表拆分为单条记录。
6. 文档生成：
   - 使用 **[Document Generator](https://www.npmjs.com/package/n8n-nodes-document-generator)** 社区节点，将推荐优惠填充到 HTML 模板，生成用户友好的文档。
7. 邮件发送：
   - 通过 **SMTP email send** 将包含推荐优惠的文档发送到用户邮箱，并附带个性化信息。

#### 工作流连线：

1. 表单提交 → Bright Data（抓取优惠）
2. Bright Data → HTML 抽取
3. HTML 抽取 → OpenAI（生成优惠）
4. OpenAI → SplitOut（拆分记录）
5. SplitOut → DocumentGenerator（生成 HTML 文档）
6. DocumentGenerator → 发送邮件

#### 最终输出：

- 用户将收到一封包含其所选类别下最佳优惠的个性化邮件。

#### 使用的凭据：

- Bright Data API：抓取 MediaMarkt 数据
- OpenAI API：使用 GPT-4o-mini 生成推荐列表
- SMTP：发送包含优惠的邮件

#### 使用的外部节点：

- **[Bright Data](https://www.npmjs.com/package/n8n-nodes-brightdata)**：用于抓取 MediaMarkt 数据
- **[Document Generator](https://www.npmjs.com/package/n8n-nodes-document-generator)**：通过模板生成 HTML 输出

### TL;DR - Marketplace Dataset

![Marketplace Datasets](images/marketplace-dataset-workflow-sample.png?raw=true "Marketplace Datasets")

全新的 Marketplace Dataset API 允许通过过滤现有 Bright Data 数据集（超过 [160 个数据集](https://docs.brightdata.com/datasets/introduction)）来创建自定义快照。

数据集由 Bright Data 进行更新，无需你自己抓取。

你可以从完整数据集中直接获取记录列表，而无需自己抓取记录。这比从零开始获取数据更快且更经济。你可以使用这些数据集构建自定义 AI 代理，例如：

- 房地产代理：搜索待购房源
- 招聘代理：搜索候选人

当前可用的数据集覆盖：

- LinkedIn
- Instagram
- Facebook
- Airbnb
- Crunchbase
- 以及更多（使用 “List Datasets” 操作获取全部列表）

在 n8n 设置 / 社区节点中安装 n8n-nodes-brightdata。复制 [Get Marketplace Functions](https://raw.githubusercontent.com/n8nhackers/n8n-nodes-brightdata/refs/heads/main/use-cases/marketplace-dataset-workflow-sample.json) 的 URL，并在 n8n 的 “Import from URL” 菜单中粘贴导入。

### TL;DR - 更多示例

你还可以在自托管版 n8n 中使用以下即用型示例：

- [使用 Bright Data 与 Google Sheets 无需抓取，逐条更新 LinkedIn 个人资料](https://raw.githubusercontent.com/n8nhackers/n8n-nodes-brightdata/refs/heads/main/use-cases/Update_LinkedIn_Profiles_one_by_one_with_Bright_Data_and_Google_Sheets_with_no_scrapping.json)![Update LinkedIn Profiles](images/Update_LinkedIn_Profiles_one_by_one_with_Bright_Data_and_Google_Sheets_with_no_scrapping.png?raw=true "Update LinkedIn Profiles")
- [基于 Google Search 与 Bright Data 按需创建商业用途的潜在客户列表](https://raw.githubusercontent.com/n8nhackers/n8n-nodes-brightdata/refs/heads/main/use-cases/Create_a_Prospect_list_for_Commercial_purposes_with_Google_Search_and_Bright_Data_on_demand.json)![Create a Prospect List](images/Create_a_Prospect_list_for_Commercial_purposes_with_Google_Search_and_Bright_Data_on_demand.png?raw=true "Create a Prospect List")

# 更新日志（Changelog）

以下为该节点的更新记录：

- 0.1.33：新增 WebScrapper/Scrape By URL 与 WebScrapper/Create Snapshot 示例
- 0.1.32：修复 MarketplaceDataset/filterSnapshot 中 equals 的问题
- 0.1.31：新增 webScrapper/downloadSnapshot
- 0.1.30：新增 webScrapper/deliverSnapshot，并修复 webScrapper/getSnapshots
- 0.1.29：修复 Marketplace/Get Snapshot Content 的错误
- 0.1.28：修复 webScrapper 资源中的错误
- 0.1.25：为 WebScrapper API 创建资源
- 0.1.24：将 listDatasets 返回在 items 输出属性中
- 0.1.23：在 “Marketplace Dataset API/Trigger Snapshot by URL” 中新增 notify webhook
- 0.1.22：在 API 错误时返回更清晰的信息，并改进 Filter Dataset
- 0.1.21：新增 getSnapshots + Trigger snapshots 以按需生成快照
- 0.1.20：新增作为 AI Agent 工具使用 Bright Data 的支持
- 0.1.19：新增对 Marketplace DataSet 端点的支持
- 0.1.11：新增作为 Agent 工具使用该节点的支持

# 贡献

为让该节点更好用，欢迎告诉我们[你的使用方式](mailto:support@n8nhackers.com)。也欢迎提交 PR。

你可以按以下步骤测试该节点：

```sh
git clone git@github.com:n8nhackers/n8n-nodes-brightdata.git
cd n8n-nodes-brightdata
nvm use
npm install -g n8n
yarn install
npm run build
npm link
mkdir ~/.n8n/custom/
cd ~/.n8n/custom/
npm link n8n-nodes-brightdata
cd ..
n8n start
```

# 问题反馈

如果你遇到任何问题，请在 GitHub 上[告诉我们](https://github.com/n8nhackers/n8n-nodes-brightdata/issues)。

# 关于

由 [n8nhackers.com](https://n8nhackers.com) 提供的节点。若需生产使用或咨询，请[联系我们](mailto:support@n8nhackers.com)。

特别感谢 Jan Oberhauser 打造的 [N8n nodemation](https://n8n.io) 工作流自动化。

# 许可证

[MIT](https://github.com/n8n-io/n8n-nodes-starter/blob/master/LICENSE.md)
