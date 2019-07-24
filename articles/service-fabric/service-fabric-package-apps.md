---
title: Azure Service Fabric アプリのパッケージ化 | Microsoft Docs
description: クラスターにデプロイする前に Service Fabric アプリケーションをパッケージ化する方法です。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: b8e66a9d5bba0c48f15b1ccd3f2d47e5405db792
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785600"
---
# <a name="package-an-application"></a>アプリケーションをパッケージ化する

この記事では、Service Fabric アプリケーションをパッケージ化し、デプロイの準備をする方法について説明します。

## <a name="package-layout"></a>パッケージのレイアウト

アプリケーション マニフェスト、1 つまたは複数のサービス マニフェスト、その他の必要なパッケージ ファイルをデプロイ用の特定のレイアウトで Service Fabric クラスターにまとめる必要があります。 この記事のマニフェスト例は、次のディレクトリ構造でまとめる必要があります。

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

フォルダーには、それぞれの対応する要素の **Name** 属性と一致する名前が付けられます。 たとえば、**MyCodeA** と **MyCodeB** という名前の付いた 2 つのコード パッケージがサービス マニフェストに含まれている場合、各コード パッケージと同じ名前のフォルダーにそれぞれのコード パッケージに必要なバイナリが含まれます。

## <a name="use-setupentrypoint"></a>SetupEntryPoint の使用

**SetupEntryPoint** を使用する際の一般的なシナリオは、サービス開始前に実行可能ファイルを実行する必要がある場合や、昇格した特権で操作を実行する必要がある場合になります。 例: 

* サービス実行可能ファイルが使用する可能性がある環境変数の設定と初期化などです。 これは、Service Fabric のプログラミング モデルによって記述された実行可能ファイルだけに限定されません。 たとえば、npm.exe は node.js アプリケーションのデプロイに構成されているいくつかの環境変数が必要です。
* セキュリティ証明書のインストールによるアクセス制御の設定

**SetupEntryPoint** の構成方法について詳しくは、[エントリ ポイント セットアップ サービスのポリシーの構成](service-fabric-application-runas-security.md)に関するページをご覧ください

<a id="Package-App"></a>

## <a name="configure"></a>構成

### <a name="build-a-package-by-using-visual-studio"></a>Visual Studio を使用したパッケージ構築

Visual Studio 2015 を使用して、アプリケーションを作成する場合、パッケージのコマンドを使用して、前述のレイアウトと一致するパッケージを自動的に作成できます。

パッケージを作成するには、次のように、ソリューション エクスプローラーでアプリケーション プロジェクトを右クリックして [パッケージ] コマンドを選択します。

![Visual Studio でアプリケーションをパッケージングする][vs-package-command]

パッケージ化が完了したら、**[出力]** ウィンドウにパッケージの場所が表示されます。 アプリケーションを Visual Studio でデプロイまたはデバッグする場合、パッケージ化の手順は自動で行われます。

### <a name="build-a-package-by-command-line"></a>コマンド ラインを使用したパッケージ構築

`msbuild.exe` を使用して、アプリケーションをプログラムによってパッケージ化することもできます。 これは内部的には Visual Studio で実行されているため、出力は同じです。

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>パッケージのテスト

パッケージ構造を PowerShell の [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) コマンドを使用して、ローカルで検証することができます。
このコマンドは、マニフェストの解析の問題をチェックし、すべての参照を検証します。 このコマンドは、パッケージ内のディレクトリとファイルの構造的な正確性を検証するだけです。
コードやデータ パッケージのコンテンツのいずれについても検証は行われず、それらがすべてそろっているかどうかは確認されません。

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

