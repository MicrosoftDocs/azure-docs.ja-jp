---
title: VM insights の有効化の概要
description: VM insights をデプロイして構成する方法について説明します。 システム要件を確認してください。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: bb2e12082b80c397eec27409b1177379a92fdd7d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102634160"
---
# <a name="enable-vm-insights-overview"></a>VM insights の有効化の概要

この記事では、VM insights を有効にして、次の正常性とパフォーマンスを監視するために使用できるオプションの概要について説明します。

- Azure の仮想マシン 
- Azure Virtual Machine Scale Sets
- Azure Arc に接続されているハイブリッド仮想マシン
- オンプレミスの仮想マシン
- 別のクラウド環境でホストされている仮想マシン  

VM insights を設定するには、次の手順を実行します。

* Azure portal のメニューから直接 **[Insights]** を選択して、単一の Azure 仮想マシン、Azure 仮想マシン スケール セット、または Azure Arc マシンを有効にします。
* Azure Policy を使用して、複数の Azure 仮想マシン、Azure 仮想マシン スケール セット、または Azure Arc マシンを有効にします。 この方法を使用すると、既存および新規の VM とスケール セットに、必要な依存関係が確実にインストールされ、適切に構成されます。 準拠していない仮想マシンとスケール セットがレポートされるので、それらを有効にするかどうかと、それらを修復するかどうかを決めることができます。
* PowerShell を使用して、指定されたサブスクリプションまたはリソース グループにわたって、複数の Azure 仮想マシン、Azure Arc 仮想マシン、Azure 仮想マシン スケール セット、または Azure Arc マシンを有効にします。
* VM insights を有効にして、企業ネットワークまたは他のクラウド環境でホストされている仮想マシンまたは物理コンピューターを監視します。

## <a name="supported-machines"></a>サポートされているマシン
VM insights では、次のマシンがサポートされています。

- Azure 仮想マシン
- Azure 仮想マシン スケール セット
- Azure Arc に接続されたハイブリッド仮想マシン


## <a name="supported-azure-arc-machines"></a>サポートされている Azure Arc マシン
VM insights は、Arc 拡張サービスが利用可能なリージョンの Azure Arc 対応サーバーで使用できます。 Arc エージェントのバージョン 0.9 以降を実行している必要があります。

