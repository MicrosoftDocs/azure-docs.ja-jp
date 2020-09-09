---
title: Azure Migrate を使用して Azure への移行について多数の物理サーバーを評価する | Microsoft Docs
description: Azure Migrate サービスを使用して Azure への移行について多数の物理サーバーを評価する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: b84a7b88f700e2ba8e9fbe29099dfcbce8dbf3b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85081074"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Azure への移行について多数の物理サーバーを評価する

この記事では、Azure Migrate Server Assessment ツールを使用して、Azure への移行について多数のオンプレミス物理サーバーを評価する方法について説明します。

[Azure Migrate](migrate-services-overview.md) では、アプリ、インフラストラクチャ、およびワークロードを検出、評価、および Microsoft Azure に移行するために役立つツールのハブが提供されます。 このハブには、Azure Migrate ツールと、サードパーティ製の独立系ソフトウェア ベンダー (ISV) オファリングが含まれています。 


この記事では、次の方法について説明します。
> [!div class="checklist"]
> * 大規模な評価を計画します。
> * Azure のアクセス許可を構成し、評価用に物理サーバーを準備します。
> * Azure Migrate プロジェクトを作成し、評価を作成します。
> * 移行を計画するときに評価をレビューします。


> [!NOTE]
> 大規模な評価を行う前に、いくつかのサーバーを評価して概念実証を試す場合は、[チュートリアル シリーズ](tutorial-prepare-physical.md)に従ってください。

## <a name="plan-for-assessment"></a>評価の計画

多数の物理サーバーの評価を計画するときは、以下のようないくつかの考慮点があります。

- **Azure Migrate プロジェクトの計画**: Azure Migrate プロジェクトをデプロイする方法を調べます。 たとえば、データセンターが地理的に異なる場所にある場合や、検出、評価、または移行に関連するメタデータを地理的に異なる場所に格納する必要がある場合は、複数のプロジェクトが必要になることがあります。
- **アプライアンスの計画**: Azure Migrate では、Windows コンピューターに展開されたオンプレミスの Azure Migrate アプライアンスを使用して、評価と移行のためにサーバーが継続的に検出されます。 アプライアンスでは、VM、ディスク、ネットワーク アダプターなどの環境の変化が監視されます。 また、これらに関するメタデータとパフォーマンス データが Azure に送信されます。 デプロイするアプライアンスの数を確認する必要があります。


## <a name="planning-limits"></a>計画の制限
 
計画には、この表にまとめた制限を使用します。

**計画** | **制限**
--- | --- 
**Azure Migrate プロジェクト** | 1 つのプロジェクトで最大 35,000 台のサーバーが評価されます。
**Azure Migrate アプライアンス** | 1 つのアプライアンスで最大 1,000 台のサーバーを検出できます。<br/> 1 つのアプライアンスは、1 つの Azure Migrate リソースにのみ関連付けることができます。<br/> 任意の数のアプライアンスを 1 つの Azure Migrate プロジェクトに関連付けることができます。 <br/><br/> 
**グループ** | 1 つのグループに最大 35,000 台のサーバーを追加できます。
**Azure Migrate の評価** | 1 回の評価で最大 35,000 台のサーバーを評価できます。


## <a name="other-planning-considerations"></a>計画に関するその他の考慮事項

- アプライアンスから検出を開始するには、各物理サーバーを選択する必要があります。 

## <a name="prepare-for-assessment"></a>評価の準備

サーバー評価のために、Azure と物理サーバーを準備します。 

1. [物理サーバーのサポート要件と制限事項](migrate-support-matrix-physical.md)を確認します。
2. Azure Migrate とやり取りするために、自分の Azure アカウントのアクセス許可を設定します。
3. 物理サーバーを準備します。

[このチュートリアル](tutorial-prepare-physical.md)の手順に従って、これらの設定を構成します。

## <a name="create-a-project"></a>プロジェクトの作成

計画の要件に従って、以下を実行します。

1. Azure Migrate プロジェクトを作成します。
2. Azure Migrate Server Assessment ツールをプロジェクトに追加します。

[詳細情報](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>評価の作成とレビュー

1. 物理サーバーの評価を作成します。
1. 移行計画に備えて評価をレビューします。

評価の作成とレビューの[詳細を確認](tutorial-assess-physical.md)します。
    

## <a name="next-steps"></a>次のステップ

この記事では、次の内容について説明します。
 
> [!div class="checklist"] 
> * 物理サーバー用に Azure Migrate 評価のスケーリングを準備しました。
> * Azure と物理サーバーを評価用に準備しました。
> * Azure Migrate プロジェクトを作成し、評価を実行しました。
> * 移行に備えて評価をレビューしました。

次に、[評価の計算](concepts-assessment-calculation.md)方法と、[評価の変更](how-to-modify-assessment.md)方法について確認します。
