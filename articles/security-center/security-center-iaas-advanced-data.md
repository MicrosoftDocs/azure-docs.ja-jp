---
title: Azure Security Center の Advanced Data Security for SQL machines (プレビュー)
description: Azure Security Center で Advanced Data Security for SQL machines を有効にする方法について説明します
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: e0085ef5213853a1577ec039d5e360114aa7c64e
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566269"
---
# <a name="advanced-data-security-for-sql-machines-preview"></a>Advanced Data Security for SQL machines (プレビュー)

Azure Security Center の Advanced Data Security for SQL machines により、Azure、他のクラウド環境、さらにはオンプレミスのマシンでホストされている SQL Server も保護されます。 これにより、Azure ネイティブの SQL Server の保護が拡張され、ハイブリッド環境を完全にサポートできるようになります。

このプレビュー機能には、データベースの潜在的な脆弱性を識別し、軽減する機能や、データベースに対する脅威を示す異常な行動を検出する機能が含まれています。 

* **脆弱性評価** - データベースの潜在的な脆弱性を検出、追跡し、修復を支援するスキャン サービス。 評価スキャンでは、SQL マシンのセキュリティ状態の概要と、セキュリティ調査結果の詳細が示されます。

* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) - SQL インジェクション、ブルートフォース攻撃、特権の悪用などの脅威について、SQL サーバーを継続的に監視する検出サービス。 このサービスでは、不審なアクティビティの詳細、脅威を軽減する方法に関するガイダンス、Azure Sentinel で調査を続行するためのオプションについて、Azure Security Center のアクション指向のセキュリティ アラートが提供されます。

>[!TIP]
> Advanced Data Security for SQL machines は Azure Security Center の [Advanced Data Security パッケージ](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)の拡張機能であり、Azure SQL Database、Azure Synapse、SQL Managed Instance で利用できます。


## <a name="set-up-advanced-data-security-for-sql-machines"></a>Advanced Data Security for SQL machines の設定 

Azure Security Center の Advanced Data Security for SQL machines の設定は、次の 2 つの手順で実行します。

* SQL サーバーのホストで Log Analytics エージェントをプロビジョニングします。 これにより、Azure への接続が提供されます。

* Security Center の [価格と設定] ページで、オプションのバンドルを有効にします。

これらの両方について以下に説明します。

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>手順 1. SQL サーバーのホストで Log Analytics エージェントをプロビジョニングする

