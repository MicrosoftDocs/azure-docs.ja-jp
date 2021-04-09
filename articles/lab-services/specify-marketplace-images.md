---
title: Azure Lab Services でラボ用の Marketplace イメージを指定する
description: この記事では、ラボ作成者が Azure Lab Services でラボ アカウントにラボを作成するために使用できる Marketplace イメージを指定する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5c81c8d7a15d67055729a29e98f5b7e30f3d0764
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434738"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>ラボ作成者が利用できる Marketplace イメージを指定する
ラボ アカウント所有者は、ラボ作成者がラボ アカウントにラボを作成するために使用できる Marketplace イメージを指定できます。 

## <a name="select-images-available-for-labs"></a>ラボで使用可能なイメージを選択する
左側のメニューで、 **[Marketplace イメージ]** を選択します。 既定では、イメージの完全な一覧 (有効なものと無効なものの両方) が表示されます。 上部のドロップダウン リストから **[有効のみ]** / **[無効のみ]** オプションを選択することにより、一覧をフィルター処理して、有効/無効のイメージのみを表示することができます。 
    
![Marketplace イメージのページ](./media/tutorial-setup-lab-account/marketplace-images-page.png)

一覧に表示される Marketplace イメージは、次の条件を満たすものだけです。
    
- 単一の VM を作成する。
- Azure Resource Manager を使用して VM をプロビジョニングする
- 追加のライセンス プランの購入を必要としない

## <a name="disable-images-for-a-lab"></a>ラボに対してイメージを無効にする 
1 つのイメージをラボに対して無効にするには、最後の列の **[...]** (省略記号) を選択し、 **[イメージを無効にする]** を選択します。 

![1 つのイメージを無効にする](./media/tutorial-setup-lab-account/disable-one-image.png) 

または、イメージ名の前にあるチェック ボックスをオンにして、ツール バーの **[選択したイメージを無効にする]** を選択します。 

複数のイメージを同時に無効にするには、イメージ名の前にあるチェック ボックスをオンにして、ツール バーの **[選択したイメージを無効にする]** を選択します。 

![複数のイメージを無効にする](./media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>ラボに対してイメージを有効にする
無効になっているイメージを有効にするには、最後の列の **[...]** (省略記号) を選択し、 **[イメージを有効にする]** を選択します。 または、イメージ名の前にあるチェック ボックスをオンにして、ツール バーの **[選択したイメージを有効にする]** を選択します。 

複数のイメージを同時に無効にするには、イメージ名の前にあるチェック ボックスをオンにして、ツール バーの **[選択したイメージを有効にする]** を選択します。 

## <a name="enable-images-at-the-time-of-lab-creation"></a>ラボの作成時にイメージを有効にする
ラボの作成時に、さらに多くのイメージを有効にすることができます。 

1. **ラボ アカウント所有者** の資格情報を使用して、[Azure Lab Services Web サイト](https://labs.azure.com)にサインインします。
2. 既定の仮想マシン イメージまたは下矢印を選択します。 
3. **[Enable more image options]\(その他のイメージ オプションを有効にする\)** を選択します。 

    ![[Enable more image options]\(その他のイメージ オプションを有効にする\)](./media/specify-marketplace-images/enable-more-images-menu.png)
4. 前のセクションの手順に従って、選択したイメージを有効にします。 
5. 前の手順で選択したイメージを表示するには、 **[新しいラボ]** ウィンドウを閉じてから再度開く必要があります。 



## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてテンプレートを設定および発行する](how-to-create-manage-template.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
- [ラボ ユーザーとしてラボにアクセスする](how-to-use-classroom-lab.md)
