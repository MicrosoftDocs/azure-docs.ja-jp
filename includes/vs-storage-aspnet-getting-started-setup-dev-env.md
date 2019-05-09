---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a7c696870e22e1692ca5ed778e47f8e4cc00615a
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610137"
---
## <a name="set-up-the-development-environment"></a>開発環境を設定する

このセクションでは、開発環境の設定について説明します。 これには、ASP.NET MVC アプリの作成、接続済みサービスの接続の追加、コントローラーの追加、必要な名前空間ディレクティブの指定が含まれます。

### <a name="create-an-aspnet-mvc-app-project"></a>ASP.NET MVC アプリ プロジェクトを作成する

1. Visual Studio を開きます。

1. メイン メニューから、**[ファイル]** > **[新規作成]** > **[プロジェクト]** の順に選択します。

1. **[新しいプロジェクト]** ダイアログ ボックスで、**[Web]** > **[ASP.NET Web アプリケーション (.NET Framework)]** の順に選択します。 **[名前]** フィールドで、**StorageAspNet** を指定します。 **[OK]** を選択します。

    ![[新しいプロジェクト] ダイアログ ボックスのスクリーンショット](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. **[新しい ASP.NET Web アプリケーション]** ダイアログ ボックスで、**[MVC]** を選択し、**[OK]** を選択します。

    ![[新しい ASP.NET Web アプリケーション] ダイアログ ボックスのスクリーンショット](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>接続済みサービスを使用して Azure ストレージ アカウントに接続する

1. **Solution Explorer** で、プロジェクト名を右クリックします。

1. コンテキスト メニューから、**[追加]** > **[接続済みサービス]** の順に選択します。

1. **[接続済みサービス]** ダイアログ ボックスで、**[Azure Storage を使用したクラウド ストレージ]** を選択します。

    ![[接続済みサービス] ダイアログ ボックスのスクリーンショット](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. **[Azure Storage]** ダイアログ ボックスで、このチュートリアルで使用する Azure ストレージ アカウントを選択します。 新しい Azure ストレージ アカウントを作成するには、**[新しいストレージ アカウントの作成]** を選択し、フォームに入力します。 既存のストレージ アカウントを選択するか新しいストレージ アカウントを作成したら、**[追加]** を選択します。 Visual Studio によって Azure Storage 用の NuGet パッケージと **Web.config** へのストレージ接続文字列がインストールされます。

1. **ソリューション エクスプローラー**で **[依存関係]** を右クリックし、**[NuGet パッケージの管理]** を選択して、NuGet パッケージ参照を Microsoft.Azure.ConfigurationManager の最新バージョンに追加します。

> [!TIP]
> [Azure ポータル](https://portal.azure.com)でのストレージ アカウントの作成方法については、「[ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)」を参照してください。
>
> [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md)、[Azure CLI](../articles/storage/common/storage-azure-cli.md)、または [Azure Cloud Shell](../articles/cloud-shell/overview.md) を使用してストレージ アカウントを作成することもできます。
