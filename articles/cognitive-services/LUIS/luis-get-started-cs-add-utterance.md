---
title: 変更、アプリのトレーニング、C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: この C# クイック スタートでは、Home Automation アプリに発話の例を追加してアプリをトレーニングします。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: d44877e35687745ddba51d4d015729e62106c9ca
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348438"
---
# <a name="quickstart-change-model-using-c"></a>クイック スタート: C# を使ってモデルを変更する

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* 最新の [**Visual Studio Community エディション**](https://www.visualstudio.com/downloads/)。
* C# プログラミング言語がインストールされていること。
* NuGet パッケージ ([JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) と [CommandLine](https://www.nuget.org/packages/CommandLineParser/))。

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>発話の例の JSON ファイル

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>クイック スタート コードの作成 

Visual Studio 上で .NET Framework を使用して新しい **Windows クラシック デスクトップ コンソール** アプリを作成します。 プロジェクトに `ConsoleApp1` と名前を付けます。

![Visual Studio プロジェクトの種類](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>System.Web の依存関係を追加する

Visual Studio プロジェクトに **System.Web** が必要です。 ソリューション エクスプローラーで **[参照]** を右クリックし、[アセンブリ] セクションから **[参照の追加]** を選択します。

![System.web の参照を追加する](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>その他の依存関係を追加する

Visual Studio プロジェクトに **JsonFormatterPlus** と **CommandLineParser** が必要です。 ソリューション エクスプローラーで、 **[参照設定]** を右クリックし、 **[NuGet パッケージの管理]** を選択します。2 つのパッケージをそれぞれ参照して追加します。 

![サード パーティの依存関係の追加](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>C# コードを作成する
次のような **Program.cs** ファイルを作成します。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

次のように依存関係を更新します。

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


LUIS ID と文字列を **Program** クラスに追加します。

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

コマンド ライン パラメーターを管理するためのクラスを **Program** クラスに追加します。

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

**Program** クラスに GET 要求メソッドを追加します。

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


**Program** クラスに POST 要求メソッドを追加します。 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

発話の例をファイルから追加するメソッドを **Program** クラスに追加します。

   [!code-csharp[Add example utterances from file.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.")]

モデルに変更が適用されたら、モデルをトレーニングします。 メソッドを **Program** クラスに追加します。

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

トレーニングはすぐには完了しない場合があります。状態をチェックして、トレーニングの完了を確認してください。 メソッドを **Program** クラスに追加します。

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

コマンド ライン引数を管理するためのメイン コードを追加します。 メソッドを **Program** クラスに追加します。

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>utterances.json を出力ディレクトリにコピーします。

ソリューション エクスプローラーで、ソリューション エクスプローラーのプロジェクト名を右クリックして `utterances.json` を追加し、 **[追加]** 、 **[既存の項目]** の順に選択します。 `utterances.json` ファイルを選択します。 これで、ファイルがプロジェクトに追加されます。 次に、これを出力方向に追加する必要があります。 `utterances.json` を右クリックし、 **[プロパティ]** を選択します。 プロパティ ウィンドウで、`Content` の **[ビルド アクション]** と、`Copy Always` の **[出力ディレクトリにコピー]** をマークします。  

![JSON ファイルをコンテンツとしてマークする](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>コードのビルド

Visual Studio でコードをビルドします。 

## <a name="run-code"></a>コードの実行

プロジェクトの /bin/Debug ディレクトリで、コマンド ラインからアプリケーションを実行します。 

```console
ConsoleApp1.exe --add utterances.json --train --status
```

このコマンドラインで、発話の追加 API を呼び出した結果が表示されます。 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイック スタートで作成したファイルは、完了後、すべて削除してください。 

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [プログラムで LUIS アプリを構築する](luis-tutorial-node-import-utterances-csv.md) 
