---
title: VMware の評価と移行に関する Azure Migrate サポート マトリックス
description: Azure Migrate サービスを使用して VMware VM を評価して Azure に移行する際にサポートされる設定と制限について要点を説明します。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: raynew
ms.openlocfilehash: a0ca483005cf1f5aaadcd7a6107b092d581d6773
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067769"
---
# <a name="support-matrix-for-vmware-assessment-and-migration"></a>VMware の評価と移行のサポート マトリックス

[Azure Migrate](migrate-overview.md) を使用すると、マシンを評価して Microsoft Azure クラウドに移行することができます。 この記事では、オンプレミスの VMware VM を評価して移行する際にサポートされる設定と制限について概要を説明します。


## <a name="vmware-scenarios"></a>VMware のシナリオ

この表は、VMware VM に関してサポートされるシナリオをまとめたものです。

**Deployment** | **詳細**
--- | ---
**オンプレミスの VMware VM を評価する** | 最初の評価を[設定](tutorial-prepare-vmware.md)します。<br/><br/> 大規模な評価を[実行](scale-vmware-assessment.md)します。
**VMware VM を移行する** | エージェントレス移行を使用して移行することも、エージェントベース移行を使用することもできます。 [詳細情報](server-migrate-overview.md)


## <a name="azure-migrate-projects"></a>Azure Migrate プロジェクト

**サポート** | **詳細**
--- | ---
**Azure のアクセス許可** | Azure Migrate プロジェクトを作成するには、サブスクリプションに共同作成者または所有者アクセス許可が必要です。
**VMware の制限事項**  | 1 つのプロジェクトで最大 35,000 台の VMware VM を評価できます。 1 つの Azure サブスクリプションで複数のプロジェクトを作成できます。 評価の上限に達するまでは、1 つのプロジェクトに VMware VM と Hyper-V VM の両方を含めることができます。
**地理的な場所** | Azure Migrate プロジェクトは、いくつかの地域で作成できます。 プロジェクトを作成できるのはこれらの地域に限られますが、ターゲットの場所がそれ以外であるマシンを評価または移行することは可能です。 プロジェクトの地域は、検出されたメタデータを格納するためにのみ使用されます。

**地理的な場所** | **メタデータ ストレージの場所**
--- | ---
Azure Government | 米国政府バージニア州
アジア太平洋 | 東アジアまたは東南アジア
オーストラリア | オーストラリア東部またはオーストラリア南東部
ブラジル | ブラジル南部
カナダ | カナダ中部またはカナダ東部
ヨーロッパ | 北ヨーロッパまたは西ヨーロッパ
フランス | フランス中部
インド | インド中部またはインド南部
日本 |  東日本または西日本
韓国 | 韓国中部または韓国南部
イギリス | 英国南部または英国西部
米国 | 米国中部または米国西部 2


 > [!NOTE]
 > Azure Government は現在、[古いバージョン](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)の Azure Migrate でのみサポートされます。


## <a name="assessment-vcenter-server-requirements"></a>評価 - vCenter Server サーバーの要件

この表は、VMware 仮想化サーバーに対する評価のサポートと制限をまとめたものです。

**サポート** | **詳細**
--- | ---
**vCenter サーバー** | 評価対象となる VMware VM は、5.5、6.0、6.5、6.7 のいずれかを実行している 1 台以上の vCenter Server で管理されている必要があります。

## <a name="assessment-vcenter-server-permissions"></a>評価 - vCenter Server のアクセス許可

評価を行うには、vCenter Server 用の読み取り専用アカウントが必要です。

## <a name="assessment-appliance-requirements"></a>評価 - アプライアンスの要件

Azure Migrate では、軽量アプライアンスを実行して VMware VM を検出し、VM メタデータとパフォーマンス データを Azure Migrate に送信します。 VMware 用アプライアンスは、vCenter Server にインポートされた OVA テンプレートを使用してデプロイされます。 次の表は、アプライアンスの要件をまとめたものです。

