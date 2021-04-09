---
title: Azure Arc 対応 PostgreSQL Hyperscale の制限事項
description: Azure Arc 対応 PostgreSQL Hyperscale の制限事項
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b1a56c8acf1789690c01f1c16b7c37a237720e39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100416851"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale の制限事項

この記事では、Azure Arc 対応 PostgreSQL Hyperscale の制限事項について説明します。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>バックアップと復元

- 同じサーバー グループへのポイントインタイム リストア (特定の日付と時刻への復元など) はサポートされていません。 ポイントインタイム リストアを実行する場合は、復元する前にデプロイした別のサーバー グループに復元する必要があります。 新しいサーバー グループに復元した後、元のサーバー グループを削除することができます。
- (特定の時点までの復元ではなく) バックアップの内容全体を同じサーバー　グループに復元することは、PostgreSQL バージョン 12 でサポートされています。 Postgresql エンジンとタイムラインの制限があるため、PostgreSQL バージョン 11 ではサポートされません。 バージョン 11 の PostgreSQL サーバー グループのバックアップの内容全体を復元するには、別のサーバー グループに復元する必要があります。


## <a name="databases"></a>データベース

1 つのサーバー グループで複数のデータベースをホストすることはサポートされていません。


## <a name="security"></a>セキュリティ

ユーザーとロールの管理はサポートされていません。 当面は、postgres 標準ユーザーを引き続き使用してください。

## <a name="roles-and-responsibilities"></a>ロールと責任

Microsoft とそのお客様の間のロールと責任は、Azure PaaS サービス (サービスとしてのプラットフォーム) と Azure ハイブリッド (Azure Arc 対応 PostgreSQL Hyperscale など) とで異なります。 

### <a name="frequently-asked-questions"></a>よく寄せられる質問

次の表は、サポートのロールと責任に関してよく寄せられる質問とその回答をまとめたものです。

| Question                      | Azure PaaS (サービスとしてのプラットフォーム) | Azure Arc ハイブリッド サービス |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| インフラストラクチャの提供者  | Microsoft                          | Customer                  |
| ソフトウェアの提供者*       | Microsoft                          | Microsoft                 |
| 運営者 | Microsoft                          | Customer                  |
| Microsoft による SLA 提供の有無      | はい                                | いいえ                        |
| SLA の責任者 | Microsoft                          | Customer                  |

\* Azure サービス

__Microsoft が Azure Arc ハイブリッド サービスに SLA を提供しないのはなぜですか。__ Microsoft はこのインフラストラクチャを所有せず、運営していないためです。 これはお客様が行います。

## <a name="next-steps"></a>次のステップ

- **実際に使ってみてください。** Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。 

- **独自のものを作成します。** 独自の Kubernetes クラスターを作成するには、こちらの手順に従います。 
   1. [クライアント ツールをインストールする](install-client-tools.md)
   2. [Azure Arc データ コントローラーを作成する](create-data-controller.md)
   3. [Azure Arc で Azure Database for PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Azure Arc 対応データ サービスについて確認する](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Azure Arc について確認する](https://aka.ms/azurearc)
