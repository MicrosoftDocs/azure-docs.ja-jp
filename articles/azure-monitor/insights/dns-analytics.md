---
title: Azure Monitor の DNS Analytics ソリューション | Microsoft Docs
description: Azure Monitor の DNS Analytics ソリューションをセットアップして使用し、DNS インフラストラクチャのセキュリティ、パフォーマンス、操作に関する分析情報を収集します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: magoedte
ms.openlocfilehash: 6dd5872d5ec3e79e3c76b1807aea946015fb0eac
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521025"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>DNS 分析プレビュー ソリューションを使用した DNS インフラストラクチャに関する洞察の収集

![DNS 分析のシンボル](./media/dns-analytics/dns-analytics-symbol.png)

この記事では、Azure Monitor の Azure DNS Analytics ソリューションをセットアップして使用し、DNS インフラストラクチャのセキュリティ、パフォーマンス、操作に関する分析情報を収集する方法について説明します。

DNS 分析により、次のことができます。

- 悪意のあるドメイン名を解決しようとしているクライアントを特定する。
- 古いリソース レコードを特定する。
- クエリ対象になる頻度が高いドメイン名と話好きな DNS クライアントを特定する。
- DNS サーバーの要求負荷を確認する。
- 動的 DNS 登録エラーを確認する。

DNS 分析ソリューションは、Windows DNS の分析ログと監査ログおよび他の関連データを DNS サーバーから収集して分析し、関連付けます。

## <a name="connected-sources"></a>接続先ソース

次の表では、このソリューションでサポートされている接続先ソースについて説明します。

| **接続先ソース** | **サポート** | **説明** |
| --- | --- | --- |
| [Windows エージェント](../platform/agent-windows.md) | はい | ソリューションでは、Windows エージェントから DNS 情報を収集します。 |
| [Linux エージェント](../learn/quick-collect-linux-computer.md) | いいえ  | ソリューションでは、ダイレクト Linux エージェントから DNS 情報は収集しません。 |
| [System Center Operations Manager 管理グループ](../platform/om-agents.md) | はい | ソリューションでは、接続された Operations Manager 管理グループ内のエージェントから DNS 情報が収集されます。 Operations Manager エージェントから Azure Monitor への直接接続は必要ありません。 データは管理グループから Log Analytics ワークスペースに転送されます。 |
| [Azure Storage アカウント](../platform/collect-azure-metrics-logs.md) | いいえ  | ソリューションでは、Azure Storage は使用されません。 |

### <a name="data-collection-details"></a>データ収集の詳細

