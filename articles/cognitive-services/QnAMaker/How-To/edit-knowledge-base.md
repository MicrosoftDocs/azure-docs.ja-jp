---
title: ナレッジ ベースの編集 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker では、使いやすい編集環境で、ナレッジ ベースのコンテンツを管理することができます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 006170b2030b6511ba732a1b12aa5b5b811d8900
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447488"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>QnA Maker のナレッジ ベースを編集する

QnA Maker では、使いやすい編集環境で、ナレッジ ベースのコンテンツを管理することができます。

<a name="add-datasource"></a>

## <a name="edit-your-knowledge-base-content"></a>ナレッジ ベースのコンテンツの編集

1.  上部のナビゲーション バーにある **[My knowledge bases]\(マイ ナレッジ ベース\)** を選択します。 

    自分が作成したサービスと他のユーザーから共有されたサービスが、**最終更新日**の降順ですべて表示されます。

    ![[My knowledge bases]\(マイ ナレッジ ベース\)](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. 編集する特定のナレッジ ベースを選択します。
 
1. **[設定]** を選択します。 ここでは、必須フィールドの [サービス名] を編集できます。
  
    |目標|Action|
    |--|--|
    |URL の追加|**[Manage knowledgebase]\(ナレッジ ベースの管理\) -> [+ Add URL]\(+ URL の追加\)** リンクをクリックして、新しい FAQ コンテンツをナレッジ ベースに追加するために新しい URL を追加できます。|
    |URL の削除|既存の URL を削除するには、ごみ箱の削除アイコンを選択します。|
    |URL コンテンツの更新|既存の URL の最新のコンテンツをクロールするためのナレッジ ベースが必要な場合は、 **[更新]** チェック ボックスをオンにします。 これにより、最新の URL コンテンツでナレッジ ベースが更新されます。|
    |ファイルの追加|**[Manage knowledge base]\(ナレッジ ベースの管理\)** を選択してから **[+ ファイルの追加]** を選択し、サポートされているファイル ドキュメントを追加してナレッジ ベースに含めることができます。|
    |[インポート]|**[Ímport Knowledgebase]\(ナレッジ ベースのインポート\)** ボタンを選択して、既存のナレッジ ベースをインポートすることもできます。 |
    |アップデート|ナレッジ ベースの更新は、ナレッジ ベースと関連付けられた QnA Maker サービスの作成中に使用している**管理価格レベル**によって異なります。 必要な場合、Azure portal から管理レベルを更新することもできます。

1. ナレッジ ベースに変更を加えた後、その変更を保持するには、ページの右上隅にある **[Save and train]\(保存してトレーニング\)** を選択します。    

    ![[Save and train]\(保存してトレーニング\)](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >**[Save and train]\(保存してトレーニング\)** を選択する前にページを閉じると、すべての変更が失われます。

## <a name="add-a-qna-pair"></a>QnA ペアの追加

**[設定]** ページ上で、 **[Add QnA pair]\(QnA ペアの追加\)** を選択して、ナレッジ ベースのテーブルに新しい行を追加します。

![[Add QnA pair]\(QnA ペアの追加\)](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>QnA ペアの削除

QnA を削除するには、QnA 行の右端にある**削除**アイコンをクリックします。 これは、永続的な操作です。 この操作を元に戻すことはできません。 ペアを削除する前に、 **[パブリッシュ]** ページから KB をエクスポートすることを検討してください。 

![QnA ペアの削除](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>代わりの質問を追加する

既存の QnA ペアに代わりの質問を追加すること (別の表現で言い換えること) で、ユーザーからの問い合わせに一致する可能性が向上します。

![代わりの質問を追加する](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>メタデータの追加

最初に **[表示オプション]** を選択してから、 **[メタデータの表示]** を選択して、メタデータのペアを追加します。 これにより、メタデータの列が表示されます。 次に、 **+** 記号を選択して、メタデータのペアを追加します。 このペアは、1 つのキーと 1 つの値で構成されます。

![メタデータの追加](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> 編集を行った後は、変更内行が失われないよう、ナレッジ ベースの保存とトレーニングを定期的に実行してください。

## <a name="manage-large-knowledge-bases"></a>大きなナレッジ ベースの管理

* **データ ソース グループ**:QnA は、抽出元のデータ ソースごとにグループ化されます。 データ ソースは、展開したり折りたたんだりすることができます。

    ![QnA Maker データ ソース バーを使用したデータ ソースの質問と回答の折りたたみと展開](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **サポート技術情報の検索**:ナレッジ ベースの検索は、ナレッジ ベース テーブルの一番上にあるテキスト ボックスに検索語句を入力することで実行できます。 Enter キーを押すと、質問、回答、またはメタデータの内容を対象に検索が実行されます。 検索フィルターを解除するには、X アイコンをクリックします。

    ![質問と回答の上にある [QnA Maker] 検索ボックスを使用して、表示をフィルターの一致項目のみに絞り込みます。](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **改ページ位置の自動修正**:データ ソース間をすばやく移動して大規模なナレッジ ベースを管理する

    ![質問と回答の上にある QnA Maker の改ページ位置の自動修正機能を使用して、質問と回答のページ間を移動します。](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>ナレッジ ベースの削除

ナレッジ ベース (KB) の削除は永続的な操作です。 この操作を元に戻すことはできません。 ナレッジ ベースを削除する前に、QnA Maker ポータルの **[設定]** ページからナレッジ ベースをエクスポートする必要があります。 

[コラボレーター](collaborate-knowledge-base.md)と共有している KB を削除すると、全員が KB にアクセスできなくなります。 

## <a name="delete-azure-resources"></a>Azure リソースを削除する 

QnA Maker ナレッジ ベースに使用されているいずれかの Azure リソースを削除すると、ナレッジ ベースは機能しなくなります。 いずれかのリソースを削除する前に、 **[設定]** ページから確実にナレッジ ベースをエクスポートしてください。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースの共同作業](./collaborate-knowledge-base.md)