**サポート** | **詳細**
--- | ---
**アプライアンスのデプロイ** | アプライアンスは VMware VM としてデプロイします。 32 GB の RAM、8 つの vCPU、約 80 GB のディスク記憶域、外部仮想スイッチを備えた VM を割り当てるには、vCenter Server に十分なリソースが必要です。<br/><br/> アプライアンスは、直接またはプロキシを介してインターネットにアクセスできる必要があります。<br/> バージョン 5.5 以降が実行されている ESXi ホストにアプライアンス VM をデプロイする必要があります。
**Azure Migrate プロジェクト** | 単一のプロジェクトにアプライアンスを関連付けることができます。 <br/> 任意の数のアプライアンスを 1 つのプロジェクトに関連付けることができます。<br/> プロジェクト内で最大 35,000 個の VM を評価できます。
**検出** | 1 つのアプライアンスで、vCenter Server 上の VMware VM を最大 10,000 台検出できます。<br/> 1 つのアプライアンスは、1 つの vCenter Server に接続できます。
**評価グループ** | 1 つのグループに最大 35,000 個のマシンを追加できます。
**評価** | 1 回の評価で最大 35,000 個の VM を評価できます。


## <a name="assessment-url-access-requirements"></a>評価 - URL アクセス要件

Azure Migrate アプライアンスには、インターネットへの接続が必要です。

- アプライアンスをデプロイすると、下の表にまとめた URL への接続チェックが Azure Migrate によって実行されます。
- URL ベースのプロキシを使用してインターネットに接続している場合は、それらの URL へのアクセスを許可して、URL の探索中に受信されたすべての CNAME レコードがプロキシによって解決されるようにします。

**URL** | **詳細**  
--- | --- |
*.portal.azure.com  | Azure portal で Azure Migrate に移動します。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Azure サブスクリプションにログインします。
*.microsoftonline.com <br/> *.microsoftonline-p.com | アプライアンスが Azure Migrate サービスと通信するための Active Directory アプリを作成します。
management.azure.com | アプライアンスが Azure Migrate サービスと通信するための Active Directory アプリを作成します。
dc.services.visualstudio.com | 内部監視に使用するアプリ ログをアップロードします。
*.vault.azure.net | Azure Key Vault でシークレットを管理します。
*.servicebus.windows.net | アプライアンスと Azure Migrate サービスの間の通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Azure Migrate サービスの URL に接続します。
*.blob.core.windows.net | ストレージ アカウントにデータをアップロードします。
http://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Azure Migrate アプライアンスの更新に使用されます。

## <a name="assessment-port-requirements"></a>評価 - ポートの要件

**デバイス** | **Connection**
--- | ---
アプライアンス | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br/><br/> ポート 44368 で、次の URL を使用してアプライアンス管理アプリにリモートでアクセスするためのインバウンド接続: ```https://<appliance-ip-or-name>:44368``` <br/><br/>ポート 443、5671、5672 で、検出とパフォーマンスのメタデータを Azure Migrate に送信するための送信接続。
vCenter サーバー | TCP ポート 443 で、アプライアンスが評価用に構成およびパフォーマンスのメタデータを収集できるようにするインバウンド接続。 <br/><br/> 既定では、アプライアンスはポート 443 で vCenter に接続します。 vCenter Server が別のポートでリッスンする場合、検出の設定時にポートを変更できます。

## <a name="migration---limitations"></a>移行 - 制限
レプリケーションでは、一度に最大 10 個の VM を選択できます。 より多くのマシンを移行する場合は、10 個単位のグループでレプリケートします。 VMware のエージェントレス移行では、最大 100 個のレプリケーションを同時に実行できます。

## <a name="agentless-migration-vmware-server-requirements"></a>エージェントレス移行 - VMware サーバーの要件

この表は、VMware 仮想化サーバーに対する評価のサポートと制限をまとめたものです。

**サポート** | **詳細**
--- | ---
vCenter Server | バージョン 5.5、6.0、6.5、または 6.7。
VMware vSphere | バージョン 5.5、6.0、6.5、または 6.7。

## <a name="agentless-migration-vcenter-server-permissions"></a>エージェントレス移行 - vCenter Server のアクセス許可

