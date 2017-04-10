---
title: "Service Fabric アプリケーションのデプロイ | Microsoft Docs"
description: "Service Fabric のアプリケーションをデプロイおよび削除する方法"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 4e3840f68c93998a52fa2956c2ea9d0976e0f627
ms.lasthandoff: 03/28/2017


---
# <a name="deploy-and-remove-applications-using-powershell"></a>PowerShell を使用してアプリケーションのデプロイと削除を実行する
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> 
> 

<br/>

[アプリケーションの種類をパッケージ化][10]した後は、Azure Service Fabric クラスターにデプロイできる状態になっています。 デプロイには、次の 3 つの手順が含まれます。

1. アプリケーション パッケージのイメージ ストアへのアップロード
2. アプリケーションの種類を登録する
3. アプリケーション インスタンスを作成する

アプリのデプロイ後、インスタンスがクラスターで実行されているときに、アプリのインスタンスとそのアプリケーションの種類を削除することができます。 クラスターからアプリを完全に削除するには、次の手順が必要となります。

1. 実行中のアプリケーションのインスタンスを削除する
2. アプリケーションの種類が不要になったら、その登録を解除する
3. イメージ ストアからのアプリケーション パッケージの削除

[Visual Studio を使用してローカルの開発クラスターでアプリケーションのデプロイとデバッグを行う](service-fabric-publish-app-remote-cluster.md)場合、前述のすべての手順は、PowerShell スクリプトによって自動的に処理されます。  このスクリプトは、アプリケーション プロジェクトの *Scripts* フォルダーにあります。 この記事では、これらのスクリプトが実行する内容の背景を説明し、Visual Studio の外部で同じ操作を実行できるようにします。 
 
## <a name="connect-to-the-cluster"></a>クラスターへの接続
この記事の PowerShell コマンドを実行する前に、必ず最初に [Connect-ServiceFabricCluster](/powershell/servicefabric/vlatest/connect-servicefabriccluster) で Service Fabric クラスターに接続してください。 ローカル開発クラスターに接続するには、次の手順を実行します。

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Azure Active Directory、X509 証明書、Windows Active Directory で保護されたクラスターまたはリモート クラスターに接続する例については、「[セキュリティ保護されたクラスターに接続する](service-fabric-connect-to-secure-cluster.md)」をご覧ください。

## <a name="upload-the-application-package"></a>アプリケーション パッケージをアップロードする
アプリケーション パッケージをアップロードすると、そのパッケージは内部 Service Fabric コンポーネントがアクセスできる場所に保存されます。
アプリケーション パッケージをローカルで検証する場合は、[Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) コマンドレットを使用します。

アプリケーション パッケージは、[Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) コマンドでクラスター イメージ ストアにアップロードします。
Service Fabric SDK PowerShell モジュールの一部である **Get-ImageStoreConnectionStringFromClusterManifest** コマンドレットは、イメージ ストアの接続文字列の取得に使用します。  SDK モジュールをインポートするには、次のコマンドを実行します。

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

*MyApplication* という名前のアプリを Visual Studio でビルドしてパッケージするとします。 ApplicationManifest.xml に既定で表示されるアプリケーション タイプ名は、"MyApplicationType" です。  アプリケーション パッケージ (必要なアプリケーション マニフェストとサービス マニフェスト、コード/構成/データ パッケージを含む) は、*C:\Users\username\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug* に格納されます。 

アプリケーション パッケージの内容を一覧表示するには、次のコマンドを使用します。

```powershell
PS C:\> $path = 'C:\Users\user\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

アプリケーション パッケージが大きい場合や含まれるファイルの数が多い場合は、アプリケーション パッケージを[圧縮](service-fabric-package-apps.md#compress-a-package)できます。 圧縮により、ファイルのサイズを小さくし、ファイル数を減らすことができます。
その副次的な作用として、アプリケーションの種類の登録と登録解除が高速になります。 現時点で、特にパッケージを圧縮する時間を含めると、アップロード操作が遅くなることがあります。 

パッケージを圧縮するには、同じ [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) コマンドを使用します。 圧縮は、`SkipCopy` フラグを使用することによってアップロードとは別に実行することも、アップロード操作と一緒に実行することもできます。 圧縮済みのパッケージに圧縮を適用した場合、何の操作も行われません。
圧縮されているパッケージの圧縮を解除するには、同じ [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) コマンドを `UncompressPackage` スイッチと共に使用します。

次のコマンドレットでは、パッケージをイメージ ストアにコピーせずに圧縮しています。 このパッケージには、`Code` パッケージと `Config` パッケージの zip 圧縮されたファイルが含まれています。 アプリケーション マニフェストとサービス マニフェストは、多くの内部操作 (パッケージ共有、特定の検証のためのアプリケーションの種類名とバージョンの抽出など) に必要なため、zip 圧縮されません。 マニフェストを zip 圧縮すると、これらの操作が非効率的になります。

```
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

