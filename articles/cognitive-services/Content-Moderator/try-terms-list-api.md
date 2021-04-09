---
title: カスタム用語リストを使用してテキストをモデレートする - Content Moderator
titleSuffix: Azure Cognitive Services
description: Text Moderation API で使用するカスタム用語リストを作成するには、List Management API を使用します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: f1dfe88741a304da92901d3997c746654336ef54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "85800042"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>API コンソールでカスタム用語リストを使用してモデレートする

Azure Content Moderator の既定のグローバルな用語リストは、ほとんどのコンテンツ モデレーションのニーズに十分対応できます。 しかし、組織に固有の用語のスクリーニングが必要になる場合があります。 たとえば、さらに詳しくレビューするために、競合他社名にタグを付けたい場合などです。 

Text Moderation API で使用するカスタム用語リストを作成するには、[List Management API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) を使用します。 **[Text - Screen]\(テキスト - スクリーニング\)** 操作では、不適切な表現がないかテキストをスキャンし、テキストをカスタム ブロック リストと共有ブロック リストに対して比較します。

> [!NOTE]
> 上限は **用語の一覧が 5 つ** で、各一覧では **用語が 10,000 個を超えてはいけません**。
>

List Management API を使用して、次のタスクを実行できます。
- リストを作成します。
- リストに用語を追加する。
- 用語をリスト内の用語に照らしてスクリーニングする。
- リストから用語を削除する。
- リストを削除する。
- リストの情報を編集する。
- リストに加えた変更が今後のスキャンに含まれるように、インデックスを最新の情報に更新する。

## <a name="use-the-api-console"></a>API コンソールを使用する

オンライン コンソールで API を試すには、サブスクリプション キーが必要です。 このキーは、**[設定]** タブの **[Ocp-Apim-Subscription-Key]** ボックス内にあります。 詳細については、[概要](overview.md)に関するページを参照してください。

## <a name="refresh-search-index"></a>検索インデックスを最新の情報に更新する

用語リストに変更を加えた後は、変更が今後のスキャンに含まれるように、用語リストのインデックスを最新の情報に更新する必要があります。 この手順は、デスクトップ上の検索エンジン (有効になっている場合) や Web 検索エンジンが、新しいファイルやページが含まれるように絶えずインデックスを最新の情報に更新する方法と似ています。

1. [Term List Management API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)の左側のメニューで、**[Term Lists]\(用語リスト\)** を選択し、**[Refresh Search Index]\(検索インデックスを最新の情報に更新\)** を選択します。 

   **[Term Lists - Refresh Search Index]\(用語リスト - 検索インデックスを最新の情報に更新\)** ページが開きます。

2. **API テスト コンソールを開く場合**、実際の場所を最もよく表しているリージョンを選択します。 

   ![[Term Lists - Refresh Search Index]\(用語リスト - 検索インデックスを最新の情報に更新\) ページでのリージョンの選択](images/test-drive-region.png)

   **[Term Lists - Refresh Search Index]\(用語リスト - 検索インデックスを最新の情報に更新\)** API コンソールが開きます。

