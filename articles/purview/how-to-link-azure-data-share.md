---
title: Azure Data Share に接続する
description: この記事では、Azure Data Share アカウントを Azure Purview に接続して、資産を検索し、データ系列を追跡する方法について説明します。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96551060"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>Azure Data Share と Azure Purview を接続する方法

この記事では、[Azure Data Share](../data-share/overview.md) アカウントを Azure Purview に接続し、データ資産における送信および受信の両方の共有データセットを管理する方法について説明します。 さまざまなデータ ガバナン スペルソナで、組織、部門、さらにはデータ センターなどの境界を越えてデータの系列を検出し、追跡することができます。

## <a name="common-scenarios"></a>一般的なシナリオ

Data Share のデータ系列は、根本原因分析および影響分析のための詳細情報を提供することを目的としています。

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>シナリオ 1:パートナー組織または社内部門で共有されているデータセット (入力/出力) の全体像

データ責任者は、パートナー組織と双方向で共有されているすべてのデータセットの一覧を表示できます。 また、組織名でデータセットを検索および検出し、すべての送信および受信共有の完全なビューを表示できます。

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>シナリオ 2: 根本原因分析 - 組織に入ってくるデータセットとのアップストリームの依存関係 (受信共有のコンシューマー ビュー)

外部の Data Share アカウントからのアップストリーム データの問題により、レポートには正しくない情報が含まれています。 データ エンジニアはアップストリームの障害を把握し、その理由についての情報を受け取り、さらに共有の所有者に連絡することで、データの不一致の原因となっている問題を解決することができます。

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>シナリオ 3: 組織外に送信されるデータセットに対する影響分析 (送信共有のプロバイダー ビュー)

データ プロデューサーは、データセットに変更が行われた場合に誰が影響を受けるのかを把握する必要があります。 データ系列を使用することで、データ プロデューサーは、Azure Data Share を使用してデータを利用しているダウンストリームの内部または外部パートナーに及ぶ影響を容易に把握できます。

## <a name="azure-data-share-and-purview-connected-experience"></a>Azure Data Share と Purview の接続時のエクスペリエンス

Azure Data Share と Azure Purview アカウントを接続するには、次のようにします。

1. Purview アカウントを作成します。 すべての Data Share データ系列情報が、Purview アカウントによって収集されます。 既存のものを使用することも、新たに Purview アカウントを作成することもできます。

1. Azure Data Share を Purview アカウントに接続します。

    1. Purview ポータルでは、 **[管理センター]** にアクセスし、 **[外部接続]** セクションで Azure Data Share を接続できます。
    1. 上部のバーで **[+ 新規]** を選択し、ポップアップ サイド バーで Azure Data Share を見つけて、Data Share アカウントを追加します。 Data Share を Purview アカウントに接続した後にスナップショット ジョブを実行します。この結果、Data Share 資産とデータ系列の情報が Purview に表示されます。

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="Azure Data Share をリンクするための管理センター":::

1. Azure Data Share でスナップショットを実行します。

    - Azure Purview との Azure Data Share 接続が確立されたら、既存の共有に対してスナップショットを実行することができます。 
    - 既存の共有がない場合は、Azure Data Share ポータルにアクセスして、[目的のデータを共有](../data-share/share-your-data.md)し、[データ共有をサブスクライブ](../data-share/subscribe-to-data-share.md)します。
    - 共有スナップショットが完了したら、関連付けられている Data Share 資産とデータ系列を Purview で確認できます。

1. Data Share アカウントを検出し、自分の Purview アカウントで情報を共有します。

    - Purview アカウントのホーム ページで、 **[Browse by Asset Type]\(資産の種類で参照\)** 、 **[Azure Data Share]** タイルの順に選択します。 アカウント名、共有名、共有スナップショット、またはパートナー組織を検索できます。 あるいは、[検索結果] ページで、アカウント名、共有の種類 (送信済み、または受信済みの共有) のフィルターを適用します。

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="検索結果ページでの Azure Data Share":::

    >[!Important]
    >Data Share 資産を Purview に表示するには、ご利用の Data Share を Purview に接続した後にスナップショット ジョブを実行する必要があります。

1. Azure Data Share と共有されているデータセットのデータ系列を追跡します。

    - Purview の検索結果ページで、Data Share スナップショット (受信済みまたは送信済み) を選択し、 **[データ系列]** タブを選択して、アップストリームおよびダウンストリームの依存関係に関するデータ系列グラフを表示します。

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text=" Azure Data Share を使用して共有されたデータセットのデータ系列":::

## <a name="next-steps"></a>次のステップ

- [カタログ データ系列ユーザー ガイド](catalog-lineage-user-guide.md)
- [系列のために Azure Data Factory にリンクする](how-to-link-azure-data-factory.md)