**アクセス許可** | **詳細**
--- | ---
Datastore.Browse | VM のログ ファイルの閲覧を許可して、スナップショットの作成と削除のトラブルシューティングを行います。
Datastore.LowLevelFileOperations | データストア ブラウザーでの読み取り、書き込み、削除、名前変更の各操作を許可して、スナップショットの作成と削除のトラブルシューティングを行います。
VirtualMachine.Configuration.DiskChangeTracking | VM ディスクに対する変更の追跡の有効化または無効化を許可し、スナップショット間における変更済みのデータ ブロックをプルします。
VirtualMachine.Configuration.DiskLease | VM のディスクのリース操作を許可し、VMware vSphere Virtual Disk Development Kit (VDDK) を使用してディスクを読み取ります。
VirtualMachine.Provisioning.AllowReadOnlyDiskAccess | VM 上のディスクを開くことを許可し、VDDK を使用してディスクを読み取ります。
VirtualMachine.Provisioning.AllowVirtualMachineDownload  | VM に関連付けられたファイルの読み取り操作を許可し、エラーが発生した場合にログをダウンロードして、トラブルシューティングを行います。
VirtualMachine.SnapshotManagement.* | レプリケーション用に VM スナップショットの作成と管理を許可します。
Virtual Machine.Interaction.Power Off | Azure への移行中に VM の電源をオフにすることを許可します。


## <a name="agentless-migration-vmware-vm-requirements"></a>エージェントレス移行 - VMware VM の要件

**サポート** | **詳細**
--- | ---
**サポートされているオペレーティング システム** | Azure でサポートされる [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) および [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) オペレーティング システムは、エージェントレス移行を使用して移行できます。
**Azure に必要な変更** | 一部の VM は、Azure で実行できるように変更が必要な場合があります。 次のオペレーティング システムでは、これらの変更が Azure Migrate によって自動的に行われます。<br/> - Red Hat Enterprise Linux 6.5+、7.0+<br/> - CentOS 6.5+、7.0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS、16.04LTS、18.04LTS<br/> - Debian 7、8<br/><br/> その他のオペレーティング システムについては、移行前に手動で調整する必要があります。 関連する記事には、その手順が記載されています。
**Linux ブート** | /boot が専用パーティションに存在する場合は、OS ディスク上に存在する必要があり、複数のディスクに分散していてはいけません。<br/> /boot がルート (/) パーティションに含まれている場合は、"/" パーティションは OS ディスク上に存在する必要があり、他のディスクにまたがっていてはいけません。
**UEFI ブート** | UEFI ブートを使用した VM の移行はサポートされません。
**ディスク サイズ** | 2 TB の OS ディスク。データ ディスク用に 4 TB。
**ディスクの制限** |  VM あたり最大 60 台のディスク。
**暗号化されたディスクまたはボリューム** | 暗号化されたディスクまたはボリュームを含む VM の移行はサポートされません。
**共有ディスク クラスター** | サポートされていません。
**独立ディスク** | サポートされていません。
**RDM またはパススルー ディスク** | RDM またはパススルー ディスクが VM に存在する場合、これらのディスクは Azure にレプリケートされません。
**NFS** | VM 上のボリュームとしてマウントされた NFS ボリュームはレプリケートされません。
**iSCSI ターゲット** | iSCSI ターゲットを含む VM は、エージェントレス移行ではサポートされていません。
**マルチパス IO** | サポートされていません。
**ストレージ vMotion** | サポートされていません。 VM でストレージ vMotion を使用している場合、レプリケーションは機能しません。
**チーミングされた NIC** | サポートされていません。
**IPv6** | サポートされていません。
**ターゲット ディスク** | VM は、Azure 内のマネージド ディスク (Standard HDD、Premium SSD) にのみ移行できます。
**同時レプリケーション** | vCenter Server あたり 100 台の VM。 それ以上ある場合は、100 台単位のバッチで移行します。


## <a name="agentless-migration-appliance-requirements"></a>エージェントレス移行 - アプライアンスの要件


