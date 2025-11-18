# Browser-Use Release Monitor

## 目的

通过 GitHub Actions 自动监控 [browser-use](https://github.com/browser-use/browser-use) 项目的版本发布，及时获取重大更新信息。

## 工作机制

- **监控频率**：每 6 小时检查一次
- **通知方式**：飞书机器人推送
- **防重复**：记录已处理的版本，避免重复通知

## 流程图

```mermaid
graph TD
    A[定时触发<br/>每6小时] --> B[拉取代码仓库]
    B --> C[读取本地记录文件<br/>.browser-use-release-record.json]
    C --> D[调用 GitHub API<br/>获取最新 release]
    D --> E{是否有新版本?}
    
    E -->|lastTag = 最新版本| F[跳过处理<br/>无需通知]
    E -->|lastTag ≠ 最新版本| G[获取版本详情]
    
    G --> H[提取信息:<br/>- Release 标题<br/>- 版本号<br/>- 发布时间<br/>- Commits 数量<br/>- 变更说明]
    H --> I[发送飞书通知]
    I --> J[更新记录文件<br/>写入最新 tag]
    J --> K[提交记录文件到 Git<br/>标记 skip ci]
    K --> L[结束]
    F --> L
    
    style E fill:#ffe6cc
    style I fill:#d5e8d4
    style F fill:#f8cecc
```

## 通知内容

每次新版本发布时，飞书会收到包含以下信息的消息：

- 🏷️ **Release 标题**
- 📦 **版本号**
- 📅 **发布时间**
- 📝 **Commits 数量**
- 🔍 **主要变更**（前10条 commit）
- 🔗 **提交对比链接**
- 🔗 **Release 详情链接**

## 配置

需要在 GitHub Repository Secrets 中配置：
- `LARK_WEBHOOK_URL`：飞书机器人的 Webhook 地址