3. **[listId]** ボックスに、リスト ID を入力します。 サブスクリプション キーを入力して、**[送信]** を選択します。

   ![[Term Lists API - Refresh Search Index]\(用語リスト - 検索インデックスを最新の情報に更新\) コンソールの [応答のコンテンツ] ボックス](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>用語リストを作成する
1. [Term List Management API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)に移動します。 

   **[Term Lists - Create]\(用語リスト - 作成\)** ページが開きます。

2. **API テスト コンソールを開く場合**、実際の場所を最もよく表しているリージョンを選択します。 

   ![[Term Lists - Create]\(用語リスト - 作成\) ページでのリージョンの選択](images/test-drive-region.png)

   **[Term Lists - Create]\(用語リスト - 作成\)** API コンソールが開きます。
 
3. **[Ocp-Apim-Subscription-Key]** ボックスにサブスクリプション キーを入力します。

4. **[要求本文]** ボックスで、**[名前]** (たとえば「MyList」) と **[説明]** の値を入力します。

   ![[Term Lists - Create]\(用語リスト - 作成\) コンソールの [要求本文] の名前と説明](images/try-terms-list-create-1.png)

5. よりわかりやすいメタデータをリストに割り当てるには、キーと値のペアのプレースホルダーを使用します。

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

   リストのメタデータは、実際の用語ではなく、キーと値のペアとして追加します。
 
6. **[Send]** を選択します。 リストが作成されました。 新しいリストに関連付けられている **ID** の値をメモします。 この ID は、他の用語リスト管理機能で必要になります。

   ![[Term Lists - Create]\(用語リスト - 作成\) コンソールの [応答のコンテンツ] ボックスに表示されるリスト ID](images/try-terms-list-create-2.png)
 
7. MyList に用語を追加します。 左側のメニューの **[用語]** で、**[Add Term]\(用語の追加\)** を選択します。 

   **[Term - Add Term]\(用語 - 用語の追加\)** ページが開きます。 

8. **API テスト コンソールを開く場合**、実際の場所を最もよく表しているリージョンを選択します。 

   ![[Term - Add Term]\(用語 - 用語の追加\) ページでのリージョンの選択](images/test-drive-region.png)

   **[Term - Add Term]\(用語 - 用語の追加\)** API コンソールが開きます。
 
9. **[listId]** ボックスに生成されたリスト ID を入力し、**[言語]** の値を選択します。 サブスクリプション キーを入力して、**[送信]** を選択します。

   ![[Term - Add Term]\(用語 - 用語の追加\) コンソールのクエリ パラメーター](images/try-terms-list-create-3.png)
 
10. 用語がリストに追加されたことを確認するには、左側のメニューで **[用語]** を選択し、**[Get All Terms]\(すべての用語を取得\)** を選択します。 

    **[Term - Get All Terms]\(用語 - すべての用語を取得\)** API コンソールが開きます。

11. **[listId]** ボックスで、リスト ID を入力し、サブスクリプション キーを入力します。 **[Send]** を選択します。

12. **[応答のコンテンツ]** ボックスで、入力した用語を確認します。

    ![[Term - Get All Terms]\(用語 - すべての用語を取得\) コンソールの [応答のコンテンツ] ボックスに入力した用語が表示される](images/try-terms-list-create-4.png)
 
13. さらにいくつかの用語を追加します。 これでカスタム用語リストを作成できたので、カスタム用語リストを使用して[いくつかのテキストをスキャン](try-text-api.md)してみましょう。 

## <a name="delete-terms-and-lists"></a>用語とリストを削除する

用語やリストを削除するのは簡単です。 API を使用して、次のタスクを実行できます。

- 用語を削除する。 (**[Term - Delete]\(用語 - 削除\)**)
- リストを削除せずに、リスト内のすべての用語を削除する。 (**[Term - Delete All Terms]\(用語 - すべての用語を削除\)**)
- リストと、そのコンテンツをすべて削除する。 ( **[Term Lists - Delete]\(用語リスト - 削除\)** )

この例では、1 つの用語だけを削除します。

1. [Term List Management API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)の左側のメニューで、**[用語]** を選択し、**[削除]** を選択します。 

   **[Term - Delete]\(用語 - 削除\)** が開きます。

2. **API テスト コンソールを開く場合**、実際の場所を最もよく表しているリージョンを選択します。 

   ![[Term - Delete]\(用語 - 削除\) ページでのリージョンの選択](images/test-drive-region.png)

   **[Term - Delete]\(用語 - 削除\)** API コンソールが開きます。
  
3. **[listId]** ボックスに、削除したい用語が含まれるリストの ID を入力します。 この ID は、MyList の **[Term Lists - Get Details]\(用語リスト - 詳細の取得\)** コンソールで返される数値 (この例では **122**) です。 用語を入力し、言語を選択します。
 
   ![[Term - Delete]\(用語 - 削除\) コンソールのクエリ パラメーター](images/try-terms-list-delete-1.png)

4. サブスクリプション キーを入力して、**[送信]** を選択します。

5. 用語が削除されたことを確認するには、**[Term Lists - Get All]\(用語リスト - すべて取得\)** コンソールを使用します。

   ![[Term Lists - Get All]\(用語リスト - すべて取得\) コンソールの [応答のコンテンツ] ボックスで、その用語が削除されたことを確認できる](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>リストの情報を変更する

リストの名前と説明を編集し、メタデータ項目を追加することができます。

1. [Term List Management API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)の左側のメニューで、**[Term Lists]\(用語リスト\)** を選択し、**[Update Details]\(詳細の更新\)** を選択します。 

   **[Term Lists - Update Details]\(用語リスト - 詳細の更新\)** ページが開きます。

2. **API テスト コンソールを開く場合**、実際の場所を最もよく表しているリージョンを選択します。 

   ![[Term Lists - Update Details]\(用語リスト - 詳細の更新\) ページでのリージョンの選択](images/test-drive-region.png)

   **[Term Lists - Update Details]\(用語リスト - 詳細の更新\)** API コンソールが開きます。

3. **[listId]** ボックスで、リスト ID を入力し、サブスクリプション キーを入力します。

4. **[要求本文]** ボックスで編集を行い、**[送信]** を選択します。

   ![[Term Lists - Update Details]\(用語リスト - 詳細の更新\) コンソールの [要求本文] の編集](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>次のステップ

コード内で REST API を使用するか、[用語リストに関する .NET のクイック スタート](term-lists-quickstart-dotnet.md)から開始して、アプリケーションと統合します。
