---
title: ナレッジ ベースを管理する - QnA Maker
description: QnA Maker を使うと、ナレッジ ベースの設定とコンテンツにアクセスできるようになり、ナレッジ ベースを管理できます。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: c7bb79cbd67ba2fb151641ddeeb31b54e399b906
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96938258"
---
# <a name="create-knowledge-base-and-manage-settings"></a>ナレッジ ベースを作成して設定を管理する

QnA Maker を使うと、ナレッジ ベースの設定とデータ ソースにアクセスして、ナレッジ ベースを管理できます。

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)してください。
> * Azure portal で作成された [QnA Maker リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)。 リソースを作成するしたに選択した Azure Active Directory ID、サブスクリプション、QnA リソース名を覚えておいてください。

## <a name="create-a-knowledge-base"></a>ナレッジ ベースの作成

1. ご自分の Azure の資格情報を使用して [QnAMaker.ai](https://QnAMaker.ai) ポータルにサインインします。

1. QnA Maker ポータルで、 **[Create a knowledge base]\(ナレッジ ベースの作成\)** を選択します。

1. QnA Maker リソースが既にある場合は、 **[Create]\(作成\)** ページで、 **[Step 1]\(手順 1\)** をスキップします。

    リソースをまだ作成していない場合は、 **[Create a QnA service]\(QnA サービスの作成\)** を選択します。 サブスクリプションで QnA Maker サービスを設定するため、[Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) にリダイレクトされます。 リソースを作成するしたに選択した Azure Active Directory ID、サブスクリプション、QnA リソース名を覚えておいてください。

    Azure portal でリソースの作成が完了したら、QnA Maker ポータルに戻り、ブラウザー ページを最新の状態に更新して、 **[Step 2]\(手順 2\)** を続けます。

1. **[Step 3]\(手順 3\)** で、Active Directory、サブスクリプション、サービス (リソース)、およびサービスで作成されたすべてのナレッジ ベースの言語を選択します。

   ![QnA Maker サービス ナレッジ ベースの選択のスクリーン ショット](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. **[Step 3]\(手順 3\)** で、ナレッジ ベースの名前を `My Sample QnA KB` に設定します。

1. **[Step 4]\(手順 4\)** で、次の表を使用して設定を構成します。

    |設定|値|
    |--|--|
    |**Enable multi-turn extraction from URLs, .pdf or .docx files (URL、.pdf、または .docx ファイルからの複数ターンの抽出を有効にする)**|オン|
    |**Default answer text (既定の回答テキスト)**| `Quickstart - default answer not found.`|
    |**+ Add URL (+ URL の追加)**|`https://azure.microsoft.com/en-us/support/faq/`|
    |**Chit-chat (おしゃべり)**|**[Professional]\(専門家\)** を選択します|

1. **[Step 5]\(手順 5\)** で、 **[Create your KB]\(KB の作成\)** を選択します。

    抽出プロセスでは、ドキュメントを読み取って質問と回答を識別します。これにはしばらくかかります。

    QnA Maker がナレッジ ベースを正常に作成すると、 **[Knowledge base]\(ナレッジ ベース\)** ページが開きます。 このページで、ナレッジ ベースの内容を編集することができます。

## <a name="edit-knowledge-base"></a>ナレッジ ベースの編集

1.  上部のナビゲーション バーにある **[My knowledge bases]\(マイ ナレッジ ベース\)** を選択します。

       自分が作成したサービスと他のユーザーから共有されたサービスが、**最終更新日** の降順ですべて表示されます。

       ![[My knowledge bases]\(マイ ナレッジ ベース\)](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. 編集する特定のナレッジ ベースを選択します。

1.  **[設定]** を選択します。 次の一覧には、変更可能なフィールドが含まれています。

       |目標|アクション|
       |--|--|
       |URL の追加|**[Manage knowledgebase]\(ナレッジ ベースの管理\) -> [+ Add URL]\(+ URL の追加\)** リンクをクリックして、新しい FAQ コンテンツをナレッジ ベースに追加するために新しい URL を追加できます。|
       |URL の削除|既存の URL を削除するには、ごみ箱の削除アイコンを選択します。|
       |コンテンツを最新の情報に更新|既存の URL の最新のコンテンツをクロールするためのナレッジ ベースが必要な場合は、 **[更新]** チェック ボックスをオンにします。 これにより、最新の URL コンテンツでナレッジ ベースが 1 回更新されます。 この操作では、定期的な更新スケジュールが設定されません。|
       |ファイルの追加|**[Manage knowledge base]\(ナレッジ ベースの管理\)** を選択してから **[+ ファイルの追加]** を選択し、サポートされているファイル ドキュメントを追加してナレッジ ベースに含めることができます。|
    |[インポート]|**[Import Knowledge base]\(ナレッジ ベースのインポート\)** ボタンを選択して、既存のナレッジ ベースをインポートすることもできます。 |
    |更新|ナレッジ ベースの更新は、ナレッジ ベースと関連付けられた QnA Maker サービスの作成中に使用している **管理価格レベル** によって異なります。 必要な場合、Azure portal から管理レベルを更新することもできます。

  1. ナレッジ ベースを変更した後、その変更を保持するには、ページの右上隅にある **[保存してトレーニング]** を選択します。

       ![[Save and train]\(保存してトレーニング\)](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >**[Save and train]\(保存してトレーニング\)** を選択する前にページを閉じると、すべての変更が失われます。



## <a name="manage-large-knowledge-bases"></a>大きなナレッジ ベースの管理

* **データ ソース グループ**:QnA は、抽出元のデータ ソースごとにグループ化されます。 データ ソースは、展開したり折りたたんだりすることができます。

    ![QnA Maker データ ソース バーを使用したデータ ソースの質問と回答の折りたたみと展開](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **サポート技術情報の検索**:ナレッジ ベースの検索は、ナレッジ ベース テーブルの一番上にあるテキスト ボックスに検索語句を入力することで実行できます。 Enter キーを押すと、質問、回答、またはメタデータの内容を対象に検索が実行されます。 検索フィルターを解除するには、X アイコンをクリックします。

    ![質問と回答の上にある [QnA Maker] 検索ボックスを使用して、表示をフィルターの一致項目のみに絞り込みます。](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **改ページ位置の自動修正**:データ ソース間をすばやく移動して大規模なナレッジ ベースを管理する

    ![質問と回答の上にある QnA Maker の改ページ位置の自動修正機能を使用して、質問と回答のページ間を移動します。](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>ナレッジ ベースの削除

ナレッジ ベース (KB) の削除は永続的な操作です。 この操作を元に戻すことはできません。 ナレッジ ベースを削除する前に、QnA Maker ポータルの **[設定]** ページからナレッジ ベースをエクスポートする必要があります。

ナレッジ ベースをコラボレーター (collaborate-knowledge-base.md) と共有している場合、削除すると、すべてのユーザーが KB にアクセスできなくなります。

## <a name="next-steps"></a>次のステップ

リソース内のすべてのナレッジ ベースの[言語の管理](../index.yml)について学習します。

* QnA ペアの編集
* QnA Maker によって使用される Azure リソースを管理する