---
title: "Service Fabric と Linux におけるコンテナーのデプロイ | Microsoft Docs"
description: "Service Fabric と、Docker コンテナーを使用してマイクロサービス アプリケーションをデプロイする方法。 この記事では、Service Fabric が提供するコンテナー用の機能と、クラスターに Docker コンテナー イメージをデプロイする方法について説明しています。"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/24/2017
ms.author: msfussell
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 01c0d7e8430df758749f7a524dd3b7771b24fac1
ms.lasthandoff: 03/29/2017


---
# <a name="deploy-a-docker-container-to-service-fabric"></a>Service Fabric への Docker コンテナーのデプロイ
> [!div class="op_single_selector"]
> * [Windows コンテナーのデプロイ](service-fabric-deploy-container.md)
> * [Docker コンテナーのデプロイ](service-fabric-deploy-container-linux.md)
>
>

この記事では、コンテナー化されたサービスを Linux 上の Docker コンテナーに構築する手順を紹介しています。

Service Fabric には、コンテナー化されたマイクロサービスで構成されたアプリケーションの構築に役立つ、いくつかのコンテナー機能が用意されています。 これのサービスは、コンテナー化されたサービスと呼ばれます。

機能は次のとおりです。

* コンテナー イメージのデプロイとアクティブ化
* リソース ガバナンス
* リポジトリの認証
* コンテナー ポートからホスト ポートへのマッピング
* コンテナー間での検出と通信
* 環境変数の構成と設定の機能

