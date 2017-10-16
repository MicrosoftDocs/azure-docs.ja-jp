---
title: "Linux 上で Azure Service Fabric Reliable Actors Java アプリケーションを作成する | Microsoft Docs"
description: "Java Service Fabric Reliable Actors アプリケーションを 5 分で作成してデプロイする方法について説明します。"
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: 68f9492231d367b1ede6ab032ec1c66c75150957
ms.contentlocale: ja-jp
ms.lasthandoff: 09/21/2017

---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Linux で初めての Java Service Fabric Reliable Actors アプリケーションを作成する
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

このクイック スタートでは、Linux 開発環境で初めての Azure Service Fabric Java アプリケーションをほんの数分で作成できます。  作業が終了すると、単純な Java 単一サービス アプリケーションがローカル開発クラスターで実行された状態になります。  

## <a name="prerequisites"></a>前提条件
作業を開始する前に、[Linux 開発環境](service-fabric-get-started-linux.md)に Service Fabric SDK と Service Fabric CLI をインストールし、開発クラスターをセットアップします。 Mac OS X を使用している場合は、[Vagrant を使用して仮想マシンに Linux 開発環境をセットアップする](service-fabric-get-started-mac.md)ことができます。

また、[Service Fabric CLI](service-fabric-cli.md) をインストールしてください。

