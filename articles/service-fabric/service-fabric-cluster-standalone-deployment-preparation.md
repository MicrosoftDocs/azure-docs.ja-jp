---
title: スタンドアロン クラスターのデプロイの準備
description: 運用ワークロードを処理するためのクラスターのデプロイ前に検討する必要がある、環境の準備およびクラスター構成の作成に関連するドキュメント。
ms.topic: conceptual
ms.date: 9/11/2018
ms.openlocfilehash: 18b8b0ce8c0e877bf9dd274596b19f85b1febe12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790954"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Service Fabric スタンドアロン クラスターのデプロイの計画と準備

<a id="preparemachines"></a>クラスターを作成する前に、次の手順を実行します。

## <a name="plan-your-cluster-infrastructure"></a>クラスターのインフラストラクチャを計画する
ここでの Service Fabric クラスターの作成先は自分が所有するマシンです。そこで、クラスターがどのような障害に対応するかを決定できます。 たとえば電気系統やインターネット接続を、それらのマシン専用に確保する必要はあるでしょうか。 また、マシンの物理的なセキュリティも考慮してください。 マシンの配置場所とマシンへのアクセスが必要なユーザー これらの点を判断したら、各種の障害ドメイン (次の手順を参照) にマシンを論理的に対応付けることができます。 運用環境クラスターのインフラストラクチャ計画は、テスト環境のクラスターの場合よりも複雑です。

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>障害ドメインとアップグレード ドメインの数を決める
"[*障害ドメイン*" (FD)](service-fabric-cluster-resource-manager-cluster-description.md) は障害の物理的単位であり、データ センター内の物理インフラストラクチャと直接関連付けられます。 障害ドメインは、同じ単一障害点を持ったハードウェア コンポーネント (コンピューター、スイッチ、ネットワークなど) で構成されます。 障害ドメインとラックとの間に 1:1 の対応はありませんが、大まかにいえば、それぞれのラックが 1 つの障害ドメインと考えてかまいません。

各障害ドメインの名前は、ClusterConfig.json に障害ドメインを指定するときに選ぶことができます。 Service Fabric では階層形式の障害ドメインがサポートされているため、実際のインフラストラクチャのトポロジをそこに反映させることが可能です。  有効な障害ドメインの例を次に示します。

* "faultDomain": "fd:/Room1/Rack1/Machine1"
* "faultDomain": "fd:/FD1"
* "faultDomain": "fd:/Room1/Rack1/PDU1/M1"

"*アップグレード ドメイン*" (UD) は、ノードの論理ユニットです。 Service Fabric によってオーケストレーションされるアップグレード (アプリケーション アップグレードまたはクラスター アップグレード) の間、アップグレード ドメイン内のノードはすべて中断されてアップグレードが実行されますが、他のアップグレード ドメイン内のノードはその間も要求に応答し続けることができます。 マシンに対するファームウェア アップグレードでは、アップグレード ドメインが考慮されません。アップグレードは 1 台ずつ行う必要があります。

障害ドメインは計画の範囲外の障害の単位、アップグレード ドメインは計画の範囲内のメンテナンスの単位であると考えると理解しやすいでしょう。

アップグレード ドメインの名前は、ClusterConfig.jsonにアップグレード ドメインを指定するときに選ぶことができます。 有効な名前の例を次に示します。

* "upgradeDomain":"UD0"
* "upgradeDomain":"UD1A"
* "upgradeDomain":"DomainRed"
* "upgradeDomain":"Blue"

FD と UD の詳細については、「[Service Fabric クラスターの記述](service-fabric-cluster-resource-manager-cluster-description.md)」を参照してください。

ノードのメンテナンスと管理に対してフル コントロールの権限がある (つまり、マシンの更新や交換に責任がある) 場合、稼働中のクラスターを運用環境でサポートするために、そのクラスターが 3 つ以上の FD にまたがるようにする必要があります。 マシンを完全に制御できない環境 (アマゾン ウェブ サービスの VM インスタンス) で実行するクラスターの場合は、クラスターに少なくとも 5 つの FD が必要です。 各 FD は、1 つ以上のノードを持つことができます。 これは、マシンのアップグレードや更新が原因で発生し、タイミングによってはクラスター内のアプリケーションやサービスの実行に干渉しかねない問題を回避するためです。

## <a name="determine-the-initial-cluster-size"></a>クラスターの初期サイズを決める

