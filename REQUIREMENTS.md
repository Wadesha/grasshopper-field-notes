# 蝗虫野外笔记静态站 · 需求与续做规范（Requirements & Resume Spec）

> 用途：本文件是「中断后继续执行不跑偏」的唯一权威依据。每次新会话/新中断后，先读本文件 + `MEMORY.md` + 当日 `YYYY-MM-DD.md` 日志，再动手。
> 最后更新：2026-08-04（第 44–47 轮已推送上线，共 463 篇；第 48 轮待续写 #464）。

---

## 0. 一句话目标

构建一个「蝗虫 / 飞蝗野外笔记式」**静态 HTML 瀑布流（masonry）站点**，发布到 **GitHub Pages**（`main` 分支，站点 `https://wadesha.github.io/grasshopper-field-notes/`）。
只写蝗虫/飞蝗内容；每张卡从**自然观察现象切入**，把学术发现编织进叙事；**纯文本、无图**；每篇必须是**超长高密度长文（巨量 token）**。

---

## 1. 硬性约束（不可违背）

1. **内容范围**：仅蝗虫/飞蝗（直翅目：蚱蜢科、蝗科、沙漠蝗 *Schistocerca gregaria*、飞蝗 *Locusta migratoria* 等）。可提近缘类群（蟋蟀、螽斯）作对照，但主体必须是不折不扣的蝗虫。
2. **无图片**：任何卡都不得含 `<img>` / `<picture>` / 外链图。本站是纯文本笔记。
3. **单篇长度（用户重点纠正过）**：每卡正文 **1000–1500 字（中文）为达标线，最低不得低于 1000 字**（校验会卡 <900）。用户原话：*"你是不是理解错了 我需要你每一篇都消耗巨量token而不是节省"* —— 不得为了省 token 写短，宁可补段也要堆满。
4. **叙事风格**：从具体自然观察/现象切入（如"某日田间看到……"），再把该现象背后的学术发现（论文、机制、数据）织入，避免干巴巴的文献摘录。
5. **不自动推送**：绝不在未获用户显式 **"推送 / 发布 / 部署"** 指令时 `git push`。只能本地提交。

---

## 2. 卡片 HTML 模板（必须与现有卡逐字节一致）

插入点唯一字符串：`</div><!-- end feed -->`（全站仅 1 处），新卡插在它**之前**。

```html
<article class="card" data-tags="beh,eco" data-year="2020">
  <div class="card-head">
    <span class="card-no">#464</span>
    <h2 class="card-title">标题：从某个田野现象切入</h2>
  </div>
  <div class="card-meta">
    <span class="meta-author">研究者 / 年份</span>
    <span class="meta-journal">Nature / Science / PNAS ...</span>
  </div>
  <div class="card-tags">
    <span class="tag">beh</span><span class="tag">eco</span>
  </div>
  <div class="card-snippet">一句话钩子（展示在折叠态）。</div>
  <div class="expand-hint">▸ 点击展开</div>
  <div class="card-body">
    <p>第 1 段：自然观察现象切入……</p>
    <p>第 2 段起：学术发现、机制、数据……（堆满 1000–1500 字）</p>
    <div class="note">边注/来源提示（可选，但多数卡有）。</div>
  </div>
</article>
```

**结构不变量（校验会卡）**：
- 每张卡**恰好 1 个** `<div class="expand-hint">▸ 点击展开</div>`
- 每张卡**恰好 1 个** `<div class="card-body">`
- 全站 `<img` 数量必须为 **0**
- 全站 `</div><!-- end feed -->` 必须为 **1**
- 文件必须以 `</html>` 收尾

---

## 3. 标签体系（实际词表，7 个）

| 缩写 | 含义 |
|---|---|
| `beh` | 行为（迁飞、聚集、取食、交配、声讯） |
| `phys` | 生理（代谢、衰老、耐寒、飞行代价、ROS） |
| `eco` | 生态（食物网、三营养级、气候、遥感、经济损失） |
| `evo` | 进化（型变、化石、染色体、比较） |
| `gen` | 基因组（测序、转座子、表观遗传、病毒组） |
| `chem` | 化学（4VA、PAN、信息素、化学防御、味觉） |
| `meta` | 综述（整合、方法论、史话） |

每卡 `data-tags` 取 1–多个，逗号连接；同时要在 `.card-tags` 内生成对应 `<span class="tag">x</span>`。
`data-year` 仅作展示（JS 不数值解析），古生物/化石可用 `"300 Mya"` 这类早年份或具体公元年份。

---

## 4. 轮次工作流（Round Workflow）

每轮 = **18 张卡**（编号连续，如 #392–#409）。完整步骤：

1. **检索**：并行 9 组 `WebSearch`（topic=academic），覆盖本轮 9 个主题，取权威来源（Nature / Science / PNAS / 专业期刊 + 年份 + DOI 倾向）。
2. **落卡**：写 18 张长文卡，用脚本 `insert_rXX.py`，以
   `html.replace(marker, block + "\n\n" + marker, 1)` **一次性插入整块**（放弃先 insert 再 expand 的两步法，一步到位写长文）。
