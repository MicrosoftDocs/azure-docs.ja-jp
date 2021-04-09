---
title: Azure Security Center のエージェントの自動デプロイ | Microsoft Docs
description: この記事では、Azure Security Center で使用される Log Analytics エージェントおよびその他のエージェントと拡張機能の自動プロビジョニングを設定する方法について説明します
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 17f3440df4fa88995f2148680aba926207a0e46b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561264"
---
# <a name="configure-auto-provisioning-for-agents-and-extensions-from-azure-security-center"></a>Azure Security Center からのエージェントと拡張機能の自動プロビジョニングを構成する

Azure Security Center では、リソースに関連するエージェントまたは拡張機能と、有効にしたデータ収集の種類を使用して、お使いのリソースからデータを収集します。 次の手順を使用して、Security Center で使用される必要なエージェントと拡張機能がお使いのリソースにあることを確認します。

## <a name="prerequisites"></a>前提条件
セキュリティ センターを使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

## <a name="availability"></a>可用性

| 側面                  | 詳細                                                                                                                                                                                                                      |
|-------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| リリース状態:          | **機能**:自動プロビジョニングが一般提供 (GA)<br>**エージェントと拡張機能**: Azure VM 用の Log Analytics エージェントは GA です。Microsoft Dependency エージェントはプレビュー段階です。Kubernetes 用のポリシー アドオンは GA です                |
| 価格:                | Free                                                                                                                                                                                                                         |
| サポートされる宛先: | ![はい](./media/icons/yes-icon.png) Azure マシン<br>![いいえ](./media/icons/no-icon.png) Azure Arc マシン<br>![いいえ](./media/icons/no-icon.png) Kubernetes ノード<br>![いいえ](./media/icons/no-icon.png) Virtual Machine Scale Sets |
| クラウド:                 | ![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) US Gov、China Gov、その他の Gov                                                                                                      |
|                         |                                                                                                                                                                                                                              |

## <a name="how-does-security-center-collect-data"></a>Security Center でのデータの収集方法

Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシン (VM)、仮想マシン スケール セット、IaaS コンテナー、Azure 以外 (オンプレミスを含む) のマシンからデータを収集します。 

不足している更新プログラム、OS のセキュリティ設定ミス、エンドポイント保護のステータス、正常性と脅威の防止を可視化するためには、データ収集が欠かせません。 データ収集を必要とするのは、コンピューティング リソース (VM、仮想マシン スケール セット、IaaS コンテナー、非 Azure コンピューターなど) だけです。 

エージェントをプロビジョニングしない場合でも、Azure Security Center は役に立ちます。 ただし、セキュリティに制限があり、上記の機能はサポートされません。  

データは以下を使用して収集されます。

- **Log Analytics エージェント**: セキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータをワークスペースにコピーします。 このようなデータの例として、オペレーティング システムの種類とバージョン、オペレーティング システム ログ (Windows イベント ログ)、実行中のプロセス、マシン名、IP アドレス、ログイン ユーザーなどがあります。
- **セキュリティ拡張機能** ([Kubernetes 用の Azure Policy アドオン](../governance/policy/concepts/policy-for-kubernetes.md)など): 特殊なリソースの種類に関するデータを Security Center に提供することもできます。

> [!TIP]
> Security Center の拡大に伴い、監視できるリソースの種類も増えています。 また、拡張機能の数も増えています。 Azure Policy の機能を活用して追加のリソースの種類をサポートするために、自動プロビジョニングが拡張されました。

