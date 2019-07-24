---
title: ナレッジ ベースの公開、REST、C#
titleSuffix: QnA Maker- Azure Cognitive Services
description: この C# REST ベースのクイック スタートでは、ナレッジ ベースを公開する手順を紹介しています。公開時に、テスト済みのナレッジ ベースの最新バージョンが、公開済みのナレッジ ベースを表す専用の Azure Search インデックスにプッシュされます。 また、アプリケーションやチャット ボットで呼び出すことができるエンドポイントが作成されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 8a6a46708b78b3f92087ace02173f9f081fa85b2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58170515"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>クイック スタート: C# を使用して QnA Maker のナレッジ ベースを公開する

この REST ベースのクイック スタートでは、ナレッジ ベース (KB) をプログラムから公開する手順を紹介しています。 公開すると、ナレッジ ベースの最新バージョンが、専用の Azure Search インデックスにプッシュされ、アプリケーションやチャット ボットで呼び出すことができるエンドポイントが作成されます。

このクイック スタートで呼び出す QnA Maker API は次のとおりです。
* [公開](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) - この API は、要求の本文に情報を必要としません。

## <a name="prerequisites"></a>前提条件

* 最新の [**Visual Studio Community エディション**](https://www.visualstudio.com/downloads/)。
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)が必要です。 キーを取得するには、ダッシュボードで **[リソース管理]** の **[キー]** を選択します。 
* URL の kbid クエリ文字列パラメーターに含まれている QnA Maker ナレッジ ベース (KB) ID (下図)。

    ![QnA Maker ナレッジ ベース ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    まだナレッジ ベースがない場合は、このクイック スタート用のサンプルを作成できます。[新しいナレッジ ベースの作成](create-new-kb-csharp.md)に関するページを参照してください。

> [!NOTE] 
> 完全なソリューション ファイルは、[**Azure-Samples/cognitive-services-qnamaker-csharp** GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base)から入手できます。

## <a name="create-knowledge-base-project"></a>ナレッジ ベース プロジェクトを作成する

1. Visual Studio 2017 Community エディションを開きます。
1. 新しい**コンソール アプリ (.NET Core)** プロジェクトを作成し、そのプロジェクトに `QnaMakerQuickstart` という名前を付けます。 残りの設定には既定値を受け入れます。

## <a name="add-required-dependencies"></a>必要な依存関係を追加する

Program.cs の先頭にある 1つの using ステートメントを次の行に置き換えて、プロジェクトに必要な依存関係を追加します。

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=1-2 "Add the required dependencies")]

## <a name="add-required-constants"></a>必要な定数を追加する

**Main** メソッド内に、QnA Maker にアクセスするために必要な定数を追加します。 サンプルの値は実際の値に変更してください。

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=11-14 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>ナレッジ ベースを公開するための POST 要求を追加する

必要な定数の後に、次のコードを追加します。このコードは、ナレッジ ベースを公開するための HTTPS 要求を QnA Maker API に送信し、その応答を受信します。

[!code-csharp[Add HTTP Post request and response](~/samples-qnamaker-csharp/documentation-samples/quickstarts/publish-knowledge-base/QnAMakerPublishQuickstart/Program.cs?range=16-29&dedent=8 "Add HTTP Post request and response")]

正常に公開されると、この API 呼び出しから状態 204 が返されます。応答の本文にはコンテンツが含まれません。 
 
## <a name="build-and-run-the-program"></a>プログラムをビルドして実行する

プログラムをビルドして実行します。 ナレッジ ベースを公開するための要求が QnA Maker API に自動的に送信され、応答がコンソール ウィンドウに出力されます。

ナレッジ ベースが公開された後は、クライアント アプリケーションまたはチャット ボットを使用して、エンドポイントからナレッジ ベースに対してクエリを実行できます。 

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>次の手順

ナレッジ ベースが公開された後、[回答を生成するためのエンドポイント URL](../Tutorials/create-publish-answer.md#generating-an-answer) が必要になります。 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
