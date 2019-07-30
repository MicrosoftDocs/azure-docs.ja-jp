---
title: クイック スタート:音声を合成する、Unity - Speech Services
titleSuffix: Azure Cognitive Services
description: このガイドでは、Unity と Unity 用 Speech SDK (ベータ版) を使用してテキスト読み上げアプリケーションを作成します。 完了すると、テキストから音声を合成し、デバイスのスピーカーにリアルタイムで出力できます。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/26/2019
ms.author: yinhew
ms.openlocfilehash: 5240ea45097ce3c0ae7ccbc15a7f99b2f5990832
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467267"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity-beta"></a>クイック スタート:Unity 用 Speech SDK (ベータ版) を使用して音声を合成する

クイック スタートは[音声認識](quickstart-csharp-unity.md)にも使用できます。

このガイドでは、[Unity](https://unity3d.com/) と Unity 用 Speech SDK (ベータ版) を使用してテキスト読み上げアプリケーションを作成します。
完了すると、テキストから音声を合成し、デバイスのスピーカーにリアルタイムで出力できます。
Unity に慣れていない場合は、アプリケーションの開発を始める前に [Unity のユーザー マニュアル](https://docs.unity3d.com/Manual/UnityManual.html)を確認することをお勧めします。

> [!NOTE]
> Unity 用 Speech SDK は、現在ベータ版です。
> Windows デスクトップ (x86 および x64) またはユニバーサル Windows プラットフォーム (x86、x64、ARM/ARM64)、および Android (x86、ARM32/64) がサポートされています。

## <a name="prerequisites"></a>前提条件

このプロジェクトを完了するには、以下が必要になります。

* [Unity 2018.3 以降](https://store.unity.com/)および [UWP ARM64 のサポートを追加する Unity 2019.1](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * ARM64 をサポートするために、[ARM64 用のオプションのビルド ツール、および ARM64 用の Windows10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) をインストールします 
* 音声サービス用のサブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。

## <a name="create-a-unity-project"></a>Unity プロジェクトを作成する

* Unity を起動し、 **[Projects]\(プロジェクト\)** タブの **[New]\(新規\)** を選択します。
* **[Project name]\(プロジェクト名\)** を **csharp-unity** に、 **[Template]\(テンプレート\)** を **[3D]** に指定して、場所を選択します。
  **[Create project]\(プロジェクトの作成\)** を選択します。
* 少し時間が経つと、Unity エディターのウィンドウがポップアップします。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Unity 用 Speech SDK (ベータ版) は、Unity のアセット パッケージとしてパッケージ化されています (.unitypackage)。
  これは、[こちら](https://aka.ms/csspeech/unitypackage)からダウンロードできます。
* **[Assets]\(アセット\)**  >  **[Import Package]\(パッケージのインポート\)**  >  **[Custom Package]\(カスタム パッケージ\)** の順に選択して、Speech SDK をインポートします。
  詳細については、[Unity のドキュメント](https://docs.unity3d.com/Manual/AssetPackages.html)を参照してください。
* ファイル ピッカーで、先ほどダウンロードした Speech SDK の .unitypackage ファイルを選択します。
* すべてのファイルが選択されていることを確認したら、 **[Import]\(インポート\)** をクリックします。

  ![Speech SDK Unity アセット パッケージをインポートするときの Unity エディターのスクリーンショット](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>UI を追加する

合成するテキストを入力するための入力フィールド、音声合成をトリガーするボタン、および結果を表示するテキスト フィールドから成る最小限の UI をシーンに追加します。

* [[Hierarchy]\(ヒエラルキー\) ウィンドウ](https://docs.unity3d.com/Manual/Hierarchy.html) (既定では左側) に、Unity の新しいプロジェクトで作成されたサンプルのシーンが表示されます。
* [Hierarchy]\(ヒエラルキー\) ウィンドウの上部にある **[Create]\(作成\)** をクリックし、 **[UI]**  >  **[Input Field]\(入力フィールド\)** の順に選択します。
* これで、3 つのゲーム オブジェクトが作成され、[Hierarchy]\(ヒエラルキー\) ウィンドウに表示されます。 **[Canvas]\(キャンバス\)** オブジェクト、その中で入れ子になった **[Input Field]\(入力フィールド\)** オブジェクト、および **[EventSystem]** オブジェクトです。
* [[Scene]\(シーン\) ビューに移動](https://docs.unity3d.com/Manual/SceneViewNavigation.html)して、[[Scene]\(シーン\) ビュー](https://docs.unity3d.com/Manual/UsingTheSceneView.html)内でキャンバスと入力フィールドが適切に表示されていることを確認します。
* [Hierarchy]\(ヒエラルキー\) ウィンドウの **[Input Field]\(入力フィールド\)** オブジェクトをクリックして、その設定を [[Inspector]\(インスペクター\) ウィンドウ](https://docs.unity3d.com/Manual/UsingTheInspector.html)に表示します (既定では右側)。
* **[Pos X]\(座標 X\)** プロパティと **[Pos Y]\(座標 Y\)** プロパティを **0** に設定して、入力フィールドがキャンバスの中央に配置されるようにします。
* [Hierarchy]\(ヒエラルキー\) ウィンドウの上部にある **[Create]\(作成\)** を再度クリックし、 **[UI]**  >  **[Button]\(ボタン\)** の順に選択して、ボタンを作成します。
* [Hierarchy]\(ヒエラルキー\) ウィンドウの **[Button]\(ボタン\)** オブジェクトをクリックして、その設定を [[Inspector]\(インスペクター\) ウィンドウ](https://docs.unity3d.com/Manual/UsingTheInspector.html)に表示します (既定では右側)。
* **[Pos X]\(座標 X\)** プロパティと **[Pos Y]\(座標 Y\)** プロパティを **0** と **-48** に設定し、 **[Width]\(幅\)** プロパティと **[Height]\(高さ\)** プロパティを **160** と **30** に設定して、ボタンと入力フィールドが重ならないようにします。
* [Hierarchy]\(ヒエラルキー\) ウィンドウの上部にある **[Create]\(作成\)** を再度クリックし、 **[UI]**  >  **[Text]\(テキスト\)** の順に選択して、テキスト フィールドを作成します。
* [Hierarchy]\(ヒエラルキー\) ウィンドウの **[Text]\(テキスト\)** オブジェクトをクリックして、その設定を [[Inspector]\(インスペクター\) ウィンドウ](https://docs.unity3d.com/Manual/UsingTheInspector.html)に表示します (既定では右側)。
* **[Pos X]\(座標 X\)** プロパティと **[Pos Y]\(座標 Y\)** プロパティを **0** と **80** に設定し、 **[Width]\(幅\)** プロパティと **[Height]\(高さ\)** プロパティを **320** と **80** に設定して、テキスト フィールドと入力フィールドが重ならないようにします。
* [Hierarchy]\(ヒエラルキー\) ウィンドウの上部にある **[Create]\(作成\)** を再度クリックし、 **[Audio]\(オーディオ\)**  >  **[Audio Source]\(オーディオ ソース\)** の順に選択して、オーディオ ソースを作成します。

完了したら、次のスクリーンショットのような UI になります。

[![Unity エディターにおけるクイック スタートのユーザー インターフェイスを示すスクリーンショット](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>サンプル コードを追加する

1. [[Project]\(プロジェクト\) ウィンドウ](https://docs.unity3d.com/Manual/ProjectView.html) (既定では左下) で **[Create]\(作成\)** をクリックし、 **[C# script]\(C# スクリプト\)** を選択します。 スクリプトに `HelloWorld` という名前を付けます。

1. ダブルクリックしてスクリプトを編集します。

   > [!NOTE]
   > **[Edit]\(編集\)**  >  **[Preferences]\(設定\)** で、起動するコード エディターを構成できます。[Unity のユーザー マニュアル](https://docs.unity3d.com/Manual/Preferences.html)を参照してください。

1. すべてのコードを次のものに置き換えます。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. `YourSubscriptionKey` という文字列を探し、実際の Speech Services のサブスクリプション キーに置き換えます。

1. `YourServiceRegion` という文字列を探し、実際のサブスクリプションに関連付けられている[リージョン](regions.md)に置き換えます。 たとえば、無料試用版を使用している場合、リージョンは `westus` になります。

1. スクリプトへの変更を保存します。

1. Unity エディターに戻ってから、スクリプトをコンポーネントとしてゲーム オブジェクトのいずれかに追加する必要があります。

   * [Hierarchy]\(ヒエラルキー\) ウィンドウの **[Canvas]\(キャンバス\)** オブジェクトをクリックします。 [[Inspector]\(インスペクター\) ウィンドウ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (既定では右側) でその設定が開きます。
   * [Inspector]\(インスペクター\) ウィンドウの **[Add Component]\(コンポーネントの追加\)** をクリックし、上で作成した HelloWorld スクリプトを検索して、追加します。
   * Hello World コンポーネントには、初期化されていない 4 つのプロパティ、**Output Text**、**Input Field**、**Speak Button**、および **Audio Source** が含まれていることに注意してください。これらは `HelloWorld` クラスのパブリック プロパティに一致します。
     これらを接続するには、オブジェクト ピッカー (プロパティの右側にある小さな円アイコン) をクリックして、先ほど作成したテキスト オブジェクトとボタン オブジェクトを選択します。

     > [!NOTE]
     > 入力フィールドとボタンには、入れ子になったテキスト オブジェクトも含まれています。 テキスト出力用として、誤ってこれを選択しないようにしてください (このような混乱を避けるために、[Inspector]\(インスペクター\) ウィンドウの [Name]\(名前\) フィールドを使用してテキスト オブジェクトの名前を変更してもかまいません)。

## <a name="run-the-application-in-the-unity-editor"></a>Unity エディターでアプリケーションを実行する

* Unity エディターのツール バー (メニュー バーの下) の **[Play]\(再生\)** を押します。

* アプリの起動後、入力フィールドにテキストを入力して、ボタンをクリックします。 テキストが Speech Services に転送されて音声に合成され、スピーカーで再生されます。

  [![Unity のゲーム ウィンドウで実行中のクイック スタートのスクリーンショット](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* [[Console]\(コンソール\) ウィンドウ](https://docs.unity3d.com/Manual/Console.html)でデバッグ メッセージを確認します。

* 音声の合成が完了したら、Unity エディターのツール バーにある **[Play]\(再生\)** をクリックしてアプリを停止します。

## <a name="additional-options-to-run-this-application"></a>このアプリケーションを実行するための追加のオプション

このアプリケーションは、Windows のスタンドアロン アプリまたは UWP アプリケーションとして Android にもデプロイできます。
これらの追加のターゲットの構成について説明している、quickstart/csharp-unity フォルダー内の[サンプル リポジトリ](https://aka.ms/csspeech/samples)を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で C# のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [音声フォントをカスタマイズする](how-to-customize-voice-font.md)
- [音声サンプルを録音する](record-custom-voice-samples.md)
