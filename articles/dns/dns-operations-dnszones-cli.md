---
title: "Azure DNS での DNS ゾーンの管理 - Azure CLI 2.0 | Microsoft Docs"
description: "Azure CLI 2.0 を使用して DNS ゾーンを管理できます。 この記事では、Azure DNS で DNS ゾーンを更新、削除、および作成する方法について説明します。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: ca6347e079c0e85d1e9583f77b2e43632bedb206
ms.lasthandoff: 03/01/2017

---

# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli-20"></a>Azure CLI 2.0 を使用して Azure DNS で DNS ゾーンを管理する方法

> [!div class="op_single_selector"]
> * [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

このガイドでは、Windows、Mac、および Linux で使用できるクロス プラットフォームの Azure CLI を使用して、DNS ゾーンを管理する方法について説明します。 DNS ゾーンは、[Azure PowerShell](dns-operations-dnszones.md) または Azure Portal を使用して管理することもできます。

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン

次のいずれかの CLI バージョンを使用してタスクを完了できます。

* [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI。
* [Azure CLI 2.0](dns-operations-dnszones-cli.md) - Resource Manager デプロイ モデル用の次世代 CLI。

## <a name="introduction"></a>はじめに

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Azure DNS 向け Azure CLI 2.0 の設定

### <a name="before-you-begin"></a>開始する前に

構成を開始する前に、以下がそろっていることを確認します。

* Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。

* Windows、Linux、または MAC 用の最新バージョンの Azure CLI 2.0 をインストールしてください。 詳しくは、「[Azure CLI 2.0 のインストール](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)」をご覧ください。

### <a name="sign-in-to-your-azure-account"></a>Azure アカウントへのサインイン

コンソール ウィンドウを開き、資格情報を使用して認証を行います。 詳細については、「Azure CLI から Azure へのログイン」を参照してください

```
az login
```

### <a name="select-the-subscription"></a>サブスクリプションの選択

アカウントのサブスクリプションを確認します。

```
az account list
```

### <a name="choose-which-of-your-azure-subscriptions-to-use"></a>使用する Azure サブスクリプションを選択します。

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>リソース グループの作成

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。 ただし、すべての DNS リソースはグローバルであり、リージョンの違いがないため、リソース グループの場所を選択しても、Azure DNS には影響しません。

既存のリソース グループを使用する場合は、この手順をスキップしてください。

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>ヘルプの表示

Azure DNS に関連するすべての CLI 2.0 コマンドは `az network dns` で始まります。 `--help` オプション (短縮形 `-h`) を使用すると、各コマンドのヘルプを利用できます。  For example:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

DNS ゾーンは、`az network dns zone create` コマンドを使用して作成します。 `az network dns zone create -h` を使用すると、ヘルプが表示されます。

次の例では、*MyResourceGroup* というリソース グループに *contoso.com* という DNS ゾーンを作成します。

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>タグのある DNS ゾーンを作成するには

次の例では、`--tags` パラメーター (短縮形 `-t`) を使用して、*project = demo* と *env = test* の&2; つ [Azure Resource Manager タグ](dns-zones-records.md#tags)を含む DNS ゾーンを作成する方法を示します。

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>DNS ゾーンの取得

DNS ゾーンを取得するには、 `az network dns zone show`を使用します。 `az network dns zone show --help` を使用すると、ヘルプが表示されます。

次の例では、リソース グループ *MyResourceGroup* から DNS ゾーン *contoso.com* とその関連データを返します。 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

次の例は応答です。

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-04.azure-dns.com.",
    "ns2-04.azure-dns.net.",
    "ns3-04.azure-dns.org.",
    "ns4-04.azure-dns.info."
  ],
  "numberOfRecordSets": 4,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

`az network dns zone show` では DNS レコードが返されないことにご注意ください。 DNS レコードを一覧表示するには、`az network dns record-set list` を使用します。


## <a name="list-dns-zones"></a>DNS ゾーンの一覧表示

DNS ゾーンを列挙するには、`az network dns zone list` を使用します。 `az network dns zone list --help` を使用すると、ヘルプが表示されます。

リソース グループを指定すると、リソース グループ内のゾーンのみが一覧表示されます。

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

リソース グループを省略すると、サブスクリプション内のすべてのゾーンが一覧表示されます。

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>DNS ゾーンの更新

DNS ゾーンのリソースへの変更は、 `az network dns zone update`を使用して行うことができます。 `az network dns zone update --help` を使用すると、ヘルプが表示されます。

このコマンドによって、ゾーン内の DNS レコード セットが更新されることはありません (「 [DNS レコードの管理方法](dns-operations-recordsets-cli.md)」を参照)。 この操作は、ゾーンのリソース自体のプロパティを更新するためだけに使用します。 現時点では、これらのプロパティは、ゾーンのリソースの [Azure Resource Manager の"タグ"](dns-zones-records.md#tags) に限定されています。

次の例では、DNS ゾーンのタグを更新する方法を示します。 既存のタグは、指定された値に置き換えられます。

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>DNS ゾーンの削除

DNS ゾーンは、`az network dns zone delete` を使用して削除できます。 `az network dns zone delete --help` を使用すると、ヘルプが表示されます。

> [!NOTE]
> DNS ゾーンを削除すると、ゾーン内の DNS レコードもすべて削除されます。 削除操作は元に戻すことができません。 DNS ゾーンを使用している場合、ゾーンを削除すると、ゾーンを使用するサービスは機能しなくなります。
>
>ゾーンを誤って削除しないようにするには、「[DNS ゾーンとレコードを保護する](dns-protect-zones-recordsets.md)」をご覧ください。

このコマンドは、確認を求めるプロンプトを表示します。 オプションの `--yes` スイッチはこのプロンプトを表示しません。

次の例は、リソース グループ *MyResourceGroup* から *contoso.com* ゾーンを削除する方法を示します。

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>次のステップ

DNS ゾーンでレコード セットとレコードを管理する方法については[こちら](dns-getstarted-create-recordset-cli.md)をご覧ください。

Azure DNS にドメインを委任する方法については[こちら](dns-domain-delegation.md)をご覧ください。


