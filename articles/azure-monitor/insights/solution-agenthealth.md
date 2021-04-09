---
title: Azure Monitor における Agent Health ソリューション | Microsoft Docs
description: この記事の目的は、Log Analytics または System Center Operations Manager に対して直接報告を行うエージェントの正常性を Agent Health ソリューションで監視する方法についてわかりやすく説明することです。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: 171230dc2ce6189e36c601c6c7d3b3612fce160c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711061"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Azure Monitor での Agent Health ソリューション
Azure において Agent Health ソリューションを使用すると、Azure Monitor の Log Analytics ワークスペースに対して、または Azure Monitor に接続された System Center Operations Manager 管理グループに対して直接報告を行うすべてのエージェントの中で、応答していないエージェントと運用データを送信しているエージェントを把握するのに役立ちます。  また、デプロイされているエージェントの数や地理的な分布を追跡できるほか、Azure を初めとする各種クラウド環境やオンプレミスにデプロイされているエージェントの分布を把握するためのその他のクエリを実行することができます。    

## <a name="prerequisites"></a>前提条件
このソリューションをデプロイする前に、現在サポートされている [Windows エージェント](../agents/agent-windows.md)が、Log Analytics ワークスペースに対して、またはワークスペースに統合されている [Operations Manager 管理グループ](../agents/om-agents.md)に対して報告を行っていることを確認してください。

## <a name="solution-components"></a>ソリューションのコンポーネント
このソリューションは次のリソースで構成されています。これらのリソースは、ワークスペースに追加され、エージェントに直接接続されるか、Operations Manager に接続された管理グループに接続されます。

### <a name="management-packs"></a>管理パック
System Center Operations Manager 管理グループが Log Analytics ワークスペースに接続されている場合は、以下の管理パックが Operations Manager にインストールされます。  これらの管理パックは、このソリューションを追加した後、直接接続された Windows コンピューターにもインストールされます。 管理パックに伴って構成や管理が必要となるものはありません。

