---
title: Azure Service Fabric コンテナー アプリケーションを作成する | Microsoft Docs
description: Azure Service Fabric で初めての Windows コンテナー アプリケーションを作成します。 Python アプリケーションで Docker イメージを作成して、そのイメージをコンテナー レジストリにプッシュし、Service Fabric コンテナー アプリケーションをビルドおよびデプロイします。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: jpconnock
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/25/2019
ms.author: aljo
ms.openlocfilehash: 70dc458e341024797761262cd9a4fd1b3eb23ec3
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359782"
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Windows で初めての Service Fabric コンテナー アプリケーションを作成する

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

既存のアプリケーションを Service Fabric クラスター上の Windows コンテナー内で実行する場合は、アプリケーションに変更を加える必要はありません。 この記事では、Python の [Flask](http://flask.pocoo.org/) Web アプリケーションが含まれた Docker イメージを作成し、ローカル マシン上で実行している Service Fabric クラスターにデプロイする方法について説明します。 また、[Azure Container Registry](/azure/container-registry/) を使用して、コンテナー化されたアプリケーションを共有する方法についても説明します。 この記事では、Docker の基本的な理解ができていることを前提としています。 Docker の詳細は、「[Docker Overview (Docker の概要)](https://docs.docker.com/engine/understanding-docker/)」で確認できます。

> [!NOTE]
> この記事は Windows 開発環境に適用されます。  Service Fabric クラスター ランタイムと Docker ランタイムが同じ OS で実行されている必要があります。  Linux クラスターで Windows コンテナーを実行することはできません。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

* 次のものを実行している開発コンピューター。
  * Visual Studio 2015 または Visual Studio 2019。
  * [Service Fabric SDK およびツール](service-fabric-get-started.md)。
  *  Docker for Windows。 [Docker CE for Windows (安定版) を入手します](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)。 Docker をインストールして起動したら、トレイ アイコンを右クリックし、 **[Switch to Windows containers]\(Windows コンテナーに切り替える\)** を選択します。 この手順は、Windows に基づいて Docker イメージを実行するために必要です。

* Windows Server with Containers 上で 3 つ以上のノードを実行している Windows クラスター。 

  この記事では、クラスター ノードで実行されている Windows Server with Containers のバージョン (ビルド) を、お使いの開発コンピューターのバージョンと一致させる必要があります。 これは、お使いの開発用コンピューターで Docker イメージをビルドしており、コンテナー OS のバージョンとデプロイ先のホスト OS のバージョンとの間に互換性の制約があるからです。 詳細については、「[Windows Server コンテナーの OS とホスト OS の互換性](#windows-server-container-os-and-host-os-compatibility)」を参照してください。 
  
クラスターに必要な Windows Server with Containers のバージョンを確認するには、開発用コンピューターで Windows コマンド プロンプトから `ver` コマンドを実行します。

* バージョンに *x.x.14323.x* が含まれている場合は、[クラスターの作成](service-fabric-cluster-creation-via-portal.md) 時にオペレーティング システムに *WindowsServer 2016-Datacenter-with-Containers* を選択します。
  * バージョンに *x.x.16299.x* が含まれている場合は、[クラスターの作成](service-fabric-cluster-creation-via-portal.md) 時にオペレーティング システムに *WindowsServerSemiAnnual Datacenter-Core-1709-with-Containers* を選択します。

* Azure Container Registry のレジストリ。Azure サブスクリプションに[コンテナー レジストリを作成します](../container-registry/container-registry-get-started-portal.md)。

> [!NOTE]
> Windows 10 上で実行されている Service Fabric クラスターへのコンテナーのデプロイは、サポートされていません。  Windows コンテナーを実行するように Windows 10 を構成する方法については、[こちらの記事](service-fabric-how-to-debug-windows-containers.md)を参照してください。
>   

> [!NOTE]
> Service Fabric バージョン 6.2 以降では、Windows Server バージョン 1709 上で実行されているクラスターにコンテナーをデプロイすることができます。  
> 

## <a name="define-the-docker-container"></a>Docker コンテナーを定義する

Docker Hub にある [Python イメージ](https://hub.docker.com/_/python/)を基にしてイメージをビルドします。

Dockerfile で Docker コンテナーを指定します。 Dockerfile は、コンテナー内で環境をセットアップし、実行するアプリを読み込み、ポートを割り当てるための手順で構成されています。 Dockerfile はイメージを作成する `docker build` コマンドへの入力です。

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

## <a name="create-a-basic-web-application"></a>基本的な Web アプリケーションの作成
ポート 80 でリッスンして `Hello World!` を返す Flask Web アプリケーションを作成します。 同じディレクトリに *requirements.txt* というファイルを作成します。 次のコードを追加して変更を保存します。
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

<a id="Build-Containers"></a>
## <a name="build-the-image"></a>イメージをビルドする
`docker build` コマンドを実行して、Web アプリケーションを実行するイメージを作成します。 PowerShell ウィンドウを開き、Dockerfile が格納されているディレクトリに移動します。 次のコマンドを実行します。

```
docker build -t helloworldapp .
```

このコマンドでは、Dockerfile の手順を使用して新しいイメージを構築し、`helloworldapp` と名前を付けます (-t というタグを付けます)。 コンテナー イメージを構築するには、まず、アプリケーションの追加先となる Docker Hub から基本イメージをダウンロードします。 

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

そのコマンドが何も返さない場合は、次のコマンドを実行し、IP アドレスの **NetworkSettings**->**Networks** 要素を検査します。
```
docker inspect my-web-site
```

実行中のコンテナーに接続します。 Web ブラウザーでその IP アドレスを開きます ("http:\//172.31.194.61" など)。 "Hello World!" という見出しが ブラウザーに表示されます。

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

``docker login`` を実行し、ご自分の[レジストリの資格情報](../container-registry/container-registry-authentication.md)を使用してお使いのコンテナー レジストリにサインインします。

次の例では、Azure Active Directory [サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)の ID とパスワードを渡します。 たとえば、自動化シナリオのために、レジストリにサービス プリンシパルを割り当てることができます。 または、ご自分のレジストリのユーザー名とパスワードを使ってサインインすることもできます。

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

1. Visual Studio を起動します。 **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** の順に選択します。
2. **[Service Fabric アプリケーション]** を選択し、"MyFirstContainer" という名前を付けて、 **[OK]** をクリックします。
3. **[サービス テンプレート]** の一覧から **[コンテナー]** を選択します。
4. **[Image Name]\(イメージ名\)** に、コンテナー リポジトリにプッシュされたイメージである "myregistry.azurecr.io/samples/helloworldapp" を入力します。
5. サービスに名前を付けて、 **[OK]** をクリックします。

## <a name="configure-communication"></a>通信を構成する
コンテナー化されたサービスには、通信のエンドポイントが必要です。 `Endpoint` 要素にプロトコル、ポート、タイプを指定して ServiceManifest.xml ファイルに追加してください。 この例では、固定ポート 8081 を使用します。 ポートを指定しなかった場合は、アプリケーション ポートの範囲から無作為に選択されます。 

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```
> [!NOTE]
> 適用可能なプロパティ値を持つ追加の EndPoint 要素を宣言することで、サービスの追加のエンドポイントを追加できます。 各ポートは、1 つのプロトコル値のみを宣言できます。

Service Fabric は、エンドポイントを定義することによって、ネーム サービスにそのエンドポイントを発行します。 同じクラスターで実行されている他のサービスは、このコンテナーを名前解決することができます。 コンテナー対コンテナーの通信は、[リバース プロキシ](service-fabric-reverseproxy.md)を使用して実行することもできます。 通信は、リバース プロキシ HTTP リスニング ポートおよび通信先のサービスの名前を環境変数として設定することで実行します。

サービスは、特定のポートでリッスンしています (この例では 8081)。 Azure でアプリケーションがクラスターにデプロイされると、クラスターとアプリケーションの両方が Azure ロード バランサーの背後で実行します。 受信トラフィックがサービスを通過できるように、Azure ロード バランサーでアプリケーション ポートが開かれている必要があります。  [PowerShell スクリプト](./scripts/service-fabric-powershell-open-port-in-load-balancer.md)または [Azure portal](https://portal.azure.com) を使用して、Azure Load Balancer でこのポートを開くことができます。

## <a name="configure-and-set-environment-variables"></a>環境変数の構成と設定
環境変数は、サービス マニフェストのコード パッケージごとに指定できます。 この機能は、コンテナー、プロセス、またはゲスト実行可能ファイルとしてデプロイされているかどうかに関係なく、すべてのサービスで使用できます。 環境変数の値は、アプリケーション マニフェスト内でオーバーライドすることも、デプロイ中にアプリケーションのパラメーターとして指定することもできます。

次のサービス マニフェストの XML スニペットは、コード パッケージ用の環境変数を指定する方法の例です。
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

これらの環境変数は、アプリケーション マニフェスト内でオーバーライドできます。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <EnvironmentOverrides CodePackageRef="FrontendService.Code">
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>コンテナー ポート対ホスト ポートのマッピングおよびコンテナー対コンテナーの検出を構成する
コンテナーとの通信に使用するホスト ポートを構成します。 このポートのバインドでは、サービスがコンテナー内部でリッスンしているポートをホスト上のポートにマップします。 `PortBinding` 要素は、ApplicationManifest.xml ファイルの `ContainerHostPolicies` 要素に追加します。 この記事では、`ContainerPort` は 80 で (コンテナーは Dockerfile で指定されたとおりにポート 80 を公開します)、`EndpointRef` は "Guest1TypeEndpoint" です (あらかじめサービス マニフェストで定義したエンドポイントです)。 ポート 8081 のサービスへの受信要求は、コンテナーのポート 80 にマッピングされています。

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```
> [!NOTE]
> 適用可能なプロパティ値を持つ追加の PortBinding 要素を宣言することで、サービスの追加の PortBinding を追加できます。

## <a name="configure-container-registry-authentication"></a>コンテナー レジストリの認証を構成する

コンテナー レジストリの認証は、ApplicationManifest.xml ファイルの `ContainerHostPolicies` に `RepositoryCredentials` を追加することによって構成します。 myregistry.azurecr.io コンテナー レジストリのアカウントとパスワードを追加することで、サービスがコンテナー イメージをリポジトリからダウンロードできるようになります。

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

リポジトリのパスワードは、クラスターのすべてのノードにデプロイされる暗号化証明書を使用して暗号化することをお勧めします。 Service Fabric がサービス パッケージをクラスターにデプロイするときに、その暗号化証明書を使って暗号テキストが解読されます。 Invoke-ServiceFabricEncryptText コマンドレットを使ってパスワードの暗号テキストを作成し、ApplicationManifest.xml ファイルに追加してください。

次のスクリプトでは、新しい自己署名証明書を作成して、PFX ファイルにエクスポートしています。 その証明書は既存の Key Vault にインポートされた後、Service Fabric クラスターにデプロイされます。

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

Login-AzAccount

Select-AzSubscription -SubscriptionId $subscriptionId

# Create a self signed cert, export to PFX file.
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject $subjectname -Provider 'Microsoft Enhanced Cryptographic Provider v1.0' `
| Export-PfxCertificate -FilePath $filepath -Password $certpwd

# Import the certificate to an existing key vault. The key vault must be enabled for deployment.
$cer = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certificateName -FilePath $filepath -Password $certpwd

Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $groupname -EnabledForDeployment
Add-AzServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $cer.SecretId
```
パスワードを暗号化するには、[Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps) コマンドレットを使用します。

```powershell
$text = "=P==/==/=8=/=+u4lyOB=+=nWzEeRfF="
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint $cer.Thumbprint -Text $text -StoreLocation Local -StoreName My
```

パスワードを [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps) コマンドレットから返された暗号テキストに置き換えて、`PasswordEncrypted` を "true" に設定します。

```xml
<ServiceManifestImport>
    ...
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
    ...
</ServiceManifestImport>
```

### <a name="configure-cluster-wide-credentials"></a>クラスター全体の資格情報を構成する

6\.3 ランタイム以降、Service Fabric では、アプリケーションから既定のリポジトリ資格情報として使用できるクラスター全体の資格情報を構成できるようになりました。

`true` または `false` の値を指定した `UseDefaultRepositoryCredentials` 属性を ApplicationManifest.xml の `ContainerHostPolicies` に追加することで、この機能を有効または無効にすることができます。

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Service Fabric では、`Hosting` セクションの ClusterManifest に指定できる既定のリポジトリ資格情報が使用されます。  `UseDefaultRepositoryCredentials` が `true` の場合、Service Fabric で ClusterManifest から次の値が読み取られます。

* DefaultContainerRepositoryAccountName (文字列)
* DefaultContainerRepositoryPassword (文字列)
* IsDefaultContainerRepositoryPasswordEncrypted (ブール値)
* DefaultContainerRepositoryPasswordType (文字列) --- 6.4 ランタイム以降でサポートされています

ClusterManifestTemplate.json ファイルの `Hosting` セクション内に追加できる内容の例を次に示します。 `Hosting` セクションは、クラスターの作成時、または構成をアップグレードするときに後で追加できます。 詳細については、[Service Fabric クラスター設定の変更](service-fabric-cluster-fabric-settings.md)と [Service Fabric アプリケーションのシークレット管理](service-fabric-application-secret-management.md)に関するページを参照してください。

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="configure-isolation-mode"></a>分離モードの構成
Windows では、コンテナーの 2 つの分離モード (プロセスおよび Hyper-V) がサポートされます。 プロセス分離モードでは、同じホスト コンピューターで実行されているすべてのコンテナーがホストとカーネルを共有します。 Hyper-V 分離モードでは、各 Hyper-V コンテナーとコンテナー ホスト間でカーネルが分離されます。 分離モードは、アプリケーション マニフェスト ファイルの `ContainerHostPolicies` 要素に指定されます。 指定できる分離モードは、`process`、`hyperv`、および `default` です。 Windows Server ホスト上での既定値は、プロセス分離モードです。 Windows 10 ホストでサポートされているのは、HYPER-V 分離モードのみなので、コンテナーはその分離モードの設定に関係なく、HYPER-V 分離モードで実行されます。 以下のスニペットは、アプリケーション マニフェスト ファイルで分離モードがどのように指定されるかを示しています。

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > hyperv 分離モードは、入れ子の仮想化がサポートされた Azure SKU (Ev3 と Dv3) で利用できます。 
   >
   >

## <a name="configure-resource-governance"></a>リソース管理を構成する
[リソース管理](service-fabric-resource-governance.md)は、コンテナーがホスト上で使用できるリソースを制限します。 `ResourceGovernancePolicy` 要素はアプリケーション マニフェストで指定され、サービス コード パッケージのリソース制限を宣言するために使用されます。 次のリソースのリソースの制限を設定できます。Memory、MemorySwap、CpuShares (CPU の相対的な重み)、MemoryReservationInMB、BlkioWeight (BlockIO の相対的な重み)。 この例では、Guest1Pkg というサービス パッケージが配置されたクラスター ノード上で 1 つのコアを取得しています。 Memory の制限は絶対的であるため、コード パッケージのメモリは両方とも 1,024 MB に制限されます (ソフト保証予約は同じです)。 コード パッケージ (コンテナーまたはプロセス) は、この制限を超えてメモリを割り当てることはできず、割り当てようとするとメモリ不足の例外が発生します。 リソース制限の強制を機能させるには、サービス パッケージ内のすべてのコード パッケージでメモリ制限を指定する必要があります。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
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

![HealthCheckHealthy][3]

![HealthCheckUnhealthyApp][4]

![HealthCheckUnhealthyDsp][5]

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

## <a name="deploy-the-container-application"></a>コンテナー アプリケーションをデプロイする
変更をすべて保存し、アプリケーションをビルドします。 アプリケーションを発行するために、ソリューション エクスプローラーの **[MyFirstContainer]** を右クリックし、 **[発行]** を選択します。

**[クラスター エンドポイント]** に、クラスターの管理エンドポイントを入力します (例: "containercluster.westus2.cloudapp.azure.com:19000")。 [Azure Portal](https://portal.azure.com) にある、お使いのクラスターの [概要] タブで、クライアントの接続エンドポイントを探すことができます。

**[発行]** をクリックします。

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) は、Service Fabric クラスター内のアプリケーションとノードを検査および管理するための Web ベースのツールです。 ブラウザーを開き、 http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ に移動して、アプリケーションのデプロイを進めます。 アプリケーションはデプロイされますが、クラスター ノードにイメージがダウンロードされるまではエラー状態となります (イメージのサイズによって時間がかかる場合があります)。![Error][1]

```Ready``` 状態になったら、アプリケーションの準備は完了しています。![準備完了][2]

ブラウザーを開き、 http://containercluster.westus2.cloudapp.azure.com:8081 に移動します。 "Hello World!" という見出しが ブラウザーに表示されます。

## <a name="clean-up"></a>クリーンアップ

クラスターの実行中は、料金が継続的に発生します。[クラスターの削除](service-fabric-cluster-delete.md)を検討してください。

コンテナー レジストリにイメージをプッシュした後は、開発コンピューターからローカルのイメージを削除できます。

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="windows-server-container-os-and-host-os-compatibility"></a>Windows Server コンテナーの OS とホスト OS の互換性

Windows Server コンテナーは、ホスト OS のすべてのバージョンで互換性はありません。 例:
 
- Windows Server バージョン 1709 を使用してビルドされた Windows Server コンテナーは、Windows Server バージョン 2016 を実行しているホスト上では機能しません。 
- Windows Server 2016 を使用してビルドされた Windows Server コンテナーは、Windows Server バージョン 1709 を実行しているホスト上で Hyper-V 分離モードでのみ機能します。 
- Windows Server 2016 を使用してビルドされた Windows Server コンテナーでは、Windows Server 2016 で実行されているホスト上でプロセス分離モードで実行しているときに、コンテナーの OS とホスト OS のリビジョンが同じであることを確認する必要がある場合があります。
 
詳細については、「[Windows コンテナーのバージョンの互換性](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility)」を参照してください。

コンテナーをビルドして Service Fabric クラスターにデプロイする場合は、ホスト OS とコンテナーの OS の互換性を考慮してください。 例:

- クラスター ノード上の OS と互換性のある OS のコンテナーを確実にデプロイする。
- コンテナー アプリに指定された分離モードが、デプロイ先のノードのコンテナーの OS のサポートと一致していることを確認する。
- クラスター ノードまたはコンテナーの OS のアップグレードが、互換性に及ぼす影響を検討する。 

次のプラクティスに従って、コンテナーが自分の Service Fabric クラスター上に正しくデプロイされていることを確認することをお勧めします。

- 自分の Docker イメージをタグ付けする明示的なイメージを使用して、コンテナーをビルドする Windows Server OS のバージョンを指定します。 
- 自分のアプリケーション マニフェスト ファイル内で [OS のタグ付け](#specify-os-build-specific-container-images)を使用して、アプリケーションがさまざまな Windows Server のバージョンとアップグレード間で互換性があることを確認します。

> [!NOTE]
> Service Fabric バージョン 6.2 以降では、Windows Server 2016 に基づいて Windows 10 ホストにコンテナーをローカルでデプロイすることができます。 Windows 10 では、アプリケーション マニフェストで設定されている分離モードに関係なく、コンテナーは HYPER-V 分離モードで実行されます。 詳細については、「[分離モードの構成](#configure-isolation-mode)」を参照してください。   
>
 
## <a name="specify-os-build-specific-container-images"></a>OS ビルド固有のコンテナー イメージを指定する 

Windows Server コンテナーは、OS の異なるバージョン間では互換性がない場合があります。 たとえば、Windows Server 2016 を使用してビルドした Windows Server コンテナーは、Windows Server バージョン 1709 ではプロセス分離モードで機能しません。 そのため、クラスター ノードが最新バージョンに更新されていると、以前のバージョンの OS を使用してビルドしたコンテナー サービスは失敗します。 ランタイムのバージョン 6.1 以降でこれを回避するため、Service Fabric では、コンテナーごとに複数の OS イメージを指定して、それらをアプリケーション マニフェストで OS のビルド バージョンにタグ付けできるようにしています。 OS のビルド バージョンを取得するには、Windows コマンド プロンプトで `winver` を実行します。 ノードの OS を更新する前に、アプリケーション マニフェストを更新し、OS のバージョンごとにイメージのオーバーライドを指定してください。 次のスニペットは、アプリケーション マニフェスト **ApplicationManifest.xml** で複数のコンテナー イメージを指定する方法を示しています。


```xml
      <ContainerHostPolicies> 
         <ImageOverrides> 
           <Image Name="myregistry.azurecr.io/samples/helloworldappDefault" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1701" Os="14393" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1709" Os="16299" /> 
         </ImageOverrides> 
      </ContainerHostPolicies> 
```
WIndows Server 2016 のビルド バージョンは 14393 であり、Windows Server バージョン 1709 のビルド バージョンは 16299 です。 サービス マニフェストは、引き続き、次に示すようにコンテナー サービスごとに 1 つのイメージのみを指定します。

```xml
<ContainerHost>
    <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName> 
</ContainerHost>
```

   > [!NOTE]
   > OS ビルド バージョンのタグ付け機能は、Windows 上の Service Fabric のみで使用可能です。
   >

VM 上の基になる OS がビルド 16299 (バージョン 1709) である場合、Service Fabric はその Windows Server バージョンに対応するコンテナー イメージを取得します。 アプリケーション マニフェストで、タグが付けられたコンテナー イメージと共にタグなしのコンテナー イメージも指定されている場合、Service Fabric は、タグなしのイメージを複数のバージョン間で動作するものとして扱います。 アップグレード中に問題が発生しないよう、コンテナー イメージには明示的にタグ付けしてください。

タグなしのコンテナー イメージは、ServiceManifest に指定されているイメージのオーバーライドとして機能します。 つまり "myregistry.azurecr.io/samples/helloworldappDefault" イメージは、ServiceManifest の ImageName に指定されている "myregistry.azurecr.io/samples/helloworldapp" をオーバーライドします。

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Service Fabric のアプリケーション マニフェストとサービス マニフェストの完全な例
この記事で使用される完全なサービス マニフェストとアプリケーション マニフェストは次のとおりです。

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
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
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

サービスの削除 (または別のノードへの移動) が開始された後でコンテナーが削除されるまでにランタイムが待機する期間を構成できます。 期間を構成すると、`docker stop <time in seconds>` コマンドがコンテナーに送信されます。  詳細については、[docker stop](https://docs.docker.com/engine/reference/commandline/stop/) の説明を参照してください。 待機する期間は `Hosting` セクションで指定します。 `Hosting` セクションは、クラスターの作成時、または構成をアップグレードするときに後で追加できます。 次のクラスター マニフェストのスニペットは、待機間隔を設定する方法を示しています。

```json
"fabricSettings": [
    ...,
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
]
```
既定の期間は 10 秒に設定されています。 この構成は動的であるため、クラスター上の構成のみをアップグレードするだけでタイムアウトが更新されます。 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>使用していないコンテナー イメージを削除するようにランタイムを構成する

使用していないコンテナー イメージをノードから削除するように Service Fabric クラスターを構成できます。 この構成により、ノード上に存在するコンテナー イメージが多すぎる場合にディスク領域を再取得できます。 この機能を有効にするには、次のスニペットに示すように、クラスター マニフェストの [Hosting](service-fabric-cluster-fabric-settings.md#hosting) セクションを更新します。 


```json
"fabricSettings": [
    ...,
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
]
```

削除してはならないイメージは `ContainerImagesToSkip` パラメーターに指定することができます。  


## <a name="configure-container-image-download-time"></a>コンテナー イメージのダウンロード時間を構成する

Service Fabric ランタイムはコンテナー イメージのダウンロードと抽出に 20 分を割り当てており、これは大部分のコンテナー イメージにとって十分な時間です。 大きなイメージの場合、またはネットワーク接続の速度が遅い場合、イメージのダウンロードおよび抽出が中止されるまでの待機時間を長くすることが必要になる場合があります。 このタイムアウトは、次のスニペットに示すように、クラスター マニフェストの **Hosting** セクションの **ContainerImageDownloadTimeout** 属性を使用して設定できます。

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
    }
]
```


## <a name="set-container-retention-policy"></a>コンテナーの保持ポリシーを設定する

コンテナーのスタートアップ エラーの診断を支援するために、Service Fabric (バージョン 6.1 以降) では、終了またはスタートアップに失敗したコンテナーの保持をサポートしています。 このポリシーは、次のスニペットで示すように、**ApplicationManifest.xml** ファイルで設定できます。

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

**ContainersRetentionCount** の設定で、エラーが発生したときに保持するコンテナーの数を指定します。 負の値が指定されている場合は、エラーが発生したすべてのコンテナーが保持されます。 **ContainersRetentionCount** 属性が指定されていない場合、コンテナーは保持されません。 **ContainersRetentionCount** 属性はアプリケーション パラメーターもサポートしているため、ユーザーはテスト クラスターと運用環境クラスターで別の値を指定できます。 コンテナー サービスが他のノードに移動することを防ぐためにこの機能を使用する場合は、配置の制約を使用して、コンテナー サービスの対象を特定のノードに制約してください。 この機能を使用して保持されるコンテナーは、手動で削除する必要があります。

## <a name="start-the-docker-daemon-with-custom-arguments"></a>カスタム引数で Docker デーモンを起動する

Service Fabric ランタイムの 6.2 バージョン以降では、カスタム引数を使って Docker デーモンを起動することができます。 カスタム引数が指定された場合、Service Fabric は、他の引数を一切 Docker エンジンに渡しませんが、`--pidfile` 引数は例外です。 したがって、`--pidfile` を引数として渡すことは避けてください。 また、Service Fabric が Docker デーモンと通信できるようにするために、Docker デーモンに引き続き既定の名前付きパイプ (Windows の場合) または UNIX ドメイン ソケット (Linux の場合) でリッスンさせるよう引数で指定する必要があります。 カスタム引数は、クラスター マニフェストの **Hosting** セクションの **ContainerServiceArguments** で渡します。その例を次のスニペットに示します。 
 

```json
"fabricSettings": [
    ...,
    { 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
    } 
]
```

## <a name="next-steps"></a>次の手順
* [Service Fabric でのコンテナー](service-fabric-containers-overview.md)の実行について確認します。
* [コンテナー内の .NET アプリケーションをデプロイする方法](service-fabric-host-app-in-a-container.md)に関するチュートリアルをご覧ください。
* Service Fabric の[アプリケーション ライフサイクル](service-fabric-application-lifecycle.md)について確認します。
* GitHub で [Service Fabric コンテナーのコード サンプル](https://github.com/Azure-Samples/service-fabric-containers)を確認します。

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[4]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[5]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
