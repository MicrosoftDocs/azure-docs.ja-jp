---
title: ナレッジ ベースの移行 - QnA Maker
description: ナレッジ ベースを移行するには、一方のナレッジ ベースからエクスポートしてから他方のナレッジ ベースにインポートする必要があります。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: c89ab375cb02824a08ff57e6b5278dd9299126ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96350927"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>エクスポート/インポートを使用したナレッジ ベースの移行

移行は、既存のナレッジ ベースから新しいナレッジ ベースを作成するプロセスです。 これを行うには、いくつかの理由があります。

* バックアップと復元のプロセス
* CI/CD パイプライン
* リージョンの移動

ナレッジ ベースを移行するには、既存のナレッジ ベースからエクスポートし、別のナレッジ ベースにインポートする必要があります。

> [!NOTE]
> 以下の手順に従って、既存のナレッジ ベースを新しい QnA Maker マネージド (プレビュー) に移行します。

## <a name="prerequisites"></a>前提条件

* 始める前に、[無料アカウント](https://azure.microsoft.com/free/cognitive-services/)を作成します。
* 新しい [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)をセットアップします

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>QnA Maker からのナレッジ ベースの移行
1. [QnA Maker ポータル](https://qnamaker.ai)にサインインします。
1. 移行する元のナレッジ ベースを選択します。

1. **[SETTINGS]\(設定\)** ページで **[Export knowledge base]\(ナレッジ ベースのエクスポート\)** をクリックして、元のナレッジ ベースのコンテンツを含む .tsv ファイルをダウンロードします。質問、回答、メタデータ、追加プロンプト、データ ソース名などは、このファイルから抽出されました。 質問と回答と共にエクスポートされた QnA ID は、[update API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update) を使用して特定の QnA ペアを更新するために使用できます。 特定の QnA ペアの QnA ID は、複数のエクスポート操作で変更されることはありません。

1. 上部のメニューから **[Create a knowledge base]\(ナレッジ ベースの作成\)** を選択し、_空の_ ナレッジ ベースを作成します。 これは空です。これを作成すると、URL やファイルは追加されません。 これらは、作成後にインポート ステップで追加されます。

    ナレッジ ベースを構成します。 新しいナレッジ ベース名のみを設定します。 名前は重複しても構いません。特殊文字も使用できます。

    ステップ 4 の値は、ファイルをインポートするときに上書きされるため、選択しないでください。

1. ステップ 5 で、**作成** を選択します。

1. この新しいナレッジ ベースで、**[SETTINGS]\(設定\)** タブを開いて、**[Import knowledge base]\(ナレッジ ベースのインポート\)** を選択します。 これにより質問、回答、およびメタデータ、追加プロンプトがインポートされ、抽出元のデータ ソースの名前は保持されます。 **新しいナレッジ ベースで作成された QnA ペアは、エクスポートされたファイルに存在するものと同じ QnA ID を持つ必要があります**。 これにより、ナレッジ ベースの正確なレプリカを作成できます。

   > [!div class="mx-imgBorder"]
   > [![ナレッジ ベースのインポート](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. テスト パネルを使用して、新しいナレッジ ベースを **テスト** します。 [ナレッジ ベースのテスト](../How-To/test-knowledge-base.md)方法を確認してください。

1. ナレッジ ベースを **公開** し、チャットボットを作成します。 [ナレッジ ベースの公開](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)方法を確認してください。

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>プログラムで QnA Maker からナレッジ ベースを移行する

次の REST API を使用して、プログラムで移行プロセスを利用できます。

**エクスポート**

* [ナレッジ ベースのダウンロード API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/download)

**[インポート]**

* [置換 API (同じナレッジ ベース ID での再読み込み)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/replace)
* [作成 API (新しいナレッジ ベース ID での読み込み)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>チャットログと変更
大文字と小文字が区別されない変更 (シノニム) は、自動的にはインポートされません。 [V4 API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) を使用して、新しいナレッジ ベースに変更を移行します。

チャットログを移行することはできません。新しいナレッジ ベースでは Application Insights を使ってチャットログが格納されているためです。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジ ベースを編集する](../How-To/edit-knowledge-base.md)