---
title: チュートリアル:カスタム イメージを作成して Azure App Service で実行する
description: Linux または Windows のカスタム イメージを作成して Azure Container Registry にプッシュし、そのイメージを Azure App Service にデプロイするためのステップ バイ ステップ ガイドです。 カスタム ソフトウェアをカスタム コンテナー内の App Service に移行する方法について説明します。
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: Azure App Service, Web アプリ, Linux, Windows, Docker, コンテナー
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 5d3a714230f0279bd68b39cd02624866b9b3bacf
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180515"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>カスタム コンテナーを使用してカスタム ソフトウェアを Azure App Service に移行する

::: zone pivot="container-windows"  

[Azure App Service](overview.md) は、IIS 上で稼働する ASP.NET や IIS 上で稼働する Node.js など、Windows 上の定義済みのアプリケーション スタックを提供します。 Windows 環境があらかじめ構成されていることで、オペレーティング システムは、管理アクセスやソフトウェアのインストール、グローバル アセンブリ キャッシュへの変更などができないようにロックされます (「[Azure App Service におけるオペレーティング システムの機能](operating-system-functionality.md)」を参照)。 ただし、App Service でカスタム Windows コンテナーを使用するとアプリで必要な OS の変更を行うことができるので、カスタム OS とソフトウェアの構成が必要なオンプレミスのアプリを簡単に移行できます。 このチュートリアルでは、Windows フォント ライブラリにインストールされているカスタム フォントを使用する ASP.NET アプリを App Service に移行する方法を示します。 Visual Studio からカスタム構成の Windows イメージを [Azure Container Registry](../container-registry/index.yml) に展開した後、App Service でそれを実行します。