## <a name="packaging-a-docker-container-with-yeoman"></a>yeoman での Docker コンテナーのパッケージ化
Linux 上でコンテナーをパッケージ化する際は、yeoman テンプレートを使用するか、それとも[アプリケーション パッケージを手動で作成](#manually)するかを選択できます。

Service Fabric のアプリケーションには、アプリケーションの機能を提供する際にそれぞれ特定の役割を果たすコンテナーを 1 つ以上含めることができます。 Linux 用の Service Fabric SDK には、[Yeoman](http://yeoman.io/) ジェネレーターが含まれています。これを使用すると、アプリケーションを作成したり、コンテナー イメージを追加したりする作業が簡単になります。 Yeoman を使用し、Docker コンテナーを 1 つだけ含むアプリケーション *SimpleContainerApp* を作成してみましょう。 生成されたマニフェスト ファイルを編集することで、サービスは後から追加できます。

## <a name="install-docker-on-your-development-box"></a>開発マシンに Docker をインストールする

次のコマンドを実行して、Linux 開発マシンに Docker をインストールします (OS X 上で Vagrant イメージを使用している場合は、Docker はインストール済みです)。

```bash
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
```

## <a name="create-the-application"></a>アプリケーションを作成する
1. ターミナルで、「`yo azuresfguest`」と入力します。
2. フレームワークには **[コンテナー]** を選択します。
3. アプリケーションに名前を付けます (例: SimpleContainerApp)。
4. DockerHub リポジトリにあるコンテナー イメージの URL を指定します。 イメージ パラメーターは、<リポジトリ>/<イメージ名> の形式で指定してください。

![コンテナー用 Service Fabric Yeoman ジェネレーター][sf-yeoman]

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
5. アプリケーション インスタンスを削除し、その種類のアプリケーションの登録を解除するには、テンプレートに指定されているアンインストール スクリプトを使用します。

```bash
    ./uninstall.sh
```

アプリケーションの例については、[GitHub にある Service Fabric コンテナーのコード サンプルをご覧ください](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="adding-more-services-to-an-existing-application"></a>既存アプリケーションへのサービスの追加

`yo` を使用して作成したアプリケーションに別のコンテナー サービスを追加するには、次の手順を実行します。 

1. ディレクトリを既存アプリケーションのルートに変更します。  たとえば、Yeoman で作成したアプリケーションが `MyApplication` の場合は、`cd ~/YeomanSamples/MyApplication` です。
2. `yo azuresfguest:AddService` を実行します。

<a id="manually"></a>

## <a name="manually-package-and-deploy-a-container-image"></a>コンテナー イメージの手動によるパッケージ化およびデプロイ
コンテナー化されたサービスを手動でパッケージ化するプロセスは、次の手順に基づいています。

1. リポジトリにコンテナーを発行します。
2. パッケージ ディレクトリ構造を作成します。
3. サービス マニフェスト ファイルを編集します。
4. アプリケーション マニフェスト ファイルを編集します。

## <a name="deploy-and-activate-a-container-image"></a>コンテナー イメージのデプロイおよびアクティブ化
Service Fabric の [アプリケーション モデル](service-fabric-application-model.md)では、コンテナーは複数のサービス レプリカが配置されたアプリケーション ホストを表します。 コンテナーをデプロイしてアクティブ化するには、コンテナー イメージ名をサービス マニフェストの `ContainerHost` 要素に入力します。

サービス マニフェストで、エントリ ポイントの `ContainerHost` を追加します。 次に、コンテナー リポジトリとイメージの名前になるように `ImageName` を設定します。 次のマニフェストの一部では、`myrepo` というリポジトリから `myimage:v1` というコンテナーをデプロイする場合の例を示しています。

```xml
    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimage:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>
```

コンテナー内で実行されるコンマ区切りの一連のコマンドによりオプションの `Commands` 要素を指定することで、入力コマンドを指定することができます。

## <a name="understand-resource-governance"></a>リソース ガバナンスについて
リソース ガバナンスは、コンテナー機能の 1 つです。コンテナーがホスト上で使用できるリソースを制限します。 アプリケーション マニフェストで指定された `ResourceGovernancePolicy` は、サービス コード パッケージのリソース制限を宣言するために使用します。 次のリソースのリソースの制限を設定できます。

* メモリ
* MemorySwap
* CpuShares (CPU の相対的な重み)
* MemoryReservationInMB  
* BlkioWeight (BlockIO の相対的な重み)

> [!NOTE]
> 今後のリリースで、IOPS、読み取り/書き込み BPS などの特定のブロック IO 制限の指定がサポートされる予定です。
> 
> 

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500"
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="authenticate-a-repository"></a>リポジトリの認証
コンテナーをダウンロードするには、コンテナー リポジトリにサインイン資格情報を提供する必要がある場合があります。 アプリケーション マニフェストで指定されたサインイン資格情報は、イメージ リポジトリからコンテナー イメージをダウンロードする際に必要なサインイン情報または SSH キーを指定するのに使用されます。 次の例は、 *TestUser* というアカウントと、クリア テキスト (推奨*されません*) のパスワードを示しています。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

コンピューターにデプロイされた証明書を使用してパスワードを暗号化することをお勧めします。

次の例は、*MyCert* という証明書を使用してパスワードが暗号化された *TestUser* というアカウントを示しています。 `Invoke-ServiceFabricEncryptText` PowerShell コマンドを使用して、パスワードのシークレット暗号化テキストを作成できます。 詳細については、「[Service Fabric アプリケーションでのシークレットの管理](service-fabric-application-secret-management.md)」を参照してください。

パスワードの暗号化を解除するための証明書の秘密キーは、ローカル コンピューターにアウトオブバンド方式でデプロイされる必要があります (Azure では、Azure Resource Manager を使用します)。その後、Service Fabric によってコンピューターにサービス パッケージが展開されたときに、シークレットを暗号化解除できます。 アカウント名とシークレットを使用すると、コンテナーのリポジトリで認証できます。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping"></a>コンテナー ポートからホスト ポートへのマッピングの構成
アプリケーション マニフェストで `PortBinding` を指定することで、コンテナーとの通信に使用されるホスト ポートを構成できます。 このポートのバインドでは、サービスがコンテナー内部でリッスンしているポートをホスト上のポートにマップします。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-to-container-discovery-and-communication"></a>コンテナー間での検出と通信の構成
`PortBinding` ポリシーを使うことで、コンテナー ポートをサービス マニフェストの `Endpoint` にマップできます。 エンドポイント `Endpoint1` は固定ポート (たとえば、ポート 80) を指定できます。 ポートをまったく指定せずにいることもでき、その場合はクラスターのアプリケーションのポート範囲からランダムにポートが選択されます。

エンドポイントを指定する場合、Service Fabric は、サービス マニフェストの `Endpoint` タグを使用して、このエンドポイントをネーム サービスに自動的に発行することができます。 これにより、クラスター内で実行されているその他のサービスが解決のための REST クエリを使用してこのコンテナーを検出できます。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

ネーム サービスに登録すると、[リバース プロキシ](service-fabric-reverseproxy.md)を使用して、コンテナー内のコードでコンテナー間の通信を簡単に行うことができます。 通信は、リバース プロキシ HTTP リスニング ポートおよび通信先のサービスの名前を環境変数として設定することで実行します。 詳細については、次のセクションを参照してください。 

## <a name="configure-and-set-environment-variables"></a>環境変数の構成と設定
環境変数は、サービス マニフェストのコード パッケージごとに指定できます。これは、コンテナーにデプロイされたサービスでも、プロセス実行可能ファイルまたはゲスト実行可能ファイルとしてデプロイされたサービスでも実施可能です。 これらの環境変数の値は、アプリケーション マニフェスト内で個別に上書きできるほか、デプロイの最中にアプリケーションのパラメーターとして指定することもできます。

次のサービス マニフェストの XML スニペットは、コード パッケージ用の環境変数を指定する方法の例です。

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>
```

これらの環境変数は、アプリケーション マニフェスト レベルで上書きできます。

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>
```

前の例では、`HttpGateway` 環境変数に対して明示的な値 (19000) を指定しています。一方、`BackendServiceName` パラメーターの値は `[BackendSvc]` アプリケーション パラメーターによって設定します。 これらの設定により、アプリケーションのデプロイ時に `BackendServiceName` の値を指定できるようになり、マニフェスト内の固定値もなくすことができます。

## <a name="complete-examples-for-application-and-service-manifest"></a>アプリケーション マニフェストとサービス マニフェストのすべてのコード例

アプリケーション マニフェスト ファイルの例を次に示します。

```xml
    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>
```

次にサービス マニフェストの例 (上記のアプリケーション マニフェストで指定されたもの) を示します。

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless front end in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Endpoints>
                <Endpoint Name="Endpoint1" UriScheme="http" Port="80" Protocol="http"/>
            </Endpoints>
        </Resources>
    </ServiceManifest>
```

## <a name="next-steps"></a>次のステップ
コンテナー化サービスをデプロイしたので、ライフ サイクルを管理する方法について、「[Service Fabric アプリケーションのライフ サイクル](service-fabric-application-lifecycle.md)」を参照してください。

* [Service Fabric とコンテナーの概要](service-fabric-containers-overview.md)
* [Azure CLI を使用した Service Fabric クラスターの対話操作](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman1.png

