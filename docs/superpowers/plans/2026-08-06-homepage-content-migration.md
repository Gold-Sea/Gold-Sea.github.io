# 主页内容迁移实施计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 把 Zili Zhang 旧站(academicpages)的全部个人内容迁入本仓库的 academic-homepage 模板,替换 Yinmin Zhong 的所有个人数据,产出可部署到 Gold-Sea.github.io(域名 zilizhang.site)的个人主页。

**Architecture:** 纯内容迁移:数据文件(`_data/*.yml`)全量替换、论文集合(`_publications/`)按旧站 12 篇重建、静态资产替换/清理;唯一模板改动是给 `_includes/widgets/experience_card.html` 增加 Teaching/Service 两个区块。

**Tech Stack:** Jekyll 3.9(系统 Ruby 2.6 + Bundler 1.17,jekyll 未预装)、YAML 数据文件、Liquid 模板。

**设计文档:** `docs/superpowers/specs/2026-08-06-homepage-content-migration-design.md`

## Global Constraints

- 旧站源(只读,不得修改):`/Users/zzl/Desktop/Archive/Gold-Sea.github.io`
- 工作仓库:`/Users/zzl/Desktop/PKUFlyingPig.github.io`(git 基线 commit `70c7e5a`,仓库级身份已配置为 `Zili Zhang <zhangzili1201@gmail.com>`)
- 作者列表以旧站 about.md 为准;标题采用 camera-ready 官方版本(FastServe/RLHFuse 已是,不改)
- 所有外部链接均已在计划中给出且经 curl 逐一验证(2026-08-06);**不得自行拼造 URL**
- 除 `_includes/widgets/experience_card.html` 外不改任何模板/布局/样式/JS 文件
- 每个任务一个 commit,commit message 末尾加 `Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>`;**不执行 git push**
- 保留论文作者列表中的 "Yinmin Zhong"(合著者署名);需清除的是他的个人资料型信息(见 Task 7 残留检查清单)
- `.gitignore` 已含 `.DS_Store`,磁盘上的 .DS_Store 未被跟踪,无需处理

---

### Task 1: 静态资产与 PDF 文件迁移

**Files:**
- Create: `files/`(整目录,11 个 PDF,来自旧站)
- Create: `assets/images/badges/rednote.png`(下载转换)
- Modify(内容替换): `assets/images/photos/avatar.jpg`、`assets/images/icon/icon.png`
- Delete: `assets/cv/resume.pdf`(Yinmin 的 CV,连同空目录 `assets/cv/`)

**Interfaces:**
- Produces: `/files/zzl-cv.pdf`、`/files/disttrain-sigcomm.pdf`(Task 2 的 `cv_link` 与 Task 4 的 Slides 链接依赖);`/assets/images/badges/rednote.png`(Task 2 experience 依赖);头像与 favicon 路径不变,无下游改动

- [ ] **Step 1: 拷贝旧站 files/ 目录**

```bash
cd /Users/zzl/Desktop/PKUFlyingPig.github.io
cp -R /Users/zzl/Desktop/Archive/Gold-Sea.github.io/files ./files
rm -f files/.DS_Store
ls files/ | sort
```

Expected 输出恰好 11 个文件:`HAWC.pdf HAWC_slides.pdf NSDI23-TGS-wu.pdf disttrain-sigcomm.pdf ditto.pdf ditto_slides.pdf nsdi23-Auncel.pdf nsdi23-wu.pdf nsdi23-zhang-zili.pdf point.pdf zzl-cv.pdf`

- [ ] **Step 2: 替换头像与 favicon**

```bash
cp /Users/zzl/Desktop/Archive/Gold-Sea.github.io/images/zzl3.jpeg assets/images/photos/avatar.jpg
cp /Users/zzl/Desktop/Archive/Gold-Sea.github.io/images/favicon-32x32.png assets/images/icon/icon.png
file assets/images/photos/avatar.jpg assets/images/icon/icon.png
```

Expected: avatar.jpg 为 `JPEG image data`,icon.png 为 `PNG image data`。

- [ ] **Step 3: 下载 RedNote 图标并转 PNG**

```bash
curl -sL --max-time 20 -o /tmp/rednote_favicon.ico "https://www.xiaohongshu.com/favicon.ico"
sips -s format png /tmp/rednote_favicon.ico --out assets/images/badges/rednote.png
file assets/images/badges/rednote.png
```

