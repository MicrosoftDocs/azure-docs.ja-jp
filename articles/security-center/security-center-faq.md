---
title: Azure Security Center のよく寄せられる質問 (FAQ) | Microsoft Docs
description: この FAQ は、Azure セキュリティ センターについて寄せられる質問とその回答です。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: v-mohabe
ms.openlocfilehash: 300d96a792663a32a326d5d01f281b77ccf9c4bf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050510"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Azure セキュリティ センターのよく寄せられる質問 (FAQ)
この FAQ は、Azure Security Center について寄せられる質問とその回答です。Azure Security Center は、Microsoft Azure リソースのセキュリティの視覚化と制御の向上により、脅威を回避、検出、対応するのに役立つサービスです。

> [!NOTE]
> 2017 年 6 月上旬以降、Security Center では、Microsoft Monitoring Agent を使用してデータの収集と格納を行います。 詳細については、「[Azure Security Center のプラットフォームの移行](security-center-platform-migration.md)」を参照してください。 この記事の情報は、Microsoft Monitoring Agent に移行した後の Security Center の機能を示しています。
>
>

## <a name="general-questions"></a>一般的な質問
### <a name="what-is-azure-security-center"></a>Azure Security Center とは
Azure セキュリティ センターは、Azure リソースのセキュリティを高度に視覚化し、制御することで脅威を回避、検出し、それに対応することに役立ちます。 これにより、サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

