---
title: 'クイックスタート: Azure Service Fabric 上に Spring Boot アプリを作成する'
description: このクイック スタートでは、Spring Boot サンプル アプリケーションを使用して、Azure Service Fabric 用の Spring Boot アプリケーションをデプロイします。
ms.topic: quickstart
ms.date: 01/29/2019
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 84ce5920af95113801f468e3149421f3b9bd8901
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91530002"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>クイックスタート: Azure Service Fabric 上に Java Spring Boot アプリをデプロイする

このクイックスタートでは、Linux または MacOS で使い慣れたコマンドライン ツールを使用して、Java Spring Boot アプリケーションを Azure Service Fabric にデプロイします。 Azure Service Fabric は、マイクロサービスとコンテナーのデプロイと管理を行うための分散システム プラットフォームです。 

## <a name="prerequisites"></a>前提条件

#### <a name="linux"></a>[Linux](#tab/linux)

- [Java 環境](./service-fabric-get-started-linux.md#set-up-java-development)と [Yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Service Fabric SDK および Service Fabric コマンド ライン インターフェイス (CLI)](./service-fabric-get-started-linux.md#installation-methods)
- [Git](https://git-scm.com/downloads)

#### <a name="macos"></a>[MacOS](#tab/macos)

- [Java 環境と Yeoman](./service-fabric-get-started-mac.md#create-your-application-on-your-mac-by-using-yeoman)
- [Service Fabric SDK および Service Fabric コマンド ライン インターフェイス (CLI)](./service-fabric-cli.md#cli-mac)
- [Git](https://git-scm.com/downloads)

--- 

## <a name="download-the-sample"></a>サンプルのダウンロード

ターミナル ウィンドウで次のコマンドを実行して、Spring Boot の [Getting Started](https://github.com/spring-guides/gs-spring-boot) サンプル アプリをローカル コンピューターに複製します。

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Spring Boot アプリケーションの構築 
*gs-spring-boot/complete* ディレクトリ内で、次のコマンドを実行してアプリケーションをビルドします。 

```bash
./gradlew build
``` 

## <a name="package-the-spring-boot-application"></a>Spring Boot アプリケーションのパッケージ作成 
1. 複製の *gs-spring-boot* ディレクトリ内で `yo azuresfguest` コマンドを実行します。 

1. 各プロンプトで次の情報を入力します。

    ![Spring Boot の Yeoman エントリ](./media/service-fabric-quickstart-java-spring-boot/yeoman-entries-spring-boot.png)

1. *SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code* フォルダーで、*entryPoint.sh* という名前のファイルを作成します。*entryPoint.sh* ファイルに次のコードを追加します。 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar *spring-boot*.jar
    ```

1. **Endpoints** リソースを *gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml* ファイルに追加する

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    これで、*ServiceManifest.xml* は次のようになります。 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

この段階では、Azure Service Fabric にデプロイできる Spring Boot の Getting Started サンプルのための Azure Service Fabric アプリケーションを作成しました。

## <a name="run-the-application-locally"></a>ローカルでアプリケーションを実行する

1. 次のコマンドを実行して、Ubuntu マシン上でローカル クラスターを開始します。

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Mac を使用する場合、Docker イメージからローカル クラスターを開始します ([前提条件](./service-fabric-get-started-mac.md#create-a-local-container-and-set-up-service-fabric)に従って Mac のローカル クラスターをセットアップした場合)。 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    ローカル クラスターの起動には、一定の時間がかかります。 クラスターが完全に起動されたことを確認するには、`http://localhost:19080` で Service Fabric Explorer にアクセスします。 5 つの正常なノードは、ローカル クラスターが起動され、実行されていることを示します。 
    
    ![Service Fabric Explorer が正常なノードを表示する](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-healthy-nodes.png)

1. *gs-spring-boot/SpringServiceFabric* フォルダーを開きます。
1. 次のコマンドを実行して、ローカル クラスターに接続します。

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. *install.sh* スクリプトを実行します。

    ```bash
    ./install.sh
    ```

1. 使い慣れた Web ブラウザーを開き、`http://localhost:8080` に接続してアプリケーションにアクセスします。

    ![Spring Boot Service Fabric サンプル](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

これで、Azure Service Fabric クラスターに展開された Spring Boot アプリケーションにアクセスできるようになりました。

詳細については、Spring Web サイトで Spring Boot の [Getting Started](https://spring.io/guides/gs/spring-boot/) サンプルを参照してください。

## <a name="scale-applications-and-services-in-a-cluster"></a>クラスター内のアプリケーションとサービスをスケールする

サービスは、その負荷の変化に対応するために、クラスターで簡単にスケーリングできます。 サービスをスケールするには、クラスターで実行されるインスタンスの数を変更します。 サービスをスケーリングする方法は多数あります。たとえば、Service Fabric CLI (sfctl) のスクリプトやコマンドを使用できます。 次の手順では、Service Fabric Explorer を使用します。

Service Fabric Explorer は、すべての Service Fabric クラスターで動作し、ブラウザーからクラスターの HTTP 管理ポート (19080) にアクセスして利用することができます (例: `http://localhost:19080`)。

Web フロントエンド サービスをスケーリングするには、以下を実行します。

1. クラスターで Service Fabric Explorer を開きます (例: `http://localhost:19080`)。
1. ツリービューで **fabric:/SpringServiceFabric/SpringGettingStarted** ノードの横にある省略記号 (**...**) を選択し、**[サービスのスケール]** を選択します。

    ![Service Fabric Explorer スケーリング サービスのサンプル](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-scale-sample.png)

    次に、スケーリングするサービスのインスタンス数を選択します。

1. 数値を **3** に変更し、**[サービスのスケール]** を選択します。

    または、次のコマンドラインを使用してサービスを拡張することもできます。

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. ツリー ビューの **fabric:/SpringServiceFabric/SpringGettingStarted** ノードを選択し、パーティション ノード (GUID で表されます) を展開します。

    ![Service Fabric Explorer スケーリング サービスの完了](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-partition-node.png)

    サービスには 3 つのインスタンスがあり、各インスタンスを実行しているノードがツリー ビューに表示されます。

この簡単な管理タスクを通じて、フロントエンド サービスでユーザー負荷を処理するためのリソースが 2 倍になりました。 実行するサービスの信頼性を高めるために、サービスのインスタンスを複数用意する必要はないことに注目してください。 サービスで障害が発生した場合、Service Fabric によって新しいサービス インスタンスがクラスターで実行されます。

## <a name="fail-over-services-in-a-cluster"></a>クラスターのフェールオーバー サービス

サービスのフェールオーバーを示すために、Service Fabric Explorer を使用して、ノードの再起動をシミュレートします。 サービス インスタンスが 1 つのみ実行されていることを確認してください。

1. クラスターで Service Fabric Explorer を開きます (例: `http://localhost:19080`)。
1. サービスのインスタンスを実行しているノードの横にある省略記号 (**...**) を選択し、ノードを再起動します。

    ![Service Fabric Explorer でのノードの再起動](./media/service-fabric-quickstart-java-spring-boot/service=fabric-explorer-restart=node.png)
1. サービスのインスタンスは別のノードに移動され、アプリケーションにはダウンタイムは発生しません。

    ![Service Fabric Explorer でのノードの再起動の成功](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-service-moved.png)

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、次の方法について説明しました。

* Spring Boot アプリケーションを Service Fabric にデプロイする
* アプリケーションをローカル クラスターにデプロイする
* 複数のノードにアプリケーションをスケールアウトする
* 可用性に影響を与えることなくサービスのフェールオーバーを実行する

Service Fabric で Java アプリを操作する方法を学習するには、Java アプリのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Java アプリのデプロイ](./service-fabric-tutorial-create-java-app.md)
