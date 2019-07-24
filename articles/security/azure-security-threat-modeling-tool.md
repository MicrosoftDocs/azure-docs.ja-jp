---
title: Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Microsoft Threat Modeling Tool のメイン ページ。ツールの使い方や、Threat Modeling プロセスについて説明しています。
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: jegeib
ms.openlocfilehash: 4e08491ae898fef16af1e199a76a4c07a6c226b9
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358662"
---
# <a name="microsoft-threat-modeling-tool"></a>Microsoft Threat Modeling Tool

Threat Modeling Tool は、Microsoft セキュリティ開発ライフサイクル (SDL) の主要な要素です。 これを使用すると、ソフトウェア アーキテクトは早い段階で潜在的なセキュリティの問題を特定し、危険を軽減することができます。早い段階であれば、問題の解決は比較的容易で、コスト効率も良くなります。 そのため、開発総コストを大幅に軽減できます。 また、このツールはセキュリティの専門家ではないユーザーを想定して設計され、脅威モデルの作成と分析に関するわかりやすいガイダンスが用意されているため、すべての開発者が簡単に脅威をモデリングできます。 

このツールを使用すると、だれでも次の作業を行うことができます。

* システムのセキュリティ設計に関して連絡する
* 実績ある手法を使用して、潜在的なセキュリティの問題に関して、これらの設計を分析する
* セキュリティの問題の軽減策を提案および管理する

ツールの代表的な機能と技術は、次のとおりです。

* **自動化:** モデルを作成するためのガイダンスとフィードバック
* **要素ごとの STRIDE:** 脅威と軽減策についてのガイド付き分析
* **レポート:** 検証フェーズでのセキュリティ アクティビティとテスト
* **独自の方法論:** 脅威を視覚化し、より的確に理解
* **開発者向けの設計とソフトウェア重視:** 多くのアプローチでは、資産または攻撃者が中心になっています。 このツールでは、ソフトウェアが中心です。 すべてのソフトウェア開発者やアーキテクトが慣れ親しんでいる、ソフトウェア アーキテクチャの図の作成などのアクティビティをベースにしています
* **設計分析を重視:**"脅威モデリング" という用語は、要件を意味することも、設計分析手法を意味することもあります。 場合によっては、両方が複雑に入り交じっています。 脅威モデリングに対する Microsoft SDL のアプローチでは、設計分析手法に重点を置いています

## <a name="next-steps"></a>次の手順

次の表は、Threat Modeling Tool を初めて使用する場合に重要なリンクの一覧です。

| 手順  | 説明                                                                                   |
| ----- | --------------------------------------------------------------------------------------------- |
| **1** | [Threat Modeling Tool をダウンロードします](https://aka.ms/threatmodelingtool)                                |
| **2** | [ファースト ステップ ガイドを読みます](./azure-security-threat-modeling-tool-getting-started.md)    |
| **3** | [機能について理解を深めます](./azure-security-threat-modeling-tool-feature-overview.md)   |
| **4** | [生成される脅威のカテゴリについて学びます](./azure-security-threat-modeling-tool-threats.md)   |
| **5** | [生成される脅威の軽減策を見つけます](./azure-security-threat-modeling-tool-mitigations.md) |

## <a name="resources"></a>リソース

今日の脅威モデリングにも関連する以前の記事:

* [脅威モデリングの重要性に関する記事](https://msdn.microsoft.com/magazine/dd347831.aspx)
* [Trustworthy Computing (信頼できるコンピューティング) 発行のトレーニング](https://www.microsoft.com/download/details.aspx?id=16420)

Threat Modeling Tool の専門家による記事:

* [Threats Manager](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Simone Curzi のセキュリティ ブログ](https://simoneonsecurity.com/)
