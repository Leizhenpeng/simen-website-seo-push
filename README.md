# 自动推送网站Sitemap至搜索引擎

该项目通过 GitHub Actions 自动从您网站的 sitemap 文件中提取 URL，并将它们推送到百度和必应搜索引擎，以提升网站收录速度。

## 工作原理

1.  **定时触发**: GitHub Actions 会根据预设的 cron 计划（默认为每天早上7点 UTC，即北京时间下午3点）自动运行。
2.  **检出代码**: Action 会检出您的仓库代码。
3.  **环境设置**: 设置 Python 和 Node.js 环境（尽管 Node.js 在当前推送逻辑中可能不是必需的）。
4.  **生成URL列表**: 运行 `generate.py` 脚本：
    *   读取您在脚本中配置的 `site` 和 `sitemaps` 变量。
    *   访问 sitemap 文件，提取所有 URL。
    *   将 URL 分别整理到 `urls.txt` (用于百度) 和 `bing.json` (用于必应) 文件中。
5.  **推送至必应**: 使用 `curl` 命令和您的 `BINGTOKEN`，将 `bing.json` 中的 URL 推送给必应 API。
6.  **推送至百度**: 使用 `curl` 命令和您的 `BAIDUTOKEN` 及 `SITE`，将 `urls.txt` 中的 URL 推送给百度 API。

## 部署步骤

1.  **使用模板创建仓库**:
    *   **重要**: 请勿直接 Fork！点击原始仓库页面右上角的 "Use this template" 按钮，选择 "Create a new repository"。这会创建一个属于您自己的、与原仓库无关的新仓库。

2.  **修改 `generate.py` 配置**:
    *   克隆您刚刚创建的仓库到本地。
    *   打开 `generate.py` 文件。
    *   修改 `site` 变量为您网站的**主域名**（例如：`'https://yourdomain.com'`，确保包含 `http` 或 `https`，结尾**不要**加 `/`）。
    *   修改 `sitemaps` 变量为您网站的 sitemap 文件路径列表（相对于主域名，例如：`['/sitemap.xml', '/another_sitemap.xml']`）。
    *   保存文件并将修改推送到您的 GitHub 仓库。

3.  **设置 GitHub Secrets**:
    *   在您的 GitHub 仓库页面，点击 "Settings" -> "Secrets and variables" -> "Actions"。
    *   点击 "New repository secret" 添加以下 **必需** 的 Secrets：
        *   **`BINGTOKEN`**:
            *   **获取**: 前往 Bing Webmaster Tools -> 设置 -> API 访问 -> API 密钥 -> 新建。
            *   **值**: 复制生成的 API 密钥。
        *   **`BAIDUTOKEN`**:
            *   **获取**: 前往 百度搜索资源平台 -> 用户中心 -> 站点管理 -> API提交，找到接口调用地址 `http://data.zz.baidu.com/urls?site=xxx&token=xxx`，复制 `token=` 后面的那一串字符。
            *   **值**: 粘贴您复制的 token。
        *   **`SITE`**:
            *   **值**: 填写您在百度搜索资源平台**验证通过**的网站主域名（**必须**与百度后台绑定的域名完全一致，包括 `http/https` 和 `www` 等，结尾**不要**加 `/`）。**这是导致 "site init fail" 错误的最常见原因。**

4.  **(可选) 配置谷歌推送**:
    *   如果您需要推送到 Google，请取消注释 `push.yml` 文件中 "Push to Google" 相关的代码块。
    *   按照原始 README 或 Google Cloud Platform 文档设置 Indexing API 访问权限，获取服务账号的 JSON 密钥文件。
    *   添加一个新的 GitHub Secret：
        *   **`GOOGLE_SERVICE_ACCOUNT`**:
            *   **值**: 将整个服务账号 JSON 文件的**内容**粘贴进去。
    *   确保服务账号的 email 地址已被添加为您 Google Search Console 对应网站的"委托所有者"。

5.  **完成**: 配置完成后，GitHub Actions 将根据 `push.yml` 中设置的 `schedule` 自动运行。您也可以在仓库的 "Actions" 页面手动触发 `push` 工作流进行测试。

## 所需环境变量 (Secrets) 总结

以下是运行此项目所需设置的 GitHub Secrets 列表：

*   **必需**:
    *   `BINGTOKEN`: 用于必应 API 推送。
    *   `BAIDUTOKEN`: 用于百度 API 推送。
    *   `SITE`: 用于百度 API 推送，**必须**与百度资源平台验证的站点完全一致。
*   **可选**:
    *   `GOOGLE_SERVICE_ACCOUNT`: (如果启用谷歌推送) Google 服务账号的 JSON 凭证内容。

**不再需要** `BOT_DEPLOY_KEY`, `BOT_NAME`, `BOT_EMAIL`, `BOT_GITHUB_TOKEN` 这些用于旧版 BotLog 工作流的 Secrets。

---

Enjoy it!

---
