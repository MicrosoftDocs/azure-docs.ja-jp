---
title: クイック スタート:音声を認識する、Unity - Speech Service
titleSuffix: Azure Cognitive Services
description: このガイドでは、Unity と Unity 用 Speech SDK を使用して音声テキスト変換アプリケーションを作成します。 完了すると、お使いのコンピューターのマイクを使用して、リアルタイムに文字起こし (音声テキスト変換) することができます。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: jhakulin
ms.openlocfilehash: a7ac6831fd21fcc0dc425b57f5d73d8c328f9350
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803457"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity"></a>クイック スタート:Unity 用 Speech SDK を使用して音声を認識する

クイック スタートは[テキスト読み上げ](quickstart-text-to-speech-csharp-unity.md)にも使用できます。

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

このガイドでは、[Unity](https://unity3d.com/) と Unity 用 Speech SDK を使用して音声テキスト変換アプリケーションを作成します。
完了すると、お使いのデバイスに話しかけて、リアルタイムに文字起こし (音声テキスト変換) することができます。
Unity を初めて使用する場合は、アプリケーションを開発する前に [Unity のユーザー マニュアル](https://docs.unity3d.com/Manual/UnityManual.html)を調べることをお勧めします。

> [!NOTE]
> Unity 用 Speech SDK では、Windows デスクトップ (x86 および x64) またはユニバーサル Windows プラットフォーム (x86、x64、ARM、ARM64)、Android (x86、ARM32、ARM64)、および iOS (x64 シミュレーター、ARM32 および ARM64) がサポートされています

## <a name="prerequisites"></a>前提条件

このプロジェクトを完了するには、以下が必要になります。

- [Unity 2018.3 以降](https://store.unity.com/)および [UWP ARM64 のサポートを追加する Unity 2019.1](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)。
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。 Visual Studio 2017 バージョン 15.9 以降も許容されます。
- Windows ARM64 をサポートするため、[ARM64 用のオプションのビルド ツール、および ARM64 用の Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) をインストールします。
- 音声サービス用のサブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。
- お使いのコンピューターに備わっているマイクへのアクセス。

## <a name="create-a-unity-project"></a>Unity プロジェクトを作成する

1. Unity を開きます。 Unity を初めて使用する場合は、 **[Unity Hub]** *<version number>* ウィンドウが表示されます (Unity Hub を直接開いてもこのウィンドウにアクセスできます)。

   [![[Unity Hub] ウィンドウ](media/sdk/qs-csharp-unity-hub.png)](media/sdk/qs-csharp-unity-hub.png#lightbox)
1. **[新規]** を選択します。 **[Create a new project with Unity]\(Unity を使用して新しいプロジェクトを作成する\)** *<version number>* ウィンドウが表示されます。

   [![Unity Hub で新しいプロジェクトを作成する](media/sdk/qs-csharp-unity-create-a-new-project.png)](media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. **[Project Name]\(プロジェクト名\)** に「**csharp-unity**」と入力します。
1. **[Templates]\(テンプレート\)** で **[3D]** がまだ選択されていない場合は、それを選択します。
1. **[Location]\(場所\)** で、プロジェクトを保存するフォルダーを選択するか作成します。
1. **作成** を選択します。

少し時間が経つと、Unity エディターのウィンドウが表示されます。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

Unity 用 Speech SDK をインストールするには、次の手順を実行します。

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

1. Unity のアセット パッケージ (.unitypackage) としてパッケージ化された [Unity 用 Speech SDK](https://aka.ms/csspeech/unitypackage) をダウンロードして開きます。 アセット パッケージを開くと、 **[Import Unity Package]\(Unity パッケージのインポート\)** ダイアログ ボックスが表示されます。

   [![Unity エディターの [Import Unity Package]\(Unity パッケージのインポート\) ダイアログ ボックス](media/sdk/qs-csharp-unity-01-import.png)](media/sdk/qs-csharp-unity-01-import.png#lightbox)
1. すべてのファイルが選択されていることを確認したら、 **[Import]\(インポート\)** を選択します。 しばらくすると、Unity アセット パッケージがプロジェクトにインポートされます。

Unity にアセット パッケージをインポートする方法の詳細については、[Unity のドキュメント](https://docs.unity3d.com/Manual/AssetPackages.html)を参照してください。

## <a name="add-ui"></a>UI を追加する

ここで、最小の UI をシーンに追加してみましょう。 この UI は、音声認識をトリガーするためのボタンと結果を表示するテキスト フィールドで構成されています。 [ **[Hierarchy]\(階層\)** ウィンドウ](https://docs.unity3d.com/Manual/Hierarchy.html)に、Unity によって新しいプロジェクトで作成されたサンプル シーンが表示されます。

1. **[Hierarchy]\(階層\)** ウィンドウの上部で、 **[Create]\(作成\)**  >  **[UI]**  >  **[Button]\(ボタン\)** の順に選択します。

   この操作により、**ボタン** オブジェクト、ボタンを含む **キャンバス** オブジェクト、**EventSystem** オブジェクトの 3 つのゲーム オブジェクトが作成され、 **[Hierarchy]\(階層\)** ウィンドウに表示されます。

   [![Unity エディター環境](media/sdk/qs-csharp-unity-editor-window.png)](media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [ **[Scene]\(シーン\)** ビュー](https://docs.unity3d.com/Manual/SceneViewNavigation.html)に移動して、[ **[Scene]\(シーン\)** ビュー](https://docs.unity3d.com/Manual/UsingTheSceneView.html)内でキャンバスとボタンが適切に表示されていることを確認します。

1. [ **[Inspector]\(インスペクター\)** ウィンドウ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (既定では右側) で、 **[Pos X]\(座標 X\)** プロパティと **[Pos Y]\(座標 Y\)** プロパティを **0** に設定して、ボタンがキャンバスの中央に配置されるようにします。

1. **[Hierarchy]\(階層\)** ウィンドウで、 **[Create]\(作成\)**  >  **[UI]**  >  **[Text]\(テキスト\)** の順に選択して、**テキスト** オブジェクトを作成します。

1. **[インスペクター]** ウィンドウで、 **[Pos X]\(座標 X\)** プロパティと **[Pos Y]\(座標 Y\)** プロパティを **0** と **120** に設定して、 **[Width]\(幅\)** プロパティと **[Height]\(高さ\)** プロパティを **240** と **120** に設定します。 これらの値により、テキスト フィールドとボタンが重ならないようにします。

完了すると、 **[Scene]\(シーン\)** ビューは次のスクリーンショットのようになります。

[![Unity エディターの [Scene]\(シーン\) ビュー](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>サンプル コードを追加する

Unity プロジェクトにサンプル スクリプト コードを追加するには、次の手順に従います。

1. [プロジェクト ウィンドウ](https://docs.unity3d.com/Manual/ProjectView.html)で、 **[Create]\(作成\)**  >  **[C# script]\(C# スクリプト\)** を選択して、新しい C# スクリプトを追加します。

   [![Unity エディターのプロジェクト ウィンドウ](media/sdk/qs-csharp-unity-project-window.png)](media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. スクリプトに `HelloWorld` という名前を付けます。

1. `HelloWorld` をダブルクリックして、新しく作成したスクリプトを編集します。

   > [!NOTE]
   > コード エディターを Unity で編集のために使用するように構成するには、 **[編集]**  >  **[ユーザー設定]** を選択して、 **[外部ツール]** 設定にアクセスします。 詳細については、[Unity のユーザー マニュアル](https://docs.unity3d.com/Manual/Preferences.html)を参照してください。

1. 既存のスクリプトを次のコードに置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. `YourSubscriptionKey` という文字列を探し、実際の Speech Services のサブスクリプション キーに置き換えます。

1. `YourServiceRegion` という文字列を探し、実際のサブスクリプションに関連付けられている[リージョン](regions.md)に置き換えます。 たとえば、無料試用版を使用している場合、リージョンは `westus` になります。

1. スクリプトへの変更を保存します。

Unity エディターに戻り、ゲーム オブジェクトの 1 つにコンポーネントとしてスクリプトを追加します。

1. **[Hierarchy]\(階層\)** ウィンドウで **[Canvas]\(キャンバス\)** オブジェクトを選択します。

1. **[Inspector]\(インスペクター\)** ウィンドウで、 **[Add Component]\(コンポーネントの追加\)** ボタンを選択します。

   [![Unity エディターの [Inspector]\(インスペクター\) ウィンドウ](media/sdk/qs-csharp-unity-inspector-window.png)](media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. ドロップダウン リストで、上記で作成した `HelloWorld` スクリプトを検索して追加します。 **[Inspector]\(インスペクター\)** ウィンドウに **[Hello World (Script)]** セクションが表示され、初期化されていない 2 つのプロパティ (**Output Text** と **Start Reco Button**) が一覧表示されます。 これらの Unity コンポーネント プロパティは、`HelloWorld` クラスのパブリック プロパティと一致します。

1. **Start Reco Button** プロパティのオブジェクト ピッカー (プロパティの右側にある小さな円アイコン) を選択し、先ほど作成した**ボタン** オブジェクトを選択します。

1. **Output Text** プロパティのオブジェクト ピッカーを選択し、先ほど作成した**テキスト** オブジェクトを選択します。

   > [!NOTE]
   > このボタンには、入れ子になったテキスト オブジェクトも含まれています。 テキスト出力用として、誤ってこれを選択しないようにしてください (混乱を避けるために、 **[Inspector]\(インスペクター\)** ウィンドウの **[Name]\(名前\)** フィールドを使用していずれかのテキスト オブジェクトの名前を変更してもかまいません)。

## <a name="run-the-application-in-the-unity-editor"></a>Unity エディターでアプリケーションを実行する

これで、Unity エディター内でアプリケーションを実行する準備ができました。

1. Unity エディターのツールバー (メニュー バーの下) で、 **[Play]\(再生\)** ボタン (右向きの三角形) を選択します。

1. [ **[Game]\(ゲーム\)** ビュー](https://docs.unity3d.com/Manual/GameView.html)にアクセスし、**テキスト** オブジェクトに **[Click button to recognize speech]\(ボタンをクリックして音声を認識する\)** が表示されるまで待機します (アプリケーションが開始されていないか、応答する準備ができていない場合は、**新しいテキスト**が表示されます)。

1. ボタンを選択し、コンピューターのマイクに向かって英語のフレーズや文章を話します。 音声が Speech Services に送信されてテキストに変換され、 **[Game]\(ゲーム\)** ビューに表示されます。

   [![Unity エディターの [Game]\(ゲーム\) ビュー](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. [ **[Console]\(コンソール\)** ウィンドウ](https://docs.unity3d.com/Manual/Console.html)でデバッグ メッセージを確認します。 **[Console]\(コンソール\)** ウィンドウが表示されていない場合は、メニュー バーにアクセスし、 **[Window]\(ウィンドウ\)**  >  **[General]\(全般\)**  >  **[Console]\(コンソール\)** の順に選択して表示します。

1. 音声の認識が完了したら、Unity エディターのツール バーにある **[Play]\(再生\)** ボタンを選択してアプリケーションを停止します。

## <a name="additional-options-to-run-this-application"></a>このアプリケーションを実行するための追加のオプション

このアプリケーションは、Android アプリ、Windows スタンドアロン アプリ、または UWP アプリケーションとしてデプロイすることもできます。
詳細については、Microsoft の[サンプル リポジトリ](https://aka.ms/csspeech/samples)を参照してください。 `quickstart/csharp-unity` フォルダーには、これらの追加のターゲットの構成が記述されています。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で C# のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [Custom Speech 用のモデルをトレーニングする](how-to-custom-speech-train-model.md)
