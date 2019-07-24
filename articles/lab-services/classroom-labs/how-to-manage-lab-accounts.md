---
title: Azure Lab Services でラボ アカウントを管理する | Microsoft Docs
description: Azure サブスクリプションに対するラボ アカウントの作成、すべてのラボ アカウントの表示、またはラボ アカウントの削除を行う方法を説明します。
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
ms.date: 02/07/2018
ms.author: spelluru
ms.openlocfilehash: f1194d8385d1e7ddcb906d0c8c3a2b56648e2547
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58120824"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Azure Lab Services でラボ アカウントを管理する 
Azure Lab Services では、ラボ アカウントは、クラスルーム ラボなどのマネージド ラボ型のコンテナーです。 管理者は、Azure Lab Services を使用してラボ アカウントを設定し、アカウントにラボを作成できるラボ所有者にアクセスを提供します。 この記事では、ラボ アカウントの作成、すべてのラボ アカウントの表示、またはラボ アカウントの削除を行う方法について説明します。

## <a name="create-a-lab-account"></a>ラボ アカウントを作成する
次の手順では、Azure portal を使って Azure Lab Services でラボ アカウントを作成する方法を示します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューから、**[すべてのサービス]** を選択します。 **[DEVOPS]** セクションで、**[ラボ アカウント]** を選択します。 **[ラボ アカウント]** の横にある星印 (`*`) を選択した場合は、左側のメニューの **[お気に入り]** セクションに追加されます。 次回以降は、**[お気に入り]** の **[ラボ アカウント]** を選択します。

    ![[すべてのサービス] -> [ラボ アカウント]](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. **[ラボ アカウント]** ページで、ツール バーの **[追加]** を選択します。 

    ![[ラボ アカウント] ページで [追加] を選択する](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. **[ラボ アカウント]** ページで、次のようにします。 
    1. **[Lab account name]\(ラボ アカウント名\)** に、名前を入力します。 
    2. ラボ アカウントを作成する **[Azure サブスクリプション]** を選択します。
    3. **[リソース グループ]** で、**[新規作成]** を選択し、リソース グループの名前を入力します。
    4. **[場所]** で、ラボ アカウントが作成される場所/リージョンを選択します。 
    5. **[仮想ネットワークのピアリング]** には、ラボ ネットワークのピア仮想ネットワーク (VNet) を選択します。 このアカウントで作成したラボは、選択した VNet に接続され、そこにあるリソースにアクセスすることができます。 
    7. **[Allow lab creator to pick lab location]\(ラボの作成者にラボの場所の選択を許可する\)** フィールドで、ラボの作成者がラボの場所を選択できるようにするかどうかを指定します。 既定では、このオプションは無効です。 無効である場合、ラボの作成者は作成するラボの場所を指定できません。 ラボは、ラボ アカウントに地理的に最も近い場所に作成されます。 有効である場合、ラボの作成者はラボの作成時に場所を選択できます。      
    8. **作成**を選択します。 

        ![ラボ アカウントの作成ウィンドウ](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. ツールバーの**ベル アイコン** (**[通知]**) を選択し、デプロイが成功したことを確認して、**[リソースに移動]** を選択します。 

    または、**[ラボ アカウント]** ページの **[更新]** を選択し、作成したラボ アカウントを選択します。 

    ![ラボ アカウントの作成ウィンドウ](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. 次の **[Lab account]\(ラボ アカウント\)** ページが表示されます。

    ![[Lab account]\(ラボ アカウント\) ページ](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>ユーザーをラボの作成者ロールに追加する
ラボ アカウントでクラスルーム ラボを設定するには、ユーザーがラボ アカウントにおける**ラボの作成者**ロールのメンバーであることが必要です。 ラボ アカウントを作成するために使用したアカウントは、このロールに自動的に追加されます。 クラスルーム ラボの作成に、同じユーザー アカウントを使用する場合は、この手順をスキップすることができます。 クラスルーム ラボの作成に、別のユーザー アカウントを使用する場合は、次の手順に従います。 

クラスのラボを作成するアクセス許可を教師に与えるには、教師を**ラボの作成者**ロールに追加します。

1. **[ラボ アカウント]** ページで、**[アクセス制御 (IAM)]** を選択し、ツールバーの **[+ ロール割り当ての追加]** をクリックします。 

    ![[アクセス制御] -> [ロールの割り当ての追加] ボタン](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. **[ロールの割り当ての追加]** ページで、**[ロール]** から **[ラボの作成者]** を選択し、ラボの作成者ロールに追加するユーザーを選択した後、**[保存]** を選択します。 

    ![ラボ作成者の追加](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="specify-marketplace-images-available-to-lab-creators"></a>ラボ作成者が利用できる Marketplace イメージを指定する
ラボ アカウント所有者は、ラボ作成者がラボ アカウントにラボを作成するために使用できる Marketplace イメージを指定できます。 

1. 左側のメニューで、**[Marketplace イメージ]** を選択します。 既定では、イメージの完全な一覧 (有効なものと無効なものの両方) が表示されます。 上部のドロップダウン リストから **[有効のみ]**/**[無効のみ]** オプションを選択することにより、一覧をフィルター処理して、有効/無効のイメージのみを表示することができます。 
    
    ![Marketplace イメージのページ](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    一覧に表示される Marketplace イメージは、次の条件を満たすものだけです。
        
    - 単一の VM を作成する。
    - Azure Resource Manager を使用して VM をプロビジョニングする
    - 追加のライセンス プランの購入を必要としない
2. 有効になっている Marketplace イメージを**無効にする**には、次のいずれかの操作を行います。 
    1. 最後の列で **[...] (省略記号)** を選択し、**[Disable image]\(イメージの無効化\)** を選択します。 

        ![1 つのイメージを無効にする](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. 一覧内のイメージ名の前のチェック ボックスをオンにして、一覧から 1 つ以上のイメージを選択し、**[Disable selected images]\(選択したイメージの無効化\)** を選択します。 

        ![複数のイメージを無効にする](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. 同様に、Marketplace イメージを**有効にする**には、次のいずれかの操作を行います。 
    1. 最後の列で **[...] (省略記号)** を選択し、**[Enable image]\(イメージの有効化\)** を選択します。 
    2. 一覧内のイメージ名の前のチェック ボックスをオンにして、一覧から 1 つ以上のイメージを選択し、**[Enable selected images]\(選択したイメージの有効化\)** を選択します。 

## <a name="configure-the-lab-account"></a>ラボ アカウントを構成する
1. **[ラボ アカウント]** ページで、左側のメニューの **[Labs configuration]\(ラボの構成\)** を選択します。

    ![[Labs configuration]\(ラボの構成\) ページ](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. **[仮想ネットワークのピアリング]** で **[有効]** または **[無効]** を選択します。 既定値は **[無効]** です。 仮想ネットワークのピアリングを有効にするには、次の手順を実行します。 
    1. **[Enabled]** を選択します。
    2. ドロップダウン リストから **[VNet]** を選択します。 
    3. ツールバーの **[保存]** を選択します。 
    
        このアカウントで作成されたラボは、選択した仮想ネットワークに接続されます。 また、選択した仮想ネットワーク内のリソースにアクセスできます。 
3. **[Allow lab creator to pick lab location]\(ラボ作成者にラボの場所の選択を許可する\)** フィールドで、ラボの作成者がラボの場所を選択できるようにする場合は **[有効]** を選択します。 無効にすると、ラボ アカウントが存在している同じ場所にラボが自動的に作成されます。 

## <a name="view-lab-accounts"></a>ラボ アカウントを表示する
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. メニューから **[すべてのリソース]** を選択します。 
3. **[種類]** で **[ラボ アカウント]** を選択します。 
    サブスクリプション、リソース グループ、場所、およびタグでフィルター処理することもできます。 

    ![[すべてのリソース] -> [ラボ アカウント]](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>ラボ アカウント内のラボの表示と管理

1. **[ラボ アカウント]** ページで、左側のメニューの **[ラボ]** を選択します。

    ![アカウント内のラボ](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. アカウント内の**ラボの一覧**に、次の情報が表示されます。 
    1. ラボの名前。
    2. ラボが作成された日付。 
    3. ラボを作成したユーザーのメール アドレス。 
    4. ラボ内で許可されるユーザーの最大数。 
    5. ラボの状態。 



## <a name="delete-a-lab-in-the-lab-account"></a>ラボ アカウント内のラボを削除する
前のセクションの手順に従って、ラボ アカウント内のラボの一覧を表示します。

1. **... (省略記号)** を選択し、**[削除]** を選択します。 

    ![ラボを削除する - ボタン](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. 警告メッセージで **[はい]** を選択します。 

    ![ラボ削除の確認](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>ラボ アカウントを削除する
前のセクションの指示に従って、一覧にラボ アカウントを表示します。 次の手順を使用して、ラボ アカウントを削除します。 

1. 削除する**ラボ アカウント**を選択します。 
2. ツールバーから **[削除]** を選択します。 

    ![[ラボ アカウント] -> [削除] ボタン](../media/how-to-manage-lab-accounts/delete-button.png)
1. **[はい]** を選択して確認します。
1. **[削除]** を選択します。 

    ![ラボ アカウントの削除 - 確認](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)



## <a name="next-steps"></a>次の手順
次の記事を参照してください。

- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてテンプレートを設定および発行する](how-to-create-manage-template.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
- [ラボ ユーザーとしてクラスルーム ラボにアクセスする](how-to-use-classroom-lab.md)