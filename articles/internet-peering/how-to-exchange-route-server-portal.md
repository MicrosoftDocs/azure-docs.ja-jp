---
title: ポータルを使用した Exchange パートナーとルート サーバーのピアリング接続
titleSuffix: Azure
description: Azure portal を使用して、ルート サーバーとの Exchange ピアリングを作成または変更します
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 7e10cd91eadd338217845b1504e8e9160bccfc98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91537193"
---
# <a name="create-or-modify-an-exchange-peering-with-route-server-in-azure-portal"></a>Azure portal でルート サーバーとの Exchange ピアリングを作成または変更する

この記事では、Azure portal を使用して、ルート サーバーとの Microsoft Exchange ピアリングを作成する方法について説明します。 また、この記事では、リソースの状態確認、更新、または削除およびプロビジョニング解除の方法も示します。


## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)と [Exchange ピアリングのチュートリアル](walkthrough-exchange-all.md)を確認します。
* Microsoft との Exchange ピアリングが既にあり、Azure リソースに変換されていない場合は、「[ポータルを使用してレガシの Exchange ピアリングを Azure リソースに変換する](howto-legacy-exchange-portal.md)」を参照してください。

## <a name="create-and-provision-an-exchange-peering"></a>Exchange ピアリングを作成・プロビジョニングする

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>ポータルにサインインしてサブスクリプションを選択する
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering-with-route-server"></a><a name=create></a>ルート サーバーとの Exchange ピアリングを作成変更する


