---
title: FabricClient を使用した Azure Service Fabric のデプロイ
description: FabricClient API を使用して Service Fabric にアプリケーションをデプロイおよび削除します。
ms.topic: conceptual
ms.date: 01/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 343a37c983b1d64a4b1986913d9d6fd648a113fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785551"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>FabricClient を使用してアプリケーションのデプロイと削除を実行する
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient API](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

[アプリケーションの種類をパッケージ化][10]した後は、Azure Service Fabric クラスターにデプロイできる状態になっています。 デプロイには、次の 3 つの手順が含まれます。

1. アプリケーション パッケージのイメージ ストアへのアップロード
2. アプリケーションの種類を登録する
3. イメージ ストアからのアプリケーション パッケージの削除
4. アプリケーション インスタンスを作成する

アプリケーションをデプロイし、インスタンスをクラスターで実行した後、アプリケーションのインスタンスとそのアプリケーションの種類を削除することができます。 以降の手順に従って、クラスターからアプリケーションを完全に削除します。

1. 実行中のアプリケーションのインスタンスを削除する
2. アプリケーションの種類が不要になったら、その登録を解除する

Visual Studio を使ってローカルの開発クラスターでアプリケーションのデプロイとデバッグを行う場合、前述のすべての手順は、PowerShell スクリプトによって自動的に処理されます。  このスクリプトは、アプリケーション プロジェクトの *Scripts* フォルダーにあります。 この記事では、このスクリプトが実行する内容の背景を説明し、Visual Studio の外部で同じ操作を実行できるようにします。 
 
## <a name="connect-to-the-cluster"></a>クラスターに接続する
この記事のいずれかのコード例を実行する前に、[FabricClient](/dotnet/api/system.fabric.fabricclient) インスタンスを作成してクラスターに接続します。 ローカル開発クラスターやリモート クラスター、または Azure Active Directory、X509 証明書、Windows Active Directory で保護されたクラスターに接続する例については、「[セキュリティ保護されたクラスターに接続する](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis)」をご覧ください。 ローカル開発クラスターに接続するには、次の例を実行します。

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>アプリケーション パッケージをアップロードする
*MyApplication* という名前のアプリケーションを Visual Studio でビルドしてパッケージするとします。 ApplicationManifest.xml に既定で表示されるアプリケーション タイプ名は、"MyApplicationType" です。  アプリケーション パッケージ (必要なアプリケーション マニフェストとサービス マニフェスト、コード/構成/データ パッケージを含む) は、*C:\Users\&lt;username&gt;\Documents\Visual Studio 2019\Projects\MyApplication\MyApplication\pkg\Debug* にあります。

アプリケーション パッケージをアップロードすると、そのパッケージは内部 Service Fabric コンポーネントがアクセスできる場所に保存されます。 Service Fabric は、アプリケーション パッケージの登録時に、アプリケーション パッケージを検証します。 ただし、アプリケーション パッケージをローカルで検証する (つまり、アップロード前に検証する) 場合は、[Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) コマンドレットを使用します。

[CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API はアプリケーション パッケージをクラスター イメージ ストアにアップロードします。 

アプリケーション パッケージが大きい場合や含まれるファイルの数が多い場合は、PowerShell を使用してアプリケーション パッケージを[圧縮](service-fabric-package-apps.md#compress-a-package)し、イメージストアにコピーできます。 圧縮により、ファイルのサイズを小さくし、ファイル数を減らすことができます。

イメージ ストアとイメージ ストア接続文字列に関する補足情報は、[イメージ ストア接続文字列の理解](service-fabric-image-store-connection-string.md)に関するページを参照してください。

## <a name="register-the-application-package"></a>アプリケーション パッケージを登録する
アプリケーション マニフェストで宣言されたアプリケーションの種類とバージョンは、アプリケーション パッケージを登録すると、利用できるようになります。 システムは、前の手順でアップロードされたパッケージを読み取り、パッケージを検証し、パッケージのコンテンツを処理し、処理済みのパッケージを内部システムの場所にコピーします。  

[ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API はアプリケーションの種類をクラスターに登録し、デプロイできるようにします。

[GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) API は正常に登録されたすべてのアプリケーションの種類に関する情報を提供します。 この API を使用して、登録がいつ完了したかを判断できます。

## <a name="remove-an-application-package-from-the-image-store"></a>イメージ ストアからのアプリケーション パッケージの削除
アプリケーションが正常に登録されたら、アプリケーション パッケージを削除することをお勧めします。  イメージ ストアからアプリケーション パッケージを削除すると、システム リソースが解放されます。  使用されていないアプリケーション パッケージを保持すると、ディスク ストレージが消費され、アプリケーションのパフォーマンスの問題につながります。 アプリケーション パッケージをイメージ ストアから削除するには、[RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) API を使用します。

## <a name="create-an-application-instance"></a>アプリケーション インスタンスを作成する
[CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) API を使用して、正常に登録されているアプリケーションの種類を指定し、アプリケーションをインスタンス化することができます。 各アプリケーションの名前は、*"fabric:"* スキームで開始され、(クラスター内の) 各アプリケーション インスタンスに対して一意でなければなりません。 ターゲット アプリケーションの種類のアプリケーション マニフェストに定義されている既定のサービスも作成されます。

登録されたアプリケーションの種類の任意のバージョンに対して、複数のアプリケーション インスタンスを作成できます。 各アプリケーション インスタンスは分離して実行され、独自の作業ディレクトリと一連のプロセスを使用します。

クラスターで実行されている名前付きのアプリケーションとサービスを確認するには、[GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) API と [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) API を実行します。

## <a name="create-a-service-instance"></a>サービス インスタンスを作成する
[CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API を使用して、サービスの種類からサービスをインスタンス化できます。  サービスがアプリケーション マニフェストで既定のサービスとして宣言されている場合、アプリケーションがインスタンス化されるときに、サービスがインスタンス化されます。  既にインスタンス化されているサービスで [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API を呼び出すと、型が FabricException の例外が返されます。 例外には、FabricErrorCode.ServiceAlreadyExists の値を持つエラー コードが含まれます。

## <a name="remove-a-service-instance"></a>サービス インスタンスを削除する
サービス インスタンスが不要になったときは、[DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) API を呼び出して実行中のアプリケーション インスタンスから削除できます。  

> [!WARNING]
> この操作は元に戻すことはできず、サービスの状態を復元することはできません。

## <a name="remove-an-application-instance"></a>アプリケーション インスタンスの削除
アプリケーション インスタンスが不要になったときは、[DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) API を使用して名前を指定して実行すれば完全に削除できます。 [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) はアプリケーションに属するすべてのサービスを自動的に削除します。その結果、すべてのサービスの状態が完全に削除されます。

> [!WARNING]
> この操作は元に戻せません。また、アプリケーションの状態を復元できません。

## <a name="unregister-an-application-type"></a>アプリケーションの種類の登録解除
特定のバージョンのアプリケーションの種類が不要になった場合は、[Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) API を使用して、その特定のバージョンのアプリケーションの種類の登録を解除する必要があります。 使用していないバージョンのアプリケーションの種類の登録を解除すると、イメージ ストアで使用されているストレージ領域が解放されます。 アプリケーションの種類のバージョンは、そのアプリケーションの種類のバージョンに対してアプリケーションがインスタンス化されていない限り、登録を解除できます。 また、アプリケーションの種類は、アプリケーションの種類のそのバージョンを参照している保留中のアプリケーション アップグレードを持つことはできません。

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage がImageStoreConnectionString を求める
Service Fabric SDK 環境には、適切な既定値を事前に設定しておく必要があります。 ただし、すべてのコマンドの ImageStoreConnectionString が、Service Fabric クラスターによって使用されている値に一致していなければならない場合もあります。 この ImageStoreConnectionString は [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) コマンドと Get-ImageStoreConnectionStringFromClusterManifest コマンドを使用して取得したクラスター マニフェストで見つけることができます。

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

Service Fabric SDK PowerShell モジュールの一部である **Get-ImageStoreConnectionStringFromClusterManifest** コマンドレットは、イメージ ストアの接続文字列の取得に使用します。  SDK モジュールをインポートするには、次のコマンドを実行します。

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


ImageStoreConnectionString は、クラスター マニフェスト内にあります。

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

イメージ ストアとイメージ ストア接続文字列に関する補足情報は、[イメージ ストア接続文字列の理解](service-fabric-image-store-connection-string.md)に関するページを参照してください。

### <a name="deploy-large-application-package"></a>大規模なアプリケーション パッケージをデプロイする
問題: (GB 単位の) 大規模なアプリケーション パッケージで [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API がタイムアウトする。
次の操作を試してください。
- `timeout` パラメーターを使用して、[CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) メソッドのタイムアウト値を大きくします。 既定では、タイムアウトは 30 分です。
- ソース コンピューターとクラスターの間のネットワーク接続を確認します。 接続が低速な場合は、ネットワーク接続が高速なコンピューターを使用することを検討してください。
クライアント コンピューターがクラスターとは別のリージョンにある場合は、クラスターと同じかより近いリージョンにあるクライアント コンピューターの使用を検討してください。
- 外部で調整されていないかどうかを確認してください。 たとえば、イメージ ストアが Azure Storage を使用するように構成されている場合、アップロードが調整される可能性があります。

問題: パッケージのアップロードが正常に完了したが、[ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API がタイムアウトする。次の操作を試してください。
- イメージ ストアにコピーする前に[パッケージを圧縮](service-fabric-package-apps.md#compress-a-package)します。
圧縮によってファイル サイズが小さくなりファイル数が減るため、トラフィックの量と Service Fabric が実行する必要のある処理が減ります。 (圧縮時間を含めた場合は特に) アップロード操作が遅くなる場合がありますが、アプリケーションの種類の登録と登録解除は高速になります。
- `timeout` パラメーターを使用して、[ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API のタイムアウト値を大きくします。

### <a name="deploy-application-package-with-many-files"></a>多数のファイルを含むアプリケーション パッケージをデプロイする
問題: (数千個の) 多数のファイルを持つアプリケーション パッケージに対する [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) がタイムアウトする。
次の操作を試してください。
- イメージ ストアにコピーする前に[パッケージを圧縮](service-fabric-package-apps.md#compress-a-package)します。 圧縮により、ファイル数を減らすことができます。
- `timeout` パラメーターを使用して、[ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) メソッドのタイムアウト値を大きくします。

## <a name="code-example"></a>コードの例
次の例では、アプリケーション パッケージをイメージ ストアにコピーし、アプリケーションの種類をプロビジョニングします。 その後、アプリケーション インスタンスを作成し、サービス インスタンスを作成します。 最後に、アプリケーション インスタンスを削除し、アプリケーションの種類のプロビジョニングを解除し、イメージ ストアからアプリケーション パッケージを削除します。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2019\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>次のステップ
[Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)

[Service Fabric の正常性の概要](service-fabric-health-introduction.md)

[Service Fabric サービスの診断とトラブルシューティング](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric のアプリケーションのモデル化](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
