---
title: インクルード ファイル
titleSuffix: Azure
description: インクルード ファイル
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: e2804c8f9b1af89ac0ea86ec14136df66d900060
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "83846164"
---
1. **[リソースの作成]**  >  **[すべて表示]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの検索](../media/setup-seeall.png)

1. 検索ボックスで **[ピアリング]** を検索し、キーボードの **Enter** キーを押します。 結果から、 **[ピアリング]** リソースを選択します。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの起動](../media/setup-launch.png)

1. **ピアリング** を起動したら、ページを確認して詳細を理解します。 準備ができたら、 **[作成]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![ピアリングを作成する](../media/setup-create.png)

1. **[ピアリングの作成]** ページの **[基本]** タブで、次のようにボックスに入力します。

    > [!div class="mx-imgBorder"]
    > ![ピアリングの [基本] タブ](../media/setup-basics-tab.png)

    * Azure **サブスクリプション** を選択します。
    * **[リソース グループ]** では、ドロップダウンから既存のリソース グループを選択するか、 **[新規作成]** を選択して新しいグループを作成します。 この例では、新しいリソース グループを作成します。
    * **[名前]** は、リソース名に対応し、任意の名前を選択できます。
    * 既存のリソース グループを選択した場合、 **[リージョン]** は自動的に選択されます。 新しいリソース グループの作成を選択した場合は、リソースを配置する Azure リージョンも選択する必要があります。

        > [!NOTE]
        > リソース グループを配置するリージョンは、Microsoft とのピアリングを作成する場所とは関係ありません。 ただし、最も近い Azure リージョンにリソース グループを配置して、使用するピアリング リソースをそこで整理することをお勧めします。 たとえば、Ashburn でのピアリングの場合、米国東部または米国東部 2 にリソース グループを作成できます。

    * **[ピア ASN]** ボックスで、使用する ASN を選択します。

        > [!IMPORTANT]
        > * 選択できるのは、ピアリング要求を送信する前に ValidationState が "Approved" になっている ASN だけです。 PeerAsn 要求を送信した直後であれば、ASN の関連付けが承認されるまで 12 時間程度かかります。 選択した ASN が検証待ちの場合、エラー メッセージが表示されます。 
        > * 選択する必要がある ASN が表示されない場合は、適切なサブスクリプションを選択しているかどうかを確認します。 選択している場合は、「[ピア ASN を Azure サブスクリプションに関連付ける](../howto-subscription-association-portal.md)」を参照して、PeerAsn を既に作成したかどうかを確認します。

        > [!div class="mx-imgBorder"]
        > ![入力済みのピアリングの [基本] タブ](../media/setup-direct-basics-filled-tab.png)

    * **[次へ :構成]** をクリックして、続行します。
