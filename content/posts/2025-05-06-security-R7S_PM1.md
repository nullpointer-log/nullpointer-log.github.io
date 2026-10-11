---
title: "令和7年春期 安全確保支援士試験 午後問１の結果とふりかえり"
date: 2025-05-06
tags: []
draft: true
---
{{< mermaid >}}
flowchart LR

  %% 上段：Web Browser
  subgraph PC
    direction LR
    A[Web Browser]
  end

  %% 中段：System Q と サーバT
  subgraph サーバ
    direction LR
    B[システム Q<br/>H社開発のシステム（HTML配信）]
    C[のっとられたサーバT<br/>（悪意のあるスクリプトP）]
  end

  %% 下段：悪意のあるWebサイト
  subgraph 攻撃者
    direction LR
    D[悪意のある<br/>Webサイト]
  end

  %% 矢印
  A -->|アクセス| B
  B -.->|リダイレクト| A
  C -.->|悪意のあるスクリプトP| A
  C --> D
  A -.->|リダイレクト| D
{{< /mermaid >}}
