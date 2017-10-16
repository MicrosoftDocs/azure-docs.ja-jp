---
title: "Azure Service Fabric アプリのパッケージ化 | Microsoft Docs"
description: "クラスターにデプロイする前に Service Fabric アプリケーションをパッケージ化する方法です。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: ryanwi
ms.openlocfilehash: 486a27d7ca576c8fe1552c02eb24ece6b8bb2ba8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="package-an-application"></a>アプリケーションをパッケージ化する
この記事では、Service Fabric アプリケーションをパッケージ化し、デプロイの準備をする方法について説明します。

## <a name="package-layout"></a>パッケージのレイアウト
アプリケーション マニフェスト、1 つまたは複数のサービス マニフェスト、その他の必要なパッケージ ファイルをデプロイ用の特定のレイアウトで Service Fabric クラスターにまとめる必要があります。 この記事のマニフェスト例は、次のディレクトリ構造でまとめる必要があります。

```
PS D:\temp> tree /f .\MyApplicationType

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
**SetupEntryPoint** を使用する際の一般的なシナリオは、サービス開始前に実行可能ファイルを実行する必要がある場合や、昇格した特権で操作を実行する必要がある場合になります。 次に例を示します。

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

```
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

このエラーは、 *SetupEntryPoint* サービス マニフェストで参照される **MySetup.bat** ファイルがコード パッケージに見つからないことを示しています。 不足しているファイルを追加すると、アプリケーションの検証に合格します。

```
PS D:\temp> tree /f .\MyApplicationType

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

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
```

アプリケーションで[アプリケーション パラメーター](service-fabric-manage-multiple-environment-app-configuration.md)が定義されている場合、それらのパラメーターを [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) に渡して適切に検証できます。

アプリケーションがデプロイされるクラスターが明らかな場合は、`ImageStoreConnectionString` パラメーターを渡すことをお勧めします。 この場合、パッケージは、既にクラスターで実行されている以前のバージョンのアプリケーションに対しても検証されます。 たとえば、この検証によって、バージョンは同じでコンテンツが異なるパッケージが既にデプロイされているかどうかを検出できます。  

アプリケーションが正しくパッケージ化されて、検証に合格すると、圧縮が必要な場合は、サイズとファイルの数に基づいて評価が行われます。

## <a name="compress-a-package"></a>パッケージを圧縮する
パッケージが大きい場合や多数のファイルを含む場合は、短時間でデプロイできるようにこのパッケージを圧縮できます。 圧縮により、ファイル数を減らし、ファイルのサイズを小さくすることができます。
圧縮されたアプリケーション パッケージの場合は、圧縮されていないパッケージと比較して、[パッケージのアップロード](service-fabric-deploy-remove-applications.md#upload-the-application-package)に時間がかかる場合があります (圧縮時間を含めた場合は特に) が、[アプリケーションの種類の登録](service-fabric-deploy-remove-applications.md#register-the-application-package)と[登録解除](service-fabric-deploy-remove-applications.md#unregister-an-application-type)は、圧縮されているパッケージの方が速くなります。

デプロイのメカニズムは、圧縮されているパッケージでも圧縮されていないパッケージでも変わりません。 パッケージが圧縮されている場合、パッケージはクラスター イメージ ストアに格納され、アプリケーションが実行される前にノード上で圧縮が解除されます。
圧縮によって、有効な Service Fabric パッケージが、圧縮されたバージョンで置き換えられます。 フォルダーへの書き込みのアクセス許可を与える必要があります。 既に圧縮済みのパッケージの圧縮を実行しても、変化はありません。

[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) Powershell コマンドを `CompressPackage` スイッチで実行して、パッケージを圧縮することができます。 `UncompressPackage` スイッチを使用して同じコマンドを実行すると、パッケージの圧縮を解除することができます。

次のコマンドでは、パッケージをイメージ ストアにコピーせずに圧縮しています。 必要に応じて、[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) を `SkipCopy` フラグを指定せずに実行して、圧縮されたパッケージを 1 つまたは複数の Service Fabric クラスターにコピーすることができます。
このパッケージには、`code`、`config`、`data` の各パッケージの zip 圧縮されたファイルが含まれています。 アプリケーション マニフェストとサービス マニフェストは、多くの内部操作 (パッケージ共有、特定の検証のためのアプリケーションの種類名とバージョンの抽出など) に必要なため、圧縮されません。
マニフェストを zip 圧縮すると、これらの操作が非効率的になります。

```
PS D:\temp> tree /f .\MyApplicationType

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
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy

PS D:\temp> tree /f .\MyApplicationType

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
```
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

内部的に、Service Fabric がアプリケーション パッケージのチェックサムを計算して検証を行います。 圧縮を使用すると、各パッケージの zip 圧縮バージョンでチェックサムが計算されます。
圧縮されていないアプリケーション パッケージのバージョンをコピーし、同じパッケージに圧縮を使用する場合は、チェックサムの不一致を避けるために、`code`、`config`、`data` の各パッケージのバージョンを変更する必要があります。 パッケージが変更されていない場合は、バージョンを変更する代わりに、[diff プロビジョニング](service-fabric-application-upgrade-advanced.md)を使用します。 このオプションでは、変更されていないパッケージは含めず、代わりにサービス マニフェストから参照します。

同様に、パッケージの圧縮バージョンをアップロードし、圧縮されていないパッケージを使用する場合は、チェックサムの不一致を避けるために、バージョンを更新する必要があります。

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

## <a name="next-steps"></a>次のステップ
「[アプリケーションのデプロイと削除][10]」では、PowerShell を使用してアプリケーション インスタンスを管理する方法について説明しています

「[複数の環境のアプリケーション パラメーターの管理][11]」では、複数のアプリケーション インスタンスに対してパラメーターと環境変数を構成する方法について説明しています。

「[アプリケーションのセキュリティ ポリシーの構成][12]」では、セキュリティ ポリシーの下でサービスを実行して、アクセスを制限する方法について説明しています。

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
