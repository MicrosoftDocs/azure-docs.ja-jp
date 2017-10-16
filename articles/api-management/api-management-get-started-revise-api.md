---
title: "リビジョンを使用した Azure API Management での API の更新 | Microsoft Docs"
description: "このチュートリアルの手順に従って、API Management でリビジョンを使用して互換性に影響しない変更を行う方法について説明します。"
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.openlocfilehash: 0d67166a16ae4d640080ad83e7625e594b0dc246
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="make-non-breaking-changes-safely-using-revisions"></a>リビジョンを使用して安全に互換性に影響しない変更を加える
このチュートリアルでは、API を安全に変更し、変更内容を開発者に伝える方法について説明します。

## <a name="prerequisites"></a>前提条件
このチュートリアルを実行するには、API Management サービスを作成する必要があります。また、(Conference API の代わりに) 変更できる既存の API が必要です。

## <a name="about-revisions"></a>リビジョンについて
API の準備が整い、開発者が使用し始めたら、その API に変更を加える際には、API の呼び出し元を混乱させないように注意する必要があります。 また、行った変更内容を開発者に知らせると有効です。 Azure API Management で**リビジョン**を使用することで、これを実現できます。

## <a name="walkthrough"></a>チュートリアル
このチュートリアルでは、新しいリビジョンを追加し、それに操作を追加した後、そのリビジョンを最新にします。この処理では変更ログ エントリを作成します。

## <a name="add-a-new-revision"></a>新しいリビジョンの追加
1. Azure Portal で API Management サービス内の **API** ページを参照します。
2. API の一覧から **[Conference API] \(Conference API\)** を選択し、ページの上部付近にあるメニューから **[Revisions] \(リビジョン\)** タブを選択します。
3. **[+ Add Revision] \(+ リビジョンの追加\)** を選択します。

    > [!TIP]
    > また、API のコンテキスト メニュー (**...**) で**[Add Revision] \(リビジョンの追加\)** を選択することもできます。
![画面の上部付近のリビジョン メニュー](media/api-management-getstarted-revise-api/TopMenu.PNG)

4. 新しいリビジョンの使用目的を示す説明を入力します。
5. **[作成]**
6. これで、新しいリビジョンが作成されました。

    > [!NOTE]
    > 元の API は**リビジョン 1** のままとなります。 別のリビジョンが最新にされない限り、ユーザーはこのリビジョンを呼び出し続けます。

## <a name="make-non-breaking-changes-to-your-revision"></a>リビジョンに互換性に影響しない変更を加える
1. 画面の上部付近の**[Design] \(デザイン\)** タブを選択します。
2. **リビジョン セレクター** ([Design] \(デザイン\) タブのすぐ上) に、現在のリビジョンが**リビジョン 2** として表示されます。

    > [!TIP]
    > リビジョン セレクターを使用して、作業対象のリビジョンに切り替えます。

3. **[+ Add Operation] \(+ 操作の追加\)** を選択します。
4. 新しい操作を「**POST**」に設定し、操作の名前と表示名を「**フィードバック**」に設定します。
5. 新しい操作を**保存**します。
6. これで、**リビジョン 2** への変更が完了しました。 ページの上部付近にある**リビジョン セレクター**を使用して、**リビジョン 1** に切り替えます。
7. 新しい操作は**リビジョン 1** には表示さません。 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>リビジョンを最新にして変更ログ エントリを追加する
1. ページの上部付近にあるメニューから **[リビジョン]** タブを選択します。
![リビジョン画面の [リビジョン] メニュー](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
2. **リビジョン 2** のコンテキスト メニュー (**...**) を開きます。
3. **[Make Current] \(最新にする\)** を選択します。
![リビジョンを最新にして変更ログを投稿する](media/api-management-getstarted-revise-api/MakeCurrent.PNG)
4. **[Post to Public Change Log for this API] \(この API のパブリック変更ログを投稿する\)** を選択します。
5. 開発者が参照する変更内容の説明を入力します。たとえば「**新しいフィードバック操作を追加**」などです。
6. これで**リビジョン 2** が最新となりました。

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>開発者ポータルを参照して、変更内容と変更ログを確認します。
1. 上部のメニューから **[開発者ポータル]** を選択します。
2. **[API]** を選択してから、**[Conference API] \(Conference API\)** を選択します。
3. 新しい**フィードバック**操作が使用可能となっています。
4. API 名の下の **[API Change History] \(API の変更履歴\)** を選択します。
5. この一覧に、変更ログ エントリが表示されます。
![開発者ポータルでの変更ログ](media/api-management-getstarted-revise-api/ChangeLogDevPortal.PNG)

## <a name="next-steps"></a>次のステップ
[Azure API Management を使用した API バージョンの公開](#api-management-getstarted-publish-versions.md)