---
title: Azure Lab Services でラボ アカウントを管理する | Microsoft Docs
description: Azure サブスクリプションに対するラボ アカウントの作成、すべてのラボ アカウントの表示、またはラボ アカウントの削除を行う方法を説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 65ff28401b73698e320735613b32c96bf3de58f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434092"
---
# <a name="create-and-manage-lab-accounts"></a>ラボ アカウントを作成および管理する
Azure Lab Services では、ラボ アカウントは、ラボなどのマネージド ラボ型のコンテナーです。 管理者は、Azure Lab Services を使用してラボ アカウントを設定し、アカウントにラボを作成できるラボ所有者にアクセスを提供します。 この記事では、ラボ アカウントの作成、すべてのラボ アカウントの表示、またはラボ アカウントの削除を行う方法について説明します。

## <a name="create-a-lab-account"></a>ラボ アカウントを作成する
次の手順では、Azure portal を使って Azure Lab Services でラボ アカウントを作成する方法を示します。 

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 左側のメニューから、 **[すべてのサービス]** を選択します。 **[DevOps]** セクションで、 **[ラボ アカウント]** を選択します。 **[ラボ アカウント]** の横にある星印 (`*`) を選択した場合は、左側のメニューの **[お気に入り]** セクションに追加されます。 次回以降は、 **[お気に入り]** の **[ラボ アカウント]** を選択します。

    ![[すべてのサービス] -> [ラボ アカウント]](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. **ラボ アカウント** ページで、ツールバーの **[追加]** を選択するか、ページの **[ラボ アカウントの作成]** を選択します。 

    ![[ラボ アカウント] ページで [追加] を選択する](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. **[ラボ アカウントを作成します]** ページの **[Basics]\(基本\)** タブで、次の操作を行います。 
    1. **[Lab account name]\(ラボ アカウント名\)** に、名前を入力します。 
    2. ラボ アカウントを作成する **[Azure サブスクリプション]** を選択します。
    3. **[リソース グループ]** で、 **[新規作成]** を選択し、リソース グループの名前を入力します。
    4. **[場所]** で、ラボ アカウントが作成される場所/リージョンを選択します。
    5. **[Allow lab creator to pick lab location]\(ラボの作成者にラボの場所の選択を許可する\)** フィールドで、ラボの作成者がラボの場所を選択できるようにするかどうかを指定します。 既定では、このオプションは無効です。 無効である場合、ラボの作成者は作成するラボの場所を指定できません。 ラボは、ラボ アカウントに地理的に最も近い場所に作成されます。 有効である場合、ラボの作成者はラボの作成時に場所を選択できます。 詳細については、 「[ラボの作成者にラボ用の場所を選択できるようにする](allow-lab-creator-pick-lab-location.md)」 を参照してください。 

        ![ラボ アカウントの作成 -> 基本](./media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. **ページ下部の [詳細設定]** から **[詳細設定]** タブに移動し、次の手順を実行します。 
    1. 既存の **共有イメージ ギャラリー** を選択するか、作成します。 他のユーザーによって再使用されるようにテンプレート VM を共有イメージ ギャラリーに保存することができます。 共有イメージ ギャラリーの詳細については、「[Azure Lab Services で共有イメージ ギャラリーを使用する](how-to-use-shared-image-gallery.md)」を参照してください。
    2. ユーザーが Windows 仮想マシンを切断した場合、**Windows 仮想マシンを自動的にシャットダウン** するかどうかを指定します。 VM が自動的にシャットダウンされる前にユーザーの再接続を待つ時間の長さを指定することもできます。 
    3. **[仮想ネットワークのピアリング]** には、ラボ ネットワークのピア仮想ネットワーク (VNet) を選択します。 このアカウントで作成したラボは、選択した VNet に接続され、そこにあるリソースにアクセスすることができます。 詳細については、「[ラボの仮想ネットワークとピア仮想ネットワークを接続する](how-to-connect-peer-virtual-network.md)」を参照してください。    
    8. ラボ内の VM の **アドレスの範囲** を指定します。 アドレスの範囲は、Classless Inter-Domain Routing (CIDR) 表記で指定する必要があります (例: 10.20.0.0/23)。 ラボ内の仮想マシンはこのアドレスの範囲に作成されます。 詳細については、「[ラボ内の VM のアドレス範囲を指定する](how-to-connect-peer-virtual-network.md#specify-an-address-range-for-vms-in-the-lab-account)」を参照してください。  

        > [!NOTE]
        > **アドレス範囲** プロパティは、ラボで **ピア仮想ネットワーク** が有効になっている場合にのみ適用されます。

        ![ラボアカウントの作成 -> 詳細](./media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. **ページ下部のタグ** を **タブ** に切り替えます。ラボアカウントに関連付けるタグを追加します。 タグは名前と値のペアで、同じタグを複数のリソースやリソース グループに適用することでリソースを分類したり、統合した請求を表示したりできるようにします。 詳細については、[タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)に関するページを参照してください。

    ![[ラボアカウントの作成] ページのスクリーンショット。[タグ] タブが強調表示されています。](./media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. このページの下部にある **[レビュー+作成]** を選択して、 **[レビュー+作成]** タブに切り替えます。 
4. このページの概要情報を確認し、 **[作成]** を選択します。 

    ![ラボアカウントの作成 -> タグ](./media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. 配置が完了するまで待ち、 **「次の手順」** を展開し、次の図に示すように **「リソースに移動」** を選択します。 

    また、ツールバーの **ベル アイコン** (**通知**)を選択し、デプロイが成功したことを確認してから、 **[リソースに移動]** を選択することもできます。 

    または、 **[ラボ アカウント]** ページの **[更新]** を選択し、作成したラボ アカウントを選択します。 

    ![ラボ アカウントの作成ウィンドウ](./media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. 次の **[Lab account]\(ラボ アカウント\)** ページが表示されます。

    ![[Lab account]\(ラボ アカウント\) ページ](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>ラボ アカウントを表示する
1. [Azure portal](https://portal.azure.com) にサインインします。
2. メニューから **[すべてのリソース]** を選択します。 
3. **[種類]** で **[ラボ アカウント]** を選択します。 
    サブスクリプション、リソース グループ、場所、およびタグでフィルター処理することもできます。 

    ![[すべてのリソース] -> [ラボ アカウント]](./media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>ラボ アカウントを削除する
前のセクションの指示に従って、一覧にラボ アカウントを表示します。 次の手順を使用して、ラボ アカウントを削除します。 

1. 削除する **ラボ アカウント** を選択します。 
2. ツールバーから **[削除]** を選択します。 

    ![[ラボ アカウント] -> [削除] ボタン](./media/how-to-manage-lab-accounts/delete-button.png)
1. **[はい]** を選択して確認します。
1. **[削除]** を選択します。 

    ![ラボ アカウントの削除 - 確認](./media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Az.LabServices PowerShell モジュール (プレビュー) を使用して、ラボ アカウントを管理することもできます。 詳しくは、[GitHub の Az.LabServices ホーム ページ](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)をご覧ください。

## <a name="next-steps"></a>次のステップ
目次の「**操作方法ガイド**」 -> 「**ラボ アカウントを作成および構成する (ラボ アカウント オーナー)** 」の他の記事を見る。 