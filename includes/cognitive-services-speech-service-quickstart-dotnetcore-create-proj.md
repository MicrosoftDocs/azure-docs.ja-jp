---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/12/2018
ms.author: erhopf
ms.openlocfilehash: 6e49db90aa9e9f933a190425afbafd15e0057fca
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729494"
---
1. Visual Studio 2017 を起動します。

1. **.NET クロスプラットフォーム開発**ワークロードが使用できることを確認してください。 Visual Studio のメニュー バーから **[ツール]** > **[Get Tools and Features]\(ツールと機能の入手\)** を選択し、Visual Studio インストーラーを開きます。 このワークロードが既に有効になっている場合は、ダイアログ ボックスを閉じます。

   ![[ワークロード] タブを強調表示した Visual Studio インストーラーのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   それ以外の場合、**[.NET Core cross-platform development] (.NET Core クロス プラットフォーム開発)** の横にあるボックスを選択し、ダイアログ ボックスの右下隅にある **[変更]** を選択します。 新しい機能のインストールにはしばらく時間がかかります。

1. 新しい Visual C# .NET Core コンソール アプリを作成します。 **[新しいプロジェクト]** ダイアログ ボックスで、左側のウィンドウから **[インストール済み]** > **[Visual C#]** > **[.NET Core]** を展開します。 **[コンソール アプリ (.NET Core)]** を選択します。 プロジェクト名として「*helloworld*」と入力します。

   ![[新しいプロジェクト] ダイアログ ボックスのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Visual C# コンソール アプリ (.NET Core) の作成")

1. [Speech SDK NuGet パッケージ](https://aka.ms/csspeech/nuget)をインストールして参照します。 ソリューション エクスプローラーで、ソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]** を選択します。

   ![[ソリューションの NuGet パッケージの管理] が強調表示されたソリューション エクスプローラーのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "[ソリューションの NuGet パッケージの管理]")

1. 右上隅の **[パッケージ ソース]** フィールドで、**[nuget.org]** を選択します。`Microsoft.CognitiveServices.Speech` パッケージを検索して、**helloworld** プロジェクトにインストールします。

   ![[ソリューションの NuGet パッケージの管理] ダイアログ ボックスのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "NuGet パッケージのインストール")

1. 表示されるライセンスに同意し、NuGet パッケージのインストールを開始します。

   ![[ライセンスの同意] ダイアログ ボックスのスクリーンショット](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "ライセンスへの同意")

パッケージがインストールされると、パッケージ マネージャー コンソールに確認メッセージが表示されます。
