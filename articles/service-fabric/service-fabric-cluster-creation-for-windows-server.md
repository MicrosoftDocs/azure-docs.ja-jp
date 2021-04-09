---
title: スタンドアロン Azure Service Fabric クラスターを作成する
description: オンプレミスまたはクラウドにある Windows Server が実行されているマシン (物理コンピューターまたは仮想マシン) で、Azure Service Fabric クラスターを作成します。
ms.topic: conceptual
ms.date: 2/21/2019
ms.openlocfilehash: 41af655be07ccae2b66e75f5bfe87629cdb54924
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785686"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Windows Server で実行されるスタンドアロン クラスターの作成
Azure Service Fabric を使用すると、Windows Server を実行するあらゆる仮想マシンまたはコンピューター上に Service Fabric クラスターを作成できます。 つまり相互に接続された一連の Windows Server コンピューターを含む環境さえあれば、オンプレミスであれ、クラウド プロバイダーであれ、Service Fabric アプリケーションをデプロイして実行できるということです。 Service Fabric には、Service Fabric クラスターを作成するためのセットアップ パッケージ (スタンドアロン Windows Server パッケージ) が用意されています。 Azure 上の従来の Service Fabric クラスターはマネージド サービスとして使用できるのに対して、スタンドアロンの Service Fabric クラスターはセルフサービスです。 違いについては、[Azure とスタンドアロン Service Fabric クラスターの比較](./service-fabric-deploy-anywhere.md)に関する記事をご覧ください。

この記事では、Service Fabric スタンドアロン クラスターの作成手順について説明します。