- **Azure VM 上の SQL Server** - SQL マシンが Azure VM でホストされている場合は、[Log Analytics エージェントを自動プロビジョニングする](security-center-enable-data-collection.md#workspace-configuration)ことができます。 または、手動の手順に従って [Azure VM を追加する](quick-onboard-azure-stack.md#add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines)こともできます。

- **Azure Arc 上の SQL Server** - SQL Server が [Azure Arc](https://docs.microsoft.com/azure/azure-arc/) マシンでホストされている場合は、Security Center のレコメンデーション [Log Analytics agent should be installed on your Windows-based Azure Arc machines (Preview)] (Log Analytics エージェントを Windows ベースの Azure Arc マシンにインストールする必要があります (プレビュー)) を使用して、Log Analytics エージェントをデプロイできます。 または、[Azure Arc のドキュメント](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal)の手動の手順に従うこともできます。

- **オンプレミスの SQL Server** - SQL Server が Azure Arc を使用しないオンプレミスの Windows マシンでホストされている場合は、Azure に接続するためのオプションが 2 つあります。
    
    - **Azure Arc をデプロイする** - 任意の Windows マシンを Security Center に接続できます。 ただし、Azure Arc を使用すると、Azure 環境の*すべて*がより緊密に統合されます。 Azure Arc を設定すると、ポータルに **[SQL Server - Azure Arc]** ページが表示され、セキュリティ アラートはそのページの専用の **[セキュリティ]** タブに表示されます。 そのため、最初に推奨されるオプションは、[ホストで Azure Arc を設定](https://docs.microsoft.com/azure/azure-arc/servers/onboard-portal#install-and-validate-the-agent-on-windows)し、前述の **Azure Arc 上の SQL Server** の手順に従うことです。
        
    - **Azure Arc を使用せずに Windows マシンを接続する** - Azure Arc を使用せずに Windows マシンで実行されている SQL Server を接続する場合は、「[Windows コンピューターを Azure Monitor に接続する](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)」の手順に従ってください。


### <a name="step-2-enable-the-optional-bundle-in-security-centers-pricing-and-settings-page"></a>手順 2. Security Center の [価格と設定] ページで、オプションのバンドルを有効にする

1. Security Center のサイドバーで、 **[価格と設定]** ページを開きます。

    - **Azure Security Center の既定のワークスペース** (名前は "defaultworkspace-[サブスクリプション ID]-[リージョン]") を使用している場合は、該当する**サブスクリプション**を選択します。

    - **既定以外のワークスペース**を使用している場合は、該当する**ワークスペース**を選択します (必要に応じて、フィルターにワークスペースの名前を入力します)。

        ![title](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)


1. **[SQL servers on machines (Preview)] (マシン上の SQL サーバー (プレビュー))** のオプションを [有効] に切り替えます。 

    [![オプションのバンドルを含む Security Center の価格ページ](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    選択したワークスペースに接続されているすべての SQL サーバーで、マシン上の SQL サーバー向け Advanced Data Security が有効になります。 この保護は、SQL Server インスタンスの最初の再起動後に完全にアクティブになります。

    >[!TIP] 
    > 新しいワークスペースを作成するには、[Log Analytics ワークスペースの作成](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)に関する記事の手順に従います。


1. 必要に応じて、セキュリティ アラートのメール通知を構成します。 
    Security Center アラートの生成時にメール通知を受け取る受信者の一覧を設定できます。 メールには、Azure Security Center におけるアラートの直接リンクと関連するすべての詳細が含まれます。 詳細については、[セキュリティ アラートの電子メール通知のセットアップ](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)に関する記事を参照してください。



## <a name="explore-vulnerability-assessment-reports"></a>脆弱性評価レポートについて調べる

脆弱性評価サービスは、1 週間に 1 回データベースをスキャンします。 スキャンは、サービスを有効にした曜日と同じ曜日に実行されます。

脆弱性評価ダッシュボードには、すべてのデータベースにおける評価結果の概要、正常なデータベースと異常なデータベースの概要、およびリスク分散による失敗したチェックの概要が表示されます。

脆弱性評価の結果は、Security Center から直接表示することができます。

1. Security Center のサイドバーから **[推奨事項]** ページを開き、レコメンデーション **[Vulnerabilities on your SQL servers on machines should be remediated (Preview)] (マシン上の SQL サーバーの脆弱性を修復する必要があります (プレビュー))** を選択します。 詳細については、「[Security Center の推奨事項](security-center-recommendations.md)」を参照してください。 


    [![**マシン上の SQL サーバーの脆弱性を修復する必要があります (プレビュー)** レコメンデーション](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    このレコメンデーションの詳細ビューが表示されます。

    [![**マシン上の SQL サーバーの脆弱性を修復する必要があります (プレビュー)** レコメンデーションの詳細ビュー](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. 詳細を表示するには、次のようにドリルダウンします。

    * スキャンされたリソース (データベース) の概要と、テスト済みのセキュリティ チェックの一覧を表示するには、目的のサーバーを選択します。

    * 特定の SQL データベースごとにグループ化された脆弱性の概要を表示するには、目的のデータベースを選択します。

    各ビューでは、セキュリティ チェックが**重要度**順に表示されます。 特定のセキュリティ チェックをクリックすると詳細ウィンドウが表示され、**説明**、**修復方法**、およびその他の関連情報 (**影響**や**ベンチマーク**など) が表示されます。

## <a name="advanced-threat-protection-for-sql-servers-on-machines-alerts"></a>マシン上の SQL サーバー向け Advanced Threat Protection のアラート
普段は見られない、潜在的に有害な SQL マシンへのアクセスが試行されると、あるいは SQL マシンの悪用が試行されると、アラートが生成されます。 これらのイベントが発生すると、[アラート リファレンス ページの「SQL Database および SQL Data Warehouse のアラート」セクション](alerts-reference.md#alerts-sql-db-and-warehouse)で示されるアラートがトリガーされます。



## <a name="explore-and-investigate-security-alerts"></a>セキュリティ通知について調査する

セキュリティ アラートは、Security Center の [アラート] ページ、リソースの [セキュリティ] タブ、またはアラート メールの直接リンクで使用できます。

1. アラートを表示するには、Security Center のサイドバーで **[セキュリティ アラート]** を選択し、アラートを選択します。

1. アラートは自己完結型であり、それぞれに詳細な修復の手順と調査情報が含まれています。 他の Azure Security Center と Azure Sentinel の機能を使用してさらに詳しく調べることができます。

    * 詳しく調べるには SQL Server の監査機能を有効にします。 Azure Sentinel ユーザーの場合は、Windows セキュリティ ログのイベントから Sentinel に SQL 監査ログをアップロードして、豊富な調査機能を利用できます。 [SQL Server 監査の詳細についてはこちらを参照してください](https://docs.microsoft.com/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?view=sql-server-ver15)。
    * セキュリティ体制を強化するには、各アラートに示されているホスト マシンに関する Security Center の推奨事項を使用します。 これにより、将来の攻撃のリスクが軽減されます。 

    [アラートの管理と応答の詳細についてはこちらを参照してください](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。


## <a name="next-steps"></a>次のステップ

関連資料については、次の記事をご覧ください。

- [SQL Database および SQL Data Warehouse のセキュリティ アラート](alerts-reference.md#alerts-sql-db-and-warehouse)
- [セキュリティ アラートのメール通知を設定する](security-center-provide-security-contact-details.md)
- [Azure Sentinel に関する詳細](https://docs.microsoft.com/azure/sentinel/)
- [Azure Security Center の Advanced Data Security パッケージ](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)