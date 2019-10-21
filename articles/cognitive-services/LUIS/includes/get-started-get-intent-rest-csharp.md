---
title: C# で REST 呼び出しを使用して意図を取得する
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e6ae9590cee3a2ddc3b8e121161fcf84815da28a
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838544"
---
## <a name="prerequisites"></a>前提条件

* [Visual Studio Community 2017 エディション](https://visualstudio.microsoft.com/vs/community/)
* C# プログラミング言語 (VS Community 2017 に含まれています)
* パブリック アプリ ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS キーを取得する

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>プログラムで意図を取得する

前のセクションでブラウザー ウィンドウに表示された結果は、C# を使って予測エンドポイントの GET [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) を照会することでも取得できます。 

1. Visual Studio で、新しいコンソール アプリケーションを作成します。 

    ![Visual Studio で新しいコンソール アプリケーションを作成する](../media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. Visual Studio プロジェクトで、ソリューション エクスプローラーの **[参照の追加]** を選択し、[アセンブリ] タブから **[System.Web]** を選択します。

    ![[参照の追加] を選択して [アセンブリ] タブから System.Web を選択する](../media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Program.cs を次のコードで上書きします。
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. `YOUR_KEY` の値を、実際の LUIS キーに置き換えます。

5. コンソール アプリケーションをビルドして実行します。 前の手順でブラウザー ウィンドウに表示されたものと同じ JSON が表示されます。

    ![コンソール ウィンドウに LUIS の JSON の結果が表示される](../media/luis-get-started-cs-get-intent/console-turn-on.png)



## <a name="luis-keys"></a>LUIS キー

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートを完了したら、Visual Studio プロジェクトを閉じ、ファイル システムからプロジェクトのディレクトリを削除します。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [C# を使った発話の追加とトレーニング](../luis-get-started-cs-add-utterance.md)