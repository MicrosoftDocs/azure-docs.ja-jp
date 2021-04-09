---
title: 既存の仮想ネットワークを構成する
titleSuffix: Azure SQL Managed Instance
description: この記事では、Azure SQL Managed Instance をデプロイできる既存の仮想ネットワークとサブネットの構成方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 03/17/2020
ms.openlocfilehash: 461acc07ee2217a38f7bb59805d4c7e0de4a1e22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91617657"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance の既存の仮想ネットワークを構成する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance は、Azure [仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)とマネージド インスタンス専用のサブネット内にデプロイする必要があります。 [SQL Managed Instance の仮想ネットワーク要件](connectivity-architecture-overview.md#network-requirements)に従って構成されていれば、既存の仮想ネットワークとサブネットを使用できます。

次のいずれかのケースに該当する場合は、この記事で説明されているスクリプトを使用してネットワークを検証および変更できます。

- まだ構成されていない新しいサブネットがある。
- サブネットが[要件](connectivity-architecture-overview.md#network-requirements)に準拠しているかどうかがわからない。
- 変更を加えた後も、サブネットが[ネットワーク要件](connectivity-architecture-overview.md#network-requirements)に準拠していることを確認したい。

> [!Note]
> Azure Resource Manager デプロイ モデルを使用して作成された仮想ネットワーク内にのみマネージド インスタンスを作成できます。 クラシック デプロイ モデルを使用して作成された Azure 仮想ネットワークはサポートされていません。 [SQL Managed Instance のサブネットのサイズの決定](vnet-subnet-determine-size.md)に関する記事のガイドラインに従って、サブネットのサイズを計算します。 サブネットは、いったんリソースを配置するとサイズを変更できなくなります。
>
> マネージド インスタンスを作成した後は、インスタンスまたは VNet を別のリソース グループまたはサブスクリプションに移動することはできません。

## <a name="validate-and-modify-an-existing-virtual-network"></a>既存の仮想ネットワークを検証および変更する

既存のサブネット内にマネージド インスタンスを作成する場合は、次の PowerShell スクリプトでサブネットを準備することをお勧めします。

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

このスクリプトでは、3 つの手順でサブネットを準備します。

1. 検証:選択された仮想ネットワークとサブネットが SQL Managed Instance のネットワーク要件と照らして検証されます。
2. 確認:SQL Managed Instance のデプロイに備えてサブネットを準備するために必要な一連の変更がユーザーに示されます。 また、同意も求められます。
3. 準備:仮想ネットワークとサブネットが適切に構成されます。

## <a name="next-steps"></a>次のステップ

- 概要については、[SQL Managed Instance](sql-managed-instance-paas-overview.md) に関するページを参照してください。
- 仮想ネットワークを作成し、マネージド インスタンスを作成して、データベース バックアップからデータベースを復元する方法を示すチュートリアルについては、[マネージド インスタンスの作成](instance-create-quickstart.md)に関するページを参照してください。
- DNS の問題については、[カスタム DNS の構成](custom-dns-configure.md)に関するページを参照してください。
