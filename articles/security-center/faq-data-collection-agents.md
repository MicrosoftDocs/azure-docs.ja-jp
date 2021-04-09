---
title: Azure Security Center FAQ - データ収集とエージェント
description: 脅威の防御、検出、対応を可能にする製品である Azure Security Center でのデータ収集、エージェント、およびワークスペースに関してよく寄せられる質問
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2020
ms.author: memildin
ms.openlocfilehash: 4ef01045d1ca16d0101cdd9ccfcd118231cd28de
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102456115"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>FAQ - データ収集、エージェント、およびワークスペースに関する質問

Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシン (VM)、仮想マシン スケール セット、IaaS コンテナー、非 Azure コンピューター (オンプレミスのマシンを含む) からデータを収集します。 データは、Log Analytics エージェントを使用して収集されます。このエージェントは、セキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータをワークスペースにコピーします。


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Security Center で作成されたワークスペース上の Azure Monitor ログには課金されますか?

いいえ。 ノードごとの Azure Monitor ログの課金が構成されている場合でも、Security Center で作成されるワークスペースに Azure Monitor ログの料金はかかりません。 Security Center の課金は、常に Security Center セキュリティ ポリシーとワークスペースにインストールされているソリューションに基づいています。

- **Azure Defender オフ** – Security Center によって、既定のワークスペースで "SecurityCenterFree" ソリューションが有効化されます。 Azure Defender がオフになっている場合は課金されません。

- **Azure Defender オン** – Security Center によって、既定のワークスペースで "Security" ソリューションが有効化されます。

選択した通貨でのお住まいのリージョンに応じた価格の詳細については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」を参照してください。

> [!NOTE]
> Security Center で作成されたワークスペースのログ分析の価格レベルは、Security Center の課金には影響しません。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-is-the-log-analytics-agent"></a>Log Analytics エージェントとは

セキュリティの脆弱性と脅威を監視するために、Azure Security Center は [Log Analytics エージェント](../azure-monitor/agents/log-analytics-agent.md)に依存しています。これは、Azure Monitor サービスで使用されるのと同じエージェントです。 

このエージェントは、Microsoft Monitoring Agent ("MMA") と呼ばれることもあります。 

エージェントは、接続されているマシンからさまざまなセキュリティ関連の構成の詳細とイベント ログを収集し、さらに分析するためにデータを Log Analytics ワークスペースにコピーします。 このようなデータの例として、オペレーティング システムの種類とバージョン、オペレーティング システム ログ (Windows イベント ログ)、実行中のプロセス、マシン名、IP アドレス、ログイン ユーザーなどがあります。

次のページの説明に従って、このエージェントでサポートされているオペレーティング システムのいずれかがマシンで実行されていることを確認してください。

