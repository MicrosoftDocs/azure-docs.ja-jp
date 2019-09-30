---
title: Azure SQL Database インスタンス プールの攻略ガイド (プレビュー) | Microsoft Docs
description: この記事では、Azure SQL Database インスタンス プール (プレビュー) を作成および管理する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 5862a54c92de7395ce42865ae32d453e926048d8
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70293785"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Azure SQL Database インスタンス プール (プレビュー) の攻略ガイド

この記事では、[インスタンス プール](sql-database-instance-pools.md)を作成および管理する方法について詳しく説明します。

## <a name="instance-pool-operations"></a>インスタンス プールの操作

次の表は、インスタンス プールに関連する使用可能な操作と、Azure portal と PowerShell でそれらを使用できるかどうかを示しています。

|command|Azure ポータル|PowerShell|
|:---|:---|:---|
|インスタンス プールを作成する|いいえ|はい|
|インスタンス プールを更新する (プロパティ数に制限あり)|いいえ |はい |
|インスタンス プールの使用状況とプロパティを確認する|いいえ|はい |
|インスタンス プールを削除する|いいえ|はい|
|インスタンス プール内にマネージド インスタンスを作成する|いいえ|はい|
|マネージド インスタンス リソースの使用状況を更新する|はい |はい|
|マネージド インスタンスの使用状況とプロパティを確認する|はい|はい|
|プールからマネージド インスタンスを削除する|はい|はい|
|プールに配置されたマネージド インスタンスにデータベースを作成する|はい|はい|
|マネージド インスタンスからデータベースを削除する|はい|はい|