Expected: `PNG image data, 32 x 32`。(此流程 2026-08-06 已实测可行。若 curl 失败:改用 `cp /tmp/rednote_test.png assets/images/badges/rednote.png` —— 该文件是规划阶段已成功转换的产物;若两者皆不可用,临时 `cp assets/images/badges/PKU_red.png assets/images/badges/rednote.png` 并在最终交付说明中标注待替换。)

- [ ] **Step 4: 删除 Yinmin 的 CV**

```bash
git rm -q assets/cv/resume.pdf
rmdir assets/cv 2>/dev/null || true
```

- [ ] **Step 5: Commit**

```bash
git add -A
git commit -m "Migrate static assets: files/, avatar, favicon, RedNote badge; drop old CV

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 2: 个人资料数据(profile.yml + authors.yml)

**Files:**
- Modify(全量重写): `_data/profile.yml`
- Modify(全量重写): `_data/authors.yml`

**Interfaces:**
- Consumes: Task 1 产出的 `/files/zzl-cv.pdf`、`/assets/images/badges/rednote.png`
- Produces: `site.data.profile.teaching` 与 `site.data.profile.service`(list of `{name, date}`)——Task 3 的模板区块按这两个键名渲染;`site.data.authors["Zili Zhang"].bold`——论文作者加粗

- [ ] **Step 1: 重写 `_data/profile.yml` 为以下完整内容**

```yaml
primary_name: "Zili Zhang"
secondary_name: ""
navbar_name: "Zili Zhang"

positions:
  - logo: /assets/images/badges/PKU_red.png
    name: PhD student @ Peking University

email: "zzlcs@pku.edu.cn"
cv_link: /files/zzl-cv.pdf
gscholar: 310QUvQAAAAJ # Google Scholar ID
github: Gold-Sea
orcid: 0000-0003-4209-9451

short_bio_text_justify: false
short_bio: >-
  <p>
    I am a fourth-year Ph.D. candidate majoring in Computer Science at the
    <a href="https://github.com/pkusys" target="_blank" rel="noopener noreferrer">Computer Systems Research Group</a>,
    Peking University (2023 - present), advised by
    <a href="https://xinjin.github.io/" target="_blank" rel="noopener noreferrer">Prof. Xin Jin</a>.
    My research and engineering work focus on systems for Multimodal LLM (MLLM) pretraining and reinforcement learning.
  </p>
  <p>
    I received my B.E. from the School of Electronics Engineering and Computer Science (EECS), Peking University (2019 - 2023).
    I used to be a research assistant at the Software Engineering Institute advised by Prof. Xin Jin (2020 - 2023).
  </p>
  <p>
    Outside of research, I am an amateur runner — here are my
    <a href="https://www.itra.run/RunnerSpace/ZHANG.Zili/4938114" target="_blank" rel="noopener noreferrer">ITRA profile</a> and
    <a href="/running_page/">Running Page</a>. I love outdoor traveling, hiking, skiing, and camping — I believe that only by
    immersing myself in nature can I find inner peace. I also enjoy video games for their immersive storytelling.
  </p>

portrait_url: /assets/images/photos/avatar.jpg
portrait_caption: >-
  Get Lost In Nature

education:
  - name: Peking University
    logo: /assets/images/badges/PKU_red.png
    position: >-
      School of Computer Science <br/>
      Ph.D. Student
    date: Sep. 2023 - present
  - name: Peking University
    logo: /assets/images/badges/PKU_red.png
    position: B.E. in Computer Science (School of EECS)
    date: Sep. 2019 - Jul. 2023

experience:
  - name: RedNote
    logo: /assets/images/badges/rednote.png
    position: RedStar Intern, Infrastructure for Multimodal LLM + RL
    date: Sep. 2025 - Jun. 2026
  - name: ByteDance Seed
    logo: /assets/images/badges/bytedance.png
    position: TopSeed Intern, Multimodal LLM + RL
    date: Apr. 2025 - Sep. 2025
  - name: StepFun
    logo: /assets/images/badges/stepfun.png
    position: Research Intern, Infrastructure for Multimodal LLM Training
    date: Apr. 2024 - Apr. 2025

