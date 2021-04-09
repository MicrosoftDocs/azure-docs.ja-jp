---
title: C# を使用して Linux 上で最初の Azure Service Fabric アプリを作成する
description: C# と .NET Core 2.0 を使用して、Service Fabric アプリケーションを作成し、デプロイする方法について説明します。
ms.topic: conceptual
ms.date: 04/11/2018
ms.openlocfilehash: 556d8dee4efd492fa98755f1ffd1cdc1c9887856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "82193651"
---
# <a name="create-your-first-azure-service-fabric-application"></a>最初の Azure Service Fabric アプリケーションを作成する
> [!div class="op_single_selector"]
> * [Java - Linux (プレビュー)](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux (プレビュー)](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric では、.NET Core と Java の両方で Linux 上のサービスを構築するための SDK を提供しています。 このチュートリアルでは Linux 用アプリケーションを作成し、.NET Core 2.0 で C# を使用してサービスを構築する方法について説明します。

## <a name="prerequisites"></a>前提条件
作業を開始する前に、 [Linux 開発環境がセットアップ](service-fabric-get-started-linux.md)されていることを確認してください。 Mac OS X を使用している場合は、 [Vagrant を使用して仮想マシンに Linux ワンボックス環境を設定](service-fabric-get-started-mac.md)します。

また、[Service Fabric CLI](service-fabric-cli.md) をインストールしてください。

### <a name="install-and-set-up-the-generators-for-c"></a>C# のジェネレーターのインストールとセットアップ
Service Fabric には、ターミナルから Yeoman テンプレート ジェネレーターを使って Service Fabric アプリケーションを作成できるスキャフォールディング ツールが用意されています。 次の手順に従って、C# の Service Fabric Yeoman テンプレート ジェネレーターを設定します。

1. マシンに nodejs と NPM をインストールします。

   ```bash
   curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash 
   nvm install node 
   ```
2. NPM からマシンに [Yeoman](https://yeoman.io/) テンプレート ジェネレーターをインストールします。

   ```bash
   npm install -g yo
   ```
3. NPM から Service Fabric Yeoman C# アプリケーション ジェネレーターをインストールします。

   ```bash
   npm install -g generator-azuresfcsharp
   ```

## <a name="create-the-application"></a>アプリケーションを作成する
Service Fabric のアプリケーションには、アプリケーションの機能を提供する際にそれぞれ特定の役割を果たすサービスを 1 つ以上含めることができます。 直前の手順でインストールした C# 用の Service Fabric [Yeoman](https://yeoman.io/) ジェネレーターを使用すると、初めてサービスを作成したり、後で追加したりする作業が簡単になります。 Yeoman を使用して、単一のサービスを持つアプリケーションを作成しましょう。

1. ターミナルで、コマンド「`yo azuresfcsharp`」を入力してスキャフォールディングの構築を開始します。
2. アプリケーションに名前を付けます。
3. 最初のサービスの種類を選択し、名前を付けます。 このチュートリアルでは、"Reliable Actor Service" を選択します。

   ![C# 用の Service Fabric Yeoman ジェネレーター][sf-yeoman]

> [!NOTE]
> オプションの詳細については、「 [Service Fabric プログラミング モデルの概要](service-fabric-choose-framework.md)」を参照してください。
>
>

## <a name="build-the-application"></a>アプリケーションのビルド
Service Fabric Yeoman テンプレートには、ビルド スクリプトが含まれています。(アプリケーションのフォルダーへの移動後に) これを使用してターミナルからアプリをビルドできます。

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>アプリケーションの配置

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

アプリケーションのデプロイ後、ブラウザーを開いて `http://localhost:19080/Explorer` の [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) に移動します。 次に、**Applications** ノードを展開し、アプリケーションの種類のエントリと、その種類の最初のインスタンスのエントリができたことを確認してください。

> [!IMPORTANT]
> アプリケーションを Azure 内のセキュアな Linux クラスターにデプロイするには、Service Fabric ランタイムを使用してアプリケーションを検証するように証明書を構成する必要があります。 これにより、Reliable Services サービスが基盤の Service Fabric ランタイム API と通信できるようになります。 詳しくは、「[Reliable Services アプリを Linux クラスター上で実行するように構成する](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)」をご覧ください。  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>テスト クライアントの起動と、フェールオーバーの実行
アクター プロジェクトは、それ自体では何も行いません。 これにメッセージを送信する別のサービスまたはクライアントが必要です。 アクター テンプレートには、アクター サービスとの対話に使用できる簡単なテスト スクリプトが含まれています。

1. ウォッチ ユーティリティを使用してスクリプトを実行し、アクター サービスの出力を確認します。

   MAC OS X の場合は、次の追加コマンドを実行して、myactorsvcTestClient フォルダーをコンテナー内の同じ場所にコピーする必要があります。
    
    ```bash
    docker cp  [first-four-digits-of-container-ID]:/home
    docker exec -it [first-four-digits-of-container-ID] /bin/bash
    cd /home
    ```
    
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. Service Fabric Explorer で、アクター サービスのプライマリ レプリカをホストしているノードを見つけます。 次のスクリーンショットでは、ノード 3 です。

    ![Finding the primary replica in Service Fabric Explorer][sfx-primary]
3. 前の手順で見つけたノードをクリックし、[アクション] メニューの **[非アクティブにする (再起動)]** を選択します。 このアクションによって、ローカル クラスターのノードのいずれかが再起動され、別のノードで実行されているセカンダリ レプリカに強制的にフェールオーバーされます。 このアクションを行うときは、テスト クライアントからの出力に注意してください。また、フェールオーバーにかかわらず、カウンターが増加していることに注意してください。

## <a name="adding-more-services-to-an-existing-application"></a>既存アプリケーションへのサービスの追加

`yo` を使用して作成したアプリケーションにサービスを追加するには、次の手順を実行します。
1. ディレクトリを既存アプリケーションのルートに変更します。  たとえば、Yeoman で作成したアプリケーションが `MyApplication` の場合は、`cd ~/YeomanSamples/MyApplication` です。
2. `yo azuresfcsharp:AddService` を実行します。

## <a name="next-steps"></a>次のステップ

* [Service Fabric CLI を使用した Service Fabric クラスターの対話操作](service-fabric-cli.md)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。
* [Service Fabric CLI の概要](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
