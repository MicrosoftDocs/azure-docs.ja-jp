---
title: "Azure のレポート ツールによる、個人データのドキュメント保護 | Microsoft Docs"
description: "個人データのプライバシーを保護するために Azure のレポート サービスとテクノロジを使用する方法。"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.openlocfilehash: 0ec9ceb63c3e1872e9815a7895b624276fc46123
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="document-protection-of-personal-data-with-azure-reporting-tools"></a>Azure のレポート ツールによる、個人データのドキュメント保護

この記事では、Azure のレポート サービスとテクノロジを使用して個人データのプライバシーを保護する方法について説明します。

## <a name="scenario"></a>シナリオ

米国に本社を置く大手クルーズ会社が、地中海、アドリア海、バルト海だけでなくイギリス諸島での旅程を提供できるように、事業を拡張しています。 この取り組みの中で、この会社は、イタリア、ドイツ、デンマーク、英国に拠点を置くいくつかの小規模クルーズ会社を買収しました。

会社は、Microsoft Azure を使用して企業データを処理および格納しています。 これには、グローバルな顧客ベースの氏名や住所、電話番号、クレジット カード情報など、個人を特定できる情報が含まれます。 また、住所、電話番号、納税者番号など、あらゆる場所の会社の従業員に関する標準的な人事情報も含まれます。 このクルーズ会社では、現在と過去の顧客とのリレーションシップを追跡するための個人情報を含む、特典やロイヤリティ プログラム メンバーの大規模なデータベースも管理しています。

この企業の従業員は、会社の支社からネットワークにアクセスし、世界各地に存在する旅行代理店の社員は、一部の会社リソースにアクセスできます。

## <a name="problem-statement"></a>問題の説明

この会社は、顧客と従業員のプライバシーと個人情報を保護するため、Azure の管理およびセキュリティ機能を使用する多階層のセキュリティ戦略を採用して、個人データへのアクセスと処理を厳重に制御し、内部および外部の監査では、保護対策を明示する必要があります。

## <a name="company-goal"></a>会社の目標

徹底的な防御セキュリティ戦略の一環として、個人データのすべてのアクセスと処理を追跡し、個人データの適切なプライバシー保護を確実に記録、機能させることが会社の目的です。

## <a name="solutions"></a>解決方法

Microsoft Azure は、個人データのアクセスと処理、データの geography 型のフロー、および個人データへの第三者のアクセスに関連するアクティビティとイベントを追跡および記録するための包括的な監視、ログ記録、および診断ツールを提供します。 クラウド内の個人データのセキュリティは共通の責任であるため、Microsoft はお客様に以下も提供します。

- Microsoft が独自に行う顧客データの処理に関する詳細情報

- Microsoft が管理しているセキュリティ対策

- 顧客データ送信場所と方法

- Microsoft 独自のプライバシーに関するレビュー プロセスの詳細

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) は、マイクロソフトが提供する、クラウドベースの、マルチテナント対応ディレクトリおよび ID 管理サービスです。 サービスのサインインと監査レポートの機能により、詳細なサインインおよびアプリケーション使用状況アクティビティ情報を取得できるため、顧客および従業員の個人データが適切にアクセスされていることを監視し、確認できます。

アクティビティ レポートには次の 2 つの種類があります。

- [監査アクティビティ レポート/ログ](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)にはシステム アクティビティ/タスクの詳細が記録されています。

- [サインイン アクティビティ レポート/ログ](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)の監査レポートには、各アクティビティを実行したユーザーが記録されます。

この 2 つを併用すると、実行された各タスクと、それぞれを実行したユーザーの履歴を追跡できます。 いずれの種類のレポートも、カスタマイズ可能なフィルターで処理できます。

#### <a name="how-do-i-access-the-audit-and-security-logs"></a>監査とセキュリティ ログのアクセス方法

監査とセキュリティ ログには、Active Directory ポータルから次の 3 つの方法でアクセスできます。**[アクティビティ]** セクション (**[監査ログ]** または **[サインイン]** のいずれかを選択)、または Active Directory の **[管理]** から **[ユーザーとグループ]** または **[エンタープライズ アプリケーション]** を選択します。 レポートは、Azure Active Directory reporting API からもアクセスできます。 

