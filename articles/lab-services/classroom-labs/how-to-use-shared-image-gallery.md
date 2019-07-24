---
title: Azure Lab Services で共有イメージ ギャラリーを使用する | Microsoft Docs
description: 共有イメージ ギャラリーを使用するようにラボ アカウントを構成して、ユーザーが他のユーザーとイメージを共有したり、他のユーザーがイメージを使用してラボでテンプレート VM を作成したりできるようにする方法について説明します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 93136c7d685bd9fc8ec4bcdea3a900b28029059b
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652874"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab Services で共有イメージ ギャラリーを使用する
この記事では、教師およびラボ管理者が、他のユーザーが再利用できるようテンプレート仮想マシン イメージを保存する方法を示します。 これらのイメージは、Azure [共有イメージ ギャラリー](../../virtual-machines/windows/shared-image-galleries.md)に保存されます。 最初の手順として、ラボ管理者は、ラボ アカウントに既存の共有イメージ ギャラリーをアタッチします。 共有イメージ ギャラリーがアタッチされたら、ラボ アカウントで作成されたラボは、共有イメージ ギャラリーにイメージを保存できます。 その他の教師は、共有イメージ ギャラリーからこのイメージを選択して、そのクラスのテンプレートを作成できます。 

## <a name="prerequisites"></a>前提条件
[Azure PowerShell](../../virtual-machines/windows/shared-images.md) または [Azure CLI](../../virtual-machines/linux/shared-images.md) のいずれかを使用して、共有イメージ ギャラリーを作成します。

## <a name="attach-a-shared-image-gallery-to-a-lab-account"></a>ラボ アカウントに共有イメージ ギャラリーをアタッチする
次の手順では、ラボ アカウントに共有イメージ ギャラリーをアタッチする方法を示します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューから、**[すべてのサービス]** を選択します。 **[DevOps]** セクションで、**[ラボ サービス]** を選択します。 **[ラボ サービス]** の横にある星印 (`*`) を選択した場合は、左側のメニューの **[お気に入り]** セクションに追加されます。 次回以降は、**[お気に入り]** の下で **[ラボ サービス]** を選択します。

    ![[すべてのサービス] -> [ラボ サービス]](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. ラボ アカウントを選択して、**[ラボ アカウント]** ページを表示します。 
4. 左側のメニューで **[共有イメージ ギャラリー]** を選択し、ツールバーで **[アタッチ]** を選択します。 

    ![共有イメージ ギャラリーの概要 - [追加] ボタン](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. **[既存の共有イメージ ギャラリーをアタッチする]** ページで共有イメージ ギャラリーを選択し、**[OK]** を選択します。

    ![既存のギャラリーを選択する](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. 次の画面が表示されます。 

    ![一覧のマイ ギャラリー](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    この例では、共有イメージ ギャラリーにイメージはありません。

Azure Lab Services ID が、ラボにアタッチされた共有イメージ ギャラリーに共同作成者として追加されます これにより、教師/IT 管理者は共有イメージ ギャラリーに仮想マシン イメージを保存できます。 このラボ アカウントで作成されるラボはすべて、アタッチされた共有イメージ ギャラリーにアクセスできます。 

アタッチされた共有イメージ ギャラリー内のすべてのイメージは、既定で有効になります。 一覧でイメージを選択し、**[選択したイメージを有効にする]** または **[選択したイメージを無効にする]** ボタンを使用して、選択したイメージを有効にしたり無効にしたりできます。 

## <a name="detach-a-shared-image-gallery"></a>共有イメージ ギャラリーをデタッチする
ラボには共有イメージ ギャラリーを 1 つだけ接続できます。 別の共有イメージ ギャラリーをアタッチする場合は、現在のギャラリーをデタッチしてから、新しいギャラリーをアタッチします。 ラボから共有イメージ ギャラリーをデタッチするには、ツールバーで **[デタッチ]** を選択し、デタッチ操作を確認します。 

## <a name="save-an-image-to-the-shared-image-gallery"></a>共有イメージ ギャラリーに既存のイメージを保存する
共有イメージ ギャラリーがアタッチされたら、教師は、他の教員が再利用できるよう共有イメージ ギャラリーにテンプレート イメージを保存できます。

![ギャラリーで仮想マシン イメージを保存する](../media/how-to-use-shared-image-gallery/save-virtual-machine.png)

## <a name="use-an-image-from-the-shared-image-gallery"></a>共有イメージ ギャラリーのイメージを使用する
教師や教授は、新しいラボの作成時に、テンプレートの共有イメージ ギャラリーにあるカスタム イメージを選択できます。

![ギャラリーの仮想マシン イメージを使用する](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>次の手順
共有イメージ ギャラリーの詳細については、[共有イメージ ギャラリー](../../virtual-machines/windows/shared-image-galleries.md)に関するページを参照してください。
