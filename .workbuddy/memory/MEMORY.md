# Time Machine — 项目记忆

## 关键决策

- **Event 识别规则**：Journals 不作为 Event 来源；Pages 目录下每个 .md 文件 = 一个 Event（除 contents.md 等内部页面）。不需要 `type:: event` 标记。这是 Kermit 的实际用法，与 PRD 原始设计不同。
- **插件化架构**：IParser / IStorage / IRenderer / IImageProcessor 四个接口，Builder 只调度。
- **数据流**：Logseq Graph → Parser → Events → Image Processor → Renderer → Static HTML（单向，不可逆）
- **增量图片处理**：检查输出文件是否存在，存在则跳过。cleanOutput() 保留 assets/ 目录。

## 技术栈

- TypeScript (CommonJS), Node.js 22
- marked (Markdown→HTML), sharp (图片处理), commander (CLI), chalk (彩色输出)
- 前端：纯 HTML/CSS/JS，localStorage 存解锁状态

## 已实现功能

- Phase 0-3：项目骨架、Parser、图片处理、渲染器、随机/解锁、Archive
- 双链关系：Builder.buildRelations() — backlinks + related events (by shared tags)
- Link 解析：[[link]] → 实际 Event 页面链接（decodeURIComponent 处理 marked URL 编码）
- 视频渲染：![[video.mp4]] → <video controls>，复制到 dist/assets/videos/
- Parser 页面级解析：全文件内容提取，支持自由文本 + 属性 + H1 标题混排
- Phase 5：R2Storage / OSSStorage 占位（委托 LocalStorage + 警告）
- Phase 6：BuildStats 摘要、tm deploy --platform、tm init 命令

## 路径

- 项目根目录：`D:/kermit/Document/Code/time-machine`
- Logseq Graph：`time-machine/graph/`（已从 `D:/kermit/Document/Article/EventCloud` 迁移）
- 配置文件：`config.json`（logseqPath 指向 `./graph`）

## 重要修复

- 前端 JS 不能用 `fetch()` 加载数据（预览浏览器安全上下文会阻塞）。改为内联 `<script type="application/json" id="event-index">` 方案，零网络请求。
