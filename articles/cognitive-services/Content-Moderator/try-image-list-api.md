---
title: カスタム リストと API コンソールを使用してイメージをモデレートする - Content Moderator
titleSuffix: Azure Content Moderator
description: カスタム イメージ リストを作成するには、Azure Content Moderator の List Management API を使用します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 0035d367017c92bd151c27e14d744ef41eace069
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85800150"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>API コンソールでカスタム イメージ リストを使用してモデレートする

カスタム イメージ リストを作成するには、Azure Content Moderator の [List Management API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) を使用します。 カスタム イメージ リストは、Image Moderation API で使用します。 イメージ モデレーション操作では、イメージが表示されます。 カスタム リストを作成すると、この操作で、イメージとカスタム リスト内のイメージとの比較も行われます。 カスタム リストを使用して、イメージをブロックしたり許可したりできます。

> [!NOTE]
> **画像リスト数は 5 個**、各リストの **画像数は 10,000 個** という上限があります。
>

List Management API を使用して、次のタスクを実行できます。

- リストを作成します。
- リストにイメージを追加する。
- イメージをリスト内のイメージに照らしてスクリーニングする。
- リストからイメージを削除する。
- リストを削除する。
- リストの情報を編集する。
- リストに加えた変更が今後のスキャンに含まれるように、インデックスを最新の情報に更新する。

## <a name="use-the-api-console"></a>API コンソールを使用する
オンライン コンソールで API を試すには、サブスクリプション キーが必要です。 これは、 **[設定]** タブの **[Ocp-Apim-Subscription-Key]** ボックス内にあります。 詳細については、[概要](overview.md)に関するページを参照してください。

## <a name="refresh-search-index"></a>検索インデックスを最新の情報に更新する

イメージ リストに変更を加えた後は、変更が今後のスキャンに含まれるように、イメージ リストのインデックスを最新の情報に更新する必要があります。 この手順は、デスクトップ上の検索エンジン (有効になっている場合) や Web 検索エンジンが、新しいファイルやページが含まれるように絶えずインデックスを最新の情報に更新する方法と似ています。

1. [Image List Management API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)の左側のメニューで、**[Image Lists]\(イメージ リスト\)** を選択し、**[Refresh Search Index]\(検索インデックスを最新の情報に更新\)** を選択します。

   **[Image Lists - Refresh Search Index]\(イメージ リスト - インデックスを最新の情報に更新\)** ページが開きます。

2. **API テスト コンソールを開く場合**、実際の場所を最もよく表しているリージョンを選択します。 
 
    ![[Image Lists - Refresh Search Index]\(イメージ リスト - インデックスを最新の情報に更新\) ページでのリージョンの選択](images/test-drive-region.png)

    **[Image Lists - Refresh Search Index]\(イメージ リスト - インデックスを最新の情報に更新\)** API コンソールが開きます。

