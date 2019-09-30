---
title: Azure Lab Services で共有イメージ ギャラリーをアタッチまたはデタッチする | Microsoft Docs
description: Azure Lab Services で共有イメージ ギャラリーをラボにアタッチする方法について説明します。
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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: d8f16baccd3a4980ee9dbe410cb2a95878b346d4
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390022"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab Services で共有イメージ ギャラリーをアタッチまたはデタッチする
教師/ラボ管理者は、テンプレート VM イメージを他のユーザーが再利用できるよう、Azure [共有イメージ ギャラリー](../../virtual-machines/windows/shared-image-galleries.md)に保存することができます。 最初の手順として、ラボ管理者は、ラボ アカウントに既存の共有イメージ ギャラリーをアタッチします。 共有イメージ ギャラリーがアタッチされたら、ラボ アカウントで作成されたラボは、共有イメージ ギャラリーにイメージを保存できます。 その他の教師は、共有イメージ ギャラリーからこのイメージを選択して、そのクラスのテンプレートを作成できます。 

この記事では、共有イメージ ギャラリーをラボ アカウントにアタッチしたり、ラボ アカウントからデタッチしたりする方法について説明します。 

> [!NOTE]
> Shared Image Gallery サービスに関連するコストについては、[Shared Image Gallery の料金](../../virtual-machines/windows/shared-image-galleries.md#billing)に関するページを参照してください。

## <a name="configure-at-the-time-of-lab-account-creation"></a>ラボ アカウントの作成時に構成する
ラボ アカウントを作成する際、共有イメージ ギャラリーをラボ アカウントにアタッチすることができます。 共有イメージ ギャラリーは、ドロップダウン リストから既存のものを選択するか、新たに作成することができます。 共有イメージ ギャラリーを作成してラボ アカウントにアタッチするには、 **[新規作成]** を選択し、ギャラリーの名前を入力して、 **[OK]** をクリックします。 

![ラボ アカウントの作成時に共有イメージ ギャラリーを構成する](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>ラボ アカウントの作成後に構成する
ラボ アカウントの作成後、次のタスクを実行できます。

- 共有イメージ ギャラリーを作成してアタッチする
- ラボ アカウントに共有イメージ ギャラリーをアタッチする
- ラボ アカウントから共有イメージ ギャラリーをデタッチする

## <a name="create-and-attach-a-shared-image-gallery"></a>共有イメージ ギャラリーを作成してアタッチする
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューから、 **[すべてのサービス]** を選択します。 **[DevOps]** セクションで、 **[ラボ サービス]** を選択します。 **[ラボ サービス]** の横にある星印 (`*`) を選択した場合は、左側のメニューの **[お気に入り]** セクションに追加されます。 次回以降は、 **[お気に入り]** の下で **[ラボ サービス]** を選択します。

    ![[すべてのサービス] -> [ラボ サービス]](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. ラボ アカウントを選択して、 **[ラボ アカウント]** ページを表示します。 
4. 左側のメニューで **[共有イメージ ギャラリー]** を選択し、ツールバーで **[+ 作成]** を選択します。  

    ![共有イメージ ギャラリーの作成ボタン](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. **[共有イメージ ギャラリーの作成]** ウィンドウにギャラリーの**名前**を入力し、 **[OK]** をクリックします。 

    ![[共有イメージ ギャラリーの作成] ウィンドウ](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services によって共有イメージ ギャラリーが作成され、ラボ アカウントにアタッチされます。 このラボ アカウントで作成されるラボはすべて、アタッチされた共有イメージ ギャラリーにアクセスできます。 

    ![アタッチされたイメージ ギャラリー](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    一番下のウィンドウに、共有イメージ ギャラリー内のイメージが表示されます。 この新しいギャラリーにはイメージがありません。 ギャラリーにイメージをアップロードすると、そのイメージがこのページに表示されます。     

    アタッチされた共有イメージ ギャラリー内のすべてのイメージは、既定で有効になります。 一覧でイメージを選択し、 **[選択したイメージを有効にする]** または **[選択したイメージを無効にする]** ボタンを使用して、選択したイメージを有効にしたり無効にしたりできます。

## <a name="attach-an-existing-shared-image-gallery"></a>既存の共有イメージ ギャラリーをアタッチする
次の手順では、ラボ アカウントに既存の共有イメージ ギャラリーをアタッチする方法を示します。 

1. **[ラボ アカウント]** ページの左側のメニューで **[共有イメージ ギャラリー]** を選択し、ツール バーの **[アタッチ]** を選択します。 

    ![共有イメージ ギャラリーの概要 - [追加] ボタン](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. **[既存の共有イメージ ギャラリーをアタッチする]** ページで共有イメージ ギャラリーを選択し、 **[OK]** を選択します。

    ![既存のギャラリーを選択する](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. 次の画面が表示されます。 

    ![一覧のマイ ギャラリー](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    この例では、共有イメージ ギャラリーにイメージはありません。

    Azure Lab Services ID が、ラボにアタッチされた共有イメージ ギャラリーに共同作成者として追加されます これにより、教師/IT 管理者は共有イメージ ギャラリーに仮想マシン イメージを保存できます。 このラボ アカウントで作成されるラボはすべて、アタッチされた共有イメージ ギャラリーにアクセスできます。 

    アタッチされた共有イメージ ギャラリー内のすべてのイメージは、既定で有効になります。 一覧でイメージを選択し、 **[選択したイメージを有効にする]** または **[選択したイメージを無効にする]** ボタンを使用して、選択したイメージを有効にしたり無効にしたりできます。 

## <a name="detach-a-shared-image-gallery"></a>共有イメージ ギャラリーをデタッチする
ラボには共有イメージ ギャラリーを 1 つだけ接続できます。 別の共有イメージ ギャラリーをアタッチする場合は、現在のギャラリーをデタッチしてから、新しいギャラリーをアタッチします。 ラボから共有イメージ ギャラリーをデタッチするには、ツールバーで **[デタッチ]** を選択し、デタッチ操作を確認します。 

![ラボ アカウントから共有イメージ ギャラリーをデタッチする](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>次の手順
共有イメージ ギャラリーにラボ イメージを保存したり、共有イメージ ギャラリーのイメージを使用して VM を作成したりする方法については、[共有イメージ ギャラリーの使用方法](how-to-use-shared-image-gallery.md)に関するページを参照してください。

共有イメージ ギャラリー全般の詳細については、[共有イメージ ギャラリー](../../virtual-machines/windows/shared-image-galleries.md)に関するページを参照してください。
