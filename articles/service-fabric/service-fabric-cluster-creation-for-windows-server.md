---
title: "スタンドアロン Azure Service Fabric クラスターの作成 | Microsoft Docs"
description: "オンプレミスまたはクラウドにある Windows Server が実行されているマシン (物理コンピューターまたは仮想マシン) で、Azure Service Fabric クラスターを作成します。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/24/2017
ms.author: chackdan;maburlik
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 3389684dca62a1b3c8297600c7c09ffef642c854
ms.lasthandoff: 03/29/2017


---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Windows Server で実行されるスタンドアロン クラスターの作成
Azure Service Fabric を使用すると、Windows Server を実行するあらゆる仮想マシンまたはコンピューター上に Service Fabric クラスターを作成できます。 つまり相互に接続された一連の Windows Server コンピューターを含む環境さえあれば、オンプレミスであれ、クラウド プロバイダーであれ、Service Fabric アプリケーションをデプロイして実行できるということです。 Service Fabric には、Service Fabric クラスターを作成するためのセットアップ パッケージ (スタンドアロン Windows Server パッケージ) が用意されています。

この記事では、Service Fabric スタンドアロン クラスターの作成手順について説明します。

> [!NOTE]
> このスタンドアロン Windows Server パッケージは市販されており、運用環境デプロイに使用できます。 このパッケージには、Service Fabric の "プレビュー" 段階の新機能が含まれている場合があります。 プレビュー機能の一覧については、「[このパッケージに含まれるプレビュー機能](#previewfeatures_anchor)」を ご覧ください。 [使用許諾契約書のコピーのダウンロード](http://go.microsoft.com/fwlink/?LinkID=733084) ができるようになりました。
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-standalone-package"></a>Service Fabric スタンドアロン パッケージに関連したサポートの利用
* Windows Server 用 Service Fabric スタンドアロン パッケージに関するコミュニティへの質問を [Azure Service Fabric フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?) で投稿します。
* [Service Fabric の専門家によるサポート](http://support.microsoft.com/oas/default.aspx?prid=16146)については、チケットを申請してください。  Microsoft によるプロフェッショナル サポートの詳細については、[こちら](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)をご覧ください。
* [Microsoft Premier サポート](https://support.microsoft.com/en-us/premier)の一環として、このパッケージのサポートを受けることもできます。
* 詳細については、「[Azure Service Fabric のサポート オプション](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support)」を参照してください。
* サポート目的でログを収集するには、[Service Fabric スタンドアロン ログ コレクター](https://go.microsoft.com/fwlink/?linkid=842487) を実行します。

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-standalone-package"></a>Service Fabric スタンドアロン パッケージのダウンロード
クラスターの作成には、次の場所にある Windows Server (2012 R2 以降) 用の Service Fabric スタンドアロン パッケージを使用します。 <br>
[ダウンロード リンク - Service Fabric スタンドアロン パッケージ - Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690)

パッケージ内容の詳細については[こちら](service-fabric-cluster-standalone-package-contents.md)を参照してください。

Service Fabric ランタイム パッケージは、クラスターの作成時に自動でダウンロードされます。 インターネットに接続されていないマシンからデプロイを行う場合は、次の場所から帯域外用のランタイム パッケージをダウンロードしてください。 <br>
[ダウンロード リンク - Service Fabric ランタイム - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

スタンドアロン クラスター構成は次の場所にあります。 <br>
[スタンドアロン クラスター構成のサンプル](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>クラスターを作成する
[サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)に含まれる "*ClusterConfig.Unsecure.DevCluster.json*" ファイルを使用すると、Service Fabric を 1 台のマシンから成る開発クラスターにデプロイできます。

スタンドアロン パッケージをマシンに解凍してサンプルの構成ファイルをローカル マシンにコピーしてから、管理者権限の PowerShell セッションでスタンドアロン パッケージのフォルダーにある *CreateServiceFabricCluster.ps1* スクリプトを実行します。
### <a name="step-1a-create-an-unsecured-local-development-cluster"></a>手順 1A: セキュリティで保護されないローカルの開発クラスターを作成する
```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

トラブルシューティングの詳細については、[クラスターのデプロイの計画と準備](service-fabric-cluster-standalone-deployment-preparation.md)に関するページの「Environment Setup」(環境のセットアップ) セクションを参照してください。

開発シナリオの実行が完了したら、Service Fabric クラスターは、「[クラスターの削除](#removecluster_anchor)」の手順を参照してマシンから削除できます。 

### <a name="step-1b-create-a-multi-machine-cluster"></a>手順 1B: 複数のマシンから成るクラスターを作成する
下記リンクで説明されている計画および準備の手順を完了すると、独自のクラスター構成ファイルを使用して運用クラスターを作成できるようになります。 <br>
[クラスターのデプロイの計画と準備](service-fabric-cluster-standalone-deployment-preparation.md)

1. スタンドアロン パッケージのフォルダーから *TestConfiguration.ps1* スクリプトを実行して、作成した構成ファイルを検証します。  

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
```

次のような出力結果が表示されます。 1 番下の "Passed" フィールドで "True" が返された場合、サニティ チェックは成功であり、クラスターは入力した構成を基にデプロイ可能と考えられます。

```
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

2. クラスターを作成します。*CreateServiceFabricCluster.ps1* を実行して、構成内の各マシンに Service Fabric クラスターをデプロイします。 
```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
```

> [!NOTE]
> デプロイのトレースは、CreateServiceFabricCluster.ps1 PowerShell スクリプトを実行した VM/マシンに書き込まれます。 このトレースは、スクリプトを実行したディレクトリの DeploymentTraces サブフォルダーにあります。 Service Fabric がマシンに正常にデプロイされたかどうかを確かめるには、FabricDataRoot ディレクトリにインストールされたファイルを確認します。このディレクトリはクラスター構成ファイルの FabricSettings セクションで指定した場所にあります (既定では c:\ProgramData\SF)。 また、タスク マネージャーで FabricHost.exe と Fabric.exe の各プロセスが実行されていることも確認します。
> 
> 

### <a name="step-2-connect-to-the-cluster"></a>手順 2: クラスターに接続する
セキュリティで保護されたクラスターに接続するには、[セキュリティで保護されたクラスターへの Service Fabric の接続](service-fabric-connect-to-secure-cluster.md)参照してください。

セキュリティで保護されていないクラスターに接続するには、次の PowerShell コマンドを実行します。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```
### <a name="step-3-bring-up-service-fabric-explorer"></a>手順 3: Service Fabric Explorer を起動する
これで、Service Fabric Explorer を使用してクラスターに接続できるようになりました。http://localhost:19080/Explorer/index.html を使っていずれかのマシンから直接接続するか、http://<*IPAddressofaMachine*>:19080/Explorer/index.html を使ってリモートで接続することができます。

## <a name="add-and-remove-nodes"></a>ノードの追加と削除
ビジネス ニーズの変化に応じて、スタンドアロン Service Fabric クラスターでノードを追加または削除できます。 詳細の手順については、[「Service Fabric スタンドアロン クラスターでノードを追加または削除する」](service-fabric-cluster-windows-server-add-remove-nodes.md) をお読みください。

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>クラスターの削除
クラスターを削除するには、パッケージ フォルダーにある *RemoveServiceFabricCluster.ps1* PowerShell スクリプトを実行し、JSON 構成ファイルのパスを渡してください。 必要に応じて、削除対象のログの場所を指定することもできます。

このスクリプトは、クラスター構成ファイルにノードとして列挙されているすべてのマシンに管理者アクセスできれば、どのマシンでも実行できます。 このスクリプトが実行されるマシンがクラスターに属している必要はありません。

```
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>収集されるテレメトリ データとオプトアウトの方法
製品品質の向上を図るために、Service Fabric の使用状況に関するテレメトリが既定で収集されます。 セットアップの過程で実行されるベスト プラクティス アナライザーは、 [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)に対する接続の可否をチェックします。 この URL に到達できなかった場合、テレメトリを無効にしない限り、セットアップは失敗します。

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
* IsContextComplete: True/False
* ClusterId: これは各クラスターについてランダムに生成される GUID です。
* ServiceFabricVersion
* テレメトリのアップロード元となった仮想マシンまたはコンピューターの IP アドレス

テレメトリを無効にするには、クラスターの構成ファイル内の *properties* に *enableTelemetry: false* を追加します。

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>このパッケージに含まれるプレビュー機能
なし。


> [!NOTE]
> [Windows Server (バージョン 5.3.204.x) 用のスタンドアロン クラスターの新しい GA バージョン](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)以降では、クラスターを今後のリリースに手動でアップグレードすることも、自動的にアップグレードすることもできます。 詳細については、[スタンドアロン Service Fabric クラスターのバージョンのアップグレード](service-fabric-cluster-upgrade-windows-server.md)に関するドキュメントをご覧ください。
> 
> 

## <a name="next-steps"></a>次のステップ
* [PowerShell を使用してアプリケーションのデプロイと削除を実行する](service-fabric-deploy-remove-applications.md)
* [スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)
* [スタンドアロン Service Fabric クラスターでノードを追加または削除する](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [スタンドアロン Service Fabric クラスターのバージョンをアップグレードする](service-fabric-cluster-upgrade-windows-server.md)
* [Windows を実行する Azure VM を使用してスタンドアロン Service Fabric クラスターを作成する](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Windows 上のスタンドアロン クラスターを Windows セキュリティで保護する](service-fabric-windows-cluster-windows-security.md)
* [X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png

