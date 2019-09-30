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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 65cc2d9ac2b96822f2c1b740f3180ba1d9eaf98c
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390000"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab Services で共有イメージ ギャラリーを使用する
この記事では、教師およびラボ管理者が、他のユーザーが再利用できるようテンプレート仮想マシン イメージを保存する方法を示します。 これらのイメージは、Azure [共有イメージ ギャラリー](../../virtual-machines/windows/shared-image-galleries.md)に保存されます。 最初の手順として、ラボ管理者は、ラボ アカウントに既存の共有イメージ ギャラリーをアタッチします。 共有イメージ ギャラリーがアタッチされたら、ラボ アカウントで作成されたラボは、共有イメージ ギャラリーにイメージを保存できます。 その他の教師は、共有イメージ ギャラリーからこのイメージを選択して、そのクラスのテンプレートを作成できます。 

## <a name="prerequisites"></a>前提条件
- [Azure PowerShell](../../virtual-machines/windows/shared-images.md) または [Azure CLI](../../virtual-machines/linux/shared-images.md) のいずれかを使用して、共有イメージ ギャラリーを作成します。
- あらかじめ共有イメージ ギャラリーをラボ アカウントにアタッチしておきます。 詳細な手順については、[共有イメージ ギャラリーをアタッチまたはデタッチする方法](how-to-attach-detach-shared-image-gallery.md)に関するページを参照してください。


## <a name="save-an-image-to-the-shared-image-gallery"></a>共有イメージ ギャラリーに既存のイメージを保存する
共有イメージ ギャラリーがアタッチされたら、ラボ アカウントの管理者または教師は、他の教師が再利用できるよう共有イメージ ギャラリーにイメージを保存できます。 

1. ラボのホーム ページで、 **[テンプレート]** セクションのタイル上の **[Save image]\(イメージの保存\)** を選択します。

    ![[Save image]\(イメージの保存\) ボタン](../media/how-to-use-shared-image-gallery/save-image-button.png)
2.  **[Save virtual machine image]\(仮想マシン イメージの保存\)** ウィンドウで、イメージの名前を入力し、 **[保存]** を選択します。 

    ![[Save virtual machine image]\(仮想マシン イメージの保存\) ウィンドウ](../media/how-to-use-shared-image-gallery/save-virtual-machine-image.png)
3. ラボ タイルで状態をチェックします。 

    ![イメージの保存操作の状態](../media/how-to-use-shared-image-gallery/save-image-status.png)
4. 操作が成功したことを確認します。

    ![イメージの保存操作が成功](../media/how-to-use-shared-image-gallery/save-image-successful.png)

ラボのコンテキスト外で共有イメージ ギャラリーにイメージをアップロードすることもできます。 詳細については、[共有イメージ ギャラリーの概要](../../virtual-machines/windows/shared-images.md)に関するページを参照してください。 

## <a name="use-an-image-from-the-shared-image-gallery"></a>共有イメージ ギャラリーのイメージを使用する
教師や教授は、新しいラボの作成時に、テンプレートの共有イメージ ギャラリーにあるカスタム イメージを選択できます。

![ギャラリーの仮想マシン イメージを使用する](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>次の手順
共有イメージ ギャラリーの詳細については、[共有イメージ ギャラリー](../../virtual-machines/windows/shared-image-galleries.md)に関するページを参照してください。
