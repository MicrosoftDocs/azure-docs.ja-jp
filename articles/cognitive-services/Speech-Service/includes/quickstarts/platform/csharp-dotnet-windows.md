---
title: クイック スタート:Speech SDK for .NET Framework (Windows) プラットフォームの設定 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech サービス SDK を使用して Windows 用の .NET Framework で C# のプラットフォームを設定するには、このガイドを使用します。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.custom: devx-track-dotnet
ms.openlocfilehash: b47fd90a3fc92234d95ddc9d9f4f14b5af67d757
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926671"
---
このガイドでは、.NET Framework (Windows) 用の [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) をインストールする方法について説明します。 このパッケージ名の使用を自分で開始する場合は、NuGet コンソールで `Install-Package Microsoft.CognitiveServices.Speech` を実行します。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Visual Studio プロジェクトを作成して Speech SDK をインストールする

[Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールして、コード内で参照できるようにする必要があります。 そのためには、必要に応じて最初に **helloworld** プロジェクトを作成します。 **.NET デスクトップ開発**ワークロードを使用できるプロジェクトが既にある場合は、そのプロジェクトを使用し、「[NuGet パッケージ マネージャーを使用して Speech SDK をインストールする](#use-nuget-package-manager-to-install-the-speech-sdk)」に進んでください。

### <a name="create-helloworld-project"></a>helloworld プロジェクトを作成する

1. Visual Studio 2019 を開きます。

1. [スタート] ウィンドウで、 **[新しいプロジェクトの作成]** を選択します。 

1. **[新しいプロジェクトの作成]** ウィンドウで、 **[コンソール アプリ (.NET Framework)]** を選択してから、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** ウィンドウの **[プロジェクト名]** に「*helloworld*」と入力し、**保存先**となるディレクトリのパスを選択するか作成して、 **[作成]** を選択します。

1. Visual Studio のメニュー バーから、 **[ツール]**  >  **[ツールと機能を取得]** の順に選択して Visual Studio インストーラーを開き、 **[変更中]** ダイアログ ボックスを表示します。

1. **[.NET デスクトップ開発]** ワークロードが利用できるかどうかを確認します。 このワークロードがまだインストールされていない場合は、その横にあるチェック ボックスをオンにし、 **[変更]** を選択してインストールを開始してください。 ダウンロードとインストールに数分かかる場合があります。

   **[.NET デスクトップ開発]** の横のチェック ボックスが既にオンになっている場合は、 **[閉じる]** を選択してダイアログ ボックスを終了します。

   ![.NET デスクトップ環境を有効にする](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Visual Studio インストーラーを閉じます。

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>NuGet パッケージ マネージャーを使用して Speech SDK をインストールする

1. ソリューション エクスプローラーで **helloworld** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択して NuGet パッケージ マネージャーを表示します。

   ![NuGet パッケージ マネージャー](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. 右上隅で **[パッケージ ソース]** ドロップダウン ボックスを探し、 **[`nuget.org`]** が選択されていることを確認します。

1. 左上隅で **[参照]** を選択します。

1. 検索ボックスに「*Microsoft.CognitiveServices.Speech*」と入力し、**Enter** キーを押します。

1. 検索結果から **[Microsoft.CognitiveServices.Speech]** パッケージを選択し、 **[インストール]** を選択して最新の安定バージョンをインストールします。

   ![Microsoft.CognitiveServices.Speech NuGet パッケージをインストールする](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. すべての契約とライセンスに同意して、インストールを開始します。

   パッケージがインストールされると、 **[パッケージマネージャー コンソール]** ウィンドウに確認が表示されます。

### <a name="choose-target-architecture"></a>ターゲット アーキテクチャを選択する

コンソール アプリケーションをビルドして実行するには、お使いのコンピューターのアーキテクチャに合ったプラットフォーム構成を作成します。

1. メニュー バーから、 **[ビルド]** 、 **[構成マネージャー]** の順に選択します。 **[構成マネージャー]** ダイアログ ボックスが表示されます。

   ![[構成マネージャー] ダイアログボックス](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. **[アクティブ ソリューション プラットフォーム]** ドロップダウン ボックスで **[新規]** を選択します。 **[新しいソリューション プラットフォーム]** ダイアログ ボックスが表示されます。

1. **[新しいプラットフォームを入力または選択してください]** ドロップダウン ボックスで:
   - 64 ビット Windows を実行している場合、**x64** を選択します。
   - 32 ビット Windows を実行している場合、**x86** を選択します。

1. **[OK]** 、 **[閉じる]** の順に選択します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [windows](../quickstart-list.md)]
