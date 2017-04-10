---
title: "Azure CLI 1.0 で Azure DNS の使用を開始する | Microsoft Docs"
description: "Azure DNS で、DNS ゾーンとレコードを作成する方法について説明します。 Azure CLI 1.0 を使用して最初の DNS ゾーンとレコードを作成して管理するためのステップ バイ ステップ ガイドです。"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 75ddb1ff6dde4a83e775b54c15c86b18d2b1cf7a
ms.lasthandoff: 03/11/2017

---

# <a name="get-started-with-azure-dns-using-azure-cli-10"></a>Azure CLI 1.0 で Azure DNS の使用を開始する

> [!div class="op_single_selector"]
> * [Azure ポータル](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

この記事では、Windows、Mac、Linux で使用できるクロスプラットフォーム Azure CLI 1.0 を使用して、最初の DNS ゾーンとレコードを作成する手順について説明します。 これらの手順は、Azure Portal または Azure PowerShell を使用して実行することもできます。

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成する必要があります。 ドメインの DNS レコードはすべて、この DNS ゾーン内に作成されます。 最後に、DNS ゾーンをインターネットに公開するには、ドメインのネーム サーバーを構成する必要があります。 ここでは、その手順について説明します。

以降の手順は、Azure CLI 1.0 がインストール済みで、既にサインインしていることを前提としています。 詳細については、[Azure CLI 1.0 を使用して DNS ゾーンを管理する方法](dns-operations-dnszones-cli-nodejs.md)に関するページをご覧ください。


## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

DNS ゾーンは、`azure network dns zone create` コマンドを使用して作成します。 このコマンドのヘルプを表示するには、「`azure network dns zone create -h`」と入力します。

次の例では、*MyResourceGroup* というリソース グループに *contoso.com* という DNS ゾーンを作成します。 この例の値を実際の値に置き換えて、DNS ゾーンを作成できます。

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```


## <a name="create-a-dns-record"></a>DNS レコードの作成

DNS レコードを作成するには、`azure network dns record-set add-record` コマンドを使用します。 `azure network dns record-set add-record -h` を使用すると、ヘルプが表示されます。

下の例では、リソース グループ "MyResourceGroup" で DNS ゾーン "contoso.com" に相対名 "www" を持つレコードを作成します。 レコード セットの完全修飾名は、"www.contoso.com" になります。 また、レコードの種類は "A"、IP アドレスは "1.2.3.4"、既定の TTL として 3,600 秒 (1 時間) が使用されています。

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

その他のレコードの種類、複数のレコードを持つレコード セット、代替 TTL 値、既存のレコードの変更については、[Azure CLI 1.0 を使用した DNS レコードおよびレコード セットの管理](dns-operations-recordsets-cli-nodejs.md)に関するページをご覧ください。


## <a name="view-records"></a>レコードの表示

ゾーンで DNS レコードを表示するには、次を使用します。

```azurecli
azure network dns record-set list MyResourceGroup contoso.com
```


## <a name="update-name-servers"></a>ネーム サーバーの更新

DNS ゾーンとレコードを正しく設定したら、Azure DNS ネーム サーバーを使用するようにドメイン名を構成する必要があります。 これにより、インターネット上の他のユーザーが DNS レコードを検索できるようになります。

ゾーンのネーム サーバーを指定するには、`azure network dns zone show` コマンドを使用します。

```azurecli
azure network dns zone show MyResourceGroup contoso.com

info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 3
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            :
info:    network dns zone show command OK
```

このネーム サーバーは、ドメイン名レジストラー (ドメイン名を購入した場所) で構成する必要があります。 レジストラーにより、ドメインのネーム サーバーを設定するオプションが提供されます。 詳細については、「[Azure DNS へのドメインの委任](dns-domain-delegation.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

Azure DNS の詳細については、「[Azure DNS の概要](dns-overview.md)」を参照してください。

Azure DNS での DNS ゾーンの管理の詳細については、[Azure CLI 1.0 を使用した Azure DNS での DNS ゾーンの管理](dns-operations-dnszones-cli-nodejs.md)に関するページをご覧ください。

Azure DNS での DNS レコードの管理の詳細については、[Azure CLI 1.0 を使用した Azure DNS での DNS レコードおよびレコード セットの管理](dns-operations-recordsets-cli-nodejs.md)に関するページをご覧ください。


