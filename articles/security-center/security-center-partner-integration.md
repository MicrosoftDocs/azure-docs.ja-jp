---
title: Azure Security Center でのセキュリティ ソリューションの統合 | Microsoft Docs
description: Azure Security Center とパートナーの統合によって Azure リソースの全体的なセキュリティを強化する方法について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 0a3bc6bcae2f06173cbc334ffe80e2dfa001e407
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309263"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Azure Security Center でのセキュリティ ソリューションの統合
このドキュメントは、既に Azure Security Center に接続されているセキュリティ ソリューションを管理したり、新しいセキュリティ ソリューションを追加したりする際に役立ちます。

> [!NOTE]
> セキュリティ ソリューションのサブセットは、2019 年 7 月 31 日に廃止されました。 詳細および代替サービスについては、「[Security Center の機能の廃止 (2019 年 7 月)](security-center-features-retirement-july2019.md#menu_solutions)」を参照してください。

## <a name="integrated-azure-security-solutions"></a>統合された Azure セキュリティ ソリューション
Security Center を使用すると、Azure で統合されたセキュリティ ソリューションを簡単に有効にすることができます。 利点は次のとおりです。

- **簡略化されたデプロイ**:Security Center により、統合されたパートナー ソリューションのプロビジョニングが簡略化されます。 マルウェア対策や脆弱性評価などのソリューションについては、Security Center によって仮想マシンにエージェントをプロビジョニングできます。 ファイアウォール アプライアンスについては、Security Center で、必要なネットワーク構成の多くに対処できます。
- **統合された検出機能**:パートナー ソリューションのセキュリティ イベントは、Security Center の警告とインシデントの一環として自動的に収集、集計、表示されます。 また、これらのイベントは、他のソースからの検出とも組み合わされ、高度な脅威検出機能を提供します。
- **統合された正常性の監視と管理**:ユーザーは、統合された正常性イベントを使用して、すべてのパートナー ソリューションをひとめで監視できます。 基本的な管理は、パートナー ソリューションを使用して高度な設定に簡単にアクセスできれば、使用できます。

現時点では、統合セキュリティ ソリューションに、[Qualys](https://www.qualys.com/public-cloud/#azure) と [Rapid7](https://www.rapid7.com/products/insightvm/) による脆弱性評価と Microsoft Application Gateway Web アプリケーション ファイアウォールが含まれます。

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

**[セキュリティ ソリューション]** では、統合された Azure セキュリティ ソリューションの正常性を表示し、基本的な管理タスクを実行できます。

### <a name="connected-solutions"></a>接続済みソリューション

**[接続済みソリューション]** セクションには、Security Center に現在接続されているセキュリティ ソリューションが含まれます。 また、各ソリューションの正常性状態も表示されます。  

![接続済みソリューション](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

パートナー ソリューションの状態は、次のいずれかになります。

* 正常 (緑) - 正常性の問題はありません。
* 異常 (赤) - 早急な対処が必要な正常性の問題があります。
* 正常性の問題 (オレンジ) - ソリューションが正常性の報告を停止しています。
* レポートなし (灰色) - ソリューションがまだ何も報告しておらず、正常性データはありません。 最近接続されて、まだデプロイ中の場合、ソリューションの状態が報告されない可能性があります。

> [!NOTE]
> 正常性状態データを使用できない場合、Security Center には、最後に受信したイベントの日時が表示され、ソリューションが報告を行っているかどうかを示します。 使用できる正常性データがなく、過去 14 日以内にアラートを受信していなかった場合、Security Center は、ソリューションで異常が発生しているか、報告が行われていないことを示します。
>
>

1. **[表示]** を選択すると、次のような追加情報とオプションを確認できます。

   - **ソリューション コンソール**。 このソリューションの管理エクスペリエンスが開きます。
   - **VM をリンクする**。 [アプリケーションのリンク] ページが開きます。 ここで、パートナー ソリューションにリソースを接続できます。
   - **ソリューションの削除**。
   - **構成**。

   ![パートナー ソリューションの詳細](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>検出されたソリューション

Security Center は、Azure で実行されていても Security Center に接続されていないセキュリティ ソリューションを自動的に検出し、 **[検出されたソリューション]** セクションにそのソリューションを表示します。 これらのソリューションには、[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) などの Azure ソリューションや、パートナー ソリューションが含まれます。

> [!NOTE]
> 検出されたソリューション機能のサブスクリプション レベルで、Security Center の Standard レベルが必要です。 価格レベルの詳細については、[価格](security-center-pricing.md)に関するページを参照してください。
>
>

ソリューションの下の **[接続]** を選択して、Security Center と統合し、セキュリティのアラートが通知されるようにします。

![検出されたソリューション](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

### <a name="add-data-sources"></a>データ ソースの追加

**[データ ソースの追加]** セクションには、接続できるその他の使用可能なデータ ソースが表示されます。 このようなソースのいずれかからデータを追加する手順については、 **[追加]** をクリックしてください。

![データ ソース](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>SIEM へのデータのエクスポート

Azure Security Center イベントを受信するように SIEM またはその他の監視ツールを構成できます。

Azure Security Center からのすべてのイベントは、Azure Monitor の Azure [アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)に発行されます。 Azure Monitor は、[統合されたパイプライン](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)を使用して、データをイベント ハブにストリーミングします。そこから、それを監視ツールに取り込むことができます。

以降のセクションでは、イベント ハブにストリーミングされるようにデータを構成する方法について説明します。 以下の手順では、Azure Security Center が Azure サブスクリプションで既に構成されているものとします。

### <a name="high-level-overview"></a>概要

![概要](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>SIEM に公開される Azure セキュリティ データ

このバージョンでは、[セキュリティのアラート](../security-center/security-center-managing-and-responding-alerts.md)を公開します。 今後のリリースでは、セキュリティに関する推奨事項でデータ セットを増やします。

### <a name="how-to-set-up-the-pipeline"></a>パイプラインを設定する方法

#### <a name="create-an-event-hub"></a>Event Hub を作成する

開始する前に、すべての監視データの送信先である [Event Hubs 名前空間を作成します](../event-hubs/event-hubs-create.md)。

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Event Hubs への Azure アクティビティ ログのストリーミング

[アクティビティ ログの Event Hubs へのストリーム](../azure-monitor/platform/activity-logs-stream-event-hubs.md)に関する記事を参照してください

#### <a name="install-a-partner-siem-connector"></a>パートナー SIEM コネクタをインストールする 

Azure Monitor で監視データを Event Hub にルーティングすると、パートナー SIEM や監視ツールに簡単に統合することができます。

[サポートされる SIEM](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-resource-logs-sent-to-an-event-hub) の一覧に関する記事を参照してください

### <a name="example-for-querying-data"></a>データのクエリの例 

アラート データの取得に使用できる Splunk クエリを次に示します。

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
