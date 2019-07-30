---
title: Azure Monitor for VMs の有効化 (プレビュー) の概要 | Microsoft Docs
description: Azure Monitor for VMs のデプロイと構成の方法を説明します。 システム要件を確認してください。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: magoedte
ms.openlocfilehash: 6fb7e6b9611b28dab856209aaf03aa93c25d3968
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478044"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Azure Monitor for VMs の有効化 (プレビュー) の概要

この記事では、Azure Monitor for VMs の設定に使用できるオプションの概要について説明します。 正常性とパフォーマンスを監視するには、Azure Monitor for VMs を使用します。 Azure 仮想マシン (VM) と仮想マシンのスケール セット、オンプレミスの VM、または別のクラウド環境でホストされている VM で実行されるアプリケーションの依存関係を発見します。  

Azure Monitor for VMs を設定する方法は、次のとおりです。

* VM または仮想マシン スケール セットから直接 **Insights (プレビュー)** を選択して、単一の Azure VM または仮想マシン スケール セットを有効にします。
* Azure Policy を使用して、複数の Azure VM および仮想マシン スケール セットを有効にします。 この方法を使用すると、既存および新規の VM とスケール セットに、必要な依存関係が確実にインストールされ、適切に構成されます。 準拠していない VM とスケール セットがレポートされるので、それらを有効にするかどうかと、それらを修復するかどうかを決めることができます。
* PowerShell を使用して、指定したサブスクリプションまたはリソース グループ全体の複数の Azure VM または仮想マシン スケール セットを有効にします。
* 企業ネットワークまたはその他のクラウド環境でホストされている VM または物理コンピューターを監視するために、Azure Monitor for VMs を有効にします。

## <a name="prerequisites"></a>前提条件

始める前に、次のセクションの情報を理解しておいてださい。

### <a name="log-analytics"></a>Log Analytics

Azure Monitor for VMs は、次のリージョンで Log Analytics ワークスペースをサポートします。

- 米国中西部
- 米国西部 2<sup>1</sup>
- East US
- カナダ中部
- 英国南部
- 西ヨーロッパ
- 東南アジア

<sup>1</sup> このリージョンでは、Azure Monitor for VMs の正常性の機能はサポートされていません。

>[!NOTE]
>任意のリージョンから Azure VM をデプロイすることができます。 これらの VM は、Log Analytics ワークスペースでサポートされているリージョンに限定されません。
>

