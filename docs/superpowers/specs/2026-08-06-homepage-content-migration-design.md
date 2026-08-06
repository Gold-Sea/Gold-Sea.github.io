# 设计:Gold-Sea 旧站内容迁移至 academic-homepage 模板

日期:2026-08-06
状态:已获用户确认(方向、部署、内容范围、布局方案均已逐项确认)

## 背景与目标

- 本仓库是 Yinmin Zhong (PKUFlyingPig) 学术主页的克隆,基于 [luost26/academic-homepage](https://github.com/luost26/academic-homepage) 模板(Jekyll)。
- 用户 Zili Zhang(GitHub: Gold-Sea)的旧主页位于 `/Users/zzl/Desktop/Archive/Gold-Sea.github.io`(academicpages 模板),自定义域名 `zilizhang.site`。
- 目标:**保留本模板的页面设计,把旧站的个人内容全部迁入,替换 Yinmin Zhong 的所有个人数据**,最终部署到 `Gold-Sea/Gold-Sea.github.io` 并沿用 `zilizhang.site` 域名。

## 已确认的决策

1. 迁移方向:新模板 + 用户内容(非照搬旧站)。
2. 部署:推送到 `Gold-Sea/Gold-Sea.github.io`,CNAME 保留 `zilizhang.site`。
3. 迁移范围:荣誉 Honors、Teaching + Service、Interests + 跑步页链接、`files/` 全部 PDF。
4. 布局方案 A:Honors 用模板自带 Awards 区块;Teaching/Service 仿照 Awards 样式在主页卡片中新增两个区块(唯一模板改动);Interests 写入简介末段。

## 变更明细

### 1. `_data/profile.yml` — 全量替换为用户信息

- `primary_name` / `navbar_name`: "Zili Zhang";`secondary_name` 留空(旧站中文名「章梓立」原本即为注释隐藏,不显示)。
- `positions`: PhD student @ Peking University(logo 沿用 `PKU_red.png`)。
- `email`: `zzlcs@pku.edu.cn`(旧站页面展示的邮箱)。
- `cv_link`: `/files/zzl-cv.pdf`。
- `gscholar`: `310QUvQAAAAJ`;`github`: `Gold-Sea`;`orcid`: `0000-0003-4209-9451`;无 twitter/linkedin/wechat。
- `short_bio`(3 段,内容来自旧站 about.md):
  1. 四年级 Ph.D. candidate @ PKU [Computer Systems Research Group](https://github.com/pkusys)(2023-Present),导师 [Xin Jin](https://xinjin.github.io/);研究方向:Multimodal LLM (MLLM) pretraining 与 reinforcement learning 的系统。
  2. B.E. @ School of EECS, PKU(2019-2023);曾在 Software Engineering Institute 任 research assistant,导师 Xin Jin(2020-2023)。
  3. Interests:业余跑者(链接 [ITRA 主页](https://www.itra.run/RunnerSpace/ZHANG.Zili/4938114) 与 [Running Page](/running_page/))、户外旅行/徒步/滑雪/露营、视频游戏。
- `portrait_url`: `/assets/images/photos/avatar.jpg`(文件内容替换为旧站头像 `images/zzl3.jpeg`);`portrait_caption`: "Get Lost In Nature"(旧站个性签名)。
- `education`:
  - Peking University, School of Computer Science, Ph.D. Student, Sep. 2023 - present
  - Peking University, B.E. in Computer Science (School of EECS), Sep. 2019 - Jul. 2023
- `experience`(logo:bytedance.png、stepfun.png 已存在;RedNote logo 需新增,见 §6):
  - RedNote, RedStar Intern — Infrastructure for Multimodal LLM + RL, Sep. 2025 - Jun. 2026
  - ByteDance Seed, TopSeed Intern — Multimodal LLM + RL, Apr. 2025 - Sep. 2025
  - StepFun, Research Intern — Infrastructure for Multimodal LLM Training, Apr. 2024 - Apr. 2025
- `awards`(来自旧站 hornors.html,16 项,倒序;名称保留原文含比例数字):
  - 2025: NSFC Basic Research Program for Young Students (PI);Top 10 Academic Stars, School of CS, PKU (10/800)
  - 2024: Merit Student (1/56);National Scholarship (Top 0.4% nationally);Presidential Scholarship of PKU
  - 2023: Top 10 Bachelor Thesis, School of EECS (10/408);Outstanding Graduation Thesis, PKU (33/4239);Outstanding Graduation Thesis, Beijing;Excellent Graduate, PKU (613/4239);Representor of Excellent Graduates, PKU (14/4239)
  - 2022: Exceptional Award for Academic Innovation (5/408);Award for Scientific Research;Lee Wai Wing Scholarship
  - 2021: Award for Scientific Research
  - 2020: Award for Academic Excellents;The Third Prize of Peking University Scholarship
- 新增 `teaching`(3 项):
  - Teaching Assistant, Operating System (Honor Track), PKU — 2024 Spring
  - Teaching Assistant, Introduction to Computer System (Honor Track), PKU — 2022 Fall
  - Teaching Assistant, Introduction to Computer System, PKU — 2021 Fall
- 新增 `service`(2 项):
  - IEEE TMC, Invited Reviewer — 2026
  - EuroSys, Shadow PC — 2025

### 2. `_includes/widgets/experience_card.html` — 唯一模板改动

仿照现有 `awards` 区块的结构(`{% if site.data.profile.xxx %}` + `col-md-6` + name/date 行),新增 **Teaching** 与 **Service** 两个可选区块。masonry 布局自动排版,与 Education/Experience/Honors & Awards 同风格。数据不存在时区块不渲染(对模板其他用户无副作用)。

### 3. 论文 `_publications/` — 以旧站 about.md 的 12 篇为准

作者列表一律以旧站 about.md 为准(用户自己维护的版本);`_data/authors.yml` 中加粗对象改为 `"Zili Zhang"`(模板自动处理 `Zili Zhang*` 的等贡献后缀匹配与图例)。全部 `selected: true`,主页展示全部 12 篇(与旧站首页一致;后续可自行调 selected 精选)。排序按真实发表时间(`date` 字段倒序),与旧站的人工排序略有差异,可接受。

**保留改造 7 篇**(已有文件,改作者/链接,封面沿用):

| 文件 | venue/pub_date | 要点 |
|---|---|---|
| 2025/disttrain.md | SIGCOMM 2025 | 作者修正为旧站版本(Yimin Jiang、Daxin Jiang 等 9 人);Paper 链接 `https://dl.acm.org/doi/10.1145/3718958.3750472`;Slides `/files/disttrain-sigcomm.pdf`(改相对路径) |
| 2026/ultraep.md | Preprint (arXiv 2606.04101) | 作者 13 人,Zili Zhang 第 8 位 |
| 2026/relibra.md | Preprint (arXiv 2605.08639) | 作者 9 人 |
| 2025/tokenlake.md | Preprint (arXiv 2508.17219) | 作者 7 人 |
| 2025/streamrl.md | Preprint (arXiv 2504.15930) | 作者 14 人 |
| 2026/fastserve.md | NSDI 2026 | 前三作者带 `*` 等贡献(Bingyang Wu*, Yinmin Zhong*, Zili Zhang*),共 9 人;arXiv 2305.05920 |
| 2025/rlhfuse.md | NSDI 2025 | 作者 11 人;arXiv 2409.13221 |

**新增 5 篇**(无封面,模板自动生成气泡占位图;abstract 取论文 arXiv 摘要的首句或前两句):

| 文件 | venue | Paper 链接 |
|---|---|---|
| 2026/bigmac.md — BigMac: Breaking the Pareto Frontier of Compute and Memory in Multimodal LLM Training | Preprint | arXiv 2605.25451(旧站给出) |
| 2025/ragcache.md — RAGCache: Efficient Knowledge Caching for Retrieval-Augmented Generation | ACM TOCS 2025 | arXiv 2404.12457(旧站给出) |
| 2024/dlora.md — dLoRA: Dynamically Orchestrating Requests and Adapters for LoRA LLM Serving | OSDI 2024 | 旧站为空链接;实施时联网核实 USENIX 演示页真实 URL |
| 2024/jolteon.md — Jolteon: Unleashing the Promise of Serverless for Serverless Workflows | NSDI 2024 | 同上 |
| 2024/rummy.md — Fast Vector Query Processing for Large Datasets Beyond GPU Memory with Reordered Pipelining | NSDI 2024 | 同上 |

链接原则:只使用旧站已有的链接,或实施时联网核实到的真实 URL(USENIX/arXiv/ACM DL);核实不到的不放链接,不得凭 URL 模式拼造。各论文 `date` 字段按 arXiv 提交月或会议召开日期填写。

**删除 11 篇**(非用户论文):distserve、loongserve、megascale、distmind(2024);deepseekv32、sd(2025);heddle、mars、deepseekv4(2026);elasticflow、alpaserve(2023)。同时删除其对应封面图(SpecRL.png、distserve.png、loongserve.png、alpaserve.png、deepseekv32.png、deepseekv4.png、heddle.png、mars.png 等,以删除后 grep 无引用为准)。

### 4. 文件与资产

- 旧站 `files/` 整目录(11 个 PDF,含 `zzl-cv.pdf`、`disttrain-sigcomm.pdf`)复制到仓库根 `files/`,保持 `zilizhang.site/files/…` 旧链接全部有效。
- 删除 Yinmin 的 `assets/cv/resume.pdf`(及空目录)。
- favicon:确认 `_layouts/default.html` 引用的 icon 路径后,用旧站 `images/favicon-32x32.png` 替换 `assets/images/icon/icon.png` 内容。
- 删除演示/遗留内容:`_showcase/` 全部、`showcase.html`、`_news/` 全部示例(display.yml 的 `show_news: false` 保持不变)、`assets/images/etc/` 猫图与 `tim_the_beaver.png`、无用 badge(`deepseek.png`、`berkeley.webp`、`alibaba.png`、`MIT_Social_circle.png`)。`preview.png` 若 README 重写后不再引用则一并删除。
- 新增 badge:`assets/images/badges/rednote.png`(从公开 favicon 服务下载小红书图标;下载失败则复制一张既有 badge 尺寸的中性占位图并在交付说明中标注待替换)。
- 删除 `.github/`(上游仓库的 issue 模板,与个人主页无关)。
- `README.md` 重写:说明这是 Zili Zhang 的个人主页(zilizhang.site),基于 luost26/academic-homepage 模板、参考 PKUFlyingPig 的站点结构;`LICENSE`(MIT)保留。
- 仓库中已有的 `.DS_Store` 文件删除并加入 `.gitignore`。

### 5. 站点配置

- `CNAME`:内容改为 `zilizhang.site`。
- `_config.yml`:新增 `exclude: [docs, README.md, LICENSE]`(避免设计文档等发布到线上);其余不动。
- `_data/navigation.yml`:保持 About Me + Publications 两项不变(CV 链接在主页资料卡内,跑步页链接在简介里)。
- `_data/display.yml`:不动(`show_news: false`、页脚模板署名保留)。
- `_data/authors.yml`:`"Zili Zhang": bold: true`,并为常见合作者留注释示例。

### 6. Git 与发布流程

- 已完成:commit `70c7e5a` 模板基线;仓库级 git 身份 `Zili Zhang <zhangzili1201@gmail.com>`(如需更换请告知)。
- 迁移改动按逻辑分批提交(profile/模板区块、publications、assets/files、站点配置)。
- `git remote add origin https://github.com/Gold-Sea/Gold-Sea.github.io.git`;**推送由用户自行执行**(将覆盖远端旧站,建议推送前二次确认;旧站在本地 Archive 有完整备份)。

### 7. 验证

1. 若本机有 Ruby/Bundler:`bundle install && bundle exec jekyll build` 构建成功,本地 serve 抽查主页/论文页。若无 Ruby 工具链:做静态一致性检查,并说明由 GitHub Pages 构建验证。
2. 残留信息检查:`grep -ri` 确认仓库(除基线 git 历史外)无 `yinmin`、`zhongyinmin`、`PKUFlyingPig`、`csdiy`、`UUM_zLwAAAAJ`、`0000-0002-2504-7652`、`Zaizuo Gong` 等 Yinmin 个人信息。
3. 引用完整性:所有 `profile.yml`/`_publications/*.md` 引用的图片、PDF 路径在仓库内存在;已删除文件无残留引用。
4. 内容核对:12 篇论文的标题/作者/venue/链接与旧站 about.md 逐条一致;awards/teaching/service 与旧站逐条一致。

## 接受的取舍

- 旧站 `/hornors/`、`/talks/` 等子页 URL 在新站不存在(404):荣誉已并入主页,talks/blog 旧站本就未启用或为模板示例,不迁移。
- 主页论文排序为严格时间倒序,与旧站人工排序(BigMac、DistTrain 置顶)略有不同;如需置顶可后续微调 `date` 字段。
- 新增 5 篇论文暂无封面图(用模板占位图),用户可后续自行补充。