awards:
  - name: NSFC Basic Research Program for Young Students (Principal Investigator)
    date: 2025
  - name: Top 10 Academic Stars, School of Computer Science, PKU (10/800)
    date: 2025
  - name: Merit Student (1/56)
    date: 2024
  - name: National Scholarship (Top 0.4% nationally)
    date: 2024
  - name: Presidential Scholarship of Peking University
    date: 2024
  - name: Top 10 Bachelor Thesis, School of EECS, PKU (10/408)
    date: 2023
  - name: Outstanding Graduation Thesis, Peking University (33/4239)
    date: 2023
  - name: Outstanding Graduation Thesis, Beijing City
    date: 2023
  - name: Excellent Graduate, Peking University (613/4239)
    date: 2023
  - name: Representor of Excellent Graduates, Peking University (14/4239)
    date: 2023
  - name: Exceptional Award for Academic Innovation, PKU (5/408)
    date: 2022
  - name: Award for Scientific Research, PKU
    date: 2022
  - name: Lee Wai Wing Scholarship, PKU
    date: 2022
  - name: Award for Scientific Research, PKU
    date: 2021
  - name: Award for Academic Excellents, PKU
    date: 2020
  - name: The Third Prize of Peking University Scholarship
    date: 2020

teaching:
  - name: Teaching Assistant, Operating System (Honor Track), PKU
    date: 2024 Spring
  - name: Teaching Assistant, Introduction to Computer System (Honor Track), PKU
    date: 2022 Fall
  - name: Teaching Assistant, Introduction to Computer System, PKU
    date: 2021 Fall

service:
  - name: IEEE TMC, Invited Reviewer
    date: 2026
  - name: EuroSys, Shadow PC
    date: 2025
```

- [ ] **Step 2: 重写 `_data/authors.yml` 为以下完整内容**

```yaml
"Zili Zhang":
  bold: true

# 可选:给常见合作者加主页链接,例如
# "Xin Jin":
#   url: https://xinjin.github.io/
```

- [ ] **Step 3: 验证 YAML 可解析、引用的资产存在**

```bash
ruby -ryaml -e 'YAML.load_file("_data/profile.yml"); YAML.load_file("_data/authors.yml"); puts "YAML OK"'
grep -oE '/(assets|files)/[A-Za-z0-9_./-]+' _data/profile.yml | sort -u | while read p; do [ -e ".$p" ] || echo "MISSING: $p"; done
```

Expected: `YAML OK`,且无 `MISSING:` 输出。

- [ ] **Step 4: Commit**

```bash
git add _data/profile.yml _data/authors.yml
git commit -m "Replace profile and authors data with Zili Zhang's information

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 3: 主页卡片新增 Teaching / Service 区块

**Files:**
- Modify: `_includes/widgets/experience_card.html`(在 awards 区块之后、masonry row 结束之前插入)

**Interfaces:**
- Consumes: `site.data.profile.teaching`、`site.data.profile.service`(Task 2 定义,list of `{name, date}`)
- Produces: 主页新增 "Teaching" 与 "Academic Service" 两个卡片小节;数据缺失时不渲染(向模板上游兼容)

- [ ] **Step 1: 插入两个区块**

在 `_includes/widgets/experience_card.html` 中,现有 awards 区块的结束标记(第 68 行 `{% endif %}`,其后是空行和 `</div>`)之后插入。用 Edit 工具,old_string:

```html
                    {% if site.data.profile.awards %}
```

先定位确认唯一性(该字符串在文件中仅出现一次)。实际编辑以 awards 整块结束处为锚点,old_string 取:

```html
                    {% endif %}

                </div>
```

new_string:

```html
                    {% endif %}

                    {% if site.data.profile.teaching %}
                    <div class="col-md-6">
                        <div class="mx-2 my-1">
                            <h6>Teaching</h6>
                            <ul class="list small pl-3 mb-1">
                                {% for item in site.data.profile.teaching %}
                                <li>
                                    <div class="d-flex">
                                        <div>{{ item.name }}</div>
                                        <div class="ml-auto mt-auto no-break"><em>{{ item.date }}</em></div>
                                    </div>
                                </li>
                                {% endfor %}
                            </ul>
                        </div>
                    </div>
                    {% endif %}

                    {% if site.data.profile.service %}
                    <div class="col-md-6">
                        <div class="mx-2 my-1">
                            <h6>Academic Service</h6>
                            <ul class="list small pl-3 mb-1">
                                {% for item in site.data.profile.service %}
                                <li>
                                    <div class="d-flex">
                                        <div>{{ item.name }}</div>
                                        <div class="ml-auto mt-auto no-break"><em>{{ item.date }}</em></div>
                                    </div>
                                </li>
                                {% endfor %}
                            </ul>
                        </div>
                    </div>
                    {% endif %}

                </div>
```

注意:`{% endif %}` + 空行 + `</div>` 的组合在文件中仅在 awards 区块结束处出现一次(前两个区块的 endif 后紧跟其他 `{% if %}`),Edit 可唯一匹配。

