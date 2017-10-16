---
title: "Azure Service Fabric を使用するための開発環境を Mac OS X にセットアップする | Microsoft Docs"
description: "ランタイム、SDK、およびツールをインストールし、ローカル開発クラスターを作成します。 このセットアップを完了すると、Mac OS X でアプリケーションを構築する準備が整います。"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2017
ms.author: saysa
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: c447a92e076bacc9b208b837493400b70cd067e1
ms.contentlocale: ja-jp
ms.lasthandoff: 09/26/2017

---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Mac OS X で開発環境をセットアップする
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Linux クラスターで実行される Service Fabric アプリケーションを Mac OS X を使用して構築できます。この記事では、開発用に Mac をセットアップする方法について説明します。

## <a name="prerequisites"></a>前提条件
Service Fabric は、OS X ではネイティブに実行されません。Microsoft では、ローカルの Service Fabric クラスターを実行できるように、Vagrant と VirtualBox を使用して事前に構成済みの Ubuntu 仮想マシンを用意しています。 作業を開始する前に、以下を行う必要があります。

* [Vagrant (v1.8.4 以降)](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

>[!NOTE]
> 相互にサポートされている Vagrant と VirtualBox のバージョンを使用する必要があります。 サポートされていない VirtualBox バージョンでは、Vagrant の動作が不安定になる可能性があります。
>

## <a name="create-the-local-vm"></a>ローカル VM を作成する
5 ノード構成の Service Fabric クラスターを保持するローカル VM を作成するには、次の手順を実行します。

1. `Vagrantfile` リポジトリを複製します。

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
    この手順を実行すると、VM 構成と VM のダウンロード元の場所が含まれた `Vagrantfile` ファイルがダウンロードされます。  ファイルはストック Ubuntu イメージを指します。

2. リポジトリのローカル クローンに移動します。

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (省略可能) 既定の VM 設定を変更します。

    既定では、ローカル VM は次のように構成されています。

   * メモリの割り当て 3 GB
   * IP 192.168.50.50 で構成されたプライベート ホスト ネットワーク。Mac ホストからのトラフィックに対してパススルーが有効化されています。

     これらの設定のどちらかを変更することも、`Vagrantfile` で VM に他の構成を追加することもできます。 構成オプションの詳細な一覧については、 [Vagrant のドキュメント](http://www.vagrantup.com/docs) をご覧ください。
4. VM の作成

    ```bash
    vagrant up
    ```


5. VM にログインして Service Fabric SDK をインストールする

    ```bash
    vagrant ssh
    ```

   「[SDK のインストール](service-fabric-get-started-linux.md)」の説明に従って SDK をインストールします。  Service Fabric ランタイムと共通 SDK を sfctl CLI と共にインストールする場合に備え以下のスクリプトが用意されています。 スクリプトの実行では、インストールされているすべてのソフトウェアのライセンスを読んで同意していることが前提となります。

    ```bash
    sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
    ```

5.  Service Fabric クラスターを起動する

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    >[!TIP]
    > VM のダウンロードに長い時間がかかる場合、wget または curl を使用してダウンロードできます。そのほか、ブラウザーを使い、`Vagrantfile` ファイルの **config.vm.box_url** で指定されたリンク先に移動してダウンロードすることもできます。 VM をローカルにダウンロードした後、イメージのダウンロード先のローカル パスを指すように、`Vagrantfile` を編集します。 たとえば、イメージのダウンロード先が /home/users/test/azureservicefabric.tp8.box であった場合、**config.vm.box_url** をこのパスに設定します。
    >

5. クラスターが正しくセットアップされていることをテストするために、Service Fabric Explorer (http://192.168.50.50:19080/Explorer) に移動します (プライベート ネットワーク IP アドレスが既定値のままであると仮定しています)。

    ![Service Fabric Explorer viewed from the host Mac][sfx-mac]

## <a name="install-the-necessary-java-artifacts-on-vagrant-to-use-service-fabric-java-programming-model"></a>必要な Java アーティファクトを Vagrant にインストールして Service Fabric Java プログラミング モデルを使用する

Service Fabric サービスを Java で構築するには、ビルド タスクを実行するための Gradle と共に、JDK 1.8 がインストールされている必要があります。 次のスニペットで Open JDK 1.8 と Gradle をインストールしてください。 Service Fabric Java ライブラリが Maven から取り込まれます。

  ```bash
  vagrant ssh
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
```

## <a name="set-up-the-service-fabric-cli"></a>Service Fabric CLI のセットアップ

[Service Fabric CLI](service-fabric-cli.md) には、クラスターやアプリケーションなどの Service Fabric エンティティを操作するコマンドが含まれています。 Python がベースになっているため、あらかじめ Python と pip がインストールされていることを確認してから、次のコマンドを実行してください。

```bash
pip install sfctl
```

## <a name="create-application-on-mac-using-yeoman"></a>Mac 上で Yeoman を使ってアプリケーションを作成する
Service Fabric には、ターミナルから Yeoman テンプレート ジェネレーターを使って Service Fabric アプリケーションを作成できるスキャフォールディング ツールが用意されています。 以下の手順に従って、ご利用のマシンに Service Fabric Yeoman テンプレート ジェネレーターをセットアップしてください。

1. ご使用の Mac に Node.js と NPM がインストールされている必要があります。 インストールされていない場合は、次のコマンドで Homebrew を使って Node.js と NPM をインストールしてください。 Mac にインストールされている Node.js と NPM のバージョンは、``-v`` オプションを使ってチェックできます。

  ```bash
  brew install node
  node -v
  npm -v
  ```
2. NPM からマシンに [Yeoman](http://yeoman.io/) テンプレート ジェネレーターをインストールします。

  ```bash
  npm install -g yo
  ```
3. 概要[ドキュメント](service-fabric-get-started-linux.md)の手順に従って、使用する Yeoman ジェネレーターをインストールします。 Yeoman を使って Service Fabric アプリケーションを作成するには、次の手順に従います。

  ```bash
  npm install -g generator-azuresfjava       # for Service Fabric Java Applications
  npm install -g generator-azuresfguest      # for Service Fabric Guest executables
  npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
  ```
4. Service Fabric Java アプリケーションを Mac で構築するには、JDK 1.8 と Gradle がマシンにインストールされている必要があります。

## <a name="set-up-net-core-20-development"></a>.NET Core 2.0 開発環境をセットアップする

[.NET Core 2.0 SDK for Mac](https://www.microsoft.com/net/core#macos) をインストールして、[C# Service Fabric アプリケーションの作成](service-fabric-create-your-first-linux-application-with-csharp.md)を開始します。 .NET Core 2.0 Service Fabric アプリケーション用のパッケージは、現在プレビューの段階で NuGet.org でホストされています。


## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Eclipse Neon 用の Service Fabric プラグインをインストールする

Service Fabric には、Java サービスの作成、構築、デプロイのプロセスを簡略化できる、**Java IDE 用の Eclipse Neon** 向けのプラグインが用意されています。 Service Fabric Eclipse プラグインのインストールまたは更新については、こちらの概要の[ドキュメント](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon)に記載されたインストール手順を利用できます。

>[!TIP]
> 既定では、生成されたアプリケーションの ``Local.json`` にある ``Vagrantfile`` で指定されている既定の IP がサポートされます。 これを変更し、別の IP で Vagrant をデプロイする場合は、アプリケーションの ``Local.json`` で対応する IP も更新してください。

## <a name="next-steps"></a>次のステップ
<!-- Links -->
* [Yeoman を使用して Linux で最初の Service Fabric Java アプリケーションを作成してデプロイする](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse 用の Service Fabric プラグインを使用して Linux で最初の Service Fabric Java アプリケーションを作成してデプロイする](service-fabric-get-started-eclipse.md)
* [Azure Portal で Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-portal.md)
* [Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)
* [Service Fabric アプリケーション モデルを理解する](service-fabric-application-model.md)
* [Service Fabric CLI を使用してアプリケーションを管理する](service-fabric-application-lifecycle-sfctl.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