3. **补段**：对正文 <1000 字的卡，用 `patch_rXX.py` 在 `</p>\n    <div class="note">` 前插一段 dense 正文，确保全部 ≥1000 字（历史上常需补 1–12 张）。
4. **计数**：更新 Hero `<strong id="heroTotal">N</strong>` 与筛选 `<span class="count" id="filterAllCount">N</span>` 为总数；其余 JS 动态重算。
5. **校验**：Python 校验断言（见 §5）。必须全过才提交。
6. **提交**：本地 `git add index.html README.md && git commit -q -m "Round XX: 18 long-form cards (#a-#b), total N"`（**不推送**）。
7. **记忆**：追加 `.workbuddy/memory/YYYY-MM-DD.md`（含本轮 18 卡主题清单、校验数字、提交 hash、是否推送）。

---

## 5. 校验脚本要点（关键断言）

对 `index.html` 跑：

```python
import re
html = open('index.html', encoding='utf-8').read()
cards   = html.count('<article class="card"')
hints   = html.count('<div class="expand-hint">▸ 点击展开</div>')
imgs    = html.count('<img')
endfeed = html.count('</div><!-- end feed -->')
ends    = html.rstrip().endswith('</html>')
# 逐卡检查恰好 1 个 hint
bad = [m for m in re.findall(r'<!-- (\d+):.*?</article>', html, re.S)
       if m.count('<div class="expand-hint">▸ 点击展开</div>') != 1]
# 新 18 卡正文纯文字数
new_bodies = [len(re.sub(r'<[^>]+>','', re.search(r'<div class="card-body">(.*)</div>\s*</article>', c, re.S).group(1)))
              for c in re.findall(r'<!-- (4XX|4YY):.*?</article>', html, re.S)]
assert cards==N and hints==N and imgs==0 and endfeed==1 and ends and not bad
assert min(new_bodies) >= 1000   # 历史目标线；至少 >= 900
```

预期（第 47 轮末）：`cards 463 | hints 463 | imgs 0 | endfeed 1 | ends True | bad=[] | 新卡 min≥1000`。

---

## 6. 工具与环境

- **工作目录**：`C:\Users\wade\OneDrive\文档\grasshopper`
- **Python（managed）**：`C:\Users\wade\.workbuddy\binaries\python\versions\3.13.12\python.exe`
  - 注意路径含**空格**与中文，Bash 调用须用引号：`cd "/c/Users/wade/OneDrive/文档/grasshopper"`
- **远端**：`https://github.com/Wadesha/grasshopper-field-notes.git`，分支 `main`，GitHub Pages 自动部署。
- **推送方式**：本机 Git Credential Manager 已存有效 Wadesha 凭据，`git push origin main` 直接成功（不需 PAT）。推送前必须 `grep` 确认待推文件不含任何 GitHub 个人访问令牌（PAT，形如 ghp 开头的一段凭证）。

---

## 7. 当前进度（截至 2026-08-04，21:08）

- **总量**：**463 篇**（#1–#463），`index.html` ~**1052 KB**（已破 1 MB，加载偏慢）。
- **第 44–47 轮**（#392–#463，共 72 张）：已写、已本地提交、已**推送**上线（最新提交 `6564e74`，`cc64f79..6564e74`，领先归 0）。
- **第 48 轮**：9 组素材已检索（生物钟 / 蜕皮激素 / 蘑菇体学习记忆 / 微孢子虫 / 中药 / 几丁质敷料 / 昆虫航天 / 卵滞育 / 寄生天敌），但插入脚本被中断、**未写入**。下次续写从 **#464** 起。
- 原始"10 轮"指令已完成第 44–47 轮；若继续应做第 48 轮及以后（每个 Round 18 张）。

---

## 8. 待决策 / 开放项

- 是否继续第 48 轮补到 ~500 篇？（素材已检索，可直接落卡）
- 单文件 >1 MB，GitHub Pages 加载偏慢；后续加卡建议**拆分**（按年代/主题分文件 + 合并加载）或**压缩**（去注释/合并空白）。
- 游离未跟踪脚本（`insert_r41~r48.py`、`patch_r44~r47b.py`、`expand_r38a/b~r42.py`）与 `MFM.txt`/`tex.txt`/一份 PDF 同名 HTML 需**用户手动清理**（safe-delete 护栏禁止 agent 删除；OneDrive 改名常被锁）。

---

## 9. 续做检查清单（Resume Checklist）

- [ ] 读 `MEMORY.md` + 本文件 + 当日 `YYYY-MM-DD.md` 日志，确认当前卡号与总数（避免重复/跳号）。
- [ ] `git rev-list --count origin/main..HEAD` 确认未推送提交数。
- [ ] 写新一轮：沿用 `insert_rXX.py` 模板，18 张，每张正文 **≥1000 字**。
- [ ] 跑 §5 校验，必须全过（`cards==N, hints==N, imgs==0, endfeed==1, ends, 每卡 1 hint, 新卡 avg≥1000`）。
- [ ] 本地提交；**未经用户"推送"指令绝不 push**。
- [ ] 追加当日 daily log。
- [ ] 用户说"推送"时才 `git push origin main`（先 token 扫描）。
