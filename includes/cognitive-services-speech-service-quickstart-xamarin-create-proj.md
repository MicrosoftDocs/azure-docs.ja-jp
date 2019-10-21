---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0e4e67710c98b80dce2b0d55a86869625f3942d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837408"
---
Xamarin を使用したクロスプラットフォーム Mobile App .NET 開発用の Visual Studio プロジェクトを作成するためには、Visual Studio 開発オプションの設定、プロジェクトの作成、ターゲット アーキテクチャの選択、Speech SDK のインストールが必要となります。

### <a name="set-up-visual-studio-development-options"></a>Visual Studio の開発オプションを設定する

最初に、.NET を使用したクロスプラットフォーム モバイル開発向けに Visual Studio を正しく設定します。

1. Visual Studio 2019 を開きます。

1. Visual Studio のメニュー バーから、 **[ツール]**  >  **[ツールと機能を取得]** の順に選択して Visual Studio インストーラーを開き、 **[変更中]** ダイアログ ボックスを表示します。

   ![[ワークロード] タブ、[変更中] ダイアログ ボックス、Visual Studio インストーラー](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. **[ワークロード]** タブの **[Windows]** で、 **[.NET によるモバイル開発]** ワークロードを見つけます。 そのワークロードの横のチェック ボックスが既にオンになっている場合は、 **[Modifying]\(変更中\)** ダイアログ ボックスを閉じて、手順 5. に進んでください。

1. **[.NET によるモバイル開発]** チェック ボックスをオンにし、 **[変更]** を選択して、 **[開始する前]** ダイアログ ボックスで **[続行]** を選択し、.NET によるモバイル開発ワークロードをインストールします。 新しい機能のインストールにはしばらく時間がかかります。

1. Visual Studio インストーラーを閉じます。

### <a name="create-the-project"></a>プロジェクトを作成する

1. Visual Studio メニュー バーで、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択して **[新しいプロジェクトの作成]** ウィンドウを表示します。

   ![新しいプロジェクトの作成 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. **[Mobile app (Xamarin Forms)]\(モバイル アプリ (Xamarin Forms)\)** を見つけて選択します。

1. **[次へ]** を選択して、 **[新しいプロジェクトの構成]** 画面を表示します。 

   ![新しいプロジェクトの構成 - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. **[プロジェクト名]** に「`helloworld`」と入力します。

1. **[場所]** で、ご自分のプロジェクトの保存先フォルダーに移動して選択するか、フォルダーを作成します。

1. **[作成]** を選択して **[新しいモバイル アプリ Xamarin Forms プロジェクト]** ウィンドウに移動します。

   ![[新しいユニバーサル Windows プラットフォーム プロジェクト] ダイアログ ボックス - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. **[空白]** テンプレートを選択します

1. **[プラットフォーム]** で、 **[Android]** 、 **[iOS]** 、および **[Windows (UWP)]** のチェック ボックスをオンにします。

1. **[OK]** を選択します。 Visual Studio IDE に戻ります。このとき、新しいプロジェクトが作成されて **[ソリューション エクスプローラー]** ウィンドウに表示された状態になっています。

   ![helloworld プロジェクト - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

次に、ターゲット プラットフォーム アーキテクチャとスタートアップ プロジェクトを選択します。 Visual Studio ツール バーの **[ソリューション プラットフォーム]** ドロップダウン ボックスを見つけます。 (表示されない場合は、 **[表示]**  >  **[ツール バー]**  >  **[標準]** の順に選択すると、 **[ソリューション プラットフォーム]** を含んだツール バーが表示されます。)64 ビット Windows を実行している場合は、ドロップダウン ボックスで **[x64]** を選択します。 64 ビット Windows でも 32 ビット アプリケーションを実行できるため、必要に応じて **[x86]** を選択してもかまいません。 **[Start-up Projects]\(スタートアップ プロジェクト\)** ドロップダウン ボックスで、"helloworld.UWP (ユニバーサル Windows)" を設定します。

### <a name="install-the-speech-sdk"></a>Speech SDK のインストール

[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールし、自分のプロジェクト内で Speech SDK を参照します。

1. **ソリューション エクスプローラー**でソリューションを右クリックし、 **[ソリューションの NuGet パッケージの管理]** を選択して **[NuGet - ソリューション]** ウィンドウに移動します。

1. **[参照]** を選択します。

   ![ソリューションのパッケージの管理ダイアログ ボックスのスクリーン ショット](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. **[パッケージ ソース]** で **[nuget.org]** を選択します。

1. **[検索]** ボックスに「`Microsoft.CognitiveServices.Speech`」と入力し、このパッケージが検索結果に表示されたらそれを選択します。

   ![ソリューションのパッケージの管理ダイアログ ボックスのスクリーン ショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)
   > 注: Microsoft.CognitiveServices.Speech nuget 内の iOS ライブラリでは、ビットコードが有効になっていません。 ビットコードが有効になっているライブラリがアプリケーションで必要な場合は、特に iOS プロジェクト用に Microsoft.CognitiveServices.Speech.Xamarin.iOS nuget を使用してください。

1. 検索結果の横のパッケージ状態ウィンドウで、すべてのプロジェクトを選択します。 **helloworld**、**helloworld.Android**、**helloworld.iOS** および **helloworld.UWP**。

1. **[インストール]** を選択します。

1. **[変更のプレビュー]** ダイアログ ボックスで **[OK]** を選択します。

1. **[ライセンスへの同意]** ダイアログ ボックスでライセンスを確認し、 **[I Accept]\(同意する\)** を選択して、すべてのプロジェクトに Speech SDK パッケージ参照をインストールします。 インストールが正常に完了すると、helloworld.iOS に対して次の警告が表示される場合があります。 これは既知の問題であり、アプリの機能に影響を与えることはありません。

> 参照 "C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a" を解決できませんでした。 この参照がコードで必要とされる場合、コンパイル エラーが発生する可能性があります。
