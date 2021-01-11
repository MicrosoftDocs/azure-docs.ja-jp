---
title: クイック スタート:ナレッジ ベースを作成する - REST、C# - QnA Maker
description: この C# REST ベースのクイック スタートでは、Cognitive Services API アカウントの Azure ダッシュボードに表示される QnA Maker ナレッジ ベースのサンプルをプログラムから作成する手順を紹介しています。
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: 343dc5f0b8475a9fad3d9834f97cff0d0f4c75f6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260086"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c-with-rest"></a>クイック スタート:C# と REST を使用して QnA Maker のナレッジ ベースを作成する

このクイック スタートでは、QnA Maker ナレッジ ベースのサンプルをプログラムから作成して発行する手順を紹介しています。 QnA Maker は、[データ ソース](../Concepts/knowledge-base.md)の FAQ などの半構造化コンテンツから質問とその回答を自動的に抽出します。 ナレッジ ベースのモデルは、API 要求の本文で送信される JSON で定義されます。

このクイック スタートで呼び出す QnA Maker API は次のとおりです。
* [KB の作成](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [取得操作の詳細](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[リファレンス ドキュメント](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [C# サンプル](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>前提条件

* 最新バージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* [QnA Maker リソース](../How-To/set-up-qnamaker-service-azure.md)が必要です。 キーと (リソース名を含む) エンドポイントを取得するには、Azure portal で対象のリソースの **[クイックスタート]** を選択します。

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

好みのエディターまたは IDE で、新しい .NET Core アプリケーションを作成します。

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`qna-maker-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。*Program.cs*。

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドできます。

```dotnetcli
dotnet build
```

ビルドの出力に警告やエラーが含まれないようにする必要があります。

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="add-the-required-dependencies"></a>必要な依存関係を追加する

Program.cs の先頭にある 1つの using ステートメントを次の行に置き換えて、プロジェクトに必要な依存関係を追加します。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="dependencies":::

## <a name="add-the-required-constants"></a>必要な定数を追加する

Program クラスの上部に、QnA Maker にアクセスするために必要な定数を追加します。

次の値を環境変数に設定します。

* `QNA_MAKER_SUBSCRIPTION_KEY` - この**キー**は 32 文字の文字列で、Azure portal の [クイックスタート] ページの QnA Maker リソースで入手できます。 これは、予測エンドポイント キーと同じではありません。
* `QNA_MAKER_ENDPOINT` - **エンドポイント**は、`https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 形式の作成の URL です。 これは、予測エンドポイントを照会するときに使用した URL と同じではありません。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="constants":::

## <a name="add-the-kb-definition"></a>KB 定義を追加する

定数の後に、次の KB 定義を追加します。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="kb":::

## <a name="add-supporting-functions-and-structures"></a>補助的な関数と構造体を追加する
Program クラス内に次のコード ブロックを追加します。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="support":::

## <a name="add-a-post-request-to-create-kb"></a>KB を作成するための POST 要求を追加する

次のコードは、KB を作成するための HTTPS 要求を QnA Maker API に送信し、その応答を受信するものです。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="post":::

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="post_create_kb":::

この API 呼び出しは、操作 ID を含んだ JSON 応答を返します。 この操作 ID を使用して、KB が正常に作成されたかどうかを判断します。

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>作成状態を確認するための GET 要求を追加する

操作の状態を確認します。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="get":::

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="get_status":::

この API 呼び出しは、操作の状態を含んだ JSON 応答を返します。

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

成功または失敗が返されるまで、この呼び出しを繰り返します。

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>CreateKB メソッドを追加する

次のメソッドは、KB を作成して、状態のチェックを繰り返し実行するものです。  _create_ という**操作 ID** は、POST 応答のヘッダー フィールド **Location** で返された後、GET 要求でルートの一部として使用されます。 KB の作成には多少時間がかかる場合があるため、成功と失敗のどちらかの状態になるまで、呼び出しを繰り返して状態をチェックする必要があります。 操作が成功すると、KB ID が **resourceLocation** で返されます。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="create_kb":::

## <a name="add-the-createkb-method-to-main"></a>CreateKB メソッドを Main に追加する

CreateKB メソッドを呼び出すように Main メソッドに変更を加えます。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="main":::

## <a name="build-and-run-the-program"></a>プログラムをビルドして実行する

プログラムをビルドして実行します。 QnA Maker API に要求が自動的に送信され、KB が作成された後、30 秒おきに結果がポーリングされます。 それぞれの応答は、コンソール ウィンドウに出力されます。

作成されたナレッジ ベースは、QnA Maker ポータルの [[My knowledge bases]\(マイ ナレッジ ベース\)](https://www.qnamaker.ai/Home/MyServices) ページで確認できます。


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2092179)