DNS 分析ソリューションでは、Log Analytics エージェントがインストールされている DNS サーバーから DNS インベントリと DNS イベント関連データを収集します。 その後このデータが Azure Monitor にアップロードされ、ソリューション ダッシュボードに表示されます。 インベントリ関連データ (DNS サーバーの数、ゾーンの数、リソース レコードの数など) は、DNS PowerShell コマンドレットを実行して収集します。 データは 2 日に 1 回更新されます。 イベント関連データは、Windows Server 2012 R2 の強化された DNS ログと診断機能によって提供される[分析ログと監査ログ](https://technet.microsoft.com/library/dn800669.aspx#enhanc)からほぼリアルタイムで収集されます。

## <a name="configuration"></a>構成

次の情報を使用して、ソリューションを構成します。

- 監視対象の各 DNS サーバーに [Windows](../platform/agent-windows.md) エージェントまたは [Operations Manager](../platform/om-agents.md) エージェントが必要です。
- [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace) から、DNS Analytics ソリューションを Log Analytics ワークスペースに追加できます。 [Solutions Gallery からの Azure Monitor ソリューションの追加](solutions.md)に関するページで説明されている手順も使用できます。

さらに構成を行わなくても、ソリューションはデータの収集を開始します。 ただし、次の構成を使用してデータ収集をカスタマイズできます。

### <a name="configure-the-solution"></a>ソリューションの構成

ソリューション ダッシュボードで **[構成]** をクリックして、[DNS 分析構成] ページを開きます。 次の 2 種類の構成変更を行うことができます。

- **ホワイトリストに含まれるドメイン名**。 ソリューションでは、すべてのルックアップ クエリが処理されるわけではありません。 ドメイン名サフィックスのホワイトリストが保持されています。 このホワイトリストのドメイン名のサフィックスと一致するドメイン名を解決するルックアップ クエリは、ソリューションでは処理されません。 ホワイトリストに含まれるドメイン名を処理しないことで、Azure Monitor に送信されるデータを最適化できます。 既定のホワイトリストには、よく使用されるパブリック ドメイン名 (www.google.com や www.facebook.com など) が含まれています。 スクロールすると、既定のリスト全体を確認できます。

  このリストを変更して、ルックアップの洞察の対象にするドメイン名のサフィックスを追加できます。 ルックアップの洞察の対象にしないドメイン名のサフィックスを削除することもできます。

- **話好きなクライアントのしきい値**。 ルックアップ要求数のしきい値を超えた DNS クライアントは、**[DNS クライアント]** ブレードで強調表示されます。 既定のしきい値は 1,000 です。 しきい値は編集できます。

    ![ホワイトリストに含まれるドメイン名](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>管理パック

Microsoft Monitoring Agent を使用して Log Analytics ワークスペースに接続すると、次の管理パックがインストールされます。

- Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.Dns)

Operations Manager 管理グループが Log Analytics ワークスペースに接続されている場合は、このソリューションを追加したときに次の管理パックが Operations Manager にインストールされます。 これらの管理パックの構成や保守は必要ありません。

- Microsoft DNS Data Collector Intelligence Pack (Microsoft.IntelligencePacks.Dns)
- Microsoft System Center Advisor DNS Analytics Configuration (Microsoft.IntelligencePack.Dns.Configuration)

ソリューション管理パックの更新方法の詳細については、「 [Operations Manager を Log Analytics に接続する](../platform/om-agents.md)」を参照してください。

## <a name="use-the-dns-analytics-solution"></a>DNS 分析ソリューションを使用する

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


DNS タイルには、データ収集中の DNS サーバーの数が含まれています。 また、過去 24 時間以内の、悪意のあるドメインを解決しようとしているクライアントからの要求の数も示されています。 タイルをクリックすると、ソリューション ダッシュボードが開きます。

![[DNS 分析] タイル](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>ソリューションのダッシュボード

ソリューション ダッシュボードには、ソリューションのさまざまな機能の集計情報が表示されます。 また、フォレンジック分析と診断の詳細ビューへのリンクも含まれています。 既定では、過去 7 日間のデータが表示されます。 次の図に示すように、**日時選択コントロール**を使用して日付と時間の範囲を変更できます。

![日時選択コントロール](./media/dns-analytics/dns-time.png)

ソリューション ダッシュボードには、以下のブレードが表示されます。

**[DNS セキュリティ]**。 悪意のあるドメインと通信しようとしている DNS クライアントを報告します。 DNS 分析では、Microsoft 脅威インテリジェンス フィードを使用して、悪意のあるドメインにアクセスしようとしているクライアント IP を検出できます。 多くの場合、マルウェアに感染したデバイスは、マルウェア ドメイン名を解決することで、悪意のあるドメインの "コマンド アンド コントロール" センターに "ダイヤル アウト" します。

![[DNS セキュリティ] ブレード](./media/dns-analytics/dns-security-blade.png)

一覧のクライアント IP をクリックすると、ログ検索が開き、それぞれのクエリのルックアップの詳細が表示されます。 次の例では、[IRCbot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot) との通信が行われたことが DNS 分析によって検出されています。

![IRCbot を示すログ検索結果](./media/dns-analytics/ircbot.png)

この情報により、次のことを確認できます。

- 通信を開始したクライアント IP。
- 悪意のある IP を解決しようとしているドメイン名。
- そのドメイン名の解決先の IP アドレス。
- 悪意のある IP アドレス。
- 問題の重大度。
- 悪意のある IP をブラックリストに含める理由。
- 検出時刻。

**[クエリ対象のドメイン]**。 環境内の DNS クライアントによるクエリの対象になる頻度が最も高いドメイン名が表示されます。 クエリ対象のすべてのドメイン名の一覧を確認できます。 また、ログ検索で特定のドメイン名のルックアップ要求の詳細にドリルダウンできます。

![[クエリ対象のドメイン] ブレード](./media/dns-analytics/domains-queried-blade.png)

**[DNS クライアント]**。 選択した期間内のクエリの数に対して*しきい値に違反した*クライアントを報告します。 すべての DNS クライアントの一覧を表示し、ログ検索で各クライアントによって実行されたクエリの詳細を確認できます。

![[DNS クライアント] ブレード](./media/dns-analytics/dns-clients-blade.png)

**[動的 DNS 登録]**。 名前登録エラーを報告します。 アドレス [リソース レコード](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (タイプ A および AAAA) のすべての登録エラーが、その登録要求を行ったクライアント IP と共に強調表示されます。 この情報を使用して、次の手順に従って登録エラーの根本原因を見つけることができます。

1. クライアントが更新しようとしている名前に対して権限のあるゾーンを見つけます。

1. ソリューションを使用して、そのゾーンのインベントリ情報を確認します。

1. ゾーンの動的更新が有効になっていることを確認します。

1. ゾーンがセキュリティで保護された動的更新を使用するように構成されているかどうかを確認します。

    ![[動的 DNS 登録] ブレード](./media/dns-analytics/dynamic-dns-reg-blade.png)

**[名前登録要求]**。 上のタイルには、成功および失敗した DNS 動的更新要求のトレンドラインが表示されます。 下のタイルには、失敗した DNS 更新要求を DNS サーバーに送信している上位 10 個のクライアントが、失敗の回数で並べ替えられて表示されます。

![[名前登録要求] ブレード](./media/dns-analytics/name-reg-req-blade.png)

**[DDI 分析のサンプル クエリ]**。 生の分析データを直接取得する最も一般的な検索クエリの一覧が含まれています。


![サンプル クエリ](./media/dns-analytics/queries.png)

これらのクエリは、カスタム レポート用の独自のクエリを作成するための出発点として使用できます。 クエリはクエリ結果が表示される DNS 分析ログ検索のページにリンクしています。

- **[DNS サーバーの一覧]**。 すべての DNS サーバーの一覧が表示されます。この一覧には、関連付けられている FQDN、ドメイン名、フォレスト名、サーバー IP などの情報が含まれています。
- **[DNS ゾーンの一覧]**。 すべての DNS ゾーンの一覧が表示されます。この一覧には、関連付けられているゾーン名、動的更新の状態、ネーム サーバー、DNSSEC 署名の状態などの情報が含まれています。
- **[使用されていないリソース レコード]**。 未使用か古いリソース レコードの一覧がすべて表示されます。 この一覧には、リソース レコード名、リソース レコードの種類、関連付けられている DNS サーバー、レコードの生成時刻、ゾーン名が含まれています。 この一覧を使用して、使用されなくなった DNS リソース レコードを特定できます。 この情報に基づいて、DNS サーバーからこれらのエントリを削除できます。
- **[DNS サーバーのクエリ負荷]**。 DNS サーバーの DNS 負荷の全体像を把握できる情報が表示されます。 この情報はサーバーの容量計画に役立ちます。 **[メトリック]** タブに移動すると、ビューをグラフ表示に変更できます。 このビューは、DNS サーバー間で DNS 負荷がどのように分散されているかを理解する上で役立ちます。 ビューには、各サーバーの DNS クエリ レートの傾向が示されます。

    ![DNS サーバーのクエリのログ検索の結果](./media/dns-analytics/dns-servers-query-load.png)

- **[DNS ゾーンのクエリ負荷]**。 ソリューションによって管理されている DNS サーバーのすべてのゾーンに関する 1 秒あたりの DNS ゾーン クエリ統計情報が表示されます。 **[メトリック]** タブをクリックすると、詳細レコードから結果のグラフ表示にビューが変更されます。
- **[構成イベント]**。 すべての DNS 構成変更イベントと、関連するメッセージが表示されます。 イベントの時刻、イベント ID、DNS サーバー、またはタスク カテゴリに基づいて、これらのイベントをフィルター処理できます。 このデータを使用して、特定の時点で特定の DNS サーバーに加えられた変更を監査できます。
- **[DNS 分析ログ]**。 ソリューションによって管理されているすべての DNS サーバーのすべての分析イベントが表示されます。 イベントの時刻、イベント ID、DNS サーバー、ルックアップ クエリを実行したクライアント IP、クエリの種類のタスク カテゴリに基づいて、これらのイベントをフィルター処理できます。 DNS サーバー分析イベントにより、DNS サーバーで動作状況の追跡が可能になります。 サーバーが DNS 情報を送受信するたびに、分析イベントがログに記録されます。

### <a name="search-by-using-dns-analytics-log-search"></a>DNS 分析ログ検索を使用して検索する

[ログ検索] ページで、クエリを作成できます。 ファセット コントロールを使用して検索結果をフィルター処理できます。 検索結果に対して変換やフィルター処理、レポート作成などを実行する高度なクエリを作成することもできます。 まず、以下のクエリを使用します。

1. **検索クエリ ボックス**に「`DnsEvents`」と入力して、ソリューションによって管理されている DNS サーバーで生成されたすべての DNS イベントを表示します。 結果には、ルックアップ クエリ、動的登録、構成変更に関連するすべてのイベントのログ データが表示されます。

    ![DnsEvents ログ検索](./media/dns-analytics/log-search-dnsevents.png)  

    a. ルックアップ クエリのログ データを表示するには、左側のファセット コントロールから **[LookUpQuery]** を選択して **[サブタイプ]** フィルター処理します。 選択した期間のすべてのルックアップ クエリ イベントが一覧になったテーブルが表示されます。

    b. 動的登録のログ データを表示するには、左側のファセット コントロールから **[DynamicRegistration]** を選択して **[サブタイプ]** をフィルター処理します。 選択した期間のすべての動的登録イベントが一覧になったテーブルが表示されます。

    c. 構成変更のログ データを表示するには、左側のファセット コントロールから **[ConfigurationChange]** を選択して **[サブタイプ]** をフィルター処理します。 選択した期間の構成変更イベントが一覧になったテーブルが表示されます。

1. **検索クエリ ボックス**に「`DnsInventory`」と入力して、ソリューションによって管理されている DNS サーバーのすべての DNS インベントリ関連データを表示します。 結果には、DNS サーバー、DNS ゾーン、リソース レコードのログ データが表示されます。

    ![DnsInventory ログ検索](./media/dns-analytics/log-search-dnsinventory.png)

## <a name="feedback"></a>フィードバック

フィードバックを送信する方法は 2 つあります。

- **UserVoice**。 DNS 分析の機能についてアイデアを投稿できます。 [Log Analytics に関する UserVoice ページ](https://aka.ms/dnsanalyticsuservoice)をご覧ください。
- **コーホートへの参加**。 Microsoft が実施しているコーホートに参加して、DNS 分析の機能向上にご協力ください。コーホートに参加すると、新機能にいち早く触れることができます。 コーホートへの参加に関心がある場合は、[こちらの簡単なアンケート](https://aka.ms/dnsanalyticssurvey)にご記入ください。

## <a name="next-steps"></a>次の手順

[ログをクエリ](../log-query/log-query-overview.md)して、詳細な DNS ログ レコードを確認します。
