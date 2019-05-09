---
title: 既存の MongoDB アプリケーションを MongoDB 用 Azure Cosmos DB API および Open Service Broker for Azure (OSBA) と統合する
description: この記事では、Open Service Broker for Azure (OSBA) を使用して既存の Java および MongoDB アプリケーションを MongoDB 用 Azure Cosmos DB API と統合する方法について説明します。
services: azure-dev-spaces
author: zr-msft
manager: jeconnoc
ms.service: azure-dev-spaces
ms.topic: article
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, Open Service Broker, Open Service Broker for Azure
ms.openlocfilehash: 46fa5564e5dd3429f812b263295044d867a8511c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57991977"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>既存の MongoDB アプリケーションを MongoDB 用 Azure Cosmos DB API および Open Service Broker for Azure (OSBA) と統合する

Azure Cosmos DB は、グローバル分散型のマルチモデル データベース サービスです。 また、MongoDB 用など、いくつかの NoSQL API とのワイヤ プロトコルの互換性も提供されます。 MongoDB 用 Cosmos DB API を使用すると、アプリケーションのデータベース ドライバーまたは実装を変更しなくても、既存の MongoDB アプリケーションで Cosmos DB を使用できます。 また、Open Service Broker for Azure を使用して Cosmos DB サービスをプロビジョニングすることもできます。

この記事では、MongoDB データベースを使用する既存の Java アプリケーションを選択し、Open Service Broker for Azure を使用して Cosmos DB データベースを使用するように更新します。

## <a name="prerequisites"></a>前提条件

次に進む前に、以下を満たしておく必要があります。
    
