---
title: Linux での開発環境の設定 | Microsoft Docs
description: Linux にランタイムと SDK をインストールし、ローカル開発クラスターを作成します。 このセットアップが終わると、アプリケーションを構築する準備は完了です。
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 49e80c3fc8935064aceef8ef4e2bd3257c41e5e7
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514200"
---
# <a name="prepare-your-development-environment-on-linux"></a>Linux で開発環境を準備する
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Linux の開発コンピューターに [Azure Service Fabric アプリケーション](service-fabric-application-model.md) をデプロイして実行するには、ランタイムと共通 SDK をインストールする必要があります。 また、必要に応じて Java および .NET Core デプロイ用 SDK をインストールすることもできます。 

この記事の手順では、Linux にネイティブにインストールするか、Service Fabric OneBox コンテナー イメージ (`microsoft/service-fabric-onebox`) を使うことを想定しています。

Service Fabric のランタイムと SDK を Windows Subsystem for Linux にインストールすることはサポートされません。 Azure Service Fabric コマンド ライン インターフェイス (CLI) を使用して、クラウドやオンプレミスでホストされた Service Fabric のエンティティを管理することができ、これはサポートされています。 CLI をインストールする方法については、[Service Fabric CLI のセットアップ](./service-fabric-cli.md)に関するページを参照してください。


## <a name="prerequisites"></a>前提条件

開発用に、次のオペレーティング システムのバージョンがサポートされています。

* Ubuntu 16.04 (`Xenial Xerus`)

    `apt-transport-https` パッケージがインストールされていることを確認してください。
         
    ```bash
    sudo apt-get install apt-transport-https
    ```
* Red Hat Enterprise Linux 7.4 (Service Fabric プレビュー サポート)


## <a name="installation-methods"></a>インストール方法

### <a name="script-installation-ubuntu"></a>インストール スクリプト (Ubuntu)

Service Fabric ランタイムと Service Fabric 共通 SDK を **sfctl** CLI と共にインストールする場合に便利なように、スクリプトが用意されています。 次のセクションの手動のインストール手順を実行します。 インストールされるものと関連のライセンスが表示されます。 スクリプトの実行では、インストールされているすべてのソフトウェアのライセンスに同意することを前提としています。