* [Windows 用の Log Analytics エージェントでサポートされているオペレーティング システム ](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

* [Linux 用の Log Analytics エージェントでサポートされているオペレーティング システム](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

詳細については、[Log Analytics エージェントによって収集されたデータ](security-center-enable-data-collection.md)に関する記事をご覧ください。




## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Log Analytics エージェント インストールの自動プロビジョニングでは、何をもって VM を適格と見なしますか?

Windows または Linux IaaS VM は、次の条件で適格とします。

- 現在、Log Analytics エージェント拡張機能が VM にインストールされていない。
- VM が実行状態である。
- Windows または Linux [Azure 仮想マシン エージェント](../virtual-machines/extensions/agent-windows.md)がインストールされている。
- VM は、Web アプリケーション ファイアウォールや次世代ファイアウォールなどのアプライアンスとしては使用されません。


## <a name="where-is-the-default-log-analytics-workspace-created"></a>既定の Log Analytics ワークスペースはどこに作成されますか?

既定のワークスペースの場所は、Azure リージョンによって異なります。

- 米国とブラジルの VM の場合、ワークスペースの場所は米国です
- カナダの VM の場合、ワークスペースの場所はカナダです
- ヨーロッパの VM の場合、ワークスペースの場所はヨーロッパです
- 英国の VM の場合、ワークスペースの場所は英国です
- 東アジアと東南アジアの VM の場合、ワークスペースの場所はアジアです
- 韓国の VM の場合、ワークスペースの場所は韓国です
- インドの VM の場合、ワークスペースの場所はインドです
- 日本の VM の場合、ワークスペースの場所は日本です
- 中国の VM の場合、ワークスペースの場所は中国です
- オーストラリアの VM の場合、ワークスペースの場所はオーストラリアです


## <a name="what-security-events-are-collected-by-the-log-analytics-agent"></a>Log Analytics エージェントではどのようなセキュリティ イベントが収集されますか?

エージェントによって収集されるセキュリティ イベントの完全な一覧については、["共通" と "最小" のセキュリティ イベント設定で保存されるイベントの種類](security-center-enable-data-collection.md#what-event-types-are-stored-for-common-and-minimal)に関するページを参照してください。

> [!IMPORTANT]
> Azure Firewall などの一部のサービスでは、ログ記録を有効にし、頻度の高いリソースをログ記録するよう選択した場合 (たとえば、ログを *verbose* に設定した場合)、Log Analytics ワークスペースのストレージ ニーズに大きな影響を与える可能性があります。 


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Security Center で作成された既定のワークスペースは削除できますか?

**既定のワークスペースを削除することは推奨されません。** Security Center は、既定のワークスペースを使用して VM のセキュリティ データを格納します。 ワークスペースを削除すると、Security Center はこのデータを収集できず、一部のセキュリティの推奨とアラートと使用できなくなります。

復旧するには、削除したワークスペースに接続されている VM 上の Log Analytics エージェントを削除します。 Security Center によってエージェントが再インストールされ、新しい既定のワークスペースが作成されます。

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>既存の Log Analytics ワークスペースを使用するにはどうすればよいですか?

Security Center によって収集されたデータを保存する既存の Log Analytics ワークスペースを選択できます。 既存の Log Analytics ワークスペースを使用する場合、次の要件があります。

- 選択した Azure サブスクリプションにワークスペースを関連付ける必要があります。
- ワークスペースにアクセスするには、少なくとも読み取りアクセス許可が必要です。

既存の Log Analytics ワークスペースを選択するには、次の手順に従います。

1. Security Center のメニューから、 **[価格と設定]** を選択します。
1. 関連するサブスクリプションを選択します。
1. **[自動プロビジョニング]** ページを開きます。
1. Log Analytics エージェントの場合は、 **[構成の編集]** を選択します。 

    :::image type="content" source="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png" alt-text="自動デプロイの使用時に使用する Log Analytics エージェントの構成" lightbox="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png":::

1. **[Connect Azure VMs to a different workspace]\(Azure VM の別のワークスペースへの接続\)** を選択し、既存のワークスペースを選択します。

    :::image type="content" source="./media/security-center-enable-data-collection/choose-workspace.png" alt-text="Log Analytics エージェントに対してレポート先の既定のワークスペース以外のワークスペースを選択する" lightbox="./media/security-center-enable-data-collection/choose-workspace.png":::

    > [!TIP]
    > このリストには、アクセス権を持っており、Azure サブスクリプション内にあるワークスペースのみが含められます。

1. **[保存]** を選択します。 監視対象の VM を再構成するかどうかをたずねられます。

    - 新しいワークスペース設定を **新しい VM にのみ適用** する場合は、 **[いいえ]** を選択します。 新しいワークスペース設定は、エージェントの新しいインストール (Log Analytics エージェントがインストールされていない、新たに検出された VM) にのみ適用されます。
    - 新しいワークスペース設定を **すべての VM に適用** する場合は、 **[はい]** を選択します。 この場合、Security Center によって作成されたワークスペースに接続されているすべての VM が、新しいターゲット ワークスペースに再接続されます。

    > [!NOTE]
    > **[はい]** を選択した場合、すべての VM が新しいターゲット ワークスペースに再接続されるまで、Security Center によって作成されたワークスペースを削除しないでください。 ワークスペースの削除が早すぎると、この操作は失敗します。


## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Log Analytics エージェントが拡張機能として VM に既にインストールされている場合はどうなりますか?<a name="mmaextensioninstalled"></a>

Monitoring Agent が拡張機能としてインストールされている場合、拡張機能の構成では 1 つのワークスペースにのみレポートできます。 Security Center は、ユーザー ワークスペースへの既存の接続をオーバーライドしません。 Security Center では、"Security" または "SecurityCenterFree" ソリューションがインストールされている場合は、既に接続されているワークスペースに VM からのセキュリティ データが保存されます。 Security Center では、このプロセスで拡張機能のバージョンを最新バージョンにアップグレードする可能性があります。

詳細については、「[既にインストールされているエージェントが存在する場合の自動プロビジョニング](security-center-enable-data-collection.md#preexisting)」をご覧ください。



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>マシンに Log Analytics エージェントが直接インストールされているものの、拡張機能 (Direct Agent) としてインストールされていない場合はどうなりますか?<a name="directagentinstalled"></a>

Log Analytics エージェントが VM に (Azure 拡張機能としてではなく) 直接インストールされている場合、Security Center によって Log Analytics エージェント拡張機能がインストールされ、Log Analytics エージェントが最新バージョンにアップグレードされる可能性があります。

インストールされているエージェントでは、既に構成されているワークスペースに引き続きレポートし、さらに Security Center で構成されたワークスペースにもレポートします (Windows マシンでは、マルチ ホームがサポートされています)。

構成済みのワークスペースが (Security Center の既定のワークスペースではなく) ユーザー ワークスペースの場合、そのワークスペースにレポートする VM およびコンピューターからのイベントの処理を Security Center で始めるには、"Security" または "SecurityCenterFree" ソリューションをインストールする必要があります。

Linux マシンでは、エージェントのマルチ ホームはまだサポートされていません。そのため、既存のエージェントのインストールが検出されても、自動プロビジョニングは行われず、マシンの構成は変更されません。

2019 年 3 月 17 日より前に Security Center にオンボードされたサブスクリプションの既存のマシンでは、既存のエージェントが検出されると、Log Analytics エージェント拡張機能はインストールされず、マシンに影響はありません。 これらのマシンについては、マシンでのエージェントのインストールに関する問題を解決するために、"マシンの監視エージェント正常性の問題を解決する" の推奨事項を参照してください。

詳細については、次のセクションの「[System Center Operations Manager または OMS のダイレクト エージェントが既に VM にインストールされている場合、どうなりますか?](#scomomsinstalled)」をご覧ください

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>VM に System Center Operations Manager エージェントが既にインストールされている場合はどうなりますか?<a name="scomomsinstalled"></a>

Security Center は、Log Analytics エージェント拡張機能を既存の System Center Operations Manager エージェントと並行してインストールします。 既存のエージェントは、引き続き通常どおり System Center Operations Manager サーバーに報告します。 Operations Manager エージェントと Log Analytics エージェントは、このプロセス中に最新バージョンに更新される、共通のランタイム ライブラリを共有することに注意してください。 注 - Operations Manager エージェントのバージョン 2012 がインストールされている場合は、自動プロビジョニングを有効にしないでください (Operations Manager サーバーもバージョン 2012 である場合、管理容易性機能が失われる可能性があり)。


## <a name="what-is-the-impact-of-removing-these-extensions"></a>これらの拡張機能を削除するとどのような影響がありますか?

Microsoft Monitoring Extension を削除すると、Security Center は VM からセキュリティ データを収集できなくなります。また、一部のセキュリティの推奨とアラートを使用できなくなります。 Security Center は、VM に拡張機能が存在せず、拡張機能を再インストールすることが 24 時間以内に判断されます。


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>自動的なエージェントのインストールとワークスペースの作成をオプトアウトするにはどうすればよいですか?

セキュリティ ポリシーでサブスクリプションの自動プロビジョニングを無効にすることはできますが、これは推奨されません。 自動プロビジョニングを無効にすると、Security Center のレコメンデーションとアラートが制限されます。 自動プロビジョニングを無効にするには、次の手順に従います。

1. Security Center のメニューから、 **[価格と設定]** を選択します。
1. 関連するサブスクリプションを選択します。
1. サブスクリプションで Azure Defender が有効になっている場合は、**Azure Defender プラン** を開き、 **[Azure Defender off]\(Azure Defender をオフ\)** を選択します。

    :::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Azure Defender を有効または無効にする":::

1. **[自動プロビジョニング]** ページから、ペンを選択して **[セキュリティ ポリシー - データ収集]** ページで自動プロビジョニングを無効にします。

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Log Analytics エージェントの自動デプロイを有効にする":::

1. **[保存]** を選択します。


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>自動的なエージェントのインストールとワークスペースの作成をオプトアウトすべきでしょうか?

> [!NOTE]
> 自動プロビジョニングをオプトアウトする場合は、[オプトアウトの影響](#what-are-the-implications-of-opting-out-of-automatic-provisioning)および[オプトアウトする場合に推奨される手順](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)に関するセクションを必ず確認してください。

次の条件に該当する場合、自動プロビジョニングのオプトアウトを検討します。

- Security Center による自動的なエージェントのインストールが、サブスクリプション全体に適用されている。 VM のサブセットには自動インストールを適用できません。 Log Analytics エージェントではインストールできない重要な VM がある場合は、自動プロビジョニングをオプトアウトする必要があります。
- Log Analytics エージェント拡張機能をインストールすると、エージェントのバージョンが更新されます。 これは、ダイレクト エージェントと System Center Operations Manager エージェントに該当します (後者の場合、Operations Manager と Log Analytics で共通のランタイム ライブラリが共有され、プロセスで更新されます)。 インストールされている Operations Manager エージェントがバージョン 2012 であり、これがアップグレードされている場合、Operations Manager サーバーもバージョン 2012 になっていると、管理容易性の機能が失われる恐れがあります。 インストールされている Operations Manager エージェントがバージョン 2012 の場合、自動プロビジョニングのオプトアウトを検討してください。
- サブスクリプション (一元化されたワークスペース) の外部にカスタム ワークスペースを保持している場合、自動プロビジョニングをオプトアウトする必要があります。 手動で Log Analytics エージェント拡張機能をインストールし、お使いになっているワークスペースに接続でき、Security Center が接続をオーバーライドすることはありません。
- サブスクリプションごとに複数のワークスペースが作成されることを回避したいと考えていて、サブスクリプション内に独自のカスタム ワークスペースがある場合、次の 2 つの選択肢があります。

   1. 自動プロビジョニングをオプトアウトできます。 移行後に、「[既存の Log Analytics ワークスペースを使用するにはどうすればよいですか?](#how-can-i-use-my-existing-log-analytics-workspace)」の説明に従って、既定のワークスペース設定を設定します。

   1. また、移行の完了を許可し、VM 上で Log Analytics エージェントをインストールし、作成されたワークスペースに VM を接続することもできます。 その後、既にインストールされているエージェントを再構成し、既定のワークスペース設定を設定して、独自のカスタム ワークスペースを選択します。 詳細については、「[既存の Log Analytics ワークスペースを使用するにはどうすればよいですか?](#how-can-i-use-my-existing-log-analytics-workspace)」をご覧ください。


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>自動プロビジョニングをオプトアウトすると、どのような影響がありますか?

移行が完了すると、Security Center は VM からセキュリティ データを収集できなくなります。また、一部のセキュリティの推奨とアラートを使用できなくなります。 オプトアウトする場合は、Log Analytics エージェントを手動でインストールします。 [オプトアウトする場合に推奨される手順](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)に関するセクションをご覧ください。


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>自動プロビジョニングをオプトアウトする場合、どのような手順が推奨されますか?

Log Analytics エージェントの拡張機能を手動でインストールし、Security Center がお使いになっている VM からセキュリティ データを収集して、推奨や通知を提示できるようにします。 インストールのガイダンスとして、[Windows VM 用のエージェントのインストール](../virtual-machines/extensions/oms-windows.md)または [Linux VM 用のエージェントのインストール](../virtual-machines/extensions/oms-linux.md)に関するページを参照してください。

エージェントをいずれかの既存のカスタム ワークスペースまたは Security Center が作成したワークスペースに接続できます。 カスタム ワークスペースの "Security" または "SecurityCenterFree" ソリューションが有効になっていない場合は、ソリューションを適用する必要があります。 適用するには、 **[セキュリティ ポリシー – 価格レベル]** ページを利用して、カスタム ワークスペースまたはサブスクリプションを選択します。

:::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Azure Defender を有効または無効にする":::

Security Center は、選択された価格レベルに基づいて、ワークスペース上で適切なソリューションを有効にします。


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Security Center にインストールされている OMS 拡張機能を削除するにはどうすればよいですか?<a name="remove-oms"></a>

Log Analytics エージェントは手動で削除できます。 ただし、Security Center の推奨とアラートが制限されるため、削除は推奨されません。

> [!NOTE]
> データ収集が有効な場合、削除しても、Security Center でエージェントが再インストールされます。  エージェントを手動で削除する前に、データ収集を無効にする必要があります。 データ収集を無効にする手順については、「自動的なエージェントのインストールとワークスペースの作成を停止するにはどうすればよいですか?」を参照してください。

エージェントを手動で削除するには:

1.    ポータルで、 **[Log Analytics]** を開きます。

1.    [Log Analytics] ページで、ワークスペースを選択します。

1.    監視しない VM を選択し、 **[切断]** を選択します。

   ![エージェントを削除する][3]

> [!NOTE]
> Linux VM に拡張機能ではない OMS エージェントが既にある場合、拡張機能を削除するとエージェントも削除されるので、これを再インストールする必要があります。


## <a name="how-do-i-disable-data-collection"></a>データ収集を無効にするにはどうしたらよいですか。

セキュリティ アラートや、システムの更新プログラム、OS の脆弱性、Endpoint Protection に関する推奨事項を取得するために、自動プロビジョニングを使用することを強くお勧めします。 既定では、自動プロビジョニングは無効になっています。

有効にした後で無効にする必要がある場合は、次のようにします。

1. [Azure portal](https://portal.azure.com) から、**Security Center** を開いて、 **[Pricing and settings]\(価格と設定\)** を選択します。

1. 自動プロビジョニングを無効にするサブスクリプションを選択します。

1. **[自動プロビジョニング]** で、Log Analytics エージェントの切り替えをオフにします。


## <a name="how-do-i-enable-data-collection"></a>データ収集を有効にするにはどうしたらよいですか。

Azure サブスクリプションのデータ収集の有効化は、セキュリティ ポリシーで行うことができます。 データ収集を有効にするには、 [Azure portal にサインイン](https://portal.azure.com)して、 **[参照]** 、 **[セキュリティ センター]** 、 **[セキュリティ ポリシー]** の順に選びます。 自動プロビジョニングを有効にするサブスクリプションを選択します。 サブスクリプションを選ぶと、 **[セキュリティ ポリシー] - [データ収集]** が開きます。 **[自動プロビジョニング]** で **[オン]** を選びます。


## <a name="what-happens-when-data-collection-is-enabled"></a>データ収集を有効にするとどうなりますか。

自動プロビジョニングを有効にすると、Security Center は、サポートされているすべての Azure VM と新しく作成される VM に Log Analytics エージェントをプロビジョニングします。 自動プロビジョニングをお勧めしますが、エージェントを手動でインストールすることもできます。 [Log Analytics エージェントの拡張機能をインストールする方法を確認してください](../azure-monitor/vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)。 

エージェントで、プロセス作成イベント 4688 とイベント 4688 内の *CommandLine* フィールドが有効になります。 VM に作成された新しいプロセスは EventLog に記録され、Security Center の検出サービスによって監視されます。 新しいプロセスごとに記録される詳細については、[4688 の説明フィールド](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields)を参照してください。 また、エージェントは VM に作成された 4688 イベントも収集し、検索に保存します。

さらに、エージェントは、[適応型アプリケーション制御](security-center-adaptive-application.md)のデータ収集を有効にでき、Security Center は、ローカル AppLocker ポリシーを監査モードで構成して、すべてのアプリケーションを許可します。 このポリシーをきっかけとして AppLocker がイベントを生成するようになり、そのイベントが Security Center によって収集されて活用されます。 ただし、既に AppLocker ポリシーが構成されているマシンでは、このポリシーが構成されないので注意してください。 

[セキュリティ連絡先の情報](security-center-provide-security-contact-details.md)が指定されている場合、Security Center が VM で疑わしいアクティビティを検出すると、電子メールでユーザーに通知します。 Security Center のセキュリティ アラート ダッシュボードにもアラートが表示されます。


## <a name="will-security-center-work-using-an-oms-gateway"></a>Security Center は OMS ゲートウェイを使用して動作しますか。

はい。 Azure Security Center は、Log Analytics エージェントを使用して、Azure VM およびサーバーからのデータ収集に Azure Monitor を活用します。
データを収集するには、各 VM とサーバーが HTTPS を使用してインターネットに接続する必要があります。 インターネット接続には、直接接続、プロキシを使用した接続、[OMS ゲートウェイ](../azure-monitor/agents/gateway.md)を介した接続を使用できます。


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent はサーバーのパフォーマンスに影響しますか。

エージェントは、システム リソースのわずかな量しか消費しないため、パフォーマンスにほとんど影響しません。 パフォーマンスの影響と、エージェントおよび拡張機能の詳細については、[計画および運用ガイド](security-center-planning-and-operations-guide.md#data-collection-and-storage)を参照してください。




<!--Image references-->
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png