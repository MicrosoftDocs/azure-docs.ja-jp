---
title: Azure Cosmos DB 用 Azure PowerShell サンプル
description: Azure PowerShell サンプル - Azure Cosmos DB アカウントの作成と管理で役立つスクリプト。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: 3498ac6a2a4aaa1682d7b5bc5aae5383866d5bcd
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873799"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure Cosmos DB 用 Azure PowerShell サンプル

次の表には、Azure Cosmos DB の Azure PowerShell スクリプトのサンプルへのリンクが含まれています。 現時点では、PowerShell 経由で Azure Cosmos DB アカウントのみを管理できます。データベースやコンテナーなどの他のリソースは、PowerShell 経由では管理できません。

| |  |
|---|---|
|**Azure Cosmos DB アカウントの作成**||
|[SQL API の Cosmos アカウントを作成して構成する](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| SQL API で使用する単一の Azure Cosmos DB アカウントを作成します。 |
|[Azure Cosmos DB の MongoDB 用 API の Cosmos アカウントを作成して構成する](scripts/create-mongodb-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos DB の MongoDB 用 API で使用する単一の Cosmos アカウントを作成します。 |
|[Gremlin API の Cosmos アカウントを作成して構成する](scripts/create-graph-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Gremlin API で使用する単一の Azure Cosmos DB アカウントを作成します。 |
|[Cassandra API の Cosmos アカウントを作成して構成する](scripts/create-and-configure-cassandra-database.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cassandra API で使用する単一の Azure Cosmos DB アカウントを作成します。 |
|[Table API の Cosmos アカウントを作成して構成する](scripts/create-table-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Table API で使用する単一の Azure Cosmos DB アカウントを作成します。 |
|**Azure Cosmos DB のスケーリング**||
|[複数リージョンへの Azure Cosmos DB アカウントのレプリケートと、フェールオーバーの優先順位の構成](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|指定したフェールオーバー優先順位で、複数リージョンにグローバルにアカウント データをレプリケートします。|
|**Azure Cosmos DB のセキュリティ保護**||
| [アカウント キーの取得](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | アカウントのプライマリおよびセカンダリのマスター書き込みキーと、プライマリおよびセカンダリの読み取り専用キーを取得します。|
| [MongoDB 接続文字列の取得](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | MongoDB アプリを Azure Cosmos DB アカウントに接続する接続文字列を取得します。|
|[アカウント キーの再生成](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|アカウントのマスター キーまたは読み取り専用キーを再生成します。|
|[ファイアウォールの作成](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 承認した一連のコンピューターやクラウド サービスからアカウントへのアクセスを制限する着信 IP アクセス制御ポリシーを作成します。|
|**高可用性、ディザスター リカバリー、バックアップと復元**||
|[フェールオーバー ポリシーの構成](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|アカウントのレプリケート先の各リージョンのフェールオーバー優先順位を設定します。|
|||
