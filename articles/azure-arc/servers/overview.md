---
title: Azure Arc 対応サーバーの概要
description: Azure Arc 対応サーバーを使用して、Azure の外部でホストされているサーバーを Azure リソースと同じように管理する方法について説明します。
keywords: azure automation, DSC, powershell, 望ましい状態の構成, 更新管理, 変更追跡, インベントリ, Runbook, Python, グラフィカル, ハイブリッド
ms.date: 02/18/2021
ms.topic: overview
ms.openlocfilehash: 863cab073018c5a592cba6e94451a9af038ebd52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023335"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Azure Arc 対応サーバーとは

Azure Arc 対応サーバーを使用すると、Azure の "*外部*" で、企業ネットワーク上で、または他のクラウド プロバイダー上でホストされている Windows または Linux の物理サーバーと仮想マシンを管理できます。 この管理エクスペリエンスは、ネイティブ Azure 仮想マシンの管理方法と一致するように設計されています。 ハイブリッド マシンは、Azure に接続されると接続済みマシンになり、Azure 内のリソースとして扱われます。 接続された各マシンは、リソース ID を持ち、リソース グループに含まれ、Azure Policy やタグの適用などの標準的な Azure のコンストラクトを利用できます。 顧客のオンプレミスのインフラストラクチャを管理するサービス プロバイダーは、[Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) と Azure Arc を使用して、ネイティブの Azure リソースで現在行っているのと同じように、複数のお客様の環境で各自のハイブリッド マシンを管理できます。

Azure の外部でホストされているハイブリッド マシンでこのエクスペリエンスを実現するには、Azure への接続を予定している各マシンに Azure Connected Machine エージェントがインストールされている必要があります。 このエージェントは他の機能をまったく持たず、Azure [Log Analytics エージェント](../../azure-monitor/agents/log-analytics-agent.md)に代わるものでもありません。 マシン上で実行されている OS とワークロードをプロアクティブに監視したい場合、それを Automation Runbook やソリューション (Update Management など) を使用して管理したい場合、または他の Azure サービス ([Azure Security Center](../../security-center/security-center-introduction.md) など) を使用したい場合は、Windows 用および Linux 用の Log Analytics エージェントが必要となります。

## <a name="supported-scenarios"></a>サポートされるシナリオ

マシンを Azure Arc 対応サーバーに接続すると、次の構成管理と監視のタスクを実行できるようになります。

- Azure 仮想マシンのポリシーの割り当てと同じエクスペリエンスを使用して [Azure Policy のゲスト構成](../../governance/policy/concepts/guest-configuration.md)を割り当てます。 今日、ほとんどのゲスト構成ポリシーでは、構成は適用されず、マシン内の設定の監査のみが行われます。 Arc 対応サーバーで Azure Policy ゲスト構成ポリシーを使用するコストについては、Azure Policy の[価格ガイド](https://azure.microsoft.com/pricing/details/azure-policy/)を参照してください。

- [Azure Defender for servers](../../security-center/defender-for-servers-introduction.md) が有効になっているサーバーに対し、Azure Automation の[変更履歴とインベントリ](../../automation/change-tracking/overview.md)および [Azure Security Center のファイルの整合性の監視](../../security-center/security-center-file-integrity-monitoring.md)を使用して、監視対象サーバー上にインストールされているソフトウェア、Microsoft サービス、Windows レジストリとファイル、および Linux デーモンの構成の変更について、レポートします。

- [Azure Monitor for VMs](../../azure-monitor/vm/vminsights-overview.md) を使用して、接続されているマシンのゲスト オペレーティング システムのパフォーマンスを監視します。また、アプリケーション コンポーネントを検出してそのプロセスや、アプリケーションが通信する他のリソースとの依存関係を監視します。

- Azure 以外の Windows または Linux マシンでサポートされている [Azure VM 拡張機能](manage-vm-extensions.md)を使用して、Azure Automation [State Configuration](../../automation/automation-dsc-overview.md) や Azure Monitor Log Analytics ワークスペースなどの他の Azure サービスによるデプロイを簡略化します。 これには、カスタム スクリプト拡張機能を使用した、デプロイ後の構成またはソフトウェアのインストールの実行が含まれます。

- Azure Automation で [Update Management](../../automation/update-management/overview.md) を使用して、Windows と Linux サーバーのオペレーティング システムの更新プログラムを管理します

    > [!NOTE]
    > 現時点では、Arc 対応サーバーから Update Management を直接有効にすることはサポートされていません。 要件とサーバーに対する有効化の方法を理解するには、「[Automation アカウントから Update Management を有効にする](../../automation/update-management/enable-from-automation-account.md)」を参照してください。

- 脅威検出に Azure 以外のサーバーを含め、[Azure Security Center](../../security-center/security-center-introduction.md) を使用して、潜在的なセキュリティの脅威を積極的に監視します。

ハイブリッド マシンから Log Analytics ワークスペースに収集および格納されるログ データには、リソース ID など、マシンに固有のプロパティが含まれるようになりました。 これを使用して、[リソース コンテキスト](../../azure-monitor/logs/design-logs-deployment.md#access-mode) ログ アクセスをサポートできます。

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>サポートされているリージョン

Azure Arc 対応サーバーでサポートされているリージョンの確定的な一覧については、[リージョン別の Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)に関するページを参照してください。

ほとんどの場合、インストール スクリプトを作成するときに選択する場所は、マシンの場所に最も近い Azure リージョンにする必要があります。 保存データは、指定したリージョンを含む Azure 地域内に保存されます。データ所在地の要件がある場合は、これがリージョンの選択に影響する可能性もあります。 マシンが接続されている Azure リージョンが障害の影響を受ける場合、接続されたマシンは影響を受けませんが、Azure を使用した管理操作を完了できない可能性があります。 リージョンで障害が発生したとき、地理的に冗長なサービスをサポートする複数の場所がある場合、各場所のマシンを別の Azure リージョンに接続することをお勧めします。

接続されているマシンに関する次のメタデータ情報が収集され、Azure Arc マシン リソースが構成されているリージョンに保存されます。

- オペレーティング システムの名前とバージョン
- コンピューター名
- コンピューターの完全修飾ドメイン名 (FQDN)
- Connected Machine エージェントのバージョン

たとえば、マシンが米国東部リージョンの Azure Arc に登録されている場合、このデータは米国リージョンに保存されます。

### <a name="supported-environments"></a>サポートされている環境

Arc 対応サーバーでは、Azure の "*外部*" でホストされている物理サーバーと仮想マシンの管理がサポートされます。 VM をホストするハイブリッド クラウド環境のうち、サポートされている環境の詳細については、[Connected Machine エージェントの前提条件](agent-overview.md#supported-environments)に関するページを参照してください。

> [!NOTE]
> Arc 対応サーバーは、Azure で実行される仮想マシンの管理を可能にするようには設計されておらず、サポートされていません。

### <a name="agent-status"></a>エージェントの状態

Connected Machine エージェントは、5 分間隔でハートビート メッセージをサービスに送信します。 これらのハートビート メッセージをサービスがマシンから受信できなくなると、そのマシンはオフラインと見なされ、15 分から 30 分以内にポータルでの状態が自動的に **[切断]** に変更されます。 その後、Connected Machine エージェントからハートビート メッセージを受信すると、状態は自動的に **[接続]** に変更されます。

## <a name="next-steps"></a>次のステップ

複数のハイブリッド マシン間で Arc 対応サーバーを評価または有効化する前に、[Connected Machine エージェントの概要](agent-overview.md)を確認し、要件、エージェントに関する技術的な詳細、およびデプロイ方法を把握してください。