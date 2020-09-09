---
title: Azure Monitor for VMs の有効化の概要
description: Azure Monitor for VMs のデプロイと構成の方法を説明します。 システム要件を確認してください。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: e3c5f6d7e04620cf36f6cd952467d47afd775b19
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87824768"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Azure Monitor for VMs の有効化の概要

この記事では、Azure Monitor for VMs を有効にして、次の正常性とパフォーマンスを監視するために使用できるオプションの概要について説明します。

- Azure の仮想マシン 
- Azure Virtual Machine Scale Sets
- Azure Arc に接続されているハイブリッド仮想マシン
- オンプレミスの仮想マシン
- 別のクラウド環境でホストされている仮想マシン  

Azure Monitor for VMs を設定する方法は、次のとおりです。

* Azure portal のメニューから直接 **[Insights]** を選択して、単一の Azure VM、Azure VMSS、または Azure Arc マシンを有効にします。
* Azure Policy を使用して、複数の Azure VM、Azure VMSS、または Azure Arc マシンを有効にします。 この方法を使用すると、既存および新規の VM とスケール セットに、必要な依存関係が確実にインストールされ、適切に構成されます。 準拠していない VM とスケール セットがレポートされるので、それらを有効にするかどうかと、それらを修復するかどうかを決めることができます。
* PowerShell を使用して、指定されたサブスクリプションまたはリソース グループにわたって、複数の Azure VM、Azure Arc VM、Azure VMSS、または Azure Arc マシンを有効にします。
* 企業ネットワークまたはその他のクラウド環境でホストされている VM または物理コンピューターを監視するために、Azure Monitor for VMs を有効にします。

## <a name="prerequisites"></a>前提条件

始める前に、次のセクションの情報を理解しておいてださい。 

>[!NOTE]
>このセクションで説明する次の情報は、[Service Map ソリューション](service-map.md)にも適用できます。  

### <a name="log-analytics"></a>Log Analytics

Azure Monitor for VMs は、次のリージョンで Log Analytics ワークスペースをサポートします。

- 米国中西部
- 米国西部
- 米国西部 2
- 米国中南部
- 米国東部
- 米国東部 2
- 米国中部
- 米国中北部
- US Gov アリゾナ州
- US Gov バージニア州
- カナダ中部
- 英国南部
- 北ヨーロッパ
- 西ヨーロッパ
- 東アジア
- 東南アジア
- インド中部
- 東日本
- オーストラリア東部
- オーストラリア南東部

>[!NOTE]
>Azure VM は任意のリージョンで監視できます。 VM 自体は、Log Analytics ワークスペースでサポートされているリージョンに限定されません。
>

Log Analytics ワークスペースがない場合は、次のいずれかのリソースを使用して作成できます。
* [Azure CLI](../learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Portal](../learn/quick-create-workspace.md)
* [Azure Resource Manager](../platform/template-workspace-configuration.md)

- Azure 仮想マシン
- Azure 仮想マシン スケール セット
- Azure Arc に接続されているハイブリッド仮想マシン

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

次の表は、Azure Monitor for VMs でサポートされている Windows および Linux オペレーティング システムの一覧です。 メジャーおよびマイナーの Linux OS リリースとサポートされているカーネルのバージョンの詳細な一覧は、このセクションの後の方にあります。

|OS バージョン |パフォーマンス |マップ |
|-----------|------------|-----|
|Windows Server 2019 | x | x |
|Windows Server 2016 1803 | x | x |
|Windows Server 2016 | X | x |
|Windows Server 2012 R2 | X | x |
|Windows Server 2012 | X | x |
|Windows Server 2008 R2 | X | x|
|Windows 10 1803 | X | x |
|Windows 8.1 | X | x |
|Windows 8 | X | x |
|Windows 7 SP1 | X | x |
|Red Hat Enterprise Linux (RHEL) 6、7| X | x| 
|Ubuntu 18.04、16.04 | X | x |
|CentOS Linux 7、6 | X | x |
|SUSE Linux Enterprise Server (SLES) 12 | X | x |
|Debian 9.4、8 | X<sup>1</sup> | |

<sup>1</sup> Azure Monitor for VMs のパフォーマンス機能は、Azure Monitor からのみ使用できます。 Azure VM の左側のウィンドウから直接使用することはできません。

>[!NOTE]
>Linux オペレーティング システムでは、次のようになっています。
> - 既定と SMP Linux のカーネル リリースのみがサポートされています。
> - Physical Address Extension (PAE) や Xen などの非標準のカーネル リリースは、どの Linux ディストリビューションでもサポートされていません。 たとえば、リリースの文字列が *2.6.16.21-0.8-xen* であるシステムはサポートされていません。
> - カスタム カーネル (標準カーネルの再コンパイルを含む) はサポートされていません。
> - CentOSPlus カーネルはサポートされています。
> - Spectre の脆弱性のために、Linux カーネルに修正プログラムを適用する必要があります。 詳細については、Linux ディストリビューション ベンダーに問い合わせてください。

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS バージョン | カーネル バージョン |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS バージョン | カーネル バージョン |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| OS バージョン | カーネル バージョン |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| OS バージョン | カーネル バージョン |
|:--|:--|
| 18.04 | 5.3.0-1020<br>5.0 (Azure で調整されたカーネルを含む)<br>4.18 *<br>4.15* |
| 16.04.3 | 4.15。* |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS バージョン | カーネル バージョン |
|:--|:--|
|12 SP4 | 4.12。* (Azure で調整されたカーネルを含む) |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| OS バージョン | カーネル バージョン |
|:--|:--|
| 9 | 4.9 | 