一般に、クラスター内のノード数はビジネス ニーズに基づいて決まります。つまり、クラスターで実行されるサービスとコンテナーの数、ワークロードを維持するために必要なリソースの数によって決まります。 運用クラスターでは、5 つの FD にまたがるノードを 5 つ以上クラスター内に用意することをお勧めします。 ただし、前述のように、ノードに対するフル コントロールの権限があり、3 つの FD にまたがる場合は、3 つのノードもジョブを実行する必要があります。

ステートフル ワークロードを実行するテスト クラスターには 3 つのノードが必要であるのに対し、ステートレス ワークロードのみを実行するテスト クラスターに必要なノードは 1 つだけです。 開発目的では、特定のマシン 1 台に複数のノードを割り当て可能であることにも注意してください。 ただし、運用環境の場合、Service Fabric がサポートするノードは物理マシンまたは仮想マシン 1 台につき 1 つだけです。

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>ノードとして機能するマシンを準備する

Service Fabric クラスター内のマシンに対して推奨される仕様を次に示します。

* 16 GB 以上の RAM
* 40 GB 以上の使用可能なディスク領域
* 4 コア以上の CPU
* すべてのマシンのセキュリティで保護された 1 つ以上のネットワークへの接続
* Windows Server OS がインストールされている (有効なバージョン: 2012 R2、2016、1709、1803)。 Service Fabric バージョン 6.4.654.9590 以降では、Server 2019 と 1809 もサポートされています。
* [.NET Framework 4.5.1 以降](https://www.microsoft.com/download/details.aspx?id=40773) (フル インストール)
* [Windows PowerShell 3.0](/powershell/scripting/windows-powershell/install/installing-windows-powershell)
* [RemoteRegistry サービス](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754820(v=ws.11))がすべてのマシンで実行されている必要があります。
* **Service Fabric のインストール ドライブは、NTFS ファイル システムである必要があります**
* **Windows サービスの "*パフォーマンス ログとアラート*" と "*Windows イベント ログ*" が [有効である](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc755249(v=ws.11))必要があります**。

> [!IMPORTANT]
> クラスターのデプロイと構成を行うクラスター管理者には、個々のマシンに対する [管理者特権](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) が必要です。 ドメイン コントローラーに Service Fabric をインストールすることはできません。

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Windows Server 用の Service Fabric スタンドアロン パッケージをダウンロードする
[Windows Server 用の Service Fabric スタンドアロン パッケージをダウンロード](https://go.microsoft.com/fwlink/?LinkId=730690)し、クラスターに属していないデプロイ用のマシンか、これからクラスターに追加するマシンのいずれかにパッケージ ファイルを解凍します。

## <a name="modify-cluster-configuration"></a>クラスターの構成を変更する
スタンドアロン クラスターを作成するには、クラスターの仕様を示すスタンドアロン クラスター構成ファイル ClusterConfig.json を作成する必要があります。 構成ファイルについては、次のリンクにあるテンプレートを参考にしてください。 <br>
[スタンドアロン クラスターの構成](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

このファイルのセクションの詳細については、[「スタンドアロン Windows クラスターの構成設定」](service-fabric-cluster-manifest.md) を参照してください。

ダウンロードしたパッケージの ClusterConfig.json ファイルのいずれかを開き、次の設定を変更します。

| **構成設定** | **説明** |
| --- | --- |
| **NodeTypes** |クラスター ノードは、ノードのタイプでグループ分けすることができます。 クラスターには少なくとも 1 つの NodeType が必要です。 グループ内のすべてのノードは、次の特性を共有します。 <br> **Name** - ノード タイプの名前です。 <br>**Endpoint Ports** - このノード タイプに関連付けられている、さまざまな名前付きエンドポイント (ポート) です。 このマニフェスト内の他のポート番号と競合せず、かつ同じマシン/VM 上で実行している他のアプリケーションでまだ使われていなければ、どのポート番号でも使用できます。 <br> **Placement Properties** - このノード タイプの特性を表します。この特性は、システム サービスや独自のサービスを配置するうえでの制約として使用します。 これらの特性は、特定のノードを補足するメタデータとして、ユーザー定義のキー/値のペアで記述されます。 ノードのプロパティの例としては、ハード ドライブやグラフィック カードの有無、ハード ドライブ内のスピンドル数、コア数などの物理的な特性があります。 <br> **Capacities** - ノード容量は、特定のノードで使用できるように確保されている特定のリソースの名前と量を定義します。 たとえば、"MemoryInMb" と呼ばれるメトリックに対して既定で 2,048 MB のメモリを使用できるようにノードの容量を定義できます。 実行時にはこの容量に基づき、特定のリソース量を必要とするサービスが確実に、そのリソースが確保されているノードに配置されます。<br>**IsPrimary**: 複数の NodeType が定義されている場合は、必ず 1 つのみをプライマリに設定してください (値を *true* にする)。プライマリでシステム サービスが実行されます。 その他のすべてのノード タイプは値 *false* に設定する必要があります。 |
| **Nodes** |クラスターに参加しているノードごとの詳細情報です (ノード タイプ、ノード名、IP アドレス、ノードの障害ドメインとアップグレード ドメイン)。 ここには、クラスターの作成先となるマシンとその IP アドレスを列挙する必要があります。 <br> すべてのノードに同じ IP アドレスを使用した場合、ワンボックス クラスターが作成されます。ワンボックス クラスターはあくまでテスト用です。 運用環境のワークロードをデプロイする用途には使用しないでください。 |

クラスター構成のすべての設定を目的のクラスター環境に合わせて構成した後で、その環境に対して構成をテストできます (手順 7)。

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>環境のセットアップ

クラスター管理者が Service Fabric スタンドアロン クラスターを構成する場合、環境は次の条件に合わせて設定する必要があります。 <br>
1. クラスターを作成するユーザーには、クラスター構成ファイルにノードとして記載されているすべてのマシンに対する管理者レベルのセキュリティ特権が必要です。
2. クラスターの作成元になるマシン、および各クラスター ノード マシンでは次の操作を行う必要があります。
   * Service Fabric SDK をアンインストールします。
   * Service Fabric ランタイムをアンインストールにします。
   * Windows Firewall サービス (mpssvc) を有効にします。
   * リモート レジストリ サービス (リモート レジストリ) を有効にします。
   * ファイル共有 (SMB) を有効化にします。
   * クラスター構成のポートに基づいて必要なポートを開放します。
   * Windows SMB とリモート レジストリ サービスに必要なポート 135、137、138、139、445 を開放します
   * 各マシンがネットワークで接続できるようにします。
3. クラスター ノード マシンはドメイン コントローラーにしないでください。
4. デプロイ予定のクラスターがセキュリティで保護されたクラスターである場合は、まず必要なセキュリティの前提条件を検証してから、目的の構成に合わせて適切に構成します。
5. クラスター マシンにインターネットでアクセスできない場合は、クラスター構成で次を設定します。
   * テレメトリを無効にする: *properties* で *"enableTelemetry": false* と設定します
   * Fabric バージョンの自動ダウンロードおよび現在のクラスター バージョンのサポート終了が近づいていることの通知を無効にする: *properties* で *"fabricClusterAutoupgradeEnabled": true* と設定します
   * また、ネットワークのインターネット アクセスが許可リストに含まれるドメインに制限されている場合、自動アップグレードには go.microsoft.com download.microsoft.com というドメインが必要になります

6. Service Fabric のウイルス対策の対象外項目を適切に設定します。

| **ウイルス対策の対象外ディレクトリ** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (クラスター構成による) |
| FabricLogRoot (クラスター構成による) |

| **ウイルス対策の対象外プロセス** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>TestConfiguration スクリプトを使用して環境を検証する
スタンドアロンのパッケージには、TestConfiguration.ps1 スクリプトが含まれています。 このスクリプトは、上記の条件の一部を検証を検証するベスト プラクティス アナライザーとして用いるものであり、サニティ チェックとして使用して指定の環境にクラスターをデプロイできるかどうかを検証する必要があります。 問題が発生した場合は、「[環境のセットアップ](service-fabric-cluster-standalone-deployment-preparation.md)」のリストを参照してトラブルシューティングを行ってください。

このスクリプトは、クラスター構成ファイルにノードとして列挙されているすべてのマシンに管理者アクセスできれば、どのマシンでも実行できます。 このスクリプトが実行されるマシンがクラスターに属している必要はありません。

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
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

現在、この構成テスト モジュールではセキュリティ構成は検証されないため、別個に検証を行う必要があります。

> [!NOTE]
> このモジュールの信頼性を高めるために絶えず改善を行っていますので、不備や、現時点では TestConfiguration により検出されないと思われるケースがある場合には、[サポート チャネル](./service-fabric-support.md) からお知らせください。
>
>

## <a name="next-steps"></a>次のステップ
* [Windows Server で実行されるスタンドアロン クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)
