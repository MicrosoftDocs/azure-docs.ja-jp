---
title: 意図の取得、Java
titleSuffix: Language Understanding - Azure Cognitive Services
description: この Java のクイック スタートでは、提供されているパブリック LUIS アプリを使って、会話形式のテキストからユーザーの意図を判断します。
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 42240c7b45029684e51c25419eab7f4378785a4d
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276154"
---
# <a name="quickstart-get-intent-using-java"></a>クイック スタート: Java を使用した意図の取得

このクイック スタートでは、LUIS エンドポイントに発話を渡して、意図とエンティティを取得します。

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>前提条件

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) または任意の IDE。
* パブリック アプリ ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS キーを取得する

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>ブラウザーで意図を取得する

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>プログラムで意図を取得する

Java を使用して、前の手順でブラウザー ウィンドウに表示されたものと同じ結果にアクセスできます。 プロジェクトには、必ず Apache ライブラリを追加してください。

1. `LuisGetRequest.java` という名前のファイルに次のコードをコピーしてクラスを作成します。

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. `YOUR-KEY` 変数の値は、実際の LUIS キーに置き換えます。

3. コマンド ラインから「`javac -cp .;<FILE_PATH>\* LuisGetRequest.java`」と入力して、java プログラムをコンパイルします。ファイル パスは、実際のパスに置き換えてください。

4. コマンド ラインから「`java -cp .;<FILE_PATH>\* LuisGetRequest.java`」と入力して、アプリケーションを実行します。ファイル パスは、実際のパスに置き換えてください。 前の手順でブラウザー ウィンドウに表示されたものと同じ JSON が表示されます。

    ![コンソール ウィンドウに LUIS の JSON の結果が表示される](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>LUIS キー

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Java ファイル/プロジェクト フォルダーを削除します。

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [発話の追加](luis-get-started-java-add-utterance.md)