- [ ] **Step 2: 验证 Liquid 标签配平**

```bash
python3 -c "
t = open('_includes/widgets/experience_card.html').read()
assert t.count('{% if') == t.count('{% endif %}') == 5, (t.count('{% if'), t.count('{% endif %}'))
assert t.count('{% for') == t.count('{% endfor %}') == 5
print('Liquid tags balanced')
"
```

Expected: `Liquid tags balanced`(原文件 3 组 if + 3 组 for,新增 2 组后各为 5)。

- [ ] **Step 3: Commit**

```bash
git add _includes/widgets/experience_card.html
git commit -m "Add Teaching and Academic Service sections to homepage card

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 4: 论文集合重建(删 11、改 3、翻 4、增 5)

**Files:**
- Delete: `_publications/2023/elasticflow.md`、`_publications/2023/alpaserve.md`、`_publications/2024/distserve.md`、`_publications/2024/loongserve.md`、`_publications/2024/megascale.md`、`_publications/2024/distmind.md`、`_publications/2025/deepseekv32.md`、`_publications/2025/sd.md`、`_publications/2026/heddle.md`、`_publications/2026/mars.md`、`_publications/2026/deepseekv4.md`
- Modify: `_publications/2025/disttrain.md`(全量重写)、`_publications/2026/fastserve.md`(作者+selected)、`_publications/2026/ultraep.md`、`_publications/2026/relibra.md`、`_publications/2025/tokenlake.md`、`_publications/2025/streamrl.md`(仅 selected)
- 不动: `_publications/2025/rlhfuse.md`(标题/作者/链接/selected 均已正确)
- Create: `_publications/2026/bigmac.md`、`_publications/2025/ragcache.md`、`_publications/2024/dlora.md`、`_publications/2024/jolteon.md`、`_publications/2024/rummy.md`
- Delete(孤儿封面): `assets/images/covers/` 下删除后无引用的 png

**Interfaces:**
- Consumes: `site.data.authors["Zili Zhang"].bold`(Task 2);`/files/disttrain-sigcomm.pdf`(Task 1);author_list 组件对 `名字*` 后缀自动剥离匹配并生成 "(* equal contribution)" 图例
- Produces: 12 个论文文件,全部 `selected: true`(主页与论文页共同的数据源)

- [ ] **Step 1: 删除 11 篇非用户论文**

```bash
git rm -q _publications/2023/elasticflow.md _publications/2023/alpaserve.md \
  _publications/2024/distserve.md _publications/2024/loongserve.md \
  _publications/2024/megascale.md _publications/2024/distmind.md \
  _publications/2025/deepseekv32.md _publications/2025/sd.md \
  _publications/2026/heddle.md _publications/2026/mars.md _publications/2026/deepseekv4.md
rm -rf _publications/2023
find _publications -name "*.md" | wc -l
```

Expected: `7`(此时剩 7 篇)。

- [ ] **Step 2: 全量重写 `_publications/2025/disttrain.md`**(作者按旧站 camera-ready 版本修正:Ranchen Ming → Yimin Jiang,补 Daxin Jiang;Paper 链接改 ACM DL;Slides 改相对路径)

```yaml
---
title:          "DistTrain: Addressing Model and Data Heterogeneity with Disaggregated Training for Multimodal Large Language Models"
date:           2025-09-08 00:01:00 +0800
selected:       true
pub:            "ACM Special Interest Group on Data Communication (SIGCOMM)"
pub_date:       "2025"

abstract: >-
    This work presents DistTrain, an efficient and adaptive framework to reform the training of multimodal large language models on large-scale clusters. The core of DistTrain is the disaggregated training technique that exploits the characteristics of multimodal LLM training to achieve high efficiency and scalability. Specifically, it leverages disaggregated model orchestration and disaggregated data reordering to address model and data heterogeneity respectively.

cover:    /assets/images/covers/disttrain.png
authors:
  - Zili Zhang
  - Yinmin Zhong
  - Yimin Jiang
  - Hanpeng Hu
  - Jianjian Sun
  - Zheng Ge
  - Yibo Zhu
  - Daxin Jiang
  - Xin Jin

links:
  Paper: https://dl.acm.org/doi/10.1145/3718958.3750472
  Slides: /files/disttrain-sigcomm.pdf
