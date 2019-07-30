---
title: Linux 上で Azure Service Fabric コンテナー アプリケーションを作成する | Microsoft Docs
description: Azure Service Fabric で初めての Linux コンテナー アプリケーションを作成します。 アプリケーションの Docker イメージをビルドして、そのイメージをコンテナー レジストリにプッシュし、Service Fabric コンテナー アプリケーションをビルドおよびデプロイします。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/4/2019
ms.author: aljo
ms.openlocfilehash: 16f117e7c5291216b5716aee40995e6f224705fa
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359367"
---
# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Linux で初めての Service Fabric コンテナー アプリケーションを作成する
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

既存のアプリケーションを Service Fabric クラスター上の Linux コンテナー内で実行する場合は、アプリケーションに変更を加える必要はありません。 この記事では、Python の [Flask](http://flask.pocoo.org/) Web アプリケーションが含まれた Docker イメージを作成し、Service Fabric クラスターにデプロイする方法について説明します。 また、[Azure Container Registry](/azure/container-registry/) を使用して、コンテナー化されたアプリケーションを共有する方法についても説明します。 この記事では、Docker の基本的な理解ができていることを前提としています。 Docker の詳細は、「[Docker Overview (Docker の概要)](https://docs.docker.com/engine/understanding-docker/)」で確認できます。

> [!NOTE]
> この記事は Linux 開発環境に適用されます。  Service Fabric クラスター ランタイムと Docker ランタイムが同じ OS で実行されている必要があります。  Windows クラスターで Linux コンテナーを実行することはできません。

## <a name="prerequisites"></a>前提条件
* 次のものを実行している開発コンピューター。
  * [Service Fabric SDK およびツール](service-fabric-get-started-linux.md)。
  * [Docker CE for Linux](https://docs.docker.com/engine/installation/#prior-releases)。 
  * [Service Fabric CLI](service-fabric-cli.md)

* Azure Container Registry のレジストリ。Azure サブスクリプションに[コンテナー レジストリを作成します](../container-registry/container-registry-get-started-portal.md)。 

## <a name="define-the-docker-container"></a>Docker コンテナーを定義する
Docker Hub にある [Python イメージ](https://hub.docker.com/_/python/)を基にしてイメージをビルドします。 

Dockerfile で Docker コンテナーを指定します。 Dockerfile は、コンテナー内で環境をセットアップし、実行するアプリを読み込み、ポートを割り当てるための手順で構成されています。 Dockerfile はイメージを作成する `docker build` コマンドへの入力です。 

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

## <a name="create-a-basic-web-application"></a>基本的な Web アプリケーションの作成
ポート 80 でリッスンして "Hello World!" を返す Flask Web アプリケーションを作成します。 同じディレクトリに *requirements.txt* というファイルを作成します。 次のコードを追加して変更を保存します。
```
Flask
```

さらに、*app.py* ファイルを作成して次のスニペットを追加します。

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

このコマンドでは、Dockerfile の手順を使用して新しいイメージを構築し、`helloworldapp` と名前を付けます (-t というタグを付けます)。 コンテナー イメージを構築するには、まず、アプリケーションの追加先となる Docker Hub から基本イメージをダウンロードします。 

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

実行中のコンテナーに接続します。 Web ブラウザーで、ポート 4000 で返された IP アドレスを開きます (たとえば "http:\//localhost:4000")。 "Hello World!" という見出しが ブラウザーに表示されます。

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

`docker login` を実行し、ご自分の[レジストリの資格情報](../container-registry/container-registry-authentication.md)を使用してお使いのコンテナー レジストリにサインインします。

次の例では、Azure Active Directory [サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)の ID とパスワードを渡します。 たとえば、自動化シナリオのために、レジストリにサービス プリンシパルを割り当てることができます。 または、ご自分のレジストリのユーザー名とパスワードを使ってサインインすることもできます。

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
Linux 用の Service Fabric SDK には、[Yeoman](https://yeoman.io/) ジェネレーターが含まれています。これを使用すると、アプリケーションを作成したり、コンテナー イメージを追加したりする作業が簡単になります。 Yeoman を使用し、Docker コンテナーを 1 つだけ含むアプリケーション *SimpleContainerApp* を作成してみましょう。

Service Fabric コンテナー アプリケーションを作成するには、ターミナル ウィンドウを開き、`yo azuresfcontainer` を実行します。 

アプリケーションとアプリケーション サービスに、それぞれ `mycontainer`、`myservice` などの名前を付けます。

イメージ名については、コンテナー レジストリにあるコンテナー イメージの URL を指定します (例: "myregistry.azurecr.io/samples/helloworldapp")。 

このイメージには定義済みのワークロード エントリ ポイントがあるため、入力コマンド (コンテナーの起動後にコンテナーの実行を維持するためにコンテナー内で実行されるコマンド) を明示的に指定する必要はありません。 

インスタンス数を "1" に指定します。

適切な形式でポート マッピングを指定します。 この記事では、```80:4000``` をポート マッピングとして提供する必要があります。 そうすることで、ホスト コンピューター上のポート 4000 に到着した受信要求がコンテナー上のポート 80 にリダイレクトされるように構成しています。

![コンテナー用 Service Fabric Yeoman ジェネレーター][sf-yeoman]

## <a name="configure-container-repository-authentication"></a>コンテナー リポジトリの認証を構成する
 コンテナーでプライベート リポジトリを使用した認証が必要な場合は、`RepositoryCredentials` を追加します。 この記事では、myregistry.azurecr.io コンテナー レジストリのアカウント名とパスワードを追加します。 適切なサービス パッケージに対応する 'ServiceManifestImport' タグにポリシーが追加されていることを確認します。

```xml
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0" />
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
   </ServiceManifestImport>
``` 

リポジトリのパスワードを暗号化することをお勧めします。 手順については、「[Service Fabric アプリケーションでシークレットを管理する](service-fabric-application-secret-management.md)」を参照してください。

### <a name="configure-cluster-wide-credentials"></a>クラスター全体の資格情報を構成する
[こちらのドキュメント](
service-fabric-get-started-containers.md#configure-cluster-wide-credentials)を参照してください

## <a name="configure-isolation-mode"></a>分離モードの構成
6\.3 のランタイム リリースでは、Linux コンテナーで VM 分離がサポートされています。つまり、process と hyperv の 2 つの分離モードがサポートされています。 hyperv 分離モードでは、各コンテナーとコンテナー ホスト間でカーネルが分離されます。 hyperv 分離は、[Clear Containers](https://software.intel.com/en-us/articles/intel-clear-containers-2-using-clear-containers-with-docker) を使用して実装されています。 分離モードは、Linux クラスター用にアプリケーション マニフェスト ファイルの `ServicePackageContainerPolicy` 要素に指定されます。 指定できる分離モードは、`process`、`hyperv`、および `default` です。 既定は、プロセス分離モードです。 以下のスニペットは、アプリケーション マニフェスト ファイルで分離モードがどのように指定されるかを示しています。

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0"/>
      <Policies>
        <ServicePackageContainerPolicy Hostname="votefront" Isolation="hyperv">
          <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ServicePackageContainerPolicy>
    </Policies>
  </ServiceManifestImport>
```


## <a name="configure-resource-governance"></a>リソース管理を構成する
[リソース管理](service-fabric-resource-governance.md)は、コンテナーがホスト上で使用できるリソースを制限します。 `ResourceGovernancePolicy` 要素はアプリケーション マニフェストで指定され、サービス コード パッケージのリソース制限を宣言するために使用されます。 次のリソースのリソースの制限を設定できます。Memory、MemorySwap、CpuShares (CPU の相対的な重み)、MemoryReservationInMB、BlkioWeight (BlockIO の相対的な重み)。 この例では、Guest1Pkg というサービス パッケージが配置されたクラスター ノード上で 1 つのコアを取得しています。 Memory の制限は絶対的であるため、コード パッケージのメモリは両方とも 1,024 MB に制限されます (ソフト保証予約は同じです)。 コード パッケージ (コンテナーまたはプロセス) は、この制限を超えてメモリを割り当てることはできず、割り当てようとするとメモリ不足の例外が発生します。 リソース制限の強制を機能させるには、サービス パッケージ内のすべてのコード パッケージでメモリ制限を指定する必要があります。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePKg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```




## <a name="configure-docker-healthcheck"></a>Docker HEALTHCHECK を構成する 

Service Fabric では、バージョン 6.1 以降、[Docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) イベントがシステム正常性レポートに自動的に統合されます。 つまり、コンテナーの **HEALTHCHECK** が有効な場合、Service Fabric は Docker によって報告されたとおりにコンテナーの正常性状態が変化するたびに正常性を報告します。 **OK** 正常性レポートは、*health_status* が "*正常*" のときに、[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) に表示され、**警告**は、*health_status* が "*異常*" のときに表示されます。 

v6.4 の最新の更新リリース以降、Docker の HEALTHCHECK 評価をエラーとしてレポートするかどうかの選択肢ができました。 このオプションを有効にすると、*health_status* が*正常*の場合、**OK** 正常性レポートが表示され、*health_status* が*異常*の場合、**ERROR** が表示されます。

コンテナーの正常性の監視のために実行される実際のチェックを指す **HEALTHCHECK** 命令は、コンテナー イメージを生成するときに使用される Dockerfile に存在する必要があります。

![HealthCheckHealthy][1]

![HealthCheckUnhealthyApp][2]

![HealthCheckUnhealthyDsp][3]

ApplicationManifest の **ContainerHostPolicies** の一部として **HealthConfig** オプションを指定することによって、コンテナーごとに **HEALTHCHECK** の動作を構成できます。

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true"
              RestartContainerOnUnhealthyDockerHealthStatus="false" 
              TreatContainerUnhealthyStatusAsError="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
既定で、*IncludeDockerHealthStatusInSystemHealthReport* は **true** に設定され、*RestartContainerOnUnhealthyDockerHealthStatus* は **false** に設定され、*TreatContainerUnhealthyStatusAsError* は **false** に設定されています。 

*RestartContainerOnUnhealthyDockerHealthStatus* を **true** に設定すると、異常を繰り返し報告するコンテナーが (おそらく他のノードで) 再起動されます。

*TreatContainerUnhealthyStatusAsError* が **true** に設定されている場合、コンテナーの *health_status* が*異常*のとき、**ERROR** 正常性レポートが表示されます。

Service Fabric クラスター全体で **HEALTHCHECK** 統合を無効化する場合、[EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) を **false** に設定する必要があります。

## <a name="deploy-the-application"></a>アプリケーションのデプロイ
アプリケーションがビルドされたら、Service Fabric CLI を使用してローカル クラスターにデプロイできます。

ローカルの Service Fabric クラスターに接続します。

```bash
sfctl cluster select --endpoint http://localhost:19080
```

https://github.com/Azure-Samples/service-fabric-containers/ にあるテンプレートに用意されているインストール スクリプトを使用してクラスターのイメージ ストアにアプリケーション パッケージをコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。


```bash
./install.sh
```

ブラウザーを開き、http:\//localhost:19080/Explorer の Service Fabric Explorer に移動します (Mac OS X で Vagrant を使用している場合は、localhost を VM のプライベート IP に置き換えます)。 Applications ノードを展開し、アプリケーションの種類のエントリと、その種類の最初のインスタンスのエントリができたことを確認します。

実行中のコンテナーに接続します。 Web ブラウザーで、ポート 4000 で返された IP アドレスを開きます (たとえば "http:\//localhost:4000")。 "Hello World!" という見出しが ブラウザーに表示されます。

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
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
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
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
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
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1. On a multi-node production 
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

1. ディレクトリを既存アプリケーションのルートに変更します。 たとえば、Yeoman で作成したアプリケーションが `MyApplication` の場合は、`cd ~/YeomanSamples/MyApplication` です。
2. `yo azuresfcontainer:AddService` を実行します。

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>コンテナーを強制終了するまでの期間を構成する

サービスの削除 (または別のノードへの移動) が開始された後でコンテナーが削除されるまでにランタイムが待機する期間を構成できます。 期間を構成すると、`docker stop <time in seconds>` コマンドがコンテナーに送信されます。  詳細については、[docker stop](https://docs.docker.com/engine/reference/commandline/stop/) の説明を参照してください。 待機する期間は `Hosting` セクションで指定します。 次のクラスター マニフェストのスニペットは、待機間隔を設定する方法を示しています。


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```

既定の期間は 10 秒に設定されています。 この構成は動的であるため、クラスター上の構成のみをアップグレードするだけでタイムアウトが更新されます。 

## <a name="configure-the-runtime-to-remove-unused-container-images"></a>使用していないコンテナー イメージを削除するようにランタイムを構成する

使用していないコンテナー イメージをノードから削除するように Service Fabric クラスターを構成できます。 この構成により、ノード上に存在するコンテナー イメージが多すぎる場合にディスク領域を再取得できます。 この機能を有効にするには、次のスニペットに示すように、クラスター マニフェストの `Hosting` セクションを更新します。 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "microsoft/windowsservercore|microsoft/nanoserver|microsoft/dotnet-frameworku|..."
          }
          ...
          }
        ]
} 
```

削除してはならないイメージは `ContainerImagesToSkip` パラメーターに指定することができます。 

## <a name="configure-container-image-download-time"></a>コンテナー イメージのダウンロード時間を構成する

Service Fabric ランタイムはコンテナー イメージのダウンロードと抽出に 20 分を割り当てており、これは大部分のコンテナー イメージにとって十分な時間です。 大きなイメージの場合、またはネットワーク接続の速度が遅い場合、イメージのダウンロードおよび抽出が中止されるまでの待機時間を長くすることが必要になる場合があります。 このタイムアウトは、次のスニペットに示すように、クラスター マニフェストの **Hosting** セクションの **ContainerImageDownloadTimeout** 属性を使用して設定できます。

```json
{
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
}
```


## <a name="set-container-retention-policy"></a>コンテナーの保持ポリシーを設定する

コンテナーのスタートアップ エラーの診断を支援するために、Service Fabric (バージョン 6.1 以降) では、終了または起動に失敗したコンテナーの保持をサポートしています。 このポリシーは、次のスニペットで示すように、**ApplicationManifest.xml** ファイルで設定できます。

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

**ContainersRetentionCount** の設定で、エラーが発生したときに保持するコンテナーの数を指定します。 負の値が指定されている場合は、エラーが発生したすべてのコンテナーが保持されます。 **ContainersRetentionCount** 属性が指定されていない場合、コンテナーは保持されません。 **ContainersRetentionCount** 属性はアプリケーション パラメーターもサポートしているため、ユーザーはテスト クラスターと運用環境クラスターで別の値を指定できます。 コンテナー サービスが他のノードに移動することを防ぐためにこの機能を使用する場合は、配置の制約を使用して、コンテナー サービスの対象を特定のノードに制約してください。 この機能を使用して保持されるコンテナーは、手動で削除する必要があります。

## <a name="start-the-docker-daemon-with-custom-arguments"></a>カスタム引数で Docker デーモンを起動する

Service Fabric ランタイムの 6.2 バージョン以降では、カスタム引数を使って Docker デーモンを起動することができます。 カスタム引数が指定された場合、Service Fabric は、他の引数を一切 Docker エンジンに渡しませんが、`--pidfile` 引数は例外です。 したがって、`--pidfile` を引数として渡すことは避けてください。 また、Service Fabric が Docker デーモンと通信できるようにするために、Docker デーモンに引き続き既定の名前付きパイプ (Windows の場合) または UNIX ドメイン ソケット (Linux の場合) でリッスンさせるよう引数で指定する必要があります。 カスタム引数は、クラスター マニフェストの **Hosting** セクションの **ContainerServiceArguments** で指定します。 その例を次のスニペットに示します。 
 

```json
{ 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
} 

```

## <a name="next-steps"></a>次の手順
* [Service Fabric でのコンテナー](service-fabric-containers-overview.md)の実行について確認します。
* [コンテナー内の .NET アプリケーションをデプロイする方法](service-fabric-host-app-in-a-container.md)に関するチュートリアルをご覧ください。
* Service Fabric の[アプリケーション ライフサイクル](service-fabric-application-lifecycle.md)について確認します。
* GitHub で [Service Fabric コンテナーのコード サンプル](https://github.com/Azure-Samples/service-fabric-containers)を確認します。

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

[1]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[2]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
