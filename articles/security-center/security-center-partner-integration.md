---
title: Azure Security Center でのセキュリティ ソリューションの統合 | Microsoft Docs
description: Azure Security Center とパートナーの統合によって Azure リソースの全体的なセキュリティを強化する方法について説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: d94567800a9fd020784c9cb07b2c6824cd032509
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064273"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Azure Security Center でのセキュリティ ソリューションの統合
このドキュメントは、既に Azure Security Center に接続されているセキュリティ ソリューションを管理したり、新しいセキュリティ ソリューションを追加したりする際に役立ちます。

> [!NOTE]
> セキュリティ ソリューションのサブセットは、2019 年 6 月 31 日に廃止されます。 詳細および代替サービスについては、「[Security Center の機能の廃止 (2019 年 7 月)](security-center-features-retirement-july2019.md#menu_solutions)」を参照してください。

## <a name="integrated-azure-security-solutions"></a>統合された Azure セキュリティ ソリューション
Security Center を使用すると、Azure で統合されたセキュリティ ソリューションを簡単に有効にすることができます。 利点は次のとおりです。

- **簡略化されたデプロイ**:Security Center により、統合されたパートナー ソリューションのプロビジョニングが簡略化されます。 マルウェア対策や脆弱性評価のようなソリューションでは、Security Center は、仮想マシンで必要なエージェントをプロビジョニングできます。また、ファイアウォール アプライアンスでは、必要とされるネットワーク構成の多くに対処できます。
- **統合された検出機能**:パートナー ソリューションのセキュリティ イベントは、Security Center の警告とインシデントの一環として自動的に収集、集計、表示されます。 また、これらのイベントは、他のソースからの検出とも組み合わされ、高度な脅威検出機能を提供します。
- **統合された正常性の監視と管理**:ユーザーは、統合された正常性イベントを使用して、すべてのパートナー ソリューションをひとめで監視できます。 基本的な管理は、パートナー ソリューションを使用して高度な設定に簡単にアクセスできれば、使用できます。

現時点では、統合セキュリティ ソリューションに、[Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) と [Rapid7](https://www.rapid7.com/products/insightvm/) による脆弱性評価と Microsoft Application Gateway Web アプリケーション ファイアウォールが含まれます。

> [!NOTE]
> ほとんどのセキュリティ ベンダーがアプライアンス上での外部エージェントの実行を禁止しているため、Security Center はパートナー仮想アプライアンスに Microsoft Monitoring Agent をインストールしません。
>
>

## <a name="how-security-solutions-are-integrated"></a>セキュリティ ソリューションを統合するしくみ
Security Center からデプロイされている Azure セキュリティ ソリューションは自動的に接続されます。 オンプレミスまたは他のクラウドで実行されているコンピューターなど、その他のセキュリティ データ ソースも接続できます。

![パートナー ソリューションの統合](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>統合された Azure セキュリティ ソリューションとその他のデータ ソースの管理

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/) にサインインします。

2. **[Microsoft Azure] メニュー**の **[セキュリティ センター]** を選択します。 **[セキュリティ センター - 概要]** が開きます。

3. Security Center のメニューで、 **[セキュリティ ソリューション]** を選択します。

   ![Security Center の概要](./media/security-center-partner-integration/overview.png)

**[セキュリティ ソリューション]** では、統合された Azure セキュリティ ソリューションの正常性に関する情報を表示し、基本的な管理タスクを実行することができます。 また、共通イベント形式 (CEF) の Azure Active Directory Identity Protection のアラートやファイアウォール ログなど、他の種類のセキュリティ データ ソースを接続することもできます。

### <a name="connected-solutions"></a>接続済みソリューション

**[接続済みのソリューション]** セクションには、現在 Security Center に接続されているセキュリティ ソリューションと、各ソリューションの正常性状態に関する情報が表示されます。  

![接続済みソリューション](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

パートナー ソリューションの状態は、次のいずれかになります。

* 正常 (緑) - 正常性の問題はありません。
* 異常 (赤) - 早急に処置が必要な正常性の問題があります。
* 正常性の問題 (オレンジ) - ソリューションが正常性の報告を停止しています。
* レポートなし (灰色) - ソリューションがまだ何も報告していません。最近接続されたばかりでデプロイ中の場合、または使用できる正常性データがない場合は、ソリューションの状態が報告されていない可能性があります。

> [!NOTE]
> 正常性状態データを使用できない場合、Security Center には、最後に受信したイベントの日時が表示され、ソリューションが報告を行っているかどうかを示します。 使用できる正常性データがなく、過去 14 日以内にアラートを受信していない場合、Security Center は、ソリューションで異常が発生しているか、報告が行われていないことを示します。
>
>

1. **[表示]** を選択すると、次の追加情報とオプションを確認できます。

   - **ソリューション コンソール**。 このソリューションの管理エクスペリエンスが開きます。
   - **VM をリンクする**。 [アプリケーションのリンク] ブレードが開きます。 ここで、パートナー ソリューションにリソースを接続できます。
   - **ソリューションの削除**。
   - **構成**。

   ![パートナー ソリューションの詳細](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>検出されたソリューション

Security Center は、Azure で実行されていても Security Center に接続されていないセキュリティ ソリューションを自動的に検出し、 **[検出されたソリューション]** セクションにソリューションを表示します。 これには、[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) などの Azure ソリューションだけでなく、パートナー ソリューションも含まれます。

> [!NOTE]
> 検出されたソリューション機能のサブスクリプション レベルで、Security Center の Standard レベルが必要です。 セキュリティの価格レベルの詳細については、[価格](security-center-pricing.md)に関するページを参照してください。
>
>

ソリューションの下の **[接続]** を選択して、Security Center と統合し、セキュリティのアラートが通知されるようにします。

![検出されたソリューション](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center は、共通イベント形式 (CEF) のログを転送することができる、サブスクリプションにデプロイされたソリューションも検出します。 Security Center に、CEF ログを使用する[セキュリティ ソリューションを接続](quick-security-solutions.md)する方法を参照してください。

### <a name="add-data-sources"></a>データ ソースの追加

**[データ ソースの追加]** セクションには、接続できるその他の使用可能なデータ ソースが表示されます。 このようなソースのいずれかからデータを追加する手順については、 **[追加]** をクリックしてください。

![データ ソース](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>SIEM へのデータのエクスポート

Azure Security Center によって生成されて処理されたイベントは、Azure Monitor で利用可能なログの種類の 1 つである Azure [アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)に発行されます。 Azure Monitor では、任意の監視データを SIEM ツールにルーティングするための統合パイプラインが提供されています。 これは Event Hub にデータをストリーミングすることによって行われ、そこからはパートナー ツールに取得できます。

このパイプでは、Azure 環境から監視データにアクセスするために [Azure Monitoring の 1 つのパイプライン](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)が使われます。 これにより、データを使うように SIEM と監視ツールを簡単に設定できます。

以降のセクションでは、イベント ハブにストリーミングされるようにデータを構成する方法について説明します。 以下の手順では、Azure Security Center が Azure サブスクリプションで既に構成されているものとします。

概要

![概要](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>SIEM に公開される Azure セキュリティ データ

このバージョンでは、[セキュリティの警告](../security-center/security-center-managing-and-responding-alerts.md)を公開します。 今後のリリースでは、セキュリティに関する推奨事項でデータ セットを増やします。

### <a name="how-to-setup-the-pipeline"></a>パイプラインのセットアップ方法

#### <a name="create-an-event-hub"></a>Event Hub を作成する

始める前に、[Event Hubs 名前空間を作成する](../event-hubs/event-hubs-create.md)必要があります。 この名前空間と Event Hub が、すべての監視データの送信先です。

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Event Hubs への Azure アクティビティ ログのストリーミング

「[アクティビティ ログの Event Hubs へのストリーム](../azure-monitor/platform/activity-logs-stream-event-hubs.md)」をご覧ください

#### <a name="install-a-partner-siem-connector"></a>パートナー SIEM コネクタをインストールする 

Azure Monitor で監視データを Event Hub にルーティングすると、パートナー SIEM や監視ツールに簡単に統合することができます。

一サポートされる SIEM の一覧は、[こちらのリンク](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)をご覧ください

### <a name="example-for-querying-data"></a>データのクエリの例 

警告データの取得に使うことができる Splunk クエリを次に示します。

| **クエリの説明** | **クエリ** |
|----|----|
| すべての警告| index=main Microsoft.Security/locations/alerts|
| 名前を指定した操作の数の集計| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| 警告情報:日時、名前、状態、ID、サブスクリプション | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>次の手順

この記事では、Security Center でパートナー ソリューションを統合する方法について説明しました。 Security Center の詳細については、次の記事を参照してください。

* [Security Center でのセキュリティ正常性の監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center の FAQ](security-center-faq.md)。 Security Center の使用に関してよく寄せられる質問とその回答を紹介しています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
