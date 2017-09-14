---
title: "Azure で運用可能なセキュリティに関するベスト プラクティス | Microsoft Docs"
description: "この記事は、Azure で運用可能なセキュリティに関するベスト プラクティスを提供します。"
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: ed3c4bf2dcdda3e04a18682da568ae73f41bb37d
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---

# <a name="azure-operational-security-best-practices"></a>Azure で運用可能なセキュリティに関するベスト プラクティス
Azure で運用可能なセキュリティとは、ユーザーのデータ、アプリケーション、および Microsoft Azure にあるその他の資産を保護するために使用できる、サービス、コントロール、機能を指します。 Azure で運用可能なセキュリティは、Microsoft セキュリティ開発ライフサイクル (SDL)、Microsoft セキュリティ レスポンス センター プログラム、およびサイバー セキュリティの脅威状況に対する深い認識など、Microsoft に固有のさまざまな機能の使用経験から得られた知識が組み込まれたフレームワーク上に構築されています。

この記事では、Azure のデータベース セキュリティに関するベスト プラクティスについて説明します。 このベスト プラクティスは、Azure のデータベース セキュリティに関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

それぞれのベスト プラクティスについて、次の点を説明します。
-   ベスト プラクティスの内容
-   そのベスト プラクティスをお勧めする理由
-   そのベスト プラクティスを実践しなかった場合に発生する可能性がある事態
- そのベスト プラクティスを実践する方法

この「Azure で運用可能なセキュリティに関するベスト プラクティス」の記事は、この記事の執筆時点における共通認識と、Azure プラットフォームの機能および機能セットに基づいています。 共通認識とテクノロジは時間が経つにつれて変化するため、そのような変化に対応するために、この記事は定期的に更新されます。

この記事で説明する Azure で運用可能なセキュリティに関するベスト プラクティスは、次のとおりです。

-   クラウド インフラストラクチャの監視、管理、および保護
-   ID の管理とシングル サインオン (SSO) の実装
-   要求のトレース、使用傾向の分析、および問題の診断
-   一元化された監視ソリューションによるサービスの監視
-   脅威の防止、検出、および対応
-   エンド ツー エンドのシナリオ ベースのネットワーク監視
-   実証済みの DevOps ツールによるセキュリティ保護された展開

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>クラウド インフラストラクチャの監視、管理、および保護
IT 運用チームは、データセンター インフラストラクチャ、アプリケーション、データの管理を担当します。これには、こうしたシステムの安定性とセキュリティが含まれます。 ただし、多くの場合、複雑さを増す IT 環境のセキュリティを組織が把握するには、複数のセキュリティおよび管理システムのデータをまとめる必要があります。

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) は、Microsoft のクラウドベースの IT 管理ソリューションです。OMS を使用して、オンプレミスとクラウドのインフラストラクチャを管理し、保護することができます。

[OMS Security and Audit ソリューション](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources)によって、IT 管理者はすべてのリソースを能動的に監視できるようになるため、セキュリティ インシデントの影響を最小化することができます。 OMS のセキュリティと監査には、リソースを監視するために利用できるセキュリティ ドメインがあります。

OMS の詳細については、[Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx) に関する記事をご覧ください。

ユーザーが脅威を回避、検出、対応できるように、[Operations Management Suite (OMS) Security and Audit ソリューション](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)は、次のような、リソースに関するデータを収集して処理します。

-   セキュリティ イベント ログ
-   Windows イベント トレーシング (ETW) イベント
-   AppLocker の監査イベント
-   Windows ファイアウォール ログ
-   Advanced Threat Analytics のイベント
-   ベースライン評価の結果
-   マルウェア対策評価の結果
-   更新プログラム/パッチ評価の結果
-   エージェントで明示的に有効になっている Syslog ストリーム


## <a name="manage-identity-and-implement-single-sign-on"></a>ID の管理とシングル サインオンの実装
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) は、マイクロソフトが提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID の管理サービスです。