## <a name="why-use-auto-provisioning"></a>自動プロビジョニングを使用する理由
このページで説明するエージェントと拡張機能はすべて手動でインストール "*できます*" ([Log Analytics エージェントの手動インストール](#manual-agent)に関するセクションをご覧ください)。 ただし、**自動プロビジョニング** では、必要なすべてのエージェントと拡張機能を既存のマシンと新しいマシンにインストールして、サポートされているすべてのリソースのセキュリティ カバレッジを迅速に確保することで、管理オーバーヘッドを削減します。 

自動プロビジョニングは既定で無効になっていますが、有効にすることをお勧めします。

## <a name="how-does-auto-provisioning-work"></a>自動プロビジョニングのしくみ
Security Center の自動プロビジョニング設定は、サポートされている拡張機能の種類ごとに切り替えることができます。 拡張機能の自動プロビジョニングを有効にする場合は、適切な **DeployIfNotExists** ポリシーを割り当てます。 このポリシーの種類により、その種類および将来のリソースすべてに拡張機能がプロビジョニングされることが保証されます。

> [!TIP]
> DeployIfNotExists など、Azure Policy の効果の詳細については、「[Azure Policy の効果について](../governance/policy/concepts/effects.md)」をご覧ください。


## <a name="enable-auto-provisioning-of-the-log-analytics-agent-and-extensions"></a>Log Analytics エージェントと拡張機能の自動プロビジョニングを有効にする<a name="auto-provision-mma"></a>

Log Analytics エージェントの自動プロビジョニングがオンの場合、Security Center では、サポートされているすべての Azure VM と新しく作成される VM にエージェントをデプロイします。 サポートされるプラットフォームの一覧については、「[Azure Security Center でサポートされているプラットフォーム](security-center-os-coverage.md)」を参照してください。

Log Analytics エージェントの自動プロビジョニングを有効にするには:

1. Security Center のメニューから、 **[価格と設定]** を選択します。
1. 関連するサブスクリプションを選択します。
1. **[自動プロビジョニング]** ページで、Log Analytics エージェントの状態を **[オン]** に設定します。

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Log Analytics エージェントの自動プロビジョニングの有効化":::

1. 構成オプション ペインで、使用するワークスペースを定義します。

    :::image type="content" source="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png" alt-text="Log Analytics エージェントを VM に自動プロビジョニングするための構成オプション" lightbox="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png":::

    - **[Connect Azure VMs to the default workspace(s) created by Security Center]\(Security Center によって作成された既定のワークスペースに Azure VM を接続する\)** - Security Center では、新しいリソース グループと既定のワークスペースを同じ位置情報で作成し、エージェントをそのワークスペースに接続します。 複数の位置情報からの VM がサブスクリプションに含まれている場合、Security Center ではデータ プライバシー要件に確実に準拠するために複数のワークスペースを作成します。

        ワークスペースとリソース グループの名前付け規則は次のとおりです。 
        - ワークスペース: DefaultWorkspace-[subscription-ID]-[geo] 
        - リソース グループ:DefaultResourceGroup-[geo] 

        サブスクリプションで設定された価格レベルに従い、ワークスペースで Security Center ソリューションが自動的に有効になります。 

        > [!TIP]
        > 既定のワークスペースに関する質問については、以下をご覧ください。
        >
        > - [Security Center で作成されたワークスペース上の Azure Monitor ログには課金されますか?](faq-data-collection-agents.md#am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center)
        > - [既定の Log Analytics ワークスペースはどこに作成されますか?](faq-data-collection-agents.md#where-is-the-default-log-analytics-workspace-created)
        > - [Security Center で作成された既定のワークスペースは削除できますか?](faq-data-collection-agents.md#can-i-delete-the-default-workspaces-created-by-security-center)

    - **[Connect Azure VMs to a different workspace]\(Azure VM を別のワークスペースに接続する\)** - ドロップダウン リストから、収集したデータを保存するワークスペースを選択します。 ドロップダウン リストには、自分の全サブスクリプション内のワークスペースがすべて含まれています。 このオプションを使用して、さまざまなサブスクリプションで稼働している仮想マシンからデータを収集し、自分が選択したワークスペースにすべて保存できます。  

        Log Analytics ワークスペースが既にある場合は、同じワークスペースを使用することをお勧めします (ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です)。 このオプションは、組織内で一元化されたワークスペースを使用していて、それをセキュリティ データの収集に使用したい場合に役立ちます。 詳細については、「[Azure Monitor でログ データとワークスペースへのアクセスを管理する](../azure-monitor/logs/manage-access.md)」をご覧ください。

        選択したワークスペースで "Security" または "SecurityCenterFree" ソリューションが既に有効になっている場合は、価格が自動的に設定されます。 それ以外の場合は、Security Center ソリューションをワークスペースにインストールします。

        1. Security Center のメニューから、 **[価格と設定]** を開きます。
        1. エージェントの接続先となるワークスペースを選択します。
        1. **[Azure Defender on]\(Azure Defender のオン\)** または **[Azure Defender off]\(Azure Defender のオフ\)** を選択します。

1. **[Windows セキュリティ イベント]** 構成で、保存する生のイベント データの量を選択します。
    - **なし** – セキュリティ イベントのストレージを無効にします。 これが既定の設定です。
    - **最小** - イベント量を最小限に抑える必要があるお客様向けの小さなイベント セットです。
    - **共通** - ほとんどのお客様のニーズを満たすイベント セットです。完全な監査証跡が得られます。
    - **すべてのイベント** - すべてのイベントを格納する必要があるお客様向けです。

    > [!TIP]
    > ワークスペース レベルでこれらのオプションを設定するには、「[ワークスペース レベルでのセキュリティ イベント オプションの設定](#setting-the-security-event-option-at-the-workspace-level)」をご覧ください。
    > 
    > これらのオプションの詳細については、「[Log Analytics エージェントの Windows セキュリティ イベント オプション](#data-collection-tier)」をご覧ください。

1. 構成ペインで **[適用]** を選択します。

1. Log Analytics エージェント以外の拡張機能の自動プロビジョニングを有効にするには: 

    1. Microsoft Dependency Agent の自動プロビジョニングを有効にする場合は、Log Analytics エージェントが自動デプロイに設定されていることを確認します。
    1. 該当する拡張機能の状態を **[オン]** に切り替えます。

        :::image type="content" source="./media/security-center-enable-data-collection/toggle-kubernetes-add-on.png" alt-text="K8s ポリシー アドオンの自動プロビジョニングを有効に切り替える":::

    1. **[保存]** を選択します。 Azure ポリシーが割り当てられ、修復タスクが作成されます。

        |拡張機能  |ポリシー  |
        |---------|---------|
        |Kubernetes 用の Policy アドオン|[Azure Policy アドオンを Azure Kubernetes Service クラスターにデプロイする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa8eff44f-8c92-45c3-a3fb-9880802d67a7)|
        |Microsoft Dependency Agent (プレビュー) (Windows VM)|[Windows 仮想マシン用の Dependency Agent のデプロイ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1c210e94-a481-4beb-95fa-1571b434fb04)         |
        |Microsoft Dependency Agent (プレビュー) (Linux VM)|[Linux 仮想マシン用の Dependency Agent のデプロイ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da21710-ce6f-4e06-8cdb-5cc4c93ffbee)|
        |||

1. **[保存]** を選択します。 ワークスペースをプロビジョニングする必要がある場合は、エージェントのインストールに最大で 25 分かかることがあります。

1. 既定のワークスペースに以前接続されていた監視対象の VM を再構成するかどうかをたずねられます。

    :::image type="content" source="./media/security-center-enable-data-collection/reconfigure-monitored-vm.png" alt-text="監視対象 VM を再構成するオプションを確認する":::

    - **いいえ** - 新しいワークスペース設定は、Log Analytics エージェントがインストールされていない、新たに検出された VM にのみ適用されます。
    - **はい** - 新しいワークスペース設定は、すべての VM に適用されます。Security Center によって作成されたワークスペースに現在接続されているすべての VM が、新しいターゲット ワークスペースに再接続されます。

   > [!NOTE]
   > **[はい]** を選択した場合、すべての VM が新しいターゲット ワークスペースに再接続されるまで、Security Center によって作成されたワークスペースを削除しないでください。 ワークスペースの削除が早すぎると、この操作は失敗します。


## <a name="windows-security-event-options-for-the-log-analytics-agent"></a>Log Analytics エージェントの Windows セキュリティ イベント オプション<a name="data-collection-tier"></a> 

Azure Security Center でのデータ収集レベルの選択は、Log Analytics ワークスペース内のセキュリティ イベントの "*ストレージ*" にのみ影響します。 Log Analytics エージェントでは、ワークスペースに保存することを選択したセキュリティ イベントのレベルに関係なく、Security Center での脅威の防止に必要なセキュリティ イベントが引き続き収集され、分析されます。 セキュリティ イベントを保存することを選択すると、ワークスペース内でそれらのイベントの調査、検索、監査が可能になります。

### <a name="requirements"></a>必要条件 
Windows セキュリティ イベント データを保存するには、Azure Defender が必要です。 [Azure Defender の詳細をご覧ください](azure-defender.md)。

Log Analytics にデータを格納すると、データ ストレージに対して追加料金が発生する可能性があります。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

### <a name="information-for-azure-sentinel-users"></a>Azure Sentinel ユーザー向けの情報 
Azure Sentinel のユーザー: 単一ワークスペースのコンテキスト内でのセキュリティ イベントの収集は、Azure Security Center と Azure Sentinel のどちらか一方からのみ構成できます。両方からは不可能です。 Azure Security Center から既にアラートを取得しており、セキュリティ イベントを収集するように設定されているワークスペースに Azure Sentinel を追加する予定の場合、次の 2 つの選択肢があります。
- Azure Security Center でのセキュリティ イベントの収集をそのまま使用する。 それらのイベントは、Azure Security Center だけでなく Azure Defender でも照会、分析できるようになります。 ただし、Azure Sentinel でコネクタの接続状態を監視したり、その構成を変更したりすることはできません。 この点が問題となる場合は、2 つ目の選択肢を検討してください。
- Azure Security Center でセキュリティ イベントの収集を無効にします (自分の Log Analytics エージェントの構成で、 **[Windows セキュリティ イベント]** を **[なし]** に設定します)。 次に、Azure Sentinel でセキュリティ イベント コネクタを追加します。 1 つ目の選択肢と同様、イベントの照会と分析は、Azure Sentinel と Azure Defender/ASC の両方で行えますが、コネクタの接続状態を監視したりその構成を変更したりする作業は、Azure Sentinel でしか行えません。

### <a name="what-event-types-are-stored-for-common-and-minimal"></a>"共通" と "最小" で保存されるイベントの種類
これらのセットは、一般的なシナリオに対応するように設計されています。 実装前に、どのセットがニーズに合うかを必ず評価してください。

Microsoft では、**共通** および **最小** オプションのイベントを決定するために、お客様や業界標準化団体と協力して、各イベントがフィルター処理されない頻度とそれらの使用方法を確認しました。 このプロセスでは次のガイドラインが使用されました。

- **最小** - このセットは、ボリュームが非常に小さく、侵害が成功したことを示すイベントと重要なイベントだけを対象とします。 たとえば、このセットにはユーザーの成功したログインと失敗したログイン (イベント ID 4624、4625) が含まれますが、監査には重要であっても検出には重要ではない、比較的ボリュームの大きいサインアウトは含まれません。 このセットのデータ量のほとんどは、ログイン イベントとプロセス作成イベント (イベント ID 4688) です。
- **共通** - このセットでは、完全なユーザー監査証跡を提供します。 たとえば、このセットには、ユーザー ログインとユーザー サインアウトの両方 (イベント ID 4634) が含まれます。 セキュリティ グループの変更などの監査アクション、ドメイン コントローラーの主要な Kerberos 操作、業界組織が推奨するその他のイベントが含まれます。

すべてのイベントではなく、共通セットを選択する主な動機は、特定のイベントを除外することではなく、ボリュームを減らすことであるため、共通セットにはボリュームが非常に小さいイベントが含まれていました。

各セットのすべてのセキュリティ イベント ID と AppLocker イベント ID を次に示します。

| データ層 | 収集したイベント インジケーター |
| --- | --- |
| 最小限 | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| 共通 | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - グループ ポリシー オブジェクト (GPO) を使用している場合は、プロセス作成イベント 4688 の監査ポリシーと、イベント 4688 内の *CommandLine* フィールドを有効にすることをお勧めします。 プロセス作成イベント 4688 の詳細については、Security Center の [FAQ](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled) を参照してください。 これらの監査ポリシーの詳細については、「[Audit Policy Recommendations](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)」(監査ポリシーの推奨事項) を参照してください。
> -  [適応型アプリケーション制御](security-center-adaptive-application.md)のデータ収集を有効にするために、Security Center は、ローカル AppLocker ポリシーを監査モードで構成して、すべてのアプリケーションを許可します。 これをきっかけとして AppLocker がイベントを生成するようになり、そのイベントが Security Center によって収集されて活用されます。 ただし、既に AppLocker ポリシーが構成されているマシンでは、このポリシーが構成されないので注意してください。 
> - Windows フィルタリング プラットフォーム イベント ([イベント ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)) を収集するには、[[フィルタリング プラットフォームの接続の監査]](/windows/security/threat-protection/auditing/audit-filtering-platform-connection) を有効にする必要があります (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable)。
>

### <a name="setting-the-security-event-option-at-the-workspace-level"></a>ワークスペース レベルでのセキュリティ イベント オプションの設定

ワークスペース レベルで保存するセキュリティ イベント データのレベルを定義できます。

1. Azure portal の Security Center のメニューで、 **[価格と設定]** を選択します。
1. 該当するワークスペースを選択します。 ワークスペースのデータ収集イベントは、このページで説明する Windows セキュリティ イベントだけです。

    :::image type="content" source="media/security-center-enable-data-collection/event-collection-workspace.png" alt-text="ワークスペースに保存するセキュリティ イベント データの設定":::

1. 保存する生のイベント データの量を選択し、 **[保存]** を選択します。

## <a name="manual-agent-provisioning"></a>エージェントの手動プロビジョニング <a name="manual-agent"></a>
 
Log Analytics エージェントを手動でインストールするには:

1. 自動プロビジョニングを無効にします。

1. 必要に応じて、ワークスペースを作成します。

1. Log Analytics エージェントをインストールしているワークスペースで Azure Defender を有効にします。

    1. Security Center のメニューから、 **[価格と設定]** を選択します。

    1. エージェントをインストールするワークスペースを設定します。 ワークスペースが存在するサブスクリプションが Security Center で使用しているサブスクリプションと同じであること、また、そのワークスペースに対する読み取りおよび書き込みアクセス許可があることを確認します。

    1. **[Azure Defender on]\(Azure Defender を有効化\)** を選択し、 **[保存]** を選択します。

       >[!NOTE]
       >対象のワークスペースで **Security** ソリューションまたは **SecurityCenterFree** ソリューションが既に有効になっている場合、価格は自動的に設定されます。 

1. Resource Manager テンプレートを使用して新しい VM にエージェントをデプロイするには、Log Analytics エージェントをインストールします。

   - [Windows 用 Log Analytics エージェントをインストールする](../virtual-machines/extensions/oms-windows.md)
   - [Linux 用 Log Analytics エージェントをインストールする](../virtual-machines/extensions/oms-linux.md)

1. 既存の VM にエージェントをデプロイするには、[Azure 仮想マシンに関するデータの収集](../azure-monitor/vm/quick-collect-azurevm.md)に関する記事の手順に従います (「**イベントとパフォーマンス データを収集する**」のセクションは省略可能です)。

1. PowerShell を使用してエージェントをデプロイするには、仮想マシンのドキュメントの手順を使用します。

    - [Windows マシンの場合](../virtual-machines/extensions/oms-windows.md?toc=%2fazure%2fazure-monitor%2ftoc.json#powershell-deployment)
    - [Linux マシンの場合](../virtual-machines/extensions/oms-linux.md?toc=%2fazure%2fazure-monitor%2ftoc.json#azure-cli-deployment)

> [!TIP]
> PowerShell を使用して Security Center をオンボードする方法については、「[Automate onboarding of Azure Security Center using PowerShell](security-center-powershell-onboarding.md)」 (PowerShell を使用して Azure Security Center のオンボードを自動化する) を参照してください。


## <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>既にインストールされているエージェントが存在する場合の自動プロビジョニング <a name="preexisting"></a> 

以下のユース ケースは、エージェントまたは拡張機能が既にインストールされていた場合の自動プロビジョニングの動作について記述したものです。 

- **Log Analytics エージェントがマシンにインストールされているが、拡張機能としてインストールされているわけではない (ダイレクト エージェント)** - Log Analytics エージェントが (Azure 拡張機能としてではなく) VM に直接インストールされている場合、Security Center によって Log Analytics エージェント拡張機能がインストールされ、Log Analytics エージェントが最新バージョンにアップグレードされる可能性があります。
インストールされているエージェントでは、既に構成されているワークスペースに引き続きレポートし、さらに Security Center で構成されたワークスペースにもレポートします (Windows マシンでは、マルチ ホームがサポートされています)。
構成済みのワークスペースが (Security Center の既定のワークスペースではなく) ユーザー ワークスペースの場合、そのワークスペースにレポートする VM およびコンピューターからのイベントの処理を Security Center で始めるには、"Security" または "SecurityCenterFree" ソリューションをインストールする必要があります。

    Linux マシンでは、エージェントのマルチ ホームはまだサポートされていません。そのため、既存のエージェントのインストールが検出されても、自動プロビジョニングは行われず、マシンの構成は変更されません。

    2019 年 3 月 17 日より前に Security Center にオンボードされたサブスクリプションの既存のマシンでは、既存のエージェントが検出されると、Log Analytics エージェント拡張機能はインストールされず、マシンに影響はありません。 これらのマシンについては、マシンでのエージェントのインストールに関する問題を解決するために、"マシンの監視エージェント正常性の問題を解決する" の推奨事項を参照してください。
  
- **System Center Operations Manager エージェントがマシンにインストールされている** - Security Center によって、Log Analytics エージェント拡張機能が既存の Operations Manager と並行してインストールされます。 通常、既存の Operations Manager エージェントは引き続き Operations Manager サーバーに報告します。 Operations Manager エージェントと Log Analytics エージェントは、このプロセス中に最新バージョンに更新される、共通のランタイム ライブラリを共有します。 Operations Manager エージェント バージョン 2012 がインストールされている場合は、自動プロビジョニングを有効に **しないでください**。

- **既存の VM 拡張機能が存在する**:
    - Monitoring Agent が拡張機能としてインストールされている場合、拡張機能の構成では 1 つのワークスペースにのみレポートできます。 Security Center は、ユーザー ワークスペースへの既存の接続をオーバーライドしません。 Security Center では、"Security" または "SecurityCenterFree" ソリューションがインストールされているという条件で、既に接続されているワークスペース内の VM からのセキュリティ データを保存します。 Security Center では、このプロセスで拡張機能のバージョンを最新バージョンにアップグレードする可能性があります。
    - 既存の拡張機能がどのワークスペースにデータを送信しているのかを調べるには、[Azure Security Center との接続を確認](/archive/blogs/yuridiogenes/validating-connectivity-with-azure-security-center)するテストを実行します。 または、Log Analytics ワークスペースを開いてワークスペースを選択し、対象の VM を選択して、Log Analytics エージェント接続を調べることもできます。
    - Log Analytics エージェントがクライアント ワークステーションにインストールされ、既存の Log Analytics ワークスペースにレポートする環境が整っている場合は、[Azure Security Center でサポートされるオペレーティング システム](security-center-os-coverage.md)の一覧で、ご利用のオペレーティング システムがサポートされているかどうかを確認します。 詳しくは、「[既存の Log Analytics ユーザー](./faq-azure-monitor-logs.md)」をご覧ください。
 

## <a name="disable-auto-provisioning"></a>自動プロビジョニングの無効化<a name="offprovisioning"></a>

自動プロビジョニングを無効にすると、エージェントは新しい VM にプロビジョニングされなくなります。

エージェントの自動プロビジョニングをオフにするには:

1. ポータルの Security Center のメニューで、 **[価格と設定]** を選択します。
1. 関連するサブスクリプションを選択します。
1. **[自動プロビジョニング]** を選択します。
1. 該当するエージェントの状態を **[オフ]** に切り替えます。

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="エージェントの種類ごとに自動プロビジョニングを無効に切り替える":::

1. **[保存]** を選択します。 自動プロビジョニングを無効にすると、既定のワークスペース構成セクションは表示されません。

    :::image type="content" source="./media/security-center-enable-data-collection/empty-configuration-column.png" alt-text="自動プロビジョニングを無効にすると、構成セルが空になる":::


> [!NOTE]
>  自動プロビジョニングを無効にしても、Log Analytics エージェントがプロビジョニングされている Azure VM からそのエージェントは削除されません。 OMS 拡張機能の削除については、「[Security Center にインストールされている OMS 拡張機能を削除するにはどうすればよいですか](faq-data-collection-agents.md#remove-oms)」をご覧ください。
>


## <a name="troubleshooting"></a>トラブルシューティング

-   自動プロビジョニングのインストールの問題を特定するには、「[エージェントの正常性の監視に関する問題](security-center-troubleshooting-guide.md#mon-agent)」を参照してください。
-  監視エージェントのネットワーク要件を確認するには、「[監視エージェントのネットワーク要件のトラブルシューティング](security-center-troubleshooting-guide.md#mon-network-req)」を参照してください。
-   手動によるオンボーディングの問題を特定するには、「[操作の管理スイートの契約時の問題をトラブルシューティングする方法 ](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)」を参照してください。



## <a name="next-steps"></a>次のステップ
このページでは、Log Analytics エージェントとその他の Security Center 拡張機能の自動プロビジョニングを有効にする方法について説明しました。 また、収集したデータを格納する Log Analytics ワークスペースの定義方法についても説明しました。 データ収集を有効にするためには、両方の操作が必要となります。 新しいワークスペースと既存のワークスペースのどちらを使用する場合でも、Log Analytics にデータを格納すると、データ ストレージに対して追加料金が発生する可能性があります。 選択した通貨でのお住まいのリージョンに応じた価格の詳細については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」を参照してください。