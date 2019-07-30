---
title: ナレッジ ベースの移行 - QnA Maker
titleSuffix: Azure Cognitive Services
description: ナレッジ ベースを移行するには、一方のナレッジ ベースからエクスポートしてから他方のナレッジ ベースにインポートする必要があります。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 04ee592122d7c76396f091f8d249518976682004
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446593"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>エクスポート/インポートを使用したナレッジ ベースの移行

ナレッジ ベースを移行するには、一方のナレッジ ベースからエクスポートしてから他方のナレッジ ベースにインポートする必要があります。 

## <a name="prerequisites"></a>前提条件

* 始める前に、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
* 新しい [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)をセットアップします

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>QnA Maker からのナレッジ ベースの移行
1. [QnA Maker ポータル](https://qnamaker.ai)にサインインします。
1. 移行するナレッジ ベースを選択します。

1. **[SETTINGS]\(設定\)** ページで **[Export knowledge base]\(ナレッジ ベースのエクスポート\)** をクリックして、ナレッジ ベースのコンテンツを含む .tsv ファイルをダウンロードします。質問、回答、メタデータ、データ ソース名などは、このファイルから抽出されました。

1. 上部のメニューから **[Create a knowledge base]\(ナレッジ ベースの作成\)** を選択し、空のナレッジ ベースを作成します。 

    ![データ ソースの設定](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - サービスに**名前**を付けます。 名前は重複しても構いません。特殊文字も使用できます。

1. **作成** を選択します。

    ![KB の作成](../media/qnamaker-how-to-create-kb/create-kb.png)

1. この新しいナレッジ ベースで、 **[SETTINGS]\(設定\)** タブを開いて、 **[Import knowledge base]\(ナレッジ ベースのインポート\)** を選択します。 これにより質問、回答、およびメタデータがインポートされ、抽出元のデータ ソースの名前は保持されます。

   ![ナレッジ ベースのインポート](../media/qnamaker-how-to-migrate-kb/Import.png)

1. テスト パネルを使用して、新しいナレッジ ベースを**テスト**します。 [ナレッジ ベースのテスト](../How-To/test-knowledge-base.md)方法を確認してください。
1. ナレッジ ベースを**公開**します。 [ナレッジ ベースの公開](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)方法を確認してください。
1. お使いのアプリケーションまたはボット コードで、エンドポイントを使用します。 ここで、[QnA ボットの作成](../Tutorials/create-qna-bot.md)方法を参照してください。

    ![QnA Maker の値](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    この時点で、ナレッジ ベースのすべてのコンテンツ、つまり、質問、回答、メタデータと、ソース ファイルの名前、URL が新しいナレッジ ベースにインポートされています。 

## <a name="chat-logs-and-alterations"></a>チャットログと変更
大文字と小文字が区別されない変更 (シノニム) は、自動的にはインポートされません。 [V4 API](https://go.microsoft.com/fwlink/?linkid=2092179) を使用して、新しいナレッジ ベースに変更を移行します。

チャットログを移行することはできません。新しいナレッジ ベースでは Application Insights を使ってチャットログが格納されているためです。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースを編集する](../How-To/edit-knowledge-base.md)
