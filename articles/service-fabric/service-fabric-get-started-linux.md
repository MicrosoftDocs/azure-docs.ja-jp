---
title: "Linux での開発環境の設定 | Microsoft Docs"
description: "Linux にランタイムと SDK をインストールし、ローカル開発クラスターを作成します。 このセットアップを終えれば、アプリケーションを構築する準備は完了です。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 516b8e517a16dd0d87e02189260166696225fbab
ms.lasthandoff: 03/29/2017


---
# <a name="prepare-your-development-environment-on-linux"></a>Linux で開発環境を準備する
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

 Linux の開発コンピューターに [Azure Service Fabric アプリケーション](service-fabric-application-model.md) をデプロイして実行するには、ランタイムと共通 SDK をインストールする必要があります。 また、必要に応じて Java 用 SDK と .NET Core 用 SDK をインストールすることもできます。

## <a name="prerequisites"></a>前提条件

### <a name="supported-operating-system-versions"></a>サポートされるオペレーティング システムのバージョン
開発では、次のオペレーティング システムのバージョンがサポートされます。

* Ubuntu 16.04 (i**"Xenial Xerus"**)

## <a name="update-your-apt-sources"></a>apt ソースを更新する
apt get を実行して SDK および関連付けられたランタイム パッケージをインストールするために、まず apt ソースを更新する必要があります。

1. ターミナルを開きます。
2. ソース リストに Service Fabric リポジトリを追加します。

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```
3. ソース リストに **dotnet** リポジトリを追加します。

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```
4. apt キーリングに新しい GPG キーを追加します。

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```
    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. 新しく追加されたリポジトリに基づいてパッケージ リストを更新します。

    ```bash
    sudo apt-get update
    ```
## <a name="install-and-set-up-the-sdk-for-containers-and-guest-executables"></a>コンテナーとゲスト実行可能ファイルを作成するための SDK をインストールしてセットアップする
ソースが更新されたら、SDK をインストールできます。

1. Service Fabric SDK パッケージをインストールします。 インストールを開始することを確認し、使用許諾契約書に同意するよう求められます。

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```
2. SDK のセットアップ スクリプトを実行します。

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

共通 SDK パッケージをインストールする手順を実行すると、`yo azuresfguest` を実行して、ゲスト実行可能サービスまたはコンテナー サービスが含まれたアプリを作成できます。 場合によっては、**$NODE_PATH** 環境変数をノード モジュールの配置場所に設定する必要があります。 

    ```bash
    export NODE_PATH=$NODE_PATH:$HOME/.node/lib/node_modules 
    ```

環境をルートとして使用している場合、次のコマンドを使用して変数を設定しなければならない場合があります。

    ```bash
    export NODE_PATH=$NODE_PATH:/root/.node/lib/node_modules 
    ```

> [!TIP]
> これらのコマンドは、ログインのたびに環境変数を設定する必要がないように、~/.bashrc ファイルに追加できます。
>

## <a name="set-up-the-azure-cross-platform-cli"></a>Azure クロスプラットフォーム CLI をセットアップする
[Azure クロスプラットフォーム CLI][azure-xplat-cli-github] には、クラスターやアプリケーションなどの Service Fabric エンティティを操作するコマンドが含まれています。 この CLI は Node.js をベースにしているため、[Node がインストールされていることを確認][install-node]してから、以下の手順に進んでください。

1. 開発用コンピューターに GitHub リポジトリをクローンします。

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```
2. クローンしたリポジトリに移動し、Node Package Manager (npm) を使用して CLI の依存関係をインストールします。

    ```bash
    cd azure-xplat-cli
    npm install
    ```
3. クローンしたリポジトリの bin/azure フォルダーから /usr/bin/azure へのシンボリック リンクを作成します。これにより、/usr/bin/azure がパスに追加され、任意のディレクトリからコマンドを利用できるようになります。

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```
4. 最後に、オート コンプリート Service Fabric コマンドを有効にします。

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

> [!NOTE]
> Service Fabric のコマンドは、まだ Azure CLI 2.0 では使用できません。


## <a name="set-up-a-local-cluster"></a>ローカル クラスターをセットアップする
すべてが正常にインストールされていれば、ローカル クラスターを起動できます。

1. クラスターのセットアップ スクリプトを実行します。

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
2. Web ブラウザーを開いて、http://localhost:19080/Explorer にアクセスします。 クラスターが起動されている場合は、Service Fabric Explorer ダッシュボードが表示されます。

    ![Service Fabric Explorer on Linux][sfx-linux]

