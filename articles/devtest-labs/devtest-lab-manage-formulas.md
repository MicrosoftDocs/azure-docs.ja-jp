---
title: VM を作成するための Azure DevTest Labs 数式の管理 | Microsoft Docs
description: この記事では、ベース (カスタム イメージ、Marketplace イメージ、他の数式) または既存の VM から数式を作成する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f012a8c59a8e938334c3e1abc4c7b3ccd0e48d3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91308896"
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

1. [Azure portal](https://portal.azure.com) にサインインします。

2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

3. ラボの一覧で目的のラボを選択します。  

4. ラボのページで、左側のメニューの **[数式 (再利用可能なベース)]** を選択します。
5. **[数式]** ページで、 **[+ 追加]** を選択します。
   
    ![数式の追加](./media/devtest-lab-create-formulas/add-formula.png)
6. **[ベースの選択]** ページで、数式を作成する際のベース (カスタム イメージ、Marketplace イメージ、または Shared Image Gallery イメージ) を選択します。

    :::image type="content" source="./media/devtest-lab-create-formulas/select-base.png" alt-text="基本イメージを選択する":::
1. **[Create formula]\(数式の作成\)** ページの **[Basic Settings]\(基本設定\)** タブで、次の値を指定します。
   
    * **Formula name** (数式の名前) - 数式の名前を入力します。 この値は、VM を作成するときに基本イメージの一覧に表示されます。 入力した名前は検証され、有効でない場合は有効な名前の要件を示すメッセージが表示されます。
    - 必要に応じて、数式の **説明** を入力します。 
    * **ユーザー名** - 管理者特権を付与するユーザー名を入力します。
    * **パスワード** - 指定されたユーザーに使用するシークレット (パスワード) に関連付けられている値を入力、またはドロップダウン リストから選択します。 キー コンテナーにシークレットを保存し、ラボ リソースの作成時に使用する方法については、[Azure Key Vault にシークレットを格納する](devtest-lab-store-secrets-in-key-vault.md)方法に関する記事を参照してください。

        パスワードを使用する代わりに Azure Key Vault からシークレットを使用する場合は、 **[保存されているシークレットを使用する]** を選択します。 
    * **仮想マシンのサイズ** - VM のサイズを変更するには、 **[サイズの変更]** を選択します。 
    - **OS ディスクの種類** - 使用するディスクの種類 (Standard HDD、Standard SSD、または Premium SSD) を選択します。
    * **成果物** - **[成果物を追加または削除]** ページを選択し、基本イメージに追加する成果物を選択して構成します。 アーティファクトの詳細については、「[Azure DevTest Labs 仮想マシンのカスタム アーティファクトの作成](devtest-lab-artifact-author.md)」をご覧ください。

        ![[基本設定] ページ](./media/devtest-lab-create-formulas/basic-settings.png)
8. **[詳細設定]** タブに切り替えて、次の値を指定します。
    - **仮想ネットワーク** - 仮想ネットワークを変更するには、 **[Vnet の変更]** を選択します。 
    - **サブネット** - サブネットを変更するには、 **[サブネットの変更]** を選択します。 
    - **IP アドレス構成** -パブリック IP アドレス、プライベート IP アドレス、または共有 IP アドレスが必要かどうかを指定します。 共有 IP アドレスの詳細については、[Azure DevTest Labs の共有 IP アドレスに関する説明](./devtest-lab-shared-ip.md)をご覧ください。
    - **有効期限日時** - このフィールドを編集することはできません。 
    - **このコンピューターを要求可能にする** - コンピューターを "要求可能" にすると、作成時に所有権が割り当てられません。 代わりに、ラボ ユーザーが、ラボのページでコンピューターの所有権を取得 ("要求") できるようになります。  

        ![[数式の作成 (再利用可能なベース)] ページの [詳細設定] を示すスクリーンショット。](./media/devtest-lab-create-formulas/advanced-settings.png)
    - ベースとして Shared Image Gallery イメージを選択した場合は、ベースとして使用するイメージのバージョンをギャラリーから選択できる **[イメージ バージョン]** フィールドも表示されます。 

        ![[詳細設定] ページ](./media/devtest-lab-create-formulas/advanced-settings-shared-image-gallery.png)
8. **[送信]** を選択して数式を作成します。

9. 数式が作成されると、 **[数式]** ページの一覧に表示されます。

### <a name="create-a-formula-from-a-vm"></a>VM から数式を作成する
既存の VM に基づいて数式を作成する手順は次のとおりです。 

> [!NOTE]
> VM から数式を作成するには、2016 年 3 月 30 日以降に作成された VM である必要があります。 
> 
> 

1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。  
4. ラボの **[概要]** ページで、数式の作成元にする VM を選択します。
   
    ![Labs VMs](./media/devtest-lab-create-formulas/my-vms.png)
5. VM のページで、 **[数式の作成 (再利用可能なベース)]** を選択します。
   
    ![[Create formula]](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. **[Create formula]\(数式の作成\)** ページで、新しい数式の **[名前]** と **[説明]** を入力します。
   
    ![[Create formula]\(数式の作成\) ページ](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. **[OK]** を選択して数式を作成します。

## <a name="modify-a-formula"></a>数式の変更
数式を変更するには、次の手順に従います。

1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。  
4. ラボのページで、 **[数式 (再利用可能なベース)]** を選択します。
   
    ![[数式 (再利用可能なベース)] が選択されたラボのページを示すスクリーンショット。](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. **[Lab formulas]\(ラボの数式\)** ページで、変更する数式を選択します。
6. **[数式の更新]** ページで、必要な編集を行い、 **[更新]** を選択します。

## <a name="delete-a-formula"></a>数式の削除
数式を削除するには、次の手順に従います。

1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。  
4. ラボの **[設定]** ページで、 **[数式]** を選択します。
   
    ![Formula menu](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. **[Lab formulas]\(ラボの数式\)** ページで、削除する数式の右側にある省略記号を選択します。
   
    ![[Lab formulas]\(ラボの数式\) ページで数式のオプションの省略記号が強調表示されたスクリーンショット。](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. 数式のコンテキスト メニューで、 **[削除]** を選択します。
   
    ![Formula context menu](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. 削除の確認のダイアログ ボックスで **[はい]** を選択します。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>関連するブログ記事
* [Custom images or formulas? (カスタム イメージか数式か?)](devtest-lab-faq.md#what-is-the-difference-between-a-custom-image-and-a-formula)

## <a name="next-steps"></a>次のステップ
VM の作成時に使用する数式を作成したら、次は [VM をラボに追加](devtest-lab-add-vm.md)します。

