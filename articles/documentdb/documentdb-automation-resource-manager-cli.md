---
title: DocumentDB Automation - Azure CLI 2.0 | Microsoft Docs
description: "Azure CLI 2.0 を使用して DocumentDB データベース アカウントを管理します。 DocumentDB は、JSON データ用のクラウドベースの NoSQL データベースです。"
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 6158c27f-6b9a-404e-a234-b5d48c4a5b29
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: b286a93d7cc5f962f969e877b2f487e56cbb1a95
ms.lasthandoff: 03/30/2017


---
# <a name="automate-azure-documentdb-account-management-using-azure-cli-20"></a>Azure CLI 2.0 を使用した Azure DocumentDB アカウントの管理の自動化
> [!div class="op_single_selector"]
> * [Azure ポータル](documentdb-create-account.md)
> * [Azure CLI 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Azure CLI 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure Powershell](documentdb-manage-account-with-powershell.md)

次のガイドでは、Azure CLI 2.0 の DocumentDB プレビュー コマンドを使用して、DocumentDB データベース アカウントの管理を自動化するためのコマンドについて説明します。 また、[複数リージョンのデータベース アカウント][scaling-globally]でアカウント キーとフェールオーバー優先度を管理するコマンドについても説明します。 データベース アカウントを更新すると、一貫性ポリシーの変更と、リージョンの追加または削除を行うことができます。 DocumentDB データベース アカウントのクロスプラットフォーム管理には、[Azure Powershell](documentdb-manage-account-with-powershell.md)、[リソースプロバイダーの REST API][rp-rest-api]、[Azure Portal](documentdb-create-account.md) のいずれも使用可能です。

## <a name="getting-started"></a>使用の開始

[Azure CLI 2.0 のインストールと構成方法][install-az-cli2]に関するページの手順に従って、Azure CLI 2.0 で開発環境をセットアップしてください。

次のコマンドを実行して画面上の手順に従い、Azure アカウントにログインします。

    az login

