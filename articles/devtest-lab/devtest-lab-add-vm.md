---
title: "Azure DevTest Labs でのラボへの VM の追加 | Microsoft Docs"
description: "Azure DevTest Labs でラボに仮想マシンを追加する方法を説明します。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 052785aa225086e55a40c061765770b9eebaa154
ms.lasthandoff: 03/21/2017


---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でラボに VM を追加する
VM は、"*ベース*" ([カスタム イメージ](devtest-lab-create-template.md)、[数式](devtest-lab-manage-formulas.md)、[Marketplace イメージ](devtest-lab-configure-marketplace-images.md)のいずれか) からラボに追加します。 このチュートリアルでは、Azure Portal を使用して、DevTest Labs でラボに VM を追加します。

> [!NOTE]
> [要求可能 VM の追加](devtest-lab-add-claimable-vm.md)に関する記事では、ラボのあらゆるユーザーが使用できるように VM を要求可能にする方法について説明しています。
>
>

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でのラボへの VM の追加手順
1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
1. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で、VM を作成するラボを選択します。  
1. ラボの **[概要]** ブレードで、**[+ 追加]** を選択します。  

    ![VM ボタンを追加する](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. **[Choose a base (ベースの選択)]** ブレードで、VM のベースを選択します。
1. **[仮想マシン]** ブレードで、**[仮想マシン名]** ボックスに新しい仮想マシンの名前を入力します。

    ![[ラボ VM] ブレード](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. **[ユーザー名]** に、仮想マシンの管理者権限を付与するユーザー名を入力します。  
1. [シークレット ストア](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store)に格納されているパスワードを使用する場合、**[Use a saved secret (保存されたシークレットを使用する)]** を選択し、シークレット (パスワード) に対応するキー値を指定します。 または、**[Type a value (値を入力してください)]** とラベル付けされているテキスト フィールドにパスワードを入力します。
1. **仮想マシンのディスクの種類**によって、ラボの仮想マシンで許可されるストレージ ディスクの種類が決まります。
1. **[仮想マシン サイズ]** を選択し、定義済みの項目の中から、作成する VM のプロセッサ コア、RAM サイズ、ハード ドライブ サイズを指定するものを選びます。
1. **[アーティファクト]** を選択し、アーティファクトの一覧から基本イメージに追加するアーティファクトを選択して構成します。
    **注:** DevTest Labs やアーティファクトの構成に関する経験がない場合は、「[既存のアーティファクトの VM への追加](./devtest-lab-add-vm-with-artifacts.md#add-an-existing-artifact-to-a-vm)」セクションを参照し、終了してからここに戻ってください。
1. **[詳細設定]** を選択して VM のネットワーク オプションと有効期限のオプションを構成します。
1. Azure Resource Manager テンプレートを表示またはコピーする場合は、「[Azure Resource Manager テンプレートの保存](./devtest-lab-add-vm-with-artifacts.md#save-azure-resource-manager-template)」セクションを参照し、終了してからここに戻ってください。
1. **[作成]** を選択して、指定した VM をラボに追加します。
1. ラボのブレードには VM の作成状況が表示されます。最初は **[作成中]**、VM が起動した後は **[実行中]** と表示されます。

## <a name="next-steps"></a>次のステップ
* VM が作成されたら、VM のブレードで **[接続]** を選択して VM に接続できます。
* [DevTest Labs Azure Resource Manager のクイックスタート テンプレート ギャラリー](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)を検索します。

