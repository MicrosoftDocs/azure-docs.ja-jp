---
title: Azure Service Fabric Java クライアント API
description: Service Fabric クライアント REST API 仕様を使って、Service Fabric Java クライアント API を生成して使います
author: rapatchi
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: 24ee4a3d8109626bf93e01fbd10c00140762c9c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87324626"
---
# <a name="azure-service-fabric-java-client-apis"></a>Azure Service Fabric Java クライアント API

Service Fabric クライアント API を使うと、Azure、オンプレミス、ローカル開発用コンピューター、または他のクラウドの Service Fabric クラスターに、マイクロサービス ベースのアプリケーションとコンテナーをデプロイして管理できます。 この記事では、Service Fabric クライアント REST API を基にして Service Fabric Java クライアント API を生成して使う方法について説明します

## <a name="generate-the-client-code-using-autorest"></a>AutoRest を使用してクライアント コードを生成する

[AutoRest](https://github.com/Azure/autorest) は、RESTful Web サービスにアクセスするためのクライアント ライブラリを生成するツールです。 AutoRest への入力は、OpenAPI 仕様形式を使って REST API を記述している仕様です。 [Service Fabric クライアント REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) はこの仕様に従っています。

AutoRest ツールを使って Service Fabric Java クライアント コードを生成するには、以下の手順のようにします。

1. マシンに nodejs と NPM をインストールします。

    Linux を使っている場合:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Mac OS X を使っている場合:
    ```bash
    brew install node
    ```

2. NPM を使って AutoRest をインストールします。
    ```bash
    npm install -g autorest
    ```

3. ローカル コンピューターで [azure-rest-api-specs](https://github.com/Azure/azure-rest-api-specs) リポジトリをフォークして複製し、お使いのコンピューターのターミナルから複製した場所に移動します。


4. 複製したリポジトリで下記の場所に移動します。
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > クラスターのバージョンが 6.0.* ではない場合は、安定したフォルダーの適切なディレクトリに移動します。
    >   

5. 次の autorest コマンドを実行して、Java クライアント コードを生成します。
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   autorest の使い方がわかる例を次に示します。
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   次のコマンドは、``servicefabric.json`` 仕様ファイルを入力として受け取り、``java-rest-api-     code`` フォルダーに Java クライアント コードを生成して、コードを ``servicefabricrest`` 名前空間に配置します。 この手順の後、2 つのフォルダー ``models`` と ``implementation``、および ``java-rest-api-code`` フォルダーに生成された 2 つのファイル ``ServiceFabricClientAPIs.java`` と ``package-info.java`` を検索します。


## <a name="include-and-use-the-generated-client-in-your-project"></a>生成されたクライアントをプロジェクトに組み込んで使用する

1. 生成されたコードをプロジェクトに適切に追加します。 生成されたコードを使ってライブラリを作成し、このライブラリをプロジェクトに含めることをお勧めします。
2. ライブラリを作成する場合は、次の依存関係をライブラリのプロジェクトに含めます。 別のアプローチを使っている場合は、適切な依存関係を含めます。

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    たとえば、Maven ビルド システムを使っている場合は、以下のものを ``pom.xml`` ファイルに含めます。

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. 次のコードを使って RestClient を作成します。

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. クライアント オブジェクトを使い、必要に応じて適切な呼び出しを行います。 クライアント オブジェクトの使い方がわかる例をいくつか示します。 以下の API を使う前に、アプリケーション パッケージをビルドし、イメージ ストアにアップロードしてあるものとします。
    * アプリケーションのプロビジョニング
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * アプリケーションの作成

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>生成されるコードの説明
すべての API に対し、実装の 4 つのオーバーロードがあります。 省略可能なパラメーターがある場合は、さらに、これらの省略可能なパラメーターを含む 4 つのバリエーションがあります。 たとえば、API ``removeReplica`` を例にすると次のようになります。
 1. **public void removeReplica(String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout)**
    * これは、removeReplica API 呼び出しの同期バリエーションです
 2. **public ServiceFuture\<Void> removeReplicaAsync(String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout, final ServiceCallback\<Void> serviceCallback)**
    * API 呼び出しのこのバリエーションは、将来ベースの非同期プログラミングとコールバックを使いたい場合に、使うことができます
 3. **public Observable\<Void> removeReplicaAsync(String nodeName, UUID partitionId, String replicaId)**
    * API 呼び出しのこのバリエーションは、リアクティブな非同期プログラミングを使いたい場合に、使うことができます
 4. **public Observable\<ServiceResponse\<Void>> removeReplicaWithServiceResponseAsync(String nodeName, UUID partitionId, String replicaId)**
    * API 呼び出しのこのバリエーションは、リアクティブな非同期プログラミングを使い、未処理の REST 応答を扱いたい場合に、使うことができます

## <a name="next-steps"></a>次のステップ
* [Service Fabric REST API](/rest/api/servicefabric/) についての詳細
