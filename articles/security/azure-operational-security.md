---
title: Azure で運用可能なセキュリティ | Microsoft Docs
description: Microsoft Azure Monitor ログおよびそのサービスとしくみについて説明します。
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: bb0726fdddf6d20a9c8c5f20d6cec0cae68322ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67119260"
---
# <a name="azure-operational-security"></a>Azure で運用可能なセキュリティ
## <a name="introduction"></a>はじめに

### <a name="overview"></a>概要
セキュリティはクラウドの最優先の課題であり、Azure セキュリティについての正確でタイムリーな情報を得ることがどれだけ重要かを、私たちは認識しています。 アプリケーションとサービスに Azure を使用する最大の理由の 1 つは、さまざまなセキュリティ ツールや機能を活用できることです。 これらのツールや機能により、Azure プラットフォーム上にセキュリティで保護されたソリューションを作成できるようになります。 Windows Azure では、透過的な説明責任を実現しつつ、顧客データの機密性、整合性、および可用性を提供する必要があります。

Microsoft Azure に実装されている多数のセキュリティ コントロールについて、お客様側と Microsoft 側の運用上の観点からご理解いただくために、このホワイト ペーパー「Azure で運用可能なセキュリティ」では、Windows Azure で使用できる運用上のセキュリティについて総合的に説明します。

### <a name="azure-platform"></a>Azure プラットフォーム
Azure は、オペレーティング システム、プログラミング言語、フレームワーク、ツール、データベース、デバイスにおいて幅広い選択肢をサポートするパブリック クラウド サービス プラットフォームです。 Docker を統合した Linux コンテナーの実行、JavaScript、Python、.NET、PHP、Java、Node.js によるアプリの構築、iOS、Android、Windows の各デバイスに対応したバックエンドの構築を行えます。 Azure パブリック クラウド サービスでは、何百万人もの開発者や IT プロフェッショナルから現在信頼が寄せられているのと同じテクノロジがサポートされています。

IT 資産を構築し、パブリック クラウド サービス プロバイダーに移行したとしましょう。このとき、移行したアプリケーションやデータをどこまで保護できるかは、採用したプロバイダーがクラウド ベースの資産のセキュリティ管理のためにどのようなサービスと体制を用意しているかに応じて変わってきます。

Azure のインフラストラクチャでは、数百万の顧客を同時にホストできるように施設からアプリケーションまでが設計されており、ビジネスのセキュリティ要件を満たす信頼性の高い基盤となっています。 また、Azure には構成可能な幅広いセキュリティ オプションと制御機能が用意されており、組織によるデプロイの独自の要件を満たすようにセキュリティをカスタマイズできます。 このドキュメントでは、Azure のセキュリティ機能を使用して、これらの要件をどのように満たすことができるかをわかりやすく説明します。

### <a name="abstract"></a>要約
Azure で運用可能なセキュリティとは、ユーザーのデータ、アプリケーション、および Microsoft Azure にあるその他の資産を保護するために使用できる、サービス、コントロール、機能を指します。 Azure で運用可能なセキュリティは、Microsoft セキュリティ開発ライフサイクル (SDL)、Microsoft セキュリティ レスポンス センター プログラム、およびサイバー セキュリティの脅威状況に対する深い認識など、Microsoft に固有のさまざまな機能の使用経験から得られた知識が組み込まれたフレームワーク上に構築されています。