3. **[listId]** ボックスに、リスト ID を入力します。 サブスクリプション キーを入力して、**[送信]** を選択します。

   ![[Image Lists - Refresh Search Index]\(イメージ リスト - インデックスを最新の情報に更新\) コンソールの [応答のコンテンツ] ボックス](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>イメージ リストを作成する

1. [Image List Management API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)に移動します。

   **[Image Lists - Create]\(イメージ リスト - 作成\)** ページが開きます。 

3. **API テスト コンソールを開く場合**、実際の場所を最もよく表しているリージョンを選択します。

   ![[Image Lists - Create]\(イメージ リスト - 作成\) ページでのリージョンの選択](images/test-drive-region.png)

   **[Image Lists - Create]\(イメージ リスト - 作成\)** API コンソールが開きます。
 
4. **[Ocp-Apim-Subscription-Key]** ボックスにサブスクリプション キーを入力します。

5. **[要求本文]** ボックスで、**[名前]** (たとえば「MyList」) と **[説明]** の値を入力します。

   ![[Image Lists - Create]\(イメージ リスト - 作成\) コンソールの [要求本文] の名前と説明](images/try-terms-list-create-1.png)

6. よりわかりやすいメタデータをリストに割り当てるには、キーと値のペアのプレースホルダーを使用します。

    ```json
    {
        "Name": "MyExclusionList",
        "Description": "MyListDescription",
        "Metadata": 
        {
            "Category": "Competitors",
            "Type": "Exclude"
        }
    }
    ```

   リストのメタデータは、実際のイメージではなく、キーと値のペアとして追加します。
 
7. **[Send]** を選択します。 リストが作成されました。 新しいリストに関連付けられている **ID** の値をメモします。 この ID は、他のイメージ リスト管理機能で必要になります。

   ![[Image Lists - Create]\(イメージ リスト - 作成\) コンソールの [応答のコンテンツ] ボックスに表示されるリスト ID](images/try-terms-list-create-2.png)
 
8. 次に、MyList にイメージを追加します。 左側のメニューで **[イメージ]** を選択し、**[イメージの追加]** を選択します。

   **[Image - Add Image]\(イメージ - イメージの追加\)** ページが開きます。 

9. **API テスト コンソールを開く場合**、実際の場所を最もよく表しているリージョンを選択します。

   ![[Image - Add Image]\(イメージ - イメージの追加\) ページでのリージョンの選択](images/test-drive-region.png)

   **[Image - Add Image]\(イメージ - イメージの追加\)** API コンソールが開きます。
 
10. **[listId]** ボックスに生成されたリスト ID を入力し、追加するイメージの URL を入力します。 サブスクリプション キーを入力して、**[送信]** を選択します。

11. イメージがリストに追加されたことを確認するには、左側のメニューで **[イメージ]** を選択し、**[Get All Image Ids]\(すべてのイメージ ID を取得\)** を選択します。

    **[Image - Get All Image Ids]\(イメージ - すべてのイメージ ID を取得\)** API コンソールが開きます。
  
12. **[listId]** ボックスで、リスト ID を入力し、サブスクリプション キーを入力します。 **[Send]** を選択します。

    ![[Image - Get All Image Ids]\(イメージ - すべてのイメージ ID を取得\) コンソールの [応答のコンテンツ] ボックスに入力した用語が表示される](images/try-image-list-create-11.png)
 
10. さらにいくつかのイメージを追加します。 これでカスタム イメージ リストを作成できたので、カスタム イメージ リストを使用して[イメージを評価](try-image-api.md)してみましょう。 

## <a name="delete-images-and-lists"></a>イメージとリストを削除する

イメージやリストを削除するのは簡単です。 API を使用して、次のタスクを実行できます。

- イメージを削除します。 (**[Image - Delete]\(イメージ - 削除\)**)
- リストを削除せずに、リスト内のすべてのイメージを削除する。 (**[Image - Delete All Images]\(イメージ - すべてのイメージを削除\)**)
- リストと、そのコンテンツをすべて削除する。 ( **[Image Lists - Delete]\(イメージ リスト - 削除\)** )

この例では、1 つのイメージだけを削除します。

1. [Image List Management API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)の左側のメニューで、**[イメージ]** を選択し、**[削除]** を選択します。 

   **[Image - Delete]\(イメージ - 削除\)** ページが開きます。

2. **API テスト コンソールを開く場合**、実際の場所を最もよく表しているリージョンを選択します。 

   ![[Image - Delete]\(イメージ - 削除\) ページでのリージョンの選択](images/test-drive-region.png)
 
   **[Image - Delete]\(イメージ - 削除\)** API コンソールが開きます。
 
3. **[listId]** ボックスに、削除するイメージが含まれるリストの ID を入力します。  これは、MyList の **[Image - Get All Image Ids]\(イメージ - すべてのイメージ ID を取得\)** コンソールで返される数値です。 次に、削除するイメージの **ImageId** を入力します。 

この例では、リスト ID は **ContentSource** の値である **58953** です。 イメージ ID は **ContentIds** の値である **59021** です。

1. サブスクリプション キーを入力して、**[送信]** を選択します。

1. イメージが削除されたことを確認するには、**[Image - Get All Image Ids]\(イメージ - すべてのイメージ ID を取得\)** コンソールを使用します。
 
## <a name="change-list-information"></a>リストの情報を変更する

リストの名前と説明を編集し、メタデータ項目を追加することができます。

1. [Image List Management API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)の左側のメニューで、**[Image Lists]\(イメージ リスト\)** を選択し、**[Update Details]\(詳細の更新\)** を選択します。 

   **[Image Lists - Update Details]\(イメージ リスト - 詳細の更新\)** ページが開きます。

2. **API テスト コンソールを開く場合**、実際の場所を最もよく表しているリージョンを選択します。  

    ![[Image Lists - Update Details]\(イメージ リスト - 詳細の更新\) ページでのリージョンの選択](images/test-drive-region.png)

    **[Image Lists - Update Details]\(イメージ リスト - 詳細の更新\)** API コンソールが開きます。
 
3. **[listId]** ボックスで、リスト ID を入力し、サブスクリプション キーを入力します。

4. **[要求本文]** ボックスで編集を行い、ページ上の **[送信]** ボタンを選択します。

   ![[Image Lists - Update Details]\(イメージ リスト - 詳細の更新\) コンソールの [要求本文] の編集](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>次のステップ

コード内で REST API を使用するか、[イメージ リストに関する .NET のクイック スタート](image-lists-quickstart-dotnet.md)から開始して、アプリケーションと統合します。
