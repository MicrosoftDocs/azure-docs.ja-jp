---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001103"
---
Windows 開発用に Visual Studio プロジェクトを作成するためには、プロジェクトの作成、.NET デスクトップ開発用の Visual Studio 設定、Speech SDK のインストール、ターゲット アーキテクチャの選択が必要となります。

### <a name="create-the-project-and-add-the-workload"></a>プロジェクトを作成してワークロードを追加する

まず、Visual Studio でプロジェクトを作成し、.NET デスクトップ開発用に Visual Studio を設定します。

1. Visual Studio 2019 を開きます。

1. [開始] ウィンドウで、 **[新しいプロジェクトの作成]** を選択します。 

1. **[新しいプロジェクトの作成]** ウィンドウで、**[コンソール アプリ (.NET Framework)]** を選択してから、**[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** ウィンドウの **[プロジェクト名]** に「*helloworld*」と入力し、**保存先** となるディレクトリのパスを選択するか作成して、**[作成]** を選択します。

1. Visual Studio のメニュー バーから、 **[ツール]**  >  **[ツールと機能を取得]** の順に選択して Visual Studio インストーラーを開き、 **[変更中]** ダイアログ ボックスを表示します。

1. **[.NET デスクトップ開発]** ワークロードが利用できるかどうかを確認します。 このワークロードがまだインストールされていない場合は、その横にあるチェック ボックスをオンにし、**[変更]** を選択してインストールを開始してください。 ダウンロードとインストールに数分かかる場合があります。

   **[.NET デスクトップ開発]** の横のチェック ボックスが既にオンになっている場合は、**[閉じる]** を選択してダイアログ ボックスを終了します。

   ![.NET デスクトップ環境を有効にする](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Visual Studio インストーラーを閉じます。

### <a name="install-the-speech-sdk"></a>Speech SDK のインストール

次の手順は、コード内で参照できるように、[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールすることです。

1. ソリューション エクスプローラーで **helloworld** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択して NuGet パッケージ マネージャーを表示します。

   ![NuGet パッケージ マネージャー](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. 右上隅で **[パッケージ ソース]** ドロップダウン ボックスを探し、**[nuget.org]** が選択されていることを確認します。

1. 左上隅で **[参照]** を選択します。

1. 検索ボックスに「*Microsoft.CognitiveServices.Speech*」と入力し、**Enter** キーを押します。

1. 検索結果から **[Microsoft.CognitiveServices.Speech]** パッケージを選択し、**[インストール]** を選択して最新の安定バージョンをインストールします。

   ![Microsoft.CognitiveServices.Speech NuGet パッケージをインストールする](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. すべての契約とライセンスに同意して、インストールを開始します。

   パッケージがインストールされると、 **[パッケージマネージャー コンソール]** ウィンドウに確認が表示されます。

### <a name="choose-the-target-architecture"></a>ターゲット アーキテクチャを選択する

次に、コンソール アプリケーションをビルドして実行する目的で、お使いのコンピューターのアーキテクチャに合ったプラットフォーム構成を作成します。

1. メニュー バーから、 **[ビルド]** 、 **[構成マネージャー]** の順に選択します。 **[構成マネージャー]** ダイアログ ボックスが表示されます。

   ![[構成マネージャー] ダイアログボックス](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. **[アクティブ ソリューション プラットフォーム]** ドロップダウン ボックスで **[新規]** を選択します。 **[新しいソリューション プラットフォーム]** ダイアログ ボックスが表示されます。

1. **[新しいプラットフォームを入力または選択してください]** ドロップダウン ボックスで:
   - 64 ビット Windows を実行している場合、**x64** を選択します。
   - 32 ビット Windows を実行している場合、**x86** を選択します。

1. **[OK]**、**[閉じる]** の順に選択します。
