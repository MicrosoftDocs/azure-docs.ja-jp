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
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 0fee02286f6d5dd4614a933590cdab43cc69237f
ms.lasthandoff: 03/11/2017

---

# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>Azure Portal で Azure DNS の使用を開始する

> [!div class="op_single_selector"]
> * [Azure ポータル](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

この記事では、Azure Portal を使用して最初の DNS ゾーンとレコードを作成する手順について説明します。 これらの手順は、Azure PowerShell またはクロスプラットフォームの Azure CLI を使用して実行することもできます。

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成する必要があります。 ドメインの DNS レコードはすべて、この DNS ゾーン内に作成されます。 最後に、DNS ゾーンをインターネットに公開するには、ドメインのネーム サーバーを構成する必要があります。 ここでは、その手順について説明します。

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

1. Azure ポータルにサインインします。
2. ハブ メニューの **[新規]、[ネットワーク]** を順にクリックし、**[DNS ゾーン]** をクリックして [DNS ゾーンの作成] ブレードを開きます。

    ![[DNS ゾーン]](./media/dns-getstarted-portal/openzone650.png)

4. **[DNS ゾーンの作成]** ブレードで、DNS ゾーンの名前を入力します。 たとえば、 *contoso.com*などのドメインです。
 
    ![ゾーンの作成](./media/dns-getstarted-portal/newzone250.png)

5. 次に、使用するリソース グループを指定します。 新しいリソース グループを作成することも、既存のリソース グループを選択することもできます。 新しいリソース グループを作成する場合は、**[場所]** ドロップダウンを使用して、リソース グループの場所を指定します。 これはリソース グループの場所を指定する設定であり、DNS ゾーンには影響しないことにご注意ください。 DNS ゾーンの場所は常に "グローバル" であり、それは表示されません。

6. ダッシュボードで新しいゾーンをすぐに見つけられるようにするには、 **[ダッシュボードにピン留めする]** チェックボックスをオンのままにします。 **[Create]**をクリックします。

    ![[ダッシュボードにピン留めする]](./media/dns-getstarted-portal/pindashboard150.png)

7. [作成] をクリックした後、ダッシュボードを見ると、新しいゾーンが構成されていることがわかります。

    ![作成](./media/dns-getstarted-portal/creating150.png)

8. 新しいゾーンが作成されると、ダッシュボードに新しいゾーンのブレードが開きます。


## <a name="create-a-dns-record"></a>DNS レコードの作成

次の例では、新しい "A" レコードを作成する手順を説明します。 他のレコードの種類と、既存のレコードの変更については、「[Azure ポータルを使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-portal.md)」を参照してください。 


1. **[DNS ゾーン]** ブレードの上部にある **[+ レコード セット]** を選択して **[レコード セットの追加]** ブレードを開きます。

    ![新しいレコード セット](./media/dns-getstarted-portal/newrecordset500.png)

4. **[レコード セットの追加]** ブレードで、レコード セットの名前を入力します (例: "**www**")。

    ![[レコード セットの追加]](./media/dns-getstarted-portal/addrecordset500.png)

5. 作成するレコードの種類を選択します。 この例では、**[A]** を選択します。
6. **TTL**を設定します。 既定の有効期間は&1; 時間です。
7. レコードの IP アドレスを追加します。
8. ブレード下部の **[OK]** を選択すると、DNS レコードが作成されます。


## <a name="view-records"></a>レコードの表示

[DNS ゾーン] ブレードの下部に、DNS ゾーンのレコードが表示されます。 ここには、すべてのゾーンで作成される既定の DNS および SOA レコードと、ご自身で作成した新しいレコードが表示されています。

![ゾーン](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>ネーム サーバーの更新

DNS ゾーンとレコードを正しく設定したら、Azure DNS ネーム サーバーを使用するようにドメイン名を構成する必要があります。 これにより、インターネット上の他のユーザーが DNS レコードを検索できるようになります。

ゾーンのネーム サーバーは、Azure Portal で次のように示されます。

![ゾーン](./media/dns-getstarted-portal/viewzonens500.png)

このネーム サーバーは、ドメイン名レジストラー (ドメイン名を購入した場所) で構成する必要があります。 レジストラーにより、ドメインのネーム サーバーを設定するオプションが提供されます。 詳細については、「[Azure DNS へのドメインの委任](dns-domain-delegation.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

Azure DNS の詳細については、「[Azure DNS の概要](dns-overview.md)」を参照してください。

Azure DNS での DNS レコードの管理の詳細については、「[Azure ポータルを使用した DNS レコードとレコード セットの管理](dns-operations-recordsets-portal.md)」を参照してください。


