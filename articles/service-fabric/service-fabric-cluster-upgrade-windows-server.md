---
title: "Windows Server でのスタンドアロン Azure Service Fabric クラスターのアップグレード | Microsoft Docs"
description: "スタンドアロン Azure Service Fabric クラスターを実行している Service Fabric コード、構成、またはその両方をアップグレードします (クラスター アップグレード モードの設定など)。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/30/2017
ms.author: dekapur
ms.openlocfilehash: f141c3d22275ff04d7161415e9c9f879d85dbc08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-your-standalone-azure-service-fabric-on-windows-server-cluster"></a>Windows Server クラスターでのスタンドアロン Azure Service Fabric のアップグレード
> [!div class="op_single_selector"]
> * [Azure クラスター](service-fabric-cluster-upgrade.md)
> * [スタンドアロン クラスター](service-fabric-cluster-upgrade-windows-server.md)
>
>

最新のシステムでは、アップグレードできることが製品の長期的な成功の鍵となります。 Azure Service Fabric クラスターは、ユーザーが所有するリソースの 1 つです。 この記事では、クラスターが Service Fabric のコードと構成のサポートされているバージョンを常に実行できるようにする方法について説明します。

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>クラスターで実行される Service Fabric のバージョンの管理
Microsoft が新しいバージョンをリリースしたときに、クラスターが Service Fabric の更新プログラムをダウンロードするように設定するには、**fabricClusterAutoupgradeEnabled** クラスター構成を true に設定します。 クラスターに適用する Service Fabric のサポートされているバージョンを選択するには、**fabricClusterAutoupgradeEnabled** クラスター構成を false に設定します。

