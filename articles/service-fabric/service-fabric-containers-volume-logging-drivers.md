---
title: Service Fabric Azure Files ボリューム ドライバー (プレビュー) | Microsoft Docs
description: Service Fabric は、Azure Files を使用したコンテナーからのボリュームのバックアップをサポートしています。 これは現在プレビューの段階です。
services: service-fabric
documentationcenter: other
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/10/2018
ms.author: aljo, subramar
ms.openlocfilehash: b8012cbdad02995c3fc98a3ea1fa02a3a08bd2dc
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665901"
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Service Fabric Azure Files ボリューム ドライバー (プレビュー)
Azure Files ボリューム プラグインは、Docker コンテナーに [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) ベースのボリュームを提供する [Docker ボリューム プラグイン](https://docs.docker.com/engine/extend/plugins_volume/)です。 この Docker ボリューム プラグインは、Service Fabric クラスターにデプロイ可能な Service Fabric アプリケーションとしてパッケージ化されています。 その目的は、クラスターにデプロイされている他の Service Fabric コンテナー アプリケーション用に Azure ファイル ベースのボリュームを提供することです。

> [!NOTE]
> バージョン 6.4.571.9590 の Azure Files ボリューム プラグインは、このドキュメントで使用可能なプレビュー リリースです。 プレビュー リリースとして、運用環境での使用はサポートされて**いません**。
>

## <a name="prerequisites"></a>前提条件
* Windows バージョンの Azure Files ボリューム プラグインは、[Windows Server バージョン 1709](https://docs.microsoft.com/windows-server/get-started/whats-new-in-windows-server-1709)、[Windows 10 バージョン 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) 以降のオペレーティング システムでのみ動作します。 Linux バージョンの Azure Files ボリューム プラグインは、Service Fabric でサポートされているすべてのオペレーティング システムのバージョンで動作します。

* Azure Files ボリューム プラグインは、Service Fabric バージョン 6.2 以降でのみ機能します。

* [Azure Files ドキュメント](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) の指示に従って、Service Fabric コンテナー アプリケーションがボリュームとして使用するファイル共有を作成します。

* [Service Fabric モジュールと Powershell](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) または [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) をインストールしている必要があります。

* Htper-V コンテナーを使用している場合は、ARM テンプレート (Azure クラスター) または ClusterConfig.json (スタンドアロン クラスター) の ClusterManifest (ローカル クラスター) セクションまたは fabricSettings セクションに次のスニペットを追加する必要があります。 ボリューム名と、そのボリュームがクラスターでリッスンするするポートが必要です。 

ClusterManifest では、Hosting セクションに次のコードを追加する必要があります。 この例では、ボリューム名は **sfazurefile**、そのボリュームがクラスター上でリッスンするポートは **19100** です。  

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

ARM テンプレート (Azure デプロイの場合) または ClusterConfig.json (スタンドアロン デプロイの場合) の fabricSettings セクションに、次のスニペットを追加する必要があります。 

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


## <a name="deploy-the-service-fabric-azure-files-application"></a>Service Fabric Azure Files アプリケーションのデプロイ

コンテナーにボリュームを提供する Service Fabric アプリケーションは、次の[リンク](https://download.microsoft.com/download/C/0/3/C0373AA9-DEFA-48CF-9EBE-994CA2A5FA2F/AzureFilesVolumePlugin.6.4.571.9590.zip)からダウンロードできます。 アプリケーションは、[PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)、[CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle-sfctl)、または [FabricClient API](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient) を使用して、クラスターにデプロイできます。

1. コマンド ラインを使用して、ダウンロードされたアプリケーション パッケージのルート ディレクトリにディレクトリを変更します。

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. アプリケーション パッケージをイメージ ストアにコピーし、[ApplicationPackagePath] と [ImageStoreConnectionString] に適切な値を指定して以下のコマンドを実行します。

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

4. アプリケーションを作成します。以下のアプリケーションを作成するコマンドで、**ListenPort** アプリケーション パラメーターに注意してください。 このアプリケーション パラメーターに指定されたこの値は、Azure Files ボリューム プラグインが、Docker デーモンからの要求をリッスンするポートです。 アプリケーションに提供されるポートは、ClusterManifest 内の VolumePluginPorts と一致していて、クラスターまたはご自分のアプリケーションで使用するその他のすべてのポートと競合しないことを確認することが重要です。

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.4.571.9590 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.4.571.9590 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Windows Server 2016 Datacenter は、SMB マウントのコンテナーへのマッピングをサポートしていません ([Windows Server バージョン 1709 でのみサポートされています](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage))。 この制約があるため、1709 より古いバージョンでは、ネットワーク ボリュームのマッピングと Azure Files ボリューム ドライバーを使用できません。

### <a name="deploy-the-application-on-a-local-development-cluster"></a>ローカル開発クラスターにアプリケーションをデプロイする
Azure Files ボリューム プラグイン アプリケーションの既定のサービス インスタンス数は、-1 です。これは、クラスター内の各ノードにデプロイされたサービスのインスタンスがあることを意味します。 ただし、ローカル開発クラスターに Azure Files ボリューム プラグイン アプリケーションをデプロイする場合は、サービス インスタンス数を 1 と指定する必要があります。 これは、**InstanceCount** アプリケーション パラメーターを使用して実行できます。 したがって、ローカル開発クラスターに Azure Files ボリューム プラグイン アプリケーションをデプロイするコマンドは、次のようになります。

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.4.571.9590 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.4.571.9590 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>ボリュームを使用するようにアプリケーションを構成する
次のスニペットは、アプリケーションのアプリケーション マニフェストに、 Azure Files ベースのボリュームを指定する方法を示しています。 興味のある特定の要素は、**Volume** タグです。

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

Azure Files ボリューム プラグインのドライバー名は **sfazurefile** です。 この値を、アプリケーション マニフェストの **Volume** 要素の **Driver** 属性に設定します。

上記スニペットの **Volume** 要素では、Azure Files ボリューム プラグインに、次のタグが必要です。
- **Source** - ボリュームの名前です。 ユーザーは、ボリュームの名前として任意の名前を選択できます。
- **Destination** - このタグは、実行中のコンテナー内でボリュームがマップされている場所です。 そのため、Destination としてコンテナー内の既存の場所を指定することはできません

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
Service Fabric では、独自のカスタム [ボリューム](https://docs.docker.com/engine/extend/plugins_volume/)または[ログ](https://docs.docker.com/engine/admin/logging/overview/) ドライバーを使用することもできます。 Docker ボリューム/ログ ドライバーがクラスターにインストールされていない場合は、RDP/SSH プロトコルを使って手動でインストールできます。 これらのプロトコルによるインストールは、[仮想マシン スケール セット スタートアップ スクリプト](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/)または [SetupEntryPoint スクリプト](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model)を使って実行できます。

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

## <a name="next-steps"></a>次の手順
* ボリューム ドライバーを含むコンテナーのサンプルを参照するには、[Service Fabric コンテナーのサンプル](https://github.com/Azure-Samples/service-fabric-containers)をご覧ください
* Service Fabric クラスターにコンテナーをデプロイする方法については、[Service Fabric へのコンテナーのデプロイ](service-fabric-deploy-container.md)に関する記事をご覧ください