---
```

- [ ] **Step 3: 修改 `_publications/2026/fastserve.md`**——仅两处:`selected:       false` → `selected:       true`;authors 列表替换为 NSDI'26 camera-ready 的 9 人(USENIX 页面 citation_author meta 已核实,与旧站一致):

```yaml
authors:
  - Bingyang Wu*
  - Yinmin Zhong*
  - Zili Zhang*
  - Shengyu Liu
  - Fangyue Liu
  - Yuanhang Sun
  - Gang Huang
  - Xuanzhe Liu
  - Xin Jin
```

其余字段(title/date/pub/abstract/cover/links)保持不变。

- [ ] **Step 4: 把 4 个文件的 selected 翻为 true**

`_publications/2026/ultraep.md`、`_publications/2026/relibra.md`、`_publications/2025/tokenlake.md`、`_publications/2025/streamrl.md` 各改一行:`selected:       false` → `selected:       true`。可用 Edit 逐个改,或:

```bash
for f in _publications/2026/ultraep.md _publications/2026/relibra.md _publications/2025/tokenlake.md _publications/2025/streamrl.md; do
  sed -i '' 's/^selected:       false$/selected:       true/' "$f"
done
grep -c "selected:       true" _publications/2026/ultraep.md _publications/2026/relibra.md _publications/2025/tokenlake.md _publications/2025/streamrl.md
```

Expected: 每个文件计数 1。

- [ ] **Step 5: 新建 `_publications/2026/bigmac.md`**(摘要取自 arXiv 2605.25451,已核实)

```yaml
---
title:          "BigMac: Breaking the Pareto Frontier of Compute and Memory in Multimodal LLM Training"
date:           2026-05-26 00:01:00 +0800
selected:       true
pub:            "In preprint"
pub_date:       "2026"

abstract: >-
    This work presents BigMac, a new training pipeline for multimodal LLMs that breaks the Pareto frontier between compute and memory efficiency. The core idea of BigMac is to elegantly nest the encoder and generator computation into the original LLM pipeline, forming a dependency-safe nested pipeline structure that reduces activation memory complexity while preserving compute efficiency.

authors:
  - Zili Zhang
  - Chengxu Yang
  - Shenglong Zhang
  - Chenyu Wang
  - Yufan Zhang
  - Tuo Dai
  - Zhouyang Li
  - Yuhong Ge
  - Chao Jin
  - Xin Jin
  - Yuliang Liu

links:
  Paper: https://arxiv.org/abs/2605.25451
---
```

- [ ] **Step 6: 新建 `_publications/2025/ragcache.md`**(摘要取自 arXiv 2404.12457,已核实)

```yaml
---
title:          "RAGCache: Efficient Knowledge Caching for Retrieval-Augmented Generation"
date:           2025-06-01 00:01:00 +0800
selected:       true
pub:            "ACM Transactions on Computer Systems (TOCS)"
pub_date:       "2025"

abstract: >-
    This work proposes RAGCache, a novel multilevel dynamic caching system tailored for Retrieval-Augmented Generation (RAG). RAGCache caches the intermediate states of injected external knowledge and shares them across multiple queries, mitigating the high computation and memory costs caused by long-sequence generation in RAG.

authors:
  - Chao Jin
  - Zili Zhang
  - Xuanlin Jiang
  - Fangyue Liu
  - Xin Liu
  - Xuanzhe Liu
  - Xin Jin

links:
  Paper: https://arxiv.org/abs/2404.12457
---
```

- [ ] **Step 7: 新建 `_publications/2024/dlora.md`**(摘要取自 OSDI'24 论文 PDF,链接经 curl 核实)

```yaml
---
title:          "dLoRA: Dynamically Orchestrating Requests and Adapters for LoRA LLM Serving"
date:           2024-07-10 00:01:00 +0800
selected:       true
pub:            "Operating Systems Design and Implementation (OSDI)"
pub_date:       "2024"

abstract: >-
    This paper introduces dLoRA, an inference serving system for LoRA models. dLoRA achieves high serving efficiency by dynamically orchestrating requests and LoRA adapters in two aspects: dynamically merging and unmerging adapters with the base model, and dynamically migrating requests and adapters between different worker replicas.

authors:
  - Bingyang Wu
  - Ruidong Zhu
  - Zili Zhang
  - Peng Sun
  - Xuanzhe Liu
  - Xin Jin

links:
  Paper: https://www.usenix.org/system/files/osdi24-wu-bingyang.pdf
  Slides: https://www.usenix.org/system/files/osdi24_slides-wu-bingyang.pdf