[リソース グループ](../azure-resource-manager/resource-group-overview.md#resource-groups)が作成されていない場合は、リソース グループを作成します。

    az group create --name <resourcegroupname> --location <resourcegrouplocation>
    az group list

`<resourcegrouplocation>` は、DocumentDB が一般公開されているリージョンのいずれかである必要があります。 最新のリージョン一覧については、 [Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。

### <a name="notes"></a>メモ

* 使用できるすべてのコマンドの一覧を取得するには、'az documentdb -h' を実行するか、[リファレンス ページ][az-documentdb-ref]をご覧ください。
* 'az documentdb <command> -h' を実行すると、コマンドごとの必要なパラメーターとオプションのパラメータの詳細のリストを取得できます。

## <a id="create-documentdb-account-cli"></a> DocumentDB データベース アカウントの作成

このコマンドでは、DocumentDB データベース アカウントを作成できます。 新しいデータベース アカウントは、単一リージョンまたは[複数リージョン][scaling-globally]で、特定の[一貫性ポリシー](documentdb-consistency-levels.md)に基づいて構成します。 

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP
                                    address ranges in CIDR form to be included as the allowed list
                                    of client IPs for a given database account. IP addresses/ranges
                                    must be comma separated and must not contain any spaces.
                                    To enable portal access, include 104.42.195.92.
    --kind                        : The type of DocumentDB database account to create.  Allowed
                                    values: GlobalDocumentDB, MongoDB, Parse.  Default:
                                    GlobalDocumentDB.
    --locations                   : Space separated locations in 'regionName=failoverPriority'
                                    format. E.g "East US"=0 "West US"=1. Failover priority values
                                    are 0 for write regions and greater than 0 for read regions. A
                                    failover priority value must be unique and less than the total
                                    number of regions. Default: single region account in the
                                    location of the specified resource group.
    --max-interval                : When used with Bounded Staleness consistency, this value
                                    represents the time amount of staleness (in seconds) tolerated.
                                    Accepted range for this value is 1 - 100.  Default: 5.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value
                                    represents the number of stale requests tolerated. Accepted
                                    range for this value is 1 - 2,147,483,647.  Default: 100.
```

次に例を示します。 

    az documentdb create -g rg-test -n docdb-test
    az documentdb create -g rg-test -n docdb-test --kind MongoDB
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb create -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

### <a name="notes"></a>メモ
* 場所は、DocumentDB が一般公開されているリージョンである必要があります。 最新のリージョン一覧については、 [Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。

## <a id="update-documentdb-account-cli"></a> DocumentDB データベース アカウントの更新

このコマンドでは、DocumentDB データベース アカウントのプロパティを更新できます。 これには、一貫性ポリシーと、データベース アカウントが存在する場所が含まれます。

> [!NOTE]
> このコマンドでは、リージョンの追加および削除が可能ですが、フェールオーバー優先度を変更することはできません。 フェールオーバー優先度の変更方法については、[こちら](#modify-failover-priority-powershell)を参照してください。

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP address
                                    ranges in CIDR form to be included as the allowed list of client
                                    IPs for a given database account. IP addresses/ranges must be comma
                                    separated and must not contain any spaces.
    --locations                   : Space separated locations in 'regionName=failoverPriority' format.
                                    E.g "East US"=0. Failover priority values are 0 for write regions
                                    and greater than 0 for read regions. A failover priority value must
                                    be unique and less than the total number of regions.
    --max-interval                : When used with Bounded Staleness consistency, this value represents
                                    the time amount of staleness (in seconds) tolerated. Accepted range
                                    for this value is 1 - 100.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value represents
                                    the number of stale requests tolerated. Accepted range for this
                                    value is 1 - 2,147,483,647.
```

次に例を示します。 

    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb update -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb update -g rg-test -n docdb-test --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

## <a id="add-remove-region-documentdb-account-cli"></a> DocumentDB データベース アカウントからのリージョンの追加/削除

既存の DocumentDB データベース アカウントとの間でリージョンの追加または削除を実行するには、[update](#update-documentdb-account-cli) コマンドと `--locations` フラグを使用します。 次の例では、新しいアカウントを作成し、続いてそのアカウントにリージョンを追加したり、アカウントからリージョンを削除したりする方法を説明します。

例:

    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1
    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "North Europe"=1 "South Central US"=2


## <a id="delete-documentdb-account-cli"></a> DocumentDB データベース アカウントの削除

このコマンドでは、既存の DocumentDB データベース アカウントを削除できます。

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

例:

    az documentdb delete -g rg-test -n docdb-test

## <a id="get-documentdb-properties-cli"></a> DocumentDB データベース アカウントのプロパティの取得

このコマンドでは、既存の DocumentDB データベース アカウントのプロパティを取得できます。

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

例:

    az documentdb show -g rg-test -n docdb-test

## <a id="list-account-keys-cli"></a> アカウント キーの一覧表示

DocumentDB アカウントを作成すると、2 つのマスター アクセス キーが生成され、DocumentDB アカウントにアクセスする際の認証に使用できます。 2 つのアクセス キーが提供されるので、DocumentDB アカウントを中断することなくキーを再生成できます。 読み取り専用操作を認証するための読み取り専用キーも使用できます。 2 つの読み取り/書き込みキー (プライマリおよびセカンダリ) と、2 つの読み取り専用キー (プライマリおよびセカンダリ) が存在します。

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

例:

    az documentdb list-keys -g rg-test -n docdb-test

## <a id="regenerate-account-key-cli"></a> アカウント キーの再生成

接続のセキュリティを高めるために、DocumentDB アカウントのアクセス キーは定期的に変更する必要があります。 片方のアクセス キーで DocumentDB アカウントに接続したまま、もう片方のアクセス キーを再生成できるように、アクセス キーは 2 つ割り当てられます。

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --key-kind          [Required]: The access key to regenerate.  Allowed values: primary, primaryReadonly,
                                    secondary, secondaryReadonly.
```

例:

    az documentdb regenerate-key -g rg-test -n docdb-test --key-kind secondary

## <a id="modify-failover-priority-cli"></a> DocumentDB データベース アカウントのフェールオーバー優先度の変更

複数リージョンのデータベース アカウントでは、DocumentDB データベース アカウントが存在するさまざまなリージョンのフェールオーバー優先度を変更できます。 DocumentDB データベース アカウントでのフェールオーバーの詳細については、[DocumentDB を使用したデータのグローバル分散](documentdb-distribute-data-globally.md)に関する記事を参照してください。

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --failover-policies [Required]: Space separated failover policies in 'regionName=failoverPriority' format.
                                    E.g "East US"=0 "West US"=1.
```

例:

    az documentdb failover-priority-change "East US"=1 "West US"=0 "South Central US"=2

## <a name="next-steps"></a>次のステップ
DocumentDB アカウントを作成できたら、次の手順として DocumentDB データベースを作成します。 データベースを作成するには、次のいずれかを使用します。

* Azure Portal。[Azure Portal を使用した DocumentDB コレクションとデータベースの作成](documentdb-create-collection.md)に関する記事をご覧ください。
* GitHub の [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) リポジトリの [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) プロジェクトにある C# .NET のサンプル。
* [DocumentDB SDK](documentdb-sdk-dotnet.md)。 DocumentDB には、.NET、Java、Python、Node.js、および JavaScript API の SDK があります。

データベースを作成した後に、データベースに [1 つまたは複数のコレクションを追加](documentdb-create-collection.md)し、それらのコレクションに[ドキュメントを追加する](documentdb-view-json-document-explorer.md)必要があります。


コレクションにドキュメントを用意した後で、ポータルの[クエリ エクスプローラー](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)、またはいずれかの [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) を使用することで、[DocumentDB SQL](documentdb-sql-query.md) を使用してドキュメントに対して[クエリを実行](documentdb-sql-query.md#ExecutingSqlQueries)できます。

DocumentDB の詳細については、以下のリソースを参照してください。

* [DocumentDB のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
* [DocumentDB のリソース モデルと概念](documentdb-resources.md)


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[install-az-cli2]: https://docs.microsoft.com/en-us/cli/azure/install-az-cli2
[az-documentdb-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb
[az-documentdb-create-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb#create
[rp-rest-api]: https://docs.microsoft.com/en-us/rest/api/documentdbresourceprovider/

