---
title: Azure Defender for SQL の設定方法
description: Azure Security Center のオプションの Azure Defender for SQL プランを有効にする方法について説明します
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2021
ms.author: memildin
ms.openlocfilehash: b82f0ca0624fcbd64f1c23f87f8f21f96d8e4d4c
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100578"
---
# <a name="enable-azure-defender-for-sql-servers-on-machines"></a>Azure Defender for SQL servers on machines を有効にする 

この Azure Defender プランにより、データベースへのアクセスやデータベースの悪用を試みる、異常で有害と考えられる不自然な動作が検出されます。

疑わしいデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセスやクエリのパターンがある場合に、アラートが表示されます。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|**Azure Defender for SQL servers on machines** は、[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)に記載されているように課金されます|
|保護される SQL のバージョン:|Azure SQL Server (Microsoft サポートの対象となるすべてのバージョン)|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![いいえ](./media/icons/no-icon.png) China Gov、その他の Gov|
|||

## <a name="set-up-azure-defender-for-sql-servers-on-machines"></a>Azure Defender for SQL servers on machines の設定

このプランを有効にするには:

[手順 1. SQL サーバーのホストで Log Analytics エージェントをプロビジョニングする:](#step-1-provision-the-log-analytics-agent-on-your-sql-servers-host)

[手順 2. Security Center の価格と設定ページで、オプションのプランを有効にする:](#step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page)


### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>手順 1. SQL サーバーのホストで Log Analytics エージェントをプロビジョニングする

- **Azure VM 上の SQL Server** - SQL マシンが Azure VM でホストされている場合は、[Log Analytics エージェントの自動プロビジョニングを有効にする <a name="auto-provision-mma"></a>](security-center-enable-data-collection.md#auto-provision-mma)ことができます。 または、手動の手順に従って [Azure Stack VM をオンボードする](quickstart-onboard-machines.md#onboard-your-azure-stack-vms)こともできます。
- **Azure Arc 上の SQL Server** - SQL Server が [Azure Arc](../azure-arc/index.yml) 対応サーバーによって管理されている場合は、Security Center のレコメンデーション [Log Analytics agent should be installed on your Windows-based Azure Arc machines (Preview)] (Log Analytics エージェントを Windows ベースの Azure Arc マシンにインストールする必要があります (プレビュー)) を使用して、Log Analytics エージェントをデプロイできます。 または、[Azure Arc のドキュメント](../azure-arc/servers/manage-vm-extensions.md)に記載されたインストール方法に従うこともできます。

- **オンプレミスの SQL Server** - SQL Server が Azure Arc を使用しないオンプレミスの Windows マシンでホストされている場合は、Azure に接続するためのオプションが 2 つあります。
    
    - **Azure Arc をデプロイする** - 任意の Windows マシンを Security Center に接続できます。 ただし、Azure Arc を使用すると、Azure 環境の *すべて* がより緊密に統合されます。 Azure Arc を設定すると、ポータルに **[SQL Server - Azure Arc]** ページが表示され、セキュリティ アラートはそのページの専用の **[セキュリティ]** タブに表示されます。 そのため、最初に推奨されるオプションは、[ホストで Azure Arc を設定](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows)し、前述の **Azure Arc 上の SQL Server** の手順に従うことです。
        
    - **Azure Arc を使用せずに Windows マシンを接続する** - Azure Arc を使用せずに Windows マシンで実行されている SQL Server を接続する場合は、「[Windows コンピューターを Azure Monitor に接続する](../azure-monitor/agents/agent-windows.md)」の手順に従ってください。


### <a name="step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page"></a>手順 2. Security Center の価格と設定ページで、オプションのプランを有効にする

1. Security Center のメニューで、 **[価格と設定]** ページを開きます。

    - **Azure Security Center の既定のワークスペース** (名前は "defaultworkspace-[サブスクリプション ID]-[リージョン]") を使用している場合は、該当する **サブスクリプション** を選択します。

    - **既定以外のワークスペース** を使用している場合は、該当する **ワークスペース** を選択します (必要に応じて、フィルターにワークスペースの名前を入力します)。

        ![既定以外のワークスペースをタイトルで検索する](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. **Azure Defender for SQL servers on machines** プランのオプションを **オン** に設定します。 

    :::image type="content" source="./media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png" alt-text="オプションのプランを含む Security Center の価格ページ":::

    選択したワークスペースに接続されているすべての SQL サーバーでプランが有効になります。 この保護は、SQL Server インスタンスの最初の再起動後に完全にアクティブになります。

    >[!TIP] 
    > 新しいワークスペースを作成するには、[Log Analytics ワークスペースの作成](../azure-monitor/logs/quick-create-workspace.md)に関する記事の手順に従います。


1. 必要に応じて、セキュリティ アラートのメール通知を構成します。 
    Security Center アラートの生成時にメール通知を受け取る受信者の一覧を設定できます。 メールには、Azure Security Center におけるアラートの直接リンクと関連するすべての詳細が含まれます。 詳細については、[セキュリティ アラートの電子メール通知のセットアップ](security-center-provide-security-contact-details.md)に関する記事を参照してください。


## <a name="azure-defender-for-sql-alerts"></a>Azure Defender for SQL のアラート
普段は見られない、潜在的に有害な SQL マシンへのアクセスが試行されると、あるいは SQL マシンの悪用が試行されると、アラートが生成されます。 これらのイベントにより、[アラートのリファレンス ページ](alerts-reference.md#alerts-sql-db-and-warehouse)に記載されているアラートがトリガーされる場合があります。

## <a name="explore-and-investigate-security-alerts"></a>セキュリティ通知について調査する

Azure Defender for SQL のアラートは、Security Center のアラート ページ、リソースのセキュリティ タブ、[Azure Defender ダッシュボード](azure-defender-dashboard.md)、またはアラート メールの直接リンクで使用できます。

1. アラートを表示するには、Security Center のメニューで **[セキュリティ アラート]** を選択し、アラートを選択します。

1. アラートは自己完結型であり、それぞれに詳細な修復の手順と調査情報が含まれています。 他の Azure Security Center と Azure Sentinel の機能を使用してさらに詳しく調べることができます。

    * 詳しく調べるには SQL Server の監査機能を有効にします。 Azure Sentinel ユーザーの場合は、Windows セキュリティ ログのイベントから Sentinel に SQL 監査ログをアップロードして、豊富な調査機能を利用できます。 [SQL Server 監査の詳細についてはこちらを参照してください](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15)。
    * セキュリティ体制を強化するには、各アラートに示されているホスト マシンに関する Security Center の推奨事項を使用します。 これにより、将来の攻撃のリスクが軽減されます。 

    [アラートの管理と応答の詳細についてはこちらを参照してください](security-center-managing-and-responding-alerts.md)。


## <a name="next-steps"></a>次のステップ

関連資料については、次の記事をご覧ください。

- [SQL Database と Azure Synapse Analytics のセキュリティ アラート](alerts-reference.md#alerts-sql-db-and-warehouse)
- [セキュリティ アラートのメール通知を設定する](security-center-provide-security-contact-details.md)
- [Azure Sentinel に関する詳細](../sentinel/index.yml)