**サポート** | **詳細**
--- | ---
**ESXi** | バージョン 5.5 以降が実行されている ESXi ホストにアプライアンス VM をデプロイする必要があります。
**Azure Migrate プロジェクト** | 1 つのプロジェクトにアプライアンスを関連付けることができます。
**vCenter Server** | 1 つのアプライアンスで、vCenter Server 上の VMware VM を最大 10,000 台検出できます。<br/> 1 つのアプライアンスは 1 つの vCenter Server に接続できます。
**VDDK** | Azure Migrate Server Migration を使用してエージェントレス移行を実行する場合は、VMware vSphere VDDK がアプライアンス VM にインストールされている必要があります。

## <a name="agentless-migration-url-access-requirements"></a>エージェントレス移行 - URL アクセスの要件

Azure Migrate アプライアンスには、インターネット接続が必要です。

- アプライアンスをデプロイすると、下の表にまとめた URL への接続チェックが Azure Migrate によって実行されます。
- URL ベースのプロキシを使用している場合は、それらの URL へのアクセスを許可して、URL の探索中に受信されたすべての CNAME レコードがプロキシによって解決されるようにします。

**URL** | **詳細**  
--- | ---
*.portal.azure.com | Azure portal で Azure Migrate に移動します。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com  | Azure サブスクリプションにログインします。
*.microsoftonline.com <br/> *.microsoftonline-p.com | アプライアンスが Azure Migrate サービスと通信するための Active Directory アプリを作成します。
management.azure.com | アプライアンスが Azure Migrate サービスと通信するための Active Directory アプリを作成します。
dc.services.visualstudio.com | 内部監視に使用するアプリ ログをアップロードします。
*.vault.azure.net | Azure Key Vault でシークレットを管理します。
*.servicebus.windows.net | アプライアンスと Azure Migrate サービスの間の通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Azure Migrate サービスの URL に接続します。
*.blob.core.windows.net | ストレージ アカウントにデータをアップロードします。
http://aka.ms/latestapplianceservices<br/><br/> https://download.microsoft.com/download | Azure Migrate アプライアンスの更新に使用されます。


## <a name="agentless-migration-port-requirements"></a>エージェントレス移行 - ポートの要件

**デバイス** | **Connection**
--- | ---
アプライアンス | ポート 443 で、レプリケートされたデータを Azure にアップロードしたり、レプリケーションや移行のために Azure Migrate と通信を行ったりするためのアウトバウンド接続。
vCenter サーバー | ポート 443 で、アプライアンスがレプリケーションを調整 (スナップショットの作成、データのコピー、スナップショットのリリース) するためのインバウンド接続
vSphere または EXSI ホスト | TCP ポート 902 で、アプライアンスがスナップショットからデータをレプリケートするためのインバウンド接続。


## <a name="agent-based-migration-vmware-server-requirements"></a>エージェントベース移行 - VMware サーバーの要件

この表は、VMware 仮想化サーバーに対する評価のサポートと制限をまとめたものです。

**サポート** | **詳細**
--- | ---
vCenter Server | バージョン 5.5、6.0、6.5、または 6.7。
VMware vSphere | バージョン 5.5、6.0、6.5、または 6.7。

### <a name="agent-based-migration-vcenter-server-permissions"></a>エージェントベース移行 - vCenter Server のアクセス許可

vCenter Server 用の読み取り専用アカウント。

## <a name="agent-based-migration-replication-appliance-requirements"></a>エージェントベース移行 - レプリケーション アプライアンスの要件

Azure Migrate Server Migration による VMware VM と物理サーバーのエージェントベース移行に使用する[レプリケーション アプライアンス](migrate-replication-appliance.md)の要件を表にまとめました。

> [!NOTE]
> Azure Migrate ハブで提供されている OVA テンプレートを使用してレプリケーション アプライアンスを設定すると、そのアプライアンスは Windows Server 2016 を実行し、サポート要件に準拠しています。 物理サーバーにレプリケーション アプライアンスを手動で設定する場合は、サーバーが要件に準拠していることを確認してください。



**コンポーネント** | **要件**
--- | ---
 | **VMware の設定** (VMware VM アプライアンス)
