---
title: "Azure Container Service と DC/OS を使用した CI/CD | Microsoft Docs"
description: "DC/OS が実行されている Azure Container Service クラスターへの複数コンテナー Docker アプリのビルドとデプロイを完全に自動化する方法。"
services: container-service
documentationcenter: 
author: spboyer
manager: wpickett
editor: 
tags: acs, azure-container-service
keywords: "Docker、コンテナー、マイクロ サービス、Mesos、Azure"
ms.assetid: b16b767a-2eaa-418a-becc-8c032713a1f2
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: johnsta
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 65fc37a1fd1d1d0149b98767117f8faafb5dcd2b
ms.lasthandoff: 03/18/2017


---

# <a name="continuous-integration-and-deployment-of-multi-container-docker-applications-to-azure-container-service"></a>Azure Container Service への複数コンテナー Docker アプリケーションの継続的インテグレーションと継続的デプロイ 
このチュートリアルでは、DC/OS が実行されている Azure Container Service クラスターへの複数コンテナー Docker アプリのビルドとデプロイを完全に自動化する方法について説明します。 継続的インテグレーションと継続的デプロイ (CI/CD) のメリットはよく知られていますが、ワークフローにコンテナーを統合する際には新しい考慮事項があります。 ここでは、新しい Azure Container Registry と CLI コマンドを使用して、カスタマイズできるエンド ツー エンドのフローを設定します。

