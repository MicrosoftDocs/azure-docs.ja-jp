---
title: Azure SQL Managed Instance のサービス支援サブネット構成の有効化
description: Azure SQL Managed Instance のサービス支援サブネット構成の有効化
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: 67b398194d9094cd99fccaa85ed0df3be362ce2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91618015"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance のサービス支援サブネット構成の有効化
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

サービス支援サブネット構成を使用すると、マネージ インスタンスをホストしているサブネットのネットワーク構成管理を自動化できます。 サービス支援サブネット構成では、ユーザーがデータへのアクセス (TDS トラフィック フロー) を完全に制御できる状態を維持しつつ、管理トラフィックが中断されないようにするための処理はマネージド インスタンスによって行われ、それによって SLA の履行が支援されます。

自動的に構成されたネットワーク セキュリティ グループとルート テーブル規則は、お客様に表示され、_Microsoft.Sql-managedInstances_UseOnly_ _ というプレフィックスで注釈が付けられます。

サービス支援構成は、`Microsoft.Sql/managedInstances` リソース プロバイダーの [サブネット委任](../../virtual-network/subnet-delegation-overview.md)をオンにすると、自動的に有効になります。

> [!IMPORTANT] 
> サブネット委任を有効にすると、最後の仮想クラスターをサブネットから削除するまで、そのサブネットを無効にすることはできません。 仮想クラスターを削除する方法の詳細については、次の[記事](virtual-cluster-delete.md#delete-a-virtual-cluster-from-the-azure-portal)を参照してください。

> [!NOTE] 
> サービス支援サブネット構成は、SLA を維持するための重要な機能です。2020 年 5 月 1 日以降は、マネージド インスタンス リソース プロバイダーに委任されていないサブネットに、マネージド インスタンスをデプロイすることはできなくなります。 2020 年 7 月 1 日には、マネージド インスタンスを含んでいるすべてのサブネットが、マネージド インスタンス リソース プロバイダーに自動的に委任されます。 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>新しいデプロイに対するサブネット委任の有効化
マネージド インスタンスを空のサブネットにデプロイするには、次の[記事](../../virtual-network/manage-subnet-delegation.md)で説明されているように、目的のマネージド インスタンスを `Microsoft.Sql/managedInstances` リソース プロバイダーに委任する必要があります。 _なお、参照先の記事では、例として `Microsoft.DBforPostgreSQL/serversv2` というリソース プロバイダーが使用されていることに注意してください。お客様は、代わりに `Microsoft.Sql/managedInstances` というリソース プロバイダーを使用する必要があります。_

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>既存のデプロイに対するサブネット委任の有効化

既存のマネージド インスタンス デプロイに対してサブネット委任を有効にするには、それが配置されている仮想ネットワーク サブネットを特定する必要があります。 

これを知るには、マネージド インスタンスの `Overview` ポータル ブレードで `Virtual network/subnet` を確認してください。

別の方法として、次の PowerShell コマンドを実行して確認することもできます。 **subscription-id** を実際のサブスクリプション ID に置き換えます。 また、**rg-name** を実際のマネージド インスタンスのリソース グループに置き換え、**mi-name** を実際のマネージド インスタンスの名前に置き換えます。

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

マネージド インスタンス サブネットが確認できたら、次の[記事](../../virtual-network/manage-subnet-delegation.md)で説明されているように、目的のマネージド インスタンスを `Microsoft.Sql/managedInstances` リソース プロバイダーに委任する必要があります。 _なお、参照先の記事では、例として `Microsoft.DBforPostgreSQL/serversv2` というリソース プロバイダーが使用されていることに注意してください。お客様は、代わりに `Microsoft.Sql/managedInstances` というリソース プロバイダーを使用する必要があります。_


> [!IMPORTANT]
> サービス支援構成を有効にしても、既にサブネット内に存在しているマネージド インスタンスに対する接続のフェールオーバーや中断は発生しません。
