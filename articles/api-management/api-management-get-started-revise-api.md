---
title: リビジョンを使用して Azure API Management で互換性に影響しない変更を安全に行う | Microsoft Docs
description: このチュートリアルの手順に従って、API Management でリビジョンを使用して互換性に影響しない変更を行う方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 757c710ddca72ec6a1383b51a8b536d196e6cb8c
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203593"
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>リビジョンを使用して互換性に影響しない変更を安全に行う
API の準備が整って開発者に使用され始めると、その API の変更が必要になることがありますが、API の呼び出し元を混乱させないように注意する必要もあります。 また、行った変更内容を開発者に知らせると有効です。 Azure API Management で**リビジョン**を使用することで、これを実現できます。 詳細については、「[Versions & revisions (バージョンとリビジョン)](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/)」と「[API Versioning with Azure API Management (Azure API Management での API のバージョン管理)](https://blogs.msdn.microsoft.com/apimanagement/2017/09/13/api-versioning-with-azure-api-management/)」を参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しいリビジョンの追加
> * リビジョンに互換性に影響しない変更を加える
> * リビジョンを最新にして変更ログ エントリを追加する
> * 開発者ポータルを参照して、変更内容と変更ログを確認します。

![開発者ポータルでの変更ログ](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>前提条件

+ [Azure API Management の用語](api-management-terminology.md)について学習します。
+ 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
+ また、次のチュートリアルを完了すること: [最初の API のインポートと発行](import-and-publish.md)。

## <a name="add-a-new-revision"></a>新しいリビジョンの追加

![API リビジョンを追加する](media/api-management-getstarted-revise-api/07-AddRevisions-01-AddNewRevision.png)

1. **[API]** ページを選択します。
2. API の一覧で **[Demo Conference API]\(デモ会議 API\)** (または、リビジョンを追加する他の API) を選択します。
3. ページの上部付近にあるメニューで **[リビジョン]** タブをクリックします。
4. **[+ Add Revision] \(+ リビジョンの追加\)** を選択します。

    > [!TIP]
    > また、API のコンテキスト メニュー ( **[...]** ) で **[リビジョンの追加]** を選択することもできます。

5. 新しいリビジョンの使用目的を示す説明を入力します。
6. **[作成]**
7. これで、新しいリビジョンが作成されました。

    > [!NOTE]
    > 元の API は**リビジョン 1** のままとなります。 別のリビジョンが最新にされない限り、ユーザーはこのリビジョンを呼び出し続けます。

## <a name="make-non-breaking-changes-to-your-revision"></a>リビジョンに互換性に影響しない変更を加える

![リビジョンを変更する](media/api-management-getstarted-revise-api/07-AddRevisions-02-MakeChanges.png)

1. API の一覧で **[Demo Conference API]\(デモ会議 API\)** を選択します。
2. 画面の上部付近の **[Design] \(デザイン\)** タブを選択します。
3. **リビジョン セレクター** (デザイン タブのすぐ上) に、現在の選択対象として**リビジョン 2** が表示されます。

    > [!TIP]
    > リビジョン セレクターを使用して、作業対象のリビジョンに切り替えます。

4. **[+ Add Operation] \(+ 操作の追加\)** を選択します。
5. 新しい操作を **POST** に設定し、操作の名前、表示名、および URL を **test** に設定します。
6. 新しい操作を**保存**します。
7. これで、**リビジョン 2** への変更が完了しました。 ページの上部付近にある**リビジョン セレクター**を使用して、**リビジョン 1** に切り替えます。
8. 新しい操作は**リビジョン 1** には表示さません。 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>リビジョンを最新にして変更ログ エントリを追加する

1. ページの上部付近にあるメニューから **[リビジョン]** タブを選択します。

    ![リビジョン画面の [リビジョン] メニュー。](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)

2. **リビジョン 2** のコンテキスト メニュー ( **...** ) を開きます。
3. **[Make Current] \(最新にする\)** を選択します。
4. この変更に関するメモを投稿する場合は、 **[Post to Public Change log for this API]\(この API のパブリック変更ログを投稿する\)** をオンにします。 開発者が参照できるように、変更内容の説明を入力します。たとえば、「**テスト用リビジョン。新しい "test" 操作を追加しました。** 」と入力します。
5. これで**リビジョン 2** が最新となりました。

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>開発者ポータルを参照して、変更内容と変更ログを確認します。

1. Azure portal で **[API]** を選択します。
2. 上部のメニューから **[開発者ポータル]** を選択します。
3. **[API]** を選択してから、 **[Demo Conference API]\(デモ会議 API\)** を選択します。
4. 新しい **test** 操作が使用可能となっています。
5. API 名の下の **[API Change History] \(API の変更履歴\)** を選択します。
6. この一覧に、変更ログ エントリが表示されます。

    ![[開発者ポータル]](media/api-management-getstarted-revise-api/developer_portal.PNG)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 新しいリビジョンの追加
> * リビジョンに互換性に影響しない変更を加える
> * リビジョンを最新にして変更ログ エントリを追加する
> * 開発者ポータルを参照して、変更内容と変更ログを確認します。

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [API の複数のバージョンを発行する](api-management-get-started-publish-versions.md)