| 接続先ソース | サポートされています | 説明 |
|:--|:--|:--|
| Windows エージェント | はい | Windows エージェントには、[Windows の Log Analytics エージェント](../agents/log-analytics-agent.md)の他に、Dependency Agent が必要です。 詳細については、[サポートされているオペレーティング システム](../agents/agents-overview.md#supported-operating-systems)のセクションを参照してください。 |
| Linux エージェント | はい | Linux エージェントには、[Linux の Log Analytics エージェント](../agents/log-analytics-agent.md)の他に、Dependency Agent が必要です。 詳細については、[サポートされているオペレーティング システム](#supported-operating-systems)のセクションを参照してください。 |
| System Center Operations Manager 管理グループ | いいえ | |

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

VM insights では、Log Analytics エージェントと Dependency Agent をサポートするすべてのオペレーティング システムをサポートしています。 完全な一覧については、[Azure Monitor エージェントの概要](../agents/agents-overview.md#supported-operating-systems)に関する記事をご覧ください。

> [!IMPORTANT]
> パブリック プレビュー段階の間、VM insights のゲストの正常性機能では、オペレーティング システムのサポートが制限されています。 詳細な一覧については、「[VM insights のゲストの正常性 (プレビュー) を有効にする](../vm/vminsights-health-enable.md)」を参照してください。

### <a name="linux-considerations"></a>Linux に関する考慮事項
VM insights をサポートする Dependency Agent の Linux サポートについては、次の考慮事項の一覧を確認してください。

- 既定と SMP Linux のカーネル リリースのみがサポートされています。
- Physical Address Extension (PAE) や Xen などの非標準のカーネル リリースは、どの Linux ディストリビューションでもサポートされていません。 たとえば、リリースの文字列が *2.6.16.21-0.8-xen* であるシステムはサポートされていません。
- カスタム カーネル (標準カーネルの再コンパイルを含む) はサポートされていません。
- バージョン 9.4 以外の Debian ディストリビューションでは、マップ機能はサポートされておらず、パフォーマンス機能は [Azure Monitor] メニューからのみ使用できます。 Azure VM の左側のウィンドウから直接使用することはできません。
- CentOSPlus カーネルはサポートされています。

Spectre と Meltdown の脆弱性のために、Linux カーネルに修正プログラムを適用する必要があります。 詳細については、Linux ディストリビューション ベンダーに問い合わせてください。 次のコマンドを実行して、Spectre または Meltdown が軽減されているかどうかを確認します。

```
$ grep . /sys/devices/system/cpu/vulnerabilities/*
```

このコマンドの出力は次のようになり、マシンがどちらの問題に対して脆弱であるかを特定します。 これらのファイルが見つからない場合、マシンには修正プログラムが適用されていません。

```
/sys/devices/system/cpu/vulnerabilities/meltdown:Mitigation: PTI
/sys/devices/system/cpu/vulnerabilities/spectre_v1:Vulnerable
/sys/devices/system/cpu/vulnerabilities/spectre_v2:Vulnerable: Minimal generic ASM retpoline
```


## <a name="log-analytics-workspace"></a>Log Analytics ワークスペース
VM insights には Log Analytics ワークスペースが必要です。 このワークスペースの詳細と要件については、「[VM insights 用に Log Analytics ワークスペースを構成する](vminsights-configure-workspace.md)」を参照してください。
## <a name="agents"></a>エージェント
VM insights では、監視する各仮想マシンまたは仮想マシン スケール セットに、次の 2 つのエージェントをインストールする必要があります。 リソースをオンボードするには、これらのエージェントをインストールし、ワークスペースに接続します。  これらのエージェントのネットワーク要件については、「[ネットワークの要件](../agents/log-analytics-agent.md#network-requirements)」を参照してください。

- [Log Analytics エージェント](../agents/log-analytics-agent.md)。 仮想マシンまたは仮想マシン スケール セットからイベントおよびパフォーマンス データを収集し、Log Analytics ワークスペースに配信します。 Azure リソース上の Log Analytics エージェントのデプロイ方法では、[Windows](../../virtual-machines/extensions/oms-windows.md) および [Linux](../../virtual-machines/extensions/oms-linux.md) 用の VM 拡張機能を使用します。
- Dependency Agent。 仮想マシンで実行されているプロセス、および外部プロセスの依存関係に関する検出データを収集します。これは、[VM insights のマップ機能](../vm/vminsights-maps.md)で使用されます。 Dependency Agent は、Azure Monitor へのデータの配信に関して Log Analytics エージェントに依存しています。 Azure リソース上の Dependency Agent のデプロイ方法では、[Windows](../../virtual-machines/extensions/agent-dependency-windows.md) および [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) 用の VM 拡張機能を使用します。

> [!NOTE]
> Log Analytics エージェントは、System Center Operations Manager に使用されているのと同じエージェントです。 VM insights では、直接接続されており、かつ Dependency Agent がインストールされている場合に、Operations Manager によって監視されているエージェントも監視できます。 [管理グループ接続](../tform/../agents/om-agents.md)を介して Azure Monitor に接続されているエージェントは、VM insights では監視できません。

これらのエージェントをデプロイするための複数の方法を次に示します。 

| メソッド | 説明 |
|:---|:---|
| [Azure Portal](../vm/vminsights-enable-portal.md) | Azure Arc に接続されている 1 つの仮想マシン、仮想マシン スケール セット、またはハイブリッド仮想マシンに両方のエージェントをインストールします。 |
| [Resource Manager テンプレート](../vm/vminsights-enable-resource-manager.md) | CLI と PowerShell を含む Resource Manager テンプレートをデプロイするために、サポートされているいずれかの方法を使用して両方のエージェントをインストールします。 |
| [Azure Policy](../vm/vminsights-enable-policy.md) | 仮想マシンまたは仮想マシン スケール セットの作成時にエージェントを自動的にインストールするように、Azure Policy イニシアチブを割り当てます。 |
| [手動インストール](../vm/vminsights-enable-hybrid.md) | データセンターや他のクラウド環境など、Azure の外部でホストされているコンピューター上のゲスト オペレーティング システムにエージェントをインストールします。 |


## <a name="network-requirements"></a>ネットワークの要件

- Log Analytics エージェントのネットワーク要件については、「[ネットワークの要件](../agents/log-analytics-agent.md#network-requirements)」を参照してください。
- 依存関係エージェントには、仮想マシンからアドレス 169.254.169.254 への接続が必要です。 これは、Azure メタデータ サービス エンドポイントです。 ファイアウォール設定で、このエンドポイントへの接続が許可されていることを確認します。


## <a name="management-packs"></a>管理パック
Log Analytics ワークスペースが VM insights 用に構成されている場合、そのワークスペースに接続されているすべての Windows コンピューターに 2 つの管理パックが転送されます。 これらの管理パックの名前は *Microsoft.IntelligencePacks.ApplicationDependencyMonitor* と *Microsoft.IntelligencePacks.VMInsights* で、 *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs* に書き込まれています。 

*ApplicationDependencyMonitor* 管理パックで使用されるデータ ソースは、 *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll* です。 *VMInsights* 管理パックによって使用されるデータ ソースは、 *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\ Microsoft.VirtualMachineMonitoringModule.dll* です。

## <a name="diagnostic-and-usage-data"></a>診断と使用状況データ

Microsoft は、お客様による Azure Monitor サービスの使用を通して、使用状況とパフォーマンス データを自動的に収集します。 Microsoft はこのデータを使用して、サービスの品質、セキュリティ、および整合性の向上に努めています。 

正確で効果的なトラブルシューティング機能を提供するために、マップ機能にはソフトウェアの構成に関するデータが含まれています。 このデータによって、オペレーティング システムとバージョン、IP アドレス、DNS 名、ワークステーション名などの情報が提供されます。 名前や住所などの連絡先情報は収集されません。

データの収集と使用の詳細については、「[Microsoft オンライン サービスのプライバシーに関する声明](https://go.microsoft.com/fwlink/?LinkId=512132)」を参照してください。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>次のステップ

パフォーマンスの監視機能の使用方法については、[VM insights のパフォーマンスの表示](../vm/vminsights-performance.md)に関する記事をご覧ください。 検出されたアプリケーションの依存関係を表示するには、[VM insights のマップの表示](../vm/vminsights-maps.md)に関する記事をご覧ください。
