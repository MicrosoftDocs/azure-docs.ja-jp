---
title: "Linux 上で Azure Service Fabric コンテナー アプリケーションを作成する | Microsoft Docs"
description: "Azure Service Fabric で初めての Linux コンテナー アプリケーションを作成します。  アプリケーションの Docker イメージをビルドして、そのイメージをコンテナー レジストリにプッシュし、Service Fabric コンテナー アプリケーションをビルドおよびデプロイします。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/28/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 8355478cb2fff3a63bc4a9b359ec8e2b132c80f6
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---

# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Linux で初めての Service Fabric コンテナー アプリケーションを作成する
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

既存のアプリケーションを Service Fabric クラスター上の Linux コンテナー内で実行する場合は、アプリケーションに変更を加える必要はありません。 この記事では、Python の [Flask](http://flask.pocoo.org/) Web アプリケーションが含まれた Docker イメージを作成し、Service Fabric クラスターにデプロイする方法について説明します。  また、[Azure Container Registry](/azure/container-registry/) を使用して、コンテナー化されたアプリケーションを共有する方法についても説明します。  この記事では、Docker の基本的な理解ができていることを前提としています。 Docker の詳細は、「[Docker Overview (Docker の概要)](https://docs.docker.com/engine/understanding-docker/)」で確認できます。

## <a name="prerequisites"></a>前提条件
* 次のものを実行している開発コンピューター。
  * [Service Fabric SDK およびツール](service-fabric-get-started-linux.md)。
  * [Docker CE for Linux](https://docs.docker.com/engine/installation/#prior-releases)。 
  * [Service Fabric CLI](service-fabric-cli.md)

* Azure Container Registry のレジストリ。Azure サブスクリプションに[コンテナー レジストリを作成します](../container-registry/container-registry-get-started-portal.md)。 

## <a name="define-the-docker-container"></a>Docker コンテナーを定義する
Docker Hub にある [Python イメージ](https://hub.docker.com/_/python/)を基にしてイメージをビルドします。 

Dockerfile で Docker コンテナーを定義します。 Dockerfile には、コンテナー内で環境をセットアップし、実行するアプリを読み込み、ポートを割り当てるための手順が含まれています。 Dockerfile はイメージを作成する `docker build` コマンドへの入力です。 

空のディレクトリを作成し、*Dockerfile* というファイル (ファイル拡張子なし) を作成します。 *Dockerfile* に次のコードを追加して変更を保存します。

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

詳細については、[Dockerfile のリファレンス](https://docs.docker.com/engine/reference/builder/)を参照してください。

## <a name="create-a-simple-web-application"></a>単純な Web アプリケーションを作成する
ポート 80 でリッスンして "Hello World!" を返す Flask Web アプリケーションを作成します。  同じディレクトリに *requirements.txt* というファイルを作成します。  次のコードを追加して変更を保存します。
```
Flask
```

さらに、*app.py* ファイルを作成して次のコードを追加します。

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    
    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="build-the-image"></a>イメージをビルドする
`docker build` コマンドを実行して、Web アプリケーションを実行するイメージを作成します。 PowerShell ウィンドウを開き、*c:\temp\helloworldapp* に移動します。 次のコマンドを実行します。

```bash
docker build -t helloworldapp .
```

このコマンドでは、Dockerfile の手順を使用して新しいイメージを構築し、"helloworldapp" と名前を付けます (-t というタグを付けます)。 イメージをビルドすると、基本イメージが Docker Hub から取得され、基本イメージ上にアプリケーションを追加する新しいイメージが作成されます。  

ビルド コマンドが完了したら、`docker images` コマンドを実行して、新しいイメージの情報を確認します。

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>ローカルでアプリケーションを実行する
コンテナー レジストリにプッシュする前に、コンテナー化されたアプリケーションがローカルで実行されていることを確認します。  

アプリケーションを実行し、コンピューターのポート 4000 をコンテナーの公開されたポート 80 にマッピングします。

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

*name* で、実行中のコンテナーに名前を付けます (コンテナー ID ではありません)。

実行中のコンテナーに接続します。  Web ブラウザーで、ポート 4000 で返された IP アドレスを開きます (たとえば " http://localhost:4000 ")。 "Hello World!" という見出しが ブラウザーに表示されます。

![Hello World!][hello-world]

コンテナーを停止するには、次を実行します。

```bash
docker stop my-web-site
```

コンテナーを開発コンピューターから削除します。

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>コンテナー レジストリにイメージをプッシュする
アプリケーションが Docker で実行されることを確認したら、イメージを Azure Container Registry のレジストリにプッシュします。

[レジストリの資格情報](../container-registry/container-registry-authentication.md)を使用してコンテナー レジストリにログインするには、`docker login` を実行します。

次の例では、Azure Active Directory [サービス プリンシパル](../active-directory/active-directory-application-objects.md)の ID とパスワードを渡します。 たとえば、自動化シナリオのために、レジストリにサービス プリンシパルを割り当てることができます。  または、レジストリのユーザー名とパスワードを使ってログインすることもできます。

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

次のコマンドでは、レジストリへの完全修飾パスを使用して、イメージのタグまたはエイリアスを作成します。 この例では、レジストリのルートが煩雑にならないように、イメージを `samples` 名前空間に配置しています。

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

コンテナー レジストリにイメージをプッシュします。

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>Yeoman で Docker イメージをパッケージ化する
Linux 用の Service Fabric SDK には、[Yeoman](http://yeoman.io/) ジェネレーターが含まれています。これを使用すると、アプリケーションを作成したり、コンテナー イメージを追加したりする作業が簡単になります。 Yeoman を使用し、Docker コンテナーを 1 つだけ含むアプリケーション *SimpleContainerApp* を作成してみましょう。

Service Fabric コンテナー アプリケーションを作成するには、ターミナル ウィンドウを開き、`yo azuresfcontainer` を実行します。  

アプリケーションに名前を付けます (例: "mycontainer")。 

コンテナー レジストリにあるコンテナー イメージの URL を指定します (例: "")。 

このイメージには定義済みのワークロード エントリ ポイントがあるため、入力コマンド (コンテナー内で実行されコマンド) を明示的に指定する必要があります。これにより、起動後にコンテナーの実行が維持されます。 

インスタンス数を "1" に指定します。

![コンテナー用 Service Fabric Yeoman ジェネレーター][sf-yeoman]

## <a name="configure-port-mapping-and-container-repository-authentication"></a>ポート マッピングとコンテナー リポジトリの認証を構成する
コンテナー化されたサービスには、通信用のエンドポイントが必要です。  ここでは、ServiceManifest.xml ファイル内の `Endpoint` にプロトコル、ポート、タイプを追加します。 この記事では、コンテナー化されたサービスがポート 4000 でリッスンします。 

```xml
<Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
```
`UriScheme` を指定すると、Service Fabric ネーム サービスを使用したコンテナー エンドポイントが自動的に登録され、検出可能性が確保されます。 ServiceManifest.xml の完全なサンプル ファイルは、この記事の最後にあります。 

ApplicationManifest.xml ファイルの `ContainerHostPolicies` 内にある `PortBinding` ポリシーを指定して、コンテナーのポートからホストへのポート マッピングを構成します。  この記事では、`ContainerPort` は 80 で (コンテナーは Dockerfile で指定されたとおりにポート 80 を公開します)、`EndpointRef` は "myserviceTypeEndpoint" です (サービス マニフェストで定義されているエンドポイントです)。  ポート 4000 で受信するサービスへの要求は、コンテナーのポート 80 にマッピングされています。  コンテナーでプライベート リポジトリを使用した認証が必要な場合は、`RepositoryCredentials` を追加します。  この記事では、myregistry.azurecr.io コンテナー レジストリのアカウント名とパスワードを追加します。 

```xml
<Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
    </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>Service Fabric アプリケーションのビルドとパッケージ化
Service Fabric Yeoman テンプレートには、[Gradle](https://gradle.org/) のビルド スクリプトが含まれています。このスクリプトを使用して、端末からアプリケーションをビルドすることができます。 アプリケーションをビルドしてパッケージ化するには、次のコマンドを実行します。

```bash
cd mycontainer
gradle
```

## <a name="deploy-the-application"></a>アプリケーションのデプロイ
アプリケーションがビルドされたら、Service Fabric CLI を使用してローカル クラスターにデプロイできます。

ローカルの Service Fabric クラスターに接続します。

```bash
sfctl cluster select --endpoint http://localhost:19080
```

テンプレートに用意されているインストール スクリプトを使用してクラスターのイメージ ストアにアプリケーション パッケージをコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。

```bash
./install.sh
```

ブラウザーを開き、http://localhost:19080/Explorer の Service Fabric Explorer に移動します (Mac OS X で Vagrant を使用している場合は、localhost を VM のプライベート IP に置き換えます)。 Applications ノードを展開し、アプリケーションの種類のエントリと、その種類の最初のインスタンスのエントリができたことを確認します。

実行中のコンテナーに接続します。  Web ブラウザーで、ポート 4000 で返された IP アドレスを開きます (たとえば " http://localhost:4000 ")。 "Hello World!" という見出しが ブラウザーに表示されます。

![Hello World!][hello-world]

## <a name="clean-up"></a>クリーンアップ
ローカル開発クラスターからアプリケーション インスタンスを削除し、その種類のアプリケーションの登録を解除するには、テンプレートに指定されているアンインストール スクリプトを使用します。

```bash
./uninstall.sh
```

コンテナー レジストリにイメージをプッシュした後は、開発コンピューターからローカルのイメージを削除できます。

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Service Fabric のアプリケーション マニフェストとサービス マニフェストの完全な例
この記事で使用される完全なサービス マニフェストとアプリケーション マニフェストは次のとおりです。

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>既存アプリケーションへのサービスの追加

yeoman を使用して作成したアプリケーションに別のコンテナー サービスを追加するには、次の手順を実行します。

1. ディレクトリを既存アプリケーションのルートに変更します。  たとえば、Yeoman で作成したアプリケーションが `MyApplication` の場合は、`cd ~/YeomanSamples/MyApplication` です。
2. `yo azuresfcontainer:AddService` を実行します。

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>コンテナーを強制終了するまでの期間を構成する

サービスの削除 (または別のノードへの移動) が開始された後でコンテナーが削除されるまでにランタイムが待機する期間を構成できます。 期間を構成すると、`docker stop <time in seconds>` コマンドがコンテナーに送信されます。   詳細については、[docker stop](https://docs.docker.com/engine/reference/commandline/stop/) の説明を参照してください。 待機する期間は `Hosting` セクションで指定します。 次のクラスター マニフェストのスニペットは、待機間隔を設定する方法を示しています。

```xml
{
        "name": "Hosting",
        "parameters": [
          {
            "ContainerDeactivationTimeout": "10",
          ...
          }
        ]
}
```
既定の期間は 10 秒に設定されています。 この構成は動的であるため、クラスター上の構成のみをアップグレードするだけでタイムアウトが更新されます。 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>使用していないコンテナー イメージを削除するようにランタイムを構成する

使用していないコンテナー イメージをノードから削除するように Service Fabric クラスターを構成できます。 この構成により、ノード上に存在するコンテナー イメージが多すぎる場合にディスク領域を再取得できます。  この機能を有効にするには、次のスニペットに示すように、クラスター マニフェストの `Hosting` セクションを更新します。 


```xml
{
        "name": "Hosting",
        "parameters": [
          {
            "PruneContainerImages": “True”,
            "ContainerImagesToSkip": "microsoft/windowsservercore|microsoft/nanoserver|…",
          ...
          }
        ]
} 
```

削除してはならないイメージは `ContainerImagesToSkip` パラメーターに指定することができます。 


## <a name="next-steps"></a>次のステップ
* [Service Fabric でのコンテナー](service-fabric-containers-overview.md)の実行について確認します。
* [コンテナー内の .NET アプリケーションをデプロイする方法](service-fabric-host-app-in-a-container.md)に関するチュートリアルをご覧ください。
* Service Fabric の[アプリケーション ライフサイクル](service-fabric-application-lifecycle.md)について確認します。
* GitHub で [Service Fabric コンテナーのコード サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-containers)を確認します。

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

