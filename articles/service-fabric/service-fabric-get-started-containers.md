---
title: "Azure Service Fabric コンテナー アプリケーションを作成する | Microsoft Docs"
description: "Azure Service Fabric で初めての Windows コンテナー アプリケーションを作成します。  Python アプリケーションで Docker イメージを作成して、そのイメージをコンテナー レジストリにプッシュし、Service Fabric コンテナー アプリケーションをビルドおよびデプロイします。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/18/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: e37a8ee4d7eda192caf7a4d3ab0db6e4a08576d8
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---

# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Windows で初めての Service Fabric コンテナー アプリケーションを作成する
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

既存のアプリケーションを Service Fabric クラスター上の Windows コンテナー内で実行する場合は、アプリケーションに変更を加える必要はありません。 この記事では、Python の [Flask](http://flask.pocoo.org/) Web アプリケーションが含まれた Docker イメージを作成し、Service Fabric クラスターにデプロイする方法について説明します。  また、[Azure Container Registry](/azure/container-registry/) を使用して、コンテナー化されたアプリケーションを共有する方法についても説明します。  この記事では、Docker の基本的な理解ができていることを前提としています。 Docker の詳細は、「[Docker Overview (Docker の概要)](https://docs.docker.com/engine/understanding-docker/)」で確認できます。

## <a name="prerequisites"></a>前提条件
次のものを実行している開発コンピューター。
* Visual Studio 2015 または Visual Studio 2017。
* [Service Fabric SDK およびツール](service-fabric-get-started.md)。
*  Docker for Windows。  [Docker CE for Windows (安定版) を入手します](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)。 Docker をインストールして起動したら、トレイ アイコンを右クリックし、**[Switch to Windows containers]\(Windows コンテナーに切り替える\)** を選択します。 これは、Windows に基づいて Docker イメージを実行するために必要です。

Windows Server 2016 上で実行されている 3 つ以上のノードがあり、コンテナーを含む Windows クラスター。[クラスターを作成する](service-fabric-cluster-creation-via-portal.md)か、[無料で Service Fabric を試してください](https://aka.ms/tryservicefabric)。

Azure Container Registry のレジストリ。Azure サブスクリプションに[コンテナー レジストリを作成します](../container-registry/container-registry-get-started-portal.md)。

## <a name="define-the-docker-container"></a>Docker コンテナーを定義する
Docker Hub にある [Python イメージ](https://hub.docker.com/_/python/)を基にしてイメージをビルドします。

Dockerfile で Docker コンテナーを定義します。 Dockerfile には、コンテナー内で環境をセットアップし、実行するアプリを読み込み、ポートを割り当てるための手順が含まれています。 Dockerfile はイメージを作成する `docker build` コマンドへの入力です。

空のディレクトリを作成し、*Dockerfile* というファイル (ファイル拡張子なし) を作成します。 *Dockerfile* に次のコードを追加して変更を保存します。

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
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

<a id="Build-Containers"></a>
## <a name="build-the-image"></a>イメージをビルドする
`docker build` コマンドを実行して、Web アプリケーションを実行するイメージを作成します。 PowerShell ウィンドウを開き、Dockerfile が格納されているディレクトリに移動します。 次のコマンドを実行します。

```
docker build -t helloworldapp .
```

このコマンドでは、Dockerfile の手順を使用して新しいイメージを構築し、"helloworldapp" と名前を付けます (-t というタグを付けます)。 イメージをビルドすると、基本イメージが Docker Hub から取得され、基本イメージ上にアプリケーションを追加する新しいイメージが作成されます。  

ビルド コマンドが完了したら、`docker images` コマンドを実行して、新しいイメージの情報を確認します。

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>ローカルでアプリケーションを実行する
コンテナー レジストリにプッシュする前に、ローカルでイメージを確認します。  

アプリケーションを実行します。

```
docker run -d --name my-web-site helloworldapp
```

*name* で、実行中のコンテナーに名前を付けます (コンテナー ID ではありません)。

コンテナーが起動したら、実行中のコンテナーにブラウザーから接続できるように、その IP アドレスを確認します。
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

実行中のコンテナーに接続します。  Web ブラウザーでその IP アドレスを開きます ("http://172.31.194.61" など)。 "Hello World!" という見出しが ブラウザーに表示されます。

コンテナーを停止するには、次を実行します。

```
docker stop my-web-site
```

コンテナーを開発コンピューターから削除します。

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>コンテナー レジストリにイメージをプッシュする
コンテナーが開発コンピューターで実行されることを確認したら、イメージを Azure Container Registry のレジストリにプッシュします。

[レジストリの資格情報](../container-registry/container-registry-authentication.md)を使用してコンテナー レジストリにログインするには、``docker login`` を実行します。

次の例では、Azure Active Directory [サービス プリンシパル](../active-directory/active-directory-application-objects.md)の ID とパスワードを渡します。 たとえば、自動化シナリオのために、レジストリにサービス プリンシパルを割り当てることができます。 または、レジストリのユーザー名とパスワードを使ってログインすることもできます。

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

次のコマンドでは、レジストリへの完全修飾パスを使用して、イメージのタグまたはエイリアスを作成します。 この例では、レジストリのルートが煩雑にならないように、イメージを ```samples``` 名前空間に配置しています。

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

コンテナー レジストリにイメージをプッシュします。

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>コンテナー化されたサービスを Visual Studio で作成する
Service Fabric SDK およびツールには、コンテナー化されたアプリケーションを作成するときに役立つサービスのテンプレートが用意されています。

1. Visual Studio を起動します。  **[ファイル]** > **[新規作成]** > **[プロジェクト]** の順に選択します。
2. **[Service Fabric アプリケーション]** を選択し、"MyFirstContainer" という名前を付けて、**[OK]** をクリックします。
3. **[サービス テンプレート]** の一覧から **[ゲスト コンテナー]** を選択します。
4. **[Image Name]\(イメージ名\)** に、コンテナー リポジトリにプッシュされたイメージである "myregistry.azurecr.io/samples/helloworldapp" を入力します。
5. サービスに名前を付けて、**[OK]** をクリックします。

## <a name="configure-communication"></a>通信を構成する
コンテナー化されたサービスには、通信のエンドポイントが必要です。 `Endpoint` 要素にプロトコル、ポート、タイプを指定して ServiceManifest.xml ファイルに追加してください。 この記事では、コンテナー化されたサービスがポート 8081 でリッスンします。  この例では、固定ポート 8081 を使用します。  ポートを指定しなかった場合は、アプリケーション ポートの範囲から無作為に選択されます。  

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```

Service Fabric は、エンドポイントを定義することによって、ネーム サービスにそのエンドポイントを発行します。  同じクラスターで実行されている他のサービスは、このコンテナーを名前解決することができます。  コンテナー対コンテナーの通信は、[リバース プロキシ](service-fabric-reverseproxy.md)を使用して実行することもできます。  通信は、リバース プロキシ HTTP リスニング ポートおよび通信先のサービスの名前を環境変数として設定することで実行します。

## <a name="configure-and-set-environment-variables"></a>環境変数の構成と設定
環境変数は、サービス マニフェストのコード パッケージごとに指定できます。 この機能は、コンテナー、プロセス、またはゲスト実行可能ファイルとしてデプロイされているかどうかに関係なく、すべてのサービスで使用できます。 環境変数の値は、アプリケーション マニフェスト内で上書きすることも、デプロイ中にアプリケーションのパラメーターとして指定することもできます。

次のサービス マニフェストの XML スニペットは、コード パッケージ用の環境変数を指定する方法の例です。
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

これらの環境変数は、アプリケーション マニフェスト内で上書きできます。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>コンテナー ポート対ホスト ポートのマッピングおよびコンテナー対コンテナーの検出を構成する
コンテナーとの通信に使用するホスト ポートを構成します。 このポートのバインドでは、サービスがコンテナー内部でリッスンしているポートをホスト上のポートにマップします。 `PortBinding` 要素は、ApplicationManifest.xml ファイルの `ContainerHostPolicies` 要素に追加します。  この記事では、`ContainerPort` は 80 で (コンテナーは Dockerfile で指定されたとおりにポート 80 を公開します)、`EndpointRef` は "Guest1TypeEndpoint" です (あらかじめサービス マニフェストで定義したエンドポイントです)。  ポート 8081 のサービスへの受信要求は、コンテナーのポート 80 にマッピングされています。

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-container-registry-authentication"></a>コンテナー レジストリの認証を構成する
コンテナー レジストリの認証は、ApplicationManifest.xml ファイルの `ContainerHostPolicies` に `RepositoryCredentials` を追加することによって構成します。 myregistry.azurecr.io コンテナー レジストリのアカウントとパスワードを追加することで、サービスがコンテナー イメージをリポジトリからダウンロードできるようになります。

```xml
<Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
    </ContainerHostPolicies>
</Policies>
```

リポジトリのパスワードは、クラスターのすべてのノードにデプロイされる暗号化証明書を使用して暗号化することをお勧めします。 Service Fabric がサービス パッケージをクラスターにデプロイするときに、その暗号化証明書を使って暗号テキストが解読されます。  Invoke-ServiceFabricEncryptText コマンドレットを使ってパスワードの暗号テキストを作成し、ApplicationManifest.xml ファイルに追加してください。

次のスクリプトでは、新しい自己署名証明書を作成して、PFX ファイルにエクスポートしています。  その証明書は既存の Key Vault にインポートされた後、Service Fabric クラスターにデプロイされます。

```powershell
# Variables.
$certpwd = ConvertTo-SecureString -String "Pa$$word321!" -Force -AsPlainText
$filepath = "C:\MyCertificates\dataenciphermentcert.pfx"
$subjectname = "dataencipherment"
$vaultname = "mykeyvault"
$certificateName = "dataenciphermentcert"
$groupname="myclustergroup"
$clustername = "mycluster"

$subscriptionId = "subscription ID"

Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Create a self signed cert, export to PFX file.
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject $subjectname -Provider 'Microsoft Enhanced Cryptographic Provider v1.0' `
| Export-PfxCertificate -FilePath $filepath -Password $certpwd

# Import the certificate to an existing key vault.  The key vault must be enabled for deployment.
$cer = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certificateName -FilePath $filepath -Password $certpwd

Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $groupname -EnabledForDeployment

# Add the certificate to all the VMs in the cluster.
Add-AzureRmServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $cer.SecretId
```
パスワードを暗号化するには、[Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps) コマンドレットを使用します。

```powershell
$text = "=P==/==/=8=/=+u4lyOB=+=nWzEeRfF="
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint $cer.Thumbprint -Text $text -StoreLocation Local -StoreName My
```

パスワードを [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps) コマンドレットから返された暗号テキストに置き換えて、`PasswordEncrypted` を "true" に設定します。

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
    <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-isolation-mode"></a>分離モードの構成
Windows では、コンテナーの 2 つの分離モード (プロセスおよび Hyper-V) がサポートされます。 プロセス分離モードでは、同じホスト コンピューターで実行されているすべてのコンテナーがホストとカーネルを共有します。 Hyper-V 分離モードでは、各 Hyper-V コンテナーとコンテナー ホスト間でカーネルが分離されます。 分離モードは、アプリケーション マニフェスト ファイルの `ContainerHostPolicies` 要素に指定されます。 指定できる分離モードは、`process`、`hyperv`、および `default` です。 分離モードを default にした場合、Windows Server ホストでは `process` に、Windows 10 ホストでは `hyperv` にそれぞれ既定で設定されます。 以下のスニペットは、アプリケーション マニフェスト ファイルで分離モードがどのように指定されるかを示しています。

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```

## <a name="configure-resource-governance"></a>リソース管理を構成する
[リソース管理](service-fabric-resource-governance.md)は、コンテナーがホスト上で使用できるリソースを制限します。 `ResourceGovernancePolicy` 要素はアプリケーション マニフェストで指定され、サービス コード パッケージのリソース制限を宣言するために使用されます。 リソースの制限は、Memory、MemorySwap、CpuShares (CPU の相対的な重み)、MemoryReservationInMB、BlkioWeight (BlockIO の相対的な重み) の各リソースに対して設定できます。  この例では、Guest1Pkg というサービス パッケージが配置されたクラスター ノード上で 1 つのコアを取得しています。  Memory の制限は絶対的であるため、コード パッケージのメモリは両方とも 1,024 MB に制限されます (ソフト保証予約は同じです)。 コード パッケージ (コンテナーまたはプロセス) は、この制限を超えてメモリを割り当てることはできず、割り当てようとするとメモリ不足の例外が発生します。 リソース制限の強制を機能させるには、サービス パッケージ内のすべてのコード パッケージでメモリ制限を指定する必要があります。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```

## <a name="deploy-the-container-application"></a>コンテナー アプリケーションをデプロイする
変更をすべて保存し、アプリケーションをビルドします。 アプリケーションを発行するために、ソリューション エクスプローラーの **[MyFirstContainer]** を右クリックし、**[発行]** を選択します。

**[クラスター エンドポイント]** に、クラスターの管理エンドポイントを入力します   (例: "containercluster.westus2.cloudapp.azure.com:19000")。 [Azure Portal](https://portal.azure.com) にある、お使いのクラスターの [概要] ブレードで、クライアントの接続エンドポイントを探すことができます。

**[発行]**をクリックします。

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) は、Service Fabric クラスター内のアプリケーションとノードを検査および管理するための Web ベースのツールです。 ブラウザーを開き、http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ に移動して、アプリケーションのデプロイを進めます。  アプリケーションはデプロイされますが、クラスター ノードにイメージがダウンロードされるまではエラー状態となります (イメージのサイズによって時間がかかる場合があります)。![エラー][1]

```Ready``` 状態になったら、アプリケーションの準備は完了しています。![準備完了][2]

ブラウザーを開き、http://containercluster.westus2.cloudapp.azure.com:8081 に移動します。 "Hello World!" という見出しが ブラウザーに表示されます。

## <a name="clean-up"></a>クリーンアップ
クラスターの実行中は、料金が継続的に発生します。[クラスターの削除](service-fabric-get-started-azure-cluster.md#remove-the-cluster)を検討してください。  [パーティ クラスター](http://tryazureservicefabric.westus.cloudapp.azure.com/)は数時間後に自動的に削除されます。

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
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

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

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