ワークスペースがない場合は、次のいずれかのリソースを使用して作成できます。
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure ポータル](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Azure portal で単一の Azure VM または仮想マシン スケール セットに対する監視を有効にしているときにも、ワークスペースを作成できます。

Azure Policy、Azure PowerShell、または Azure Resource Manager テンプレートを使用した大規模なシナリオを設定するには、Log Analytics ワークスペースで次の操作を行います。

* ServiceMap および InfrastructureInsights ソリューションをインストールします。 提供されている Azure Resource Manager テンプレートを使用して、このインストールを完了できます。 または、 **[はじめに]** タブで **[ワークスペースの構成]** を選択します。
* パフォーマンス カウンターを収集するように Log Analytics ワークスペースを構成します。

大規模シナリオ用ワークスペースを構成するには、次のいずれかの方法を使用します。

* [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace) を使用します。
* Azure Monitor for VMs の [ **[ポリシー対象範囲]** ](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) ページで、 **[ワークスペースの構成]** を選択します。 

### <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

次の表は、Azure Monitor for VMs でサポートされている Windows および Linux オペレーティング システムの一覧です。 メジャーおよびマイナーの Linux OS リリースとサポートされているカーネルのバージョンの詳細な一覧は、このセクションの後の方にあります。

|OS バージョン |パフォーマンス |マップ |Health |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | X |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6、7| X | X| X |
|Ubuntu 14.04、16.04、18.04 | X | X | X |
|CentOS Linux 6、7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Debian 8、9.4 | X<sup>1</sup> | | X |

<sup>1</sup> Azure Monitor for VMs のパフォーマンス機能は、Azure Monitor からのみ使用できます。 Azure VM の左側のウィンドウから直接使用することはできません。

>[!NOTE]
>Azure Monitor for VMs の正常性機能では、Azure VM の[入れ子になった仮想化](../../virtual-machines/windows/nested-virtualization.md)はサポートされていません。
>

>[!NOTE]
>Linux オペレーティング システムでは、次のようになっています。
> - 既定と SMP Linux のカーネル リリースのみがサポートされています。
> - Physical Address Extension (PAE) や Xen などの非標準のカーネル リリースは、どの Linux ディストリビューションでもサポートされていません。 たとえば、リリースの文字列が *2.6.16.21-0.8-xen* であるシステムはサポートされていません。
> - カスタム カーネル (標準カーネルの再コンパイルを含む) はサポートされていません。
> - CentOSPlus カーネルはサポートされています。

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS バージョン | カーネル バージョン |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS バージョン | カーネル バージョン |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

#### <a name="centosplus"></a>CentOSPlus
| OS バージョン | カーネル バージョン |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| OS バージョン | カーネル バージョン |
|:--|:--|
| Ubuntu 18.04 | カーネル 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | カーネル 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS バージョン | カーネル バージョン
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |
|12 SP4 | 4.4.* |
|12 SP4 | Azure で調整されたカーネル |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

Azure Monitor for VMs のマップ機能では、Microsoft Dependency Agent からデータが取得されます。 Dependency Agent は、Log Analytics への接続に関して Log Analytics エージェントに依存しています。 そのため、お使いのシステムに Log Analytics エージェントをインストールし、Dependency Agent を使用して構成する必要があります。

単一の Azure VM に対して Azure Monitor for VMs を有効にするか、または大規模なデプロイ方法を使用するかにかかわらず、Azure VM Dependency Agent 拡張機能を使用し、エクスペリエンスの一部としてエージェントをインストールします。

ハイブリッド環境では、手動で Dependency Agent をダウンロードしてインストールできます。 VM が Azure の外部でホストされている場合は、自動化されたデプロイ方法を使用してください。

次の表では、ハイブリッド環境でマップ機能がサポートしている接続先ソースについて説明します。

| 接続先ソース | サポートされています | 説明 |
|:--|:--|:--|
| Windows エージェント | はい | Windows エージェントには、[Windows の Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md)の他に、Dependency Agent が必要です。 詳細については、[サポートされているオペレーティング システム](#supported-operating-systems)のセクションを参照してください。 |
| Linux エージェント | はい | Linux エージェントには、[Linux の Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md)の他に、Dependency Agent が必要です。 詳細については、[サポートされているオペレーティング システム](#supported-operating-systems)のセクションを参照してください。 |
| System Center Operations Manager 管理グループ | いいえ | |

Dependency Agent は、以下の場所からダウンロードできます。

| ファイル | OS | バージョン | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

Azure Monitor for VMs の機能を有効にしてアクセスするには、"*Log Analytics 共同作成者*" ロールが必要です。 パフォーマンス、正常性、およびマップのデータを表示するには、Azure VM に対する "*監視閲覧者*" ロールが必要です。 Azure Monitor for VMs 用に Log Analytics ワークスペースを構成する必要があります。

Log Analytics ワークスペースへのアクセスを制御する方法の詳細については、「[ワークスペースを管理する](../../azure-monitor/platform/manage-access.md)」を参照してください。

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Azure Monitor for VMs を有効にする方法 (プレビュー)

この表で説明されているいずれかの方法で、Azure Monitor for VMs を有効にします。

| デプロイの状態 | Method | 説明 |
|------------------|--------|-------------|
| 単一の Azure VM または仮想マシン スケール セット | [VM から有効にする](vminsights-enable-single-vm.md) | 単一の Azure VM を有効にするには、VM または仮想マシン スケール セットから直接 **Insights (プレビュー)** を選択します。 |
| 複数の Azure VM または仮想マシン スケール セット | [Azure Policy を介して有効にする](vminsights-enable-at-scale-policy.md) | Azure Policy と使用可能なポリシー定義を使用すると、複数の Azure VM を有効にできます。 |
| 複数の Azure VM または仮想マシン スケール セット | [Azure PowerShell または Azure Resource Manager テンプレートを介して有効にする](vminsights-enable-at-scale-powershell.md) | Azure PowerShell または Azure Resource Manager テンプレートを使用すると、指定したサブスクリプションまたはリソース グループ全体の複数の Azure VM または仮想マシン スケール セットを有効にできます。 |
| ハイブリッド クラウド | [ハイブリッド環境用に有効にする](vminsights-enable-hybrid-cloud.md) | ご自身のデータ センターやその他のクラウド環境でホストされている VM または物理コンピューターにデプロイできます。 |

## <a name="performance-counters-enabled"></a>パフォーマンス カウンターの有効化 

Azure Monitor for VMs では、使用されているパフォーマンス カウンターが収集されるように Log Analytics ワークスペースが構成されます。 次の表は、60 秒ごとに収集されるオブジェクトとカウンターの一覧です。

### <a name="windows-performance-counters"></a>Windows パフォーマンス カウンター

|オブジェクト名 |カウンター名 |
|------------|-------------|
|LogicalDisk |% Free Space |
|LogicalDisk |Avg.Disk sec/Read |
|LogicalDisk |Avg.Disk sec/Transfer |
|LogicalDisk |Avg.Disk sec/Write |
|LogicalDisk |Disk Bytes/sec |
|LogicalDisk |Disk Read Bytes/sec |
|LogicalDisk |Disk Reads/sec |
|LogicalDisk |Disk Transfers/sec |
|LogicalDisk |Disk Write Bytes/sec |
|LogicalDisk |Disk Writes/sec |
|LogicalDisk |Free Megabytes |
|メモリ |Available MBytes |
|ネットワーク アダプター |Bytes Received/sec |
|ネットワーク アダプター |Bytes Sent/sec |
|プロセッサ |% Processor Time |

### <a name="linux-performance-counters"></a>Linux パフォーマンス カウンター

|オブジェクト名 |カウンター名 |
|------------|-------------|
|論理ディスク |% Used Space |
|論理ディスク |Disk Read Bytes/sec |
|論理ディスク |Disk Reads/sec |
|論理ディスク |Disk Transfers/sec |
|論理ディスク |Disk Write Bytes/sec |
|論理ディスク |Disk Writes/sec |
|論理ディスク |Free Megabytes |
|論理ディスク |Logical Disk Bytes/sec |
|メモリ |Available MBytes Memory |
|ネットワーク |Total Bytes Received |
|ネットワーク |Total Bytes Transmitted |
|プロセッサ |% Processor Time |

## <a name="diagnostic-and-usage-data"></a>診断と使用状況データ

Microsoft は、お客様による Azure Monitor サービスの使用を通して、使用状況とパフォーマンス データを自動的に収集します。 Microsoft はこのデータを使用して、サービスの品質、セキュリティ、および整合性の向上に努めています。 

正確で効果的なトラブルシューティング機能を提供するために、マップ機能にはソフトウェアの構成に関するデータが含まれています。 このデータによって、オペレーティング システムとバージョン、IP アドレス、DNS 名、ワークステーション名などの情報が提供されます。 名前や住所などの連絡先情報は収集されません。

データの収集と使用の詳細については、「[Microsoft オンライン サービスのプライバシーに関する声明](https://go.microsoft.com/fwlink/?LinkId=512132)」を参照してください。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

VM の監視が有効になったので、Azure Monitor for VMs での分析に監視情報を使用できます。

## <a name="next-steps"></a>次の手順

正常性機能の使用方法については、[Azure Monitor for VMs の正常性の表示](vminsights-health.md)に関する記事をご覧ください。 検出されたアプリケーションの依存関係を表示するには、[Azure Monitor for VMs のマップの表示](vminsights-maps.md)に関する記事をご覧くださいい。