インターネット交換プロバイダーは、[Exchange ピアリングを作成]( https://go.microsoft.com/fwlink/?linkid=2129593)することにより、ピアリング要求を作成できます。

1. **[ピアリングの作成]** ページの **[基本]** タブで、次のようにボックスに入力します。

    > [!div class="mx-imgBorder"] 
    > ![Peering Service の登録](./media/setup-basics-tab.png)

* お使いの Azure サブスクリプションを選択します。

* [リソース グループ] では、ボックスの一覧から既存のリソース グループを選択するか、[新規作成] を選択して新しいグループを作成します。 この例では、新しいリソース グループを作成します。

* [名前] は、リソース名に対応し、任意の名前を選択できます。

* 既存のリソース グループを選択した場合、[リージョン] は自動的に選択されます。 新しいリソース グループの作成を選択した場合は、リソースを配置する Azure リージョンも選択する必要があります。

    >[!NOTE]
    >リソース グループを配置するリージョンは、Microsoft とのピアリングを作成する場所とは関係ありません。 ただし、最も近い Azure リージョンにリソース グループを配置して、使用するピアリング リソースをそこで整理することをお勧めします。 たとえば、Ashburn でのピアリングの場合、米国東部または米国東部 2 にリソース グループを作成できます。

* **[PeerASN]** ボックスで、使用する ASN を選択します。

    >[!IMPORTANT] 
    >選択できるのは、ピアリング要求を送信する前に ValidationState が "Approved" になっている ASN だけです。 PeerAsn 要求を送信した直後であれば、ASN の関連付けが承認されるまで 12 時間程度かかります。 選択した ASN が検証待ちの場合、エラー メッセージが表示されます。 選択する必要がある ASN が表示されない場合は、適切なサブスクリプションを選択しているかどうかを確認します。 選択している場合は、 **[ピア ASN の Azure サブスクリプションへの関連付け](https://go.microsoft.com/fwlink/?linkid=2129592)** を使用して、PeerAsn を既に作成したかどうかを確認します。

* **[次へ: 構成]** を選択して続行します。

#### <a name="configure-connections-and-submit"></a>接続を構成して送信する

1. [ピアリングの作成] ページの [構成] タブで、ここに示されているようにボックスに入力します。

    > [!div class="mx-imgBorder"]
    > ![ルート設定を構成する](./media/setup-exchange-conf-tab-routeserver.png)
 
    * [ピアリングの種類] には、 **[ダイレクト]** を選択します。
    * [Microsoft ネットワーク] では、 **[AS8075 with exchange route server]\(AS8075 と Exchange ルート サーバー\)** を選択します。 
    * [SKU] には、 **[Basic Free]\(Basic 無料\)** を選択します。 [premium free]\(Premium 無料\) は、特定のアプリケーション用に予約されているため、選択しないでください。
    * ピアリングを設定する **[Metro]\(都市圏\)** の場所を選択します。

1. **[Peering Connections]\(ピアリング接続\)** で、 **[新規作成]** を選択します

1.  **[Direct Peering Connection]\(ダイレクト ピアリング接続\)** で、次の BGP セッションの詳細を入力します。

    > [!div class="mx-imgBorder"]
    > ![[Direct ピアリング接続] ウィンドウを示すスクリーンショット。詳細が追加されています。](./media/setup-exchange-conf-tab-direct-route.png)


     * [Peering facility]\(ピアリング ファシリティ\) で、ピアリングに適切な物理的な場所を選択します
     * [Session Address Provider]\(セッション アドレス プロバイダー\) で、[ピア] を選択します
     * セッション IPv4 プレフィックスは、交換プロバイダーのピアによって提供されます
     * ピア セッション IPv4 アドレスは、ルート サーバーの交換ピアによって、IP プレフィックスの範囲から選択されます。
     * Microsoft セッション IPv4 アドレスは、IP プレフィックスの範囲から割り当てられたルーター IP になります。
     * 現時点では、セッション IPv6 はオプションです。
     * [Maximum advertised IPv4 prefix]\(アドバタイズされる IPv4 プレフィックスの最大数\) には、最大で 20,000 を指定できます。 
     * [Use for Peering Service]\(Peering Service に使用\) は、既定で無効になっています。 交換プロバイダーが Microsoft との Peering Service 契約に署名すると、この機能を有効にすることができます。

1. 完了したら、 **[保存]** をクリックします。 

1. [Create a peering]\(ピアリングの作成\) の下に、"検証に成功しました" と表示されます。 検証に成功したら、 **[作成]** をクリックします

    > [!div class="mx-imgBorder"]
    > ![設定の検証](./media/setup-exchange-conf-tab-validation.png)

    >[!NOTE]
    >Microsoft Peering Service パートナーである通常のインターネット サービス プロバイダー (ISP) の場合、顧客の IP プレフィックスの登録が必要です。 ただし、ルート サーバーを使用する交換パートナーの場合は、プレフィックスではなく顧客の ASN を登録する必要があります。 同じ ASN キーは、顧客のプレフィックスの登録に対して有効です。

1. [設定] セクションで **[登録された ASN]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[ピアリング] ウィンドウを示すスクリーンショット。[登録された ASN] メニュー項目が呼び出されています。](./media/setup-exchange-registered-asn.png)

1. 対象の交換サブスクリプションで新しい顧客 ASN を作成するために、 **[Add registered ASN]\(登録された ASN を追加する\)** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[Register an ASN]\(ASN の登録\) ウィンドウを示すスクリーンショット。[名前] テキスト ボックスと [ASN] テキストボックスが表示されています。](./media/setup-exchange-register-new-asn.png)

1. [Register an ASN]\(ASN の登録\) で名前を選択し、顧客 ASN を入力して、[保存] をクリックします。

1. [登録された ASN] の下に、各 ASN に割り当てられている、関連付けられたプレフィックス キーが表示されます。 交換プロバイダーは、このプレフィックス キーを顧客に提供して、顧客がサブスクリプションに Peering Service を登録できるようにする必要があります。

    > [!div class="mx-imgBorder"]
    > ![[登録された ASN] ウィンドウを示すスクリーンショット。プレフィックス キーが表示されています。](./media/setup-exchange-register-asn-prefixkey.png)




### <a name="verify-an-exchange-peering"></a><a name=get></a>Exchange ピアリングを確認する
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Exchange ピアリングを変更する
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Exchange ピアリングをプロビジョニング解除する
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>次のステップ

* [ポータルを使用して Direct ピアリングを作成または変更する](howto-direct-portal.md)
* [ポータルを使用してレガシの Direct ピアリングを Azure リソースに変換する](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>その他のリソース

詳細については、「[インターネット ピアリングのよくあるご質問](faqs.md)」を参照してください。