### <a name="how-do-i-get-azure-security-center"></a>Azure セキュリティ センターはどうしたら取得できますか。
Azure Security Center は、Microsoft Azure サブスクリプションで有効化し、[Azure Portal](https://azure.microsoft.com/features/azure-portal/) からアクセスします ([ポータルにサインイン](https://portal.azure.com)して、 **[参照]** を選択し、 **[セキュリティ センター]** までスクロールします)。  

## <a name="billing"></a>課金
### <a name="how-does-billing-work-for-azure-security-center"></a>Azure セキュリティ センターの課金のしくみを教えてください。
Azure Security Center は 2 つのレベルで提供されます。

**Free レベル**では、Azure リソースのセキュリティ状態の表示、基本的なセキュリティ ポリシー、セキュリティに関する推奨事項、パートナーのセキュリティ製品やサービスとの統合が提供されます。

**Standard レベル**には、脅威インテリジェンス、行動分析、異常検出、セキュリティ インシデント、脅威評価レポートなどの高度な脅威検出機能が追加されます。 Standard レベルの無料試用版を開始できます。 アップグレードを行うには、 [セキュリティ ポリシー](https://docs.microsoft.com/azure/security-center/security-center-pricing)で価格レベルを選択してください。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

### <a name="how-can-i-track-who-in-my-organization-performed-pricing-tier-changes-in-azure-security-center"></a>Azure Security Center で価格レベルの変更を実行した組織内のユーザーを追跡するにはどうすればよいですか?
Azure サブスクリプションでは、価格レベルを変更するアクセス許可を持つ管理者が複数いる可能性があるため、ユーザーは価格レベルの変更を実行したユーザーを確認できます。 そのためには、Azure アクティビティ ログを使います。 詳しくは、[こちら](https://techcommunity.microsoft.com/t5/Security-Identity/Tracking-Changes-in-the-Pricing-Tier-for-Azure-Security-Center/td-p/390832)をご覧ください

## <a name="permissions"></a>アクセス許可
Azure Security Center では、[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/role-assignments-portal.md) が使用されています。RBAC が提供する[組み込みのロール](../role-based-access-control/built-in-roles.md)は、Azure でユーザー、グループ、サービスに割り当てることができます。

Security Center は、リソースの構成を評価して、セキュリティの問題と脆弱性を特定します。 Security Center では、リソースが属するサブスクリプションまたはリソース グループの所有者、共同作業者、閲覧者のいずれかのロールが割り当てられているときにリソースに関連した情報のみが表示されます。

Security Center のロールと許可されているアクションの詳細については、「[Permissions in Azure Security Center (Azure Security Center のアクセス許可)](security-center-permissions.md)」を参照してください。

## <a name="data-collection-agents-and-workspaces"></a>データの収集、エージェント、およびワークスペース
Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシン (VM)、仮想マシン スケール セット (VMSS)、IaaS コンテナー、非 Azure (オンプレミスを含む) コンピューターからデータを収集します。 データは、Microsoft Monitoring Agent を使用して収集されます。Microsoft Monitoring Agent は、セキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータをワークスペースにコピーします。

### <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Security Center で作成されたワークスペース上の Azure Monitor ログには課金されますか?
いいえ。 ノードごとの Azure Monitor ログの課金が構成されている場合でも、Security Center で作成されるワークスペースに Azure Monitor ログの料金はかかりません。 Security Center の課金は、常に Security Center セキュリティ ポリシーとワークスペースにインストールされているソリューションに基づいています。

- **Free レベル** - Security Center によって既定のワークスペースに 'SecurityCenterFree' ソリューションが有効化されます。 Free レベルの料金はかかりません。
- **Standard レベル** - Security Center によって既定のワークスペースに 'Security' ソリューションが有効化されます。

価格の詳細については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」を参照してください。 この価格ページでは、2017 年 6 月からのセキュリティ データ ストレージと日割り課金の変更が反映されています。

> [!NOTE]
> Security Center で作成されたワークスペースのログ分析の価格レベルは、Security Center の課金には影響しません。
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Microsoft Monitoring Agent インストールの自動プロビジョニングでは、何をもって VM を適格と見なしますか?
Windows または Linux IaaS VM は、次の条件で適格とします。

- Microsoft Monitoring Agent 拡張機能が現在、VM 上にインストールされていない。
- VM が実行状態である。
- Windows または Linux [Azure 仮想マシン エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)がインストールされている。
- VM は、Web アプリケーション ファイアウォールや次世代ファイアウォールなどのアプライアンスとしては使用されません。

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Security Center で作成された既定のワークスペースは削除できますか?
**既定のワークスペースを削除することは推奨されません。** Security Center は、既定のワークスペースを使用して VM のセキュリティ データを格納します。  ワークスペースを削除すると、Security Center はこのデータを収集できず、一部のセキュリティの推奨とアラートと使用できなくなります。

復旧するには、削除したワークスペースに接続されている VM 上の Microsoft Monitoring Agent を削除します。 Security Center によってエージェントが再インストールされ、新しい既定のワークスペースが作成されます。

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>既存の Log Analytics ワークスペースを使用するにはどうすればよいですか?

Security Center によって収集されたデータを保存する既存の Log Analytics ワークスペースを選択できます。 既存の Log Analytics ワークスペースを使用する場合、次の要件があります。

- 選択した Azure サブスクリプションにワークスペースを関連付ける必要があります。
- ワークスペースにアクセスするには、少なくとも読み取りアクセス許可が必要です。

既存の Log Analytics ワークスペースを選択するには、次の手順に従います。

1. **[セキュリティ ポリシー - データ収集]** で、 **[Use another workspace]\(別のワークスペースを使用する\)** を選択します。

   ![別のワークスペースを使用する][5]

2. プルダウン メニューから、収集したデータを保存するワークスペースを選択します。

   > [!NOTE]
   > プルダウン メニューには、Azure サブスクリプション内の、アクセスできるワークスペースだけが表示されます。
   >
   >

3. **[保存]** を選択します。
4. **[保存]** をクリックすると、監視対象の VM を再構成するかどうかをたずねられます。

   - 新しいワークスペース設定を**新しい VM にのみ適用**する場合は、 **[いいえ]** を選択します。 この場合、新しいワークスペース設定は、エージェントの新しいインストール (Microsoft Monitoring Agent がインストールされていない、新たに検出された VM) にのみ適用されます。
   - 新しいワークスペース設定を**すべての VM に適用**する場合は、 **[はい]** を選択します。 この場合、Security Center によって作成されたワークスペースに接続されているすべての VM が、新しいターゲット ワークスペースに再接続されます。

   > [!NOTE]
   > [はい] を選択した場合、すべての VM が新しいターゲット ワークスペースに再接続されるまで、Security Center によって作成されたワークスペースを削除しないでください。 ワークスペースの削除が早すぎると、この操作は失敗します。
   >
   >

   - 操作を取り消すには、 **[キャンセル]** をクリックします。

### Microsoft Monitoring Agent が拡張機能として VM に既にインストールされている場合はどうなりますか?<a name="mmaextensioninstalled"></a>
Monitoring Agent が拡張機能としてインストールされている場合、拡張機能の構成では 1 つのワークスペースにのみレポートできます。 Security Center は、ユーザー ワークスペースへの既存の接続をオーバーライドしません。 Security Center では、"Security" または "SecurityCenterFree" ソリューションがインストールされている場合は、既に接続されているワークスペースに VM からのセキュリティ データが保存されます。 Security Center では、このプロセスで拡張機能のバージョンを最新バージョンにアップグレードする可能性があります。

詳細については、「[既にインストールされているエージェントが存在する場合の自動プロビジョニング](security-center-enable-data-collection.md#preexisting)」をご覧ください。


### マシンに Microsoft Monitoring Agent が拡張機能 (直接エージェント) としてではなく、直接インストールされている場合はどうなりますか?<a name="directagentinstalled"></a>
Microsoft Monitoring Agent が VM に (Azure 拡張機能としてではなく) 直接インストールされている場合、Security Center によって Microsoft Monitoring Agent 拡張機能がインストールされ、Microsoft Monitoring Agent が最新バージョンにアップグレードされる可能性があります。
インストールされているエージェントでは、既に構成されているワークスペースに引き続きレポートし、さらに Security Center で構成されたワークスペースにもレポートします (Windows マシンでは、マルチ ホームがサポートされています)。
構成済みのワークスペースが (Security Center の既定のワークスペースではなく) ユーザー ワークスペースの場合、そのワークスペースにレポートする VM およびコンピューターからのイベントの処理を Security Center で始めるには、"Security/SecurityCenterFree" ソリューションをインストールする必要があります。

Linux マシンでは、エージェントのマルチ ホームはまだサポートされていません。そのため、既存のエージェントのインストールが検出されても、自動プロビジョニングは行われず、マシンの構成は変更されません。

2019 年 3 月 17 日より前に Security Center にオンボードされたサブスクリプションの既存のマシンでは、既存のエージェントが検出されると、Microsoft Monitoring Agent 拡張機能はインストールされず、マシンに影響はありません。 これらのマシンについては、マシンでのエージェントのインストールに関する問題を解決するために、"マシンの監視エージェント正常性の問題を解決する" の推奨事項を参照してください。

 詳細については、次のセクションの「[SCOM または OMS のダイレクト エージェントが既に VM にインストールされている場合、どうなりますか?](#scomomsinstalled)」をご覧ください。

### VM に System Center Operations Manager (SCOM) エージェントが既にインストールされている場合はどうなりますか?<a name="scomomsinstalled"></a>
Security Center によって、Microsoft Monitoring Agent 拡張機能は、既存の System Center Operations Manager エージェントと並行してインストールされます。 既存の SCOM エージェントは、引き続き通常どおり System Center Operations Manager サーバーに報告します。 System Center Operations Manager エージェントと Microsoft Monitoring Agent は、このプロセス中に最新バージョンに更新される、共通のランタイム ライブラリを共有することに注意してください。 注 - System Center Operations Manager エージェントのバージョン 2012 がインストールされている場合は、自動プロビジョニングを有効にしないでください (System Center Operations Manager サーバーもバージョン 2012 である場合、管理容易性機能が失われる可能性があり)。

### <a name="what-is-the-impact-of-removing-these-extensions"></a>これらの拡張機能を削除するとどのような影響がありますか?
Microsoft Monitoring Extension を削除すると、Security Center は VM からセキュリティ データを収集できなくなります。また、一部のセキュリティの推奨とアラートを使用できなくなります。 Security Center は、VM に拡張機能が存在せず、拡張機能を再インストールすることが 24 時間以内に判断されます。

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>自動的なエージェントのインストールとワークスペースの作成を停止するにはどうすればよいですか?
セキュリティ ポリシーでサブスクリプションの自動プロビジョニングを無効にすることはできますが、これは推奨されません。 自動プロビジョニングを無効にすると、Security Center の推奨事項とアラートが制限されます。 自動プロビジョニングを無効にするには、次の手順に従います。

1. Standard レベルのサブスクリプションを構成する場合、そのサブスクリプションのセキュリティ ポリシーを開き、 **[Free]** レベルを選択します。

   ![価格レベル][1]

2. 次に、 **[セキュリティ ポリシー - データ収集]** ブレードで **[オフ]** を選択して自動プロビジョニングを無効にします。
   ![データ収集][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>自動的なエージェントのインストールとワークスペースの作成を停止するにはどうすればよいですか?

> [!NOTE]
> 自動プロビジョニングを停止する場合は、[停止の影響](#what-are-the-implications-of-opting-out-of-automatic-provisioning)および[停止する場合に推奨される手順](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)に関するセクションを必ず確認してください。
>
>

次の条件に該当する場合、自動プロビジョニングの停止を検討する可能性があります。

- Security Center による自動的なエージェントのインストールが、サブスクリプション全体に適用されている。  VM のサブセットには自動インストールを適用できません。 Microsoft Monitoring Agent ではインストールできない重要な VM がある場合は、自動プロビジョニングを停止する必要があります。
- Microsoft Monitoring Agent (MMA) 拡張機能をインストールすると、エージェントのバージョンが更新される。 これは、ダイレクト エージェントと SCOM エージェントに該当します (後者の場合、SCOM と MMA で共通のランタイム ライブラリが共有され、それはプロセスで更新されます)。 インストールされている SCOM エージェントがバージョン 2012 であり、これがアップグレードされている場合、SCOM サーバーもバージョン 2012 になっていると、管理容易性の機能が失われる恐れがあります。 インストールされている SCOM エージェントがバージョン 2012 の場合、自動プロビジョニングの停止を検討する必要があります。
- サブスクリプション (一元化されたワークスペース) の外部にカスタム ワークスペースを保持している場合、自動プロビジョニングを停止する必要があります。 手動で Microsoft Monitoring Agent 拡張機能をインストールして、Security Center が接続をオーバーライドせずに、お使いのワークスペースに接続できます。
- サブスクリプションごとに複数のワークスペースが作成されることを回避したいと考えていて、サブスクリプション内に独自のカスタム ワークスペースがある場合、次の 2 つの選択肢があります。

   1. 自動プロビジョニングを停止できます。 移行後に、「[既存の Log Analytics ワークスペースを使用するにはどうすればよいですか?](#how-can-i-use-my-existing-log-analytics-workspace)」の説明に従って、既定のワークスペース設定を設定します。
   2. 移行の完了を許可して、VM 上に Microsoft Monitoring Agent がインストールされ、作成されたワークスペースに VM が接続されるようにします。 その後、既にインストールされているエージェントを再構成し、既定のワークスペース設定を設定して、独自のカスタム ワークスペースを選択します。 詳細については、「[既存の Log Analytics ワークスペースを使用するにはどうすればよいですか?](#how-can-i-use-my-existing-log-analytics-workspace)」をご覧ください。

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>自動プロビジョニングを停止すると、どのような影響がありますか?
移行が完了すると、Security Center は VM からセキュリティ データを収集できなくなります。また、一部のセキュリティの推奨とアラートを使用できなくなります。 停止した場合、Microsoft Monitoring Agent を手動でインストールする必要があります。 [停止する場合に推奨される手順](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)に関するセクションをご覧ください。

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>自動プロビジョニングを停止する場合、どのような手順が推奨されますか?

Security Center がお使いの VM からセキュリティ データを収集して、推奨や通知を提示できるように、Microsoft Monitoring Agent 拡張機能を手動でインストールする必要があります。 インストールのガイダンスとして、[Windows VM 用のエージェントのインストール](../virtual-machines/extensions/oms-windows.md)または [Linux VM 用のエージェントのインストール](../virtual-machines/extensions/oms-linux.md)に関するページを参照してください。

エージェントをいずれかの既存のカスタム ワークスペースまたは Security Center が作成したワークスペースに接続できます。 カスタム ワークスペースの 'Security' または 'SecurityCenterFree' ソリューションが有効になっていない場合は、ソリューションを適用する必要があります。 適用するには、 **[セキュリティ ポリシー – 価格レベル]** ブレードを利用して、カスタム ワークスペースまたはサブスクリプションを選択します。

   ![価格レベル][1]

Security Center は、選択された価格レベルに基づいて、ワークスペース上で適切なソリューションを有効にします。

### Security Center にインストールされている OMS 拡張機能を削除するにはどうすればよいですか?<a name="remove-oms"></a>
Microsoft Monitoring Agent は手動で削除することができます。 ただし、Security Center の推奨とアラートが制限されるため、削除は推奨されません。

> [!NOTE]
> データ収集が有効な場合、削除しても、Security Center でエージェントが再インストールされます。  エージェントを手動で削除する前に、データ収集を無効にする必要があります。 データ収集を無効にする手順については、「自動的なエージェントのインストールとワークスペースの作成を停止するにはどうすればよいですか?」を参照してください。
>
>

エージェントを手動で削除するには:

1.  ポータルで、 **[Log Analytics]** を開きます。
2.  [Log Analytics] ブレードで、ワークスペースを選択します。
3.  監視しない各 VM を選択し、 **[切断]** を選択します。

   ![エージェントを削除する][3]

> [!NOTE]
> Linux VM に拡張機能ではない OMS エージェントが既にある場合、拡張機能を削除するとエージェントも削除されるので、ユーザーは再インストールする必要があります。
>
>
### <a name="how-do-i-disable-data-collection"></a>データ収集を無効にするにはどうしたらよいですか。
自動プロビジョニングは、既定ではオフです。 リソースの自動プロビジョニングは、セキュリティ ポリシーでこの設定をオフにすることで、いつでも無効にすることができます。 セキュリティ アラートや、システムの更新プログラム、OS の脆弱性、Endpoint Protection に関する推奨事項を取得するために、自動プロビジョニングを使用することを強くお勧めします。

データ収集を無効にするには、[Azure portal にサインイン](https://portal.azure.com)して、 **[参照]** 、 **[セキュリティ センター]** 、 **[ポリシーの選択]** の順に選びます。 自動プロビジョニングを無効にするサブスクリプションを選択します。 サブスクリプションを選ぶと、 **[セキュリティ ポリシー] - [データ収集]** が開きます。 **[自動プロビジョニング]** で **[オフ]** を選びます。

### <a name="how-do-i-enable-data-collection"></a>データ収集を有効にするにはどうしたらよいですか。
Azure サブスクリプションのデータ収集の有効化は、セキュリティ ポリシーで行うことができます。 データ収集を有効にするには、 [Azure portal にサインイン](https://portal.azure.com)して、 **[参照]** 、 **[セキュリティ センター]** 、 **[セキュリティ ポリシー]** の順に選びます。 自動プロビジョニングを有効にするサブスクリプションを選択します。 サブスクリプションを選ぶと、 **[セキュリティ ポリシー] - [データ収集]** が開きます。 **[自動プロビジョニング]** で **[オン]** を選びます。

### <a name="what-happens-when-data-collection-is-enabled"></a>データ収集を有効にするとどうなりますか。
自動プロビジョニングを有効にすると、Security Center は、サポートされているすべての Azure VM と新しく作成される VM に Microsoft Monitoring Agent をプロビジョニングします。 自動プロビジョニングを強くお勧めしますが、エージェントを手動でインストールすることもできます。 [Microsoft Monitoring Agent の拡張機能をインストールする方法を参照してください](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)。 

エージェントで、プロセス作成イベント 4688 とイベント 4688 内の *CommandLine* フィールドが有効になります。 VM に作成された新しいプロセスは EventLog に記録され、Security Center の検出サービスによって監視されます。 新しいプロセスごとに記録される詳細については、[4688 の説明フィールド](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields)を参照してください。 また、エージェントは VM に作成された 4688 イベントも収集し、検索に保存します。

さらに、エージェントは、[適応型アプリケーション制御](security-center-adaptive-application.md)のデータ収集を有効にでき、Security Center は、ローカル AppLocker ポリシーを監査モードで構成して、すべてのアプリケーションを許可します。 これをきっかけとして AppLocker がイベントを生成するようになり、そのイベントが Security Center によって収集されて活用されます。 ただし、既に AppLocker ポリシーが構成されているマシンでは、このポリシーが構成されないので注意してください。 

[セキュリティ連絡先の情報](security-center-provide-security-contact-details.md)が指定されている場合、Security Center が VM で疑わしいアクティビティを検出すると、電子メールでユーザーに通知します。 Security Center のセキュリティ アラート ダッシュボードにもアラートが表示されます。



### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent はサーバーのパフォーマンスに影響しますか。
エージェントは、システム リソースのわずかな量しか消費しないため、パフォーマンスにほとんど影響しません。 パフォーマンスの影響と、エージェントおよび拡張機能の詳細については、[計画および運用ガイド](security-center-planning-and-operations-guide.md#data-collection-and-storage)を参照してください。

### <a name="where-is-my-data-stored"></a>データはどこに格納されますか。
このエージェントから収集されたデータは、サブスクリプションに関連付けられている既存の Log Analytics ワークスペースまたは新規のワークスペースのいずれかに格納されます。 詳細については、[データ セキュリティ](security-center-data-security.md)に関するページを参照してください。

## 既存の Azure Monitor ログ ユーザー<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center は VM とワークスペース間の既存の接続をオーバーライドしますか?
VM に Azure 拡張機能としてインストールされている Microsoft Monitoring Agent が既にある場合、Security Center は既存のワークスペース接続をオーバーライドしません。 代わりに、Security Center は既存のワークスペースを使用します。 VM のレポート先のワークスペースに "Security" または "SecurityCenterFree" ソリューションがインストールされている場合、VM は保護されます。 

[データ収集] 画面で選択されているワークスペースに Security Center ソリューションがインストールされ (まだ存在しない場合)、ソリューションは関連する VM にのみ適用されます。 ソリューションを追加すると、既定では、そのソリューションは、Log Analytics ワークスペースに接続されているすべての Windows エージェントおよび Linux エージェントに自動的にデプロイされます。 [ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)を使用すると、ソリューションにスコープを適用することができます。

Microsoft Monitoring Agent が VM に (Azure 拡張機能としてではなく) 直接インストールされている場合、Security Center によって Microsoft Monitoring Agent がインストールされず、セキュリティの監視は制限されます。

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center は既存の Log Analytics ワークスペースにソリューションをインストールしますか? 課金にどのような影響がありますか?
その VM が、作成したワークスペースに既に接続されていることを Security Center が特定すると、Security Center はご利用の価格レベルに従ってそのワークスペースでソリューションを有効にします。 ソリューションは、[ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)に従って関連する Azure VM にのみ適用されるため、課金額は同じままです。

- **Free レベル** - Security Center によってワークスペースに 'SecurityCenterFree' ソリューションがインストールされます。 Free レベルの料金はかかりません。
- **Standard レベル** - Security Center によってワークスペースに 'Security' ソリューションがインストールされます。

   ![既定のワークスペースのソリューション][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>環境内に既にワークスペースがありますが、それらを使用してセキュリティ データを収集できますか?
VM に Azure 拡張機能としてインストールされている Microsoft Monitoring Agent が既にある場合、Security Center は既存の接続済みワークスペースを使用します。 Security Center ソリューションがワークスペースにまだ存在しない場合は、インストールされます。また、[ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)に従って、ソリューションは関連する VM にのみ適用されます。

Security Center で VM に Microsoft Monitoring Agent がインストールされると、Security Center で作成された既定のワークスペースが使用されます。

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>ワークスペースにはセキュリティ ソリューションが既にあります。 課金にどのような影響がありますか?
Security & Audit ソリューションは、Azure VM で Security Center Standard レベルの機能を有効にするために使用されます。 Security & Audit ソリューションがワークスペースに既にインストールされている場合、Security Center は既存のソリューションを使用します。 課金額の変更はありません。

## <a name="using-azure-security-center"></a>Azure セキュリティ センターの使用
### <a name="what-is-a-security-policy"></a>セキュリティ ポリシーとは何ですか。
セキュリティ ポリシーは、指定されたサブスクリプション内のリソースに対して推奨されるコントロールのセットを定義します。 Azure Security Center では、セキュリティに関する会社の要件、各サブスクリプションでのアプリケーションの種類やデータの機密度に合わせて Azure サブスクリプションのポリシーを定義します。

Azure Security Center で有効になっているセキュリティ ポリシーが、セキュリティに関する推奨事項と監視を促進します。 セキュリティ ポリシーの詳細については、「 [Azure セキュリティ センターでのセキュリティ ヘルスの監視](security-center-monitoring.md)」を参照してください。

### <a name="who-can-modify-a-security-policy"></a>セキュリティ ポリシーを変更できるのは誰ですか。
セキュリティ ポリシーを変更するには、セキュリティ管理者であるか、そのサブスクリプションの所有者または共同作成者である必要があります。

セキュリティ ポリシーを構成する方法については、「 [Azure セキュリティ センターでのセキュリティ ポリシーの設定](tutorial-security-policy.md)」を参照してください。

### <a name="what-is-a-security-recommendation"></a>セキュリティに関する推奨事項とは
Azure セキュリティ センターは、Azure リソースのセキュリティの状態を分析します。 潜在的なセキュリティの脆弱性が識別されると、推奨事項が作成されます。 推奨事項では、必要なコントロールを構成する手順を説明します。 次に例をいくつか示します。

* 悪意のあるソフトウェアを識別して削除するためのマルウェア対策をプロビジョニングする
* 仮想マシンへのトラフィックを制御するための[ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)とルール
* Web アプリケーションを対象とする攻撃から保護するための Web アプリケーション ファイアウォールをプロビジョニングする
* 不足しているシステムの更新をデプロイする
* 推奨基準と一致しない OS 構成に対処する

ここにはセキュリティ ポリシーで有効な推奨事項のみが表示されています。

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Azure リソースの現在のセキュリティ状態を確認する方法を教えてください。
**[Security Center Overview]\(Security Center の概要\)** ブレードでは、お使いの環境の全体的なセキュリティ体制が [コンピューティング]、[ネットワーク]、[ストレージおよびデータ]、[アプリケーション] に分けて詳しく示されます。 リソースの種類にはそれぞれ、潜在的なセキュリティの脆弱性が確認されたかどうかを示すインジケーターがあります。 各タイルをクリックすると、Security Center によって特定されたセキュリティの問題の一覧が、サブスクリプション内のリソースのインベントリと共に表示されます。

### <a name="what-triggers-a-security-alert"></a>セキュリティの警告をトリガーするものは何ですか。
Azure Security Center は、Azure のリソース、ネットワーク、パートナー ソリューション (マルウェア対策やファイアウォールなど) から、自動的にログ データを収集して分析し、結合します。 脅威が検出されると、セキュリティの警告が作成されます。 例には次の検出が含まれます。

* 既知の悪意のある IP アドレスと通信する、セキュリティ侵害された仮想マシン
* Windows エラー報告を使用して検出された高度なマルウェア
* 仮想マシンに対するブルート フォース攻撃
* マルウェア対策や Web アプリケーション ファイアウォールなどのセキュリティ ソリューションの統合パートナーからのセキュリティの警告

### セキュリティ スコア値が変わったのはなぜですか? <a name="secure-score-faq"></a>
2019 年 2 月の時点で、Security Center では、推奨事項の重大度に合わせて一部の推奨事項のスコアを調整しました。 この調整により、全体的なセキュリティ スコア値が変わる可能性があります。  セキュリティ スコアの詳細については、「[Secure score calculation (セキュリティ スコアの計算)](security-center-secure-score.md)」をご覧ください。

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Microsoft Security Response Center と Azure Security Center によって検出され、警告される脅威の違いは何ですか。
Microsoft Security Response Center (MSRC) では、Azure のネットワークとインフラストラクチャの選択的なセキュリティ監視を行い、第三者から脅威インテリジェンスと不正使用の報告を受け取ります。 不正利用者や許可されていない利用者が顧客データにアクセスしたことや、顧客による Azure の利用方法が利用規約の条件に従っていないことを MSRC が検出すると、セキュリティ インシデント マネージャーが顧客に通知します。 通常、通知は、Azure Security Center で指定されたセキュリティ担当者または Azure サブスクリプション所有者 (セキュリティ担当者が指定されていない場合) に電子メールで送られます。

Security Center は、顧客の Azure 環境を継続的に監視し、分析を適用して悪意のある可能性があるさまざまなアクティビティを自動的に検出する Azure サービスです。 検出されたアクティビティは、Security Center のダッシュボードにセキュリティの警告として表示されます。

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Azure Security Center の監視対象になる Azure リソースは何ですか。
Azure Security Center は、次の Azure リソースを監視します。

* 仮想マシン (VM) ( [Cloud Services](../cloud-services/cloud-services-choose-me.md)を含む)
* 仮想マシン スケール セット (VMSS)
* Azure 仮想ネットワーク
* Azure SQL サービス
* Azure ストレージ アカウント
* ([App Service 環境](../app-service/environment/intro.md)にある) Azure Web アプリ
* Azure サブスクリプションに統合済みのパートナー ソリューション (VM 上および App Service Environment 上の Web アプリケーション ファイアウォールなど)

さらに、Azure 以外 (オンプレミスを含む) のコンピューターも、Azure Security Center で監視できます ([Windows コンピューター](./quick-onboard-windows-computer.md)と [Linux コンピューター](./quick-onboard-linux-computer.md)の両方がサポートされます)

## <a name="virtual-machines"></a>Virtual Machines
### <a name="what-types-of-virtual-machines-are-supported"></a>サポートされる仮想マシンのタイプは何ですか。
監視と推奨事項は、[クラシック デプロイ モデルと Resource Manager デプロイ モデル](../azure-classic-rm.md)のどちらで作成された仮想マシン (VM) でも利用できます。

サポートされるプラットフォームの一覧については、「[Azure Security Center でサポートされているプラットフォーム](security-center-os-coverage.md)」を参照してください。

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Azure VM で実行しているマルウェア対策ソリューションが、Azure Security Center で認識されないのはなぜですか。
Azure Security Center では、Azure 拡張機能によりインストールされたマルウェア対策ソリューションを可視化できます。 たとえば、Azure Security Center では、お客様のイメージにプレインストールされていたマルウェア対策ソリューションや、お客様が独自のプロセス (構成管理システムなど) で仮想マシンにインストールしたマルウェア対策ソリューションは検出できません。

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>VM で "スキャン データがありません" というメッセージが表示されるのはなぜですか。
このメッセージが表示されるのは、VM のスキャン データがない場合です。 Azure Security Center でデータ収集が有効になると、スキャン データが取り込まれるまでにある程度の時間 (1 時間未満) がかかる場合があります。 スキャン データの初回作成以降でも、スキャン データが存在しなかったり、最近のスキャン データがなかったりする場合にはこのメッセージが表示される可能性があります。 停止状態の VM のスキャン データは取り込まれません。 スキャン データが最近取り込まれていない場合 (Windows エージェントの保持ポリシーに従い、既定値は 30 日間) にも、このメッセージが表示されることがあります。

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Security Center は、オペレーティング システムの脆弱性、システムの更新、およびエンドポイントの保護の問題をどのくらいの頻度でスキャンしますか。
脆弱性、更新プログラム、その他の問題を Security Center がスキャンするときの待ち時間は次の通りです。

- オペレーティング システムのセキュリティ構成 - データは 48 時間以内に更新されます
- システムの更新プログラム - 24 時間以内にデータを更新
- Endpoint Protection の問題 - 8 時間以内にデータを更新

Security Center は通常、1 時間ごとに新しいデータをスキャンし、それに応じて推奨事項を更新します。 

> [!NOTE]
> 2017 年 6 月上旬以降、Security Center では、Microsoft Monitoring Agent を使用してデータの収集と格納を行います。 詳細については、「[Azure Security Center のプラットフォームの移行](security-center-platform-migration.md)」を参照してください。 この記事の情報は、Microsoft Monitoring Agent に移行した後の Security Center の機能を示しています。
>
>

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>"VM Agent is Missing? (VM エージェントが見つかりません)" というメッセージが表示されるのはなぜですか。
データ収集を有効にするには、VM エージェントが VM にインストールされている必要があります。 既定では、Azure Marketplace からデプロイされた VM に VM エージェントがインストールされます。 他の VM への VM エージェントのインストール方法については、 [VM エージェントと拡張機能](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)に関するブログ投稿を参照してください。


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
