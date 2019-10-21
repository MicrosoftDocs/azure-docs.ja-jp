---
title: Java で REST 呼び出しを使用して意図を取得する
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 7199dfaaa476e4be27929010b76a6e0544857bdb
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838520"
---
## <a name="prerequisites"></a>前提条件

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) または任意の IDE。
* パブリック アプリ ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS キーを取得する

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>プログラムで意図を取得する

Java を使用して、前の手順でブラウザー ウィンドウに表示されたものと同じ結果にアクセスできます。 プロジェクトには、必ず Apache ライブラリを追加してください。

1. `LuisGetRequest.java` という名前のファイルに次のコードをコピーしてクラスを作成します。

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. `YOUR-KEY` 変数の値は、実際の LUIS キーに置き換えます。

3. コマンド ラインから「`javac -cp .;<FILE_PATH>\* LuisGetRequest.java`」と入力して、java プログラムをコンパイルします。ファイル パスは、実際のパスに置き換えてください。

4. コマンド ラインから「`java -cp .;<FILE_PATH>\* LuisGetRequest.java`」と入力して、アプリケーションを実行します。ファイル パスは、実際のパスに置き換えてください。 前の手順でブラウザー ウィンドウに表示されたものと同じ JSON が表示されます。

    ![コンソール ウィンドウに LUIS の JSON の結果が表示される](../media/luis-get-started-java-get-intent/console-turn-on.png)
    


## <a name="luis-keys"></a>LUIS キー

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートを完了したら、Visual Studio プロジェクトを閉じ、ファイル システムからプロジェクトのディレクトリを削除します。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Java を使った発話の追加とトレーニング](../luis-get-started-java-add-utterance.md)