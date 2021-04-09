---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 7386cfaaf410614c6eeee19669f8f769ad691c1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99215077"
---
ユニバーサル Windows プラットフォーム (UWP) 開発用の Visual Studio プロジェクトを作成するためには、Visual Studio 開発オプションの設定、プロジェクトの作成、ターゲット アーキテクチャの選択、オーディオ キャプチャの設定、Speech SDK のインストールが必要となります。

### <a name="set-up-visual-studio-development-options"></a>Visual Studio の開発オプションを設定する

最初に、UWP 開発向けに Visual Studio を正しく設定します。

1. Visual Studio 2019 を開いて **スタート** ウィンドウを表示します。

   ![[スタート] ウィンドウを示すスクリーンショット。[コードなしで続行] 操作が強調表示されています。](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. **[コードなしで続行]** を選択して Visual Studio IDE に移動します。

1. Visual Studio のメニュー バーから、 **[ツール]**  >  **[ツールと機能を取得]** の順に選択して Visual Studio インストーラーを開き、 **[変更中]** ダイアログ ボックスを表示します。

   ![[変更中] ダイアログ ボックスの [ワークロード] タブを示すスクリーンショット。[ユニバーサル Windows プラットフォーム開発] が強調表示されています。](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. **[ワークロード]** タブの **[Windows]** で、 **[ユニバーサル Windows プラットフォーム開発]** ワークロードを見つけます。 そのワークロードの横のチェック ボックスが既にオンになっている場合は、 **[変更中]** ダイアログ ボックスを閉じて、手順 6. に進んでください。

1. **[ユニバーサル Windows プラットフォーム開発]** チェック ボックスをオンにし、 **[変更]** を選択して、 **[開始する前]** ダイアログ ボックスで **[続行]** を選択し、UWP 開発ワークロードをインストールします。 新しい機能のインストールにはしばらく時間がかかります。

1. Visual Studio インストーラーを閉じます。

### <a name="create-the-project-and-select-the-target-architecture"></a>プロジェクトを作成してターゲット アーキテクチャを選択する

次に、プロジェクトを作成します。

1. Visual Studio メニュー バーで、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択して **[新しいプロジェクトの作成]** ウィンドウを表示します。

   ![[新しいプロジェクトの作成] ウィンドウを示すスクリーンショット。[空白のアプリ (ユニバーサル Windows)] が選択され、[次へ] ボタンが強調表示されています。](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. **[空白のアプリ (ユニバーサル Windows)]** を探して選択します。 このプロジェクト タイプの (Visual Basic ではなく) C# バージョンを選択してください。

1. **[次へ]** を選択して、 **[新しいプロジェクトの構成]** 画面を表示します。

   ![[新しいプロジェクトの構成] 画面を示すスクリーンショット。[プロジェクト名] フィールド、[場所] フィールド、[作成] ボタンが強調表示されています。](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. **[プロジェクト名]** に「`helloworld`」と入力します。

1. **[場所]** で、ご自分のプロジェクトの保存先フォルダーに移動して選択するか、フォルダーを作成します。

1. **[作成]** を選択して **[新しいユニバーサル Windows プラットフォーム プロジェクト]** ウィンドウに移動します。

   ![[新しいユニバーサル Windows プラットフォーム プロジェクト] ダイアログ ボックスを示すスクリーンショット。](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. **[最小バージョン]** (2 番目のドロップダウン ボックス) で、Speech SDK の最小要件である **[Windows 10 Fall Creators Update (10.0; Build 16299)]** を選択します。

1. **[ターゲットのバージョン]** (1 番目のドロップダウン ボックス) で、 **[最小バージョン]** の値と同じかそれより大きい値を選択します。

1. **[OK]** を選択します。 Visual Studio IDE に戻ります。このとき、新しいプロジェクトが作成されて **[ソリューション エクスプローラー]** ウィンドウに表示された状態になっています。

   ![helloworld プロジェクト - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

次に、ターゲット プラットフォーム アーキテクチャを選択します。 Visual Studio ツール バーの **[ソリューション プラットフォーム]** ドロップダウン ボックスを見つけます。 (表示されない場合は、 **[表示]**  >  **[ツール バー]**  >  **[標準]** の順に選択すると、 **[ソリューション プラットフォーム]** を含んだツール バーが表示されます。)64 ビット Windows を実行している場合は、ドロップダウン ボックスで **[x64]** を選択します。 64 ビット Windows でも 32 ビット アプリケーションを実行できるため、必要に応じて **[x86]** を選択してもかまいません。

> [!NOTE]
> Speech SDK は、すべての Intel 互換プロセッサをサポートしていますが、ARM プロセッサの場合は **x64 バージョンのみ** です。

### <a name="set-up-audio-capture"></a>オーディオ キャプチャを設定する

プロジェクトでオーディオ入力をキャプチャできるようにします。

1. **ソリューション エクスプローラー** で **[Package.appxmanifest]** をダブルクリックしてパッケージ アプリケーション マニフェストを開きます。

1. **[機能]** タブを選択します。

   ![[機能] タブ、パッケージ アプリケーション マニフェスト - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. **[マイク]** 機能のチェック ボックスをオンにします。

1. メニュー バーで **[ファイル]**  >  **[Package.appxmanifest の保存]** を選択して変更内容を保存します。

### <a name="install-the-speech-sdk"></a>Speech SDK のインストール

最後に、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールし、自分のプロジェクト内の Speech SDK を参照します。

1. **ソリューション エクスプローラー** でソリューションを右クリックし、 **[ソリューションの NuGet パッケージの管理]** を選択して **[NuGet - ソリューション]** ウィンドウに移動します。

1. **[参照]** を選択します。

   ![[ソリューションのパッケージの管理] ダイアログ ボックスを示すスクリーンショット。[参照] タブ、[検索] ボックス、[パッケージ ソース] が強調表示されています。](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. **[パッケージ ソース]** で **[nuget.org]** を選択します。

1. **[検索]** ボックスに「`Microsoft.CognitiveServices.Speech`」と入力し、このパッケージが検索結果に表示されたらそれを選択します。

   ![[Microsoft.CognitiveServices.Speech] が選択されていることを示すスクリーンショット。プロジェクトと [インストール] ボタンが強調表示されています。](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. 検索結果の横のパッケージ状態ウィンドウで、自分の **helloworld** プロジェクトを選択します。

1. **[インストール]** を選択します。

1. **[変更のプレビュー]** ダイアログ ボックスで **[OK]** を選択します。

1. **[ライセンスへの同意]** ダイアログ ボックスでライセンスを確認し、 **[同意する]** を選択します。 パッケージのインストールが開始されます。インストールが完了すると、"`Successfully installed 'Microsoft.CognitiveServices.Speech 1.15.0' to helloworld`" と同様のメッセージが **[出力]** ウィンドウに表示されます。
