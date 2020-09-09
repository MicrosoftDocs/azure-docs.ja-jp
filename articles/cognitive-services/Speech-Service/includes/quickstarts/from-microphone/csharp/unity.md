---
title: クイック スタート:マイクから音声を認識する、C# (Unity) - Speech サービス
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.custom: devx-track-csharp
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 8307cfee90fdf65d119bbc1e55eabb0533a4a6ad
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926438"
---
> [!NOTE]
> Unity 用 Speech SDK では、Windows デスクトップ (x86 および x64) またはユニバーサル Windows プラットフォーム (x86、x64、ARM、ARM64)、Android (x86、ARM32、ARM64)、および iOS (x64 シミュレーター、ARM32 および ARM64) がサポートされています

## <a name="prerequisites"></a>前提条件

開始する前に、以下の操作を行います。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [開発環境を設定し、空のプロジェクトを作成する](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)
> * オーディオ キャプチャ用のマイクにアクセスできることを確認する

既にこれを行っている場合は、これで完了です。 このまま続けましょう。

## <a name="create-a-unity-project"></a>Unity プロジェクトを作成する

1. Unity を開きます。 Unity を初めて使用する場合は、 **[Unity Hub]** *<version number>* ウィンドウが表示されます (Unity Hub を直接開いてもこのウィンドウにアクセスできます)。

   [![[Unity Hub] ウィンドウ](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. **[新規]** を選択します。 **[Create a new project with Unity]\(Unity を使用して新しいプロジェクトを作成する\)** *<version number>* ウィンドウが表示されます。

   [![Unity Hub で新しいプロジェクトを作成する](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. **[Project Name]\(プロジェクト名\)** に「**csharp-unity**」と入力します。
1. **[Templates]\(テンプレート\)** で **[3D]** がまだ選択されていない場合は、それを選択します。
1. **[Location]\(場所\)** で、プロジェクトを保存するフォルダーを選択するか作成します。
1. **［作成］** を選択します

少し時間が経つと、Unity エディターのウィンドウが表示されます。



## <a name="add-ui"></a>UI を追加する

ここで、最小の UI をシーンに追加してみましょう。 この UI は、音声認識をトリガーするためのボタンと結果を表示するテキスト フィールドで構成されています。 [ **[Hierarchy]\(階層\)** ウィンドウ](https://docs.unity3d.com/Manual/Hierarchy.html)に、Unity によって新しいプロジェクトで作成されたサンプル シーンが表示されます。

1. **[Hierarchy]\(階層\)** ウィンドウの上部で、 **[Create]\(作成\)**  >  **[UI]**  >  **[Button]\(ボタン\)** の順に選択します。

   この操作により、**ボタン** オブジェクト、ボタンを含む **キャンバス** オブジェクト、**EventSystem** オブジェクトの 3 つのゲーム オブジェクトが作成され、 **[Hierarchy]\(階層\)** ウィンドウに表示されます。

   [![Unity エディター環境](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [ **[Scene]\(シーン\)** ビュー](https://docs.unity3d.com/Manual/SceneViewNavigation.html)に移動して、[ **[Scene]\(シーン\)** ビュー](https://docs.unity3d.com/Manual/UsingTheSceneView.html)内でキャンバスとボタンが適切に表示されていることを確認します。

1. [ **[Inspector]\(インスペクター\)** ウィンドウ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (既定では右側) で、 **[Pos X]\(座標 X\)** プロパティと **[Pos Y]\(座標 Y\)** プロパティを **0** に設定して、ボタンがキャンバスの中央に配置されるようにします。

1. **[Hierarchy]\(階層\)** ウィンドウで、 **[Create]\(作成\)**  >  **[UI]**  >  **[Text]\(テキスト\)** の順に選択して、**テキスト** オブジェクトを作成します。

1. **[インスペクター]** ウィンドウで、 **[Pos X]\(座標 X\)** プロパティと **[Pos Y]\(座標 Y\)** プロパティを **0** と **120** に設定して、 **[Width]\(幅\)** プロパティと **[Height]\(高さ\)** プロパティを **240** と **120** に設定します。 これらの値により、テキスト フィールドとボタンが重ならないようにします。

完了すると、 **[Scene]\(シーン\)** ビューは次のスクリーンショットのようになります。

[![Unity エディターの [Scene]\(シーン\) ビュー](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>サンプル コードを追加する

Unity プロジェクトにサンプル スクリプト コードを追加するには、次の手順に従います。

1. [プロジェクト ウィンドウ](https://docs.unity3d.com/Manual/ProjectView.html)で、 **[Create]\(作成\)**  >  **[C# script]\(C# スクリプト\)** を選択して、新しい C# スクリプトを追加します。

   [![Unity エディターのプロジェクト ウィンドウ](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. スクリプトに `HelloWorld` という名前を付けます。

1. `HelloWorld` をダブルクリックして、新しく作成したスクリプトを編集します。

   > [!NOTE]
   > コード エディターを Unity で編集のために使用するように構成するには、 **[編集]**  >  **[ユーザー設定]** を選択して、 **[外部ツール]** 設定にアクセスします。 詳細については、[Unity のユーザー マニュアル](https://docs.unity3d.com/Manual/Preferences.html)を参照してください。

1. 既存のスクリプトを次のコードに置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. `YourSubscriptionKey` という文字列を探し、実際の Speech サービスのサブスクリプション キーに置き換えます。

1. `YourServiceRegion` という文字列を見つけて、自分のサブスクリプションに関連付けられている[リージョン](https://aka.ms/speech/sdkregion)の**リージョン識別子**に置き換えます。

1. スクリプトへの変更を保存します。

Unity エディターに戻り、ゲーム オブジェクトの 1 つにコンポーネントとしてスクリプトを追加します。

1. **[Hierarchy]\(階層\)** ウィンドウで **[Canvas]\(キャンバス\)** オブジェクトを選択します。

1. **[Inspector]\(インスペクター\)** ウィンドウで、 **[Add Component]\(コンポーネントの追加\)** ボタンを選択します。

   [![Unity エディターの [Inspector]\(インスペクター\) ウィンドウ](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. ドロップダウン リストで、上記で作成した `HelloWorld` スクリプトを検索して追加します。 **[Inspector]\(インスペクター\)** ウィンドウに **[Hello World (Script)]** セクションが表示され、初期化されていない 2 つのプロパティ (**Output Text** と **Start Reco Button**) が一覧表示されます。 これらの Unity コンポーネント プロパティは、`HelloWorld` クラスのパブリック プロパティと一致します。

1. **Start Reco Button** プロパティのオブジェクト ピッカー (プロパティの右側にある小さな円アイコン) を選択し、先ほど作成した**ボタン** オブジェクトを選択します。

1. **Output Text** プロパティのオブジェクト ピッカーを選択し、先ほど作成した**テキスト** オブジェクトを選択します。

   > [!NOTE]
   > このボタンには、入れ子になったテキスト オブジェクトも含まれています。 テキスト出力用として、誤ってこれを選択しないようにしてください (混乱を避けるために、 **[Inspector]\(インスペクター\)** ウィンドウの **[Name]\(名前\)** フィールドを使用していずれかのテキスト オブジェクトの名前を変更してもかまいません)。

## <a name="run-the-application-in-the-unity-editor"></a>Unity エディターでアプリケーションを実行する

これで、Unity エディター内でアプリケーションを実行する準備ができました。

1. Unity エディターのツールバー (メニュー バーの下) で、 **[Play]\(再生\)** ボタン (右向きの三角形) を選択します。

1. [ **[Game]\(ゲーム\)** ビュー](https://docs.unity3d.com/Manual/GameView.html)にアクセスし、**テキスト** オブジェクトに **[Click button to recognize speech]\(ボタンをクリックして音声を認識する\)** が表示されるまで待機します (アプリケーションが開始されていないか、応答する準備ができていない場合は、**新しいテキスト**が表示されます)。

1. ボタンを選択し、コンピューターのマイクに向かって英語のフレーズや文章を話します。 音声が Speech サービスに送信されてテキストに変換され、 **[Game]\(ゲーム\)** ビューに表示されます。

   [![Unity エディターの [Game]\(ゲーム\) ビュー](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. [ **[Console]\(コンソール\)** ウィンドウ](https://docs.unity3d.com/Manual/Console.html)でデバッグ メッセージを確認します。 **[Console]\(コンソール\)** ウィンドウが表示されていない場合は、メニュー バーにアクセスし、 **[Window]\(ウィンドウ\)**  >  **[General]\(全般\)**  >  **[Console]\(コンソール\)** の順に選択して表示します。

1. 音声の認識が完了したら、Unity エディターのツール バーにある **[Play]\(再生\)** ボタンを選択してアプリケーションを停止します。

## <a name="additional-options-to-run-this-application"></a>このアプリケーションを実行するための追加のオプション

このアプリケーションは、Android アプリ、Windows スタンドアロン アプリ、または UWP アプリケーションとしてデプロイすることもできます。
詳細については、Microsoft の[サンプル リポジトリ](https://aka.ms/csspeech/samples)を参照してください。 `quickstart/csharp-unity` フォルダーには、これらの追加のターゲットの構成が記述されています。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

