---
title: "Security Center のプラットフォーム移行についてよく寄せられる質問 | Microsoft Docs"
description: "ここでは、Azure Security Center プラットフォームの移行についてよく寄せられる質問に答えます。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: 4b88b5015fcf44e8979b8b1a3aa1eb26f0fbb704
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="security-center-platform-migration-faq"></a>Security Center プラットフォームの移行についてよく寄せられる質問
2017 年 6 月上旬より、Azure Security Center では、Microsoft Monitoring Agent を使用してデータの収集と格納を行っています。 詳細については、「[Azure Security Center のプラットフォームの移行](security-center-platform-migration.md)」を参照してください。 ここでは、プラットフォームの移行についてよく寄せられる質問に答えます。

## <a name="data-collection-agents-and-workspaces"></a>データの収集、エージェント、およびワークスペース

### <a name="how-is-data-collected"></a>データの収集方法について教えてください
Security Center では、Microsoft Monitoring Agent を使用して、ユーザーの VM からセキュリティ データを収集しています。 セキュリティ データには、脆弱性を識別するために使用されるセキュリティ構成と、脅威を検出するために使用されるセキュリティ イベントに関する情報が含まれています。 エージェントで収集されたデータは、VM に接続されている既存の Log Analytics ワークスペース、または Security Center で作成された新しいワークスペースに保存されます。 Security Center で新しいワークスペースを作成するときには、VM の位置情報が考慮されます。

> [!NOTE]
> Microsoft Monitoring Agent は、Operations Management Suite (OMS)、Log Analytics サービス、および System Center Operations Manager (SCOM) で使用されるものと同じエージェントです。
>
>

初めてデータ収集を有効にした場合、またはサブスクリプションが移行されたときに、Security Center は各 VM に Azure 拡張機能として Microsoft Monitoring Agent が既にインストールされているかどうかを確認します。 Microsoft Monitoring Agent がインストールされていない場合、Security Center では次の処理が実行されます。

- VM に Microsoft Monitoring Agent をインストールする
   - Security Center で作成されたワークスペースが VM と同じ位置情報に既に存在する場合、エージェントはこのワークスペースに接続されます
   - ワークスペースが存在しない場合、Security Center で新しいリソース グループとその位置情報の既定のワークスペースが作成され、エージェントがそのワークスペースに接続されます。 ワークスペースとリソース グループの名前付け規則は次のとおりです。

       ワークスペース: DefaultWorkspace-[subscription-ID]-[geo]

       リソース グループ: DefaultResouceGroup-[geo]
- Security Center ソリューションをワークスペースにインストールする

ワークスペースの場所は VM の位置に基づいています。 詳細については、「[Azure Security Center のデータ セキュリティ](security-center-data-security.md)」を参照してください。

> [!NOTE]
> プラットフォームの移行前に、Security Center で Azure Monitoring Agent を使用して VM からセキュリティ データが収集され、データはストレージ アカウントに保存されています。 プラットフォームの移行後は、Security Center では Microsoft Monitoring Agent とワークスペースを使用して同じデータが収集され、保存されます。 移行後は、ストレージ アカウントを削除できます。
>
>

### <a name="am-i-billed-for-log-analytics-or-oms-on-the-workspaces-created-by-security-center"></a>Security Center で作成されたワークスペース上の Log Analytics または OMS には課金されますか?
いいえ。 ノードごとの OMS の課金が構成されている場合でも、Security Center で作成されるワークスペースに OMS の料金はかかりません。 Security Center の課金は、常に Security Center セキュリティ ポリシーとワークスペースにインストールされているソリューションに基づいています。

- **Free レベル** - Security Center によって既定のワークスペースに 'SecurityCenterFree' ソリューションがインストールされます。 Free レベルの料金はかかりません。
- **Standard レベル** - Security Center によって既定のワークスペースに 'SecurityCenterFree' および 'Security’ ソリューションがインストールされます。

価格の詳細については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」を参照してください。 この価格ページでは、2017 年 6 月からのセキュリティ データ ストレージと日割り課金の変更が反映されています。

> [!NOTE]
> Security Center で作成されたワークスペースの OMS 価格レベルは、Security Center の課金に影響しません。
>
>

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Security Center で作成された既定のワークスペースは削除できますか?
**既定のワークスペースを削除することは推奨されません。** Security Center は、既定のワークスペースを使用して VM のセキュリティ データを格納します。  ワークスペースを削除すると、Security Center はこのデータを収集できず、一部のセキュリティの推奨とアラートと使用できなくなります。