使用可能な [PowerShell コマンド](https://docs.microsoft.com/powershell/module/az.sql/)

|コマンドレット |説明 |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Azure SQL Database インスタンス プールを作成します。 |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Azure SQL インスタンス プールに関する情報を返します。 |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Azure SQL Database インスタンス プールのプロパティを設定します。 |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Azure SQL Database インスタンス プールを削除します。 |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Azure SQL インスタンス プールの使用状況に関する情報を返します。 |


Powershell を使用するには、[最新バージョンの PowerShell Core をインストール](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell-core)し、指示に従って [Azure PowerShell モジュールをインストール](https://docs.microsoft.com/powershell/azure/install-az-ps)します。

プール内のインスタンスと単一インスタンスの両方に関連する操作では、標準の[マネージド インスタンス コマンド](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)を使用しますが、プール内のインスタンスでこれらのコマンドを使用する場合は、"*インスタンス プール名*" プロパティを設定する必要があります。

## <a name="how-to-deploy-managed-instances-into-pools"></a>マネージド インスタンスをプールにデプロイする方法

プールにインスタンスをデプロイするプロセスは、次の 2 つの手順で構成されています。

1. 1 回限りのインスタンス プール デプロイ。 これは、実行時間の長い操作で、[空のサブネットに作成された単一インスタンス](sql-database-managed-instance.md#managed-instance-management-operations)をデプロイするのと同じ時間がかかります。

2. インスタンス プール内の繰り返しのインスタンス デプロイ。 インスタンス プール パラメーターは、この操作の一部として明示的に指定する必要があります。 これは比較的高速な操作であり、通常は最大 5 分かかります。

パブリック プレビューでは、どちらの手順も、PowerShell および Resource Manager テンプレートを使用した場合のみサポートされます。 Azure portal エクスペリエンスは現在使用できません。

プールにマネージド インスタンスがデプロイされた後は、Azure portal を使用して、価格レベル ページでそのプロパティを変更 "*できます*"。


## <a name="create-an-instance-pool"></a>インスタンス プールを作成する

インスタンス プールを作成するには:

1. [サブネットを含む仮想ネットワークを作成します](#create-a-virtual-network-with-a-subnet)。
2. [インスタンス プールを作成します](#create-an-instance-pool)。


### <a name="create-a-virtual-network-with-a-subnet"></a>サブネットを含む仮想ネットワークを作成する 

同じ仮想ネットワーク内に複数のインスタンス プールを配置するには、次の記事を参照してください。

- [Azure SQL Database Managed Instance に使用する VNet のサブネット サイズを決める](sql-database-managed-instance-determine-size-vnet-subnet.md)。
- [Azure portal テンプレート](sql-database-managed-instance-create-vnet-subnet.md)を使用して新しい仮想ネットワークとサブネットを作成するか、[既存の仮想ネットワークを準備する](sql-database-managed-instance-configure-vnet-subnet.md)手順に従う。
 


### <a name="create-an-instance-pool"></a>インスタンス プールを作成する 

前の手順を完了すると、インスタンス プールを作成する準備が整います。

インスタンス プールには、次の制限が適用されます。

- パブリック プレビューでは、General Purpose と Gen5 のみを使用できます。
- プール名に使用できるのは、小文字、数字、ハイフンのみで、先頭をハイフンにすることはできません。
- サブネット ID を取得するには、`Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork` を使用します。
- AHB (Azure ハイブリッド特典) を使用する場合は、インスタンス プール レベルで適用されます。 プールの作成時にライセンスの種類を設定できます。また、作成後にいつでも更新することができます。

> [!IMPORTANT]
> インスタンス プールのデプロイは実行時間の長い操作であり、約 4.5 時間かかります。

インスタンス プールを作成するには:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId "/subscriptions/subscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/miPoolVirtualNetwork/subnets/miPoolSubnet" `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> インスタンス プールのデプロイは実行時間の長い操作であるため、この記事の次の手順はすべて、完了するまで待ってから行ってください。

## <a name="create-a-managed-instance-inside-the-pool"></a>プール内にマネージド インスタンスを作成する 

インスタンス プールが正常にデプロイされたら、その内部にインスタンスを作成します。

マネージド インスタンスを作成するには、次のコマンドを実行します。

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

プール内にインスタンスをデプロイするには数分かかります。 最初のインスタンスが作成された後、追加のインスタンスを作成できます。

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>インスタンス内にデータベースを作成する 

プール内のマネージド インスタンスでデータベースを作成および管理するには、単一インスタンス コマンドを使用します。

マネージド インスタンス内にデータベースを作成するには、次のようにします。

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>インスタンス プールの使用状況を取得する 
 
プール内のインスタンスのリストを取得するには:

```powershell
$instancePool | Get-AzSqlInstance
```


プール リソースの使用状況を取得するには:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


プールとその内部のインスタンスの詳細な使用状況の概要を取得するには:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

インスタンス内のデータベースをリストするには:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> プールあたり (インスタンスあたりではない) のデータベース数は 100 に制限されています。


## <a name="scale-a-managed-instance-inside-a-pool"></a>プール内のマネージド インスタンスをスケーリングする 


マネージド インスタンスにデータベースを設定した後、ストレージまたはパフォーマンスに関するインスタンスの制限に達することがあります。 その場合、プールの使用量を超えていなければ、インスタンスをスケーリングできます。
プール内のマネージド インスタンスのスケーリングは、数分かかる操作です。 スケーリングの前提条件として、インスタンス プール レベルで仮想コアとストレージを使用できます。

仮想コアの数とストレージ サイズを更新するには:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


ストレージ サイズのみを更新するには:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>プール内のマネージド インスタンスに接続する

プール内のマネージド インスタンスに接続するには、次の 2 つの手順を行う必要があります。

1. [インスタンスのパブリック エンドポイントを有効にします](#enable-the-public-endpoint-for-the-instance)。
2. [ネットワーク セキュリティ グループ (NSG) に受信規則を追加します](#add-an-inbound-rule-to-the-network-security-group)。

両方の手順が完了したら、インスタンス作成時に提供されたパブリック エンドポイント アドレス、ポート、および資格情報を使用して、インスタンスに接続できます。 

### <a name="enable-the-public-endpoint-for-the-instance"></a>インスタンスのパブリック エンドポイントを有効にする

インスタンスのパブリック エンドポイントの有効化は、Azure portal から、または次の PowerShell コマンドを使用して行うことができます。


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

このパラメーターは、インスタンスの作成時にも設定できます。

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>ネットワーク セキュリティ グループに受信規則を追加する 

この手順は、Azure portal から、または PowerShell コマンドを使用して行うことができ、マネージド インスタンスのサブネットが準備された後はいつでも行うことができます。

詳細については、「[ネットワーク セキュリティ グループでパブリック エンドポイント トラフィックを許可する](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)」を参照してください。


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>インスタンス プール内の既存の単一インスタンスを移動する 
 
プール内外へのインスタンスの移動は、パブリック プレビューの制限事項の 1 つです。 使用できる回避策は、プールの外部のインスタンスから、プールに既に存在するインスタンスへのデータベースのポイントインタイム リストアに依存します。 

両方のインスタンスが同じサブスクリプションとリージョン内にある必要があります。 リージョン間およびサブスクリプション間での復元は、現在、サポートされていません。

このプロセスにはダウンタイムがあります。

既存のデータベースを移動するには:

1. 移行元のマネージド インスタンスのワークロードを一時停止します。
2. システム データベースを作成するスクリプトを生成して、インスタンス プール内のインスタンスで実行します。
3. 単一インスタンスから、プール内のインスタンスへの各データベースのポイントインタイム リストアを行います。

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. アプリケーションを新しいインスタンスにポイントし、そのワークロードを再開します。

複数のデータベースがある場合は、データベースごとにプロセスを繰り返します。


## <a name="next-steps"></a>次の手順

- 機能比較一覧については、[SQL 共通機能](sql-database-features.md)に関する記事をご覧ください。
- VNet の構成の詳細については、[マネージド インスタンス VNet の構成](sql-database-managed-instance-connectivity-architecture.md)に関するページを参照してください。
- マネージド インスタンスを作成し、バックアップ ファイルからデータベースを復元するためのクイック スタートについては、[マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)に関するページを参照してください。
- Azure Database Migration Service (DMS) を使用して移行する方法のチュートリアルについては、[DMS を使用したマネージド インスタンスの移行](../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。
- 組み込みのトラブルシューティング インテリジェンスを使用したマネージド インスタンス データベースの高度なパフォーマンス監視については、[Azure SQL Analytics を使用した Azure SQL Database の監視](../azure-monitor/insights/azure-sql.md)に関するページを参照してください。
- 料金情報については、[SQL Database マネージド インスタンスの価格](https://azure.microsoft.com/pricing/details/sql-database/managed/)に関するページを参照してください。