これで、構築済みの Service Fabric アプリケーション パッケージか、ゲスト コンテナーやゲスト実行可能ファイルをベースに新規作成したパッケージをデプロイできるようになりました。 Java 用 SDK または .NET Core 用 SDK を使用して新しいサービスを構築するには、後続のセクションに示す設定手順を実行します。


> [!NOTE]
> スタンドアロン クラスターは Linux でサポートされていません。プレビューでは、ワン ボックスと Azure Linux マルチマシン クラスターのみがサポートされています。
>

## <a name="install-the-java-sdk-optional-if-you-wish-to-use-the-java-programming-models"></a>Java SDK をインストールする (省略可能。Java プログラミング モデルを使用したい場合)
Java SDK には、Java を使用して Service Fabric サービスを構築するために必要なライブラリとテンプレートが用意されています。

1. Java SDK パッケージをインストールします。

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```
2. SDK のセットアップ スクリプトを実行します。

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```
## <a name="install-the-eclipse-neon-plugin-optional"></a>Eclipse Neon プラグインをインストールする (省略可能)

**Eclipse IDE for Java Developers** 内から Service Fabric 用 Eclipse プラグインをインストールできます。 Eclipse を使用すると、Service Fabric Java アプリケーションのほかに、Service Fabric ゲスト実行可能アプリケーションと Service Fabric コンテナー アプリケーションを作成できます。

> [!NOTE]
> Eclipse プラグインで作成してデプロイするのがゲスト実行可能ファイルとコンテナー アプリケーションのみであっても、Java SDK のインストールは Eclipse プラグインを使用するうえで必須です。
>

1. 最新の Eclipse **Neon** と Buildship バージョン (1.0.17 以降) がインストールされていることを Eclipse で確認します。 **[Help (ヘルプ)]、[Installation Details (インストールの詳細)]** の順に選択して、インストールされたコンポーネントのバージョンを確認できます。 Buildship は、[こちら][buildship-update]の手順に従って更新できます。
2. **[Help (ヘルプ)]、[Install New Software (新しいソフトウェアのインストール)]** の順に選択して、Service Fabric プラグインをインストールします。
3. [Work with (作業対象)] ボックスに、「http://dl.windowsazure.com/eclipse/servicefabric」と入力します。
4. [追加] をクリックします。
    ![Eclipse プラグイン][sf-eclipse-plugin]
5. Service Fabric プラグインを選択し、[Next (次へ)] をクリックします。
6. 指示に従ってインストールを実行し、使用許諾契約書に同意します。

Service Fabric Eclipse プラグインを既にインストールしてある場合は、最新バージョンを使用していることを確認してください。 [``Help => Installation Details``] を選択し、インストールされているプラグインの一覧で Service Fabric を探すことで確認できます。 新しいバージョンが使用できる場合は更新を選択します。 

詳細については、[Service Fabric での Eclipse の使用](service-fabric-get-started-eclipse.md)に関するページを参照してください。


## <a name="install-the-net-core-sdk-optional-if-you-wish-to-use-the-net-core-programming-models"></a>.NET Core SDK をインストールする (省略可能。.NET Core プログラミング モデルを使用したい場合)
.NET Core SDK には、クロスプラットフォームの .NET Core を使用して Service Fabric サービスを構築するために必要なライブラリとテンプレートが用意されています。

1. .NET Core SDK パッケージをインストールします。

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. SDK のセットアップ スクリプトを実行します。

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## <a name="updating-the-sdk-and-runtime"></a>SDK とランタイムの更新

SDK とランタイムを最新バージョンに更新するには、次の手順を実行します (更新やインストールを行わない SDK はリストから削除します)。

   ```bash
   sudo apt-get update
   sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
   ```

CLI を更新する場合は、CLI をクローンしたディレクトリに移動し、`git pull` を実行して更新します。  更新に追加の手順が必要な場合は、リリース ノートで確認できます。 


## <a name="next-steps"></a>次のステップ
* [Yeoman を使用して Linux で最初の Service Fabric Java アプリケーションを作成してデプロイする](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse 用の Service Fabric プラグインを使用して Linux で最初の Service Fabric Java アプリケーションを作成してデプロイする](service-fabric-get-started-eclipse.md)
* [Linux で最初の CSharp アプリケーションを作成する](service-fabric-create-your-first-linux-application-with-csharp.md)
* [OSX で開発環境を準備する](service-fabric-get-started-mac.md)
* [Azure CLI を使用した Service Fabric アプリケーションの管理](service-fabric-azure-cli.md)
* [Service Fabric における Windows と Linux の違い](service-fabric-linux-windows-differences.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

