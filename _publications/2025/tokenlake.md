---
title:          "TokenLake: A Unified Segment-level Prefix Cache Pool for Fine-grained Elastic Long-Context LLM Serving"
date:           2025-08-1 00:01:00 +0800
selected:       true
pub:            "In preprint"
# pub_pre:        "Submitted to "
# pub_post:       'Under review.'
# pub_last:       ' <span class="badge badge-pill badge-publication badge-success">Spotlight</span>'
pub_date:       "2025"

abstract: >-
    This work presents TokenLake, which uses a declarative cache interface to expose requests' query tensors, prefix caches, and cache-aware operations for efficient pooling. Powered by this abstraction, TokenLake can manage prefix cache at the segment level with a heavy-hitter-aware load balancing algorithm to achieve better cache load balance, deduplication, and defragmentation. TokenLake also transparently minimizes the communication volume of query tensors and new caches. Based on TokenLake, the scheduler can schedule requests elastically by using existing techniques without considering prefix cache management.

cover:    /assets/images/covers/tokenlake.png
authors:
    - Bingyang Wu
    - Zili Zhang
    - Yinmin Zhong
    - Guanzhe Huang
    - Yibo Zhu
    - Xuanzhe Liu
    - Xin Jin

links:
  Paper: https://arxiv.org/abs/2508.17219v1
---