---
title: 接続の種類
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance の接続の種類について学習します
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f1c4fe8268d24026609f55d76a102a5c9a4e8295
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91356318"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Azure SQL Managed Instance の接続の種類
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、接続の種類に応じて、クライアントがどのように Azure SQL Managed Instance に接続するかについて説明します。 接続の種類を変更するためのスクリプト サンプルと、既定の接続設定の変更に関連する考慮事項を以下に示します。

## <a name="connection-types"></a>接続の種類

Azure SQL Managed Instance では、次の 2 種類の接続をサポートしています。

- **リダイレクト (推奨):** クライアントは、データベースをホストしているノードへの直接接続を確立します。 リダイレクトを使用して接続を有効にするには、ファイアウォールとネットワーク セキュリティ グループ (NSG) を開き、ポート 1433 と 11000 から 11999 でのアクセスを許可する必要があります。 パケットはデータベースに直接送信されるため、プロキシ経由のリダイレクトを使用すると、待機時間とスループットのパフォーマンスが向上します。
- **プロキシ (既定値):** このモードでは、すべての接続でプロキシ ゲートウェイ コンポーネントが使用されます。 接続を有効にするには、プライベート ネットワーク用のポート 1433、およびパブリック接続用のポート 3342 のみを開く必要があります。 このモードを選択すると、ワークロードの性質によっては待機時間が長くなり、スループットが低下する可能性があります。 最短の待機時間と最高のスループットを実現するために、プロキシ接続ポリシーを使用したリダイレクト接続ポリシーを強くお勧めします。

## <a name="redirect-connection-type"></a>リダイレクトの接続の種類

リダイレクトの接続の種類では、TCP セッションが SQL エンジンに対して確立された後、クライアント セッションによってロード バランサーから仮想クラスター ノードの接続先の仮想 IP が取得されます。 後続のパケットは、ゲートウェイを使用せずに、仮想クラスター ノードに直接送信されます。 次の図にこのトラフィックの流れを示します。

![redirect-find-db が Azure 仮想ネットワーク内のゲートウェイに接続され、redirect-query が仮想ネットワーク内のデータベース プライマリ ノードに接続されているオンプレミス ネットワークを示す図。](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> 現在、リダイレクトの接続の種類は、プライベート エンドポイントに対してのみ機能します。 接続の種類の設定に関係なく、パブリック エンドポイント経由の接続はプロキシを介して行われます。

## <a name="proxy-connection-type"></a>プロキシの接続の種類

プロキシの接続の種類では、TCP セッションがゲートウェイを使用して確立され、すべての後続パケットがゲートウェイ経由で送信されます。 次の図にこのトラフィックの流れを示します。

![プロキシが Azure 仮想ネットワーク内のゲートウェイに接続されているオンプレミス ネットワークを示す図。その後、ゲートウェイから仮想ネットワーク内のデータベース プライマリ ノードに接続されています。](./media/connection-types-overview/proxy.png)

## <a name="changing-connection-type"></a>接続の種類を変更する

- **ポータルの使用:** Azure portal を使用して接続の種類を変更するには、Virtual Network ページを開き、 **[接続の種類]** 設定を使用して接続の種類を変更し、変更を保存します。

- **PowerShell を使用して接続の種類の設定を変更するスクリプト:**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

次の PowerShell スクリプトでは、マネージド インスタンスの接続の種類を `Redirect` に変更する方法を示しています。

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>次のステップ

- [データベースを SQL Managed Instance に復元する](restore-sample-database-quickstart.md)
- [SQL Managed Instance のパブリック エンドポイントの構成](public-endpoint-configure.md)方法について学習する
- [SQL Managed Instance の接続アーキテクチャ](connectivity-architecture-overview.md)について学習する
