---
title: "Java を使用して Linux で初めての Azure マイクロサービス アプリを作成する | Microsoft Docs"
description: "Java を使用して Service Fabric アプリケーションを作成およびデプロイします"
services: service-fabric
documentationcenter: java
author: seanmck
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: eedddf7a40acfba7513efd810d115f1afe2f224d
ms.lasthandoff: 03/23/2017


---
# <a name="create-your-first-azure-service-fabric-application"></a>最初の Azure Service Fabric アプリケーションを作成する
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric では、.NET Core と Java の両方で Linux 上のサービスを構築するための SDK を提供しています。 このチュートリアルでは、Linux 用アプリケーションを作成し、Java を使用するサービスを構築します。  

> [!NOTE]
> Java は優れた組み込みプログラミング言語として、Linux プレビューでのみサポートされています (Windows でのサポートは計画中)。 ただし、Java アプリケーションを含むすべてのアプリケーションは、Windows と Linux のゲスト実行可能ファイルとして、またはコンテナー内で実行できます。 詳細については、[Azure Service Fabric への既存実行ファイルのデプロイ](service-fabric-deploy-existing-app.md)に関するページと [Service Fabric へのコンテナーのデプロイ](service-fabric-deploy-container.md)に関するページを参照してください。
>

## <a name="video-tutorial"></a>ビデオ チュートリアル

次の Microsoft Virtual Academy ビデオで、Linux で Java アプリを作成する手順について説明しています。  
<center><a target="\_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-create-your-first-linux-application-with-java/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>


## <a name="prerequisites"></a>前提条件
作業を開始する前に、 [Linux 開発環境がセットアップ](service-fabric-get-started-linux.md)されていることを確認してください。 Mac OS X を使用している場合は、 [Vagrant を使用して仮想マシンに Linux ワンボックス環境を設定](service-fabric-get-started-mac.md)します。

## <a name="create-the-application"></a>アプリケーションを作成する
Service Fabric のアプリケーションには、アプリケーションの機能を提供する際にそれぞれ特定の役割を果たすサービスを 1 つ以上含めることができます。 Linux 用の Service Fabric SDK には、[Yeoman](http://yeoman.io/) ジェネレーターが含まれています。これを使用すると、初めてサービスを作成したり、後で追加したりする作業が簡単になります。 Yeoman を使用して、単一のサービスを持つアプリケーションを作成しましょう。

1. ターミナルで、「``yo azuresfjava``」と入力します。
2. アプリケーションに名前を付けます。
3. 最初のサービスの種類を選択し、名前を付けます。 このチュートリアルでは、"Reliable Actor Service" を選択します。

   ![Service Fabric Yeoman generator for Java][sf-yeoman]

> [!NOTE]
> オプションの詳細については、「 [Service Fabric プログラミング モデルの概要](service-fabric-choose-framework.md)」を参照してください。
>

## <a name="build-the-application"></a>アプリケーションのビルド
Service Fabric Yeoman テンプレートには、[Gradle](https://gradle.org/) のビルド スクリプトが含まれています。端末からアプリをビルドするために、これを使用することができます。

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>アプリケーションのデプロイ
アプリケーションがビルドされたら、Azure CLI を使用してローカル クラスターにデプロイできます。

1. ローカルの Service Fabric クラスターに接続します。

    ```bash
    azure servicefabric cluster connect
    ```

2. テンプレートに用意されているインストール スクリプトを使用してクラスターのイメージ ストアにアプリケーション パッケージをコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。

    ```bash
    ./install.sh
    ```

3. ブラウザーを開き、http://localhost:19080/Explorer の Service Fabric Explorer に移動します (Mac OS X で Vagrant を使用している場合は、localhost を VM のプライベート IP に置き換えます)。

4. Applications ノードを展開し、アプリケーションの種類のエントリと、その種類の最初のインスタンスのエントリができたことを確認します。

## <a name="start-the-test-client-and-perform-a-failover"></a>テスト クライアントの起動と、フェールオーバーの実行
アクター プロジェクトは、それ自体では何も行いません。 これにメッセージを送信する別のサービスまたはクライアントが必要です。 アクター テンプレートには、アクター サービスとの対話に使用できる簡単なテスト スクリプトが含まれています。

1. ウォッチ ユーティリティを使用してスクリプトを実行し、アクター サービスの出力を確認します。

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. Service Fabric Explorer で、アクター サービスのプライマリ レプリカをホストしているノードを見つけます。 次のスクリーンショットでは、ノード 3 です。

    ![Finding the primary replica in Service Fabric Explorer][sfx-primary]

3. 前の手順で見つけたノードをクリックし、[アクション] メニューの **[非アクティブにする (再起動)]** を選択します。 ローカル クラスターの 5 つのノードのいずれかが再起動され、別のノードで実行されているセカンダリ レプリカのいずれかに強制的にフェールオーバーされます。 このアクションを行うときは、テスト クライアントからの出力に注意してください。また、フェールオーバーにかかわらず、カウンターが増加していることに注意してください。

## <a name="create-and-deploy-an-application-with-the-eclipse-neon-plugin"></a>Eclipse Neon プラグインによるアプリケーションの作成とデプロイ

Service Fabric では、Eclipse を使用して Service Fabric Java アプリケーションの作成、ビルド、デプロイのプロビジョニングを行うこともできます。 Eclipse をインストールするときに、**Java 開発者向け Eclipse IDE** を選択します。 さらに、Service Fabric では現在、Eclipse **Neon** のプラグインがサポートされています。 [Linux で Eclipse 用の Service Fabric プラグインを使用した最初の Service Fabric Java アプリケーションの作成とデプロイ](service-fabric-get-started-eclipse.md)に関する詳細なドキュメントをご覧ください。

## <a name="adding-more-services-to-an-existing-application"></a>既存アプリケーションへのサービスの追加

### <a name="using-command-line-utility"></a>コマンド ライン ユーティリティを使用する
`yo` を使用して作成したアプリケーションにサービスを追加するには、次の手順を実行します。
1. ディレクトリを既存アプリケーションのルートに変更します。  たとえば、Yeoman で作成したアプリケーションが `MyApplication` の場合は、`cd ~/YeomanSamples/MyApplication` です。
2. `yo azuresfjava:AddService` を実行します。

### <a name="using-service-fabric-eclipse-plugin-for-java-on-linux"></a>Linux で Java 用の Service Fabric Eclipse プラグインを使用する
Service Fabric Eclipse プラグインを使用して作成された既存のアプリケーションにサービスを追加する場合、[こちら](service-fabric-get-started-eclipse.md#add-a-service-fabric-service-to-your-service-fabric-application)のドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ
* [Linux で Eclipse 用の Service Fabric プラグインを使用した最初の Service Fabric Java アプリケーションの作成とデプロイ](service-fabric-get-started-eclipse.md)
* [Service Fabric Reliable Actors の概要](service-fabric-reliable-actors-introduction.md)
* [Azure CLI を使用した Service Fabric クラスターの対話操作](service-fabric-azure-cli.md)
* [デプロイのトラブルシューティング](service-fabric-azure-cli.md#troubleshooting)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