![Windows コンテナーで実行中の Web アプリを示します。](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- <a href="https://hub.docker.com/" target="_blank">Docker Hub アカウントにサインアップする</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Docker for Windows をインストールする</a>。
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Windows コンテナーを実行するように Docker を切り替える</a>。
- **ASP.NET と Web 開発** ワークロードと **Azure の開発** ワークロードを含めて <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 をインストールする</a>。 Visual Studio 2019 を既にインストールしている場合:
    - **[ヘルプ]**  >  **[更新プログラムの確認]** の順にクリックし、Visual Studio に最新の更新プログラムをインストールします。
    - **[ツール]**  >  **[ツールと機能を取得]** の順にクリックし、Visual Studio にワークロードを追加します。

## <a name="set-up-the-app-locally"></a>アプリをローカルでセットアップする

### <a name="download-the-sample"></a>サンプルのダウンロード

この手順では、ローカルな .NET プロジェクトを設定します。

- [サンプル プロジェクトをダウンロードします](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip)。
- *custom-font-win-container.zip* ファイルを抽出 (解凍) します。

サンプル プロジェクトには、Windows フォント ライブラリにインストールされているカスタム フォントを使用する簡単な ASP.NET アプリケーションが含まれています。 フォントをインストールする必要はありませんが、基になる OS と統合されているアプリの一例です。 このようなアプリを App Service に移行するには、統合を削除するようにコードを再設計するか、またはカスタム Windows コンテナーにそのまま移行します。

### <a name="install-the-font"></a>フォントをインストールする

エクスプローラーで _custom-font-win-container-master/CustomFontSample_ に移動し、_FrederickatheGreat-Regular.ttf_ を右クリックして、 **[インストール]** を選択します。

このフォントは、[Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great) で一般公開されています。

### <a name="run-the-app"></a>アプリを実行する

*custom-font-win-container/CustomFontSample.sln* ファイルを Visual Studio で開きます。 

デバッグせずにアプリを実行するには、`Ctrl+F5` キーを押します。 アプリが既定のブラウザーに表示されます。 

:::image type="content" source="media/tutorial-custom-container/local-app-in-browser.png" alt-text="既定のブラウザーに表示されているアプリを示すスクリーンショット。":::

アプリはインストールされているフォントを使用するため、App Service サンド ボックスでは実行できません。 ただし、Windows コンテナーにフォントをインストールできるので、代わりに Windows コンテナーを使用して展開することができます。

### <a name="configure-windows-container"></a>Windows コンテナーを構成する

ソリューション エクスプローラーで **CustomFontSample** を右クリックし、 **[追加]**  >  **[Container Orchestration Support]\(コンテナー オーケストレーションのサポート\)** を選択します。

:::image type="content" source="media/tutorial-custom-container/enable-container-orchestration.png" alt-text="CustomFontSample プロジェクトを示す [ソリューション エクスプローラー] ウィンドウのスクリーンショット。[追加] および [コンテナー オーケストレーター サポート] メニュー項目が選択されている。":::

**[Docker Compose]**  >  **[OK]** を選択します。

プロジェクトが Windows コンテナーで実行するように設定されます。 _Dockerfile_ が **CustomFontSample** プロジェクトに追加され、**docker-compose** プロジェクトがソリューションに追加されます。 

ソリューション エクスプローラーで **Dockerfile** を開きます。

[サポートされている親イメージ](configure-custom-container.md#supported-parent-images)を使用する必要があります。 `FROM` 行を次のコードに置き換えることで、親イメージを変更します。

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

ファイルの末尾に次の行を追加し、ファイルを保存します。

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

**CustomFontSample** プロジェクトには _InstallFont.ps1_ があります。 これはフォントをインストールする簡単なスクリプトです。 さらに複雑なバージョンのスクリプトが[スクリプト センター](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133)にあります。

> [!NOTE]
> Windows コンテナーをローカルでテストするには、ローカル コンピューターで Docker が起動していることを確認します。
>

## <a name="publish-to-azure-container-registry"></a>Azure Container Registry に発行する

[Azure Container Registry](../container-registry/index.yml) では、コンテナーの展開用にイメージを格納することができます。 Azure Container Registry でホストされているイメージを使用するように App Service を構成できます。

### <a name="open-publish-wizard"></a>発行ウィザードを開く

ソリューション エクスプローラーで **CustomFontSample** プロジェクトを右クリックし、 **[発行]** を選択します。

:::image type="content" source="media/tutorial-custom-container/open-publish-wizard.png" alt-text="CustomFontSample プロジェクトを示し、[発行] が選択されているソリューション エクスプローラーのスクリーンショット。":::

### <a name="create-registry-and-publish"></a>レジストリを作成して発行する

発行ウィザードで **[コンテナー レジストリ]**  >  **[新しい Azure コンテナー レジストリを作成する]**  >  **[発行]** を選択します。

:::image type="content" source="media/tutorial-custom-container/create-registry.png" alt-text="[コンテナー レジストリ]、[新しい Azure コンテナー レジストリを作成する]、[発行] ボタンの順に選択されているところを示す発行ウィザードのスクリーンショット。":::

### <a name="sign-in-with-azure-account"></a>Azure アカウントでサインインする

**[新しい Azure コンテナー レジストリを作成する]** ダイアログ ボックスで、 **[アカウントの追加]** をクリックし、Azure サブスクリプションにサインインします。 既にサインインしている場合は、目的のサブスクリプションを含んだアカウントをドロップダウンから選択します。

![Azure へのサインイン](./media/tutorial-custom-container/add-an-account.png)

### <a name="configure-the-registry"></a>レジストリを構成する

次の表の推奨値に基づいて新しいコンテナー レジストリを構成します。 完了したら、 **[作成]** をクリックします。

| 設定  | 推奨値 | 詳細情報 |
| ----------------- | ------------ | ----|
|**DNS プレフィックス**| 生成されたレジストリ名をそのまま使用するか、または別の一意名に変更します。 |  |
|**リソース グループ**| **[新規]** をクリックし、「**myResourceGroup**」と入力して、 **[OK]** をクリックします。 |  |
|**SKU**| Basic | [価格レベル](https://azure.microsoft.com/pricing/details/container-registry/)|
|**レジストリの場所**| 西ヨーロッパ | |

![Azure コンテナー レジストリを構成する](./media/tutorial-custom-container/configure-registry.png)

ターミナル ウィンドウが開き、イメージの展開の進行状況が表示されます。 デプロイが完了するまで待ちます。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-web-app"></a>Web アプリを作成する

左側のメニューから **[リソースの作成]**  >  **[Web]**  >  **[Web App for Containers]** を選択します。

### <a name="configure-app-basics"></a>アプリの基本情報を構成する

**基本** タブで、次の表に従って設定を構成し、**Next: Docker\(次へ: Docker** をクリックします。

| 設定  | 推奨値 | 詳細情報 |
| ----------------- | ------------ | ----|
|**サブスクリプション**| 正しいサブスクリプションが選択されていることを確認します。 |  |
|**リソース グループ**| **[新規作成]** を選択し、「**myResourceGroup**」と入力して、 **[OK]** をクリックします。 |  |
|**名前**| 一意の名前を入力します。 | Web アプリの URL は `http://<app-name>.azurewebsites.net` です。`<app-name>` には自分のアプリの名前を指定します。 |
|**発行**| Docker コンテナー | |
|**オペレーティング システム**| Windows | |
|**リージョン**| 西ヨーロッパ | |
|**Windows プラン**| **[新規作成]** を選択し、「**myAppServicePlan**」と入力して、 **[OK]** をクリックします。 | |

**[基本]** タブは、次のように表示されます。

![Web アプリの構成に使用する [基本] タブを示します。](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>Windows コンテナーを構成する

**[Docker]** タブで、次の表に示したようにカスタム Windows コンテナーを構成し、 **[確認および作成]** を選択します。

| 設定  | 推奨値 |
| ----------------- | ------------ |
|**イメージのソース**| Azure Container Register |
|**レジストリ**| [以前に作成したレジストリ](#publish-to-azure-container-registry)を選択します。 |
|**Image**| customfontsample |
|**Tag**| latest |

### <a name="complete-app-creation"></a>アプリの作成を完了する

**[作成]** をクリックして、必要なリソースが Azure によって作成されるのを待ちます。

## <a name="browse-to-the-web-app"></a>Web アプリを参照する

Azure の処理が完了すると、通知ボックスが表示されます。

![Azure の処理が完了したことを示します。](media/tutorial-custom-container/portal-create-finished.png)

1. **[リソースに移動]** をクリックします。

2. アプリ ページで、 **[URL]** の下にあるリンクをクリックします。

新しいブラウザー ページが開いて次のページが表示されます。

![Web アプリの新しいブラウザー ページを示します。](media/tutorial-custom-container/app-starting.png)

期待した美しいフォントでホーム ページが表示されるまで、数分待ってからもう一度試します。

![構成したフォントでホームページを表示します。](media/tutorial-custom-container/app-running.png)

**お疲れさまでした。** Windows コンテナーで Azure App Service に ASP.NET アプリケーションを移行しました。

## <a name="see-container-start-up-logs"></a>コンテナーの起動ログの表示

Windows コンテナーが読み込まれるまでにしばらく時間がかかる場合があります。 進行状況を確認するには、 *\<app-name>* をアプリの名前に置き換えて次の URL に移動します。
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

次のようなログがストリーム配信されます。

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

::: zone-end

::: zone pivot="container-linux"

Azure App Service では、組み込みイメージとカスタム イメージのどちらも、Docker コンテナー テクノロジを使用してホストされます。 組み込みイメージを一覧表示するには、Azure CLI コマンド "[az webapp list-runtimes --linux](/cli/azure/webapp#az-webapp-list-runtimes)" を実行します。 それらのイメージでニーズが満たせない場合は、カスタム イメージを作成してデプロイすることができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ニーズを満たす組み込みイメージがない場合にカスタム イメージを作成する
> * Azure 上のプライベート コンテナー レジストリにカスタム イメージをプッシュする
> * App Service 内でカスタム イメージを実行する
> * 環境変数を構成する
> * イメージを更新して再デプロイする
> * 診断ログにアクセスする
> * SSH を使用してコンテナーに接続する

このチュートリアルが完了すると、わずかですがコンテナー レジストリの料金がお使いの Azure アカウントに発生します。また、そのコンテナーをホストする期間が 1 か月を超えると、さらにコストが発生します。

## <a name="set-up-your-initial-environment"></a>初期環境を設定する

- アクティブなサブスクリプションが含まれる Azure アカウントを用意します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Docker](https://docs.docker.com/get-started/#setup) をインストールします。これを Docker イメージの作成に使用します。 Docker をインストールするには、コンピューターの再起動が必要になる場合があります。
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
- このチュートリアルには、Azure CLI のバージョン 2.0.80 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

Docker をインストールした後、または Azure Cloud Shell を実行した後、ターミナル ウィンドウを開いて、Docker がインストールされていることを確認します。

```bash
docker --version
```

## <a name="clone-or-download-the-sample-app"></a>サンプル アプリをクローンまたはダウンロードする

このチュートリアルのサンプルは、Git Clone またはダウンロードによって入手できます。

### <a name="clone-with-git"></a>Git でのクローン

サンプル リポジトリをクローンします。

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Linux コンテナー内で使用される適切な行末がファイルに使用されるよう、必ず `--config core.autocrlf=input` 引数を追加してください。

次に、そのフォルダーに移動します。

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>GitHub からのダウンロード

Git Clone を使用する代わりに、[https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux) にアクセスして **[Clone]\(クローン\)** を選択し、 **[Download ZIP]\(ZIP のダウンロード\)** を選択してもかまいません。 

その ZIP ファイルを、*docker-django-webapp-linux* という名前のフォルダーに展開します。 

次に、その *docker-django-webapp-linux* フォルダーからターミナル ウィンドウを開きます。

## <a name="optional-examine-the-docker-file"></a>(省略可) Docker ファイルを調べる

サンプル内の _Dockerfile_ という名前のファイルには、Docker イメージが記述されているほか、構成に関する命令が含まれています。

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* 1 つ目のコマンド グループは、アプリの要件を環境にインストールするものです。
* 2 つ目のコマンド グループで、コンテナーとホストとの間で安全に通信を行うための [SSH](https://www.ssh.com/ssh/protocol/) サーバーが作成されます。
* 最後の行 (`ENTRYPOINT ["init.sh"]`) は、`init.sh` を呼び出して SSH サービスと Python サーバーを起動しています。

## <a name="build-and-test-the-image-locally"></a>イメージをローカルで作成してテストする

> [!NOTE]
> Docker Hub には、[無料ユーザーごとに、IP あたりの匿名プル数と、認証済みプル数にクォータがあります (「**データ転送**」を参照してください)](https://www.docker.com/pricing)。 まだログインしておらず、Docker Hub からのプルが制限されていることに気付いた場合は `docker login` を試してください。
> 

1. 次のコマンドを実行して、イメージをビルドします。

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Docker コンテナーをローカルで実行して、ビルドの動作をテストします。

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    この [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) コマンドに `-p` 引数でポートを指定し、続けてイメージの名前を指定します。 
    
    > [!TIP]
    > Windows を実行していて "*standard_init_linux.go:211: exec user process caused "no such file or directory" (standard_init_linux.go:211: exec ユーザー プロセスが原因で "ファイルまたはディレクトリの欠落" が発生しました)* " というエラーが表示された場合、LF であるはずの *init.sh* ファイルの行末が CR-LF になっています。 このエラーは、Git を使用してサンプル リポジトリをクローンした際、`--config core.autocrlf=input` パラメーターを省略した場合に発生します。 その場合は、`--config`` 引数を使用して、再度リポジトリをクローンしてください。 *init.sh* を編集し、行末に CRLF を使用して保存した場合にも、このエラーが表示される可能性があります。 この場合は、行末を LF のみにして再度ファイルを保存してください。

1. ブラウザーで `http://localhost:8000` にアクセスして、Web アプリとコンテナーが正しく機能していることを確認します。

    ![ローカルでの Web アプリのテスト](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="create-a-resource-group"></a>リソース グループを作成する

このセクション以降では、イメージをプッシュして Azure App Service にコンテナーをデプロイするためのリソースを Azure にプロビジョニングします。 最初に、これらすべてのリソースを集約するリソース グループを作成します。

[az group create](/cli/azure/group#az-group-create) コマンドを実行してリソース グループを作成します。

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

`--location` の値を変更することで最寄りのリージョンを指定できます。

## <a name="push-the-image-to-azure-container-registry"></a>Azure Container Registry にイメージをプッシュする

このセクションでは、Azure Container Registry にイメージをプッシュします。App Service は、そこからイメージをデプロイすることができます。

1. [`az acr create`](/cli/azure/acr#az-acr-create) コマンドを実行して Azure Container Registry を作成します。

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    `<registry-name>` は、適切なレジストリ名に置き換えてください。 この名前は Azure 全体で一意である必要があり、使用できるのはアルファベットと数字のみです。

1. [`az acr show`](/cli/azure/acr#az-acr-show) コマンドを実行して、レジストリの資格情報を取得します。

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    このコマンドの JSON 出力からは、レジストリのユーザー名と共に 2 つのパスワードが得られます。
    
1. `docker login` コマンドを使用して、コンテナー レジストリにサインインします。

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    `<registry-name>` と `<registry-username>` は、前の手順で得られた値に置き換えてください。 入力を求められたら、前の手順のいずれかのパスワードを入力します。

    このセクションでは、以降すべての手順で同じレジストリ名を使用します。

1. ログインに成功したら、レジストリのローカル Docker イメージにタグを付けます。

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. `docker push` コマンドを使用してレジストリにイメージをプッシュします。

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    イメージの初回アップロードには、基本イメージが含まれるため、数分かかる場合があります。 通常、それ以降のアップロードはもっと短時間で済みます。

    待っている間、次のセクションの手順を実行して、レジストリからデプロイするための構成を App Service に対して行うことができます。

1. `az acr repository list` コマンドを使用してプッシュが成功したことを確認します。

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    自分のイメージの名前が出力に表示されます。


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>レジストリからイメージをデプロイするように App Service を構成する

Azure App Service にコンテナーをデプロイするには、まず App Service に Web アプリを作成した後、その Web アプリをコンテナー レジストリに接続します。 Web アプリが起動すると、App Service によって自動的にレジストリからイメージがプルされます。

1. [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) コマンドを使用して App Service プランを作成します。

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    App Service プランは、Web アプリのホストとなる仮想マシンに相当します。 前のコマンドでは、最初の月は無料で利用できる低コストの [B1 価格レベル](https://azure.microsoft.com/pricing/details/app-service/linux/)が既定で使用されます。 レベルは、`--sku` パラメーターで制御できます。

1. [`az webpp create`](/cli/azure/webapp#az-webapp-create) コマンドを使用して Web アプリを作成します。

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    `<app-name>` は Web アプリの名前に置き換えてください。この名前は Azure 全体で一意である必要があります。 また、`<registry-name>` は、前のセクションで指定したレジストリの名前に置き換えます。

1. [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) を使用して、アプリのコードで想定されている値に `WEBSITES_PORT` 環境変数を設定します。 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    `<app-name>` は、前のステップで使用した名前に置き換えてください。
    
    この環境変数の詳細については、[サンプルの GitHub リポジトリにある Readme](https://github.com/Azure-Samples/docker-django-webapp-linux) を参照してください。

1. [`az webapp identity assign`](/cli/azure/webapp/identity#az-webapp-identity-assign) コマンドを使用して、Web アプリの[マネージド ID](./overview-managed-identity.md) を有効にします。

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    `<app-name>` は、前のステップで使用した名前に置き換えてください。 コマンドからは、`--query` 引数と `--output` 引数のフィルターを通じて、割り当てられた ID のサービス プリンシパルが出力されます。このサービス プリンシパルは、この後すぐに使用します。

    マネージド ID を利用すれば、特定の資格情報なしに、他の Azure リソースへのアクセス許可を Web アプリに与えることができます。

1. [`az account show`](/cli/azure/account#az-account-show) コマンドを使用してサブスクリプション ID を取得します。次の手順でサブスクリプション ID が必要になります。

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. コンテナー レジストリへのアクセス許可を Web アプリに与えます。

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    次の値を置き換えます。
    - `<principal-id>` を、`az webapp identity assign` コマンドで得たサービス プリンシパル ID に置き換えます。
    - `<registry-name>` を、コンテナー レジストリの名前に置き換えます。
    - `<subscription-id>` を、`az account show` コマンドから取得したサブスクリプション ID に置き換えます。

これらのアクセス許可については、「[Azure ロールベースのアクセス制御とは](../role-based-access-control/overview.md)」を参照してください。 

## <a name="deploy-the-image-and-test-the-app"></a>イメージをデプロイしてアプリをテストする

これらの手順は、イメージがコンテナー レジストリにプッシュされ、App Service が完全にプロビジョニングされてから実行してください。

1. [`az webapp config container set`](/cli/azure/webapp/config/container#az-webapp-config-container-set) コマンドを使用して、コンテナー レジストリと Web アプリ用にデプロイするイメージを指定します。

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    `<app_name>` を自分の Web アプリの名前に、また 2 か所ある `<registry-name>` を実際のレジストリの名前に置き換えてください。 

    - この例のように Docker Hub 以外のレジストリを使用する場合、`--docker-registry-server-url` は、`https://` の後にレジストリの完全修飾ドメイン名が続く形式にする必要があります。
    - "No credential was provided to access Azure Container Registry. Trying to look up... (Azure Container Registry にアクセスするための資格情報が指定されませんでした。...を検索しています)" というメッセージは、Azure がコンテナー レジストリに対する認証で、ユーザー名とパスワードを求めるのではなく、アプリのマネージド ID を使用していることを表します。 Trying to look up..." tells you that Azure is using the app's managed identity to authenticate with the container registry rather than asking for a username and password.
    - "AttributeError: 'NoneType' object has no attribute 'reserved' (AttributeError: 'NoneType' オブジェクトには 'reserved' という属性はありません)" というエラーが発生した場合は、指定した `<app-name>` が正しいことを確認してください。

    > [!TIP]
    > Web アプリのコンテナー設定は、`az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg` というコマンドを使用していつでも取得できます。 イメージは、`DOCKER_CUSTOM_IMAGE_NAME` プロパティで指定されます。 Web アプリが Azure DevOps または Azure Resource Manager テンプレートを使用してデプロイされる場合、イメージが、`LinuxFxVersion` という名前のプロパティにも反映されることがあります。 どちらのプロパティも目的は同じです。 その両方が Web アプリの構成に存在する場合、`LinuxFxVersion` が優先されます。

1. `az webapp config container set` コマンドが完了すると、App Service 上のコンテナーで Web アプリが実行されているはずです。

    アプリをテストするために、ブラウザーで `http://<app-name>.azurewebsites.net` にアクセスします。`<app-name>` は、実際の Web アプリの名前に置き換えてください。 初回アクセス時は、App Service がイメージ全体をレジストリからプルする必要があるため、アプリが応答するまでに時間がかかる場合があります。 ブラウザーがタイムアウトした場合は、単にページを更新します。 最初のイメージがプルされた後、それ以降のテストははるかに高速に実行されます。

    ![Azure での Web アプリのテストが成功](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>アプリのコードを変更して再デプロイする

このセクションでは、Web アプリのコードに変更を加えてコンテナーをリビルドした後、そのコンテナーをレジストリにプッシュします。 すると、更新されたイメージを App Service が自動的にレジストリからプルして、実行中の Web アプリを更新します。

1. ローカル *docker-django-webapp-linux* フォルダーにあるファイル *app/templates/app/index.html* を開きます。

1. 最初の HTML 要素を次のコードに一致するように変更します。

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. 変更を保存します。

1. *docker-django-webapp-linux* フォルダーに移動してイメージをリビルドします。

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. イメージのタグにあるバージョン番号を v1.0.1 に更新します。

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    `<registry-name>` をレジストリの名前に置き換えます。

1. イメージをレジストリにプッシュします。

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Web アプリを再起動します。

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    `<app_name>` は、実際の Web アプリの名前に置き換えてください。 再起動すると、更新したイメージが App Service によってコンテナー レジストリからプルされます。

1. ブラウザーで `http://<app-name>.azurewebsites.net` にアクセスして、更新がデプロイされていることを確認します。

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

1. コンテナーのログをオンにします。

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. ログのストリームを有効にします。

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    コンソール ログがすぐに表示されない場合は、30 秒以内にもう一度確認します。

    `https://<app-name>.scm.azurewebsites.net/api/logs/docker` で、ブラウザーからログ ファイルを検査することもできます。

1. 任意のタイミングでログのストリーミングを停止するには、**Ctrl**+**C** キーを押します。

## <a name="connect-to-the-container-using-ssh"></a>SSH を使用してコンテナーに接続する

SSH では、コンテナーとクライアント間の通信をセキュリティで保護できます。 コンテナーへの SSH 接続を有効にするには、それに合わせてカスタム イメージを構成する必要があります。 コンテナーが実行状態になったら、SSH 接続を開くことができます。

### <a name="configure-the-container-for-ssh"></a>SSH のコンテナーを構成する

このチュートリアルで使用しているサンプル アプリには、あらかじめ必要な構成が *Dockerfile* に存在し、その構成によって SSH サーバーがインストールされ、またログイン資格情報も設定されます。 このセクションは情報提供のみを目的としています。 コンテナーに接続するには、次のセクションに進んでください。

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> この構成は、コンテナーへの外部接続を許可しません。 SSH は Kudu/SCM サイトを通してのみ利用できます。 Kudu/SCM サイトは Azure アカウントにより認証されます。

また、この *Dockerfile* は *sshd_config* ファイルを */etc/ssh/* フォルダーにコピーし、コンテナーのポート 2222 を公開します。

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

ポート 2222 はプライベート仮想ネットワークのブリッジ ネットワーク内でコンテナーのみがアクセスできる内部ポートです。 

最後に、エントリ スクリプト *init.sh* によって SSH サーバーが起動されます。

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>コンテナーへの SSH 接続 を開く

1. ブラウザーで `https://<app-name>.scm.azurewebsites.net/webssh/host` にアクセスし、Azure アカウントでサインインします。 `<app-name>` は、実際の Web アプリの名前に置き換えてください。

1. サインインすると、Web アプリの情報ページにリダイレクトされます。 シェルを開いてコマンドを使用するには、ページの上部にある **[SSH]** を選択します。

    たとえば、そこで実行されているプロセスを観察するには、`top` コマンドを使用します。
    
## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースによって、継続してコストが発生する可能性があります。 リソース グループを削除すれば、そこに含まれているリソースはクリーンアップされます。

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

::: zone-end

## <a name="next-steps"></a>次のステップ

ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * カスタム イメージをプライベート コンテナー レジストリにデプロイする
> * App Service にカスタム イメージをデプロイする
::: zone pivot="container-linux"
> * イメージを更新して再デプロイする
::: zone-end
> * 診断ログにアクセスする
::: zone pivot="container-linux"
> * SSH を使用してコンテナーに接続する
::: zone-end

次のチュートリアルで、カスタム DNS 名をアプリにマップする方法を確認してください。

> [!div class="nextstepaction"]
> [チュートリアル:カスタム DNS 名をアプリにマップする](app-service-web-tutorial-custom-domain.md)

または、他のリソースを参照してください。

> [!div class="nextstepaction"]
> [カスタム コンテナーの構成](configure-custom-container.md)

::: zone pivot="container-linux"
> [!div class="nextstepaction"]
> [チュートリアル:マルチコンテナーの WordPress アプリ](tutorial-multi-container-app.md)
::: zone-end