* Microsoft System Center Advisor HealthAssessment Direct Channel インテリジェンス パック (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel インテリジェンス パック (Microsoft.IntelligencePacks.HealthAssessmentViaServer)  

ソリューション管理パックの更新方法の詳細については、「 [Operations Manager を Log Analytics に接続する](../agents/om-agents.md)」を参照してください。

## <a name="configuration"></a>構成
[ソリューションの追加](solutions.md)に関するページの手順に従って、Agent Health ソリューションを Log Analytics ワークスペースに追加します。 さらに手動で構成する必要はありません。


## <a name="data-collection"></a>データ コレクション
### <a name="supported-agents"></a>サポートされているエージェント
次の表は、このソリューションの接続先としてサポートされているソースとその説明です。

| 接続先ソース | サポートされています | 説明 |
| --- | --- | --- |
| Windows エージェント | はい | ハートビート イベントは、直接の Windows エージェントから収集されます。|
| System Center Operations Manager 管理グループ | はい | ハートビート イベントは、管理グループに対して報告を行うエージェントから 60 秒ごとに収集されて、Azure Monitor に転送されます。 Operations Manager エージェントから Azure Monitor への直接接続は必要ありません。 ハートビート イベント データは管理グループから Log Analytics ワークスペースに転送されます。|

## <a name="using-the-solution"></a>ソリューションの使用
Log Analytics ワークスペースに Agent Health ソリューションを追加すると、ダッシュボードに **[Agent Health]** タイルが追加されます。 このタイルには、エージェントの総数と直近 24 時間応答していないエージェントの数が表示されます。<br><br> ![ダッシュボードの Agent Health ソリューション タイル](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

**[Agent Health]** タイルをクリックすると、**[Agent Health]** ダッシュボードが開きます。  ダッシュボードには、次の表に示した列が存在します。 それぞれの列には、特定の時間の範囲について、その列の基準に該当するイベント数の上位 10 件が表示されます。 ログ検索を実行してイベント全件を取得するには、各列の右下にある **[すべて表示]** を選択するか、列ヘッダーをクリックします。

| 列 | 説明 |
|--------|-------------|
| Agent count over time (時間の経過に伴うエージェント数) | 7 日間にわたるエージェント数の傾向。Linux エージェントと Windows エージェントの両方が対象となります。|
| Count of unresponsive agents (応答しないエージェントの数) | 過去 24 時間ハートビートを送信していないエージェントの一覧。|
| OS の種類ごとの分布 | 対象の環境に存在する Windows エージェントと Linux エージェントの区分。|
| Distribution by Agent Version (エージェントのバージョンごとの分布) | 対象の環境にインストールされている各種エージェント バージョンの区分と各バージョンの数。|
| Distribution by Agent Category (エージェントのカテゴリごとの分布) | ハートビート イベントを送信するエージェントの各カテゴリの区分 (ダイレクト エージェント、OpsMgr エージェント、または OpsMgr 管理サーバー)。|
| Distribution by Management Group (管理グループごとの分布) | 対象の環境に存在する各種 Operations Manager 管理グループの区分。|
| エージェントの地理的な位置 | エージェントが存在する国や地域の区分と、それぞれの国や地域でインストールされているエージェントの総数。|
| Count of Gateways Installed (インストールされたゲートウェイの数) | Log Analytics ゲートウェイがインストールされているサーバーの数とその一覧。|

![Agent Health ソリューション ダッシュボードの例](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Azure Monitor のログ レコード
このソリューションによって Log Analytics ワークスペースに作成されるレコードの種類は 1 つです。  

### <a name="heartbeat-records"></a>ハートビート レコード
**Heartbeat** という種類のレコードが作成されます。  これらのレコードは、次の表に示したプロパティを持ちます。  

| プロパティ | 説明 |
| --- | --- |
| `Type` | *ハートビート*|
| `Category` | *Direct Agent*、*SCOM Agent*、*SCOM Management Server* のいずれかの値になります。|
| `Computer` | コンピューター名。|
| `OSType` | Windows または Linux オペレーティング システム。|
| `OSMajorVersion` | オペレーティング システムのメジャー バージョン。|
| `OSMinorVersion` | オペレーティング システムのマイナー バージョン。|
| `Version` | Log Analytics エージェントまたは Operations Manager エージェントのバージョン。|
| `SCAgentChannel` | *Direct* と *SCManagementServer* のいずれかの値になります。|
| `IsGatewayInstalled` | Log Analytics ゲートウェイがインストールされている場合、値は *true* です。それ以外の場合は *false* になります。|
| `ComputerIP` | コンピューターのパブリック IP アドレス。 Azure VM では、パブリック IP が使用可能な場合はそれが表示されます。 プライベート IP を使用している VM の場合、Azure SNAT アドレス (プライベート IP アドレスではありません) が表示されます。 |
| `RemoteIPCountry` | コンピューターがデプロイされている地理的位置。|
| `ManagementGroupName` | Operations Manager 管理グループの名前。|
| `SourceComputerId` | コンピューターの一意の ID。|
| `RemoteIPLongitude` | コンピューターの地理的位置の経度。|
| `RemoteIPLatitude` | コンピューターの地理的位置の緯度。|

Operations Manager 管理サーバーに対して報告を行う各エージェントからは 2 つのハートビートが送信され、サブスクリプション内で有効にしたデータ ソースと監視ソリューションによっては、SCAgentChannel プロパティが **Direct** および **SCManagementServer** の両方の値を取ります。 ソリューションからのデータは、Operations Manager 管理サーバーから Azure Monitor に直接送信される場合と、エージェントで収集されるデータのボリュームが原因で、エージェントから Azure Monitor に直接送信される場合とがあることを思い出してください。 この値が **SCManagementServer** であるハートビート イベントの場合、データは実質的に管理サーバーによってアップロードされるため、ComputerIP の値は、管理サーバーの IP アドレスになります。  SCAgentChannel が **Direct** に設定されているハートビートの場合は、エージェントのパブリック IP アドレスになります。  

## <a name="sample-log-searches"></a>サンプル ログ検索
次の表は、このソリューションによって収集されたレコードを探すログ検索の例です。

| クエリ | 説明 |
|:---|:---|
| Heartbeat &#124; distinct Computer |エージェントの総数 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |直近 24 時間応答がなかったエージェントの数 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |直近 15 分間応答がなかったエージェントの数 |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |(直近 24 時間) オンライン状態のコンピューター |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |(直近 24 時間のうち) 直近 30 分間オフライン状態であったエージェントの総数 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |時間の経過に伴うエージェント数の傾向を OSType ごとに取得|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |OS の種類ごとの分布 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Distribution by Agent Version (エージェントのバージョンごとの分布) |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Distribution by Agent Category (エージェントのカテゴリごとの分布) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Distribution by Management Group (管理グループごとの分布) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |エージェントの地理的な位置 |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |インストールされている Log Analytics ゲートウェイの数 |




## <a name="next-steps"></a>次のステップ

* ログ クエリからのアラートの生成について詳しくは、[Azure Monitor でのアラート](../alerts/alerts-overview.md)に関する記事をご覧ください。 