[Azure AD](https://azure.microsoft.com/services/active-directory/) には、[多要素認証](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)、デバイスの登録、セルフサービスのパスワード管理、セルフサービスのグループ管理、特権を持つアカウントの管理、ロールベースのアクセス制御、アプリケーション使用状況の監視、機能豊富な監査、セキュリティの監視とアラートなど、一連の [ID 管理](https://docs.microsoft.com/azure/security/security-identity-management-overview)機能も用意されています。

以下の機能により、クラウドベース アプリケーションのセキュリティ保護、IT プロセスの効率化、コストの削減を実現し、企業のコンプライアンス目標を確実に達成します。

-   クラウドの ID およびアクセス管理
-   ユーザーによるクラウド アプリへのアクセスを簡素化
-   機微なデータとアプリケーションの保護
-   従業員のセルフサービスを実現
-   Azure Active Directory との統合

### <a name="identity-and-access-management-for-the-cloud"></a>クラウドの ID およびアクセス管理
Azure Active Directory (Azure AD) は、包括的な [ID およびアクセス管理のためのクラウド ソリューション](https://www.microsoft.com/cloud-platform/identity-management)で、ユーザーとグループを管理するための堅牢な機能が用意されています。 オンプレミスとクラウドのアプリケーション (Office 365 などの Microsoft の Web サービスや、Microsoft 以外のサービスとしてのソフトウェア (SaaS) アプリケーションなど) への安全なアクセスを支援します。
Azure AD の ID 保護機能の詳細については、「[Azure Active Directory Identity Protection の有効化](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable)」をご覧ください。

### <a name="simplify-user-access-to-any-cloud-app"></a>ユーザーによるクラウド アプリへのアクセスを簡素化
[シングル サインオンを有効にする](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps)と、ユーザーは Windows、Mac、Android、iOS デバイスから何千ものクラウド アプリケーションに簡単にアクセスできます。 ユーザーは、所属企業の資格情報を使用して、カスタマイズされた Web ベースのアクセス パネルまたはモバイル アプリから、アプリケーションを起動できます。 Azure AD アプリケーション プロキシ モジュールを使用することによって単なる SaaS アプリケーション以上のものを実現できます。オンプレミスの Web アプリケーションを発行して、安全性の高いリモート アクセスやシングル サインオンを提供できます。

### <a name="protect-sensitive-data-and-applications"></a>機微なデータとアプリケーションの保護
[Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) は、追加の認証レベルを提供することによってオンプレミス アプリケーションおよびクラウド アプリケーションへの不正なアクセスを防ぐことができます。 セキュリティの監視やアラート、整合性のないアクセス パターンを識別する機械学習ベースのレポートを使用して、ビジネスを保護し、潜在的な脅威を軽減できます。

### <a name="enable-self-service-for-your-employees"></a>従業員のセルフサービスを実現
パスワードのリセットや、グループの作成と管理などの重要なタスクを従業員に任せることができます。 Azure AD を使用して、[セルフサービスによるパスワードの変更](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)とリセットおよびセルフサービスのグループ管理を実現できます。

### <a name="integrate-with-azure-active-directory"></a>Azure Active Directory との統合
[Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) やそれ以外のオンプレミス ディレクトリを Azure AD に拡張し、あらゆるクラウドベースのアプリケーションでシングル サインオンを実現できます。 ユーザー属性は、あらゆる種類のオンプレミス ディレクトリから、クラウド ディレクトリに自動的に同期できます。

Azure Active Directory の統合およびその有効化方法については、「[オンプレミスのディレクトリと Azure Active Directory の統合](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)」の記事を参照してください。

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>要求のトレース、使用傾向の分析、および問題の診断
[Azure Storage Analytics](https://docs.microsoft.com/azure/storage/storage-analytics) では、ログが記録され、ストレージ アカウントのメトリック データを得ることができます。 このデータを使用して、要求のトレース、使用傾向の分析、ストレージ アカウントの問題の診断を行うことができます。

新しいストレージ アカウントでは、Storage Analytics Metrics が既定で有効になっています。 Azure ポータルでログを有効化し、メトリックとログの両方を構成できます。詳細については、「[Azure ポータルでのストレージ アカウントの監視](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account)」を参照してください。 また、プログラムから REST API またはクライアント ライブラリを使用して有効にすることもできます。 サービスごとに Storage Analytics を個別に有効にするには、Set Service Properties 操作を使用します。

Storage Analytics や他のツールを使用した Azure Storage 関連の問題の特定、診断、トラブルシューティングに関する詳しいガイドについては、「 [Microsoft Azure ストレージの監視、診断、およびトラブルシューティング](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting)」をご覧ください。

Azure Active Directory の統合とそれを有効にする方法については、「[Enabling and Configuring Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN)」(Storage Analytics の有効化と構成) の記事を参照してください。

## <a name="monitoring-services"></a>サービスの監視
クラウド アプリケーションは、動的なパーツを多数使った複雑な構成になっています。 監視では、アプリケーションを正常な状態で稼働させ続けるためのデータを取得できます。 また、潜在的な問題を防止したり、発生した問題をトラブルシューティングするのにも役立ちます。 さらに、監視データを使用して、アプリケーションに関する深い洞察を得ることもできます。 そのような知識は、アプリケーションのパフォーマンスや保守容易性を向上させたり、手作業での介入が必要な操作を自動化したりするうえで役立ちます。

### <a name="monitor-azure-resources"></a>Azure リソースの監視
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) は、Azure リソースを 1 つのソースから監視できるプラットフォーム サービスです。 Azure Monitor を使用すると、Azure のリソースのメトリックとログを視覚化、クエリ、ルーティング、アーカイブし、そのメトリックとログに対してアクションを実行できます。 このデータを操作するには、Monitor ポータル ブレード、[Monitor PowerShell コマンドレット](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)、[クロスプラットフォーム CLI](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)、または [Azure Monitor REST API](https://msdn.microsoft.com/library/dn931943.aspx) を使用します。

### <a name="enable-autoscale-with-azure-monitor"></a>Azure Monitor の自動スケールの有効化
[Azure Monitor の自動スケール](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started)は、仮想マシン スケール セット (VMSS)、クラウド サービス、App Service プラン、および App Service 環境のみに適用されます。

### <a name="manage-roles-permissions-and-security"></a>ロールのアクセス許可とセキュリティの管理
多くのチームで、[監視データおよび設定へのアクセスを厳密に管理](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security)する必要があります。 たとえば、チームの中に監視のみを行うメンバー (サポート エンジニア、開発エンジニアなど) がいる場合、またはマネージ サービス プロバイダーを使用する場合は、監視データへのアクセス権のみを付与し、リソースを作成、変更、削除する機能については制限が必要になることがあります。

この記事では、Azure ユーザーに組み込みの監視 RBAC 役割をすばやく適用する方法、または限定的な監視アクセス許可を必要とするユーザーに対して、独自のカスタム ロールを作成する方法について説明します。 その後、Azure Monitor 関連のリソースのセキュリティに関する考慮事項と、そのリソースに含まれるデータへのアクセスを制限する方法を取り上げます。

## <a name="prevent-detect-and-respond-to-threats"></a>脅威の防止、検出、および対応
Security Center の脅威検出は、Azure のリソースやネットワーク、接続されているパートナー ソリューションからセキュリティ情報を自動的に収集することによって機能します。 この情報を分析し、ときには複数の情報源から得た情報との関連性を探りながら、脅威を特定します。 Security Center では、セキュリティの警告に優先順位が、脅威に対処するための推奨事項と共に割り当てられます。

-   Azure サブスクリプションの[セキュリティ ポリシーを構成](https://docs.microsoft.com/azure/security-center/security-center-policies)します。
-   [Security Center の推奨事項](https://docs.microsoft.com/azure/security-center/security-center-recommendations)を使用して、Azure リソースを保護します。
-   現在の[セキュリティ アラート](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)を確認して、管理します。

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) は、Azure リソースのセキュリティを高度に視覚化し、制御することで脅威を回避、検出し、それに対応できるようにします。 これにより、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

Security Center には、脅威の回避、検出、対応を容易かつ効果的に行える Azure 組み込みの機能が用意されています。 主な機能は次のとおりです。

-   クラウド セキュリティの状態を把握
-   クラウドのセキュリティを制御
-   統合されたクラウド セキュリティ ソリューションを簡単にデプロイ
-   脅威の検出と迅速な対応

### <a name="understand-cloud-security-state"></a>クラウド セキュリティの状態を把握
Azure Security Center では、すべての Azure リソースに関するセキュリティの状態を一元的に把握できます。 セキュリティの制御が適切かつ正しく構成されているかを一目で確認し、注意が必要なリソースを素早く特定できます。

### <a name="take-control-of-cloud-security"></a>クラウドのセキュリティを制御
会社のクラウド セキュリティに関するニーズに従って、Azure サブスクリプションの[セキュリティ ポリシー](https://docs.microsoft.com/azure/security-center/security-center-policies)を定義できます。ポリシーは、各サブスクリプションでのアプリケーションのタイプやデータの機密度に合わせて調整できます。 リソース所有者、ポリシー主導型の推奨事項に従って必要な制御を実装できるため、クラウドのセキュリティに関して推測する必要がなくなります。

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>統合されたクラウド セキュリティ ソリューションを簡単にデプロイ
Microsoft とパートナーによって提供される、業界をリードするファイアウォールやマルウェア対策を含む幅広い[セキュリティ ソリューションをすぐに活用](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)できます。 セキュリティ ソリューションはシンプルなプロビジョニングでデプロイでき、ネットワークの変更も自動的に構成されます。 また、パートナー ソリューションからのセキュリティ イベントは自動的に収集され、分析とアラートに活用できます。

### <a name="detect-threats-and-respond-fast"></a>脅威の検出と迅速な対応
統合された分析主導型のアプローチにより、クラウドに対する目の前の脅威や新たな脅威に対し先手を打つことができます。 Microsoft の包括的な[脅威インテリジェンス](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)や専門知識、Azure でのデプロイ全体にわたるクラウド セキュリティ関連イベントに関する洞察を組み合わせることで、Security Center では実際の脅威を早期に検出すると同時に、誤検知を減らすことができます。 クラウド セキュリティ アラートにより、関連するイベントや影響を受けたリソースなどの情報を含む、一連の攻撃に関する洞察を得ることができます。また、問題を修復してすばやく復旧する方法についての提案も得られます。

## <a name="end-to-end-scenario-based-network-monitoring"></a>エンド ツー エンドのシナリオ ベースのネットワーク監視
Azure では、VNet、ExpressRoute、Application Gateway、ロード バランサーなどのさまざまなネットワーク リソースを調整および作成することで、エンドツーエンド ネットワークを作成できます。 各ネットワーク リソースは監視することができます。

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) は、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できる地域サービスです。 Network Watcher に搭載されているネットワークの診断および監視ツールを使用して、Azure 内のネットワークを把握および診断し、洞察を得ることができます。

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>パケット キャプチャによりリモート ネットワーク監視を自動化
Network Watcher を使用すると、仮想マシン (VM) にログインすることなくネットワークの問題を監視して診断できます。 アラートの設定により[パケット キャプチャ](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture)をトリガーし、パフォーマンスに関する情報をパケット レベルでリアルタイムに確認可能です。 問題が起きた場合には、詳細に調査してより適切に診断できます。

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>フロー ログを使用してネットワーク トラフィックの詳細を把握
[ネットワーク セキュリティ グループのフロー ログ](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-nsg-flow-logging-overview)により、ネットワーク トラフィックのパターンをより深く把握できます。 フロー ログから得られた情報をもとに、コンプライアンスに関するデータを収集し、ネットワークのセキュリティ プロファイルを監査、監視できます。

### <a name="diagnose-vpn-connectivity-issues"></a>VPN 接続の問題を診断
Network Watcher には、[VPN Gateway と接続に関する最も一般的な問題を診断](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity)する機能があります。 問題を特定できるだけでなく、作成された詳細なログを使用した、より深い調査も可能です。

ネットワーク ウォッチャーの構成方法および有効化方法の詳細については、「[configure Network watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)」(Network Watcher の構成)の記事を参照してください。

## <a name="secure-deployment-using-proven-devops-tools"></a>実証済みの DevOps ツールによるセキュリティ保護された展開
これは、企業や開発チームの生産性と効率性を支援する、Microsoft Cloud スペースでの Azure DevOps ベスト プラクティスの一覧です。

-   **コードとしてのインフラストラクチャ (IaC):**コードとしてのインフラストラクチャは、モジュール式インフラストラクチャの構築と管理に関連する IT 担当者の日常作業の負担を軽減する、一連の技法とベスト プラクティスです。 これにより、ソフトウェア開発者がアプリケーション コードを構築し保守するように、IT 担当者は最新のサーバー環境を構築、維持できます。 Azure では、[Azure Resource Manager]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) で提供されている宣言型のテンプレートを使用してアプリケーションをプロビジョニングできます。 1 つのテンプレートで、複数のサービスをその依存関係と共にデプロイできます。 アプリケーション ライフサイクルの各ステージで、同じテンプレートを使用してアプリケーションを繰り返しデプロイします。
-   **継続的インテグレーションとデプロイ:** Azure Web アプリまたはクラウド サービスを[自動的に構築し展開](https://www.visualstudio.com/docs/build/overview)するよう、Visual Studio Team Services オンライン チーム プロジェクトを構成できます。 コードをチェックインするたびに、VSO が、Azure へビルドを実行し、バイナリを自動的に展開します。 この記事で説明されているパッケージのビルド プロセスは、Visual Studio の Package コマンドに相当し、発行手順は Visual Studio の Publish コマンドに相当します。
-   **リリース管理:** Visual Studio [Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) は、複数の段階にわかれた展開を自動化し、リリース プロセスを管理するためのソリューションです。 継続的で管理されたデプロイメント パイプラインを作成し、すばやく、簡単に、頻繁にリリースできます。 Release Management を使用することにより、リリース プロセスの大部分を自動化し、承認ワークフローを事前定義できます。 オンプレミスまたはクラウドに展開し、必要に応じて拡張、カスタマイズできます。
-   **アプリケーションのパフォーマンス監視:** 課題を検出し、問題を解決して、アプリケーションを継続的に改善します。 実際のアプリケーションの問題を今すぐ診断する。 ユーザーがそれで何を行っているかを理解します。 JS コードと webconfig エントリを追加するだけで簡単に構成でき、わずか数分間で詳細結果をポータルで確認できます。[アプリケーションに対する洞察](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/)を得ることで、企業は迅速に問題を検出し修復できます。
-   **ロード テストと自動スケール:** アプリケーション内のパフォーマンスの問題を検出して、デプロイの品質を改善できるため、アプリケーションを常に稼働、または使用可能状態に維持してビジネス ニーズに対応できます。 アプリケーションが、次の立ち上げ、またはマーケティング キャンペーン時のトラフィックに対応できることを保証できます。 Visual Studio Online では、すぐにクラウドベースの[ロード テスト](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)を開始できます。

## <a name="next-steps"></a>次のステップ
- 詳細については [Azure で運用可能なセキュリティ](https://docs.microsoft.com/azure/security/azure-operational-security)を参照してください。
- [Operations Management Suite | Security & Compliance](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Operations Management Suite Security and Audit ソリューションの概要](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)