---
```

- [ ] **Step 8: 新建 `_publications/2024/jolteon.md`**(摘要取自 NSDI'24 论文 PDF,链接经 curl 核实)

```yaml
---
title:          "Jolteon: Unleashing the Promise of Serverless for Serverless Workflows"
date:           2024-04-17 00:01:00 +0800
selected:       true
pub:            "Networking Systems Design and Implementation (NSDI)"
pub_date:       "2024"

abstract: >-
    This paper proposes Jolteon, an orchestrator to unleash the promise of automatic resource provisioning for serverless workflows. At the core of Jolteon is a stochastic performance model that combines the benefits of whitebox modeling to capture the execution characteristics of serverless computing and blackbox modeling to accommodate the inherent performance variability.

authors:
  - Zili Zhang
  - Chao Jin
  - Xin Jin

links:
  Paper: https://www.usenix.org/system/files/nsdi24-zhang-zili-jolteon.pdf
  Slides: https://www.usenix.org/system/files/nsdi24_slides-zhang_zili_jolteon.pdf
---
```

- [ ] **Step 9: 新建 `_publications/2024/rummy.md`**(摘要取自 NSDI'24 论文 PDF,链接经 curl 核实)

```yaml
---
title:          "Fast Vector Query Processing for Large Datasets Beyond GPU Memory with Reordered Pipelining"
date:           2024-04-16 00:01:00 +0800
selected:       true
pub:            "Networking Systems Design and Implementation (NSDI)"
pub_date:       "2024"

abstract: >-
    This work presents RUMMY, the first GPU-accelerated vector query processing system that achieves high performance and supports large vector datasets beyond GPU memory. The core of RUMMY is a novel reordered pipelining technique that exploits the characteristics of vector query processing to efficiently pipeline data transmission from host memory to GPU memory with query processing in GPU.

authors:
  - Zili Zhang
  - Fangyue Liu
  - Gang Huang
  - Xuanzhe Liu
  - Xin Jin

links:
  Paper: https://www.usenix.org/system/files/nsdi24-zhang-zili-pipelining.pdf
  Slides: https://www.usenix.org/system/files/nsdi24_slides-zhang_zili_pipelining.pdf
