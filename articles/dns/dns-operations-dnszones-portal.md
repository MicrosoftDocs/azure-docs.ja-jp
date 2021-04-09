---
title: Azure DNS での DNS ゾーンの管理 - Azure Portal | Microsoft Docs
description: Azure Portal を使って DNS ゾーンを管理できます。 この記事では、Azure DNS で DNS ゾーンを更新、削除、および作成する方法について説明します
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: rohink
ms.openlocfilehash: 51e17812e1116f1e625685d5a818c18e25cdb2da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965801"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Azure Portal で DNS ゾーンを管理する方法

> [!div class="op_single_selector"]
> * [ポータル](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure クラシック CLI](./dns-operations-dnszones-cli.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

この記事では、Azure Portal を使って DNS ゾーンを管理する方法について説明します。 DNS ゾーンは、クロスプラットフォームの [Azure CLI](dns-operations-dnszones-cli.md) または Azure [PowerShell](dns-operations-dnszones.md) を使って、管理することもできます。

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

1. Azure portal にサインインする
2. [ハブ] メニューで、**[リソースの作成] > [ネットワーキング] > [DNS ゾーン]** に移動して **[DNS ゾーンの作成]** ブレードを開きます。

    ![[DNS ゾーン]](./media/dns-operations-dnszones-portal/openzone650.png)

4. **[DNS ゾーンの作成]** ブレードで次の値を入力してから、**[作成]** をクリックします。


   | **設定** | **Value** | **詳細** |
   |---|---|---|
   |**名前**|contoso.com|DNS ゾーンの名前です。|
   |**[サブスクリプション]**|<該当するサブスクリプション>|DNS ゾーンを作成するサブスクリプションを選択します。|
   |**リソース グループ**|**[新規作成]**: contosoDNSRG|リソース グループを作成します。 選択したサブスクリプション内で一意となるリソース グループ名を使用してください。 リソース グループについて詳しくは、[Resource Manager](../azure-resource-manager/management/overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) の概要に関する記事をご覧ください。|
   |**場所**|米国西部||

> [!NOTE]
> リソース グループの設定はリソース グループの場所を指定するものであり、DNS ゾーンには影響しません。 DNS ゾーンの場所は常に "グローバル" であり、それは表示されません。

## <a name="list-dns-zones"></a>DNS ゾーンの一覧表示

Azure Portal で、 **[その他のサービス]**  >  **[ネットワーク]**  >  **[DNS ゾーン]** の順に移動します。 各 DNS ゾーンは独自のリソースであり、レコード セットやネーム サーバーの数などの情報をこのビューから表示できます。 **[NAME SERVERS] (ネーム サーバー)** 列は、既定のビューに含まれていません。 それを追加するには、**[列]** をクリックし、**[Name servers] (ネーム サーバー)** を選択して **[完了]** をクリックします。

![DNS ゾーンの一覧表示](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>DNS ゾーンの削除

Portal で DNS ゾーンに移動します。 **[DNS ゾーン]** ブレードで、**[ゾーンを削除する]** をクリックします。 その後、DNS ゾーンを削除することを確認するよう求められます。 DNS ゾーンを削除すると、そのゾーンに含まれているレコードもすべて削除されます。

## <a name="next-steps"></a>次のステップ

「[Azure Portal で Azure DNS の使用を開始する](dns-getstarted-portal.md)」で、DNS ゾーンとレコードの使用方法を学習してください。