## <a name="get-started"></a>作業開始
このチュートリアルは、OS X、Windows、または Linux で実行できます。
- Azure サブスクリプションが必要です。 Azure サブスクリプションがない場合は、[アカウントにサインアップ](https://azure.microsoft.com/)できます。
- [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールします。

## <a name="what-well-create"></a>作成内容
これから設定するアプリとそのデプロイ フローのいくつかの重要な点について、手短に説明します。
* **アプリケーションは複数のサービスで構成されます**。 アプリのサービスを定義する Docker 資産、Dockerfile、docker-compose.yml は、それぞれ別個のコンテナーで実行されています。 これらのおかげで、アプリの構成要素は別々にスケールできます。また、各サービスを異なるプログラミング言語とフレームワークで記述できます。 アプリのコードは、1 つ以上の Git ソース リポジトリに分散してホストできます (現在、ツールでは GitHub または Visual Studio Team Services がサポートされています)。

* アプリは、**DC/OS を使用して構成された ACS クラスター**で実行されます。 コンテナー オーケストレーターを使用すると、クラスターの正常性を管理し、必要な数のコンテナー インスタンスが実行し続けるようにさせることができます。 

* **コンテナー イメージのビルドとデプロイのプロセスをダウンタイムなしで完全に自動化**します。 ブランチに対して "git push" を実行するようチームの開発者にお勧めします。これにより、統合プロセスが自動的にトリガーされます。 つまり、コンテナー イメージをビルドしてタグ付けし、各コンテナーでテストを実行して、これらのイメージを Docker プライベート レジストリにプッシュします。 そこから、さらにテストを行うために、ACS クラスター上で共有される運用前環境に新しいイメージが自動的にデプロイされます。

* **ある環境から次の環境にリリースを昇格します**。たとえば、開発環境、テスト環境、ステージング環境、運用環境の順に昇格します。 下流の環境に昇格するたびに、コンテナー イメージをビルドし直して、前の環境でテストしたのと同じイメージをデプロイする必要はありません。 この概念は "*不変のサービス*" と呼ばれ、未検出のエラーが運用環境に入り込む可能性を低くします。

* ACS クラスターのコンピューティング リソースを最も効果的に使用するには、同じクラスターを使ってビルド タスクを実行し、ビルドとデプロイの手順を完全にコンテナー化します。 クラスターでは、複数の開発/テスト/運用環境もホストしています。


## <a name="create-an-azure-container-service-cluster-configured-with-dcos"></a>DC/OS を使用して構成された Azure Container Service クラスターを作成する

>[!IMPORTANT]
> セキュリティで保護されたクラスターを作成するには、`az acs create` を呼び出す際に渡す SSH 公開キー ファイルを渡します。 `--generate-ssh-keys` オプションを使用して Azure CLI 2.0 でキーを生成すると同時にそれらを渡すことも、`--ssh-key-value` オプションを使用してパスをキーに渡すこともできます (既定の場所は、Linux では `~/.ssh/id_rsa.pub`、Windows では `%HOMEPATH%\.ssh\id_rsa.pub` ですが、変更できます)。
<!---Loc Comment: What do you mean by "you pass your SSH public key file to pass"? Thank you.--->
> Linux で SSH 公開キー ファイルと秘密キー ファイルを作成するにはに、[Linux と Mac での SSH キーの作成](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fcontainer-services%2ftoc.json)に関するページを参照してください。 
> Windows で SSH 公開キー ファイルと秘密キー ファイルを作成するには、[Windows での SSH キーの作成](../virtual-machines/virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fcontainer-services%2ftoc.json)に関するページを参照してください。 

1. 最初に、ターミナル ウィンドウに [as login](/cli/azure/#login) コマンドを入力して、Azure CLI で Azure サブスクリプションにログインします。 

    `az login`

1. [az group create](/cli/azure/group#create) を使用して、クラスターを配置するリソース グループを作成します。
    
    `az group create --name myacs-rg --location westus`

    最も近い [Azure データセンター リージョン](https://azure.microsoft.com/regions)を指定することをお勧めします。 

1. [az acs create](/cli/azure/acs#create) を使って既定の設定で ACS クラスターを作成し、パスを SSH 公開キー ファイルに渡します。 

    ```azurecli
    az acs create \
    --resource-group myacs-rg 
    --name myacs \
    --dns-prefix myacs \
    --ssh-key-value ~/.ssh/id_rsa.pub
    ```
    
この手順には数分かかるため、そのまま読み進めてください。  `acs create` コマンドを実行すると、新しく作成したクラスターの情報が返されます (または、`az acs list` を使ってサブスクリプション内の ACS クラスターを一覧表示できます)。 その他の ACS 構成オプションについては、[ACS クラスターの作成と構成に関する記事をお読みください](container-service-deployment.md)。

## <a name="set-up-sample-code"></a>サンプル コードを設定する
クラスターが作成されている間に、ACS にデプロイするサンプル コードを設定できます。

1. [https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git](https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git) でサンプル GitHub リポジトリを[フォーク](https://help.github.com/articles/fork-a-repo/)して、独自のコピーを作成します。 基本的に、このアプリは "hello world" の複数コンテナー バージョンです。
1. 独自の GitHub アカウントにフォークを作成したら、リポジトリをローカルのコンピューターに複製します。

    ```bash
    git clone  https://github.com/your-github-account/container-service-dotnet-continuous-integration-multi-container.git
    cd container-service-dotnet-continuous-integration-multi-container
    ```
    
コードについて詳しく見ていきます。
* `/service-a` は、Node.js バックエンドを持つ Angular.js ベースの Web アプリです。
* `/service-b` は .NET Core サービスであり、`service-a` によって REST 経由で呼び出されます。
* `service-a` と `service-b` の両方で、それぞれ Node.js ベースと .NET Core ベースのコンテナー イメージを表す各ディレクトリに `Dockerfile` が含まれています。 
* `docker-compose.yml` は、ビルドしてデプロイするサービスのセットを宣言します。
* `service-a` と `service-b` のほか、`cache` という名前の 3 番目のサービスによって、`service-a` で使用できる Redis Cache が実行されます。 `cache` は、ソース リポジトリにコードがないという点で、最初の 2 つのサービスと異なります。 その代わり、事前に作成された `redis:alpine` イメージを Docker Hub から取得して ACS にデプロイします。
* `/service-a/server.js` には、`service-a` が `service-b` と `cache` の両方を呼び出すコードが含まれています。 `docker-compose.yml` で付けられた名前で、`service-a` コードが `service-b` と `cache` を参照することに注意してください。 `docker-compose` を使ってローカル コンピューター上でこれらのサービスを実行すると、Docker によって、すべてのサービスが適切にネットワーク接続され、名前で互いを検出します。 負荷分散されたネットワークを使用してクラスター環境でサービスを実行すると、通常、ローカルで実行するよりもかなり複雑になります。 さいわい、CI/CD フローを設定する Azure CLI コマンドを使用すると、この簡単なサービス検出コードが ACS でそのまま実行されます。 

    ![複数コンテナー サンプル アプリの概要](media/container-service-setup-ci-cd/multi-container-sample-app-overview.PNG)

## <a name="set-up-continuous-integration-and-deployment"></a>継続的インテグレーションと継続的デプロイを設定する
1. ACS クラスターの準備ができていることを確認します。[az acs list](/cli/azure/acs#list) を実行し、作成した ACS クラスターが一覧表示されることを確かめます  (注: ACS では DC/OS 1.8 以上が実行されている必要があります)。

1. [GitHub の個人用アクセス トークンを作成](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)し、少なくとも `repo` スコープを許可します。  次のコマンドで使用するため、忘れずにトークンをクリップボードにコピーしてください (このコマンドによって GitHub リポジトリの [webhook](https://help.github.com/articles/about-webhooks/) が設定されます)。 

1. 現在のディレクトリを複製したソース リポジトリのルートに設定し、先ほど作成した _&lt;GitHubPersonalAccessToken&gt; を使用してビルドとリリースのパイプラインを作成します。
    
    `cd container-service-dotnet-continuous-integration-multi-container`

    ```azurecli
    az container release create \
    --target-name myacs \
    --target-resource-group myacs-rg \
    --remote-access-token <GitHubPersonalAccessToken>
    ```

    `--target-name` は ACS クラスターの名前、`--target-resource-group` は ACS クラスターのリソース グループの名前です。

初回実行時には、このコマンドが完了するまでしばらく時間がかかることがあります。 完了したら、作成したビルドとリリースのパイプラインに関する重要な情報が返されます。
* `sourceRepo`: ソース コードがプッシュされたらビルドとリリースのパイプラインが自動的にトリガーされるように、[webhook](https://help.github.com/articles/about-webhooks/) はソース リポジトリに対して構成されています。  
* `vstsProject`: [Visual Studio Team Services](https://www.visualstudio.com/team-services/) (VSTS) は、ワークフローを "*進める*" ように構成されています (実際のビルド タスクとデプロイ タスクは ACS のコンテナー内で実行されます)。 特定の VSTS アカウントとプロジェクトを使用したい場合は、`--vsts-account-name` パラメーターと `--vsts-project-name` パラメーターを使用して定義できます。
* `buildDefinition`: 各ビルドで実行されるタスクを定義します。 コンテナー イメージは、docker-compose.yml で定義されているサービスごとに生成され、Docker コンテナー レジストリにプッシュされます。 
* `containerRegistry`: Azure Container Registry は、Docker コンテナー レジストリを実行する管理されたサービスです。 新しい Azure コンテナー レジストリは、既定の名前で作成されます。代わりに、`--registry-name` パラメーターを使用して Azure コンテナー レジストリの名前を指定することもできます。
* `releaseDefinition`: 各デプロイで実行されるタスクを定義します。 docker-compose.yml で定義されているサービスのコンテナー イメージは、コンテナー レジストリからプルされ、ACS クラスターにデプロイされます。 既定では、"*開発*"、"*テスト*"、"*運用*" の 3 つの環境が作成されます。 リリース定義は、ビルドが正常に完了するたびに "*開発*" に自動的にデプロイされるよう既定で構成されています。 リリースは、ビルドし直すことなく "*テスト*" または "*運用*" に手動で昇格できます。 既定のフローは VSTS でカスタマイズできます。 
* `containerService`: ターゲット ACS クラスター (DC/OS 1.8 を実行している必要があります)。


次のスニペットは、`myregistry` という名前の既存の Azure コンテナー レジストリが既にある場合に入力するコマンド例です。 VSTS アカウント `myvstsaccount.visualstudio.com` と既存の VSTS プロジェクト `myvstsproject` を使ってリリース定義を作成してビルドします。
        
```azurecli
az container release create \
--target-name myacs \
--target-resource-group myacs-rg \
--registry-name myregistry \
--vsts-account-name myvstsaccount \
--vsts-project-name myvstsproject \
--remote-access-token <GitHubPersonalAccessToken>
```

## <a name="view-deployment-pipeline-progress"></a>デプロイ パイプラインの進行状況を表示する
パイプラインが作成されたら、初回のビルドとデプロイが自動的に開始されます。 その後のビルドは、ソース リポジトリにコードがプッシュされるたびにトリガーされます。 ビルド定義またはリリース定義の URL をブラウザーで開くことで、ビルドやリリースの進行状況を確認できます。

次のコマンドを実行すると、ACS クラスターに関連付けられたリリース定義 URL をいつでも確認できます。

```azurecli
az container release list \
--target-name myacs \
--target-resource-group myacs-rg
``` 

![VSTS ビルド](media/container-service-setup-ci-cd/vsts-build.PNG)

"*複数コンテナー アプリの CI 結果を示す VSTS のスクリーンショット*"

![VSTS リリース](media/container-service-setup-ci-cd/vsts-release.PNG)

"*複数の環境がある VSTS docker-compose リリース*"

## <a name="view-the-application"></a>アプリケーションを表示する
この時点では、アプリケーションは共有の "開発" 環境にデプロイされており、一般公開されていません。 その間、DC/OS ダッシュボードを使用してサービスを表示および管理し、[DC/OS 関連のエンドポイントへの SSH トンネルを作成](https://azure.microsoft.com/documentation/articles/container-service-connect/)します。または、Azure CLI に備わっている便利なコマンドを実行します。

> [!IMPORTANT]
> 初回デプロイ時は、続行する前に VSTS リリースが正常にデプロイされていることを確認してください。

> [!NOTE]
> Windows のみ: このセクションを完了するには、[Pageant](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) を設定する必要があります。
> 
>* *PuttyGen* を起動し、ACS クラスターの作成に使用する SSH 秘密キーを読み込みます (%homepath%\id_rsa)。
>* 同じフォルダーに SSH 秘密キーを `id_rsa.ppk` として保存します。
>* *Pageant* を起動します。実行が開始され、右下のシステム トレイにアイコンが表示されます。
>* システム トレイのアイコンを右クリックし、*[キーの追加]* を選択します。
>* `id_rsa.ppk` ファイルを追加します。
> 
> 

1. Azure CLI の便利なコマンドを使用して ACS クラスターの DC/OS ダッシュボードを開きます。
    
    `az acs dcos browse -g myacs-rg -n myacs`

    * `-g` は、ターゲット ACS クラスターのリソース グループ名です。
    * `-n` は、ターゲット ACS クラスターの名前です。
    * このコマンドには管理者特権が必要なため、ローカル アカウントのパスワードが求められる場合があります。 コマンドを使用すると、DC/OS エンドポイントへの SSH トンネルが作成され、そのエンドポイントが既定のブラウザーで開かれます。また、ブラウザーの Web プロキシが一時的に構成されます。 

    > [!TIP]
    > ACS クラスターの名前を検索する必要がある場合は、`az acs list` を実行することで、サブスクリプション内のすべての ACS クラスターを一覧表示できます。 

1. DC/OS ダッシュボードの左側にあるナビゲーション メニューの **[Services (サービス)]** をクリックします ([http://localhost/#/services](http://localhost/#/services))。 パイプラインを介してデプロイされたサービスは、(VSTS リリース定義の環境にちなんで名付けられた) *dev* というルート フォルダーの下にグループ化されます。 

![Marathon UI](media/container-service-setup-ci-cd/marathon-ui.png)

DC/OS ダッシュボードでは多数の便利な機能を実行できます。

* 各サービスのデプロイの状態の追跡
* CPU とメモリの要件の表示
* ログの表示
* 各サービスのインスタンス数のスケーリング

**service-a の Web アプリケーションを表示するには**: *dev* ルート フォルダーから始めて、`service-a` に到達するまでフォルダー階層をドリルダウンします。 このビューには、`service-a` の実行中のタスク (またはコンテナー インスタンス) の一覧が表示されます。

![service a](media/container-service-setup-ci-cd/service-a.png)

タスクをクリックしてそのビューを開き、使用可能なエンドポイントのいずれかをクリックします。

![service a のタスク](media/container-service-setup-ci-cd/service-a-task.PNG)

簡単な Web アプリで `service-a` を呼び出します。これにより、`service-b` が呼び出され、"hello world" メッセージが返されます。 要求が行われるたびに Redis のカウンターが増加します。

![service a の Web アプリ](media/container-service-setup-ci-cd/service-a-web-app.PNG)

### <a name="optional-reaching-a-service-from-the-command-line"></a>(省略可能) コマンド ラインを使用したサービスへの接続
コマンド ラインで curl を使用してサービスに接続したい場合は、次の手順に従います。

1. `az acs dcos browse --verbose -g myacs-rg -n myacs` を実行し、パスワードを入力した後に "Proxy running on <ip-address>:<port>" という行をメモしておきます。
1. 新しいターミナル ウィンドウで、次のように入力します。

    `export http_proxy=http://<web-proxy-service-ip>:<portnumber>`

    次に例を示します。`export http_proxy=http://127.0.0.1:55405`

1. これで、サービス エンドポイント `curl http://service-url` に対して curl を実行できます。`service-url` は、Marathon UI からサービス エンドポイントに移動するときに表示されるアドレスです。 コマンド ラインで http_proxy 変数の設定を解除するには、「`unset http_proxy`」と入力します。
 

## <a name="scale-services"></a>サービスをスケールする
DC/OS ダッシュボードを表示している間に、サービスをスケールします。
1. *dev* サブフォルダー内のアプリケーションに移動します。
1. `service-b` にマウス ポインターを置いて歯車アイコンをクリックし、**[Scale (スケール)]** を選択します。

    ![[操作] メニュー](media/container-service-setup-ci-cd/marathon-ui-action-menu.PNG)

1. 数を 3 に増やし、**[Scale Service (サービスをスケールする)]** をクリックします。

    ![サービスのスケール](media/container-service-setup-ci-cd/marathon-ui-scale-service.png)

1. 実行中の Web アプリに戻り、*[Say It Again]* ボタンを繰り返しクリックします。 `service-b` の呼び出しが、ホスト名のコレクションでラウンドロビンを開始することがわかります。一方、`service-a` の単一インスタンスは同じホストを報告し続けます。   

## <a name="promote-a-release-to-downstream-environments-without-rebuilding-container-images"></a>コンテナー イメージをビルドし直すことなく下流の環境にリリースを昇格する
既定では、VSTS リリース パイプラインによって "*開発*"、"*テスト*"、"*運用*" の 3 つの環境が設定されます。 ここまで、"*開発*" へのデプロイを行いました。 コンテナー イメージをビルドし直すことなく、次の下流の環境である "*テスト*" にリリースを昇格する方法について見ていきます。 このワークフローを実行することで、前の環境でテストしたのとまったく同じイメージをデプロイできます。この概念は "*不変のサービス*" と呼ばれ、未検出のエラーが運用環境に入り込む可能性を低くします。

1. VSTS Web UI で **[Releases (リリース)]** に移動します。

    ![VSTS リリース メニュー](media/container-service-setup-ci-cd/vsts-releases-menu.PNG)

1. 最新のリリースを開きます。

1. リリース定義のメニュー バーで、**[Deploy (デプロイ)]** をクリックします。次に、デプロイする次の環境として **[Test (テスト)]** を選択して、新しいデプロイを開始します。先ほど "*開発*" にデプロイしたのと同じイメージをここでも使用します。 デプロイの詳細を確認したい場合は、**[Logs (ログ)]** をクリックします。

    ![VSTS でのリリースの昇格](media/container-service-setup-ci-cd/vsts-promote-release.PNG)

"*テスト*" へのデプロイが成功したら、Marathon UI にある *test* という名前の新しいルート フォルダーに、この環境で実行中のサービスが含まれます。 

![DC/OS の各環境のサブフォルダー](media/container-service-setup-ci-cd/marathon-ui-dev-test-environments.png)

## <a name="trigger-a-new-build-and-deployment"></a>新しいビルドとデプロイのトリガー
チームの開発者がコードの変更をソース リポジトリにプッシュした場合にどうなるかをシミュレーションしてみましょう。

1. コード エディターに戻り、`service-a/public/index.html` を開きます。 
1. 次のコード行を変更します。

    `<h2>Server Says</h2>`

    次のようにします。

    `<h2>Server Says Hello</h2>`

1. ファイルを保存し、コードの変更をソース ディレクトリにコミットしてプッシュします。

    ```bash
    git commit -am 'updated title'
    git push
    ```

コミットすると新しいビルドが自動的に始まり、新しいリリースは "*開発*" にデプロイされます。 下流の環境 ("*テスト*" または "*運用*") のサービスは、特定のリリースをその環境に昇格することを決定しない限り変更されません。

VSTS でビルド定義を開くと、次のように表示されます。 

![git push でトリガーされた新しいビルド](media/container-service-setup-ci-cd/new-build.png)



## <a name="expose-public-endpoint-for-production"></a>運用のためにパブリック エンドポイントを公開する

1. 次の yaml コードを、ソース リポジトリのルート フォルダーにある `docker-compose.env.production.yml` という名前の新しいファイルに追加します。 これにより、パブリック エンドポイントが `service-a` 用に公開されるようにするラベルが追加されます。 
    
    ```yaml
    version: "2"
    services:
      service-a:
        labels:
          com.microsoft.acs.dcos.marathon.vhost: "<FQDN, or custom domain>"
    ```

    * ラベルの値には、ACS エージェントの完全修飾ドメイン名 (FQDN) の URL か、カスタム ドメイン (例: app.contoso.com) を指定できます。 ACS エージェントの FQDN を確認するには、`az acs list` コマンドを実行し、プロパティで `agentPoolProfiles.fqdn` を確認します。 たとえば、「 `myacsagents.westus.cloudapp.azure.com`」のように入力します。
    * サンプル アプリは既定で、ポート 80 をリッスンします。使用する Docker アプリケーションが他のポートをリッスンするユーザーの場合、インスタンス `port 8080` または `443` について、ポート番号を FQDN にアタッチしてください。 たとえば、「 `myacsagents.westus.cloudapp.azure.com:8080`」のように入力します。 ただし、外部からアプリケーションにアクセスしようとした場合、ポート 80 で照会する必要があります。
    * docker-compose.env.<*環境名*>.yml というファイル名の規則に従うと、これらの設定のみが名前付きの環境に影響します (例では、"*運用*" という名前の環境)。 VSTS でリリース定義を確認します。各環境のデプロイ タスクは、この規則に従って名付けられた docker-compose ファイルからの読み取りに設定されています。

1. マスター ソース リポジトリにファイルをコミットしてプッシュし、別のビルドを開始します。

    ```bash
    git add .
    git commit -am "expose public port for service-a"
    git push
    ```

1. 更新のビルドが完了して "*開発*" にデプロイされるのを待ってから、それを "*テスト*"、"*運用*" の順に昇格します  ("*運用*" に直接デプロイできますが、このチュートリアルの目的を考慮して、次の下流の環境のみにデプロイする練習を行うことをお勧めします)。

1. (省略可能) (app.contoso.com など) vhost に**カスタム ドメインを指定した場合**、DNS レコードをドメイン プロバイダーの設定に追加します。 ドメイン プロバイダーの管理 UI にログインし、次のように DNS レコードを追加します。

    * 種類: CNAME
    * ホスト: カスタム ドメイン (app.contoso.com など)
    * 応答: ACS エージェント FQDN (myacsagents.westus.cloudapp.azure.com など)
    * TTL (オプション): TTL を編集する機能をドメイン プロバイダーから付与される場合があります。 値を小さくすると、DNS レコードの更新の反映が速くなります。   

1. リリースが "*運用*" にデプロイされると、そのバージョンはだれでもアクセスできるようになります。 `com.microsoft.acs.dcos.marathon.vhost` ラベルで指定した URL をブラウザーで開きます  (注: 運用前環境へのリリースはプライベートのままです)。

## <a name="summary"></a>概要
ご利用ありがとうございます。 DC/OS を使用して ACS クラスターを作成する方法のほか、完全に自動化およびコンテナー化された、複数コンテナー アプリのビルドとデプロイのパイプラインを設定する方法について学習しました。

確認する次のステップをいくつか次に示します。
* **VSTS エージェントのスケール。** ビルドとリリースのタスクの実行に関してスループットを向上させる必要がある場合、VSTS エージェント インスタンスの数を増やすことができます。 DC/OS ダッシュボードの **[Services (サービス)]** に移動して vsts-agents フォルダーを開き、VSTS エージェント インスタンスの数のスケーリングを試します。
* **単体テストの統合。** 次の GitHub リポジトリでは、単体テストと統合テストをコンテナーで実行し、ビルド タスクに含める方法がわかります: [https://github.com/mindaro/sample-app](https://github.com/mindaro/sample-app)。 
    * ヒント: リポジトリにある `service-a/unit-tests.js`、`service-a/service-tests.js`、`docker-compose.ci.unit-tests.yml`、`docker-compose.ci.service-tests.yml` という名前のファイルを参照してください。

## <a name="clean-up"></a>クリーンアップ
このチュートリアルに関連するコンピューティング料金を抑えるには、次のコマンドを実行して ACS クラスターに関連するデプロイ パイプライン リソースをメモします。

```azurecli    
az container release list --resource-name myacs --resource-group myacs-rg
```

ACS クラスターを削除します。
1. [Azure Portal](https://portal.azure.com) にサインインします。
1. ACS クラスターが含まれているリソース グループを検索します。
1. リソース グループのブレード UI を開き、ブレードのコマンド バーにある **[削除]** をクリックします。

Azure コンテナー レジストリを削除します。Azure Portal で、Azure コンテナー レジストリを検索して削除します。 

[Visual Studio Team Services アカウントには、最初の 5 ユーザーを無料で使用できる Basic アクセス レベルが用意されています](https://azure.microsoft.com/en-us/pricing/details/visual-studio-team-services/)が、ビルドとリリースの定義を削除できます。

VSTS ビルド定義を削除します。
        
1. ブラウザーでビルド定義 URL を開き、(現在表示されているビルド定義の名前の横にある) **[ビルド定義]** リンクをクリックします。
2. 削除したいビルド定義の横にあるアクション メニューをクリックし、**[定義の削除]** を選択します。

`![VSTS ビルド定義の削除](media/container-service-setup-ci-cd/vsts-delete-build-def.png) 

VSTS リリース定義を削除します。

1. ブラウザーでリリース定義 URL を開きます。
2. 左側の [リリース定義] リストで、削除するリリース定義の横にあるドロップダウンをクリックし、**[削除]** を選択します。

`![VSTS リリース定義の削除](media/container-service-setup-ci-cd/vsts-delete-release-def.png)

