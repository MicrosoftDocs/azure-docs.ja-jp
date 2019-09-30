---
title: Azure Cosmos DB のセキュリティ コントロール
description: Azure Cosmos DB を評価するためのセキュリティ コントロールのチェックリスト
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 20cdfc61a4cdfe5263e48d049aab14cad2458b06
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886311"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Azure Cosmos DB のセキュリティ コントロール

この記事では、Azure Cosmos DB に組み込まれているセキュリティ コントロールについて説明します。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>ネットワーク

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| サービス エンドポイントのサポート| はい |  |
| VNet インジェクションのサポート| はい | VNet サービス エンドポイントを使用して、仮想ネットワーク (VNet) の特定のサブネットからのアクセスのみを許可するように Azure Cosmos DB アカウントを構成できます。 VNet へのアクセスとファイアウォール規則を組み合わせることもできます。  「[仮想ネットワークから Azure Cosmos DB にアクセスする](VNet-service-endpoint.md)」を参照してください。 |
| ネットワークの分離とファイアウォールのサポート| はい | ファイアウォールのサポートを使用して、承認された IP アドレスのセット、IP アドレスの範囲、およびクラウド サービスからのアクセスのみを許可するように Azure Cosmos アカウントを構成できます。 「[Azure Cosmos DB で IP ファイアウォールを構成する](how-to-configure-firewall.md)」を参照してください。|
| 強制トンネリングのサポート| はい | 仮想マシンが配置されている VNet のクライアント側で構成できます。   |

## <a name="monitoring--logging"></a>監視およびログ記録

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| Azure 監視サポート (Log analytics や App Insights など)| はい | Azure Cosmos DB に送信されたすべての要求が記録されます。 [Azure 監視](../azure-monitor/overview.md)、Azure メトリック、Azure 監査ログがサポートされています。  データ プレーン要求、クエリのランタイム統計、クエリ テキスト、MongoDB 要求に対応する情報を記録することができます。 アラートを設定することもできます。 |
| コントロールと管理プレーンのログ記録と監査| はい | ファイアウォール、VNet、キー アクセス、IAM などのアカウント レベルの操作の Azure アクティビティ ログ。 |
| データ プレーンのログ記録と監査 | はい | コンテナーの作成、スループットのプロビジョニング、インデックス作成ポリシー、ドキュメントの CRUD 操作などのコンテナー レベルの診断監視のログ記録。 |

## <a name="identity"></a>ID

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 認証| はい | データベース アカウント レベルでは「はい」です。データ プレーン レベルでは、Cosmos DB では、リソース トークンとキー アクセスが使用されます。 |
| Authorization| はい | マスター キー (プライマリおよびセカンダリ) とリソース トークンを持つ Azure Cosmos アカウントでサポートされます。 マスター キーを使用して、データに対して、読み取り/書き込みアクセスまたは読み取り専用アクセスを実行できます。 リソース トークンを使用して、ドキュメントやコンテナーなどのリソースに、限られた時間だけアクセスできます。 |

## <a name="data-protection"></a>データ保護

| セキュリティ コントロール | はい/いいえ | メモ |
|---|---|--|
| 保存時のサーバー側の暗号化:Microsoft のマネージド キー | はい | すべての Cosmos データベースとバックアップは、既定で暗号化されます。「[Azure Cosmos DB でのデータの暗号化](database-encryption-at-rest.md)」を参照してください。 カスタマー マネージド キーによるサーバー側での暗号化はサポートされていません。 |
| 保存時のサーバー側の暗号化: カスタマー マネージド キー (BYOK) | いいえ |  |
| 列レベルの暗号化 (Azure Data Services)| はい | Tables API Premium でのみ。 この機能は、すべての API でサポートされるわけではありません。 「[Azure Cosmos DB の概要:Table API](table-introduction.md)」を参照してください。 |
| 転送中の暗号化 (ExpressRoute 暗号化、VNet 内の暗号化、および VNet 間暗号化など)| はい | Azure Cosmos DB のすべてのデータは、転送中に暗号化されます。 |
| API 呼び出しの暗号化| はい | Azure Cosmos DB へのすべての接続で HTTPS がサポートされます。 Azure Cosmos DB では、TLS 1.2 接続もサポートされますが、これはまだ実施されていません。 お客様が自分の側で下位レベルの TLS をオフにすると、Cosmos DB に確実に接続できます。  |

## <a name="configuration-management"></a>構成管理

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| 構成管理のサポート (構成のバージョン管理など)| いいえ  | | 

## <a name="additional-security-controls-for-cosmos-db"></a>Cosmos DB の追加のセキュリティ コントロール

| セキュリティ コントロール | はい/いいえ | メモ|
|---|---|--|
| クロスオリジン リソース共有 (CORS) | はい | 「[クロスオリジン リソース共有 (CORS) の構成](how-to-configure-cross-origin-resource-sharing.md)」を参照してください。 |

## <a name="next-steps"></a>次の手順

- [Azure サービス全体の組み込みセキュリティ コントロール](../security/fundamentals/security-controls.md)について説明します。