1. Azure ポータルで、**[Azure Active Directory]** を選択します。

2. **[アクティビティ]** セクションで、**[監査ログ]** を選択します。

    ![](media/protection-personal-data-azure-reporting-tools/image001.png)

3. リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

4.  リスト ビュー内の項目を選択すると、その項目に関する確認可能な詳細がすべて表示されます。

    ![](media/protection-personal-data-azure-reporting-tools/image003.png)

Azure Active Directory レポートには、**リスクのフラグが設定されたユーザー**と**危険なサインイン**の 2 種類のセキュリティ レポートも含まれており、Azure 環境での潜在的なリスクを監視できます。

レポート サービスの詳細については、「[Azure Active Directory レポート](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)」を参照してください。

Azure Active Directory で利用できるレポートに関する詳細については、[Azure Active Directory のアクティビティ レポート](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal#activity-reports)に関する記事をご覧ください。 このサイトには、ポータルで[監査ログのアクティビティ レポート](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)と[サインイン アクティビティ レポート](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)にアクセスして使用する方法の詳細が記載されています。 また、[リスクのフラグが設定されたユーザー](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-user-at-risk)と[危険なサインイン](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-risky-sign-ins)セキュリティ レポートの詳細情報も記載されています。

Azure Directory レポート機能に接続するためのプログラミング上の詳細については「[Azure Active Directory 監査 API リファレンス](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)」を参照してください。

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) を使用すると、[Azure Monitor](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-storage) からデータを収集して他のデータと関連付け、追加の分析を行うことができます。 Azure Monitor は、Azure 環境の監視データを収集、分析します。 

ログ検索、ビュー、ソリューションなどの Log Analytics の解析ツールは、収集されたすべてのデータに対して動作し、環境全体を一元的に分析します。 Log Analytics は、Windows イベント ログ、IIS ログ、および Syslog を集計および解析し、個人データを権限のないユーザーに公開される危険性がある、潜在的な個人データ侵害の検出に貢献できます。

#### <a name="how-do-i-use-log-analytics"></a>Log Analytics の使用方法

Log Analytics は、任意の Web ブラウザーを使用して、OMS ポータルまたは Azure ポータルからアクセスできます。 Log Analytics には、リポジトリ内のデータを迅速に取得して統合するためのクエリ言語が用意されています。 ログ検索を作成、保存して、ポータルで直接データを解析できます。

Azure ポータルで、Log Analytics ワークスペースを作成するには、次の操作を行います。

1. Marketplace のサービス一覧から **[Log Analytics]** を選択します。

2. **[作成]** を選択し、OMS のワークスペースの名前を指定して、サブスクリプション、リソース グループ、場所、価格レベルを選択します。

3. **[OK]** をクリックするとワークスペースの一覧が表示されます。

4. ワークスペースを選択すると、その詳細が表示されます。

    ![](media/protection-personal-data-azure-reporting-tools/image004.png)

サービスの詳細については、「[Log Analytics のドキュメント](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)」を参照してください。

評価ワークスペースを作成し、サービスを使用する基本方法については、「[Log Analytics ワークスペースを使って作業を開始する](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)」チュートリアルを参照してください。

上記で説明したログを使用して Log Analytics に接続して使用する方法の詳細については、次の Web ページを参照してください。

[Log Analytics での Windows イベント ログのデータ ソース](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)

[Log Analytics の IIS ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)

[Log Analytics の Syslog データ ソース](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-syslog)

### <a name="azure-monitorazure-activity-log"></a>Azure Monitor/アクティビティ ログのアラート 