> [!NOTE]
> クラスターで Service Fabric のサポートされているバージョンが常に実行されるようにする必要があります。 Microsoft が Service Fabric の新しいバージョンのリリースを発表すると、前のバージョンのサポート期間の終了が発表日から最短で 60 日後に設定されます。 新しいリリースは、[Service Fabric チーム ブログ](https://blogs.msdn.microsoft.com/azureservicefabric/)で発表されます。 その時点で新しいリリースを選択できるようになります。
>
>

各 Service Fabric ノードが別個の物理または仮想マシンに割り当てられる、運用スタイルのノード構成を使用している場合にのみ、クラスターを新しいバージョンにアップグレードできます。 1 台の物理マシンまたは仮想マシンに複数の Service Fabric ノードが存在する開発クラスターがある場合、新しいバージョンでクラスターを再構築する必要があります。

2 つの異なるワークフローで、Service Fabric の最新バージョンまたはサポートされているバージョンにクラスターをアップグレードできます。 1 つは、最新バージョンを自動的にダウンロードするための接続を使用できるクラスター向けのワークフローです。 もう 1 つは、Service Fabric の最新バージョンをダウンロードするための接続を使用できないクラスター向けのワークフローです。

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>最新のコードと構成をダウンロードするための接続を使用できるクラスターをアップグレードする
クラスター ノードが [http://download.microsoft.com](http://download.microsoft.com) にインターネット接続できる場合は、以下の手順でクラスターをサポートされているバージョンにアップグレードします。

[http://download.microsoft.com](http://download.microsoft.com) に接続できるクラスターの場合、Service Fabric の新しいバージョンが利用可能かどうかを Microsoft が定期的に確認します。

Service Fabric の新しいバージョンが利用可能になると、パッケージがローカルのクラスターにダウンロードされ、アップグレードの準備が行われます。 さらに、この新しいバージョンの情報をお客様に伝えるために、クラスターの正常性に関する次のような警告が表示されます。

「現在のクラスター バージョン [version #] のサポートは [Date] に終了します。」

クラスターで最新バージョンが実行されるようになると、この警告は表示されなくなります。

#### <a name="cluster-upgrade-workflow"></a>クラスターのアップグレード ワークフロー
クラスターの正常性に関する警告が表示されたら、次の手順を実行します。

1. クラスターにノードとして列挙されているすべてのマシンに管理者アクセスできるマシンからクラスターに接続します。 このスクリプトが実行されるマシンがクラスターに属している必要はありません。

    ```powershell

    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. アップグレード可能な Service Fabric のバージョンの一覧を取得します。

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    次のような出力が表示されます。

    ![ファブリックのバージョンを取得する][getfabversions]
3. [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx) PowerShell コマンドを使用して、利用可能なバージョンへのクラスターのアップグレードを開始します。

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   アップグレードの進捗状況を監視するには、Service Fabric Explorer を使用するか、次の Windows PowerShell コマンドを実行します。

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。 **Start-ServiceFabricClusterUpgrade** コマンドにカスタム正常性ポリシーを指定する方法については、「[Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx)」をご覧ください。

ロールバックの原因となった問題を解決したら、前述の手順に従って、もう一度アップグレードを開始します。

### <a name="upgrade-clusters-that-have-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>最新のコードと構成をダウンロードするための<U>接続を使用できない</u>クラスターをアップグレードする
クラスター ノードが [http://download.microsoft.com](http://download.microsoft.com) にインターネット接続できない場合は、以下の手順でクラスターをサポートされているバージョンにアップグレードします。

> [!NOTE]
> インターネットに接続されていないクラスターを実行している場合、新しいリリースの情報は Service Fabric チーム ブログで確認する必要があります。 新しいリリースについて通知する、クラスターの正常性に関する警告は表示されません。  
>
>

#### <a name="auto-provisioning-vs-manual-provisioning"></a>自動プロビジョニングと手動プロビジョニングの比較
最新バージョンのコードの自動ダウンロードおよび自動登録を有効にするには、Service Fabric 更新サービスを設定します。 手順については、[スタンドアロン パッケージ](service-fabric-cluster-standalone-package-contents.md)内の Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt を参照してください。
手動プロセスの場合は、次の手順に従ってください。

構成のアップグレードを開始する前に、クラスター構成を変更して次のプロパティを false に設定します。

        "fabricClusterAutoupgradeEnabled": false,

使用方法の詳細については、[Start-ServiceFabricClusterConfigurationUpgrade PS cmd](https://msdn.microsoft.com/en-us/library/mt788302.aspx)を参照してください。 構成のアップグレードを開始する前に、JSON の "clusterConfigurationVersion" を必ず更新してください。

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>クラスターのアップグレード ワークフロー

1. クラスター内のいずれかのノードから Get-ServiceFabricClusterUpgrade を実行し、TargetCodeVersion を書き留めます。
2. インターネットに接続されているコンピューターから次のスクリプトを実行して、現在のバージョンとアップグレードの互換性があるすべてのバージョンを一覧表示し、関連付けられたダウンロード リンクから対応するパッケージをダウンロードします。

    ```powershell

    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. クラスターにノードとして列挙されているすべてのマシンに管理者アクセスできるマシンからクラスターに接続します。 このスクリプトが実行されるマシンがクラスターに属している必要はありません。

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```
4. ダウンロードしたパッケージをクラスター イメージ ストアにコピーします。

5. コピーしたパッケージを登録します。

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
6. 利用可能なバージョンへのクラスターのアップグレードを開始します。

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Service Fabric Explorer を使用するか、次の PowerShell コマンドを実行することで、アップグレードの進行状況を監視できます。

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。 **Start-ServiceFabricClusterUpgrade** コマンドにカスタム正常性ポリシーを指定する方法については、「[Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx)」をご覧ください。

ロールバックの原因となった問題を解決したら、前述の手順に従って、もう一度アップグレードを開始します。


## <a name="upgrade-the-cluster-configuration"></a>クラスター構成のアップグレード
構成アップグレードを開始する前に、スタンドアロン パッケージで PowerShell スクリプトを実行し、新しいクラスター構成をテストできます。

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>

```
または

```powershell

    TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>

```

エンドポイント、クラスター名、ノード IP など、一部の構成はアップグレードできません。これは古いクラスター構成 JSON に対して新しいクラスター構成 JSON をテストし、問題があれば、PowerShell ウィンドウにエラーを表示します。

クラスター構成をアップグレードするには、**Start-ServiceFabricClusterConfigurationUpgrade** を実行します。 構成のアップグレードは、アップグレード ドメインごとに処理されます。

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

### <a name="cluster-certificate-config-upgrade"></a>クラスター証明書の構成のアップグレード  
クラスター証明書はクラスター ノード間での認証で使用されるため、証明書のロール オーバーは特に注意して実行する必要があります。失敗するとクラスター ノード間の通信がブロックされます。  
技術的には、次の 3 つのオプションがサポートされます。  

1. 証明書のシングル アップグレード: アップグレードのパスは '証明書 A (プライマリ) -> 証明書 B (プライマリ) -> 証明書 C (プライマリ) -> ...' です。   
2. 証明書のダブル アップグレード: アップグレードのパスは '証明書 A (プライマリ) -> 証明書 A (プライマリ) および B (セカンダリ) -> 証明書 B (プライマリ) -> 証明書 B (プライマリ) および C (セカンダリ) -> 証明書 C (プライマリ) -> ...' です。
3. 証明書の種類のアップグレード: 拇印ベースの証明書の構成 <-> CommonName ベースの証明書の構成。 たとえば、証明書の拇印 A (プライマリ) と拇印 B (セカンダリ) -> 証明書 CommonName C。
4. 証明書の発行者の拇印のアップグレード: アップグレードのパスは、'証明書 CN = A、発行者の拇印 = IT1 (プライマリ) -> 証明書 CN = A、発行者の拇印 = IT1、IT2 (プライマリ) -> 証明書 CN = A、発行者の拇印 = IT2 (プライマリ)' です。


## <a name="next-steps"></a>次のステップ
* 一部の [Service Fabric クラスター設定](service-fabric-cluster-fabric-settings.md)をカスタマイズする方法を学習します。
* [クラスターのスケールアップとスケールダウン](service-fabric-cluster-scale-up-down.md)を行う方法を学習します。
* [アプリケーションのアップグレード](service-fabric-application-upgrade.md)を行う方法を学習します。

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
