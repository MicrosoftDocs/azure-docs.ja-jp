---
title: スタンドアロン クラスターのバージョンをアップグレードする
description: スタンドアロンの Service Fabric クラスターを実行する Azure Service Fabric コードをアップグレードします。
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 5831fcffe718e4966c3444a29d35c5bbd55e5f57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842939"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>クラスターで実行される Service Fabric のバージョンをアップグレードする 

最新のシステムでは、アップグレードできることが製品の長期的な成功の鍵となります。 Azure Service Fabric クラスターは、ユーザーが所有するリソースの 1 つです。 この記事では、スタンドアロン クラスター上で実行されている Service Fabric のバージョンをアップグレードする方法について説明します。

> [!NOTE]
> クラスターで Service Fabric のサポートされているバージョンが常に実行されるようにする必要があります。 Microsoft が Service Fabric の新しいバージョンのリリースを発表すると、前のバージョンのサポート期間の終了が発表日から最短で 60 日後に設定されます。 新しいリリースは、[Service Fabric チーム ブログ](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)で発表されます。 その時点で新しいリリースを選択できるようになります。
>
>

各 Service Fabric ノードが別個の物理または仮想マシンに割り当てられる、運用スタイルのノード構成を使用している場合にのみ、クラスターを新しいバージョンにアップグレードできます。 1 台の物理マシンまたは仮想マシンに複数の Service Fabric ノードが存在する開発クラスターがある場合、新しいバージョンでクラスターを再構築する必要があります。

2 つの異なるワークフローで、Service Fabric の最新バージョンまたはサポートされているバージョンにクラスターをアップグレードできます。 1 つは、最新バージョンを自動的にダウンロードするための接続を使用できるクラスター向けのワークフローです。 もう 1 つは、Service Fabric の最新バージョンをダウンロードするための接続を使用できないクラスター向けのワークフローです。

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>クラスターの Service Fabric バージョンの自動アップグレードを有効にする
Microsoft が新しいバージョンをリリースしたときに、クラスターが Service Fabric の更新プログラムをダウンロードするように設定するには、`fabricClusterAutoupgradeEnabled` クラスター構成を *true* に設定します。 クラスターに適用する Service Fabric のサポートされているバージョンを手動で選択するには、`fabricClusterAutoupgradeEnabled` クラスター構成を *false* に設定します。

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>最新のコードと構成をダウンロードするための接続を使用できるクラスターをアップグレードする
クラスター ノードがインターネットを通じて [Microsoft ダウンロード センター](https://download.microsoft.com)に接続できる場合、次の手順でクラスターをサポートされているバージョンにアップグレードします。

[Microsoft ダウンロード センター](https://download.microsoft.com)に接続できるクラスターについては、マイクロソフトが Service Fabric の新しいバージョンが利用可能であるかどうかを定期的に確認します。

Service Fabric の新しいバージョンが利用可能になると、パッケージがローカルのクラスターにダウンロードされ、アップグレードの準備が行われます。 さらに、この新しいバージョンの情報をお客様に伝えるために、クラスターの正常性に関する次のような警告が表示されます。

「現在のクラスター バージョン [バージョン番号] のサポートは [日付] に終了します。」

クラスターで最新バージョンが実行されるようになると、この警告は表示されなくなります。

クラスターの正常性に関する警告が表示されたら、クラスターをアップグレードします。

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

    ![Service Fabric のバージョンの取得][getfabversions]
3. [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade) Windows PowerShell コマンドを使用して、利用可能なバージョンへのクラスターのアップグレードを開始します。

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   アップグレードの進捗状況を監視するには、Service Fabric Explorer を使用するか、次の PowerShell コマンドを実行します。

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。 Start-ServiceFabricClusterUpgrade コマンドにカスタム正常性ポリシーを指定する方法については、「[Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade)」をご覧ください。

    ロールバックの原因となった問題を解決したら、前述の手順に従って、もう一度アップグレードを開始します。

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>最新のコードと構成をダウンロードするための *接続を使用できない* クラスターをアップグレードする
クラスター ノードがインターネットを通じて [Microsoft ダウンロード センター](https://download.microsoft.com)に接続できない場合、次の手順でクラスターをサポートされているバージョンにアップグレードします。

> [!NOTE]
> インターネットに接続されていないクラスターを実行している場合、新しいリリースの情報は [Service Fabric チーム ブログ](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)で確認する必要があります。 新しいリリースについて通知する、クラスターの正常性に関する警告は表示されません。  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>自動プロビジョニングと手動プロビジョニングの比較
最新バージョンのコードの自動ダウンロードおよび自動登録を有効にするには、Service Fabric 更新サービスを設定します。 手順については、[スタンドアロン パッケージ](service-fabric-cluster-standalone-package-contents.md)内の *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* を参照してください。

手動プロセスの場合は、次の手順に従ってください。

構成のアップグレードを開始する前に、クラスター構成を変更して次のプロパティを *false* に設定します。

```json
"fabricClusterAutoupgradeEnabled": false,
```

使用方法について詳しくは、[Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) PowerShell コマンドに関するページをご覧ください。 構成のアップグレードを開始する前に、JSON の "clusterConfigurationVersion" を必ず更新してください。

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>クラスターのアップグレード ワークフロー

1. クラスター内のいずれかのノードから [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) を実行し、*TargetCodeVersion* を書き留めます。

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

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    Service Fabric Explorer を使用するか、次の PowerShell コマンドを実行することで、アップグレードの進行状況を監視できます。

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。 Start-ServiceFabricClusterUpgrade コマンドにカスタム正常性ポリシーを指定する方法については、「[Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade)」をご覧ください。

    ロールバックの原因となった問題を解決したら、前述の手順に従って、もう一度アップグレードを開始します。

## <a name="next-steps"></a>次のステップ
* [スタンドアロン クラスターの構成をアップグレードする](service-fabric-cluster-config-upgrade-windows-server.md)
* 一部の [Service Fabric クラスターの設定](service-fabric-cluster-fabric-settings.md)をカスタマイズします。
* [クラスターのスケールインとスケールアウト](service-fabric-cluster-scale-in-out.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
