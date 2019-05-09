---
title: Azure DevTest Labs のラボへのアクセス | Microsoft Docs
description: このチュートリアルでは、Azure DevTest Labs を使って作成されたラボにアクセスし、仮想マシンを要求して使用した後、それらを解放します。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/18/2019
ms.author: spelluru
ms.openlocfilehash: b5abb8d4aad7c58bf673aa578255efe12d32ad4b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54422900"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>チュートリアル:Azure DevTest Labs のラボへのアクセス
このチュートリアルでは、[Azure DevTest Labs でラボを作成するチュートリアル](tutorial-create-custom-lab.md)で作成したラボを使います。

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * ラボの仮想マシン (VM) を要求する
> * VM に接続します
> * VM を解放する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="access-the-lab"></a>ラボにアクセスする

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューで、**[すべてのリソース]** を選択します。 
3. リソースの種類として **[DevTest Labs]** を選びます。 
4. ラボを選びます。 

    ![ラボを選ぶ](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>VM を要求する

1. **[要求可能な仮想マシン]** の一覧で、**[...]** (省略記号) を選び、**[マシンの要求]** を選びます。

    ![仮想マシンを要求する](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. **[自分の仮想マシン]** の一覧に VM が表示されることを確認します。

    ![自分の仮想マシン](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>VM に接続します

1. 一覧で VM を選びます。 選んだ VM の**仮想マシン ページ**が表示されます。 ツール バーの **[接続]** を選びます。

    ![仮想マシンへの接続](./media/tutorial-use-custom-lab/connect-button.png)
2. ダウンロードした **RDP** ファイルをハード ディスクに保存し、それを使って仮想マシンに接続します。 前のセクションで VM を作成するときに設定したユーザー名とパスワードを指定します。 

    > [!NOTE] 
    > Linux VM に接続するには、その VM に対する SSH アクセスや RDP アクセスが有効になっている必要があります。 Linux VM に RDP で接続する手順については、「[リモート デスクトップをインストールして Azure の Linux VM に接続するように構成する](../virtual-machines/linux/use-remote-desktop.md)」を参照してください。 


## <a name="unclaim-the-vm"></a>VM を解放する
VM を使い終わったら、次の手順で VM を解放します。 

1. 仮想マシンのページで、ツール バーの **[解放]** を選びます。 

    ![VM を解放する](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. VM は、解放される前にシャットダウンされます。 この動作の状態は、通知で確認できます。  
3. 一番上の階層リンク メニューでラボ名をクリックして DevTest Lab ページに戻ります。 
    
    ![ラボに戻る](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. 一番下の **[要求可能な仮想マシン]** ボックスの一覧に、対象の VM が表示されていることを確認します。

    
## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure DevTest Labs を使って作成されたラボにアクセスして使用する方法を示しました。 ラボの VM にアクセスして使用する方法について詳しくは、次をご覧ください 

> [!div class="nextstepaction"]
> [方法:ラボでの VM の使用](devtest-lab-add-vm.md)

