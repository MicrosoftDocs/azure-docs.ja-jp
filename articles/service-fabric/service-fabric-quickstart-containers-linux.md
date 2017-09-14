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
ms.date: 09/05/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 78306672e812745fd1902ae264c2adea196ab721
ms.contentlocale: ja-jp
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-a-service-fabric-linux-container-application-on-azure"></a>Service Fabric Linux コンテナー アプリケーションを Azure にデプロイする
Azure Service Fabric は、スケーラブルで信頼性に優れたマイクロサービスとコンテナーのデプロイと管理を行うための分散システム プラットフォームです。 

既存のアプリケーションを Service Fabric クラスター上の Linux コンテナー内で実行する場合は、アプリケーションに変更を加える必要はありません。 このクイックスタートでは、あらかじめ用意されている Docker コンテナー イメージを Service Fabric アプリケーションにデプロイする方法を紹介します。 最後まで読み進めていけば、nginx コンテナーを稼働状態にすることができます。  このクイックスタートでは、Linux コンテナーのデプロイについて説明しています。Windows コンテナーをデプロイする場合は、[こちらのクイックスタート](service-fabric-quickstart-containers.md)を参照してください。

![Nginx][nginx]

このクイックスタートでは、次の方法について説明します。
> [!div class="checklist"]
> * Docker イメージ コンテナーをパッケージ化する
> * 通信を構成する
> * Service Fabric アプリケーションのビルドとパッケージ化
> * コンテナー アプリケーションを Azure にデプロイする

## <a name="prerequisites"></a>前提条件
[Service Fabric SDK、Service Fabric CLI、Service Fabric yeoman テンプレート ジェネレーター](service-fabric-get-started-linux.md)をインストールします。
  
