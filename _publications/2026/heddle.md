---
title:          "Heddle: A Distributed Orchestration System for Agentic RL Rollout"
date:           2026-03-1 00:01:00 +0800
selected:       false
pub:            "In preprint"
# pub_pre:        "Submitted to "
# pub_post:       'Under review.'
# pub_last:       ' <span class="badge badge-pill badge-publication badge-success">Spotlight</span>'
pub_date:       "2026"

abstract: >-
    This work proposes Heddle, a trajectory-centric system to optimize the when, where, and how of agentic rollout execution. Heddle integrates three core mechanisms: trajectory-level scheduling using runtime prediction and progressive priority to minimize cumulative queueing; trajectory-aware placement via presorted dynamic programming and opportunistic migration during idle tool call intervals to minimize interference; and trajectory-adaptive resource manager that dynamically tunes model parallelism to accelerate the per-token time of long-tail trajectories while maintaining high throughput for short trajectories. 

cover:    /assets/images/covers/heddle.png
authors:
    - Zili Zhang
    - Yinmin Zhong
    - Chengxu Yang
    - Chao Jin
    - Bingyang Wu
    - Xinming Wei
    - Yuliang Liu
    - Xin Jin

links:
  Paper: https://arxiv.org/abs/2603.28101
---