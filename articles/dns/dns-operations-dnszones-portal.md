---
title: "Azure DNS での DNS ゾーンの管理 - Azure Portal | Microsoft Docs"
description: "Azure Portal を使って DNS ゾーンを管理できます。 この記事では、Azure DNS で DNS ゾーンを更新、削除、および作成する方法について説明します"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: gwallace
ms.openlocfilehash: 69a509612e6204fc93dd42bf2fe69cb165b5777c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Azure Portal で DNS ゾーンを管理する方法

> [!div class="op_single_selector"]
> * [ポータル](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)

この記事では、Azure Portal を使って DNS ゾーンを管理する方法について説明します。 DNS ゾーンは、クロスプラットフォームの [Azure CLI](dns-operations-dnszones-cli.md) または Azure [PowerShell](dns-operations-dnszones.md) を使って、管理することもできます。

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

1. Azure Portal にサインインします。
2. ハブ メニューの **[新規]、[ネットワーク]** を順にクリックし、**[DNS ゾーン]** をクリックして [DNS ゾーンの作成] ブレードを開きます。

    ![[DNS ゾーン]](./media/dns-operations-dnszones-portal/openzone650.png)

4. **[DNS ゾーンの作成]** ブレードで次の値を入力してから、**[作成]** をクリックします。


   | **設定** | **値** | **詳細** |
   |---|---|---|
   |**名前**|contoso.com|DNS ゾーンの名前です。|
   |**サブスクリプション**|<該当するサブスクリプション>|DNS ゾーンを作成するサブスクリプションを選択します。|
   |**[リソース グループ]**|**[新規作成]**: contosoDNSRG|リソース グループを作成します。 選択したサブスクリプション内で一意となるリソース グループ名を使用してください。 リソース グループについて詳しくは、[Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) の概要に関する記事をご覧ください。|
   |**場所**|米国西部||

> [!NOTE]
> リソース グループの設定はリソース グループの場所を指定するものであり、DNS ゾーンには影響しません。 DNS ゾーンの場所は常に "グローバル" であり、それは表示されません。

## <a name="list-dns-zones"></a>DNS ゾーンの一覧表示

Azure Portal で、**[その他のサービス]** > **[ネットワーク]** > **[DNS ゾーン]** の順に移動します。 各 DNS ゾーンは独自のリソースであり、レコード セットの数などの情報とネーム サーバーをこのビューから表示できます。 **[ネーム サーバー]** 列は既定のビューには含まれず、追加するには、**[列]** をクリックし、**[ネーム サーバー]** を選んで、**[完了]** をクリックします。

![DNS ゾーンの一覧表示](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>DNS ゾーンの削除

Portal で DNS ゾーンに移動します。 **[DNS ゾーン]** ブレードで、**[ゾーンを削除する]** をクリックします。 DNS ゾーンを削除する意思の確認を求められます。 DNS ゾーンを削除すると、そのゾーンに含まれるレコードもすべて削除されます。

## <a name="next-steps"></a>次のステップ

「[Azure Portal で Azure DNS の使用を開始する](dns-getstarted-portal.md)」で、DNS ゾーンとレコードの使用方法を学習してください。