> [!NOTE]
> このスタンドアロン Windows Server パッケージは無料で商用利用が可能であり、運用環境デプロイに使用できます。 このパッケージには、Service Fabric の "プレビュー" 段階の新機能が含まれている場合があります。 プレビュー機能の一覧については、「[このパッケージに含まれるプレビュー機能](#previewfeatures_anchor)」を ご覧ください。 [使用許諾契約書のコピーのダウンロード](https://go.microsoft.com/fwlink/?LinkID=733084) ができるようになりました。
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Windows Server 用 Service Fabric パッケージのサポートを受ける
* Windows Server 用 Service Fabric スタンドアロン パッケージに関するコミュニティへの質問を [Azure Service Fabric の Microsoft Q&A 質問ページ](/answers/topics/azure-service-fabric.html)で投稿します。
* [Service Fabric の専門家によるサポート](https://support.microsoft.com/oas/default.aspx?prid=16146)については、チケットを申請してください。  Microsoft によるプロフェッショナル サポートの詳細については、[こちら](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)をご覧ください。
* [Microsoft Premier サポート](https://support.microsoft.com/en-us/premier)の一環として、このパッケージのサポートを受けることもできます。
* 詳細については、「[Azure Service Fabric のサポート オプション](./service-fabric-support.md)」を参照してください。
* サポート目的でログを収集するには、[Service Fabric スタンドアロン ログ コレクター](service-fabric-cluster-standalone-package-contents.md)を実行します。

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Windows Server 用 Service Fabric パッケージをダウンロードする
クラスターの作成には、次の場所にある Windows Server 用 Service Fabric パッケージ (Windows Server 2012 R2 以降) を使用します。 <br>
[ダウンロード リンク - Service Fabric スタンドアロン パッケージ - Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

パッケージ内容の詳細については[こちら](service-fabric-cluster-standalone-package-contents.md)を参照してください。

Service Fabric ランタイム パッケージは、クラスターの作成時に自動でダウンロードされます。 インターネットに接続されていないマシンからデプロイを行う場合は、次の場所から帯域外用のランタイム パッケージをダウンロードしてください。 <br>
[ダウンロード リンク - Service Fabric ランタイム - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

スタンドアロン クラスター構成は次の場所にあります。 <br>
[スタンドアロン クラスター構成のサンプル](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>クラスターを作成する
セットアップ パッケージと共に、いくつかのサンプル クラスター構成ファイルがインストールされます。 *ClusterConfig.Unsecure.DevCluster.json* は最も単純なクラスター構成です。1 台のコンピューターで動作する 3 ノード クラスターとなっており、セキュリティ対策は施されていません。  その他の構成ファイルには、X.509 証明書または Windows セキュリティによって安全性が確保された 1 台または複数台のコンピューターから成るクラスターが記述されています。  このチュートリアルのために既定の構成設定を変更する必要はありませんが、構成ファイルに目を通して、設定の概要を把握しておいてください。  **nodes** セクションでは、クラスター内の 3 つのノードの name、IP address、[node type、fault domain、および upgrade domain](service-fabric-cluster-manifest.md#nodes-on-the-cluster) が記述されています。  **properties** セクションでは、クラスターの [セキュリティ、信頼性レベル、診断コレクション、およびノードの種類](service-fabric-cluster-manifest.md#cluster-properties)が定義されています。

この記事で作成するクラスターは安全ではありません。  だれでも匿名で接続し、管理操作を実行することができるため、運用クラスターは常に X.509 証明書または Windows セキュリティを使用して保護する必要があります。  セキュリティを構成できるのはクラスターを作成するときだけであり、クラスターの作成後はセキュリティを有効にすることはできません。 構成ファイルを更新して、[証明書によるセキュリティ](service-fabric-windows-cluster-x509-security.md)または [Windows のセキュリティ](service-fabric-windows-cluster-windows-security.md)を有効にします。 Service Fabric クラスターのセキュリティについて詳しくは、[クラスターのセキュリティ保護](service-fabric-cluster-security.md)についてのページを参照してください。

### <a name="step-1-create-the-cluster"></a>手順 1:クラスターを作成する

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>シナリオ A: セキュリティで保護されていないローカルの開発クラスターを作成する
[サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)に含まれる "*ClusterConfig.Unsecure.DevCluster.json*" ファイルを使用すると、Service Fabric を 1 台のマシンから成る開発クラスターにデプロイできます。

スタンドアロン パッケージをマシンに解凍してサンプルの構成ファイルをローカル マシンにコピーしてから、管理者権限の PowerShell セッションでスタンドアロン パッケージのフォルダーにある *CreateServiceFabricCluster.ps1* スクリプトを実行します。

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

トラブルシューティングの詳細については、「[Service Fabric スタンドアロン クラスターのデプロイの準備と計画](service-fabric-cluster-standalone-deployment-preparation.md)」の「環境のセットアップ」を参照してください。

開発シナリオの実行が完了したら、Service Fabric クラスターは、「[クラスターの削除](#removecluster_anchor)」の手順を参照してマシンから削除できます。 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>シナリオ B: 複数のコンピューターから成るクラスターを作成する
[クラスターのデプロイの計画と準備](service-fabric-cluster-standalone-deployment-preparation.md)に関するページで説明されている計画および準備の手順を完了すると、独自のクラスター構成ファイルを使用して運用クラスターを作成できるようになります。

クラスターのデプロイと構成を行うクラスター管理者には、コンピューターに対する管理特権が必要です。 ドメイン コントローラーに Service Fabric をインストールすることはできません。

1. スタンドアロン パッケージの *TestConfiguration.ps1* スクリプトをベスト プラクティス アナライザーとして使って、特定の環境にクラスターをデプロイできるかどうかを検証します。 前提条件と環境の要件については、[デプロイの準備](service-fabric-cluster-standalone-deployment-preparation.md)のページを参照してください。 スクリプトを実行して、開発クラスターを作成できるかどうかを検証します。  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    次のような出力が表示されます。 1 番下の "Passed" フィールドで "True" が返された場合、サニティ チェックは成功であり、クラスターは入力した構成を基にデプロイ可能と考えられます。

    ```powershell
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. クラスターを作成します。*CreateServiceFabricCluster.ps1* スクリプトを実行して、構成内の各コンピューターに Service Fabric クラスターをデプロイします。 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> デプロイのトレースは、CreateServiceFabricCluster.ps1 PowerShell スクリプトを実行した VM/マシンに書き込まれます。 このトレースは、スクリプトを実行したディレクトリの DeploymentTraces サブフォルダーにあります。 Service Fabric がマシンに正常にデプロイされたかどうかを確かめるには、FabricDataRoot ディレクトリにインストールされたファイルを確認します。このディレクトリはクラスター構成ファイルの FabricSettings セクションで指定した場所にあります (既定では c:\ProgramData\SF)。 また、タスク マネージャーで FabricHost.exe と Fabric.exe の各プロセスが実行されていることも確認します。
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>シナリオ C: オフラインの (インターネットから切断された) クラスターを作成する
Service Fabric ランタイム パッケージは、クラスターの作成時に自動でダウンロードされます。 インターネットに接続していないコンピューターにクラスターをデプロイする場合、Service Fabric ランタイム パッケージを別にダウンロードしておき、クラスターの作成時にそのパスを指定する必要があります。
ランタイム パッケージはインターネットに接続されている他のコンピューターから別にダウンロードすることができます ([ダウンロード リンク - Service Fabric ランタイム - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354))。 オフライン クラスターをデプロイしている場所にランタイム パッケージをコピーし、次の例のように `-FabricRuntimePackagePath` パラメーターを指定して `CreateServiceFabricCluster.ps1` を実行してクラスターを作成します。 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.json* と *.\MicrosoftAzureServiceFabric.cab* は、それぞれクラスター構成とランタイムの .cab ファイルのパスです。

### <a name="step-2-connect-to-the-cluster"></a>手順 2:クラスターに接続する
クラスターに接続し、クラスターが実行されており使用可能であることを確認します。 Service Fabric ランタイムをインストールすると、ServiceFabric PowerShell モジュールがインストールされます。  クラスター ノードのいずれか、または Service Fabric ランタイムがインストールされたリモート コンピューターから、クラスターに接続できます。  クラスターへの接続は、[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) コマンドレットで確立します。

セキュリティで保護されていないクラスターに接続するには、次の PowerShell コマンドを実行します。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

次に例を示します。
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.234:19000
```

その他の方法でクラスターに接続する例については、「[セキュリティ保護されたクラスターに接続する](service-fabric-connect-to-secure-cluster.md)」を参照してください。 クラスターに接続した後、[Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) コマンドレットを使って、クラスターに含まれている一連のノードとノードごとの状態情報を表示します。 すべてのノードで **HealthState** が *OK* になっている必要があります。

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>手順 3:Service Fabric Explorer を使用したクラスターの視覚化
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) は、クラスターを視覚化してアプリケーションを管理するための最適なツールです。  Service Fabric Explorer はクラスターで動作するサービスであり、ブラウザーを使用して `http://localhost:19080/Explorer` にアクセスすることができます。

クラスター ダッシュボードにクラスターの概要 (アプリケーションとノードの正常性の概要など) が表示されます。 ノード ビューには、クラスターの物理的なレイアウトが表示されます。 特定のノードについて、そのノードでコードがデプロイされているアプリケーション、

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>ノードの追加と削除
ビジネス ニーズの変化に応じて、スタンドアロン Service Fabric クラスターでノードを追加または削除できます。 詳細の手順については、[「Service Fabric スタンドアロン クラスターでノードを追加または削除する」](service-fabric-cluster-windows-server-add-remove-nodes.md) をお読みください。

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>クラスターの削除
クラスターを削除するには、パッケージ フォルダーにある *RemoveServiceFabricCluster.ps1* PowerShell スクリプトを実行し、JSON 構成ファイルのパスを渡してください。 必要に応じて、削除対象のログの場所を指定することもできます。

このスクリプトは、クラスター構成ファイルにノードとして列挙されているすべてのマシンに管理者アクセスできれば、どのマシンでも実行できます。 このスクリプトが実行されるマシンがクラスターに属している必要はありません。

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>収集されるテレメトリ データとオプトアウトの方法
製品品質の向上を図るために、Service Fabric の使用状況に関するテレメトリが既定で収集されます。 セットアップの過程で実行されるベスト プラクティス アナライザーは、[https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) に対する接続の可否をチェックします。 この URL に到達できなかった場合、テレメトリを無効にしない限り、セットアップは失敗します。

1. テレメトリ パイプラインは、以下のデータを 1 日に 1 回、[https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) にアップロードしようと試みます。 アップロードはベスト エフォートで実行され、クラスターの動作には一切影響しません。 テレメトリの送信元になるのは、Failover Manager プライマリを実行しているノードだけです。 それ以外のノードからはテレメトリが送信されません。
2. テレメトリは次のデータで構成されています。

* サービスの数
* ServiceTypes の数
* アプリケーションの数
* ApplicationUpgrades の数
* FailoverUnits の数
* InBuildFailoverUnits の数
* UnhealthyFailoverUnits の数
* レプリカの数
* InBuildReplicas の数
* StandByReplicas の数
* OfflineReplicas の数
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* ノードの数
* IsContextComplete: True または False
* ClusterId: これは、クラスターごとにランダムに生成された GUID です。
* ServiceFabricVersion
* テレメトリのアップロード元となった仮想マシンまたはコンピューターの IP アドレス

テレメトリを無効にするには、クラスターの構成ファイル内の *properties* に *enableTelemetry: false* を追加します。

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>このパッケージに含まれるプレビュー機能
[なし] :


> [!NOTE]
> [Windows Server (バージョン 5.3.204.x) 用のスタンドアロン クラスターの新しい GA バージョン](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)以降では、クラスターを今後のリリースに手動でアップグレードすることも、自動的にアップグレードすることもできます。 詳細については、[スタンドアロン Service Fabric クラスターのバージョンのアップグレード](service-fabric-cluster-upgrade-windows-server.md)に関するドキュメントをご覧ください。
> 
> 

## <a name="next-steps"></a>次のステップ
* [PowerShell を使用してアプリケーションのデプロイと削除を実行する](service-fabric-deploy-remove-applications.md)
* [スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)
* [スタンドアロン Service Fabric クラスターでノードを追加または削除する](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [スタンドアロン Service Fabric クラスターのバージョンをアップグレードする](service-fabric-cluster-upgrade-windows-server.md)
* [Windows を実行する Azure VM を使用してスタンドアロン Service Fabric クラスターを作成する](./service-fabric-cluster-creation-via-arm.md)
* [Windows 上のスタンドアロン クラスターを Windows セキュリティで保護する](service-fabric-windows-cluster-windows-security.md)
* [X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
