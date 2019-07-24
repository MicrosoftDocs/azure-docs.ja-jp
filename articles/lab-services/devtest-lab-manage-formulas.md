---
title: VM を作成するための Azure DevTest Labs 数式の管理 | Microsoft Docs
description: Azure DevTest Labs 数式を更新および削除する方法について説明します
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: d8f2ae64e2f8e694de5a7cf5aa9049e63998dca0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452659"
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

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

3. ラボの一覧で目的のラボを選択します。  

4. ラボのページで、**[数式 (再利用可能なベース)]** を選択します。
   
    ![Formula menu](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. **[数式]** ページで、**[+ 追加]** を選択します。
   
    ![数式の追加](./media/devtest-lab-create-formulas/add-formula.png)

6. **[ベースの選択]** ページで、数式を作成する際のベース (カスタム イメージ、Marketplace イメージ、または数式) を選択します。
   
    ![Base list](./media/devtest-lab-create-formulas/base-list.png)

7. **[Create formula]\(数式の作成\)** ページの **[Basic Settings]\(基本設定\)** タブで、次の値を指定します。
   
    * **Formula name** (数式の名前) - 数式の名前を入力します。 この値は、VM を作成するときに基本イメージの一覧に表示されます。 入力した名前は検証され、有効でない場合は有効な名前の要件を示すメッセージが表示されます。
    * **ユーザー名** - 管理者特権を付与するユーザー名を入力します。
    * **パスワード** - 指定されたユーザーに使用するシークレット (パスワード) に関連付けられている値を入力、またはドロップダウン リストから選択します。 キー コンテナーにシークレットを保存し、ラボ リソースの作成時に使用する方法については、[Azure Key Vault にシークレットを格納する](devtest-lab-store-secrets-in-key-vault.md)方法に関する記事を参照してください。
    * **VM サイズ** - VM のサイズを変更するには、**[サイズの変更]** を選択します。 
    * **成果物** - **[成果物を追加または削除]** ページを選択し、基本イメージに追加する成果物を選択して構成します。 アーティファクトの詳細については、「[Azure DevTest Labs 仮想マシンのカスタム アーティファクトの作成](devtest-lab-artifact-author.md)」をご覧ください。
8. **[詳細設定]** タブに切り替えて、次の値を指定します。
    - **仮想ネットワーク** - 仮想ネットワークを変更するには、**[Vnet の変更]** を選択します。 
    - **サブネット** - サブネットを変更するには、**[サブネットの変更]** を選択します。 
    - **IP アドレス構成** -パブリック IP アドレス、プライベート IP アドレス、または共有 IP アドレスが必要かどうかを指定します。 共有 IP アドレスの詳細については、[Azure DevTest Labs の共有 IP アドレスに関する説明](./devtest-lab-shared-ip.md)をご覧ください。
    - **Expiration date and time (有効期限の日時)** - VM が自動的に削除されるように、VM の有効期限の日付と時刻を指定します。 
    - **このコンピューターを要求可能にする** - コンピューターを "要求可能" にすると、作成時に所有権が割り当てられません。 代わりに、ラボ ユーザーが、ラボのページでコンピューターの所有権を取得 ("要求") できるようになります。     
    - **Number of claimable instances (要求可能なインスタンスの数)** - 作成する要求可能なインスタンスの数を指定します。 
8. **[送信]** を選択して数式を作成します。

9. 数式が作成されると、**[数式]** ページの一覧に表示されます。

### <a name="create-a-formula-from-a-vm"></a>VM から数式を作成する
既存の VM に基づいて数式を作成する手順は次のとおりです。 

> [!NOTE]
> VM から数式を作成するには、2016 年 3 月 30 日以降に作成された VM である必要があります。 
> 
> 

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。  
4. ラボの **[概要]** ページで、数式の作成元にする VM を選択します。
   
    ![Labs VMs](./media/devtest-lab-create-formulas/my-vms.png)
5. VM のページで、**[数式の作成 (再利用可能なベース)]** を選択します。
   
    ![[Create formula]](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. **[Create formula]\(数式の作成\)** ページで、新しい数式の **[名前]** と **[説明]** を入力します。
   
    ![[Create formula]\(数式の作成\) ページ](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. **[OK]** を選択して数式を作成します。

## <a name="modify-a-formula"></a>数式の変更
数式を変更するには、次の手順に従います。

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。  
4. ラボのページで、**[数式 (再利用可能なベース)]** を選択します。
   
    ![Formula menu](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. **[Lab formulas]\(ラボの数式\)** ページで、変更する数式を選択します。
6. **[数式の更新]** ページで、必要な編集を行い、**[更新]** を選択します。

## <a name="delete-a-formula"></a>数式の削除
数式を削除するには、次の手順に従います。

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。  
4. ラボの **[設定]** ページで、**[数式]** を選択します。
   
    ![Formula menu](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. **[Lab formulas]\(ラボの数式\)** ページで、削除する数式の右側にある省略記号を選択します。
   
    ![Formula menu](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. 数式のコンテキスト メニューで、 **[削除]** を選択します。
   
    ![Formula context menu](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. 削除の確認のダイアログ ボックスで **[はい]** を選択します。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>関連するブログ記事
* [Custom images or formulas? (カスタム イメージか数式か?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>次の手順
VM の作成時に使用する数式を作成したら、次は [VM をラボに追加](devtest-lab-add-vm.md)します。