復旧するには、削除したワークスペースに接続されている VM 上の Microsoft Monitoring Agent を削除します。 Security Center によってエージェントが再インストールされ、新しい既定のワークスペースが作成されます。

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>既存の Log Analytics ワークスペースを使用するにはどうすればよいですか?

Security Center によって収集されたデータを保存する既存の Log Analytics ワークスペースを選択できます。 既存の Log Analytics ワークスペースを使用する場合、次の要件があります。

- 選択した Azure サブスクリプションにワークスペースを関連付ける必要があります。
- ワークスペースにアクセスするには、少なくとも読み取りアクセス許可が必要です。

既存の Log Analytics ワークスペースを選択するには、次の手順に従います。

1. **[セキュリティ ポリシー - データ収集]** で、**[Use another workspace]\(別のワークスペースを使用する\)** を選択します。

   ![別のワークスペースを使用する][5]

2. プルダウン メニューから、収集したデータを保存するワークスペースを選択します。

   > [!NOTE]
   > プルダウン メニューには、Azure サブスクリプション内の、アクセスできるワークスペースだけが表示されます。
   >
   >

3. [ **保存**] を選択します。
4. **[保存]** をクリックすると、監視対象の VM を再構成するかどうかをたずねられます。

   - 新しいワークスペース設定を**新しい VM にのみ適用**する場合は、**[いいえ]** を選択します。 この場合、新しいワークスペース設定は、エージェントの新しいインストール (Microsoft Monitoring Agent がインストールされていない、新たに検出された VM) にのみ適用されます。
   - 新しいワークスペース設定を**すべての VM に適用**する場合は、**[はい]** を選択します。 この場合、Security Center によって作成されたワークスペースに接続されているすべての VM が、新しいターゲット ワークスペースに再接続されます。

   > [!NOTE]
   > [はい] を選択した場合、すべての VM が新しいターゲット ワークスペースに再接続されるまで、Security Center によって作成されたワークスペースを削除しないでください。 ワークスペースの削除が早すぎると、この操作は失敗します。
   >
   >

   - 操作を取り消すには、**[キャンセル]** をクリックします。

      ![監視対象の VM を再構成する][6]

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>VM の拡張機能として既に Microsoft Monitoring Agent がインストールされている場合はどうなりますか?
Security Center は、ユーザー ワークスペースへの既存の接続を上書きしません。 Security Center は、VM のセキュリティ データを既に接続されているワークスペースに保存します。

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>コンピューターに Microsoft Monitoring Agent がインストールされていて、拡張機能としてインストールされていない場合はどうなりますか?
Microsoft Monitoring Agent が VM に (Azure 拡張機能としてではなく) 直接インストールされている場合、Security Center によって Microsoft Monitoring Agent がインストールされず、セキュリティの監視は制限されます。

### <a name="what-is-the-impact-of-removing-these-extensions"></a>これらの拡張機能を削除するとどのような影響がありますか?
Microsoft Monitoring Extension を削除すると、Security Center は VM からセキュリティ データを収集できなくなります。また、一部のセキュリティの推奨とアラートを使用できなくなります。 Security Center は、VM に拡張機能が存在せず、拡張機能を再インストールすることが 24 時間以内に判断されます。

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>自動的なエージェントのインストールとワークスペースの作成を停止するにはどうすればよいですか?
セキュリティ ポリシーでサブスクリプションの自動プロビジョニングを無効にすることはできますが、これは推奨されません。 自動プロビジョニングを無効にすると、Security Center の推奨事項とアラートが制限されます。 自動プロビジョニングは、Standard 価格レベルのサブスクリプションでは必須です。 自動プロビジョニングを無効にするには、次の手順に従います。

1. Standard レベルのサブスクリプションを構成する場合、そのサブスクリプションのセキュリティ ポリシーを開き、**[Free]** レベルを選択します。

   ![[価格レベル] ][1]

2. 次に、**[セキュリティ ポリシー - データ収集]** ブレードで **[オフ]** を選択して自動プロビジョニングを無効にします。
   ![データ収集][2]

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Security Center にインストールされている OMS 拡張機能を削除するにはどうすればよいですか?
Microsoft Monitoring Agent は手動で削除することができます。 ただし、Security Center の推奨とアラートが制限されるため、削除は推奨されません。

> [!NOTE]
> データ収集が有効な場合、削除しても、Security Center でエージェントが再インストールされます。  エージェントを手動で削除する前に、データ収集を無効にする必要があります。 データ収集を無効にする手順については、「[自動的なエージェントのインストールとワークスペースの作成を停止するにはどうすればよいですか?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?)」を参照してください。
>
>

