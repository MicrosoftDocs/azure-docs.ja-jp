---
title: "VM を作成するための Azure DevTest Labs 数式の管理 | Microsoft Docs"
description: "Azure DevTest Labs 数式を更新および削除する方法について説明します"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bfdab5def50158f9b764bbb1e50c2624cc6d5fb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-devtest-labs-formulas"></a>Azure DevTest Labs 数式の管理

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

この記事では、ベース (カスタム イメージ、Marketplace イメージ、他の数式) または既存の VM から数式を作成する方法について説明します。 また既存の数式を管理する方法についても説明します。

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

## <a name="modify-a-formula"></a>数式の変更
数式を変更するには、次の手順に従います。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
2. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。  
4. ラボのブレードで、 **[数式 (再利用可能なベース)]**を選択します。
   
    ![Formula menu](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. **[Lab formulas]** (ラボ数式) ブレードで、変更する数式を選択します。
6. **[Update formula (数式の更新)]** ブレードで、必要な編集を行い、**[更新]** を選択します。

## <a name="delete-a-formula"></a>数式の削除
数式を削除するには、次の手順に従います。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
2. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。  
4. ラボの **[設定]** ブレードで、**[Formulas (数式)]** を選択します。
   
    ![Formula menu](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. **[Lab formulas]** (ラボ数式) ブレードで、削除する数式の右側にある省略記号を選択します。
   
    ![Formula menu](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. 数式のコンテキスト メニューで、 **[削除]**を選択します。
   
    ![Formula context menu](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. 削除の確認のダイアログ ボックスで **[はい]** を選択します。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>関連するブログ記事
* [Custom images or formulas? (カスタム イメージか数式か?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>次のステップ
VM の作成時に使用する数式を作成したら、次は [VM をラボに追加](devtest-lab-add-vm-with-artifacts.md)します。

