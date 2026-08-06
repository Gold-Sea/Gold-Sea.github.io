---
title:          "ReLibra: Routing-Replay-Guided Load Balancing for MoE Training in Reinforcement Learning"
date:           2026-06-1 00:01:00 +0800
selected:       false
pub:            "In preprint"
# pub_pre:        "Submitted to "
# pub_post:       'Under review.'
# pub_last:       ' <span class="badge badge-pill badge-publication badge-success">Spotlight</span>'
pub_date:       "2026"

abstract: >-
    This work proposes ReLibra, an MoE RL training system that exploits a unique opportunity in RL's rollout-training workflow, routing replay, to enable fine-grained load balancing at micro-batch granularity. Because rollout and training process the same tokens with the same MoE parameters, the token-to-expert routing decisions are known before training starts. Leveraging this information, ReLibra places two MoE load-balancing mechanisms at inter- and intra-batch timescales, matching their communication patterns to hierarchical network bandwidths. At the inter-batch timescale, ReLibra performs expert reordering to redistribute experts for batch-level cross-node balancing; at the intra-batch timescale, it dynamically performs expert replication within a node to absorb micro-batch-level load fluctuations.

cover:    /assets/images/covers/relibra.png
authors:
    - Chao Jin
    - Xinming Wei
    - Yinmin Zhong
    - Chengxu Yang
    - Bingyang Wu
    - Ruidong Zhu
    - Zili Zhang
    - Yuliang Liu
    - Xin Jin

links:
  Paper: https://arxiv.org/abs/2605.08639
---