### <a name="install-and-set-up-the-generators-for-java"></a>Java のジェネレーターのインストールとセットアップ
Service Fabric には、ターミナルから Yeoman テンプレート ジェネレーターを使って Service Fabric Java アプリケーションを作成できるスキャフォールディング ツールが用意されています。 以下の手順に従って、ご利用のマシンに Java 用の Service Fabric Yeoman テンプレート ジェネレーターをセットアップしてください。
1. マシンに nodejs と NPM をインストールします。

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. NPM からマシンに [Yeoman](http://yeoman.io/) テンプレート ジェネレーターをインストールします。

  ```bash
  sudo npm install -g yo
  ```
3. NPM から Service Fabric Yeo Java アプリケーション ジェネレーターをインストールします。

  ```bash
  sudo npm install -g generator-azuresfjava
  ```

## <a name="create-the-application"></a>アプリケーションを作成する
Service Fabric アプリケーションには、アプリケーションの機能を提供する際にそれぞれ特定の役割を果たすサービスが 1 つ以上含まれます。 直前のセクションでインストールしたジェネレーターを使用すると、初めてサービスを作成したり、後で追加したりする作業が簡単になります。  Eclipse 用のプラグインを使用して、Service Fabric Java アプリケーションを作成、ビルド、およびデプロイすることもできます。 [Eclipse を使用した初めての Java アプリケーションの作成とデプロイ](service-fabric-get-started-eclipse.md)に関するページを参照してください。 このクイック スタートでは、Yeoman を使用して、カウンター値の格納と取得という単一のサービスを行うアプリケーションを作成します。

1. ターミナルで、「``yo azuresfjava``」と入力します。
2. アプリケーションに名前を付けます。
3. 最初のサービスの種類を選択し、名前を付けます。 このチュートリアルでは、[Reliable Actor サービス] を選択します。 他の種類のサービスの詳細については、「[Service Fabric プログラミング モデルの概要](service-fabric-choose-framework.md)」を参照してください。
   ![Java 用 Service Fabric Yeoman ジェネレーター][sf-yeoman]

## <a name="build-the-application"></a>アプリケーションのビルド
Service Fabric Yeoman テンプレートには、[Gradle](https://gradle.org/) のビルド スクリプトが含まれています。このスクリプトを使用して、端末からアプリケーションをビルドすることができます。
Service Fabric Java 依存関係は、Maven からフェッチされます。 Service Fabric Java アプリケーションをビルドして操作するには、JDK と Gradle がインストールされている必要があります。 まだインストールしていない場合は、以下を実行して、JDK (openjdk-8-jdk) と Gradle をインストールすることができます。

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

アプリケーションをビルドしてパッケージ化するには、次のコマンドを実行します。

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>アプリケーションのデプロイ
ビルドしたアプリケーションは、ローカル クラスターにデプロイできます。

1. ローカルの Service Fabric クラスターに接続します。

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. テンプレートに用意されているインストール スクリプトを実行してクラスターのイメージ ストアにアプリケーション パッケージをコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。

    ```bash
    ./install.sh
    ```

ビルドしたアプリケーションは、他のすべての Service Fabric アプリケーションと同じようにデプロイできます。 詳細な手順については、[Service Fabric CLI を使用した Service Fabric アプリケーションの管理](service-fabric-application-lifecycle-sfctl.md)についてのドキュメントを参照してください。

これらのコマンドのパラメーターは、アプリケーション パッケージ内の生成されたマニフェストで確認できます。

アプリケーションのデプロイ後、ブラウザーを開いて [http://localhost:19080/Explorer](http://localhost:19080/Explorer) の [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) に移動します。
次に、**Applications** ノードを展開し、アプリケーションの種類のエントリと、その種類の最初のインスタンスのエントリができたことを確認してください。

## <a name="start-the-test-client-and-perform-a-failover"></a>テスト クライアントの起動と、フェールオーバーの実行
アクターは単独では何も実行しません。メッセージを送信するには、別のサービスまたはクライアントが必要です。 アクター テンプレートには、アクター サービスとの対話に使用できる簡単なテスト スクリプトが含まれています。

1. ウォッチ ユーティリティを使用してスクリプトを実行し、アクター サービスの出力を確認します。  テスト スクリプトは、アクターに対して `setCountAsync()` メソッドを呼び出してカウンターを増分させ、`getCountAsync()` メソッドを呼び出して新しいカウンター値を取得し、その値をコンソールに表示します。

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. Service Fabric Explorer で、アクター サービスのプライマリ レプリカをホストしているノードを見つけます。 次のスクリーンショットでは、ノード 3 です。 プライマリ サービス レプリカは、読み取り操作と書き込み操作を処理します。  サービスの状態の変更がセカンダリ レプリカにレプリケートされます (次のスクリーンショットでは、ノード 0 のノード 1 で実行されています)。

    ![Finding the primary replica in Service Fabric Explorer][sfx-primary]

3. **[ノード]** で、前の手順で見つけたノードをクリックし、[アクション] メニューの **[非アクティブにする (再起動)]** を選択します。 この操作によって、プライマリ サービス レプリカを実行しているノードが再起動され、別のノードで実行されているセカンダリ レプリカのいずれかに強制的にフェールオーバーされます。  フェールオーバーされたセカンダリ レプリカがプライマリに昇格し、別のノードに別のセカンダリ レプリカが作成され、プライマリ レプリカによる読み取り/書き込み操作が開始されます。 ノードが再起動したら、テスト クライアントからの出力を監視して、フェールオーバーにかかわらず、カウンターが増加し続けることを確認してください。

## <a name="remove-the-application"></a>アプリケーションを削除する
テンプレートに用意されているアンインストール スクリプトを使用してアプリケーション インスタンスを削除し、アプリケーション パッケージの登録を解除して、クラスターのイメージ ストアからアプリケーション パッケージを削除します。

```bash
./uninstall.sh
```

Service Fabric explorer で、アプリケーションとアプリケーションの種類が **[アプリケーション]** ノードに表示されていないことを確認します。

## <a name="service-fabric-java-libraries-on-maven"></a>Maven 上の Service Fabric Java ライブラリ
Service Fabric Java ライブラリは、Maven でホストされてきました。 **mavenCentral** から Service Fabric Java ライブラリを使用するために、プロジェクトの ``pom.xml`` または ``build.gradle`` に依存関係を追加することができます。 

### <a name="actors"></a>アクター

アプリケーションの Service Fabric Reliable Actors サポート。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors-preview:0.12.0'
  }
  ```

### <a name="services"></a>サービス

アプリケーションの Service Fabric Reliable Services サポート。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services-preview:0.12.0'
  }
  ```

### <a name="others"></a>その他
#### <a name="transport"></a>トランスポート

Service Fabric Java アプリケーションのトランスポート層サポート。 トランスポート層でプログラムを作成する場合以外は、Reliable Actors または Service アプリケーションにこの依存関係を明示的に追加する必要はありません。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport-preview:0.12.0'
  }
  ```

#### <a name="fabric-support"></a>Fabric サポート

Service Fabric のシステム レベルのサポート。ネイティブの Service Fabric ランタイムと対話します。 Reliable Actors または Service アプリケーションにこの依存関係を明示的に追加する必要はありません。 上記の他の依存関係を取り込むときに、Maven から自動的にフェッチされます。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-preview:0.12.0'
  }
  ```

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>以前の Service Fabric Java アプリケーションを移行して Maven で使用する
最近、Service Fabric Java ライブラリが Service Fabric Java SDK から Maven リポジトリに移行されました。 Yeoman または Eclipse を使って生成する新しいアプリケーションでは、(Maven で使用可能な) 最新のプロジェクトが生成されますが、(これまで Service Fabric Java SDK を使っていた) 既存の Service Fabric のステートレスまたはアクター Java アプリケーションは、Maven からの Service Fabric Java 依存関係を使うように更新することができます。 以前のアプリケーションを Maven で使用するには、[こちら](service-fabric-migrate-old-javaapp-to-use-maven.md)に記載した手順に従ってください。

## <a name="next-steps"></a>次のステップ

* [Linux で初めての Service Fabric Java アプリケーションを作成する](service-fabric-get-started-eclipse.md)
* [Service Fabric Reliable Actors の概要](service-fabric-reliable-actors-introduction.md)
* [Service Fabric CLI を使用した Service Fabric クラスターの対話操作](service-fabric-cli.md)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。
* [Service Fabric CLI の概要](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

