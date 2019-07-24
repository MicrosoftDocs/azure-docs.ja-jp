---
title: Azure Lab Services でラボ アカウントを設定する | Microsoft Docs
description: このチュートリアルでは、Azure Lab Services でラボ アカウントを設定します。
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
ms.date: 03/29/2019
ms.author: spelluru
ms.openlocfilehash: 962b69a97b8116b82878a0a82960c9159091a9a7
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652622"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>チュートリアル:Azure Lab Services でラボ アカウントを設定する
Azure Lab Services では、ラボ アカウントは、それによって組織のラボが管理される中心的なアカウントとして機能します。 ラボ アカウントでは、ラボを作成する権限を他のユーザーに付与し、ラボ アカウントの管理下にあるすべてのラボに適用されるポリシーを設定します。 このチュートリアルでは、ラボ管理者としてラボ アカウントを作成する方法について説明します。 

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * ラボ アカウントを作成する
> * ユーザーをラボの作成者ロールに追加する
> * ラボ所有者が利用できる Marketplace イメージを指定する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="create-a-lab-account"></a>ラボ アカウントを作成する
次の手順では、Azure portal を使って Azure Lab Services でラボ アカウントを作成する方法を示します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューから、**[すべてのサービス]** を選択します。 **[DevOps]** セクションで、**[ラボ サービス]** を選択します。 **[ラボ サービス]** の横にある星印 (`*`) を選択した場合は、左側のメニューの **[お気に入り]** セクションに追加されます。 次回以降は、**[お気に入り]** の下で **[ラボ サービス]** を選択します。

    ![[すべてのサービス] -> [ラボ サービス]](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. **[ラボ サービス]** ページで、ツール バーの **[追加]** を選択します。 

    ![[ラボ アカウント] ページで [追加] を選択する](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. **[ラボ アカウント]** ページで、次のようにします。 
    1. **[Lab account name]\(ラボ アカウント名\)** に、名前を入力します。 
    2. ラボ アカウントを作成する **[Azure サブスクリプション]** を選択します。
    3. **[リソース グループ]** で、**[新規作成]** を選択し、リソース グループの名前を入力します。
    4. **[場所]** で、ラボ アカウントが作成される場所/リージョンを選択します。 
    5. **[仮想ネットワークのピアリング]** には、ラボ ネットワークのピア仮想ネットワーク (VNet) を選択します。 このアカウントで作成したラボは、選択した VNet に接続され、そこにあるリソースにアクセスすることができます。 
    6. **[Allow lab creator to pick lab location]\(ラボの作成者にラボの場所の選択を許可する\)** フィールドで、ラボの作成者がラボの場所を選択できるようにするかどうかを指定します。 既定では、このオプションは無効です。 無効である場合、ラボの作成者は作成するラボの場所を指定できません。 ラボは、ラボ アカウントに地理的に最も近い場所に作成されます。 有効である場合、ラボの作成者はラボの作成時に場所を選択できます。 
    7. **作成**を選択します。 

        ![ラボ アカウントの作成ウィンドウ](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. ツールバーの**ベル アイコン** (**[通知]**) を選択し、デプロイが成功したことを確認して、**[リソースに移動]** を選択します。 

    または、**[ラボ アカウント]** ページの **[更新]** を選択し、作成したラボ アカウントを選択します。 

    ![ラボ アカウントの作成ウィンドウ](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. 次の **[Lab account]\(ラボ アカウント\)** ページが表示されます。

    ![[Lab account]\(ラボ アカウント\) ページ](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>ユーザーをラボの作成者ロールに追加する
ラボ アカウントでクラスルーム ラボを設定するには、ユーザーがラボ アカウントにおける**ラボの作成者**ロールのメンバーであることが必要です。 ラボ アカウントを作成するために使用したアカウントは、このロールに自動的に追加されます。 クラスルーム ラボの作成に、同じユーザー アカウントを使用する場合は、この手順をスキップすることができます。 クラスルーム ラボの作成に、別のユーザー アカウントを使用する場合は、次の手順に従います。 

クラスのラボを作成するアクセス許可を教師に与えるには、教師を**ラボの作成者**ロールに追加します。

1. **[ラボ アカウント]** ページで、**[アクセス制御 (IAM)]** を選択し、ツール バーの **[+ 追加]** を選択してから、ツール バーの **[+ ロール割り当ての追加]** を選択します。 

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

## <a name="next-steps"></a>次の手順
このチュートリアルでは、ラボ アカウントを作成しました。 専門職としてクラスルーム ラボを作成する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [クラスルーム ラボを設定する](tutorial-setup-classroom-lab.md)