---
```

- [ ] **Step 10: 删除孤儿封面图**

```bash
referenced=$(grep -rhoE '/assets/images/covers/[A-Za-z0-9_.-]+' _publications | sort -u)
for f in assets/images/covers/*; do
  echo "$referenced" | grep -q "/$(basename "$f")$" || git rm -q "$f"
done
ls assets/images/covers/
```

Expected 剩余 7 个:`disttrain.png fastserve.png relibra.png rlhfuse.png streamrl.png tokenlake.png ultraep.png`(即删除 SpecRL/alpaserve/deepseekv32/deepseekv4/distserve/heddle/loongserve/mars 共 8 个)。

- [ ] **Step 11: 验证论文数据完整性**

```bash
find _publications -name "*.md" | wc -l
ruby -ryaml -e '
ok = true
Dir.glob("_publications/**/*.md").each do |f|
  c = File.read(f)
  m = c.match(/\A---\s*\n(.*?)\n---/m) or (puts "FAIL no frontmatter #{f}"; ok = false; next)
  d = YAML.load(m[1])
  %w[title date selected pub pub_date authors].each { |k| d.key?(k) or (puts "FAIL #{f} missing #{k}"; ok = false) }
  d["selected"] == true or (puts "FAIL #{f} not selected"; ok = false)
  d["authors"].any? { |a| a.sub(/[*#]$/, "") == "Zili Zhang" } or (puts "FAIL #{f} no Zili Zhang"; ok = false)
end
puts ok ? "PUBS OK" : "PUBS ERRORS"'
```

Expected: `12` 和 `PUBS OK`。

- [ ] **Step 12: Commit**

```bash
git add -A _publications assets/images/covers
git commit -m "Rebuild publications: Zili Zhang's 12 papers, drop upstream-only entries

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 5: 清理演示内容与上游遗留

**Files:**
- Delete: `_showcase/`(整目录)、`showcase.html`、`_news/` 下全部 6 个示例(`2022-lorem.md 2023-news1.md 2023-news2.md 2024-news1.md 2024-news2.md 2024-news3.md`)、`.github/`(整目录)、`assets/images/etc/cat1.jpg`、`assets/images/etc/cat2.jpg`、`assets/images/etc/tim_the_beaver.png`、`assets/images/badges/deepseek.png`、`assets/images/badges/berkeley.webp`、`assets/images/badges/alibaba.png`、`assets/images/badges/MIT_Social_circle.png`
- 保留: `assets/images/etc/preview.png`(现 README 仍引用,Task 6 重写 README 时一并删除)、`assets/images/empty_300x200.png`(publication_item 懒加载占位图)、`assets/css/images/loading.gif`(CSS 引用)

**Interfaces:**
- Consumes: `_data/display.yml` 中 `show_news: false`(保持不变,首页不渲染 news);`_data/navigation.yml` 中 Showcase 项本已注释
- Produces: 无新接口;`site.news` 变为空集合(index.html 的 news 引用有 `count_news > 0` 双重防护)

- [ ] **Step 1: 删除**

```bash
git rm -rq _showcase _news .github
git rm -q showcase.html assets/images/etc/cat1.jpg assets/images/etc/cat2.jpg assets/images/etc/tim_the_beaver.png \
  assets/images/badges/deepseek.png assets/images/badges/berkeley.webp assets/images/badges/alibaba.png assets/images/badges/MIT_Social_circle.png
rm -rf _showcase _news .github
```

- [ ] **Step 2: 验证无悬空引用**

```bash
grep -rn "showcase\|cat1\|cat2\|tim_the_beaver\|deepseek\.png\|berkeley\.webp\|alibaba\.png\|MIT_Social" \
  --include="*.html" --include="*.yml" --include="*.md" \
  --exclude-dir={.git,docs,vendor,_site} . ; echo "exit=$?"
```

Expected: 恰好 3 行命中——`_config.yml` 的 `- showcase` 集合声明(Task 6 处理)、`_data/navigation.yml` 第 3 行与第 9 行的注释(2026-08-06 预演确认);不得有任何 assets 图片引用命中(profile.yml 的旧 badge 引用已在 Task 2 消除)。

- [ ] **Step 3: Commit**

```bash
git add -A
git commit -m "Remove template demo content and upstream leftovers

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 6: 站点配置(CNAME / _config.yml / README)

**Files:**
- Modify: `CNAME`(单行替换)
- Modify: `_config.yml`(去掉 showcase 集合、新增 exclude)
- Modify(全量重写): `README.md`
- Delete: `assets/images/etc/preview.png`(README 重写后不再被引用)

**Interfaces:**
- Consumes: 无
- Produces: 站点域名 `zilizhang.site`;`docs/`(spec/plan)不再发布到线上

- [ ] **Step 1: 替换 CNAME 内容**(整文件单行)

```
zilizhang.site
```

- [ ] **Step 2: 修改 `_config.yml`**——collections 去掉 showcase,并在文件末尾追加 exclude。改后整个文件为:

```yaml
# Welcome to Jekyll!
#
# This config file is meant for settings that affect your whole blog, values
# which you are expected to set up once and rarely edit after that. If you find
# yourself editing this file very often, consider using Jekyll's data files
# feature for the data you need to update frequently.
#
# For technical reasons, this file is *NOT* reloaded automatically when you use
# 'bundle exec jekyll serve'. If you change this file, please restart the server process.

# Site settings
# These are used to personalize your new site. If you look in the HTML files,
# you will see them accessed via {{ site.title }}, {{ site.email }}, and so on.
# You can create any custom variable you would like, and they will be accessible
# in the templates via {{ site.myvariable }}.
baseurl: "" # the subpath of your site, e.g. /blog

# Build settings
markdown: kramdown
plugins:
  - jekyll-email-protect

# timezone: Asia/Shanghai

collections:
  - publications
  - news

exclude:
  - docs
  - README.md
  - LICENSE
```

- [ ] **Step 3: 重写 `README.md` 为以下完整内容**

````markdown
# zilizhang.site

Personal academic homepage of **Zili Zhang**, served by GitHub Pages at [zilizhang.site](https://zilizhang.site).

Built on the [academic-homepage](https://github.com/luost26/academic-homepage) Jekyll template by [@luost26](https://github.com/luost26), with the site structure adapted from [PKUFlyingPig's homepage](https://github.com/PKUFlyingPig/PKUFlyingPig.github.io).

## Local development

```bash
bundle install
bundle exec jekyll serve
```

Then open <http://127.0.0.1:4000>.

## Content layout

- `_data/profile.yml` — name, bio, education, experience, awards, teaching, service
- `_publications/<year>/<paper>.md` — one file per publication (`selected: true` shows it on the homepage)
- `_data/authors.yml` — author display rules (bold / links)
- `files/` — PDFs (CV, slides) kept at stable URLs

## License

Code is released under the [MIT License](LICENSE) (template by Shitong Luo). Site content — text, publication data, images, and PDFs — © Zili Zhang.
````

- [ ] **Step 4: 删除 preview.png 并验证**

```bash
git rm -q assets/images/etc/preview.png
cat CNAME
ruby -ryaml -e 'c = YAML.load_file("_config.yml"); abort "bad collections" unless c["collections"] == ["publications", "news"]; abort "bad exclude" unless (c["exclude"] & ["docs", "README.md", "LICENSE"]).size == 3; puts "CONFIG OK"'
grep -rn "preview.png" --exclude-dir={.git,docs,vendor,_site} . ; echo "grep exit=$?"
```

Expected: `zilizhang.site`、`CONFIG OK`、preview.png 无命中(grep exit=1)。

- [ ] **Step 5: Commit**

```bash
git add -A
git commit -m "Point site at zilizhang.site; exclude docs from build; rewrite README

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

### Task 7: 构建验证、残留检查与远端配置

**Files:**
- Create: `vendor/bundle/`(gitignored,本地 gem 安装目录)
- 无源码改动(除非验证发现问题需修复)

**Interfaces:**
- Consumes: 前 6 个任务的全部产出
- Produces: 通过验证的可部署仓库 + `origin` 远端配置(不推送)

- [ ] **Step 1: 尝试本地构建**

```bash
bundle install --path vendor/bundle
bundle exec jekyll build
```

Expected: build 无报错,生成 `_site/`。**Fallback:** 若 `bundle install` 因系统 Ruby 2.6 编译原生扩展失败或网络不可达而失败,跳过 Step 2 的 `_site` 检查,改为依赖 Task 2-6 已做的静态检查,并在交付说明中注明"由 GitHub Pages 构建验证"。此为可接受降级,不视为任务失败。

- [ ] **Step 2: 构建产物抽查**(仅当 Step 1 成功)

```bash
grep -c "Zili Zhang" _site/index.html
grep -o '<h5[^>]*>' _site/publications.html | wc -l
grep -c 'equal contribution' _site/index.html
```

Expected: 第一条 ≥ 1;第二条 `24`(12 篇 × 桌面/移动两套布局);第三条 ≥ 1(FastServe 的 * 图例)。

- [ ] **Step 3: Yinmin 个人信息残留检查**(合著者署名 "Yinmin Zhong" 属正常,不在检查项内)

```bash
grep -rniE "zhongyinmin|yinminzhong\.com|pkuflyingpig|csdiy|UUM_zLwAAAAJ|0000-0002-2504-7652|zaizuo" \
  --exclude-dir={.git,docs,vendor,_site} --exclude=README.md . ; echo "exit=$?"
```

Expected: 无命中(exit=1)。(README.md 排除是因为其中保留了指向 PKUFlyingPig 仓库的模板致谢链接,属设计决定。)

- [ ] **Step 4: 全站资源引用完整性**

```bash
grep -rhoE '/(assets|files)/[A-Za-z0-9_./-]+' _data _publications _includes _layouts index.html publications.html 404.html \
  | sort -u | while read p; do [ -e ".$p" ] || echo "MISSING: $p"; done; echo done
```

Expected: 仅输出 `done`,无 `MISSING:` 行。

- [ ] **Step 5: 配置远端(不推送)**

```bash
git remote add origin https://github.com/Gold-Sea/Gold-Sea.github.io.git
git remote -v
```

Expected: origin 两行(fetch/push)。

- [ ] **Step 6: 如有修复则提交**

```bash
git status --short
# 若有修复改动:
git add -A && git commit -m "Fix issues found during build verification

Co-Authored-By: Claude Fable 5 <noreply@anthropic.com>"
```

---

## 完成后交付说明(写给用户的最终报告要点)

1. 本地预览方式(`bundle exec jekyll serve`,或直接推送由 GitHub Pages 构建)。
2. 推送命令(由用户自行执行,会覆盖远端旧站):`git push -u origin main --force-with-lease`(远端旧仓库历史与本仓库无关,需强推;旧站内容在 `/Users/zzl/Desktop/Archive/Gold-Sea.github.io` 有完整备份)。同时提醒:GitHub 仓库 Settings → Pages 确认 Source 为 main 分支、自定义域名 zilizhang.site 与 DNS 不变则无需改动。
3. 待用户后续自行完善的项:RedNote logo 若用了占位图需替换;新增 5 篇论文可补封面图;如需精选主页论文,把不想展示的文件 `selected` 改回 `false`;如需显示中文名,在 `_data/profile.yml` 的 `secondary_name` 填入「章梓立」。
