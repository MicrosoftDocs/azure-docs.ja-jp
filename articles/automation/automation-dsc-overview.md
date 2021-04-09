---
title: Azure Automation State Configuration の概要
description: この記事では、Azure Automation State Configuration の概要について説明します。
keywords: powershell dsc, 必要な状態の構成, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 01/26/2021
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 752d7f86941967c218b3a57fa163698b9f502057
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897022"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation State Configuration の概要

Azure Automation State Configuration は、任意のクラウドまたはオンプレミスのデータセンターのノードについて PowerShell Desired State Configuration (DSC) の[構成](/powershell/scripting/dsc/configurations/configurations)を記述、管理、およびコンパイルできる Azure 構成管理サービスです。 また、サービスでは、[DSC リソース](/powershell/scripting/dsc/resources/resources)のインポートと、ターゲット ノードへの構成の割り当てをすべて、クラウドで実行できます。 Azure portal で **[構成管理]** の **[状態の構成 (DSC)]** を選択すると、Azure Automation State Configuration にアクセスできます。

Azure Automation State Configuration を使用して、さまざまなマシンを管理できます。

- Azure の仮想マシン
- Azure Virtual Machines (クラシック)
- オンプレミスの物理または仮想 Windows マシン、または Azure 以外のクラウド (AWS EC2 インスタンスなど)
- オンプレミス、Azure、または Azure 以外のクラウド内の物理/仮想 Linux マシン

クラウドからマシン構成を管理する準備ができていない場合は、Azure Automation State Configuration をレポート専用のエンドポイントとして使用できます。 この機能により、DSC 経由で構成を設定 (プッシュ) し、Azure Automation でレポートの詳細を表示できます。

> [!NOTE]
> インストールされている Azure VM Desired State Configuration 拡張機能のバージョンが 2.70 を超えている場合は、Azure Automation State Configuration での Azure VM の管理が追加料金なしで含まれています。 詳細については、[**Automation 価格に関するページ**](https://azure.microsoft.com/pricing/details/automation/)を参照してください。

## <a name="why-use-azure-automation-state-configuration"></a>Azure Automation State Configuration を使用する理由

Azure Automation State Configuration には、Azure の外部で DSC を使用する際にいくつかの利点があります。 このサービスを使用すると、セキュリティで保護された中央の場所から、数千台のマシンにまたがるスケーラビリティを迅速かつ容易に実現できます。 マシンの有効化、それらへの宣言型構成の割り当て、さらに指定した望ましい状態への各マシンの準拠を示すレポートの表示を容易に行うことができます。

Azure Automation State Configuration サービスは、Azure Automation Runbook が PowerShell スクリプトに対応する DSC に対応します。 つまり、Azure Automation を使用して、PowerShell スクリプトを管理するのと同様に DSC 構成も管理できます。

### <a name="built-in-pull-server"></a>組み込みのプル サーバー

Azure Automation State Configuration では、[Windows 機能 DSC サービス](/powershell/scripting/dsc/pull-server/pullserver)に似た DSC プル サーバーが提供されます。 ターゲット ノードでは、自動的に構成を受信し、目的の状態に適合させ、コンプライアンスについて報告することができます。 Azure Automation の組み込みのプル サーバーでは、独自のプル サーバーを設定して管理する必要はありません。 Azure Automation は、クラウドまたはオンプレミスの仮想または物理的な Windows コンピューターや Linux コンピューターを対象とすることができます。

### <a name="management-of-all-your-dsc-artifacts"></a>すべての DSC アーティファクトの管理

Azure Automation State Configuration では、PowerShell スクリプト用に提供されるものと同じ管理レイヤーが、[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) に提供されます。 Azure portal から、または PowerShell から、すべての DSC 構成、リソース、およびターゲット ノードを管理できます。

![Azure Automation ページのスクリーンショット](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Azure Monitor ログへのレポート データのインポート

Azure Automation State Configuration で管理されているノードは、組み込みのプル サーバーに詳細なレポート ステータス データを送信します。 Azure Automation State Configuration を構成して、このデータを Log Analytics ワークスペースに送信できます。 「[Azure Monitor ログへの Azure Automation State Configuration レポート データの転送](automation-dsc-diagnostics.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure Automation State Configuration を使用する場合は、このセクションの要件を考慮してください。

### <a name="operating-system-requirements"></a>オペレーティング システムの要件

Windows を実行しているノードの場合は、次のバージョンがサポートされます。

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>[Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) スタンドアロン製品 SKU には、DSC の実装は含まれていません。 したがって、PowerShell DSC または Azure Automation State Configuration で管理することはできません。

Linux が実行されているノードの場合、DSC Linux 拡張機能では、[PowerShell DSC に関するドキュメント](/powershell/scripting/dsc/getting-started/lnxgettingstarted)に示されている Linux ディストリビューションがすべてサポートされます。

### <a name="dsc-requirements"></a>DSC 要件

Azure で実行されているすべての Windows ノードでは、マシンが有効になるときに [WMF 5.1](/powershell/scripting/wmf/setup/install-configure) がインストールされます。 Windows Server 2012 および Windows 7 を実行しているノードでは、[WinRM が有効になります](/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency)。

Azure で実行されているすべての Linux ノードで、マシンが有効になるときに [Linux 用の PowerShell DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux) がインストールされます。

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>プライベート ネットワークの構成

プライベート ネットワーク上のノードに必要なポート、URL、およびその他のネットワークの詳細については、[Azure Automation のネットワーク構成](automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration)を確認してください。

#### <a name="proxy-support"></a>プロキシのサポート

DSC エージェントのプロキシは、Windows バージョン 1809 以降でサポートされます。 このオプションを有効にするには、ノードの登録に使用される[メタ構成スクリプト](automation-dsc-onboarding.md#generate-dsc-metaconfigurations)で `ProxyURL` および `ProxyCredential` プロパティの値を設定します。

>[!NOTE]
>Azure Automation State Configuration では、以前のバージョンの Windows に対する DSC プロキシ サポートは提供されません。

Linux ノードでは、DSC エージェントによってプロキシがサポートされ、`http_proxy` 変数を使用して URL が特定されます。 プロキシのサポートの詳細については、「[DSC メタ構成を生成する](automation-dsc-onboarding.md#generate-dsc-metaconfigurations)」を参照してください。

#### <a name="dns-records-per-region"></a>リージョンあたりの DNS レコード数

例外を定義するときは、「[リージョンあたりの DNS レコード数](how-to/automation-region-dns-records.md)」セクションの表に挙げられているアドレスを使用することをお勧めします。

## <a name="next-steps"></a>次のステップ

- 使用を開始するには、「[Azure Automation State Configuration の使用を開始する](automation-dsc-getting-started.md)」をご覧ください。
- ノードを有効にする方法については、[Azure Automation State Configuration を有効にする](automation-dsc-onboarding.md)方法に関するページを参照してください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てる方法の詳細については、「[Azure Automation State Configuration で DSC 構成をコンパイルする](automation-dsc-compile.md)」を参照してください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Chocolatey を使用して継続的配置を設定する](automation-dsc-cd-chocolatey.md)」を参照してください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation)」をご覧ください。
