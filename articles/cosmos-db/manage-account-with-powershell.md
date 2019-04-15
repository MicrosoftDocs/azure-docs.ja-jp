---
title: PowerShell を使用した Azure Cosmos DB リソースの作成と管理
description: Azure PowerShell を使用して Azure Cosmos DB アカウントを管理します。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 01c351ad08399c0b42e831e325b3f818741d1d83
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904374"
---
# <a name="manage-azure-cosmos-resources-using-powershell"></a>PowerShell を使用した Azure Cosmos リソースの管理

次のガイドでは、Azure PowerShell を使用して Azure Cosmos DB データベース アカウントの管理を自動化するためのコマンドについて説明します。 また、[複数リージョンのデータベース アカウント][distribute-data-globally]でアカウント キーとフェールオーバー優先度を管理するコマンドについても説明します。 データベース アカウントを更新すると、一貫性ポリシーの変更と、リージョンの追加または削除を行うことができます。 Azure Cosmos DB アカウントのクロスプラットフォーム管理には、[Azure CLI](cli-samples.md)、[リソース プロバイダーの REST API][rp-rest-api]、[Azure Portal](create-sql-api-dotnet.md#create-account) のいずれも使用可能です。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Getting Started (概要)

[Azure PowerShell のインストールと構成][powershell-install-configure]に関する記事の手順に従ってインストールを行い、PowerShell で Azure Resource Manager アカウントにログインします。

### <a name="notes"></a>メモ

* 次のコマンドをユーザーの確認を要求せずに実行する場合は、`-Force` フラグをコマンドに追加します。
* 次のコマンドはすべて同期コマンドです。

## <a id="create-documentdb-account-powershell"></a> Azure Cosmos DB アカウントを作成する

このコマンドでは、Azure Cosmos DB データベース アカウントを作成できます。 新しいデータベース アカウントは、単一リージョンまたは[複数リージョン][distribute-data-globally]で、特定の[一貫性ポリシー](consistency-levels.md)に基づいて構成します。

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` データベース アカウントの書き込みリージョンの場所の名前。 この場所のフェールオーバー優先度の値は 0 である必要があります。 書き込みリージョンは、データベース アカウントごとに 1 つである必要があります。
* `<read-region-location>` データベース アカウントの読み取りリージョンの場所の名前。 この場所のフェールオーバー優先度の値は 0 よりも大きい値である必要があります。 読み取りリージョンは、データベース アカウントごとに複数あってもかまいません。
* `<ip-range-filter>` 特定のデータベース アカウントのクライアント IP の許可リストとして追加する一連の IP アドレスまたは IP アドレス範囲を CIDR 形式で指定する必要があります。 IP アドレス/範囲は、コンマで区切る必要があり、スペースを含めることはできません。 詳細については、「[Azure Cosmos DB のファイアウォール サポート](firewall-support.md)」をご覧ください。
* `<default-consistency-level>` Azure Cosmos DB アカウントの既定の一貫性レベル。 詳細については、[Azure Cosmos DB の一貫性レベル](consistency-levels.md)に関する記事をご覧ください。
* `<max-interval>` 有界整合性制約の一貫性と併用すると、この値は許容される古さの期間を秒単位で示します。 この値の許容範囲は、1 ～ 100 です。
* `<max-staleness-prefix>` 有界整合性制約の一貫性と併用すると、この値は許容される古い要求の数を示します。 この値の許容範囲は、1 ～ 2,147,483,647 です。
* `<resource-group-name>` 新しい Azure Cosmos DB データベース アカウントが属する [Azure リソース グループ][azure-resource-groups]の名前。
* `<resource-group-location>` 新しい Azure Cosmos DB データベース アカウントが属する Azure リソース グループの場所。
* `<database-account-name>` 作成する Azure Cosmos DB データベース アカウントの名前。 使用できる文字は、英小文字、数字、"-" のみです。文字数は 3 ～ 50 文字にする必要があります。

例: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>メモ
* 前の例では、2 つのリージョンでデータベース アカウントを作成します。 単一リージョン (書き込みリージョンとして指定されており、フェールオーバー優先度の値が 0 であるもの) または 3 つ以上のリージョンでデータベース アカウントを作成することも可能です。 詳細については、[複数リージョンのデータベース アカウント][distribute-data-globally]に関する記事を参照してください。
* 場所は、Azure Cosmos DB が一般公開されているリージョンである必要があります。 最新のリージョン一覧については、 [Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。

## <a id="update-documentdb-account-powershell"></a>Azure Cosmos DB データベース アカウントを更新する

このコマンドでは、Azure Cosmos DB データベース アカウントのプロパティを更新できます。 これには、一貫性ポリシーと、データベース アカウントが存在する場所が含まれます。

> [!NOTE]
> このコマンドでは、リージョンの追加および削除が可能ですが、フェールオーバー優先度を変更することはできません。 フェールオーバー優先度の変更方法については、[こちら](#modify-failover-priority-powershell)を参照してください。

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` データベース アカウントの書き込みリージョンの場所の名前。 この場所のフェールオーバー優先度の値は 0 である必要があります。 書き込みリージョンは、データベース アカウントごとに 1 つである必要があります。
* `<read-region-location>` データベース アカウントの読み取りリージョンの場所の名前。 この場所のフェールオーバー優先度の値は 0 よりも大きい値である必要があります。 読み取りリージョンは、データベース アカウントごとに複数あってもかまいません。
* `<default-consistency-level>` Azure Cosmos DB アカウントの既定の一貫性レベル。 詳細については、[Azure Cosmos DB の一貫性レベル](consistency-levels.md)に関する記事をご覧ください。
* `<ip-range-filter>` 特定のデータベース アカウントのクライアント IP の許可リストとして追加する一連の IP アドレスまたは IP アドレス範囲を CIDR 形式で指定する必要があります。 IP アドレス/範囲は、コンマで区切る必要があり、スペースを含めることはできません。 詳細については、「[Azure Cosmos DB のファイアウォール サポート](firewall-support.md)」をご覧ください。
* `<max-interval>` 有界整合性制約の一貫性と併用すると、この値は許容される古さの期間を秒単位で示します。 この値の許容範囲は、1 ～ 100 です。
* `<max-staleness-prefix>` 有界整合性制約の一貫性と併用すると、この値は許容される古い要求の数を示します。 この値の許容範囲は、1 ～ 2,147,483,647 です。
* `<resource-group-name>` 新しい Azure Cosmos DB データベース アカウントが属する [Azure リソース グループ][azure-resource-groups]の名前。
* `<resource-group-location>` 新しい Azure Cosmos DB データベース アカウントが属する Azure リソース グループの場所。
* `<database-account-name>` 更新する Azure Cosmos DB データベース アカウントの名前。

例: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

## <a id="delete-documentdb-account-powershell"></a>Azure Cosmos DB データベース アカウントを削除する

このコマンドでは、既存の Azure Cosmos DB データベース アカウントを削除できます。

    Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` 新しい Azure Cosmos DB データベース アカウントが属する [Azure リソース グループ][azure-resource-groups]の名前。
* `<database-account-name>` 削除する Azure Cosmos DB データベース アカウントの名前。

例:

    Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a>Azure Cosmos DB データベース アカウントのプロパティを取得する

このコマンドでは、既存の Azure Cosmos DB データベース アカウントのプロパティを取得できます。

    Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` 新しい Azure Cosmos DB データベース アカウントが属する [Azure リソース グループ][azure-resource-groups]の名前。
* `<database-account-name>` Azure Cosmos DB データベース アカウントの名前。

例:

    Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a> Azure Cosmos DB データベース アカウントのタグの更新

次の例は、Azure Cosmos DB データベース アカウントで使用する [Azure のリソース タグ][azure-resource-tags]の設定方法を示しています。

> [!NOTE]
> このコマンドは、対応するパラメーターと `-Tags` フラグを追加することで、作成または更新のコマンドと組み合わせることができます。

例:

    $tags = @{"dept" = "Finance"; environment = "Production"}
    Set-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts"  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a> アカウント キーの一覧表示

Azure Cosmos DB アカウントを作成すると、2 つのマスター アクセス キーが生成されます。これらのアクセス キーは、Azure Cosmos DB アカウントにアクセスする際の認証に使用できます。 2 つのアクセス キーが提供されるので、Azure Cosmos DB アカウントを中断することなくキーを再生成できます。 読み取り専用操作を認証するための読み取り専用キーも使用できます。 2 つの読み取り/書き込みキー (プライマリおよびセカンダリ) と、2 つの読み取り専用キー (プライマリおよびセカンダリ) が存在します。

    $keys = Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` 新しい Azure Cosmos DB データベース アカウントが属する [Azure リソース グループ][azure-resource-groups]の名前。
* `<database-account-name>` Azure Cosmos DB データベース アカウントの名前。

例:

    $keys = Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a> 接続文字列の一覧表示

MongoDB アカウントの場合、MongoDB アプリをデータベース アカウントに接続する接続文字列は、次のコマンドで取得できます。

    $keys = Invoke-AzResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` 新しい Azure Cosmos DB データベース アカウントが属する [Azure リソース グループ][azure-resource-groups]の名前。
* `<database-account-name>` Azure Cosmos DB データベース アカウントの名前。

例:

    $keys = Invoke-AzResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a> アカウント キーの再生成

接続のセキュリティを高めるために、Azure Cosmos DB アカウントのアクセス キーは定期的に変更する必要があります。 一方のアクセス キーを使用して Azure Cosmos DB アカウントへの接続を維持しながら、もう一方のアクセス キーを再生成できるように、2 つのアクセス キーが割り当てられます。

    Invoke-AzResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` 新しい Azure Cosmos DB データベース アカウントが属する [Azure リソース グループ][azure-resource-groups]の名前。
* `<database-account-name>` Azure Cosmos DB データベース アカウントの名前。
* `<key-kind>` 再生成するキーの種類 ("Primary"、"Secondary"、"PrimaryReadonly"、"SecondaryReadonly" の 4 種類のうちのいずれか)。

例:

    Invoke-AzResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a> Azure Cosmos DB データベース アカウントのフェールオーバー優先度の変更

複数リージョンのデータベース アカウントでは、Azure Cosmos DB データベース アカウントが存在するさまざまなリージョンのフェールオーバー優先度を変更できます。 Azure Cosmos DB データベース アカウントでのフェールオーバーの詳細については、[Azure Cosmos DB を使用したデータのグローバル分散][distribute-data-globally]に関する記事をご覧ください。

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` データベース アカウントの書き込みリージョンの場所の名前。 この場所のフェールオーバー優先度の値は 0 である必要があります。 書き込みリージョンは、データベース アカウントごとに 1 つである必要があります。
* `<read-region-location>` データベース アカウントの読み取りリージョンの場所の名前。 この場所のフェールオーバー優先度の値は 0 よりも大きい値である必要があります。 読み取りリージョンは、データベース アカウントごとに複数あってもかまいません。
* `<resource-group-name>` 新しい Azure Cosmos DB データベース アカウントが属する [Azure リソース グループ][azure-resource-groups]の名前。
* `<database-account-name>` Azure Cosmos DB データベース アカウントの名前。

例:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>次の手順

* .NET を使用して接続する方法については、[.NET を使った接続とクエリ](create-sql-api-dotnet.md)に関する記事をご覧ください。
* Node.js を使用して接続する方法については、[Node.js と MongoDB アプリを使った接続とクエリ](create-mongodb-nodejs.md)に関する記事をご覧ください。

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