エージェントを手動で削除するには:

1.  ポータルで、**[Log Analytics]** を開きます。
2.  [Log Analytics] ブレードで、ワークスペースを選択します。
3.  監視しない各 VM を選択し、**[切断]** を選択します。

   ![エージェントを削除する][3]

> [!NOTE]
> Linux VM に拡張機能ではない OMS エージェントが既にある場合、拡張機能を削除するとエージェントも削除されるので、ユーザーは再インストールする必要があります。
>
>

## <a name="existing-oms-customers"></a>既存の OMS ユーザー

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center は VM とワークスペース間の既存の接続を上書きしますか?
VM に Azure 拡張機能としてインストールされている Microsoft Monitoring Agent が既にある場合、Security Center は既存のワークスペース接続を上書きしません。 代わりに、Security Center は既存のワークスペースを使用します。

Security Center ソリューションがワークスペースにまだ存在しない場合は、インストールされます。また、ソリューションは関連する VM にのみ適用されます。 ソリューションを追加すると、既定では、そのソリューションは、Log Analytics ワークスペースに接続されているすべての Windows エージェントおよび Linux エージェントに自動的にデプロイされます。 OMS 機能である[ソリューションのターゲット設定](../operations-management-suite/operations-management-suite-solution-targeting.md)を使用すると、ソリューションにスコープを適用することができます。

Microsoft Monitoring Agent が VM に (Azure 拡張機能としてではなく) 直接インストールされている場合、Security Center によって Microsoft Monitoring Agent がインストールされず、セキュリティの監視は制限されます。

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>データ プラットフォームの移行によって、VM とワークスペースのいずれかの間で接続が切断されたと思われる場合はどうすればよいですか?
このような問題はまず発生しません。 このような問題が発生した場合は、[Azure サポート リクエストを作成](../azure-supportability/how-to-create-azure-support-request.md)し、次の詳細情報を記載してください。

- 影響を受ける VM の Azure リソース ID
- 接続が切断する前の、拡張機能に構成されていたワークスペースの Azure リソース ID
- 以前にインストールされていたエージェントとバージョン

### <a name="does-security-center-install-solutions-on-my-existing-oms-workspaces-what-are-the-billing-implications"></a>Security Center は既存の OMS ワークスペースにソリューションをインストールしますか? 課金にどのような影響がありますか?
その VM が、作成したワークスペースに既に接続されていることを Security Center が特定すると、Security Center はご利用の価格レベルに従ってそのワークスペースでソリューションを有効にします。 ソリューションは、[ソリューションのターゲット設定](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting)に従って関連する Azure VM にのみ適用されるため、課金額は同じままです。

- **Free レベル** - Security Center によってワークスペースに 'SecurityCenterFree' ソリューションがインストールされます。 Free レベルの料金はかかりません。
- **Standard レベル** - Security Center によってワークスペースに 'SecurityCenterFree' および 'Security’ ソリューションがインストールされます。

   ![既定のワークスペースのソリューション][4]

> [!NOTE]
> Log Analytics の 'Security' ソリューションは、OMS の Security & Audit ソリューションです。
>
>

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>環境内に既にワークスペースがありますが、それらを使用してセキュリティ データを収集できますか?
VM に Azure 拡張機能としてインストールされている Microsoft Monitoring Agent が既にある場合、Security Center は既存の接続済みワークスペースを使用します。 Security Center ソリューションがワークスペースにまだ存在しない場合は、インストールされます。また、[ソリューションのターゲット設定](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting)に従って、ソリューションは関連する VM にのみ適用されます。

Security Center で VM に Microsoft Monitoring Agent がインストールされると、Security Center で作成された既定のワークスペースが使用されます。 使用するワークスペースを構成する機能が間もなくリリースされる予定です。

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>ワークスペースにはセキュリティ ソリューションが既にあります。 課金にどのような影響がありますか?
Security & Audit ソリューションは、Azure VM で Security Center Standard レベルの機能を有効にするために使用されます。 Security & Audit ソリューションがワークスペースに既にインストールされている場合、Security Center は既存のソリューションを使用します。 課金額の変更はありません。

## <a name="next-steps"></a>次のステップ
Security Center プラットフォームの移行の詳細については、以下を参照してください。

- [Azure Security Center のプラットフォームの移行](security-center-platform-migration.md)
- [Azure Security Center トラブルシューティング ガイド](security-center-troubleshooting-guide.md)

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
[6]: ./media/security-center-platform-migration-faq/reconfigure-monitored-vm.png