大規模なアプリケーション パッケージでは、圧縮に時間がかかります。 最良の結果を得るには、高速の SSD ドライブを使用してください。 圧縮時間と圧縮後のパッケージ サイズもパッケージの内容によって異なります。
例として、いくつかのパッケージの圧縮に関する統計情報を次に示します。ここには、初期パッケージ サイズ、圧縮後のパッケージ サイズ、および圧縮時間が示されています。

|初期サイズ (MB)|ファイル数|圧縮時間|圧縮後のパッケージ サイズ (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1,000|00:01:04.3775554|1,231|
|5,012|100|00:02:45.2951288|3,074|

圧縮したパッケージは、必要に応じて 1 つまたは複数の Service Fabric クラスターにアップロードできます。 デプロイ メカニズムは、圧縮されているパッケージでも圧縮されていないパッケージでも変わりません。 パッケージが圧縮されている場合、パッケージはクラスター イメージ ストアに格納され、アプリケーションが実行される前にノード上で圧縮が解除されます。


以下の例では、先ほどのパッケージをイメージ ストアの "MyApplicationV1" という名前のフォルダーにアップロードしています。

```powershell
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
```

*-ApplicationPackagePathInImageStore* パラメーターを指定しなかった場合は、イメージ ストアの "Debug" フォルダーにアプリ パッケージがコピーされます。

パッケージのアップロードにかかる時間は、さまざまな要素に応じて変わってきます。 これらの要素として、パッケージに含まれるファイルの数、パッケージ サイズ、ファイル サイズなどがあります。 ソース コンピューターと Service Fabric クラスターの間のネットワーク速度もアップロード時間に影響を与えます。 [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) の既定のタイムアウトは 30 分です。
説明した要素に応じて、タイムアウトを長くすることが必要になる場合があります。 コピーの呼び出しでパッケージを圧縮する場合は、圧縮時間も考慮する必要があります。

イメージ ストアとイメージ ストア接続文字列に関する補足情報は、[イメージ ストア接続文字列の理解](service-fabric-image-store-connection-string.md)に関するページを参照してください。

## <a name="register-the-application-package"></a>アプリケーション パッケージを登録する
アプリケーション マニフェストで宣言されたアプリケーションの種類とバージョンは、アプリケーション パッケージを登録すると、利用できるようになります。 システムは、前の手順でアップロードされたパッケージを読み取り、パッケージを検証し、パッケージのコンテンツを処理し、処理済みのパッケージを内部システムの場所にコピーします。  

アプリケーションの種類をクラスターに登録し、デプロイできる状態にするには、[Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) コマンドレットを実行します。

```powershell
PS C:\> Register-ServiceFabricApplicationType MyApplicationV1
Register application type succeeded
```

"MyApplicationV1" は、アプリ パッケージが格納されるイメージ ストア内のフォルダーです。 アプリケーション タイプ名 "MyApplicationType"、バージョン "1.0.0" (どちらもアプリケーション マニフェストに記述されます) としてアプリケーションの種類がクラスターに登録されます。

[Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) コマンドは、アプリケーション パッケージがシステムによって正常に登録された場合にのみ戻ります。 登録の所要時間は、アプリケーション パッケージのサイズと内容によって異なります。 必要に応じて、**-TimeoutSec** パラメーターを使用して、より長いタイムアウトを指定できます (既定のタイムアウトは 60 秒)。

アプリケーション パッケージが大規模な場合やタイムアウトが発生する場合は、**-Async** パラメーターを使用します。 このコマンドを実行すると、クラスターが登録コマンドを受け入れたときに制御が返されます。処理は必要に応じて続行されます。
[Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) コマンドは、正常に登録されたアプリケーションの種類の全バージョンとその登録状態を一覧表示します。 このコマンドを使用して、登録がいつ完了したかを判断できます。

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="create-the-application"></a>アプリケーションを作成する
[New-ServiceFabricApplication](/powershell/servicefabric/vlatest/new-servicefabricapplication) コマンドレットでは、正常に登録されているアプリケーションの種類のバージョンを指定して、アプリケーションをインスタンス化することができます。 各アプリケーションの名前は、 *fabric:* スキームで開始され、各アプリケーション インスタンスに対して一意でなければなりません。 ターゲット アプリケーションの種類のアプリケーション マニフェストに定義されている既定のサービスも作成されます。

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
登録されたアプリケーションの種類の任意のバージョンに対して、複数のアプリケーション インスタンスを作成できます。 各アプリケーション インスタンスは分離して実行され、独自の作業ディレクトリとプロセスを使用します。

クラスターで実行されている名前付きのアプリとサービスを確認するには、それぞれ [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) コマンドレットと [Get-ServiceFabricService](/powershell/servicefabric/vlatest/get-servicefabricservice) コマンドレットを実行します。

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>アプリケーションの削除
アプリケーション インスタンスが不要になったときは、[Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) コマンドレットの引数に名前を指定して実行すれば完全に削除できます。 [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) を実行すると、アプリケーションに属するサービスもすべて自動的に削除されます。その結果、すべてのサービスの状態が完全に削除されます。 この操作は元に戻せません。また、アプリケーションの状態を復元できません。

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>アプリケーションの種類の登録解除
特定のバージョンのアプリケーションの種類が不要になった場合は、[Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) コマンドレットを使用してアプリケーションの種類の登録を解除する必要があります。 使用していないアプリケーションの種類について、その登録を解除すると、イメージ ストアによって使用されているストレージ領域が解放されます。 あるアプリケーションの種類に対してインスタンス化されたアプリケーションがなく、それを参照している保留中のアプリケーションのアップグレードもない場合に、そのアプリケーションの種類の登録を解除できます。

現在クラスターに登録されているアプリケーションの種類を確認するには、[Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) を実行します。

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

アプリケーションの種類を指定して登録を解除するには、[Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) を実行します。

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="remove-an-application-package-from-the-image-store"></a>イメージ ストアからのアプリケーション パッケージの削除
不要になったアプリケーション パッケージは、イメージ ストアから削除して、システム リソースを解放することができます。

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
```

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage がImageStoreConnectionString を求める
Service Fabric SDK 環境には、適切な既定値を事前に設定しておく必要があります。 ただし、すべてのコマンドの ImageStoreConnectionString が、Service Fabric クラスターによって使用されている値に一致していなければならない場合もあります。 この ImageStoreConnectionString は [Get-ServiceFabricClusterManifest](/powershell/servicefabric/vlatest/get-servicefabricclustermanifest) コマンドで取得したクラスター マニフェストで確認できます。

```powershell
PS C:\> Get-ServiceFabricClusterManifest
```

ImageStoreConnectionString は、クラスター マニフェスト内にあります。

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

イメージ ストアとイメージ ストア接続文字列に関する補足情報は、[イメージ ストア接続文字列の理解](service-fabric-image-store-connection-string.md)に関するページを参照してください。

### <a name="deploy-large-application-package"></a>大規模なアプリケーション パッケージをデプロイする
問題: (GB 単位の) 大規模なアプリケーション パッケージで [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) がタイムアウトする。
次の操作を試してください。
- `TimeoutSec` パラメーターを使用して、[Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) コマンドのタイムアウト値を大きくします。 既定では、タイムアウトは 30 分です。
- ソース コンピューターとクラスターの間のネットワーク接続を確認します。 接続が低速な場合は、ネットワーク接続が高速なコンピューターを使用することを検討してください。
クライアント コンピューターがクラスターとは別のリージョンにある場合は、クラスターと同じかより近いリージョンにあるクライアント コンピューターの使用を検討してください。
- 外部で調整されていないかどうかを確認してください。 たとえば、イメージ ストアが Azure Storage を使用するように構成されている場合、アップロードが調整される可能性があります。

問題: パッケージのアップロードが正常に完了したが、[Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) がタイムアウトする。
次の操作を試してください。
- イメージ ストアにコピーする前に[パッケージを圧縮](service-fabric-package-apps.md#compress-a-package)します。
圧縮によってファイル サイズが小さくなりファイル数が減るため、トラフィックの量と Service Fabric が実行する必要のある処理が減ります。 (圧縮時間を含めた場合は特に) アップロード操作が遅くなる場合がありますが、アプリケーションの種類の登録と登録解除は高速になります。
- `TimeoutSec` パラメーターを使用して、[Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) コマンドのタイムアウト値を大きくします。
- [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) に `Async` スイッチを指定します。 このコマンドを実行すると、クラスターがコマンドを受け入れたときに制御が返されます。プロビジョニングは非同期で継続されます。
このため、この場合にはより大きなタイムアウト値を指定する必要はありません。

### <a name="deploy-application-package-with-many-files"></a>多数のファイルを含むアプリケーション パッケージをデプロイする
問題: (数千個の) 多数のファイルを持つアプリケーション パッケージに対する [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) がタイムアウトする。
次の操作を試してください。
- イメージ ストアにコピーする前に[パッケージを圧縮](service-fabric-package-apps.md#compress-a-package)します。 圧縮により、ファイル数を減らすことができます。
- `TimeoutSec` パラメーターを使用して、[Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) コマンドのタイムアウト値を大きくします。
- [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) に `Async` スイッチを指定します。 このコマンドを実行すると、クラスターがコマンドを受け入れたときに制御が返されます。プロビジョニングは非同期で継続されます。
このため、この場合にはより大きなタイムアウト値を指定する必要はありません。 

## <a name="next-steps"></a>次のステップ
[Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)

[Service Fabric の正常性の概要](service-fabric-health-introduction.md)

[Service Fabric サービスの診断とトラブルシューティング](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric のアプリケーションのモデル化](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md

