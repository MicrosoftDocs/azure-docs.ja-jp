---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: erhopf
ms.openlocfilehash: 99a7dec6936b86af4ab9b80d266cd886dae66d12
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70381825"
---
C++ デスクトップ開発用の Visual Studio プロジェクトを作成するためには、Visual Studio 開発オプションの設定、プロジェクトの作成、ターゲット アーキテクチャの選択、Speech SDK のインストールが必要となります。 

### <a name="set-up-visual-studio-development-options"></a>Visual Studio の開発オプションを設定する

最初に、C++ デスクトップ開発向けに Visual Studio を正しく設定します。

1. Visual Studio 2019 を開いて**スタート** ウィンドウを表示します。

   ![スタート ウィンドウ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png) 

1. **[コードなしで続行]** を選択して Visual Studio IDE に移動します。

1. Visual Studio のメニュー バーから、 **[ツール]**  >  **[ツールと機能を取得]** の順に選択して Visual Studio インストーラーを開き、 **[変更中]** ダイアログ ボックスを表示します。

   ![[ワークロード] タブ、[変更中] ダイアログ ボックス、Visual Studio インストーラー](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. **[ワークロード]** タブの **[Windows]** で、 **[C++ によるデスクトップ開発]** ワークロードを見つけます。 そのワークロードの横のチェック ボックスがまだオンになっていない場合はオンにしてください。

1. **[個別のコンポーネント]** タブで、 **[NuGet パッケージ マネージャー]** チェック ボックスを見つけます。 チェック ボックスがまだオンになっていない場合はオンにしてください。

1. 隅にある **[閉じる]** ボタンまたは **[変更]** ボタンを選択します (インストールする機能を選択したかどうかによってボタン名は異なります)。 **[変更]** を選択した場合は、インストールが開始されます。インストールにはしばらく時間がかかることがあります。

1. Visual Studio インストーラーを閉じます。

### <a name="create-the-project-and-select-the-target-architecture"></a>プロジェクトを作成してターゲット アーキテクチャを選択する

次に、プロジェクトを作成します。

1. Visual Studio メニュー バーで、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択して **[新しいプロジェクトの作成]** ウィンドウを表示します。

   ![新しいプロジェクトの作成 (C++) - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. **[コンソール アプリ]** を見つけて選択します。 このプロジェクト タイプの (C# や Visual Basic ではなく) C++ バージョンを選択してください。

1. **[次へ]** を選択して、 **[新しいプロジェクトの構成]** 画面を表示します。

   ![新しいプロジェクトの構成 (C++) - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. **[プロジェクト名]** に「`helloworld`」と入力します。

1. **[場所]** で、ご自分のプロジェクトの保存先フォルダーに移動して選択するか、フォルダーを作成します。

次に、ターゲット プラットフォーム アーキテクチャを選択します。 Visual Studio ツール バーの **[ソリューション プラットフォーム]** ドロップダウン ボックスを見つけます。 (表示されない場合は、 **[表示]**  >  **[ツール バー]**  >  **[標準]** の順に選択すると、 **[ソリューション プラットフォーム]** を含んだツール バーが表示されます。)64 ビット Windows を実行している場合は、ドロップダウン ボックスで **[x64]** を選択します。 64 ビット Windows でも 32 ビット アプリケーションを実行できるため、必要に応じて **[x86]** を選択してもかまいません。

### <a name="install-the-speech-sdk"></a>Speech SDK のインストール

最後に、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールし、自分のプロジェクト内の Speech SDK を参照します。

1. **ソリューション エクスプローラー**でソリューションを右クリックし、 **[ソリューションの NuGet パッケージの管理]** を選択して **[NuGet - ソリューション]** ウィンドウに移動します。

1. **[参照]** を選択します。

   ![NuGet - [ソリューション] タブ、Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. **[パッケージ ソース]** で **[nuget.org]** を選択します。

1. **[検索]** ボックスに「`Microsoft.CognitiveServices.Speech`」と入力し、このパッケージが検索結果に表示されたらそれを選択します。

   ![Microsoft.CognitiveServices.Speech C++ パッケージのインストール - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. 検索結果の横のパッケージ状態ウィンドウで、自分の **helloworld** プロジェクトを選択します。

1. **[インストール]** を選択します。

1. **[変更のプレビュー]** ダイアログ ボックスで **[OK]** を選択します。

1. **[ライセンスへの同意]** ダイアログ ボックスでライセンスを確認し、 **[同意する]** を選択します。 パッケージのインストールが開始されます。インストールが完了すると、"`Successfully installed 'Microsoft.CognitiveServices.Speech 1.6.0' to helloworld`" と同様のメッセージが **[出力]** ウィンドウに表示されます。 
