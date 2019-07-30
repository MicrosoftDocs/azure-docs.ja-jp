---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: afe6f1493c7fa8272c67f23d6708ad6e4eea9381
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181468"
---
1. Visual Studio 2017 を起動します。

1. Visual Studio のメニュー バーから **[ツール] > [ツールの取得]** を選択し、 **[.NET デスクトップ開発]** ワークロードが利用できることを確認します。 このワークロードがまだインストールされていない場合は、チェック ボックスをオンにし、 **[変更]** をクリックしてインストールを開始してください。 ダウンロードとインストールに数分かかる場合があります。

   **[.NET デスクトップ開発]** の横のチェック ボックスがオンになっている場合は、ここでダイアログ ボックスを閉じてかまいません。

   ![.NET デスクトップ環境を有効にする](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. 次に、プロジェクトを作成しましょう。 メニュー バーから、 **[ファイル] > [新規作成] > [プロジェクト]** の順に選択します。 ダイアログ ボックスが表示されたら、左側のパネルの **[インストール済み] > [Visual C#] > [Windows デスクトップ]** の各セクションを展開し、 **[コンソール アプリ (.NET Framework)]** を選択します。 このプロジェクトに *helloworld* という名前を付けます。

    ![Visual C# コンソール アプリの作成 (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Visual C# コンソール アプリの作成 (.NET Framework)")

1. プロジェクトが設定されたら、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールして、それをコード内で参照する必要があります。 ソリューション エクスプローラーを探して、[helloworld] を右クリックします。 メニューから **[NuGet パッケージの管理]** を選択します。

   ![右クリック後の [ソリューションの NuGet パッケージの管理]](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "ソリューションの NuGet パッケージの管理")

1. NuGet パッケージ マネージャーの右上隅で **[パッケージ ソース]** ドロップダウンを探し、 **[nuget.org]** が選択されていることを確認します。 次に、 **[参照]** を選択し、`Microsoft.CognitiveServices.Speech` パッケージを検索して、最新の安定バージョンをインストールします。

   ![Microsoft.CognitiveServices.Speech NuGet パッケージのインストール](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Nuget パッケージのインストール")

1. すべての契約とライセンスに同意して、インストールを開始します。

   ![ライセンスに同意](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "ライセンスに同意")

    パッケージがインストールされると、パッケージ マネージャー コンソールに確認メッセージが表示されます。

1. 次の手順で、コンソール アプリケーションのビルドと実行に使用するコンピューターのアーキテクチャに合ったプラットフォーム構成を作成します。 メニュー バーから **[ビルド]**  >  **[構成マネージャー]** を選択します。

    ![構成マネージャーの起動](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "構成マネージャーの起動")

1. **[構成マネージャー]** ダイアログ ボックスで **[アクティブ ソリューション プラットフォーム]** ドロップダウン リストを探し、 **[新規作成]** を選択します。

    ![[構成マネージャー] ウィンドウでの新しいプラットフォームの追加](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "[構成マネージャー] ウィンドウでの新しいプラットフォームの追加")

1. 64 ビット Windows を実行している場合は、 **[新しいプラットフォームを入力または選択してください]** に「`x64`」と入力します。 32 ビット Windows を実行している場合は、`x86` を選択します。 操作が終了したら、 **[OK]** をクリックします。

    ![64 ビット Windows での "x64" という名前の新しいプラットフォームの追加](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "x64 プラットフォームの追加")