* [Azure Kubernetes Service クラスター](kubernetes-walkthrough.md)を作成してあること。
* [お使いの AKS クラスターに Open Service Broker for Azure をインストールして構成](integrate-azure.md)してあること。 
* [Service Catalog CLI](https://svc-cat.io/docs/install/) をインストールし、`svcat` コマンドを実行するように構成してあること。
* 既存の [MongoDB](https://www.mongodb.com/) データベースがあること。 たとえば、[開発用コンピューター](https://docs.mongodb.com/manual/administration/install-community/)または [Azure VM](../virtual-machines/linux/install-mongodb.md) で実行されている MongoDB を使用できます。
* MongoDB データベースに接続してクエリを実行する手段があること ([Mongo シェル](https://docs.mongodb.com/manual/mongo/) など)。

## <a name="get-application-code"></a>アプリケーションのコードを入手する
    
この記事では、[Cloud Foundry の Spring Music サンプル アプリケーション](https://github.com/cloudfoundry-samples/spring-music)を使用して、MongoDB データベースを使用するアプリケーションのデモを行います。
    
GitHub からアプリケーションを複製して、そのディレクトリに移動します。
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>MongoDB データベースを使用するようにアプリケーションを準備する

Spring Music サンプル アプリケーションでは、データソース用にさまざまなオプションが提供されています。 この記事では、既存の MongoDB データベースを使用するように構成します。 

次の YAML を *src/main/resources/application.yml* の最後に追加します。 これを追加すると、*mongodb* という名前のプロファイルが作成されて、URI とデータベース名が構成されます。 URI を、お使いの既存の MongoDB データベースへの接続情報に置き換えてください。 ユーザー名とパスワードが含まれている URI をこのファイルに直接追加するのは、**開発目的の場合のみ**であり、**バージョン コントロールには追加しないでください**。

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



アプリケーションを起動し、*mongodb* プロファイルを使用するように指定すると、MongoDB データベースに接続され、それを使用してアプリケーションのデータが格納されます。

アプリケーションをビルドするには:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

アプリケーションを起動し、*mongodb* プロファイルを使用するよう指定します。

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

ブラウザーで `http://localhost:8080` に移動します。

![Spring Music アプリと既定のデータ](media/music-app.png)

アプリケーションには、いくつか[既定のデータ](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json)が設定されていることに注意してください。 アプリケーションと対話して、既存のアルバムをいくつか削除し、新しいアルバムをいくつか作成します。

アプリケーションで MongoDB データベースが使用されていることは、データベースに接続し、*musicdb* データベースに対するクエリを実行することによって確認できます。

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

上の例では、[Mongo シェル](https://docs.mongodb.com/manual/mongo/)を使用して、MongoDB データベースに接続し、クエリを実行しています。 アプリケーションを停止、再起動して、ブラウザーで再びアクセスすることにより、変更が保存されているのを確認することもできます。 行った変更が保持されていることに注意してください。


## <a name="create-a-cosmos-db-database"></a>Cosmos DB データベースを作成する

Open Service Broker を使用して Azure で Cosmos DB データベースを作成するには、`svcat provision` コマンドを使用します。

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

上のコマンドでは、Azure の *eastus* リージョンの *MyResourceGroup* リソース グループに、Cosmos DB データベースがプロビジョニングされます。 *resourceGroup* や *location* など、Azure 固有の JSON パラメーターについて詳しくは、[Cosmos DB モジュールのリファレンス ドキュメント](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3)をご覧ください。

お客様のデータベースのプロビジョニングが完了したことを確認するには、`svcat get instance` コマンドを使用します。

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

*[STATUS]* の下に *[Ready]* と表示されていれば、データベースは準備ができています。

データベースのプロビジョニングが完了したら、そのメタデータを [Kubernetes のシークレット](https://kubernetes.io/docs/concepts/configuration/secret/)にバインドする必要があります。 シークレットへのバインドが済むと、他のアプリケーションでそのデータにアクセスできるようになります。 データベースのメタデータをシークレットにバインドするには、`svcat bind` コマンドを使用します。

```cmd
$ svcat bind musicdb

  Name:        musicdb
  Namespace:   default
  Status:
  Secret:      musicdb
  Instance:    musicdb

Parameters:
  No parameters defined
```


## <a name="use-the-cosmos-db-database-with-your-application"></a>アプリケーションで Cosmos DB データベースを使用する

アプリケーションで Cosmos DB データベースを使用するには、データベースに接続するための URI を知る必要があります。 この情報を取得するには、`kubectl get secret` コマンドを使用します。

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

上のコマンドでは、*musicdb* のシークレットが取得されて、URI のみが表示されます。 シークレットは base64 形式で格納されているため、上記のコマンドではそのデコードも行われます。

Cosmos DB データベースの URI を使用するように *src/main/resources/application.yml* を更新します。

```yaml
...
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
      database: musicdb
```

ユーザー名とパスワードが含まれている URI でこのファイルを直接更新するのは、**開発目的の場合のみ**であり、**バージョン コントロールには追加しないでください**。

アプリケーションをリビルドして起動し、Cosmos DB データベースの使用を開始します。

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

アプリケーションではまだ *mongodb* プロファイルと *mongodb://* で始まる URI を使用して Cosmos DB データベースに接続していることに注意してください。 [MongoDB 用 Azure Cosmos DB API](../cosmos-db/mongodb-introduction.md) ではこの互換性が提供されています。 これにより、アプリケーションは MongoDB データベースを使用しているかのように動作し続けますが、実際には Cosmos DB を使用しています。

ブラウザーで `http://localhost:8080` に移動します。 既定のデータが復元されていることに注意してください。 アプリケーションと対話して、既存のアルバムをいくつか削除し、新しいアルバムをいくつか作成します。 アプリケーションを停止、再起動して、ブラウザーで再びアクセスすることにより、変更が保存されているのを確認できます。 行った変更が保持されていることに注意してください。 変更は、Open Service Broker for Azure を使用して作成した Cosmos DB に保存されます。


## <a name="run-your-application-on-your-aks-cluster"></a>AKS クラスターでアプリケーションを実行する

[Azure Dev Spaces](../dev-spaces/azure-dev-spaces.md) を使用して、AKS クラスターにアプリケーションをデプロイすることができます。 Azure Dev Spaces は、Dockerfile や Helm チャートなどの成果物を生成し、AKS にアプリケーションをデプロイして実行するのに役立ちます。

AKS クラスターで Azure Dev Spaces を有効にするには:

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Azure Dev Spaces ツールを使用して、AKS で実行するようにアプリケーションを準備します。

```cmd
azds prep --public
```

このコマンドでは、Helm チャートである *charts/* フォルダーなどの複数の成果物が、プロジェクトのルートに生成されます。 このコマンドではこの特定のプロジェクトに対する *Dockerfile* を生成できないので、自分で作成する必要があります。

次のような内容で *Dockerfile* という名前のファイルを、プロジェクトのルートに作成します。

```Dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

さらに、*azds.yaml* で *configurations.develop.build* プロパティを *false* に更新する必要があります。
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

また、*charts/spring-music/templates/deployment.yaml* で *containerPort* 属性を *8080* に更新する必要もあります。

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
```

アプリケーションを AKS にデプロイするには:

```cmd
$ azds up

Using dev space 'default' with target 'MyAKS'
Synchronizing files...1m 18s
Installing Helm chart...5s
Waiting for container image build...23s
Building container image...
Step 1/5 : FROM openjdk:8-jdk-alpine
Step 2/5 : EXPOSE 8080
Step 3/5 : WORKDIR /app
Step 4/5 : COPY build/libs/spring-music-1.0.jar .
Step 5/5 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
Built container image in 21s
Waiting for container...8s
Service 'spring-music' port 'http' is available at http://spring-music.1234567890abcdef1234.eastus.aksapp.io/
Service 'spring-music' port 8080 (TCP) is available at http://localhost:57892
press Ctrl+C to detach
...
```

ログに表示されている URL に移動します。 前の例では、*http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* を使用します。 

アプリケーションで変更内容が表示されることを確認します。

## <a name="next-steps"></a>次の手順

この記事では、MongoDB を使用している既存のアプリケーションを、MongoDB 用 Cosmos DB API を使用するように更新する方法を説明しました。 また、この記事では、Open Service Broker for Azure を使用して Cosmos DB サービスをプロビジョニングし、そのアプリケーションを Azure Dev Spaces で AKS にデプロイする方法についても説明しました。

Cosmos DB、Open Service Broker for Azure、Azure Dev Spaces について詳しくは、以下の記事をご覧ください。
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Open Service Broker for Azure](https://osba.sh)
* [Dev Spaces を使用した開発](../dev-spaces/azure-dev-spaces.md)
