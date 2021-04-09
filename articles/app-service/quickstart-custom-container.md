---
title: クイック スタート:App Service でカスタム コンテナーを実行する
description: 初めてのカスタム アプリをデプロイして、Azure App Service でのコンテナーの使用を開始します。
author: msangapu-msft
ms.author: msangapu
ms.date: 10/21/2019
ms.topic: quickstart
ms.custom: devx-track-csharp
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 360da015f012822593dbb6390cb7df0017ba85b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745079"
---
# <a name="run-a-custom-container-in-azure"></a>Azure でカスタム コンテナーを実行する

::: zone pivot="container-windows"
[Azure App Service](overview.md) は、IIS 上で稼働する ASP.NET や IIS 上で稼働する Node.js など、Windows 上の定義済みのアプリケーション スタックを提供します。 事前構成済みの Windows コンテナー環境では、オペレーティング システムは、管理アクセス、ソフトウェアのインストール、グローバル アセンブリ キャッシュへの変更などができないようにロックされます。 詳細については、「[Azure App Service におけるオペレーティング システムの機能](operating-system-functionality.md)」を参照してください。 アプリケーションから利用すべき領域が事前構成済みの環境を超える場合は、カスタムの Windows コンテナーをデプロイすることで対応できます。

このクイックスタートでは、Visual Studio から [Docker Hub](https://hub.docker.com/) に、Windows イメージで ASP.NET アプリをデプロイする方法について説明します。 アプリは、Azure App Service のカスタム コンテナーで実行します。

> [!NOTE]
> Windows コンテナーは Azure Files に限定されており、現時点では Azure Blob はサポートされていません。


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- <a href="https://hub.docker.com/" target="_blank">Docker Hub アカウントにサインアップする</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Docker for Windows をインストールする</a>。
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Windows コンテナーを実行するように Docker を切り替える</a>。
- **ASP.NET と Web 開発** ワークロードと **Azure の開発** ワークロードを含めて <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 をインストールする</a>。 Visual Studio 2019 を既にインストールしている場合:

    - **[ヘルプ]**  >  **[更新プログラムの確認]** の順に選択して、Visual Studio に最新の更新プログラムをインストールします。
    - **[ツール]**  >  **[ツールと機能を取得]** の順に選択し、Visual Studio にワークロードを追加します。

## <a name="create-an-aspnet-web-app"></a>ASP.NET Web アプリを作成する

次の手順に従って ASP.NET Web アプリを作成します。

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択します。

1. **[新しいプロジェクトの作成]** で、C# の **[ASP.NET Web アプリケーション (.NET Framework)]** を選択し、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、アプリケーションに _myfirstazurewebapp_ という名前を付け、 **[作成]** を選択します。

   ![Web アプリ プロジェクトを構成する](./media/quickstart-custom-container/configure-web-app-project-container.png)

1. 任意の種類の ASP.NET Web アプリを Azure にデプロイできます。 このクイックスタートでは、 **[MVC]** テンプレートを選択します。

1. **[Docker サポート]** を選択し、認証が **[認証なし]** に設定されていることを確認します。 **［作成］** を選択します

   ![ASP.NET Web アプリケーションを作成する](./media/quickstart-custom-container/select-mvc-template-for-container.png)

1. _Dockerfile_ ファイルが自動的に開かない場合は、**ソリューション エクスプローラー** から開きます。

1. [サポートされている親イメージ](configure-custom-container.md#supported-parent-images)が必要です。 `FROM` 行を次のコードに置き換えることで親イメージを変更し、ファイルを保存します。

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. Visual Studio のメニューから **[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、Web アプリをローカルで実行します。

   ![アプリをローカルで実行する](./media/quickstart-custom-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Docker Hub に発行する

1. **ソリューション エクスプローラー** で **myfirstazurewebapp** プロジェクトを右クリックし、 **[発行]** を選択します。

1. **App Service** を選択し、 **[発行]** を選択します。

1. **[発行先を選択]** で **[コンテナー レジストリ]** と **[Docker Hub]** を選択し、 **[発行]** をクリックします。

   ![プロジェクトの概要ページから発行する](./media/quickstart-custom-container/publish-to-docker-vs2019.png)

1. Docker Hub アカウントの資格情報を指定し、 **[保存]** を選択します。

   デプロイが完了するまで待ちます。 **[発行]** ページに、後で使用するリポジトリ名が表示されるようになります。

   ![リポジトリ名が強調表示されているスクリーンショット。](./media/quickstart-custom-container/published-docker-repository-vs2019.png)

1. 後で使用するのでこのリポジトリ名をコピーします。

## <a name="create-a-windows-container-app"></a>Windows コンテナー アプリの作成

1. [Azure portal]( https://portal.azure.com) にサインインします。

1. Azure portal の左上隅にある **[リソースの作成]** を選択します。

1. Azure Marketplace リソース一覧の上にある検索ボックスで、 **[Web App for Containers]** を検索して、 **[作成]** を選択します。

1. **[Web App Create]\(Web アプリの作成\)** で、サブスクリプションと **リソース グループ** を選択します。 必要な場合は、新しいリソース グループを作成できます。

1. アプリ名 (例: *win-container-demo*) を入力し、 **[オペレーティング システム]** に **[Windows]** を選択します。 **Docker** を選択して続行します。

   ![Web App for Containers を作成する](media/quickstart-custom-container/create-web-app-continer.png)

1. **[イメージのソース]** に **[Docker Hub]** を選択し、 **[イメージとタグ]** に、「[Docker Hub に発行する](#publish-to-docker-hub)」でコピーしたリポジトリ名を入力します。

   ![Web App for Containers を構成する](media/quickstart-custom-container/configure-web-app-continer.png)

    [Azure Container Registry](../container-registry/index.yml) や他のプライベート リポジトリなど、どこか他の場所に目的の Web アプリケーションのカスタム イメージがある場合は、ここで構成することができます。

1. **[確認と作成]** 、 **[作成]** の順に選択して、必要なリソースが Azure によって作成されるまで待ちます。

## <a name="browse-to-the-container-app"></a>コンテナー アプリにブラウザーでアクセスする

Azure の処理が完了すると、通知ボックスが表示されます。

![デプロイメント成功](media/quickstart-custom-container/portal-create-finished.png)

1. **[リソースに移動]** をクリックします。

1. このリソースの概要で、 **[URL]** の横にあるリンクに移動します。

新しいブラウザー ページが開いて次のページが表示されます。

![Windows コンテナー アプリ (起動中)](media/quickstart-custom-container/app-starting.png)

数分待ってから再試行すると、既定の ASP.NET ホーム ページが表示されます。

![Windows コンテナー アプリ (実行中)](media/quickstart-custom-container/app-running-vs.png)

**お疲れさまでした。** 初めてのカスタム Windows コンテナーを Azure App Service で実行することができました。

## <a name="see-container-start-up-logs"></a>コンテナーの起動ログの表示

Windows コンテナーが読み込まれるまでにしばらく時間がかかる場合があります。 進行状況を確認するには、 *\<app_name>* をアプリの名前に置き換えて次の URL に移動します。
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

次のようなログがストリーム配信されます。

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>ローカルで更新して再デプロイする

1. Visual Studio の **ソリューション エクスプローラー** から、 **[表示]**  >  **[ホーム]**  >  **[Index.cshtml]** の順に開きます。

1. 上部の `<div class="jumbotron">` HTML タグを検索し、要素全体を次のコードに置き換えます。

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Azure に再デプロイするには、**ソリューション エクスプローラー** で **myfirstazurewebapp** プロジェクトを右クリックし、 **[発行]** を選択します。

1. 発行ページで **[発行]** を選択し、発行が完了するまで待ちます。

1. App Service に Docker Hub から新しいイメージをプルするよう指示するには、アプリを再起動します。 ポータルでアプリ ページに戻り、 **[再起動]**  >  **[はい]** をクリックします。

   ![Azure で Web アプリを再起動する](./media/quickstart-custom-container/portal-restart-app.png)

再び[コンテナー アプリにブラウザーでアクセス](#browse-to-the-container-app)します。 Web ページを更新すると、アプリは最初に "起動" ページに戻った後、数分すると更新された Web ページが再び表示されます。

![Azure での更新された Web アプリ](./media/quickstart-custom-container/azure-web-app-updated.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure で Windows コンテナーに移行する](tutorial-custom-container.md)

または、他のリソースを参照してください。

> [!div class="nextstepaction"]
> [カスタム コンテナーの構成](configure-custom-container.md)

::: zone-end  

::: zone pivot="container-linux"
App Service on Linux は、事前定義済みのアプリケーション スタックを Linux 上で提供し、.NET、PHP、Node.js などの言語をサポートします。 まだ Azure で定義されていないアプリケーション スタックで Web アプリを実行する場合にも、カスタム Docker イメージを使用できます。 このクイックスタートでは、[Azure Container Registry](../container-registry/index.yml) (ACR) から App Service にイメージをデプロイする方法について説明します。

## <a name="prerequisites"></a>前提条件

* [Azure アカウント](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* [VS Code 用 Azure App Service 拡張情報](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。 この拡張機能を使用して、Azure PaaS (サービスとしてのプラットフォーム) 上に Linux Web Apps を作成、管理、デプロイすることができます。
* [VS Code 用 Docker 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)。 この拡張機能を使用して、ローカルの Docker イメージと Docker コマンドの管理を簡素化し、作成したアプリ イメージを Azure にデプロイすることができます。

## <a name="create-an-image"></a>イメージを作成する

このクイックスタートを完了するには、[Azure Container Registry](../container-registry/index.yml) に格納されている適切な Web アプリ イメージが必要です。 「[クイックスタート: Azure portal を使用したプライベート コンテナー レジストリの作成](../container-registry/container-registry-get-started-portal.md)」の手順に従ってください。ただし、使用するイメージは、`hello-world` ではなく `mcr.microsoft.com/azuredocs/go` となります。 [Azure Samples リポジトリにサンプル Dockerfile があります](https://github.com/Azure-Samples/go-docs-hello-world)。ご参考ください。

> [!IMPORTANT]
> コンテナー レジストリを作成するときは必ず、**[管理者ユーザー]** オプションを **[有効]** に設定してください。 Azure portal のレジストリ ページの **[アクセス キー]** セクションから設定することもできます。 App Service にアクセスするためには、この設定が必要となります。

## <a name="sign-in"></a>サインイン

次に、VS Code を起動し、App Service 拡張機能を使用して Azure アカウントにログインします。 そのためには、アクティビティ バーで Azure ロゴを選択し、**[APP SERVICE]** エクスプローラーに移動して **[Azure にサインイン]** を選択し、画面の指示に従います。

![Azure にサインイン](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>前提条件を確認する

それでは、すべての前提条件がインストールされ、適切に構成されていることを確認しましょう。

VS Code では、ステータス バーに自分の Azure メール アドレスが、**APP SERVICE** エクスプローラーに自分のサブスクリプションが表示されます。

次に、Docker がインストールされ、実行されていることを確認します。 次のコマンドは、Docker が実行されている場合に、そのバージョンを表示するものです。

```bash
docker --version
```

最後に、Azure Container Registry が接続されていることを確認します。 そのためには、アクティビティ バーで Docker ロゴを選択し、**[レジストリ]** に移動します。

![スクリーンショットは、[Azure] が展開された [レジストリ] の値として、ファイル名拡張子 .io を持つファイルを示しています。](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Azure App Service にイメージをデプロイする

すべての構成が済んだら、Docker 拡張機能エクスプローラーから直接 [Azure App Service](https://azure.microsoft.com/services/app-service/) にイメージをデプロイできます。

**DOCKER** エクスプローラーで **[レジストリ]** ノードからイメージを見つけて展開し、そのタグを表示します。 タグを右クリックし、**[Deploy Image to Azure App Service]\(Azure App Service にイメージをデプロイ\)** を選択します。

そこからプロンプトに従って、サブスクリプション、グローバルに一意なアプリ名、リソース グループ、App Service プランを選択します。 価格レベルには **[B1 Basic]** を選択し、リージョンを選択してください。

デプロイ後は、`http://<app name>.azurewebsites.net` でアプリにアクセスできます。

**リソース グループ** は、Azure で利用するすべてのアプリケーションのリソースをまとめた名前付きのコレクションです。 たとえば、Web サイトやデータベース、Azure 関数への参照をリソース グループに含めることができます。

Web サイトをホストするために使用される物理リソースは、**App Service プラン** によって定義されます。 このクイックスタートでは、**Linux** インフラストラクチャ上の **Basic** ホスティング プランを使用します。つまりサイトは Linux マシン上で、他の Web サイトと一緒にホストされます。 **Basic** プランから開始すれば、マシン上で自分のサイトだけが実行されるように、Azure portal を使用してスケールアップすることができます。

## <a name="browse-the-website"></a>Web サイトを閲覧する

デプロイ中は、**[出力]** パネルが開いてデプロイ操作の状態が表示されます。 その操作が完了したら、**APP SERVICE** エクスプローラーで作成したアプリを見つけて右クリックし、**[Web サイトの参照]** を選択して、ブラウザーでサイトを開きます。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>次のステップ

おめでとうございます。このクイックスタートを正常に完了できました。

次は、他の Azure 拡張機能もチェックしてみましょう。

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Azure CLI Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

または、[Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 拡張機能パックをインストールして、これらすべてを入手しましょう。

他のリソースを確認してください。

> [!div class="nextstepaction"]
> [カスタム コンテナーの構成](configure-custom-container.md)

::: zone-end
