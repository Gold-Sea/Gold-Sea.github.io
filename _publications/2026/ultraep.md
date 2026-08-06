---
title:          "UltraEP: Unleash MoE Training and Inference on Rack-Scale Nodes with Near-Optimal Load Balancing"
date:           2026-07-1 00:01:00 +0800
selected:       false
pub:            "In preprint"
# pub_pre:        "Submitted to "
# pub_post:       'Under review.'
# pub_last:       ' <span class="badge badge-pill badge-publication badge-success">Spotlight</span>'
pub_date:       "2026"

abstract: >-
    This work presents UltraEP, the first exact-load, real-time balancer for large-EP MoE training and serving prefill on rack-scale nodes (RSNs). Leveraging the extended scale-up connectivity among dozens of GPUs within RSNs, UltraEP rebalances every microbatch and layer on critical paths, which requires nontrivial co-design of plan solving and expert replication communication to minimize exposed overhead. To this end, UltraEP eagerly reacts to post-gating load with an efficient quota-driven planner, and executes the resulting irregular expert-state transfers with RSN-native persistent tile streaming and relay-based fan-out mitigation. We evaluate UltraEP in a multi-RSN deployment of up to 256 GPUs, using cutting-edge MoE models from 106B to 671B parameters.

cover:    /assets/images/covers/ultraep.png
authors:
    - Xinming Wei
    - Chao Jin
    - Tuo Dai
    - Yinmin Zhong
    - Shan Yu
    - Chengxu Yang
    - Bingyang Wu
    - Zili Zhang
    - Jing Mai
    - Qianchao Zhu
    - Zhouyang Li
    - Yuliang Liu
    - Guojie Luo

links:
  Paper: https://arxiv.org/pdf/2606.04101
---