このホワイト ペーパーでは、Microsoft Azure クラウド プラットフォーム内での Azure で運用可能なセキュリティに対する Microsoft の取り組みと、次のサービスについて説明します。
1.  [Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Azure Network watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure Monitor ログ

Microsoft Azure Monitor ログは、ハイブリッド クラウド向けの IT 管理ソリューションです。 Azure Monitor ログは単独で使用されるか、System Center の既存のデプロイを拡張するために使用され、お客様のインフラストラクチャをクラウドベースで管理するための柔軟性と制御を最大限に実現します。

![Azure Monitor ログ](./media/azure-operational-security/azure-operational-security-fig1.png)

Azure Monitor ログを使用すれば、オンプレミス型、Azure、AWS、Windows Server、Linux、VMware、OpenStack など、あらゆるクラウドのインスタンスを競合ソリューションよりも低コストで管理できます。 クラウド中心に構築された Azure Monitor ログは、新しいビジネス課題に対応し、新しいワークロード、アプリケーション、およびクラウド環境にも対応する最も高速でコスト効率の良い新たな企業の管理方法を提供します。

### <a name="azure-monitor-services"></a>Azure Monitor サービス

Azure Monitor ログのコア機能は、Azure で実行される一連のサービスによって提供されます。 サービスごとに固有の管理機能があり、サービスを組み合わせてさまざまな管理シナリオを実現できます。

| Service  | 説明|
| :------------- | :-------------|
| Azure Monitor ログ | 物理マシンや仮想マシンなど、さまざまなリソースの可用性とパフォーマンスを監視、分析します。 |
|Automation | 手動プロセスを自動化し、物理マシンと仮想マシンの構成を適用します。 |
| バックアップ | 重要なデータのバックアップと復元を行います。 |
| Site Recovery | 重要なアプリケーションの高可用性を実現します。 |

### <a name="azure-monitor-logs"></a>Azure Monitor ログ

[Azure Monitor ログ](https://azure.microsoft.com/documentation/services/log-analytics)は、管理対象リソースから中央リポジトリにデータを収集する監視サービスです。 このデータには、API 経由で提供されたイベント、パフォーマンス データ、カスタム データが含まれます。 一度収集されたデータは、アラート、分析、エクスポートに使用できます。


この方法を使用すると、さまざまなソースからのデータを統合できるため、Azure サービスから得たデータを既存のオンプレミス環境と組み合わせることが可能です。 さらに、データの収集とそのデータに対して実行される操作は明確に分離されているため、あらゆる種類のデータにすべての操作を実行できます。


![Azure Monitor ログ](./media/azure-operational-security/azure-operational-security-fig2.png)

Azure Monitor サービスは次の方法でクラウドベースのデータを安全に管理しています。
-   データの分離
-   データの保持
-   物理的なセキュリティ
-   インシデント管理
-   コンプライアンス
-   セキュリティ基準認定

### <a name="azure-backup"></a>Azure Backup

Azure Monitor スイートの製品およびサービスの一部である [Azure Backup](https://azure.microsoft.com/documentation/services/backup) は、データのバックアップと復元サービスを提供します。
アプリケーション データを保護し、長年にわたってデータを保管できます。設備投資は不要で、運用コストも最小限で済みます。 SQL Server や SharePoint などのアプリケーション ワークロードだけでなく、Windows の物理サーバーや仮想サーバーのデータもバックアップできます。 また、[System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) で Azure Backup を使用することで、保護データを Azure にレプリケートし、冗長性と長期保存を実現しています。


Azure Backup で保護されるデータは、特定の地域リージョンにあるバックアップ コンテナーに格納されます。 データは同じリージョン内でレプリケートされます。コンテナーのタイプによっては、回復性を高めるために別のリージョンにレプリケートされることもあります。

### <a name="management-solutions"></a>管理ソリューション
[Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) は、オンプレミスのインフラストラクチャやクラウド インフラストラクチャの管理および保護に役立つ、Microsoft のクラウド ベースの IT 管理ソリューションです。


[管理ソリューション](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions)は、1 つ以上の Azure Monitor サービスを使用する特定の管理シナリオが実装された、事前にパッケージ化された一連のロジックです。 さまざまなソリューションが Microsoft とパートナーによって提供されており、Azure サブスクリプションに簡単に追加して、Azure Monitor への投資の価値を高めることができます。 パートナーは、アプリケーションとサービスをサポートする独自のソリューションを作成し、Azure Marketplace またはクイック スタート テンプレートを使用してユーザーに提供できます。


![管理ソリューション](./media/azure-operational-security/azure-operational-security-fig4.png)

複数のサービスを使用して追加の機能を提供するソリューションの良い例は、[更新管理ソリューション](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)です。 このソリューションでは、Windows と Linux 用の [Azure Monitor ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) エージェントを使用して、各エージェントから必要な更新プログラムの情報を収集します。 このデータは、付属のダッシュボードを使ってデータを分析できる Azure Monitor ログ リポジトリに書き込まれます。

必要な更新プログラムをインストールするために、デプロイ作成時に [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) の Runbook が使用されます。 このプロセスはすべてポータルで管理するため、根底にある詳細について配慮する必要はありません。

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center によって、Azure リソースを保護します。 Azure サブスクリプション間のセキュリティ監視とポリシー管理を総合的に提供します。 サービス内では、Azure サブスクリプションに対してだけでなく[リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)に対してもポリシーを定義できるので、より細かい定義が行えます。

### <a name="security-policies-and-recommendations"></a>セキュリティ ポリシーと推奨事項

セキュリティ ポリシーは、指定されたサブスクリプションまたはリソース グループ内のリソースに推奨されるコントロールのセットを定義します。

Security Center では、セキュリティに関する会社の要件、アプリケーションの種類、データの機密度に合わせてポリシーを定義できます。

![セキュリティ ポリシーと推奨事項](./media/azure-operational-security/azure-operational-security-fig5.png)


右側の図に示すように、サブスクリプション レベルで有効になっているポリシーは、サブスクリプション内のすべてのリソース グループに自動的に反映されます。


### <a name="data-collection"></a>データ収集

Security Center では、仮想マシン (VM) のセキュリティ状態へのアクセス、セキュリティ推奨事項の提供、脅威についての警告を行うために、その仮想マシンからデータを収集します。 最初に Security Center にアクセスするときは、サブスクリプション内のすべての VM に対してデータ収集が有効になっています。 データ収集は有効にしておくことをお勧めしますが、Security Center のポリシーでデータ収集をオフにして、オプトアウトすることもできます。

### <a name="data-sources"></a>データ ソース

- Azure Security Center は、セキュリティ状態の可視化、脆弱性の識別、軽減策の提案、アクティブな脅威の検出のために、次のソースからのデータを分析します。

-   Azure サービス:デプロイされた Azure サービスの構成についての情報を、サービスのリソース プロバイダーと通信して使用します。

- ネットワーク トラフィック:送信元/送信先 IP/ポート、パケット サイズ、ネットワーク プロトコルなど、Microsoft のインフラストラクチャからサンプリングされたネットワーク トラフィック メタデータを使用します。

-   パートナー ソリューション:ファイアウォールやマルウェア対策ソリューションなど、統合されたパートナー ソリューションからのセキュリティの警告を使用します。

-   仮想マシン:仮想マシンからの構成情報とセキュリティ イベントについての情報 (Windows のイベントおよび監査ログ、IIS ログ、syslog メッセージ、クラッシュ ダンプ ファイルなど) を使用します。

### <a name="data-protection"></a>データ保護

Azure Security Center では、脅威に対する防御と検出、対応を支援するために、構成情報、メタデータ、イベント ログ、クラッシュ ダンプ ファイルなど、セキュリティに関連するさまざまなデータを収集、処理しています。 Microsoft ではコーディングからサービスの運用まで、厳密なコンプライアンスとセキュリティのガイドラインに準拠しています。

-   **データの分離**:データはサービス全体を通じて、各コンポーネントに論理的に分割されて保存されます。 すべてのデータは組織ごとにタグ付けされます。 このタグ付けはデータのライフ サイクルにおいて継続され、サービスの各層で強制されます。

-   **データ アクセス**:セキュリティに関する推奨事項を提供したり、潜在的なセキュリティの脅威を調査したりするために、Azure サービスによって収集または分析された情報 (クラッシュ ダンプ ファイル、プロセス作成イベント、VM ディスク スナップショット、アーティファクトなど) に Microsoft の担当者がアクセスする場合があります。これらの情報には、仮想マシンにある顧客データまたは個人データが意図せずに含まれる場合があります。 Microsoft は、[Microsoft オンライン サービスの使用条件とプライバシーに関する声明](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)を遵守しています。これらの文書には、Microsoft が宣伝や他の商業上の目的のために顧客データを使用したり、そのデータから情報を取り出したりしないと記載されています。

-   **データの使用**:Microsoft は、防止と検出の機能を強化するために、複数のテナントにわたって見られるパターンおよび脅威インテリジェンスを使用します。その際に、[プライバシーに関する声明](https://www.microsoft.com/en-us/privacystatement/OnlineServices/)で説明されている誓約に従います。

### <a name="data-location"></a>データの場所

Azure Security Center は、クラッシュ ダンプ ファイルの一時的なコピーを収集し、悪用の試行と成功した侵害の証拠がないか分析します。 Azure Security Center は、この分析をワークスペースと同じ geo 内で行い、分析が完了すると、一時的なコピーを削除します。 マシン アーティファクトは、VM と同じリージョンに一元的に格納されます。

-   **ストレージ アカウント**:ストレージ アカウントは、仮想マシンが実行されているリージョンごとに指定されます。 そうすることで、データの収集元となる仮想マシンと同じリージョンにデータを格納することができます。

-   **Azure Security Center ストレージ**:パートナーの警告、推奨事項、セキュリティの正常性状態を含むセキュリティ警告に関する情報は、現在米国内に一元的に保存されます。 この情報には、仮想マシンから収集された関連の構成情報やセキュリティ イベントが含まれる場合があります。これらは、セキュリティ警告、推奨事項、またはセキュリティの正常性状態をユーザーに提供するために必要な情報です。


## <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor ログのセキュリティ](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources)と監査ソリューションによって、IT 管理者はすべてのリソースを能動的に監視できるようになるため、セキュリティ インシデントの影響を最小限に抑えることができます。 Azure Monitor ログ Security and Audit には、リソースを監視するために利用できるセキュリティ ドメインがあります。 セキュリティ ドメインによって各オプションへの速やかなアクセスが可能になるほか、セキュリティを監視するうえで、次に示すドメインの監視対象がさらに詳細になります。

-   マルウェアの評価
-   [Update Assessment] \(更新の評価)
-   ID とアクセス

Azure Monitor では、特定の種類のリソースについての詳しい情報を提供しています。 Azure インフラストラクチャ (アクティビティ ログ) と個々の Azure リソース (診断ログ) から得られたデータの視覚化、クエリ、ルーティング、アラート、自動スケール、自動化を実行します。

![Azure Monitor](./media/azure-operational-security/azure-operational-security-fig6.png)


クラウド アプリケーションは、動的なパーツを多数使った複雑な構成になっています。 監視では、アプリケーションを正常な状態で稼働させ続けるためのデータを取得できます。 また、潜在的な問題を防止したり、発生した問題をトラブルシューティングするのにも役立ちます。

さらに、監視データを使用して、アプリケーションに関する深い洞察を得ることもできます。 そのような知識は、アプリケーションのパフォーマンスや保守容易性を向上させたり、手作業での介入が必要な操作を自動化したりするうえで役立ちます。

### <a name="azure-activity-log"></a>[Azure Activity Log (Azure アクティビティ ログ)]


サブスクリプションのリソースに対して実行された操作を調査できるログです。 アクティビティ ログではサブスクリプションのコントロール プレーン イベントが報告されるため、以前は "監査ログ" または "操作ログ" と呼ばれていました。

![[Azure Activity Log (Azure アクティビティ ログ)]](./media/azure-operational-security/azure-operational-security-fig7.png)

アクティビティ ログを使用すると、サブスクリプションのリソースに対して発生する書き込み操作 (PUT、POST、DELETE) すべてについて、"いつ誰が何を" 行ったのかを確認できます。 さらに、操作の状態など、重要性の大きなプロパティを確認することもできます。 アクティビティ ログには、読み取り (GET) 操作や、クラシック モデルを使用するリソースに対する操作は含まれていません。

### <a name="azure-diagnostic-logs"></a>Azure 診断ログ

Azure 診断ログは、リソースによって出力され、そのリソースの操作に関する豊富なデータを提供します。 これらのログの内容は、リソースの種類によって異なります。

たとえば、Windows イベント システム ログは、VM、BLOB、テーブルの診断ログのカテゴリの 1 つであり、キューのログは、ストレージ アカウントの診断ログのカテゴリです。

診断ログは、[アクティビティ ログ (以前は監査ログまたは操作ログと呼ばれていたもの)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) とは異なります。 アクティビティ ログでは、サブスクリプションのリソースに対して実行された操作を調査できます。 診断ログでは、リソース自体が実行した操作を調査できます。

### <a name="metrics"></a>メトリック

Azure Monitor では、テレメトリを使用して、Azure のワークロードのパフォーマンスと正常性を視覚的に確認できます。 Azure テレメトリ データの種類の中でも最も重要なのは、Azure リソースのほとんどから出力されるメトリックであり、これはパフォーマンス カウンターとも呼ばれます。 Azure Monitor では、この[メトリック](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)を複数の方法で構成して使用することで、監視やトラブルシューティングを実行できます。 テレメトリの重要なソースであるメトリックを使用すると、次の作業を行うことができます。

-   **パフォーマンスを追跡する**: VM、Web サイト、ロジック アプリなどのリソースのメトリックをポータルのグラフにプロットし、そのグラフをダッシュボードに固定することで、リソースのパフォーマンスを追跡できます。

-   **問題の通知を受け取る**: メトリックが特定のしきい値を超えたときに、リソースのパフォーマンスに影響する場合はその旨が通知されます。

-   **自動化されたアクションを構成する**: メトリックが特定のしきい値を超えたときに、リソースを自動スケールしたり、Runbook を起動したりします。

-   **高度な分析を実行する**: リソースのパフォーマンスや使用傾向も報告することもできます。

-   **アーカイブする**: コンプライアンスまたは監査の目的で、リソースのパフォーマンスや正常性の履歴をアーカイブします。

### <a name="azure-diagnostics"></a>Azure Diagnostics

デプロイされたアプリケーションで診断データを収集できるようにする Azure 内の機能です。 さまざまなソースで診断拡張機能を使用することができます。 現時点でのサポート対象は、[Azure クラウド サービスの Web ロールおよび Worker ロール](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service)、Microsoft Windows を実行している [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/overview)、および [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) となっています。 その他の Azure サービスには、独自の独立した診断があります。

## <a name="azure-network-watcher"></a>Azure Network Watcher

ネットワークの脆弱性を検出し、IT セキュリティおよび規制ガバナンス モデルへのコンプライアンスを確保するためには、ネットワーク セキュリティの監査が不可欠です。 セキュリティ グループ ビューでは、構成したネットワーク セキュリティ グループとセキュリティ ルール、および有効なセキュリティ ルールを取得できます。 適用済みのルール一覧に基づいて、開いているポートを特定し、ネットワークの脆弱性を評価できます。

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) は地域サービスであり、ネットワーク レベルで Azure 内と Azure 間の状態を監視して診断できます。 Network Watcher に搭載されているネットワークの診断および監視ツールを使用して、Azure 内のネットワークを把握および診断し、洞察を得ることができます。 このサービスには、パケット キャプチャ、次のホップ、IP フロー検証、セキュリティ グループ ビュー、NSG フロー ログなどが搭載されています。 シナリオ レベルの監視では、個別のネットワーク リソースの監視とは対照的に、ネットワーク リソースを隅から隅まで確認できます。

![Azure Network Watcher](./media/azure-operational-security/azure-operational-security-fig8.png)

現在、Network Watcher が備える機能は次のとおりです。

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">監査ログ</a>** - ネットワークの構成の一環として実行された操作が記録されます。 これらのログは、Azure Portal で確認することも、Power BI などの Microsoft ツールやサードパーティ ツールを使用して取得することもできます。 監査ログは、ポータル、PowerShell、CLI、Rest API で利用できます。 監査ログの詳細については、Resource Manager の監査操作に関するページを参照してください。 監査ログは、すべてのネットワーク リソースに対して実行された操作について用意されています。


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">IP フロー検証</a>** - フロー情報の 5 タプル パケット パラメーター (宛先 IP、送信元 IP、宛先ポート、送信元ポート、プロトコル) に基づいてパケットが許可されたか拒否されたかを確認します。 パケットがネットワーク セキュリティ グループにより拒否された場合、そのパケットを拒否した規則とネットワーク セキュリティ グループが返されます。

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">次のホップ</a>** - Azure Network Fabric におけるルーティング対象パケットの次のホップを特定します。これにより、誤って構成されたユーザー定義のルーティングがあるかどうかを診断できます。

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">セキュリティ グループ ビュー</a>** - VM に適用されている有効な適用セキュリティ規則を確認できます。

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">NSG フロー ログ</a>** - ネットワーク セキュリティ グループのフロー ログにより、そのグループのセキュリティ規則で許可または拒否されるトラフィックに関係するログを記録できます。 フローは 5 タプル情報 (送信元 IP、宛先 IP送信元ポート、宛先ポート、プロトコル) で定義されます。

## <a name="azure-storage-analytics"></a>Azure Storage Analytics

[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) では、ストレージ サービスへの要求に関して集計されたトランザクション統計情報と容量データを含むメトリックを格納できます。 トランザクションは、API 操作レベルとストレージ サービス レベルの両方でレポートされます。容量は、ストレージ サービス レベルでレポートされます。 メトリック データは、ストレージ サービスの使用状況の分析、ストレージ サービスに対する要求に関する問題の診断、サービスを使用するアプリケーションのパフォーマンスの向上に利用できます。

[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) では、ログが記録され、ストレージ アカウントのメトリック データを得ることができます。 このデータを使用して、要求のトレース、使用傾向の分析、ストレージ アカウントの問題の診断を行うことができます。 Storage Analytics Logging は、[BLOB、Queue、Table の各サービス](https://docs.microsoft.com/azure/storage/storage-introduction)で使用できます。 Storage Analytics は、ストレージ サービスに対する要求の成功と失敗についての詳細な情報をログに記録します。

この情報を使って個々の要求を監視したり、ストレージ サービスに関する問題を診断したりできます。 要求は、ベスト エフォートでログに記録されます。 ログ エントリが作成されるのは、サービス エンドポイントに対して行われた要求がある場合に限られます。 たとえば、ストレージ アカウントの BLOB エンドポイントにはアクティビティが存在するが、Table エンドポイントや Queue エンドポイントには存在しない場合、Blob service に関連したログだけが作成されます。

Storage Analytics を使用するには、監視するサービスごとに Storage Analytics を個別に有効にする必要があります。 Storage Analytics は [Azure Portal](https://portal.azure.com/) で有効にできます。詳細については、「[Azure Portal でのストレージ アカウントの監視](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)」を参照してください。 また、プログラムから REST API またはクライアント ライブラリを使用して有効にすることもできます。 サービスごとに Storage Analytics を個別に有効にするには、Set Service Properties 操作を使用します。

集計データは、既知の BLOB (ログの場合) と既知のテーブル (メトリックの場合) に格納されます。集計データには、Blob service と Table service の API を使用してアクセスできます。

ストレージ アカウントの合計の制限とは別に、Storage Analytics には、格納されたデータの量に関して 20 TB (テラバイト) の制限があります。 すべてのログは、Storage Analytics をストレージ アカウントに対して有効にしたときに自動的に作成される $logs という名前のコンテナー内の[ブロック BLOB](https://docs.microsoft.com/azure/storage/storage-analytics) に格納されます。

Storage Analytics によって実行される次の操作には料金が発生します。

-   ログの BLOB の作成要求
-   メトリックのテーブル エンティティの作成要求

> [!Note]
> 課金ポリシーとデータ保持ポリシーの詳細については、「[Storage Analytics and Billing (Storage Analytics と課金)](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)」を参照してください。
> パフォーマンスを最適化するには、仮想マシンに接続する使用率が高いディスク数を制限して、スロットルを回避するようにします。 すべてのディスクの使用率が同時に高くならなければ、ストレージ アカウントは多数のディスクをサポートできます。

> [!Note]
> ストレージ アカウントの制限の詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](https://docs.microsoft.com/azure/storage/storage-scalability-targets)」を参照してください。


次のタイプの認証済み要求と匿名要求が記録されます。

| 認証済み  | 匿名|
| :------------- | :-------------|
| 成功した要求 | 成功した要求 |
|失敗した要求 (タイムアウト、スロットル、ネットワーク、承認などに関する各種エラー) | Shared Access Signature (SAS) を使用した要求 (失敗した要求と成功した要求を含む) |
| Shared Access Signature (SAS) を使用した要求 (失敗した要求と成功した要求を含む) |クライアントとサーバーの両方のタイムアウト エラー |
|   データの分析要求 |    エラー コード 304 (変更されていません) で失敗した GET 要求 |
| Storage Analytics そのものによる要求 (ログの作成/削除など) は記録されません。 ログに記録されるデータの一覧については、「[Storage Analytics によって記録される操作およびステータス メッセージ](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages)」および「[Storage Analytics のログの形式](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)」をご覧ください。 | その他の失敗した匿名要求は一切記録されません。 ログに記録されるデータの全一覧については、「[Storage Analytics によって記録される操作やステータス メッセージ](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages)」および「[Storage Analytics のログの形式](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)」を参照してください。 |

## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD には、多要素認証、デバイスの登録、セルフサービスのパスワード管理、セルフサービスのグループ管理、特権を持つアカウントの管理、ロール ベースのアクセス制御、アプリケーション使用状況の監視、機能豊富な監査とセキュリティの監視、アラートなど、一連の ID 管理機能も用意されています。

-   Azure AD の多要素認証と条件付きアクセスにより、アプリケーションのセキュリティを強化します。

-   セキュリティのレポート作成と監視機能により、アプリケーションの使用状況を監視し、高度な脅威からビジネスを保護します。

Azure Active Directory (Azure AD) には、ディレクトリに関するセキュリティ レポート、アクティビティ レポート、および監査レポートが含まれています。 [Azure Active Directory 監査レポート](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide)を利用すると、Azure Active Directory で発生した特権アクションを識別できます。 特権アクションとしては、昇格の変更 (例: ロールの作成、パスワードのリセット)、ポリシー構成の変更 (例: パスワード ポリシー)、ディレクトリ構成の変更 (例: ドメインのフェデレーション設定の変更) などがあります。

レポートでは、イベント名、アクションを実行したアクター、変更によって影響を受けた対象リソース、日時 (UTC) に関する監査レコードが提供されます。 [監査ログの表示](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)に関するページに説明されているように、[Azure Portal](https://portal.azure.com/) を使用して Azure Active Directory に対する監査イベントのリストを取得できます。 含まれているレポートの一覧を次に示します。

| セキュリティ レポート  | アクティビティ レポート| 監査レポート |
| :------------- | :-------------| :-------------|
|不明なソースからのサインイン | アプリケーションの使用状況: 概要 | ディレクトリ監査レポート |
|複数のエラー後のサインイン | アプリケーションの使用状況: 詳細 |   |
|複数の地域からのサインイン | アプリケーション ダッシュボード |  |
|不審なアクティビティのある IP アドレスからのサインイン |アカウント プロビジョニング エラー |  |
|不規則なサインイン アクティビティ |個々のユーザー デバイス |  |
|感染している可能性があるデバイスからのサインイン |個々のユーザー アクティビティ |   |
|異常なサインイン アクティビティがあるユーザー |グループのアクティビティ レポート |   |
| |パスワード リセット登録アクティビティ レポート |   |
| |パスワード リセット アクティビティ |   |



これらのレポートのデータは、SIEM システム、監査、ビジネス インテリジェンス ツールなどのアプリケーションに役立ちます。 Azure AD Reporting [API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) は、一連の REST ベースの API を使用して、プログラムによってデータにアクセスできるようにします。 これらの API は、さまざまなプログラミング言語とツールから呼び出すことができます。

Azure AD の監査レポートのイベントは、180 日間保持されます。

> [!Note]
> レポートの保持の詳細については、「 [Azure Active Directory Report Retention Policies (Azure Active Directory レポートの保持ポリシー)](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention)」をご覧ください。

[監査イベント](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events)を長期間保存する場合は、Reporting API を使用して監査イベントを定期的に別のデータ ストアにプルできます。

## <a name="summary"></a>まとめ

この記事は、組織の IT インフラストラクチャの管理に役立つソフトウェアやサービスを提供しながら、お客様のプライバシーとデータを保護する方法についてまとめています。 Microsoft は、他者にデータを委託する場合、信頼には厳格なセキュリティが必要であることを把握しております。 Microsoft ではコーディングからサービスの運用まで、厳密なコンプライアンスとセキュリティのガイドラインに準拠しています。 Microsoft においてデータの保護は最優先事項になります。

この記事では、以下の内容について説明しています。

-   Azure Monitor スイートでのデータの収集、処理、および保護方法。

-   複数のデータ ソース間での迅速なイベント分析。 セキュリティ上のリスクを特定し、脅威と攻撃の範囲および影響を把握してセキュリティ違反による損害を軽減します。

-   悪意のある発信 IP トラフィックおよび脅威の種類を視覚化することによる、攻撃パターンの特定。 プラットフォームにかかわらず、お使いの環境全体でのセキュリティ対策について把握します。

-   セキュリティまたはコンプライアンスの監査に必要なすべてのログとイベント データの取得。 検索とエクスポートが可能な、完全なログとイベント データ セットによって、セキュリティ監査に必要な時間とリソースを削減できます。

<ul>
<li>資産を監視するための、次のようなセキュリティ関連のイベント、監査、および違反分析の収集。</li>
<ul>
<li>セキュリティ体制</li>
<li>注目すべき問題</li>
<li>脅威の概要</li>
</ul>
</ul>

## <a name="next-steps"></a>次の手順

- [設計および運用面のセキュリティ](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft は、攻撃に強く、回復力のあるクラウド インフラストラクチャを確保することを考慮しながら、サービスとソフトウェアを設計しています。

- [Azure Monitor ログ |Security & Compliance](https://www.microsoft.com/cloud-platform/security-and-compliance)

Microsoft のセキュリティ データと分析を使用することにより、よりインテリジェントで効果的な脅威の検出を実行できます。

- [Azure Security Center の計画および運用](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) 組織のセキュリティ要件とクラウド管理モデルに応じて Security Center の利用を最適化できる、一連の手順とタスクについて説明します。

