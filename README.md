🔗 **https://wadesha.github.io/grasshopper-field-notes/**

# 🦗 Field Notes on Grasshoppers

193 篇观察笔记式卡片，覆盖蝗虫学最新研究（1951–2026）。特征：

- 📜 瀑布流 · 每次随机推送 5 篇 · 下滑自动加载
- 🔀 刷新即洗牌 · 手机端下拉重排
- 🏷️ 主题过滤器（生理/生态/进化/行为/基因组/综述）
- 📝 每篇从"可观察的现象"出发 · 学术发现自然融入叙事
- 🖼️ 零图片 · 纯文字 · 单文件 HTML

---

## 写作规范 Writing Spec

### 核心原则
1. **从现象出发，不复制摘要。** 每张卡片的开头必须是一个可以亲眼观察到的现象、一个自然产生的疑问、或一个反直觉的日常场景。不要以"本研究通过实验证明"开头。
2. **学术发现自然融入叙事。** 研究结论通过"后来有人发现…""研究人员注意到…"等叙述节奏引入，而不是以 blockquote 的引用格式呈现。
3. **不追求完整自洽的学术逻辑。** 不需要像教科书一样介绍完美的理论体系。可以提学术发现，但只需作为注脚或顺便一提。
4. **语言口语化但不幼稚。** 面向有兴趣的爱好者/博物学家，不是儿童科普也不是学术汇报。

### 卡片结构
```
标题：一个吸引人的现象描述或问题（15-30 字）
摘要：3 行以内的引子，勾住读者
展开内容：2-5 段叙述 + 1 个 `.note` 关键信息块
来源行：`.source` 标注原始论文信息
```

### 技术约束
- 每张卡片用 `<article class="card" data-tags="..." data-year="...">` 包裹
- 标签用空格分隔：`phys eco evo beh gen meta`
- 展开内容放在 `.card-body` 中，初始隐藏
- 不包含任何 `<img>` 标签

---

## 网站技术架构

| 特性 | 实现 |
|---|---|
| 随机推送 | Fisher-Yates 洗牌算法，页面加载时执行 |
| 无限滚动 | Intersection Observer 监听底部 sentinel |
| 每次加载 | 5 张卡片（`CARDS_PER_BATCH` 常量） |
| 过滤器 | 按 `data-tags` 属性筛选，切换时重新洗牌 |
| 下拉刷新 | `touchstart`/`touchend` 检测 >100px 下拉 |
| 卡片展开 | 点击切换 `.expanded` class，链接点击不触发 |
| 移动端 | `<meta viewport>` + CSS 媒体查询 |

---

## 更新流程

### 日常增加卡片
```
1. WebSearch → 搜索蝗虫相关论文（每次 3-5 个关键词方向）
2. 提取关键信息 → 标题、核心发现、期刊、DOI
3. 改写成观察笔记体 → 遵循上方写作规范
4. 追加到 index.html → 在 </div><!-- end feed --> 前插入
5. 更新头部计数器 → heroTotal
6. git add + commit + push → GitHub Pages 自动部署
```

### 自动化搜论文（GitHub Actions）
`.github/workflows/fetch-papers.yml`
- 每周一 UTC 8:00 自动运行
- 查询 Europe PMC API（免费、无需 key）
- 6 个关键词：grasshopper/locust/Orthoptera/Acrididae/Melanoplus/Schistocerca
- 过滤近 90 天内发表
- 结果写入 `data/new_papers_YYYY-MM-DD.json`
- 修改后需 WorkBuddy 手动改写成卡片

### 部署
- 仓库：`Wadesha/grasshopper-field-notes`
- Pages 源：`master` 分支根目录
- URL：`https://wadesha.github.io/grasshopper-field-notes/`

---

## 内容统计

| 维度 | 数值 |
|---|---|
| 卡片总数 | 193 |
| 时间跨度 | 1951–2026 |
| 搜索轮次 | 32 |
| 覆盖期刊 | 30+ |
| HTML 体积 | ~115 KB |
| 图片 | 0 |

**主题标签分布**：行为 · 生理 · 生态 · 进化 · 基因组 · 综述/方法

---

## 排除的文件
原始学术 PDF 和照片不推送到仓库（.gitignore 过滤）：`*.pdf *.JPG *.png *.gif *.docx *.html`
