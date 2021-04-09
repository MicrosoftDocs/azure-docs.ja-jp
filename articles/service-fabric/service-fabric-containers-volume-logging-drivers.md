---
title: Service Fabric 用の Azure Files ボリューム ドライバー
description: Service Fabric は、Azure Files を使用したコンテナーからのボリュームのバックアップをサポートしています。
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: a5125dbd88a2fe236196c427244f1311d9b73b9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86247695"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Service Fabric 用の Azure Files ボリューム ドライバー

Azure Files ボリューム ドライバーは、Docker コンテナーに [Azure Files](../storage/files/storage-files-introduction.md) ベースのボリュームを提供する [Docker ボリューム プラグイン](https://docs.docker.com/engine/extend/plugins_volume/)です。 これは Service Fabric アプリケーションとしてパッケージ化されており、Service Fabric クラスターにデプロイして、クラスター内の他の Service Fabric コンテナー アプリケーションにボリュームを提供できます。

> [!NOTE]
> Azure Files ボリューム プラグインのバージョン 6.5.661.9590 が一般公開されています。
>

## <a name="prerequisites"></a>前提条件
* Windows バージョンの Azure Files ボリューム プラグインは、[Windows Server バージョン 1709](/windows-server/get-started/whats-new-in-windows-server-1709)、[Windows 10 バージョン 1709](/windows/whats-new/whats-new-windows-10-version-1709) 以降のオペレーティング システムでのみ動作します。

* Linux バージョンの Azure Files ボリューム プラグインは、Service Fabric でサポートされているすべてのオペレーティング システムのバージョンで動作します。

* Azure Files ボリューム プラグインは、Service Fabric バージョン 6.2 以降でのみ機能します。

* [Azure Files ドキュメント](../storage/files/storage-how-to-create-file-share.md) の指示に従って、Service Fabric コンテナー アプリケーションがボリュームとして使用するファイル共有を作成します。

* [Service Fabric モジュールと Powershell](./service-fabric-get-started.md) または [SFCTL](./service-fabric-cli.md) をインストールしている必要があります。

* Hyper-V コンテナーを使用している場合は、Azure Resource Manager テンプレート (Azure クラスター) または ClusterConfig.json (スタンドアロン クラスター) の ClusterManifest (ローカル クラスター) セクションまたは fabricSettings セクションに、次のスニペットを追加する必要があります。

ClusterManifest では、Hosting セクションに次のコードを追加する必要があります。 この例では、ボリューム名は **sfazurefile**、そのボリュームがクラスター上でリッスンするポートは **19100** です。 これらは、クラスターの正しい値で置き換えてください。

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

Azure Resource Manager テンプレート (Azure デプロイの場合) または ClusterConfig.json (スタンドアロン デプロイの場合) の fabricSettings セクションに、次のスニペットを追加する必要があります。 ここでも、ボリューム名とポート値を独自の値で置き換えます。

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Service Fabric Azure Files ボリューム ドライバーを使用してサンプル アプリケーションをデプロイする

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>提供された Powershell スクリプトを介した Azure Resource Manager の使用 (推奨)

クラスターが Azure ベースの場合は、Azure Resource Manager アプリケーション リソース モデルを使用して、Azure にアプリケーションをデプロイすることをお勧めします。その方が使いやすいし、インフラストラクチャをコードとして維持するモデルに移行しやすくなるためです。 この方法であれば、Azure Files ボリューム ドライバーのアプリ バージョンを追跡する必要がなくなります。 また、サポートされている OS ごとに個別の Azure Resource Manager テンプレートを維持することもできます。 このスクリプトは、Azure Files アプリケーションの最新バージョンをデプロイすることを前提としており、OS の種類、クラスター サブスクリプション ID、およびリソース グループのパラメーターを受け取ります。 このスクリプトは、[Service Fabric のダウンロード サイト](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip)からダウンロードできます。 このスクリプトにより、自動的に ListenPort が 19100 に設定されます。この値は、Azure Files ボリューム プラグインが、Docker デーモンからの要求をリッスンするポートです。 "listenPort" という名前のパラメーターを追加することによって、これを変更することができます。 そのポートが、クラスターまたはアプリケーションで使用する他のどのポートとも競合しないことを確認します。
 

Windows 用の Azure Resource Manager デプロイ コマンドは次のとおりです。
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Linux 用の Azure Resource Manager デプロイ コマンドは次のとおりです。
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

スクリプトが正常に実行されたら、[アプリケーションの構成](#configure-your-applications-to-use-the-volume)に関するセクションに進むことができます。


### <a name="manual-deployment-for-standalone-clusters"></a>スタンドアロン クラスターの手動デプロイ

コンテナーにボリュームを提供する Service Fabric アプリケーションは、[Service Fabric のダウンロード サイト](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip)からダウンロードできます。 アプリケーションは、[PowerShell](./service-fabric-deploy-remove-applications.md)、[CLI](./service-fabric-application-lifecycle-sfctl.md)、または [FabricClient API](./service-fabric-deploy-remove-applications-fabricclient.md) を使用して、クラスターにデプロイできます。

1. コマンド ラインを使用して、ダウンロードされたアプリケーション パッケージのルート ディレクトリにディレクトリを変更します。

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. 次に、アプリケーション パッケージをイメージ ストアにコピーし、[ApplicationPackagePath] と [ImageStoreConnectionString] に適切な値を指定します。

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. アプリケーションの種類を登録する

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. **ListenPort** アプリケーション パラメーター値に注意して、アプリケーションを作成します。 この値は、Azure Files ボリューム プラグインが、Docker デーモンからの要求をリッスンするポートです。 アプリケーションに提供されるポートは、ClusterManifest 内の VolumePluginPorts と一致していて、クラスターまたはご自分のアプリケーションで使用する他のどのポートとも競合していないことを確認する必要があります。

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Windows Server 2016 Datacenter は、SMB マウントのコンテナーへのマッピングをサポートしていません ([Windows Server バージョン 1709 でのみサポートされています](/virtualization/windowscontainers/manage-containers/container-storage))。 この制約があるため、1709 より古いバージョンでは、ネットワーク ボリュームのマッピングと Azure Files ボリューム ドライバーを使用できません。

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>ローカル開発クラスターにアプリケーションをデプロイする
[上記](#manual-deployment-for-standalone-clusters)の手順 1. から 3. に従います。

 Azure Files ボリューム プラグイン アプリケーションの既定のサービス インスタンス数は、-1 です。これは、クラスター内の各ノードにデプロイされたサービスのインスタンスがあることを意味します。 ただし、ローカル開発クラスターに Azure Files ボリューム プラグイン アプリケーションをデプロイする場合は、サービス インスタンス数を 1 と指定する必要があります。 これは、**InstanceCount** アプリケーション パラメーターを使用して実行できます。 したがって、ローカル開発クラスターに Azure Files ボリューム プラグイン アプリケーションを作成するコマンドは、次のようになります。

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>ボリュームを使用するようにアプリケーションを構成する
次のスニペットは、アプリケーションのアプリケーション マニフェスト ファイルに、Azure Files ベースのボリュームを指定する方法を示しています。 興味のある特定の要素は、**Volume** タグです。

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

Azure Files ボリューム プラグインのドライバー名は **sfazurefile** です。 この値を、アプリケーション マニフェストの **Volume** タグ要素の **Driver** 属性に設定します。

上記のスニペットの **Volume** タグでは、Azure Files ボリューム プラグインに次の属性が必要です。
- **Source** - ボリュームの名前です。 ユーザーは、ボリュームの名前として任意の名前を選択できます。
- **Destination** - この属性は、実行中のコンテナー内でボリュームがマップされている場所です。 そのため、Destination としてコンテナー内の既存の場所を指定することはできません

上記スニペットの **DriverOption** 要素に示すように、Azure Files ボリューム プラグインは、次のドライバー オプションをサポートしています。
- **shareName** - コンテナーのボリュームを提供する Azure Files ファイル共有の名前。
- **storageAccountName** - Azure Files ファイル共有を含む Azure Storage アカウントの名前。
- **storageAccountKey** - Azure Files ファイル共有を含む Azure Storage アカウントのアクセス キー。
- **storageAccountFQDN** - ストレージ アカウントに関連付けられたドメイン名。 storageAccountFQDN が指定されない場合、ドメイン名は既定のサフィックス (.file.core.windows.net) と storageAccountName を使用して形成されます。  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>独自のボリュームまたはログ ドライバーの使用
Service Fabric では、独自のカスタム [ボリューム](https://docs.docker.com/engine/extend/plugins_volume/)または[ログ](https://docs.docker.com/engine/admin/logging/overview/) ドライバーを使用することもできます。 Docker ボリューム/ログ ドライバーがクラスターにインストールされていない場合は、RDP/SSH プロトコルを使って手動でインストールできます。 これらのプロトコルによるインストールは、[仮想マシン スケール セット スタートアップ スクリプト](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/)または [SetupEntryPoint スクリプト](./service-fabric-application-model.md)を使って実行できます。

[Azure 用 Docker ボリューム ドライバー](https://docs.docker.com/docker-for-azure/persistent-data-volumes/)をインストールするスクリプトの例を次に示します。

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

アプリケーションで、インストールしたボリュームまたはログ ドライバーを使用するには、アプリケーション マニフェストの **ContainerHostPolicies** の下にある **Volume** 要素と **LogConfig** 要素に適切な値を指定する必要があります。

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

ボリューム プラグインを指定すると、Service Fabric は指定されたパラメーターを使って自動的にボリュームを作成します。 **Volume** 要素の **Source** タグはボリュームの名前を示し、**Driver** タグはボリュームのドライバー プラグインを指定します。 **Destination** タグは、実行中のコンテナー内で **Source** がマップされている場所です。 そのため、Destination としてコンテナー内の既存の場所を指定することはできません。 次のように、**DriverOption** タグを使ってオプションを指定できます。

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

前述のマニフェスト スニペットに示したように、ボリュームに関してはアプリケーションのパラメーターがサポートされます (使用例については `MyStorageVar` を探してください)。

Docker ログ ドライバーを指定する場合は、クラスター内のログを処理するエージェント (またはコンテナー) をデプロイする必要があります。 **DriverOption** タグを使って、ログ ドライバーのオプションを指定できます。

## <a name="next-steps"></a>次のステップ
* ボリューム ドライバーを含むコンテナーのサンプルを参照するには、[Service Fabric コンテナーのサンプル](https://github.com/Azure-Samples/service-fabric-containers)をご覧ください
* Service Fabric クラスターにコンテナーをデプロイする方法については、[Service Fabric へのコンテナーのデプロイ](./service-fabric-get-started-containers.md)に関する記事をご覧ください