## <a name="supported-azure-arc-machines"></a>サポートされている Azure Arc マシン
Azure Monitor for VMs は、Arc 拡張サービスが利用可能なリージョンの Azure Arc 対応サーバーで使用できます。 Arc エージェントのバージョン 0.9 以降を実行している必要があります。

| 接続先ソース | サポートされています | 説明 |
|:--|:--|:--|
| Windows エージェント | はい | Windows エージェントには、[Windows の Log Analytics エージェント](../platform/log-analytics-agent.md)の他に、Dependency Agent が必要です。 詳細については、[サポートされているオペレーティング システム](#supported-operating-systems)のセクションを参照してください。 |
| Linux エージェント | はい | Linux エージェントには、[Linux の Log Analytics エージェント](../platform/log-analytics-agent.md)の他に、Dependency Agent が必要です。 詳細については、[サポートされているオペレーティング システム](#supported-operating-systems)のセクションを参照してください。 |
| System Center Operations Manager 管理グループ | いいえ | |

## <a name="agents"></a>エージェント
Azure Monitor for VMs では、監視する各仮想マシンまたは仮想マシン スケール セットに、次の 2 つのエージェントをインストールする必要があります。 これらのエージェントをインストールしてワークスペースに接続することは、リソースをオンボードするための唯一の要件です。

- [Log Analytics エージェント](../platform/log-analytics-agent.md)。 仮想マシンまたは仮想マシン スケール セットからイベントおよびパフォーマンス データを収集し、Log Analytics ワークスペースに配信します。 Azure リソース上の Log Analytics エージェントのデプロイ方法では、[Windows](../../virtual-machines/extensions/oms-windows.md) および [Linux](../../virtual-machines/extensions/oms-linux.md) 用の VM 拡張機能を使用します。
- Dependency Agent。 仮想マシンで実行されているプロセス、および外部プロセスの依存関係に関する検出データを収集します。これは、[Azure Monitor for VMs のマップの機能](vminsights-maps.md)で使用されます。 Dependency Agent は、Azure Monitor へのデータの配信に関して Log Analytics エージェントに依存しています。 Azure リソース上の Dependency Agent のデプロイ方法では、[Windows](../../virtual-machines/extensions/agent-dependency-windows.md) および [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) 用の VM 拡張機能を使用します。

> [!NOTE]
> Log Analytics エージェントは、System Center Operations Manager に使用されているのと同じエージェントです。 Azure Monitor for VMs では、直接接続されている場合に、Operations Manager によっても監視されているエージェントを監視できます。これらに Dependency Agent をインストールします。 [管理グループ接続](../tform/../platform/om-agents.md)を介して Azure Monitor に接続されているエージェントは、Azure Monitor for VMs では監視できません。

これらのエージェントをデプロイするための複数の方法を次に示します。 

| メソッド | 説明 |
|:---|:---|
| [Azure Portal](./vminsights-enable-portal.md) | Azure Arc に接続されている 1 つの仮想マシン、仮想マシン スケール セット、またはハイブリッド仮想マシンに両方のエージェントをインストールします。 |
| [Resource Manager テンプレート](vminsights-enable-powershell.md) | CLI と PowerShell を含む Resource Manager テンプレートをデプロイするために、サポートされているいずれかの方法を使用して両方のエージェントをインストールします。 |
| [Azure Policy](./vminsights-enable-policy.md) | 仮想マシンまたは仮想マシン スケール セットの作成時にエージェントを自動的にインストールするように、Azure Policy イニシアチブを割り当てます。 |
| [手動インストール](./vminsights-enable-hybrid.md) | データセンターや他のクラウド環境など、Azure の外部でホストされているコンピューター上のゲスト オペレーティング システムにエージェントをインストールします。 |




## <a name="management-packs"></a>管理パック
Log Analytics ワークスペースが Azure Monitor for VMs 用に構成されている場合、そのワークスペースに接続されているすべての Windows コンピューターに 2 つの管理パックが転送されます。 これらの管理パックの名前は *Microsoft.IntelligencePacks.ApplicationDependencyMonitor* と *Microsoft.IntelligencePacks.VMInsights* で、*%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\* に書き込まれています。 

*ApplicationDependencyMonitor* 管理パックで使用されるデータ ソースは、 *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll* です。 *VMInsights* 管理パックによって使用されるデータ ソースは、 *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\ Microsoft.VirtualMachineMonitoringModule.dll* です。

## <a name="diagnostic-and-usage-data"></a>診断と使用状況データ

Microsoft は、お客様による Azure Monitor サービスの使用を通して、使用状況とパフォーマンス データを自動的に収集します。 Microsoft はこのデータを使用して、サービスの品質、セキュリティ、および整合性の向上に努めています。 

正確で効果的なトラブルシューティング機能を提供するために、マップ機能にはソフトウェアの構成に関するデータが含まれています。 このデータによって、オペレーティング システムとバージョン、IP アドレス、DNS 名、ワークステーション名などの情報が提供されます。 名前や住所などの連絡先情報は収集されません。

データの収集と使用の詳細については、「[Microsoft オンライン サービスのプライバシーに関する声明](https://go.microsoft.com/fwlink/?LinkId=512132)」を参照してください。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>次のステップ

パフォーマンスの監視機能の使用方法については、[Azure Monitor for VMs のパフォーマンスの表示](vminsights-performance.md)に関する記事をご覧ください。 検出されたアプリケーションの依存関係を表示するには、[Azure Monitor for VMs のマップの表示](vminsights-maps.md)に関する記事をご覧くださいい。