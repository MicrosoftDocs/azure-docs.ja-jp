---
title: クイック スタート:ナレッジ ベースを公開する - REST、C# - QnA Maker
description: この C# REST ベースのクイックスタートでは、自分のナレッジ ベースを公開し、アプリケーションまたはチャット ボット内で呼び出すことができるエンドポイントを作成します。
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-csharp
ms.topic: how-to
ms.openlocfilehash: df8ca85ff0d59817a939936aa6738910bd354996
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267022"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>クイック スタート:C# を使用して QnA Maker のナレッジ ベースを公開する

この REST ベースのクイック スタートでは、ナレッジ ベース (KB) をプログラムから公開する手順を紹介しています。 公開すると、ナレッジ ベースの最新バージョンが、専用の Azure Cognitive Search インデックスにプッシュされ、アプリケーションやチャット ボット内で呼び出すことができるエンドポイントが作成されます。

このクイック スタートで呼び出す QnA Maker API は次のとおりです。
* [公開](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - この API は、要求の本文に情報を必要としません。

## <a name="prerequisites"></a>前提条件

* 最新の [**Visual Studio Community エディション**](https://www.visualstudio.com/downloads/)。
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)が必要です。 キーと (リソース名を含む) エンドポイントを取得するには、Azure portal で対象のリソースの **[クイックスタート]** を選択します。
* URL の `kbid` クエリ文字列パラメーターに含まれている QnA Maker ナレッジ ベース (KB) ID (下図)。

    ![QnA Maker ナレッジ ベース ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    まだナレッジ ベースがない場合は、このクイック スタート用のサンプルを作成できます。[新しいナレッジ ベースを作成します](create-new-kb-csharp.md)。

> [!NOTE]
> 完全なソリューション ファイルは、[**Azure-Samples/cognitive-services-qnamaker-csharp** GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base)から入手できます。

## <a name="create-knowledge-base-project"></a>ナレッジ ベース プロジェクトを作成する

1. Visual Studio 2019 Community エディションを開きます。
1. 新しい**コンソール アプリ (.NET Core)** プロジェクトを作成し、そのプロジェクトに `QnaMakerQuickstart` という名前を付けます。 残りの設定には既定値を受け入れます。

## <a name="add-required-dependencies"></a>必要な依存関係を追加する

Program.cs の先頭にある 1つの using ステートメントを次の行に置き換えて、プロジェクトに必要な依存関係を追加します。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="dependencies":::

## <a name="add-required-constants"></a>必要な定数を追加する

**Program** クラスに、QnA Maker にアクセスするために必要な定数を追加します。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="constants":::

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>ナレッジ ベースを発行するための Main メソッドを追加する

必要な定数の後に、次のコードを追加します。このコードは、ナレッジ ベースを公開するための HTTPS 要求を QnA Maker API に送信し、その応答を受信します。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="post":::

正常に公開されると、この API 呼び出しから状態 204 が返されます。応答の本文にはコンテンツが含まれません。

## <a name="build-and-run-the-program"></a>プログラムをビルドして実行する

プログラムをビルドして実行します。 ナレッジ ベースを公開するための要求が QnA Maker API に自動的に送信され、応答がコンソール ウィンドウに出力されます。

ナレッジ ベースが公開された後は、クライアント アプリケーションまたはチャット ボットを使用して、エンドポイントからナレッジ ベースに対してクエリを実行できます。

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>次のステップ

ナレッジ ベースが公開された後、[回答を生成するためのエンドポイント URL](./get-answer-from-knowledge-base-csharp.md) が必要になります。

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2092179)