## <a name="package-a-docker-image-container-with-yeoman"></a>Yeoman で Docker イメージ コンテナーをパッケージ化する
Linux 用の Service Fabric SDK には、[Yeoman](http://yeoman.io/) ジェネレーターが含まれています。これを使用すると、アプリケーションを作成したり、コンテナー イメージを追加したりする作業が簡単になります。 

Service Fabric コンテナー アプリケーションを作成するには、ターミナル ウィンドウを開き、`yo azuresfcontainer` を実行します。  

アプリケーションには "MyFirstContainer" という名前を、アプリケーション サービスには "MyContainerService" という名前を付けます。

コンテナー イメージの名前 "nginx:latest" を指定します (Docker Hub の [nginx コンテナー イメージ](https://hub.docker.com/r/_/nginx/))。 

このイメージには定義済みのワークロード エントリ ポイントがあるため、入力コマンドを明示的に指定する必要があります。 

インスタンス数を "1" に指定します。

![コンテナー用 Service Fabric Yeoman ジェネレーター][sf-yeoman]

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>通信/コンテナー ポートからホスト ポートへのマッピングを構成する
クライアントがサービスと通信できるように HTTP エンドポイントを構成しましょう。  *./MyFirstContainer/MyContainerServicePkg/ServiceManifest.xml* ファイルを開いて、**ServiceManifest** 要素にエンドポイント リソースを宣言します。  プロトコル、ポート、名前を追加します。 このクイックスタートでは、サービスがポート 80 でリッスンします。 

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
    <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
`UriScheme` を指定すると、Service Fabric ネーム サービスを使用したコンテナー エンドポイントが自動的に登録され、検出可能性が確保されます。 ServiceManifest.xml の完全なサンプル ファイルは、この記事の最後にあります。 

ApplicationManifest.xml ファイルの `ContainerHostPolicies` にある `PortBinding` ポリシーを使用して、コンテナー ポートをサービスの `Endpoint` にマッピングします。  このクイックスタートでは、`ContainerPort` は 80 (コンテナーがポート 80 を公開) で、`EndpointRef` は "myserviceTypeEndpoint" (サービス マニフェストで事前に定義されたエンドポイント) です。  ポート 80 で受信するサービスへの要求は、コンテナーのポート 80 にマッピングされています。  

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>Service Fabric アプリケーションのビルドとパッケージ化
Service Fabric Yeoman テンプレートには、[Gradle](https://gradle.org/) のビルド スクリプトが含まれています。このスクリプトを使用して、端末からアプリケーションをビルドすることができます。 すべての変更を保存します。  アプリケーションをビルドしてパッケージ化するには、次のコマンドを実行します。

```bash
cd MyFirstContainer
gradle
```
## <a name="create-a-cluster"></a>クラスターの作成
Azure 内のクラスターにアプリケーションをデプロイする場合、独自のクラスターを作成する方法と、パーティ クラスターを使用する方法とがあります。

パーティ クラスターは、Azure でホストされる無料の期間限定の Service Fabric クラスターであり、Service Fabric チームによって実行されます。このクラスターには、だれでもアプリケーションをデプロイして、プラットフォームについて学習することができます。 パーティ クラスターにアクセスするには、[こちらの手順を実行します](http://aka.ms/tryservicefabric)。  

独自クラスターの作成については、「[Azure で初めての Service Fabric クラスターを作成する](service-fabric-get-started-azure-cluster.md)」を参照してください。

接続エンドポイントは書き留めておいてください。次の手順で使用します。

## <a name="deploy-the-application-to-azure"></a>Azure にアプリケーションをデプロイする
アプリケーションがビルドされたら、Service Fabric CLI を使用して Azure クラスターにデプロイできます。

Azure の Service Fabric クラスターに接続します。

```bash
sfctl cluster select --endpoint http://lnxt10vkfz6.westus.cloudapp.azure.com:19080
```

テンプレートに用意されているインストール スクリプトを使用してクラスターのイメージ ストアにアプリケーション パッケージをコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。

```bash
./install.sh
```

ブラウザーを開いて、Service Fabric Explorer (http://lnxt10vkfz6.westus.cloudapp.azure.com:19080/Explorer) に移動します。 Applications ノードを展開し、アプリケーションの種類のエントリと、その種類の最初のインスタンスのエントリができたことを確認します。

![Service Fabric Explorer][sfx]

実行中のコンテナーに接続します。  Web ブラウザーで、ポート 80 で返される IP アドレスを開きます (例: "lnxt10vkfz6.westus.cloudapp.azure.com:80")。 ブラウザーに、nginx のウェルカム ページが表示されます。

![Nginx][nginx]

## <a name="clean-up"></a>クリーンアップ
クラスターからアプリケーション インスタンスを削除し、その種類のアプリケーションの登録を解除するには、テンプレートに指定されているアンインストール スクリプトを使用します。

```bash
./uninstall.sh
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Service Fabric のアプリケーション マニフェストとサービス マニフェストの完全な例
このクイックスタートで使用される完全なサービス マニフェストとアプリケーション マニフェストは次のとおりです。

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>nginx:latest</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>

```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest  ApplicationTypeName="MyFirstContainerType" ApplicationTypeVersion="1.0.0"
                      xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
   
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
   <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
   
   <DefaultServices>
      <Service Name="MyContainerService">
        <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
        cluster, set InstanceCount to -1 for the container service to run on every node in 
        the cluster.
        -->
        <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="1">
            <SingletonPartition />
        </StatelessService>
      </Service>
   </DefaultServices>
   
</ApplicationManifest>

```

## <a name="next-steps"></a>次のステップ
このクイックスタートでは、次の方法について説明します。
> [!div class="checklist"]
> * Docker イメージ コンテナーをパッケージ化する
> * 通信を構成する
> * Service Fabric アプリケーションのビルドとパッケージ化
> * コンテナー アプリケーションを Azure にデプロイする

* [Service Fabric でのコンテナー](service-fabric-containers-overview.md)の実行について確認します。
* [コンテナー内の .NET アプリケーションをデプロイする方法](service-fabric-host-app-in-a-container.md)に関するチュートリアルをご覧ください。
* Service Fabric の[アプリケーション ライフサイクル](service-fabric-application-lifecycle.md)について確認します。
* GitHub で [Service Fabric コンテナーのコード サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-containers)を確認します。

[sfx]: ./media/service-fabric-quickstart-containers-linux/SFX.png
[nginx]: ./media/service-fabric-quickstart-containers-linux/nginx.png
[sf-yeoman]: ./media/service-fabric-quickstart-containers-linux/YoSF.png

