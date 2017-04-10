---
title: "Azure DocumentDB Automation - PowerShell での管理 | Microsoft Docs"
description: "Azure PowerShell を使用して DocumentDB データベース アカウントを管理します。 DocumentDB は、JSON データ用のクラウドベースの NoSQL データベースです。"
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: 8078f9822b392af09e00e9bf1e448e0a51994e11
ms.openlocfilehash: 15707a71500424e4776adc80491af95b57bea222
ms.lasthandoff: 02/27/2017


---
# <a name="automate-azure-documentdb-account-management-using-azure-powershell"></a>Azure PowerShell を使用した Azure DocumentDB アカウントの管理の自動化
> [!div class="op_single_selector"]
> * [Azure ポータル](documentdb-create-account.md)
> * [Azure CLI 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Azure CLI 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

次のガイドでは、Azure PowerShell を使用して DocumentDB データベース アカウントの管理を自動化するためのコマンドについて説明します。 また、[複数リージョンのデータベース アカウント][scaling-globally]でアカウント キーとフェールオーバー優先度を管理するコマンドについても説明します。 データベース アカウントを更新すると、一貫性ポリシーの変更と、リージョンの追加または削除を行うことができます。 DocumentDB データベース アカウントのクロスプラットフォーム管理には、[Azure CLI](documentdb-automation-resource-manager-cli.md)、[リソース プロバイダーの REST API][rp-rest-api]、[Azure Portal](documentdb-create-account.md) のどれもが使用可能です。

## <a name="getting-started"></a>Getting Started (概要)

[Azure PowerShell のインストールと構成][powershell-install-configure]に関する記事の手順に従ってインストールを行い、PowerShell で Azure Resource Manager アカウントにログインします。

### <a name="notes"></a>メモ

* 次のコマンドをユーザーの確認を要求せずに実行する場合は、`-Force` フラグをコマンドに追加します。
* 次のコマンドはすべて同期コマンドです。

## <a name="a-idcreate-documentdb-account-powershella-create-a-documentdb-database-account"></a><a id="create-documentdb-account-powershell"></a> DocumentDB データベース アカウントの作成

次のコマンドでは、DocumentDB データベース アカウントを作成することができます。 新しいデータベース アカウントは、単一リージョンまたは[複数リージョン][scaling-globally]で、特定の[一貫性ポリシー](documentdb-consistency-levels.md)に基づいて構成します。

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -PropertyObject $DocumentDBProperties
    
* `<write-region-location>` データベース アカウントの書き込みリージョンの場所の名前。 この場所のフェールオーバー優先度の値は 0 である必要があります。 書き込みリージョンは、データベース アカウントごとに&1; つである必要があります。
* `<read-region-location>` データベース アカウントの読み取りリージョンの場所の名前。 この場所のフェールオーバー優先度の値は 0 よりも大きい値である必要があります。 読み取りリージョンは、データベース アカウントごとに複数あってもかまいません。
* `<ip-range-filter>` 特定のデータベース アカウントのクライアント IP の許可リストとして追加する一連の IP アドレスまたは IP アドレス範囲を CIDR 形式で指定する必要があります。 IP アドレス/範囲は、コンマで区切る必要があり、スペースを含めることはできません。 詳細については、「[DocumentDB のファイアウォール サポート](documentdb-firewall-support.md)」を参照してください。
* `<default-consistency-level>` DocumentDB アカウントの既定の一貫性レベル。 詳細については、「[DocumentDB の一貫性レベル](documentdb-consistency-levels.md)」を参照してください。
* `<max-interval>` 有界整合性制約の一貫性と併用すると、この値は許容される古さの期間を秒単位で示します。 この値の許容範囲は、1 ～ 100 です。
* `<max-staleness-prefix>` 有界整合性制約の一貫性と併用すると、この値は許容される古い要求の数を示します。 この値の許容範囲は、1 ～ 2,147,483,647 です。
* `<resource-group-name>` 新しい DocumentDB データベース アカウントが属する [Azure リソース グループ][azure-resource-groups]の名前。
* `<resource-group-location>` 新しい DocumentDB データベース アカウントが属する Azure リソース グループの場所。
* `<database-account-name>` 作成する DocumentDB データベース アカウントの名前。 使用できる文字は、英小文字、数字、"-" のみです。文字数は 3 ～ 50 文字にする必要があります。

例: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -PropertyObject $DocumentDBProperties

### <a name="notes"></a>メモ
* 前の例では、2 つのリージョンでデータベース アカウントを作成します。 単一リージョン (書き込みリージョンとして指定されており、フェールオーバー優先度の値が 0 であるもの) または 3 つ以上のリージョンでデータベース アカウントを作成することも可能です。 詳細については、[複数リージョンのデータベース アカウント][scaling-globally]に関する記事を参照してください。
* 場所は、DocumentDB が一般公開されているリージョンである必要があります。 最新のリージョン一覧については、 [Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。

## <a name="a-idupdate-documentdb-account-powershella-update-a-documentdb-database-account"></a><a id="update-documentdb-account-powershell"></a> DocumentDB データベース アカウントの更新

このコマンドでは、DocumentDB データベース アカウントのプロパティを更新することができます。 これには、一貫性ポリシーと、データベース アカウントが存在する場所が含まれます。

> [!NOTE]
> このコマンドでは、リージョンの追加および削除が可能ですが、フェールオーバー優先度を変更することはできません。 フェールオーバー優先度の変更方法については、[こちら](#modify-failover-priority-powershell)を参照してください。

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -PropertyObject $DocumentDBProperties
    
* `<write-region-location>` データベース アカウントの書き込みリージョンの場所の名前。 この場所のフェールオーバー優先度の値は 0 である必要があります。 書き込みリージョンは、データベース アカウントごとに&1; つである必要があります。
* `<read-region-location>` データベース アカウントの読み取りリージョンの場所の名前。 この場所のフェールオーバー優先度の値は 0 よりも大きい値である必要があります。 読み取りリージョンは、データベース アカウントごとに複数あってもかまいません。
* `<default-consistency-level>` DocumentDB アカウントの既定の一貫性レベル。 詳細については、「[DocumentDB の一貫性レベル](documentdb-consistency-levels.md)」を参照してください。
* `<ip-range-filter>` 特定のデータベース アカウントのクライアント IP の許可リストとして追加する一連の IP アドレスまたは IP アドレス範囲を CIDR 形式で指定する必要があります。 IP アドレス/範囲は、コンマで区切る必要があり、スペースを含めることはできません。 詳細については、「[DocumentDB のファイアウォール サポート](documentdb-firewall-support.md)」を参照してください。
* `<max-interval>` 有界整合性制約の一貫性と併用すると、この値は許容される古さの期間を秒単位で示します。 この値の許容範囲は、1 ～ 100 です。
* `<max-staleness-prefix>` 有界整合性制約の一貫性と併用すると、この値は許容される古い要求の数を示します。 この値の許容範囲は、1 ～ 2,147,483,647 です。
* `<resource-group-name>` 新しい DocumentDB データベース アカウントが属する [Azure リソース グループ][azure-resource-groups]の名前。
* `<resource-group-location>` 新しい DocumentDB データベース アカウントが属する Azure リソース グループの場所。
* `<database-account-name>` 更新する DocumentDB データベース アカウントの名前。

例: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -PropertyObject $DocumentDBProperties

## <a name="a-iddelete-documentdb-account-powershella-delete-a-documentdb-database-account"></a><a id="delete-documentdb-account-powershell"></a> DocumentDB データベース アカウントの削除

このコマンドでは、既存の DocumentDB データベース アカウントを削除することができます。

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` 新しい DocumentDB データベース アカウントが属する [Azure リソース グループ][azure-resource-groups]の名前。
* `<database-account-name>` 削除する DocumentDB データベース アカウントの名前。

例:

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a name="a-idget-documentdb-properties-powershella-get-properties-of-a-documentdb-database-account"></a><a id="get-documentdb-properties-powershell"></a> DocumentDB データベース アカウントのプロパティの取得

このコマンドでは、既存の DocumentDB データベース アカウントのプロパティを取得することができます。

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` 新しい DocumentDB データベース アカウントが属する [Azure リソース グループ][azure-resource-groups]の名前。
* `<database-account-name>` DocumentDB データベース アカウントの名前。

例:

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a name="a-idupdate-tags-powershella-update-tags-of-a-documentdb-database-account"></a><a id="update-tags-powershell"></a> DocumentDB データベース アカウントのタグの更新

次の例は、DocumentDB データベース アカウントで使用する [Azure のリソース タグ][azure-resource-tags]の設定方法を示しています。

> [!NOTE]
> このコマンドは、対応するパラメーターと `-Tags` フラグを追加することで、作成または更新のコマンドと組み合わせることができます。

例:

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a name="a-idlist-account-keys-powershella-list-account-keys"></a><a id="list-account-keys-powershell"></a> アカウント キーの一覧表示

DocumentDB アカウントを作成すると、2 つのマスター アクセス キーが生成され、DocumentDB アカウントにアクセスする際の認証に使用できます。 2 つのアクセス キーが提供されるので、DocumentDB アカウントを中断することなくキーを再生成できます。 読み取り専用操作を認証するための読み取り専用キーも使用できます。 2 つの読み取り/書き込みキー (プライマリおよびセカンダリ) と、2 つの読み取り専用キー (プライマリおよびセカンダリ) が存在します。

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` 新しい DocumentDB データベース アカウントが属する [Azure リソース グループ][azure-resource-groups]の名前。
* `<database-account-name>` DocumentDB データベース アカウントの名前。

例:

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a name="a-idregenerate-account-key-powershella-regenerate-account-key"></a><a id="regenerate-account-key-powershell"></a> アカウント キーの再生成

接続のセキュリティを高めるために、DocumentDB アカウントのアクセス キーは定期的に変更する必要があります。 片方のアクセス キーで DocumentDB アカウントに接続したまま、もう片方のアクセス キーを再生成できるように、アクセス キーは&2; つ割り当てられます。

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` 新しい DocumentDB データベース アカウントが属する [Azure リソース グループ][azure-resource-groups]の名前。
* `<database-account-name>` DocumentDB データベース アカウントの名前。
* `<key-kind>` "Primary"、"Secondary"、"PrimaryReadonly"、"SecondaryReadonly" の&4; 種類のキーのいずれかを再生成します。

例:

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a name="a-idmodify-failover-priority-powershella-modify-failover-priority-of-a-documentdb-database-account"></a><a id="modify-failover-priority-powershell"></a> DocumentDB データベース アカウントのフェールオーバー優先度の変更

複数リージョンのデータベース アカウントでは、DocumentDB データベース アカウントが存在するさまざまなリージョンのフェールオーバー優先度を変更できます。 DocumentDB データベース アカウントでのフェールオーバーの詳細については、「[DocumentDB を使用したデータのグローバル分散][distribute-data-globally]」を参照してください。

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` データベース アカウントの書き込みリージョンの場所の名前。 この場所のフェールオーバー優先度の値は 0 である必要があります。 書き込みリージョンは、データベース アカウントごとに&1; つである必要があります。
* `<read-region-location>` データベース アカウントの読み取りリージョンの場所の名前。 この場所のフェールオーバー優先度の値は 0 よりも大きい値である必要があります。 読み取りリージョンは、データベース アカウントごとに複数あってもかまいません。
* `<resource-group-name>` 新しい DocumentDB データベース アカウントが属する [Azure リソース グループ][azure-resource-groups]の名前。
* `<database-account-name>` DocumentDB データベース アカウントの名前。

例:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/en-us/azure/powershell-install-configure
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[distribute-data-globally]: https://docs.microsoft.com/en-us/azure/documentdb/documentdb-distribute-data-globally
[azure-resource-groups]: https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/en-us/rest/api/documentdbresourceprovider/