このエラーは、 *SetupEntryPoint* サービス マニフェストで参照される **MySetup.bat** ファイルがコード パッケージに見つからないことを示しています。 不足しているファイルを追加すると、アプリケーションの検証に合格します。

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
True
```

アプリケーションで[アプリケーション パラメーター](service-fabric-manage-multiple-environment-app-configuration.md)が定義されている場合、それらのパラメーターを [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) に渡して適切に検証できます。

アプリケーションがデプロイされるクラスターが明らかな場合は、`ImageStoreConnectionString` パラメーターを渡すことをお勧めします。 この場合、パッケージは、既にクラスターで実行されている以前のバージョンのアプリケーションに対しても検証されます。 たとえば、この検証によって、バージョンは同じでコンテンツが異なるパッケージが既にデプロイされているかどうかを検出できます。  

アプリケーションが正常にパッケージ化され、検証に合格したら、デプロイ操作を迅速化するためにパッケージを圧縮することを検討してください。

## <a name="compress-a-package"></a>パッケージを圧縮する

パッケージが大きい場合や多数のファイルを含む場合は、短時間でデプロイできるようにこのパッケージを圧縮できます。 圧縮により、ファイル数を減らし、ファイルのサイズを小さくすることができます。
圧縮されたアプリケーション パッケージの場合は、圧縮されていないパッケージと比較して、(圧縮がコピーの一部として実行される場合は特に) [パッケージのアップロード](service-fabric-deploy-remove-applications.md#upload-the-application-package)に時間がかかる場合があります。 圧縮されたパッケージの場合、アプリケーションの種類の[登録](service-fabric-deploy-remove-applications.md#register-the-application-package)と[登録解除](service-fabric-deploy-remove-applications.md#unregister-an-application-type)が高速になります。

デプロイのメカニズムは、圧縮されているパッケージでも圧縮されていないパッケージでも変わりません。 パッケージが圧縮されている場合、パッケージはクラスター イメージ ストアに格納され、アプリケーションが実行される前にノード上で圧縮が解除されます。
圧縮によって、有効な Service Fabric パッケージが、圧縮されたバージョンで置き換えられます。 フォルダーへの書き込みのアクセス許可を与える必要があります。 既に圧縮済みのパッケージの圧縮を実行しても、変化はありません。

[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) Powershell コマンドを `CompressPackage` スイッチで実行して、パッケージを圧縮することができます。 `UncompressPackage` スイッチを使用して同じコマンドを実行すると、パッケージの圧縮を解除することができます。

次のコマンドでは、パッケージをイメージ ストアにコピーせずに圧縮しています。 必要に応じて、[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) を `SkipCopy` フラグを指定せずに実行して、圧縮されたパッケージを 1 つまたは複数の Service Fabric クラスターにコピーすることができます。
このパッケージには、`code`、`config`、`data` の各パッケージの zip 圧縮されたファイルが含まれています。 アプリケーション マニフェストとサービス マニフェストは、多くの内部操作に必要なため、圧縮されません。 たとえば、パッケージ共有、特定の検証のためのアプリケーションの種類名とバージョンの抽出では、必ずマニフェストへのアクセスが必要になります。 マニフェストを zip 圧縮すると、これらの操作が非効率的になります。

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

または、[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) を使用して 1 ステップでパッケージを圧縮してコピーできます。
パッケージのサイズが大きい場合は、パッケージの圧縮とクラスターへのアップロードの両方の時間を確保するのに十分な大きいタイムアウト値を指定してください。

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

内部的に、Service Fabric がアプリケーション パッケージのチェックサムを計算して検証を行います。 圧縮を使用すると、各パッケージの zip 圧縮バージョンでチェックサムが計算されます。 同じアプリケーション パッケージから新しい zip を生成すると、異なるチェックサムが作成されます。 検証エラーを防ぐには、[diff プロビジョニング](service-fabric-application-upgrade-advanced.md)を使用します。 このオプションを使用する場合は、変更されていないパッケージを新しいバージョンに含めないでください。 代わりに、新しいサービス マニフェストから直接参照してください。

diff プロビジョニングが選択肢とならず、パッケージを含める必要がある場合は、チェックサムの不一致を避けるために `code`、`config`、および `data` の各パッケージの新しいバージョンを生成してください。 圧縮されたパッケージを使用する場合は、以前のバージョンで圧縮が使用されているかどうかを問わず、変更されていないパッケージの新しいバージョンを生成する必要があります。

これでパッケージは正しくパッケージ化、検証、および (必要に応じて) 圧縮されたため、1 つまたは複数の Service Fabric クラスターに[デプロイ](service-fabric-deploy-remove-applications.md)する準備ができました。

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Visual Studio を使ってデプロイするときにパッケージを圧縮する

`CopyPackageParameters` 要素を発行プロファイルに追加し、`CompressPackage` 属性を `true` に設定することで、デプロイ時にパッケージを圧縮するよう Visual Studio に指示できます。

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>sfpkg の作成

バージョン 6.1 より、Service Fabric では外部ストアからのプロビジョニングが可能になりました。
この場合、アプリケーション パッケージをイメージ ストアにコピーする必要はありません。 代わりに、`sfpkg` を作成して外部ストアにアップロードし、プロビジョニング時にダウンロード URI を Service Fabric に提供することができます。 同じパッケージを複数のクラスターにプロビジョニングできます。 外部ストアからプロビジョニングすることにより、パッケージを各クラスターにコピーするのに要する時間を節約できます。

`sfpkg` ファイルは、初期アプリケーション パッケージを含む、拡張子が ".sfpkg" の zip です。
zip には、圧縮されているアプリケーション パッケージと圧縮されていないアプリケーション パッケージのどちらも含めることができます。 zip 内のアプリケーション パッケージの圧縮は、[前述のように](service-fabric-package-apps.md#compress-a-package)、コード、構成、およびデータ パッケージ レベルで行われます。

`sfpkg` を作成するには、圧縮されているかどうかにかかわらず、元のアプリケーション パッケージを含むフォルダーから開始します。 その後、任意のユーティリティを使用してフォルダーを圧縮して、拡張子 ".sfpkg" を付けます。 たとえば、[ZipFile.CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx) を使用します。

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

`sfpkg` は、Service Fabric の外部の帯域外の外部ストアにアップロードする必要があります。 外部ストアには、REST http または https エンドポイントを公開している任意のストアを指定できます。 プロビジョニング中、Service Fabric は、GET 操作を実行して `sfpkg` アプリケーション パッケージをダウンロードします。そのため、ストアではパッケージの READ アクセスを許可する必要があります。

パッケージをプロビジョニングするには、ダウンロード URI とアプリケーションの種類の情報を必要とする外部プロビジョニングを使用します。

>[!NOTE]
> 現在、イメージ ストアの相対パスに基づくプロビジョニングでは、`sfpkg` ファイルはサポートされていません。 したがって、`sfpkg` をイメージ ストアにコピーしてはいけません。

## <a name="next-steps"></a>次の手順

「[アプリケーションのデプロイと削除][10]」では、PowerShell を使用してアプリケーション インスタンスを管理する方法について説明しています

「[複数の環境のアプリケーション パラメーターの管理][11]」では、複数のアプリケーション インスタンスに対してパラメーターと環境変数を構成する方法について説明しています。

「[アプリケーションのセキュリティ ポリシーの構成][12]」では、セキュリティ ポリシーの下でサービスを実行して、アクセスを制限する方法について説明しています。

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md