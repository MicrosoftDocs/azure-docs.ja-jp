---
title: "Azure DNS での DNS ゾーンの管理 - Azure CLI 1.0 | Microsoft Docs"
description: "Azure CLI 1.0 を使用して DNS ゾーンを管理できます。 この記事では、Azure DNS で DNS ゾーンを更新、削除、および作成する方法について説明します。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 9b545937f3e375dfcef815a66263a57bd5042f69
ms.lasthandoff: 02/27/2017

---

# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli-10"></a>Azure CLI 1.0 を使用して Azure DNS で DNS ゾーンを管理する方法

> [!div class="op_single_selector"]
> * [Azure CLI](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

このガイドでは、Windows、Mac、および Linux で使用できるクロス プラットフォームの Azure CLI 1.0 を使用して、DNS ゾーンを管理する方法について説明します。 DNS ゾーンは、[Azure PowerShell](dns-operations-dnszones.md) または Azure Portal を使用して管理することもできます。

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン

次のいずれかの CLI バージョンを使用してタスクを完了できます。

* [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI。
* [Azure CLI 2.0](dns-operations-dnszones-cli.md) - Resource Manager デプロイ モデル用の次世代 CLI。

## <a name="introduction"></a>はじめに

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]

## <a name="getting-help"></a>ヘルプの表示

Azure DNS に関連するすべての CLI 1.0 コマンドは `azure network dns` で始まります。 `--help` オプション (短縮形 `-h`) を使用すると、各コマンドのヘルプを利用できます。  For example:

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

DNS ゾーンは、`azure network dns zone create` コマンドを使用して作成します。 `azure network dns zone create -h` を使用すると、ヘルプが表示されます。

次の例では、*MyResourceGroup* というリソース グループに *contoso.com* という DNS ゾーンを作成します。

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>タグのある DNS ゾーンを作成するには

次の例では、`--tags` パラメーター (短縮形 `-t`) を使用して、*project = demo* と *env = test* の&2; つ [Azure Resource Manager タグ](dns-zones-records.md#tags)を含む DNS ゾーンを作成する方法を示します。

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>DNS ゾーンの取得

DNS ゾーンを取得するには、 `azure network dns zone show`を使用します。 `azure network dns zone show -h` を使用すると、ヘルプが表示されます。

次の例では、リソース グループ *MyResourceGroup* から DNS ゾーン *contoso.com* とその関連データを返します。 

```azurecli
azure network dns zone show MyResourceGroup contoso.com
```

次の例は応答です。

```
info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/.../contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 2
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            : project=demo;env=test
info:    network dns zone show command OK
```

`azure network dns zone show` では DNS レコードが返されないことにご注意ください。 DNS レコードを一覧表示するには、`azure network dns record-set list` を使用します。


## <a name="list-dns-zones"></a>DNS ゾーンの一覧表示

DNS ゾーンを列挙するには、`azure network dns zone list` を使用します。 `azure network dns zone list -h` を使用すると、ヘルプが表示されます。

リソース グループを指定すると、リソース グループ内のゾーンのみが一覧表示されます。

```azurecli
azure network dns zone list MyResourceGroup
```

リソース グループを省略すると、サブスクリプション内のすべてのゾーンが一覧表示されます。

```azurecli
azure network dns zone list 
```

## <a name="update-a-dns-zone"></a>DNS ゾーンの更新

DNS ゾーンのリソースへの変更は、 `azure network dns zone set`を使用して行うことができます。 `azure network dns zone set -h` を使用すると、ヘルプが表示されます。

このコマンドによって、ゾーン内の DNS レコード セットが更新されることはありません (「 [DNS レコードの管理方法](dns-operations-recordsets-cli-nodejs.md)」を参照)。 この操作は、ゾーンのリソース自体のプロパティを更新するためだけに使用します。 現時点では、これらのプロパティは、ゾーンのリソースの [Azure Resource Manager の"タグ"](dns-zones-records.md#tags) に限定されています。

次の例では、DNS ゾーンのタグを更新する方法を示します。 既存のタグは、指定された値に置き換えられます。

```azurecli
azure network dns zone set MyResourceGroup contoso.com -t "team=support"
```

## <a name="delete-a-dns-zone"></a>DNS ゾーンの削除

DNS ゾーンは、`azure network dns zone delete` を使用して削除できます。 `azure network dns zone delete -h` を使用すると、ヘルプが表示されます。

> [!NOTE]
> DNS ゾーンを削除すると、ゾーン内の DNS レコードもすべて削除されます。 削除操作は元に戻すことができません。 DNS ゾーンを使用している場合、ゾーンを削除すると、ゾーンを使用するサービスは機能しなくなります。
>
>ゾーンを誤って削除しないようにするには、「[DNS ゾーンとレコードを保護する](dns-protect-zones-recordsets.md)」をご覧ください。

このコマンドは、確認を求めるプロンプトを表示します。 オプションの `--quiet` スイッチ (短縮形 `-q`) はこのプロンプトを表示しません。

次の例は、リソース グループ *MyResourceGroup* から *contoso.com* ゾーンを削除する方法を示します。

```azurecli
azure network dns zone delete MyResourceGroup contoso.com
```

## <a name="next-steps"></a>次のステップ

DNS ゾーンでレコード セットとレコードを管理する方法については[こちら](dns-getstarted-create-recordset-cli-nodejs.md)をご覧ください。

Azure DNS にドメインを委任する方法については[こちら](dns-domain-delegation.md)をご覧ください。