[Azure Monitor](https://azure.microsoft.com/services/monitor/) では、Microsoft Azure のほぼすべてのサービス向けに標準となるインフラストラクチャのメトリックおよびログを提供します。
監視することにより、Azure アプリケーションに関する深い洞察を得られます。 Azure Monitor は、Azure 診断拡張機能 (Windows または Linux) を利用して、アプリケーション レベルのメトリックとログの大半を収集します。 [Azure のアクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)は、Azure Monitor で確認できるリソースの 1 つです。 これは、すべての API 呼び出しを追跡し、[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) で発生するアクティビティに関する詳細情報を提供します。
リソースの情報については、Azure インフラストラクチャで確認できるようなアクティビティ ログ (旧称、操作ログまたは監査ログ) を検索できます。 

アクティビティ ログで記録される情報の多くはパフォーマンスとサービスの正常性に関するものですが、データ保護に関連する情報もあります。 アクティビティ ログを使用すると、Azure サブスクリプションのリソースに対して発生する書き込み操作 (PUT、POST、DELETE) すべてについて、"いつ誰が何を" 行ったのかを確認できます。

たとえば、個人データの保護に影響を与える可能性のある、ネットワーク セキュリティ グループを管理者がいつ削除したかという記録が取得できます。 アクティビティ ログのエントリは Azure Monitor で 90 日間保存されます。

#### <a name="how-do-i-use-the-data-collected-by-azure-monitor"></a>Azure Monitor によって収集されたデータの活用方法

アクティビティ ログやその他の Azure Monitor リソース内のデータはさまざまな方法で活用ます。

- データ他の場所にリアルタイムでストリーミングできます。

- [Azure ストレージ アカウント](https://docs.microsoft.com/azure/storage/common/storage-introduction)を使用して保有ポリシーを設定することで、データを既定値よりも長い期間、保存することができます。

- [Azure ポータル](https://azure.microsoft.com/features/azure-portal/)、[Azure Application Insights](https://azure.microsoft.com/services/application-insights/)、[Microsoft PowerBI](https://powerbi.microsoft.com/)、またはサード パーティ製の視覚化ツールを使用して、データをグラフィックやグラフに視覚化できます。

- Azure Monitor REST API、CLI コマンド、[PowerShell](https://docs.microsoft.com/powershell/)コマンドレット、または .NET SDK を使用すれば、データに対しクエリを実行できます。

Azure Monitor の使用を開始するには、Azure ポータルで **[その他のサービス]** を選択します。

1. **[監視と管理]** セクションで **[監視]** まで下方向にスクロールします。

    ![](media/protection-personal-data-azure-reporting-tools/image005.png)

2.  **アクティビティ ログ** ビューが開きます。

    ![](media/protection-personal-data-azure-reporting-tools/image007.png)

一般的なフィルターに対するクエリを作成して保存し、最も重要なクエリをポータル ダッシュボードに固定すると、発生したイベントが条件を満たしているかどうかを常に把握できます。

1. リソース グループ、時間の範囲、およびイベント カテゴリ別に表示をフィルター処理することができます。

    ![](media/protection-personal-data-azure-reporting-tools/image008.png)

2. **[ピン留めする]** ボタンをクリックすると、ポータル ダッシュボードにクエリをピン留めすることができます。 これにより、サービス内の運用データ情報に対して単一のソースを作成できます。 クエリ名と結果数がダッシュボードに表示されます。

Azure Monitor を使用して、すべての Azure リソースのメトリックを表示したり、診断やアラートを設定したり、ログを検索することができます。 Azure Monitor とアクティビティ ログの使用方法の詳細については、「[Azure Monitor の使用](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started)」を参照してください。

### <a name="azure-diagnostics"></a>Azure 診断 

Azure での診断機能を使用することにより、複数のソースからデータを収集できます。 特に、セキュリティ ログを含む Windows イベント ログは、個人データの保護を追跡および記録する上で役立ちます。 セキュリティ ログは、ログオンの成功および失敗イベントとアクセス許可の変更、特定の種類の攻撃を示すパターンの検出、セキュリティ関連のポリシーへの変更、セキュリティ グループ メンバーシップの変更などを追跡します。

たとえば、イベント ID 4695 は、監査可能な保護データの保護解除が試行されたことを警告します。 これには、秘密キー、保存された資格情報、およびその他の機密情報などのデータを保護するデータ保護 API (DPAPI) を使用します。

#### <a name="how-do-i-enable-the-diagnostics-extension-for-windows-vms"></a>Windows VM の診断拡張機能を有効にする方法

PowerShell を使用して、Windows VM の診断拡張機能を有効にし、ログ データを収集することができます。 このための手順は、使用している配置モデル (Resource Manager または Classic) によって異なります。 Resource Manager デプロイ モデルを使用して作成された既存の VM で診断拡張機能を有効にするには、[Set-AzureRMVMDiagnosticsExtension PowerShell コマンドレット](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension?view=azurermps-4.3.1)を使用します。

   ![](media/protection-personal-data-azure-reporting-tools/image009.png)

*\$$diagnosticsconfig_path*  は、XML での診断構成が含まれているファイルへのパスです。 VM 上で Azure Diagnostics を有効にする方法については、「[PowerShell を使用して Windows を実行している仮想マシンで Azure 診断を有効にする](https://docs.microsoft.com/azure/virtual-machines/windows/ps-extensions-diagnostics)」をご覧ください。

Azure 診断拡張機能は、収集されたデータを Azure ストレージ アカウントに転送したり、Application Insights などのサービスに送信することができます。 このデータを監査使用できます。

#### <a name="how-do-i-store-and-view-diagnostic-data"></a>診断データの保存と表示

診断データは、Microsoft Azure ストレージ エミュレーターまたは Azure Storage に転送しない限り、永続的に保存されないことに注意してください。 Azure Storage で診断データを保存および表示するには、次の手順を実行します。

1. ServiceConfiguration.cscfg ファイルでストレージ アカウントを指定します。 Azure 診断は、データの種類に応じて、Blob service または、Table service のいずれかを使用します。 Windows イベント ログは、テーブル形式で保存されます。

2. データを転送します。 診断データの転送は構成ファイルを使って要求できます。 SDK 2.4 以前のバージョンでは、プログラミングにより要求することも可能です。

3. データは、Visual Studio の [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)、[サーバー エクスプローラー](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)、または Azure Management Studio の [Azure 診断マネージャー](https://www.cerebrata.com/products/azure-diagnostics-manager)を使用して表示します。

各実行手順については、「[Azure Storage への診断データの保存と表示](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)」を参照してください。

### <a name="azure-storage-analytics"></a>Azure Storage Analytics 

Storage Analytics は、ストレージ サービスに対する要求の成功と失敗についての詳細な情報をログに記録します。 この情報は、サービスに格納されている個人データへのアクセスを記録する目的で、各要求を監視するために使用できます。 ただし、Storage Analytics のログ記録は、既定では、ストレージ アカウントで有効になっていません。 これは、Azure ポータルから有効にできます。

#### <a name="how-do-i-configure-monitoring-for-a-storage-account"></a>ストレージ アカウントの監視の設定方法

ストレージ アカウントの監視を設定するには、次の操作を行います。

1. Azure ポータルで**[ストレージ アカウント]** を選択し、監視するアカウントの名前を選択します。

    ![](media/protection-personal-data-azure-reporting-tools/image011.png)

2. **[監視]** セクションで **[診断]** を選択します。

3.  サービス (Blob、Table、File) ごとに監視するメトリック データの**タイプ**を選択します。 BLOB、テーブル、およびキュー サービスの読み取り要求、書き込み要求、および削除要求の診断ログを保存するよう Azure Storage に指示するには、**[BLOB ログ]、[テーブル ログ]** および **[キュー ログ]** を選択します。

    ![](media/protection-personal-data-azure-reporting-tools/image013.png)

4. 下部にあるスライダーを使って**保持ポリシー**を日数 (1 ～ 365 の値) で設定します。 既定では 7 日間に設定されています。

5. **[保存]** を選択して構成設定を適用します。

ストレージ ログのログ エントリには、各要求に関する次の情報が含まれます。

- 開始時刻、エンド ツー エンドの待機時間、およびサーバーの待機時間などのタイミング情報。

- 操作の種類、クライアントがアクセスしているストレージ オブジェクトのキー、成功または失敗、およびクライアントに返された HTTP 状態コードなどのストレージ操作の詳細情報。

- クライアントが使用した認証の種類などの認証の詳細。

- ETag 値と最終変更タイムスタンプなどの同時実行情報。

- 要求メッセージと応答メッセージのサイズ。

Storage Analytics のログ記録を有効にする方法については、「[Azure ポータルでのストレージ アカウントの監視](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)」を参照してください。

### <a name="azure-security-center"></a>[Azure Security Center] \(Azure Security Center) 

[Azure Security Center](https://azure.microsoft.com/services/security-center/) は、Azure リソースのセキュリティ状態を監視して、脅威を予防および検出して対応策を推奨します。 個人データのプライバシーを保護するセキュリティ対策を文書化する方法がいくつかあります。

セキュリティ正常性の監視は、セキュリティ ポリシーに準拠していることを確認するために役立ちます。 セキュリティの監視は、組織の標準やベスト プラクティスを満たしていないシステムを特定するためにリソースを監査する、事前対応型の戦略です。 次のリソースのセキュリティの状態を監視することができます。

- Cloud Services (仮想マシンおよびクラウド サービス)

- ネットワーキング (仮想ネットワーク)

- 記憶域とデータ (サーバーとデータベースの監査および脅威の検出、TDE、記憶域の暗号化)

- アプリケーション (潜在的なセキュリティの問題)

これらのいずれのカテゴリのセキュリティ問題は、個人データのプライバシーに脅威をおよぼす可能性があります。

#### <a name="how-do-i-view-the-security-state-of-my-azure-resources"></a>Azure リソースのセキュリティ状態を監視方法

セキュリティ センターは、Azure リソースのセキュリティの状態を定期的に分析します。 ダッシュ ボードの **[防止]** セクションで、検出された潜在的なセキュリティの脆弱性が確認できます。

   ![](media/protection-personal-data-azure-reporting-tools/image014.png)

1. **[防止]** セクションで、**[コンピューティング]** を選択します。 この **[概要]** と **[仮想マシン]** には、すべての仮想マシンおよびそのセキュリティ状態と、Azure Security Center で監視している**クラウド サービス** の Web およびワーカー ロールが一覧表示されます。

2. より詳細な情報を表示するには、**[概要]** タブの推奨設定に従ってください。

3. **[仮想マシン]** タブで、VM を選択して、詳細を表示します。

Azure Security Center でデータ収集を有効にすると、Microsoft Monitoring Agent が、デプロイされた既存および新しくサポートされたすべての仮想マシン上に自動的にプロビジョニングされます。 このエージェントから収集されたデータは、サブスクリプションに関連付けられている既存の [Log Analytics](https://azure.microsoft.com/services/log-analytics/) ワークスペースまたは新規のワークスペースのいずれかに格納されます。

[脅威インテリジェンス レポート](https://docs.microsoft.com/azure/security-center/security-center-threat-report)は、Azure Security Center から提供されます。 このレポートは、攻撃者の身元、目標、現在および過去の攻撃キャンペーンと戦術、ツール、および使用している手順の特定に役立つ情報を提供します。 軽減策と修復方法に関する情報も含まれています。

これらの脅威レポートの主な目的は、目下の脅威に効果的に対応し、発生する問題を軽減するための事後対策を取れるようにすることです。 レポートに記載されている情報は、報告および監査目的で事故対策を文書化するときにも役立ちます。

Azure Security Center の各セキュリティ アラートの**疑わしいプロセスが実行された**ブレードの**レポート** フィールドにあるリンクからアクセスすると、PDF 形式の脅威インテリジェンス レポートが表示されます。

脅威インテリジェンス レポートの表示および使用方法の詳細については、「[Azure Security Center の脅威インテリジェンス レポート](https://docs.microsoft.com/azure/security-center/security-center-threat-report)」を参照してください。

## <a name="next-steps"></a>次のステップ:

[Azure Active Directory eporting API の概要](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

[Log Analytics とは](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

[Microsoft Azure での監視の概要](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

[Azure のアクティビティ ログの概要 (ビデオ)](https://azure.microsoft.com/resources/videos/intro-activity-log/)