PowerCLI | VMware VM 上でレプリケーション アプライアンスが実行されている場合は、[PowerCLI バージョン 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) をインストールする必要があります。
NIC の種類 | VMXNET3 (アプライアンスが VMware VM である場合)
 | **ハードウェアの設定**
CPU コア数 | 8
RAM | 16 GB
ディスクの数 | 3 (OS ディスク、プロセス サーバーのキャッシュ ディスク、リテンション ドライブ)
空きディスク領域 (キャッシュ) | 600 GB
ディスクの空き領域 (リテンション ディスク) | 600 GB
**ソフトウェアの設定** |
オペレーティング システム | Windows Server 2016 または Windows Server 2012 R2
オペレーティング システムのロケール | 英語 (en-us)
TLS | TLS 1.2 を有効にする必要があります。
.NET Framework | .NET Framework 4.6 以降が (強力な暗号が有効にされた) マシンにインストールされている必要があります。
MySQL | MySQL をアプライアンスでインストールする必要があります。<br/> MySQL をインストールする必要があります。 手動でインストールするか、アプライアンスのデプロイ中に Site Recovery でインストールすることができます。
その他のアプリ | レプリケーション アプライアンスで他のアプリを実行しないでください。
Windows Server の役割 | これらの役割を有効にしないでください。 <br> - Active Directory Domain Services <br>- インターネット インフォメーション サービス <br> - Hyper-V
グループ ポリシー | これらのグループ ポリシーを有効にしないでください。 <br> - コマンド プロンプトへのアクセス禁止。 <br> - レジストリ編集ツールへのアクセス禁止。 <br> - ファイル添付の信頼ロジック。 <br> - スクリプト実行の有効化。 <br> [詳細情報](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 既存の Web サイトが存在しない <br> - ポート 443 でリッスンしている既存の Web サイト/アプリケーションが存在しない <br>- [匿名認証](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx)を有効にする <br> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定を有効にする
**ネットワーク設定** |
IP アドレスの種類 | 静的
Port | 443 (コントロール チャネルのオーケストレーション)<br>9443 (データ転送)
NIC の種類 | VMXNET3

### <a name="replication-appliance-url-access"></a>レプリケーション アプライアンスの URL アクセス

レプリケーション アプライアンスは、次の URL にアクセスできる必要があります。

**URL** | **詳細**
--- | ---
\*.backup.windowsazure.com | レプリケートされたデータの転送と調整に使用
\*.store.core.windows.net | レプリケートされたデータの転送と調整に使用
\*.blob.core.windows.net | レプリケートされたデータを格納するストレージ アカウントへのアクセスに使用
\*.hypervrecoverymanager.windowsazure.com | レプリケーション管理操作と調整に使用
https:\//management.azure.com | レプリケーション管理操作と調整に使用
*.services.visualstudio.com | テレメトリの目的で使用 (省略可能)
time.nist.gov | システム時刻とグローバル時刻間の時刻同期の確認に使用。
time.windows.com | システム時刻とグローバル時刻間の時刻同期の確認に使用。
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | OVF の設定では、次の URL にアクセスできる必要があります。 Azure Active Directory によるアクセス制御と ID 管理に使用されます。
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | MySQL のダウンロードを完了するためのものです


#### <a name="mysql-installation-options"></a>MySQL のインストール オプション

レプリケーション アプライアンスには、次のいずれかの方法で MySQL をインストールできます。

**メソッド** | **詳細**
--- | ---
手動でダウンロードしてインストールする | MySQL アプリケーションをダウンロードし、C:\Temp\ASRSetup フォルダー内に配置してから手動でインストールします。<br/> アプライアンスを設定すると、MySQL が既にインストールされているものとして表示されます。
オンライン ダウンロードなし | MySQL インストーラー アプリケーションを C:\Temp\ASRSetup フォルダーに配置します。 アプライアンスをインストールし、MySQL をダウンロードおよびインストールするようクリックすると、追加したインストーラーが設定で使用されます。
Azure Migrate でダウンロードしてインストールする | アプライアンスをインストールし、MySQL を求められたら、 **[ダウンロードしてインストール]** を選択します。



## <a name="agent-based-migration-vmware-vm-requirements"></a>エージェントベース移行 - VMware VM の要件

**サポート** | **詳細**
--- | ---
**マシンのワークロード** | Azure Migrate は、サポートされているマシンで実行されている任意のワークロード (Active Directory、SQL サーバーなど) の移行をサポートします。
**オペレーティング システム** | 最新情報については、Site Recovery に関する[オペレーティング システムのサポート](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)を確認してください。 Azure Migrate でも同じ VM オペレーティング システムがサポートされます。
**Linux ファイル システムとゲストのストレージ** | 最新情報については、Site Recovery に関する [Linux ファイル システムのサポート](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)を確認してください。 Azure Migrate でも同じ Linux ファイル システムがサポートされます。
**ネットワークとストレージ** | 最新情報については、Site Recovery の[ネットワーク](../site-recovery/vmware-physical-azure-support-matrix.md#network)と[ストレージ](../site-recovery/vmware-physical-azure-support-matrix.md#storage)の前提条件を確認してください。 Azure Migrate の場合も、ネットワークとストレージの要件は同じです。
**Azure の要件** | 最新情報については、Site Recovery に使用する [Azure ネットワーク](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[ストレージ](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)、[コンピューティング](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)の要件を確認してください。 VMware の移行に関する要件は、Azure Migrate の場合も同じです。
**モビリティ サービス** | 移行対象となる各 VM に、Mobility Service エージェントがインストールされている必要があります。
**UEFI ブート** | Azure 内の移行された VM は、自動的に BIOS ブート VM に変換されます。<br/><br/> OS ディスクには最大 4 つのパーティションが必要で、ボリュームは NTFS でフォーマットされている必要があります。
**ターゲット ディスク** | VM は、Azure 内のマネージド ディスク (Standard HDD、Premium SSD) にのみ移行できます。
**ディスク サイズ** | 2 TB の OS ディスク。データ ディスク用に 8 TB。
**ディスクの制限** |  VM あたり最大 63 台のディスク。
**暗号化されたディスクまたはボリューム** | 暗号化されたディスクまたはボリュームを含む VM の移行はサポートされません。
**共有ディスク クラスター** | サポートされていません。
**独立ディスク** | サポートされています。
**パススルー ディスク** | サポートされています。
**NFS** | VM 上のボリュームとしてマウントされた NFS ボリュームはレプリケートされません。
iSCSI ターゲット | iSCSI ターゲットを含む VM は、エージェントレス移行ではサポートされていません。
**マルチパス IO** | サポートされていません。
**ストレージ vMotion** | サポートされています
**チーミングされた NIC** | サポートされていません。
**IPv6** | サポートされていません。




## <a name="agent-based-migration-url-access-requirements"></a>エージェントベース移行 - URL アクセスの要件

VMware VM 上で動作する Mobility Service には、インターネット接続が必要です。

Mobility Service をデプロイすると、下の表にまとめた URL への接続チェックが自動的に実行されます。


**URL** | **詳細**  
--- | ---
*.portal.azure.com | Azure portal で Azure Migrate に移動します。
*.windows.net | Azure サブスクリプションにログインします。
*.microsoftonline.com | アプライアンスが Azure Migrate サービスと通信するための Active Directory アプリを作成します。
management.azure.com | アプライアンスが Azure Migrate サービスと通信するための Active Directory アプリを作成します。
dc.services.visualstudio.com | 内部監視に使用するアプリ ログをアップロードします。
*.vault.azure.net | Azure Key Vault でシークレットを管理します。
*.servicebus.windows.net | アプライアンスと Azure Migrate サービスの間の通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Azure Migrate サービスの URL に接続します。
*.blob.core.windows.net | ストレージ アカウントにデータをアップロードします。

## <a name="agent-based-migration-port-requirements"></a>エージェントベース移行 - ポートの要件

**デバイス** | **Connection**
--- | ---
VM | VM 上で実行される Mobility Service は、レプリケーション管理のために、インバウンド ポート HTTPS 443 でオンプレミスのレプリケーション アプライアンス (構成サーバー) と通信します。<br/><br/> VM は、受信ポート HTTPS 9443 でレプリケーション データを (構成サーバー マシン上で実行されている) プロセス サーバーに送信します。 このポートは変更可能です。
レプリケーション アプライアンス | レプリケーション アプライアンスは、アウトバウンド ポート HTTPS 443 経由で Azure によるレプリケーションを調整します。
プロセス サーバー | プロセス サーバーは、レプリケーション データを受信し、データを最適化して暗号化し、アウトバウンド ポート 443 経由で Azure ストレージに送信します。<br/> 既定では、プロセス サーバーはレプリケーション アプライアンスで実行されます。

## <a name="azure-vm-requirements"></a>Azure VM の要件

Azure にレプリケートされたオンプレミス VM はすべて、この表にまとめられている Azure VM の要件を満たしている必要があります。 Site Recovery がレプリケーションの前提条件確認を実行するとき、満たされていない要件がある場合には確認が失敗します。

**コンポーネント** | **要件** | **詳細**
--- | --- | ---
ゲスト オペレーティング システム | [エージェントレス レプリケーションを使用した VMware VM](#agentless-migration-vmware-vm-requirements) と[エージェントベース レプリケーションを使用した VMware VM](#agent-based-migration-vmware-vm-requirements) に関するセクションで、サポートされるオペレーティング システムを確認してください。<br/> サポートされているオペレーティング システム上で実行されているすべてのワークロードを移行できます。 | サポートされていない場合、確認は失敗します。
ゲスト オペレーティング システムのアーキテクチャ | 64 ビット。 | サポートされていない場合、確認は失敗します。
オペレーティング システムのディスク サイズ | 最大 2,048 GB。 | サポートされていない場合、確認は失敗します。
オペレーティング システムのディスク数 | 1 | サポートされていない場合、確認は失敗します。
データ ディスク数 | 64 以下。 | サポートされていない場合、確認は失敗します。
データ ディスク サイズ | 最大 4,095 GB | サポートされていない場合、確認は失敗します。
ネットワーク アダプター | 複数のアダプターがサポートされます。 |
共有 VHD | サポートされていません。 | サポートされていない場合、確認は失敗します。
FC ディスク | サポートされていません。 | サポートされていない場合、確認は失敗します。
BitLocker | サポートされていません。 | マシンのレプリケーションを有効にする前に、BitLocker を無効にする必要があります。
VM 名 | 1 から 63 文字。<br/> 名前に使用できるのは、英文字、数字、およびハイフンのみです。<br/><br/> マシン名の最初と最後は、文字か数字とする必要があります。 |  Site Recovery でマシンのプロパティの値を更新します。
移行後の接続 - Windows | 移行後に、Windows が実行されている Azure VM に接続するには:<br/> - 移行前に、オンプレミスの VM で RDP を有効にします。 TCP と UDP の規則が **[パブリック]** プロファイルに追加されていることを確認し、 **[Windows ファイアウォール]**  >  **[許可されたアプリ]** で、すべてのプロファイルで RDP が許可されていることを確認します。<br/> サイト間 VPN アクセスの場合は、RDP を有効にし、 **[Windows ファイアウォール]**  ->  **[許可されたアプリおよび機能]** で**ドメイン ネットワークとプライベート ネットワーク**の RDP を許可します。 さらに、オペレーティング システムの SAN ポリシーが **[OnlineAll]** に設定されていることを確認します。 [詳細情報](https://support.microsoft.com/kb/3031135)。 |
移行後の接続 - Linux | 移行後に、SSH を使用して Azure VM に接続するには:<br/> 移行前に、オンプレミスのマシンで、Secure Shell サービスが [開始] に設定されていることと、ファイアウォール規則で SSH 接続が許可されていることを確認します。<br/> フェールオーバー後の Azure VM で、フェールオーバーされた VM とその接続先の Azure サブネットのネットワーク セキュリティ グループ規則について、SSH ポートへの受信接続を許可します。 さらに、VM のパブリック IP アドレスを追加します。 |  


## <a name="next-steps"></a>次の手順

[VMware の評価と移行の準備](tutorial-prepare-vmware.md)をします。
