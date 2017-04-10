---
title: "Azure DevTest Labs での数式の作成 | Microsoft Docs"
description: "Azure DevTest Labs 数式を作成し、新しい VM の作成に使用する方法について説明します。"
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
ms.date: 03/07/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 4f9f86c2cc106aa96f27293a43cd784e2fb6fe76
ms.lasthandoff: 03/17/2017


---
# <a name="create-azure-devtest-labs-formulas"></a>Azure DevTest Labs の数式の作成

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

この記事では、ベース (カスタム イメージ、Marketplace イメージ、他の数式) または既存の VM から数式を作成する方法について説明します。 

## <a name="create-a-formula"></a>数式の作成
DevTest Labs の *ユーザー* アクセス許可が付与されていれば、だれでも、数式をベースとして使用して VM を作成することができます。 数式を作成する方法は 2 つあります。 

* ベースから - 数式のすべての特性を定義するときに使用します。
* 既存のラボ VM から - 既存の VM の設定に基づいて数式を作成するときに使用します。

ユーザーおよびアクセス許可を追加する方法の詳細については、「[Azure DevTest Labs での所有者とユーザーの追加](./devtest-lab-add-devtest-user.md)」を参照してください。

### <a name="create-a-formula-from-a-base"></a>ベースから数式を作成する
数式を、カスタムイメージ、Marketplace イメージ、またはその他の数式から作成する手順は次のとおりです。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

2. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

3. ラボの一覧で目的のラボを選択します。  

4. ラボのブレードで、 **[数式 (再利用可能なベース)]**を選択します。
   
    ![Formula menu](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. **[数式]** ブレードで、**[+ 追加]** を選択します。
   
    ![数式の追加](./media/devtest-lab-create-formulas/add-formula.png)

6. **[ベースの選択]** ブレードで、数式を作成する際のベース (カスタム イメージ、Marketplace イメージ、または数式) を選択します。
   
    ![Base list](./media/devtest-lab-create-formulas/base-list.png)

7. **[Create formula]** (数式の作成) ブレードで、次の値を指定します。
   
    * **Formula name** (数式の名前) - 数式の名前を入力します。 この値は、VM を作成するときに基本イメージの一覧に表示されます。 入力した名前は検証され、有効でない場合は有効な名前の要件を示すメッセージが表示されます。
    * **説明** - 数式のわかりやすい説明を入力します。 この値は、VM を作成する際に数式のコンテキスト メニューに表示されます。
    * **ユーザー名** - 管理者特権を付与するユーザー名を入力します。
    * **パスワード** - 指定されたユーザーに使用するシークレット (パスワード) に関連付けられている値を入力、またはドロップダウン リストから選択します。 シークレットの詳細については、「[Azure DevTest Labs: Personal secret store (Azure DevTest Labs: 個人のシークレット ストア)](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store/)」を参照してください。
    * **仮想マシンのディスクの種類** - 基本イメージを使用してプロビジョニングされた仮想マシンのストレージ ディスクの種類として、HDD (ハード ディスク ドライブ) または SSD (ソリッドステート ドライブ) のどちらが許可されているかを指定します。
    * **仮想マシン サイズ** - 定義済みの項目のいずれかを選択して、作成する VM のプロセッサ コア、RAM サイズ、ハード ドライブ サイズを指定します。 
    * **アーティファクト** - これを選択すると、**[成果物の追加]** ブレードが開きます。このブレードで、基本イメージに追加するアーティファクトを選択して構成します。 アーティファクトの詳細については、「[Azure DevTest Labs での VM アーティファクトの管理](./devtest-lab-add-vm-with-artifacts.md)」を参照してください。
    * **詳細設定** - これを選択すると、**[詳細設定]** ブレードが開きます。このブレードで次の設定を構成します。
        * **仮想ネットワーク** - 目的の仮想ネットワークを選んでタップします。
        * **サブネット** - 目的のサブネットを指定します。    
        * **IP アドレス構成** -パブリック IP アドレス、プライベート IP アドレス、または共有 IP アドレスが必要かどうかを指定します。 共有 IP アドレスの詳細については、[Azure DevTest Labs の共有 IP アドレスに関する説明](./devtest-lab-shared-ip.md)をご覧ください。
        * **このコンピューターを要求可能にする** - コンピューターを "要求可能" にすると、作成時に所有権が割り当てられません。 代わりに、ラボ ユーザーが、ラボのブレードでコンピューターの所有権を取得 ("要求") できるようになります。     
    * **イメージ** - このフィールドには、前のブレードで選択した基本イメージの名前が表示されます。 
     
       ![[Create formula]](./media/devtest-lab-create-formulas/create-formula.png)

8. **[作成]** を選択すると、数式が作成されます。

9. 数式が作成されると、**[数式]** ブレードの数式の一覧に表示されます。

### <a name="create-a-formula-from-a-vm"></a>VM から数式を作成する
既存の VM に基づいて数式を作成する手順は次のとおりです。 

> [!NOTE]
> VM から数式を作成するには、2016 年 3 月 30 日以降に作成された VM である必要があります。 
> 
> 

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
2. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。  
4. ラボの **[概要]** ブレードで、数式の作成元にする VM を選択します。
   
    ![Labs VMs](./media/devtest-lab-create-formulas/my-vms.png)
5. VM のブレードで、 **[数式の作成 (再利用可能なベース)]**を選択します。
   
    ![[Create formula]](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. **[Create formula (数式の作成)]** ブレードで、新しい数式の**名前**と**説明**を入力します。
   
    ![[Create Formula] (数式の作成) ブレード](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. **[OK]** を選択して数式を作成します。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>関連するブログ記事
* [Custom images or formulas? (カスタム イメージか数式か?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>次のステップ
- [Azure DevTest Labs でラボに VM を追加する](devtest-lab-add-vm.md)
- [Azure DevTest Labs 数式を管理する](devtest-lab-manage-formulas.md)
