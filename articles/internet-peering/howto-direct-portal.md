---
title: Azure portal を使用して Direct ピアリングを作成または変更する
titleSuffix: Azure
description: Azure portal を使用して Direct ピアリングを作成または変更する
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/19/2020
ms.author: derekol
ms.openlocfilehash: 1a89ce873c53e94036aa4f8ac2c2870365924187
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91537210"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Azure portal を使用して Direct ピアリングを作成または変更する

この記事では、Azure portal を使用して、インターネット サービス プロバイダーまたはインターネット交換プロバイダー向けに Microsoft Direct ピアリングを作成する方法について説明します。 また、この記事では、リソースの状態確認、更新、または削除およびプロビジョニング解除の方法も示します。

必要に応じて、Azure [PowerShell](howto-direct-powershell.md) を使用してこのガイドを完了することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)と [Direct ピアリングのチュートリアル](walkthrough-direct-all.md)を確認します。
* Microsoft との Direct ピアリング接続が既にあり、Azure リソースに変換されていない場合は、「[ポータルを使用してレガシの Direct ピアリングを Azure リソースに変換する](howto-legacy-direct-portal.md)」を参照してください。

## <a name="create-and-provision-a-direct-peering"></a>Direct ピアリングを作成およびプロビジョニングする

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>ポータルにサインインしてサブスクリプションを選択する
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Direct ピアリングを作成する

インターネット サービス プロバイダーまたはインターネット交換プロバイダーは、[ピアリングを作成]( https://go.microsoft.com/fwlink/?linkid=2129593)することにより、新しい Direct ピアリング要求を作成できます。

1. **[Create a Peering]\(ピアリングの作成\)** ページの **[基本]** タブで、次のようにボックスに入力します。


    ![[ピアリングの作成] ページの [基本] タブが表示されているスクリーンショット。[新規作成] リンクのコールアウトが示されています。](./media/setup-basics-tab.png)

2. お使いの Azure サブスクリプションを選択します。

3. [リソース グループ] では、ボックスの一覧から既存のリソース グループを選択するか、[新規作成] を選択して新しいグループを作成します。 この例では、新しいリソース グループを作成します。

4. [名前] は、リソース名に対応し、任意の名前を選択できます。

5. 既存のリソース グループを選択した場合、[リージョン] は自動的に選択されます。 新しいリソース グループの作成を選択した場合は、リソースを配置する Azure リージョンも選択する必要があります。

    >[!NOTE]
    > リソース グループを配置するリージョンは、Microsoft とのピアリングを作成する場所とは関係ありません。 ただし、最も近い Azure リージョンにリソース グループを配置して、使用するピアリング リソースをそこで整理することをお勧めします。 たとえば、Ashburn でのピアリングの場合、米国東部または米国東部 2 にリソース グループを作成できます。

6. **[PeerASN]** ボックスで、使用する ASN を選択します。

    >[!IMPORTANT]
    >選択できるのは、ピアリング要求を送信する前に ValidationState が "Approved" になっている ASN だけです。 PeerAsn 要求を送信した直後であれば、ASN の関連付けが承認されるまで 12 時間程度かかります。 選択した ASN が検証待ちの場合、エラー メッセージが表示されます。 選択する必要がある ASN が表示されない場合は、適切なサブスクリプションを選択しているかどうかを確認します。 選択している場合は、 **[ピア ASN の Azure サブスクリプションへの関連付け](https://go.microsoft.com/fwlink/?linkid=2129592)** を使用して、PeerAsn を既に作成したかどうかを確認します。

7. **構成** をクリックして、続行します。



    ![[ピアリングの作成] ページの [基本] タブが表示されているスクリーンショット。すべての値が入力されています。](./media/setup-direct-basics-filled-tab.png)


#### <a name="configure-connections-and-submit"></a>接続を構成して送信する
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Direct ピアリングを確認する
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Direct ピアリングを変更する
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Direct ピアリングをプロビジョニング解除する
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>次のステップ

* [ポータルを使用して Exchange ピアリングを作成または変更する](howto-exchange-portal.md)
* [ポータルを使用してレガシの Exchange ピアリングを Azure リソースに変換する](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>その他のリソース

詳細については、「[インターネット ピアリングのよくあるご質問](faqs.md)」を参照してください。
