---
title: Azure Maps Creator を管理する
description: この記事では Azure Maps Creator を管理する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 579294388dbcf9f785ef41e06505c14b6767565f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "83677950"
---
# <a name="manage-azure-maps-creator"></a>Azure Maps Creator を管理する

Azure Maps Creator を使用すると、プライベートな屋内マップ データを作成できます。 Azure Maps API と Indoor Maps モジュールを使用して、対話型で動的な屋内マップ Web アプリケーションを開発できます。 現在、Creator は、S1 価格レベルを使用して米国内でのみ利用できます。

この記事では、Azure Maps アカウントで Creator リソースを作成および削除する手順について説明します。

## <a name="create-creator-resource"></a>Creator リソースを作成する

1. [Azure ポータル](https://portal.azure.com)

2. Azure Maps アカウントを選択します。 **[最近のリソース]** に Azure Maps アカウントが表示されない場合は、Azure portal のメニューに移動します。 **[すべてのリソース]** を選択します。 自分の Azure Maps アカウントを探して選択します。

    ![Azure Maps ポータルのホーム ページ](./media/how-to-manage-creator/select-maps-account.png)

3. Azure Maps アカウントのページに移動したら、 **[Creator]** の下にある **[概要]** オプションに移動します。 **[作成]** をクリックして、Azure Maps Creator リソースを作成します。

    ![Azure Maps Creator 作成ページ](./media/how-to-manage-creator/creator-blade-settings.png)

4. Creator リソースの名前と場所を入力します。 現在、Creator は米国でのみサポートされています。 **[Review + create]\(レビュー + 作成\)** をクリックします。

   ![Creator アカウント情報入力ページ](./media/how-to-manage-creator/creator-creation-dialog.png)

5. 設定を確認し、 **[作成]** をクリックします。

    ![Creator アカウント設定確認ページ](./media/how-to-manage-creator/creator-create-dialog.png)

6. デプロイが完了すると、成功または失敗のメッセージを含むページが表示されます。

   ![リソース デプロイ状態ページ](./media/how-to-manage-creator/creator-resource-created.png)

7. **[リソースに移動]** をクリックします。 Creator リソース ビュー ページには、Creator リソースの状態と選択されている地域が表示されます。

    ![Creator 状態ページ](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >Creator リソース ページからは、[Azure Maps アカウント] をクリックすることにより、それが属している Azure Maps アカウントに戻ることができます。

## <a name="delete-creator-resource"></a>Creator リソースを削除する

Creator リソースを削除するには、Azure Maps アカウントに移動します。 **[Creator]** の下の **[概要]** を選択します。 **[削除]** ボタンをクリックします。

>[!WARNING]
>Azure Maps アカウントの Creator リソースを削除すると、Creator サービスを使用して作成したデータセット、タイルセット、地物状態セットも削除されます。

![削除ボタンがある Creator ページ](./media/how-to-manage-creator/creator-delete.png)

**[削除]** ボタンをクリックして Creator 名を入力し、削除を確認します。 リソースが削除されると、次の図のような確認ページが表示されます。

![削除の確認が表示された Creator ページ](./media/how-to-manage-creator/creator-confirmdelete.png)

## <a name="authentication"></a>認証

Creator は Azure Maps Access Control (IAM) の設定を継承します。 データにアクセスするすべての API 呼び出しは、認証および承認の規則と共に送信される必要があります。

Creator の使用状況データは、Azure Maps の使用状況グラフとアクティビティ ログに組み込まれています。  詳細については、「[Azure Maps での認証の管理](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)」をご覧ください。

## <a name="access-to-creator-services"></a>Creator サービスにアクセスする

Creator サービスには、作成時に選択した場所からのみアクセスできます。 選択した場所の外部から Creator サービスに対する呼び出しが行われると、ユーザー エラー メッセージが返されます。 選択した場所の外部から呼び出しを行うには、サービス URL に、選択した場所の地理プレフィックスを含める必要があります。 たとえば、Creator を米国に作成した場合は、変換サービスへのすべての呼び出しを `us.atlas.microsoft.com/conversion/convert` に送信する必要があります。

また、Creator にインポートされたすべてのデータは、Creator リソースと同じ地理的な場所にアップロードされる必要があります。 たとえば、Creator が米国でプロビジョニングされている場合、すべての生データを `us.atlas.microsoft.com/mapData/upload` 経由でアップロードする必要があります。

## <a name="next-steps"></a>次のステップ

屋内マッピングに関する Creator の概要:

> [!div class="nextstepaction"]
> [Data Upload](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [データ変換](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [データセット](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [タイルセット](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [地物状態セット](creator-indoor-maps.md#feature-statesets)

Creator を使用して、アプリケーションに室内マップをレンダリングする方法について学習します。

> [!div class="nextstepaction"]
> [Azure Maps Creator チュートリアル](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [屋内マップの動的スタイル設定](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Indoor Maps モジュールを使用する](how-to-use-indoor-module.md)