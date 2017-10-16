---
title: "Azure Portal で Azure DNS の使用を開始する | Microsoft Docs"
description: "Azure DNS で、DNS ゾーンとレコードを作成する方法について説明します。 Azure Portal を使用して最初の DNS ゾーンとレコードを作成して管理するためのステップ バイ ステップ ガイドです。"
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
ms.openlocfilehash: 93b24e3d9fbb3fbb3ea995271fd63d1e82eb9c9e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>Azure Portal で Azure DNS の使用を開始する

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

この記事では、Azure Portal を使用して最初の DNS ゾーンとレコードを作成する手順について説明します。 これらの手順は、Azure PowerShell またはクロスプラットフォームの Azure CLI を使用して実行することもできます。

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成する必要があります。 ドメインの DNS レコードはすべて、この DNS ゾーン内に作成されます。 最後に、DNS ゾーンをインターネットに公開するには、ドメインのネーム サーバーを構成する必要があります。 以下では、これらの各手順について説明します。

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

1. Azure Portal にサインインします。
2. ハブ メニューの **[新規]、[ネットワーク]** を順にクリックし、**[DNS ゾーン]** をクリックして [DNS ゾーンの作成] ブレードを開きます。

    ![[DNS ゾーン]](./media/dns-getstarted-portal/openzone650.png)

4. **[DNS ゾーンの作成]** ブレードで次の値を入力してから、**[作成]** をクリックします。


   | **設定** | **値** | **詳細** |
   |---|---|---|
   |**名前**|contoso.com|DNS ゾーンの名前です。|
   |**サブスクリプション**|<該当するサブスクリプション>|DNS ゾーンを作成するサブスクリプションを選択します。|
   |**[リソース グループ]**|**[新規作成]**: contosoDNSRG|リソース グループを作成します。 選択したサブスクリプション内で一意となるリソース グループ名を使用してください。 リソース グループについて詳しくは、[Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) の概要に関する記事をご覧ください。|
   |**場所**|米国西部||

> [!NOTE]
> リソース グループの設定はリソース グループの場所を指定するものであり、DNS ゾーンには影響しません。 DNS ゾーンの場所は常に "グローバル" であり、それは表示されません。

## <a name="create-a-dns-record"></a>DNS レコードの作成

次の例では、新しい "A" レコードを作成する手順を説明します。 他のレコードの種類と、既存のレコードの変更については、「[Azure Portal を使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-portal.md)」を参照してください。 

1. DNS ゾーンが作成されたら、Azure Portal の **[お気に入り]** ウィンドウで **[すべてのリソース]** をクリックします。 [すべてのリソース] ブレードで **contoso.com** DNS ゾーンをクリックします。 選択したサブスクリプションに既存のリソースがいくつもある場合は、**[名前でフィルター]** ボックスに「**contoso.com**」と入力すると、 目的の DNS ゾーンがすぐに見つかります。

1. **[DNS ゾーン]** ブレードの上部にある **[+ レコード セット]** を選択して **[レコード セットの追加]** ブレードを開きます。

1. **[レコード セットの追加]** ブレードで次の値を入力して、**[OK]** をクリックします。 この例では、A レコードを作成します。

   |**設定** | **値** | **詳細** |
   |---|---|---|
   |**名前**|www|レコードの名前です。|
   |**型**|A| 作成する DNS レコードの種類です (指定できる値は A、AAAA、CNAME、MX、NS、SRV、TXT、PTR)。  レコードの種類の詳細については、「[DNS ゾーンとレコードの概要](dns-zones-records.md)」を参照してください。|
   |**TTL**|1|DNS 要求の有効期限です。|
   |**TTL の単位**|時間|TTL 値の時間の単位です。|
   |**IP アドレス**|ipAddressValue| この値は、DNS レコードが解決する IP アドレスです。|

## <a name="view-records"></a>レコードの表示

[DNS ゾーン] ブレードの下部に、DNS ゾーンのレコードが表示されます。 ここには、すべてのゾーンで作成される既定の DNS および SOA レコードと、ご自身で作成した新しいレコードが表示されています。

![ゾーン](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>ネーム サーバーの更新

DNS ゾーンとレコードを正しく設定したら、Azure DNS ネーム サーバーを使用するようにドメイン名を構成する必要があります。 これにより、インターネット上の他のユーザーが DNS レコードを検索できるようになります。

ゾーンのネーム サーバーは、Azure Portal で次のように示されます。

![ゾーン](./media/dns-getstarted-portal/viewzonens500.png)

このネーム サーバーは、ドメイン名レジストラー (ドメイン名を購入した場所) で構成する必要があります。 レジストラーにより、ドメインのネーム サーバーを設定するオプションが提供されます。 詳細については、「[Azure DNS へのドメインの委任](dns-domain-delegation.md)」を参照してください。

## <a name="delete-all-resources"></a>すべてのリソースの削除

この記事で作成したリソースをすべて削除するには、次の手順を実行します。

1. Azure Portal の **[お気に入り]** ウィンドウで **[すべてのリソース]** をクリックします。 [すべてのリソース] ブレードで **MyResourceGroup** リソース グループをクリックします。 選択したサブスクリプションに既存のリソースがいくつもある場合は、**[名前でフィルター]** ボックスに「**MyResourceGroup**」と入力すると 目的のリソース グループがすぐに見つかります。
1. **MyResourceGroup** ブレードで **[削除]** ボタンをクリックします。
1. 削除の意図を確認するために、リソース グループの名前を入力するよう求められます。 **[削除]** をクリックし、リソース グループの名前に「*MyResourceGroup*」と入力して、**[削除]** をクリックします。 リソース グループを削除すると、そこに含まれているリソースもすべて削除されます。削除する前に、リソース グループの内容を必ず確認してください。 まずリソース グループに含まれているすべてのリソースが削除された後、リソース グループそのものが削除されます。 このプロセスには数分かかります。


## <a name="next-steps"></a>次のステップ

Azure DNS の詳細については、「[Azure DNS の概要](dns-overview.md)」を参照してください。

Azure DNS での DNS レコードの管理の詳細については、「[Azure Portal を使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-portal.md)」を参照してください。