スクリプトが正常に実行されたら、[ローカル クラスターのセットアップ](#set-up-a-local-cluster)に進みます。

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="manual-installation"></a>手動のインストール
Service Fabric ランタイムと共通 SDK の手動インストールの場合、このガイドの残りの説明に従います。

## <a name="update-your-apt-sources-or-yum-repositories"></a>APT ソースまたは Yum リポジトリを更新する
コマンド ライン ツール apt-get を実行して SDK および関連付けられたランタイム パッケージをインストールするために、まず APT (Advanced Packaging Tool) ソースを更新する必要があります。

### <a name="ubuntu"></a>Ubuntu

1. ターミナルを開きます。
2. ソース リストに Service Fabric リポジトリを追加します。

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. ソース リストに `dotnet` リポジトリを追加します。

    ```bash
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    ```

4. 新しい Gnu Privacy Guard (GnuPG または GPG) キーを APT キーリングに追加します。

    ```bash
    curl -fsSL https://packages.microsoft.com/keys/msopentech.asc | sudo apt-key add -
    ```

5. 公式の Docker GPG キーを APT キーリングに追加します。

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Docker レポジトリを設定します。

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Azul JDK キーを APT キーリングに追加し、そのリポジトリを設定します。

    ```bash
    curl -fsSL https://repos.azul.com/azul-repo.key | sudo apt-key add -
    sudo add-apt-repository 'deb http://repos.azulsystems.com/ubuntu stable main'
    ```

8. 新しく追加されたリポジトリに基づいてパッケージ リストを更新します。

    ```bash
    sudo apt-get update
    ```


### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (Service Fabric プレビュー サポート)

1. ターミナルを開きます。
2. Extra Packages for Enterprise Linux (EPEL) をダウンロードしてインストールします。

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. EfficiOS RHEL7 パッケージ リポジトリをシステムに追加します。

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. EfficiOS パッケージの署名キーをローカル GPG キーリングにインポートします。

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```

5. Microsoft RHEL リポジトリをシステムに追加します。

    ```bash
    curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
    sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
    ```

6. .NET SDK をインストールします。

    ```bash
    yum install rh-dotnet20 -y
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster"></a>ローカル クラスター用の Service Fabric SDK をインストールしてセットアップする

ソースを更新したら、SDK をインストールできます。 Service Fabric SDK パッケージをインストールし、インストールを確認して、ライセンス契約に同意します。

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

> [!TIP]
>   Service Fabric パッケージのライセンス受け取りを自動化するコマンドを以下に示します。
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (Service Fabric プレビュー サポート)

```bash
sudo yum install servicefabricsdkcommon
```

SDK インストールに付属する Service Fabric ランタイムには、次の表のパッケージが含まれています。 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
Ubuntu | 2.0.0 | AzulJDK 1.8 | npm から暗黙的に | latest |
RHEL | - | OpenJDK 1.8 | npm から暗黙的に | latest |

## <a name="set-up-a-local-cluster"></a>ローカル クラスターをセットアップする
インストールの完了後、ローカル クラスターを起動します。

1. クラスターのセットアップ スクリプトを実行します。

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Web ブラウザーを開いて､[Service Fabric Explorer](http://localhost:19080/Explorer) (`http://localhost:19080/Explorer`) に移動します。 クラスターが起動すると、Service Fabric Explorer ダッシュボードが表示されます。 クラスターが完全にセットアップされるまでに数分かかる場合があります。 ブラウザーで URL を開けない場合、またはシステムの準備が完了していることを Service Fabric Explorer で確認できない場合は、数分待ってからもう一度実行してください。

    ![Service Fabric Explorer on Linux][sfx-linux]

    これで、構築済みの Service Fabric アプリケーション パッケージか、またはゲスト コンテナーやゲスト実行可能ファイルをベースに新規作成したパッケージをデプロイできるようになりました。 Java 用 SDK または .NET Core 用 SDK を使用して新しいサービスを構築するには、次のセクションに示すセットアップ手順を実行します。


> [!NOTE]
> スタンドアロン クラスターは Linux ではサポートされません。


> [!TIP]
> SSD ディスクが利用できる場合、優れたパフォーマンスを得るために、devclustersetup.sh で `--clusterdataroot` を使用して SSD フォルダー パスを渡すことをお勧めします。

## <a name="set-up-the-service-fabric-cli"></a>Service Fabric CLI のセットアップ

[Service Fabric CLI](service-fabric-cli.md) には、クラスターやアプリケーションなどの Service Fabric エンティティを操作するコマンドが含まれています。 CLI をインストールするには、[Service Fabric CLI](service-fabric-cli.md) に関するページの手順に従ってください。


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>コンテナーとゲスト実行可能ファイルを作成するための Yeoman ジェネレーターをセットアップする
Service Fabric には、ターミナルから Yeoman テンプレート ジェネレーターを使って Service Fabric アプリケーションを作成できるスキャフォールディング ツールが用意されています。 次の手順に従って、Service Fabric Yeoman テンプレート ジェネレーターを設定します。

1. マシンに Node.js と npm をインストールします。

    ```bash
    sudo add-apt-repository "deb https://deb.nodesource.com/node_8.x $(lsb_release -s -c) main"
    sudo apt-get update
    sudo apt-get install nodejs
    ```
2. npm からマシンに [Yeoman](https://yeoman.io/) テンプレート ジェネレーターをインストールします。

    ```bash
    sudo npm install -g yo
    ```
3. npm から Service Fabric Yeo のコンテナー ジェネレーターとゲスト実行可能ジェネレーターをインストールします。

    ```bash
    sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
    sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
    ```

ジェネレーターのインストール後、`yo azuresfguest` または `yo azuresfcontainer` を実行して、ゲスト実行可能ファイルまたはコンテナー サービスを作成します。

## <a name="set-up-net-core-20-development"></a>.NET Core 2.0 開発環境をセットアップする

[.NET Core 2.0 SDK for Ubuntu](https://www.microsoft.com/net/core#linuxubuntu) をインストールして、[C# Service Fabric アプリケーションの作成](service-fabric-create-your-first-linux-application-with-csharp.md)を開始します。 NuGet.org は、現在プレビュー段階の .NET Core 2.0 Service Fabric アプリケーション用のパッケージをホストします。

## <a name="set-up-java-development"></a>Java 開発をセットアップする

Java を使用して Service Fabric サービスをビルドするには、Gradle をインストールしてビルド タスクを実行します。 次のコマンドを実行して、Gradle をインストールします。 Service Fabric Java ライブラリが Maven から取り込まれます。


* Ubuntu

    ```bash
    curl -s https://get.sdkman.io | bash
    sdk install gradle 5.1
    ```

* Red Hat Enterprise Linux 7.4 (Service Fabric プレビュー サポート)

  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```

さらに、Java 実行可能ファイル用の Service Fabric Yeo ジェネレーターをインストールする必要があります。 [Yeoman がインストールされている](#set-up-yeoman-generators-for-containers-and-guest-executables) ことを確認してから、次のコマンドを実行します。

  ```bash
  npm install -g generator-azuresfjava
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>Eclipse プラグインをインストールする (省略可能)

Eclipse IDE for Java Developers または Eclipse IDE for Java EE Developers 内から Service Fabric 用 Eclipse プラグインをインストールできます。 Eclipse を使用すると、Service Fabric Java アプリケーションのほかに、Service Fabric ゲスト実行可能アプリケーションと Service Fabric コンテナー アプリケーションを作成できます。

> [!IMPORTANT]
> Service Fabric プラグインには、Eclipse Neon またはそれ以降のバージョンが必要です。 Eclipse のバージョンを確認する方法については、この注の後の手順を参照してください。 以前のバージョンの Eclipse がインストールされている場合は、より新しいバージョンを [Eclipse サイト](https://www.eclipse.org)からダウンロードすることができます。 Eclipse の既存のインストールの上にインストールする (上書きする) ことはお勧めしません。 インストーラーを実行する前に削除するか、新しいバージョンを別のディレクトリにインストールします。
> 
> Ubuntu では、パッケージ インストーラー (`apt` または `apt-get`) を使用するのではなく、Eclipse サイトから直接インストールすることをお勧めします。 そうすることで、Eclipse の最新バージョンを確実に入手することができます。 Eclipse IDE for Java Developers または Eclipse IDE for Java EE Developers をインストールできます。

1. Eclipse で、Eclipse Neon 以降および Buildship バージョン 2.2.1 以降がインストールされていることを確認します。 **[ヘルプ]**  >  **[Eclipse について]**  >  **[インストール詳細]** の順に選択して、インストールされたコンポーネントのバージョンを確認します。 Buildship は、[Eclipse Buildship:Gradle 用の Eclipse プラグイン][buildship-update]に関するページの手順に従って更新できます。

2. **[Help]\(ヘルプ\)**  >  **[Install New Software]\(新しいソフトウェアのインストール\)** の順に選択して、Service Fabric プラグインをインストールします。

3. **[Work with]\(作業対象\)** ボックスに「 **https://dl.microsoft.com/eclipse** 」と入力します。

4. **[追加]** を選択します。

    ![[利用可能なソフトウェア] ページ][sf-eclipse-plugin]

5. **[ServiceFabric]** プラグインを選択し、 **[次へ]** を選択します。

6. インストール手順を実行します。 次に、使用許諾契約に同意します。

Service Fabric Eclipse プラグインを既にインストールしてある場合は、最新バージョンを使用していることを確認してください。 確認するには、 **[ヘルプ]**  >  **[Eclipse について]**  >  **[インストール詳細]** の順に選択します。 次に、インストールされているプラグインの一覧で Service Fabric を探します。新しいバージョンが使用できる場合は **[更新]** を選択します。

詳細については、「[Eclipse Java アプリケーション開発用の Service Fabric プラグイン](service-fabric-get-started-eclipse.md)」を参照してください。

## <a name="update-the-sdk-and-runtime"></a>SDK とランタイムを更新する

SDK とランタイムを最新バージョンに更新するには、次のコマンドを実行します。

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Maven からの Java SDK バイナリを更新するには、``build.gradle`` ファイルで、最新バージョンが参照されるように、対応するバイナリのバージョン情報を更新する必要があります。 具体的なバージョンの更新箇所については、[Service Fabric 入門用サンプル](https://github.com/Azure-Samples/service-fabric-java-getting-started)にある任意の ``build.gradle`` ファイルを参照してください。

> [!NOTE]
> 上記のパッケージを更新すると、ローカルの開発クラスターが停止する可能性があります。 この記事の手順に従って、アップグレード後にローカル クラスターを再起動してください。

## <a name="remove-the-sdk"></a>SDK を削除する
Service Fabric SDK を削除するには、次のコマンドを実行します。

* Ubuntu

    ```bash
    sudo apt-get remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    sudo apt-get install -f
    ```


* Red Hat Enterprise Linux 7.4 (Service Fabric プレビュー サポート)

    ```bash
    sudo yum remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    ```

## <a name="next-steps"></a>次の手順

* [Yeoman を使用して Linux で最初の Service Fabric Java アプリケーションを作成してデプロイする](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse 用の Service Fabric プラグインを使用して Linux で最初の Service Fabric Java アプリケーションを作成してデプロイする](service-fabric-get-started-eclipse.md)
* [Linux で最初の CSharp アプリケーションを作成する](service-fabric-create-your-first-linux-application-with-csharp.md)
* [OSX で開発環境を準備する](service-fabric-get-started-mac.md)
* [Windows で Linux 開発環境を準備する](service-fabric-local-linux-cluster-windows.md)
* [Service Fabric CLI を使用してアプリケーションを管理する](service-fabric-application-lifecycle-sfctl.md)
* [Service Fabric における Windows と Linux の違い](service-fabric-linux-windows-differences.md)
* [Service Fabric CLI の概要](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
