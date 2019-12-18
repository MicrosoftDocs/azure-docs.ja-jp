---
title: Security Center の機能の廃止 (2019 年 7 月) | Microsoft Docs
description: この記事では、2019 年 7 月 31 日に終了した Security Center の機能について説明します。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: bb8cfae73d84135858733899e6441f564106e9d1
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997226"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Security Center の機能の廃止 (2019 年 7 月)

> [!NOTE]
> このドキュメントでは、2019 年 7 月 31 日に Azure Security Center から廃止された機能を一覧に示して詳しく説明します。
>
>

マイクロソフトでは、2019 年 7 月まで 6 か月にわたって、Azure Security Center に対していくつかの[強化策](https://azure.microsoft.com/updates/?product=security-center)を実施しました。
これらの機能強化に伴い、2019 年 7 月 31 日をもって、いくつの冗長な機能と関連する API を Security Center から削除しました。

これらの廃止された機能のほとんどは、Azure Security Center または Azure Log Analytics の他の機能で置き換えることができます。 その他の機能は、[Azure Sentinel (プレビュー)](https://azure.microsoft.com/services/azure-sentinel/) を使用して実装できます。

廃止された Security Center の機能は次のとおりです。

- [イベント ダッシュボード](#menu_events)
- [メニュー項目の検索](#menu_search)
- [[ID およびアクセス] にある [クラシック ID とアクセスの表示] (プレビュー)](#menu_classicidentity)
- [[セキュリティ アラート マップ] にある [セキュリティ イベント マップ] ボタン (プレビュー)](#menu_securityeventsmap)
- [カスタムのアラート ルール (プレビュー)](#menu_customalerts)
- [[脅威の防止] セキュリティ アラートにある [調査] ボタン](#menu_investigate)
- [セキュリティ ソリューションのサブセット](#menu_solutions)
- [セキュリティ ポリシーに対するセキュリティ構成の編集](#menu_securityconfigurations)
- [Log Analytics ワークスペースに対するセキュリティおよび監査のダッシュボード (元は OMS ポータルで使用されていた)](#menu_securityomsdashboard)

この記事では、廃止される各機能の詳細情報と、置き換えられる機能を実装するために実行できる手順を示します。

## イベント ダッシュボード<a name="menu_events"></a>

Security Center は、Microsoft Monitoring Agent を使用して、さまざまなセキュリティ関連の構成とイベントをマシンから収集します。 ワークスペースにこれらのイベントを格納します。 [イベント ダッシュボード](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard)はこのデータを表示し、Log Analytics へのエントリ ポイントを提供します。

ワークスペースを選択したときに表示されるイベント ダッシュボードは廃止されました。

![イベント ダッシュボード][2]

### <a name="events-dashboard---the-new-experience"></a>イベント ダッシュボード - 新しいエクスペリエンス

Azure Log Analytics のネイティブ機能を使用して、ワークスペース上で注目すべきイベントを閲覧することをお勧めします。

Security Center でカスタムの注目すべきイベントを作成済みの場合、それらは今後もアクセスできます。 Log Analytics で、 **[ワークスペースの選択]**  >  **[保存済み検索]** に移動します。 データは失われず、変更もされません。 ネイティブの注目すべきイベントも、Log Analytics の同じ画面から使用できます。

![保存された検索のワークスペース][3]

## メニュー項目の検索<a name="menu_search"></a>

Azure Security Center では現在、Azure Monitor ログ検索を使用してセキュリティ データが取得され、分析されます。 この画面は、Log Analytics の検索ページへの窓口の役割を担い、これによってユーザーは選択したワークスペース上で検索クエリを実行できます。 詳細については、[Azure Security Center の検索](https://docs.microsoft.com/azure/security-center/security-center-search)に関するページを参照してください。 この検索ウィンドウは廃止されました。

![Search page][4]

### <a name="search-menu-entry---the-new-experience"></a>検索メニュー項目 - 新しいエクスペリエンス

Azure Log Analytics のネイティブ機能を使用して、ワークスペース上で検索クエリを実行することをお勧めします。 Azure Log Analytics に移動し、 **[ログ]** を選択します。

![Log Analytics の [ログ] のページ][5]

## クラシック ID とアクセス (プレビュー)<a name="menu_classicidentity"></a>

Security Center の [クラシック ID とアクセス] エクスペリエンスでは現在、ID とアクセスに関する情報のダッシュボードを Log Analytics に表示しています。 このダッシュボードを表示するには:

1. **[クラシック ID とアクセスの表示]** を選択します。

   ![ID ページ][6]

1. **[ID とアクセスのダッシュボード]** を表示します。

    ![ID ページ - ワークスペースの選択][7]

1. ワークスペースを選択して、Log Analytics で **[ID およびアクセス]** ダッシュボードを開き、ID およびアクセスの情報をワークスペース上で表示します。

   ![ID ページ - ダッシュボード][8]

上記の手順で示した 3 つの画面はすべて廃止されました。 データは、Log Analytics のセキュリティ ソリューション内で引き続き使用でき、変更または削除されていません。

### <a name="classic-identity--access-preview---the-new-experience"></a>クラシック ID とアクセス (プレビュー) - 新しいエクスペリエンス

Log Analytics のダッシュボードでは、1 つのワークスペースに関する分析情報を表示していました。 しかし、Security Center のネイティブ機能によって、すべてのサブスクリプションとそれらに関連付けられているすべてのワークスペースを可視化できます。 安全スコアに従ってランク付けされた提案により、重要事項に集中できる使いやすいビューにアクセスできます。

Log Analytics の **[ID およびアクセス]** ダッシュボードのすべての機能には、Security Center 内で **[ID およびアクセス (プレビュー)]** を選択することでアクセスできます。

![ID ページ - 従来のエクスペリエンスの廃止][9]

## セキュリティ イベント マップ<a name="menu_securityeventsmap"></a>

Security Center は、セキュリティの脅威を識別するために役立つ[セキュリティ アラート マップ](https://docs.microsoft.com/azure/security-center/security-center-threat-intel)を提供します。 該当のマップの **[セキュリティ イベント マップに移動]** ボタンから、選択したワークスペース上に未加工のセキュリティ イベントを表示できるダッシュボードが開きます。

**[セキュリティ イベント マップに移動]** ボタンと、ワークスペースごとのダッシュボードは廃止されました。

![[セキュリティ アラート マップ] - ボタン][10]

**[セキュリティ イベント マップに移動]** ボタンを選択すると、(廃止された) 脅威インテリジェンス ダッシュボードが開きました。

![[脅威インテリジェンス] ダッシュボード][11]

ワークスペースを選択してその脅威インテリジェンス ダッシュボードを表示すると、Log Analytics の (廃止された) セキュリティ アラート マップ (プレビュー) 画面が開きました。

![Log Analytics にある [セキュリティ アラート マップ]][12]

既存のデータは、Log Analytics のセキュリティ ソリューション内で引き続き使用でき、変更または削除されていません。

### <a name="security-events-map---the-new-experience"></a>セキュリティ イベント マップ - 新しいエクスペリエンス

Security Center に組み込まれた次のアラート マップ機能を使用することをお勧めします: **セキュリティ アラート マップ (プレビュー)** 。 この機能は最適化されたエクスペリエンスを提供し、すべてのサブスクリプションと関連するワークスペースにわたって機能します。 提供されるのは環境全体の概略ビューであり、1 つのワークスペースに焦点を当てるものではありません。

## カスタムのアラート ルール (プレビュー)<a name="menu_customalerts"></a>

基盤となるインフラストラクチャが廃止されることから、2019 年 6 月 30 日をもって[カスタム アラートは廃止されました](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)。 廃止日以降は、カスタム セキュリティ アラートは生成されません。
[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) を有効にして、そこでカスタム アラートを再作成することをお勧めします。 Azure Monitor のログ アラートを使用してアラートを作成することもできます。

Azure Sentinel でカスタム アラートを作成するには:

1. [Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) を開き、カスタム アラートが保存されているワークスペースを選択します
1. メニューの **[分析]** を選択します
1. Azure Sentinel でカスタム アラートを作成する方法については、次の[チュートリアル](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats)の指示に従ってください

Azure Sentinel の使用に関心がない場合は、Azure Monitor のログ アラートでアラートを作成できます。 手順については、「 [Azure Monitor を使用してログ アラートを作成、表示、管理する](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)」および「[Azure Monitor でのログ アラート](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)」を参照してください。

![カスタム アラート][13]

カスタム アラートの廃止について詳しくは、「[Azure Security Center のカスタム アラート ルール (プレビュー)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)」を参照してください。

## セキュリティ アラートの調査<a name="menu_investigate"></a>

Security Center の[調査機能](https://docs.microsoft.com/azure/security-center/security-center-investigation)は、潜在的なセキュリティ インシデントのトリアージに役立ちます。 この機能を使用すると、インシデントのスコープを理解し、その根本原因を突き止めることができます。 この機能は、[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) において強化されたエクスペリエンスに置き換えられているため、Security Center からは削除されました。

![セキュリティ インシデント][14]

**[セキュリティ インシデント]** 画面から **[調査]** ボタンを選択すると、調査ダッシュボード (プレビュー) が Log Analytics で開きます。 調査のダッシュボードは廃止されました。

既存のデータは、Log Analytics のセキュリティ ソリューション内で引き続き使用でき、変更または削除されていません。

![Log Analytics にある調査のダッシュボード][15]

### <a name="investigation---the-new-experience"></a>調査 - 新しいエクスペリエンス

豊富な調査エクスペリエンスを提供する [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) に移行することをお勧めします。 Azure Sentinel では、組織のデータ ソースにわたってセキュリティ上の脅威を検出するために、強力な検索とクエリのツールを提供しています。

## セキュリティ ソリューションのサブセット<a name="menu_solutions"></a>

Security Center を使用すると、[Azure で統合されたセキュリティ ソリューション](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)を有効にすることができます。 Security Center の次のパートナー ソリューションは廃止されました。 これらのソリューションは、[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) で多数の追加データ ソースと共に有効になっています。

- [次世代ファイアウォールおよび Web アプリケーション ファイアウォールのソリューション](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [共通イベント形式 (CEF) をサポートするセキュリティ ソリューションの統合](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

廃止後は、UI と API のどちらからも、前出の一覧に記載されているソリューションの種類の追加または変更はできません。 Azure Security Center で、これらのパートナー ソリューションの新しいインスタンスは検出されなくなります。

既存の接続済みソリューションがある場合は、Azure Sentinel に移行することをお勧めします。

![Security Center ソリューション][16]

## セキュリティ ポリシーに対するセキュリティ構成の編集<a name="menu_securityconfigurations"></a>

Azure Security Center は、OS を強化するための [150 を超える推奨の規則](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)のセットを適用して、セキュリティ構成を監視します。 これらの規則は、ファイアウォール、監査、パスワード ポリシーなどに関するものです。 脆弱な構成を持つマシンが見つかった場合、Security Center はセキュリティ推奨事項を生成します。 お客様は、[[セキュリティ構成の編集] 画面](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config)を利用して、Security Center にある既定の OS セキュリティ構成をカスタマイズできます。

このプレビュー機能は廃止されました。 提供終了日以降に、セキュリティ構成を既定値にリセットするには、API または Powershell を使用してます。[次の手順](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20samples/Reset%20security%20configurations%20customization)に従ってください。

![セキュリティ構成の編集][17]

### <a name="edit-security-configurations---the-new-experience"></a>セキュリティ構成の編集 - 新しいエクスペリエンス

Security Center で [Guest 構成エージェント](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)のサポートを有効にする予定です。 このようなアップデートにより、より多くのオペレーティング システムのサポート、ゲスト構成用の Azure ゲスト内ポリシーの統合など、はるかに豊富な機能セットが実現します。 これらの変更が有効になった後は、大きな規模で構成を制御し、それらを新しいリソースに自動的に適用することも可能になります。

## Log Analytics ワークスペースの [セキュリティおよび監査] ダッシュボード<a name="menu_securityomsdashboard"></a>

セキュリティおよび監査ダッシュボードは、最初は OMS ポータルで使用されていました。 Log Analytics では、ダッシュボードが、注目すべきセキュリティ イベントと脅威、脅威インテリジェンス マップ、およびワークスペースに保存されたセキュリティ イベントの ID とアクセスの評価の概要を、ワークスペースごとに提供します。 ダッシュボードを削除しました。 ダッシュボードの UI で既にお勧めしているように、Azure Security Center に移行することをお勧めします。

![Log Analytics のセキュリティ ダッシュボード][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>[セキュリティおよび監査] ダッシュボード - 新しいエクスペリエンス

Azure Security Center に切り替えることをお勧めします。 複数のサブスクリプションとそれらに関連付けられたワークスペースにわたって、同じセキュリティ概要に加えて、より豊富な機能セットが提供されます。

[セキュリティおよび監査] ダッシュボードを事前設定する、元の Log Analytics クエリは Security Center の [GitHub リポジトリ](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards)で入手できます。

## <a name="next-steps"></a>次の手順

- [Azure Security Center](https://docs.microsoft.com/azure/security-center/) の詳細について学習します。
- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel) の詳細について学習します。

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
