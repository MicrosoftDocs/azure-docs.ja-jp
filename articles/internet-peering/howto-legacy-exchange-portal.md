---
title: Azure portal を使用してレガシの Exchange ピアリングを Azure リソースに変換する
titleSuffix: Azure
description: Azure portal を使用してレガシの Exchange ピアリングを Azure リソースに変換する
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/21/2020
ms.author: derekol
ms.openlocfilehash: 6249d68486af754f2d2980aaed9d5fd8287dcb5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84700163"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Azure portal を使用してレガシの Exchange ピアリングを Azure リソースに変換する

この記事では、Azure portal を使用して既存のレガシの Exchange ピアリングを Azure リソースに変換する方法について説明します。

必要に応じて、[PowerShell](howto-legacy-exchange-powershell.md) を使用してこのガイドを完了することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)と [Exchange ピアリングのチュートリアル](walkthrough-exchange-all.md)を確認します。

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>レガシの Exchange ピアリングを Azure リソースに変換する

インターネット交換プロバイダーは、[ピアリングを作成]( https://go.microsoft.com/fwlink/?linkid=2129593)することにより、Exchange ピアリング要求を作成できます。

1. **[ピアリングの作成]** ページの **[基本]** タブで、次のようにボックスに入力します。

   ![Peering Service の登録](./media/setup-basics-tab.png)

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
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Exchange ピアリングを確認する
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>その他のリソース

詳細については、「[インターネット ピアリングのよくあるご質問](faqs.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [ポータルを使用して Exchange ピアリングを作成または変更する](howto-exchange-portal.md)
