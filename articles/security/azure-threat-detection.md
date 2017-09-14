---
title: "Azure の高度な脅威検出 | Microsoft Docs"
description: "ID の保護とその機能について説明します。"
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: TomSh
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 7db677614c23a3447e3e40ae867711a754b06d0d
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="azure-advanced-threat-detection"></a>Azure の高度な脅威の検出
## <a name="introduction"></a>はじめに

### <a name="overview"></a>概要

マイクロソフトでは、Azure ユーザーを支援するために一連のホワイト ペーパー、セキュリティの概要、ベスト プラクティス、およびチェックリストを開発して Azure プラットフォームやその周辺の環境でさまざまなセキュリティ関連の機能を使用できるようにしています。 このトピックの内容は広範囲かつ詳細であることから定期的に更新されています。 このドキュメントは、次の要約のセクションで概要を示した一連の内容の一部です。

### <a name="azure-platform"></a>Azure プラットフォーム

Azure は、オペレーティング システム、プログラミング言語、フレームワーク、ツール、データベース、デバイスにおいて幅広い選択肢をサポートするパブリック クラウド サービス プラットフォームです。
次のプログラミング言語がサポートされています。
-   Docker と統合した Linux コンテナーの実行。
-   JavaScript、Python、.NET、PHP、Java、および Node.js を使用したアプリの構築。
-   iOS、Android、および Windows デバイス用バックエンドの構築。

Azure パブリック クラウド サービスでは、何百万人もの開発者や IT プロフェッショナルから現在信頼が寄せられているのと同じテクノロジがサポートされています。

ユーザーが組織と共にパブリック クラウドに移行する場合、その組織はユーザーのデータを保護し、システム周囲にセキュリティやガバナンスを提供する責任があります。

Azure のインフラストラクチャでは、数百万の顧客を同時にホストできるように施設からアプリケーションまでが設計されており、ビジネスのセキュリティ ニーズを満たす信頼性の高い基盤となっています。 Azure では、アプリのデプロイ要件を満たすセキュリティを構成およびカスタマイズするための幅広いオプションを提供します。 このドキュメントはこれらの要件を満たすうえで役立ちます。

### <a name="abstract"></a>要約

Microsoft Azure では、Azure Active Directory、Azure Operations Management Suite (OMS) および Azure Security Center などのサービスを通じて、高度な脅威検出を行う組み込み機能を提供しています。 このセキュリティ サービスと機能のコレクションにより、Azure デプロイの内部で起きている事象をシンプルかつ迅速に把握する方法が提供されます。

このホワイト ペーパーは、脅威の脆弱性診断に対する “Microsoft Azure の手法” について説明し、サーバーや他の Azure リソースをターゲットとした悪意あるアクティビティに関するリスクを分析します。 Azure プラットフォームと顧客向けセキュリティ サービスとテクノロジで最適化されたソリューションと共にこれを使用することにより、脅威を特定して脆弱性を管理する方法を見つけることができます。

このホワイト ペーパーでは、Azure プラットフォームと顧客向けのコントロールのテクノロジに重点を置いています。SLA、価格モデル、および DevOps プラクティスに関する考慮事項には言及しません。

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

![Azure Active Directory Identity Protection](./media/azure-threat-detection/azure-threat-detection-fig1.png)


[Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) は、[Azure AD Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) エディションの機能で、リスク イベントと組織の ID に影響する潜在的な脆弱性に関する概要を提供します。 Microsoft は 10 年以上にわたってクラウド ベースの ID を保護してきました。Azure AD Identity Protection は、同じ保護システムを企業のお客様が利用できるようにするものです。 Identity Protection は、[Azure AD の異常アクティビティ レポート](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports#anomalous-activity-reports)を通じて利用できる、既存の Azure AD の異常検出機能を使用して、リアルタイムの異常を検出できる新しいリスク イベントの種類を紹介します。

Identity Protection はアダプティブ Machine Learning アルゴリズムとヒューリスティックを使用して、ID が侵害されていることを示している可能性のある異常とリスク イベントを検出します。 このデータを使用して、Identity Protection はレポートとアラートを生成し、ユーザーがこれらのリスク イベントを調査して、適切な修復または軽減のアクションを実行できるようにします。

ただし、Azure Active Directory Identity Protection は単なる監視とレポート作成のツールではありません。 リスク イベントを基にして、Identity Protection は各ユーザーのユーザー リスク レベルを計算し、ユーザーがリスク ベースのポリシーを構成して組織の ID を自動的に保護するできるようにします。

これらのリスクに基づくポリシーと、Azure Active Directory および [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) によって提供される他の[条件付きアクセス コントロール](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access)により、パスワードのリセットや多要素認証の適用などのアダプティブ修復アクションを自動的にブロックまたは提供できます。

### <a name="identity-protections-capabilities"></a>Identity Protection の機能

Azure Active Directory Identity Protection は単なる監視とレポート作成のツールではありません。 指定したリスク レベルに達したときに、検出された問題が自動的に対処されるようにリスク ベースのポリシーを構成することで、組織の ID を保護できます。 こうしたポリシーと、Azure Active Directory および EMS によって提供される他の条件付きアクセス コントロールにより、パスワードのリセットや多要素認証の適用などのアダプティブ修復アクションを自動的にブロックまたは開始できます。

Azure の Identity Protection によってユーザーのアカウントや ID を保護できる方法の例には次のようなものがあります。

[リスク イベントとリスクの高いアカウントの検出:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Machine Learning とヒューリスティック ルールを使用した 6 つのリスク イベントの種類の検出
-   ユーザーのリスク レベルを計算します
-   脆弱性を目立たせることにより全体的なセキュリティ対策を向上させるためのカスタム推奨事項を提供します

[リスク イベントの調査:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   リスク イベントの通知を送信します
-   関連情報とコンテキスト情報を使用してリスク イベントを調査します
-   調査を追跡するための基本的なワークフローを提供します
-   パスワード リセットなどの修復アクションへの簡単なアクセスを提供します

[リスクに基づく条件付きアクセス ポリシー:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#risky-sign-ins)
-   サインインのブロックまたは多要素認証チャレンジの要求によりリスクの高いサインインを軽減するポリシー
-   リスクの高いユーザー アカウントをブロックまたはセキュリティ保護するためのポリシー
-   多要素認証用に登録するようユーザーに要求するポリシー

### <a name="azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management (PIM)

[Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) を使用すると、

![Azure AD Privileged Identity Management](./media/azure-threat-detection/azure-threat-detection-fig2.png)

組織内でのアクセスを管理、制御、および監視できます。 その対象には、Azure AD をはじめとする Microsoft の各種オンライン サービス (Office 365、Microsoft Intune など) 内のリソースへのアクセスが含まれます。

Azure AD Privileged Identity Management では、次のことが可能です。

-   Azure AD 管理者のアラートとレポートを受け取り、Office 365 や Intune などの Microsoft Online Services に "ジャスト イン タイム" で管理アクセスを行う

-   管理者のアクセス履歴と管理者の割り当ての変更に関するレポートを取得する

-   特権ロールへのアクセスに関するアラートを受け取る

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS)

[Microsoft Operations Management Suite ](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) は、Microsoft のクラウドベースの IT 管理ソリューションです。OMS を使用して、オンプレミスとクラウドのインフラストラクチャを管理し、保護することができます。 OMS はクラウドベースのサービスとして実装されるため、インフラストラクチャ サービスに最小限の投資をするだけで、すぐに稼働させることができます。 新しいセキュリティ機能が追加されると自動的に配信されるため、継続的なメンテナンスやアップグレードのコストが節約されます。

OMS は、価値のある独自のサービスに加え、[System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/) などの System Center のコンポーネントと統合して、管理のための既存のセキュリティ投資をクラウドに拡張できます。 System Center と OMS を連携させることで、本格的なハイブリッド管理を実現できます。

### <a name="holistic-security-and-compliance-posture"></a>包括的なセキュリティおよびコンプライアンスの体制

[OMS の [セキュリティおよび監査] ダッシュボード](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)では、注意を必要とする重要な問題向けの組み込みの検索クエリと共に、組織の IT セキュリティ対策への包括的な視点が提供されます。 [セキュリティおよび監査] ダッシュボードは、OMS におけるすべてのセキュリティ関連機能のホーム画面です。 この画面では、コンピューターのセキュリティの状態について大まかな情報を得ることができます。 また、過去の 24 時間、7 日間、またはそれ以外のカスタム期間に発生したすべてのイベントを表示する機能も含まれています。

OMS のダッシュボードは、あらゆる環境における包括的なセキュリティ体制をすばやく簡単に理解するのに役立ちます。この環境には、IT オペレーションというコンテキストでのソフトウェアの更新の評価、マルウェア対策の評価、構成基準などがすべて含まれます。 さらに、セキュリティ ログ データに容易にアクセスできるため、セキュリティとコンプライアンスを目的とする監査プロセスが合理化されます。

OMS のセキュリティと監査ダッシュボードは、次の 4 つの主要カテゴリで構成されています。

![OMS Security and Audit dashboard](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

-   **[セキュリティ ドメイン]**: この領域では、時間の経過に伴うセキュリティ レコードを詳しく調査したり、マルウェアの評価、更新プログラムの評価、ネットワーク セキュリティ、ID とアクセスの情報、セキュリティ イベントが発生したコンピューターにアクセスしたりできます。また、Azure Security Center のダッシュボードにも簡単にアクセスできます。

-   **[注目に値する問題]**: このオプションでは、アクティブな問題の数と重要度をすばやく特定することができます。

-   **[検出 (プレビュー)]**: ここでは、リソースに対して攻撃が発生したときにセキュリティの警告を視覚化することで、攻撃パターンを特定できます。

-   **[脅威インテリジェンス]**: 悪意のある送信側 IP トラフィックを持つサーバーの総数、悪意のある脅威の種類、このような IP の発信元を示すマップを視覚化することで、攻撃パターンを特定することができます。

-   **[一般的なセキュリティ クエリ]**: このオプションでは、環境を監視するために使用できる最も一般的なセキュリティ クエリの一覧が示されます。 これらのクエリの 1 つをクリックすると、[検索] ブレードが開き、そのクエリの結果が表示されます。

### <a name="insight-and-analytics"></a>洞察と分析
[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) の核となる機能は、Azure クラウドでホストされている OMS リポジトリです。

![洞察と分析](./media/azure-threat-detection/azure-threat-detection-fig4.png)

データは、データ ソースを構成してソリューションをサブスクリプションに追加することによって接続されているソースからリポジトリに収集されます。

![サブスクリプション](./media/azure-threat-detection/azure-threat-detection-fig5.png)

データ ソースとソリューションは、独自のプロパティのセットを持つさまざまなレコードの種類をそれぞれ作成しますが、リポジトリへのクエリでまとめて分析することもできます。 これにより、同じツールと方法を使用して、異なるソースによって収集されたさまざまな種類のデータを操作できます。


Log Analytics とのやり取りのほとんどは、任意のブラウザーで実行する OMS ポータルを通じて行います。ここから構成設定や複数のツールにアクセスして収集したデータの分析や操作ができる機能が提供されます。 このポータルから、[ログ検索](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches)を使用して、クエリを作成して収集したデータを分析することができます。また、[ダッシュボード](https://docs.microsoft.com/azure/log-analytics/log-analytics-dashboards)からは、最も重要な検索のグラフィック表示をカスタマイズすることができます。さらに、[ソリューション](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)からその他の機能や分析ツールが提供されます。

![分析ツール](./media/azure-threat-detection/azure-threat-detection-fig6.png)

ソリューションにより、Log Analytics に機能が追加されます。 これらは主にクラウドで実行し、OMS リポジトリで収集されたデータの分析を提供します。 また、OMS ダッシュボードのソリューションによって提供されるログ検索または追加のユーザー インターフェイスで分析できる収集対象の新しいレコードの種類を定義することもできます。
[セキュリティおよび監査] はこのようなソリューションの一例です。



### <a name="automation--control-alert-on-security-configuration-drifts"></a>Automation と制御: セキュリティ構成の誤差に関するアラート

Azure Automation は、PowerShell に基づいた Runbook を使用して管理プロセスを自動化し、Azure クラウドで実行します。 ローカル データセンターのサーバーで Runbook を実行してローカル リソースを管理することもできます。 Azure Automation では、PowerShell DSC (Desired State Configuration) によって構成を管理します。

![Azure Automation](./media/azure-threat-detection/azure-threat-detection-fig7.png)

ユーザーは Azure でホストされる DSC リソースを作成または管理することができ、これをクラウドやオンプレミスのシステムに適用して定義したり、その構成を自動的に施行するか、セキュリティ構成がポリシー内に確実に留まるようにするために役立つ、誤差に関するレポートを取得することができます。

## <a name="azure-security-center"></a>[Azure Security Center] \(Azure Security Center)

Azure Security Center によって、Azure リソースを保護します。 Azure サブスクリプション間のセキュリティ監視とポリシー管理を総合的に提供します。 このサービス内では、Azure サブスクリプションに対してだけでなく、[リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)に対してもポリシーを定義できるため、より詳細な定義が可能になります。

![[Azure Security Center] \(Azure Security Center)](./media/azure-threat-detection/azure-threat-detection-fig8.png)

マイクロソフトのセキュリティ研究員は、絶えず脅威に目を光らせています。 クラウドやオンプレミスにおけるマイクロソフトのグローバル プレゼンスから得た広範なテレメトリにアクセスすることが彼らには許されています。 この広範かつ多様なデータセットのコレクションによって、マイクロソフトは、そのオンプレミスの消費者向け/企業向け製品からオンライン サービスに至るまで、攻撃の新しいパターンや傾向を把握することができます。

このため、Security Center は、攻撃者が新たにより高度な攻撃を仕掛けてくるたびに検出アルゴリズムを迅速に更新することができます。 この方法は、刻々と進化する脅威の環境に後れを取らないように対処するのに役立ちます。

![Security Center](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

Security Center の脅威検出は、Azure のリソースやネットワーク、接続されているパートナー ソリューションからセキュリティ情報を自動的に収集することによって機能します。  これは、複数のソースからの情報を関連付けることで情報を分析して脅威を識別します。
Security Center では、セキュリティの警告に優先順位が、脅威に対処するための推奨事項と共に割り当てられます。

Security Center には、シグネチャ ベースの手法とは比較にならない高度なセキュリティ分析が採用されています。 画期的なビッグ データと [Machine Learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) テクノロジを使用して、手動の方法では識別が不可能な脅威の検出と、攻撃の進化の予測によって、クラウド ファブリック全体のイベントを評価し、脅威を検出します。 こうしたセキュリティ分析には、次のようなものがあります。

### <a name="threat-intelligence"></a>[脅威インテリジェンス]

Microsoft は、膨大なグローバル脅威インテリジェンスを保有しています。
Azure、Office 365、Microsoft CRM online、Microsoft Dynamics AX、outlook.com、MSN.com、Microsoft Digital Crimes Unit (DCU)、および Microsoft セキュリティ レスポンス センター (MSRC) などの複数のソースから製品利用統計情報が送られてきます。

![[脅威インテリジェンス]](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

また研究員も、大手クラウド サービス プロバイダー間で共有されている脅威インテリジェンス情報を入手しているほか、サード パーティから脅威インテリジェンス フィードをサブスクライブしています。 Azure Security Center はこの情報を基に、既知の有害因子から生じる脅威について利用者に警告を発することができます。 次に例をいくつか示します。

-   **Machine Learning の力を利用する -**Azure Security Center は、クラウド ネットワークのアクティビティに関する大量のデータにアクセスし、このデータを使用して Azure デプロイメントを対象とする脅威を検出することができます。 次に例を示します。

-   **ブルート フォース検出 -** Machine learning を使用してリモート アクセスが試行された履歴のパターンを作成します。これによって SSH、RDP、および SQL の各ポートに対するブルート フォース攻撃を検出できます。

-   **DDoS およびボットネット送信の検出** - クラウド リソースを対象とした攻撃の主な目的は、これらのリソースの計算機能を使用して他の攻撃を実行することです。

-   **新しい行動分析サーバーおよび VM -** サーバーまたは仮想マシンが一度侵害されると、攻撃者は、検出を逃れながら繰り返しセキュリティ制御を回避しながら、そのシステムに対して悪意のあるコードを実行するためにさまざまな手法を仕掛けます。

-   **Azure SQL データベースの脅威の検出 -** Azure SQL Database の脅威の検出では、データベースへのアクセスや攻撃を目的とした通常とは異なる潜在的に有害な試行であることを示す、異常なデータベースのアクティビティが識別されます。

### <a name="behavioral-analytics"></a>行動分析

行動分析は、データを分析し、既知のパターンのコレクションと照らして比較する手法です。 ただし、これらのパターンはただのシグネチャではありません。 大量のデータセットに適用された複雑な機械学習アルゴリズムを通じてパターンが決定されています。

![行動分析](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)


また、パターンの特定にあたっては、専門のアナリストによって悪質な行動が緻密に分析されます。 Azure Security Center は、行動分析を使用して仮想マシンのログ、仮想ネットワーク デバイスのログ、ファブリック ログ、クラッシュ ダンプ、およびその他のソースを分析し、これに基づいて侵害されたリソースを識別することができます。

また、他のシグナルとの間には、蔓延している攻撃の裏付けとなる兆候を確認できる相関関係が存在します。 この相関関係によって、セキュリティ侵害の証拠として確立されたインジケーターと一致しているイベントが特定されます。

次に例をいくつか示します。
-   **不審なプロセスの実行:** 攻撃者がいくつかの手法を使用して悪意のあるソフトウェアを検出されずに実行しています。 たとえば、攻撃者は正当なシステム ファイルと同じ名前をマルウェアに付けて通常とは異なる場所に配置したり、まるで害がないように見えるファイル名を使用したり、ファイルの本来の拡張子をマスクしたりします。 Security Center はプロセスの動作をモデル化し、そのプロセスの実行を監視することで、こうした異常を検出します。

-   **マルウェアおよび脆弱性の悪用の試行を非表示にする:** 巧妙なマルウェアは、ディスクへの書き込みやディスクに格納されているソフトウェア コンポーネントの暗号化をまったくしないために、従来のマルウェア対策製品から逃れることができます。 しかし、マルウェアは動作するためにメモリにトレースを残すため、メモリを分析するとこのようなマルウェアを検出できます。 ソフトウェアがクラッシュすると、クラッシュ時のメモリが部分的にクラッシュ ダンプにキャプチャされます。 クラッシュ ダンプにメモリを分析することで、Azure Security Center は、ソフトウェアの脆弱性を悪用したり、機密データにアクセスしたり、侵害したマシン内部でひそかに生き残るために使用されたテクニックを、コンピューターのパフォーマンスに影響を与えずに検出することができます。

-   **水平移動と内部偵察:** 攻撃者は、侵害されたネットワークで存続またはこのようなネットワークを探して貴重なデータを奪取するために、侵害されたマシンから同一ネットワーク内の他のマシンに水平移動を試みる場合がよくあります。 Security Center は、攻撃者がネットワーク内部で足がかりを拡大するために試行するリモート コマンドの実行、ネットワーク プローブ、アカウントの列挙などを見つけるために、プロセスとログインのアクティビティを監視します。

-   **悪質な PowerShell スクリプト**: 攻撃者が標的となる仮想マシン上で悪質なコードを実行するために、さまざまな目的から PowerShell が使用されることがあります。 疑わしい活動の兆候がないか、PowerShell のアクティビティは Security Center によって調査されます。

-   **送信方向の攻撃:** 攻撃の踏み台として利用するために、攻撃者がクラウド リソースを標的にすることは少なくありません。 たとえば、侵入した仮想マシンを利用して、他の仮想マシンにブルート フォース攻撃を仕掛けたり、スパムを送信したり、開放ポートやインターネット上の他のデバイスをスキャンしたりする場合があります。 Security Center では、ネットワーク トラフィックに機械学習を適用することで、正常な範囲を逸脱した送信ネットワーク通信を検出することができます。 さらに、スパムで、普通ではない電子メール トラフィックと Office 365 から得られたインテリジェンスとの相関を明らかにし、不正目的のメールであるか、正当な電子メール キャンペーンによるものであるかを判断します。

### <a name="anomaly-detection"></a>異常検出

Azure Security Center での脅威の特定には、異常検出も使用されます。 行動分析は、大規模なデータセットから導いた既知のパターンに依存します。これとは対照的に、異常検出は "独自色" が強く、個々の環境に固有の基準に重点が置かれます。 機械学習を適用して個々の環境の正常なアクティビティを突き止めたうえで、セキュリティ イベントの可能性を示す異常な条件とは何かを定義する規則を作成します。 次に例を示します。

-   **受信方向の RDP/SSH ブルート フォース攻撃**: 同じ環境でも、日々多数のユーザーがログインする稼働率の高い仮想マシンもあれば、ほとんどまたはまったくログインされない仮想マシンもあります。 Azure Security Center は、これらの仮想マシンにおける基準となるログイン アクティビティを決定したり、機械学習で通常のログイン アクティビティの範囲を定義することができます。 ログインに関連する特性で基準との不一致がある場合は、アラートが生成される場合があります。 前述のように、何をもって有意とするかは、機械学習によって突き止められます。

### <a name="continuous-threat-intelligence-monitoring"></a>脅威インテリジェンスの継続的監視

Azure Security Center は、世界中のセキュリティ リサーチ チームやデータ サイエンス チームと連携して脅威に関する状況の変化を絶えず監視しています。 たとえば次のような取り組みが行われています。

-   **脅威インテリジェンスの監視**: 脅威インテリジェンスには、既存の脅威や新たに発生した脅威に関するメカニズム、インジケーター、示唆、即時に利用可能なアドバイスが含まれます。 こうした情報はセキュリティ コミュニティから得られるほか、マイクロソフトも、社内や社外のソースから提供される脅威インテリジェンスを絶えず監視しています。

-   **シグナルの共有**: クラウドとオンプレミス向けに Microsoft が保有するさまざまなサービス、サーバー、クライアント エンドポイント デバイスを通じてセキュリティ チームが得た知見は共有され、分析されます。

-   **Microsoft のセキュリティ スペシャリスト**: フォレンジクスや Web 攻撃検出など、専門のセキュリティ分野に従事する Microsoft 内のさまざまなチームと絶えず連携します。

-   **検出のチューニング**: 実際のユーザーのデータセットに対してアルゴリズムが実行され、セキュリティ研究員がユーザーと連携してその結果を検証します。 機械学習アルゴリズムの精度を高めるために、真陽性と偽陽性が使用されます。

こうしたさまざまな取り組みを通じて、これまでできなかったような検出が可能となり、検出の精度が向上しています。しかもその恩恵はすぐに得ることができます。ユーザーが措置を講じる必要はありません。

## <a name="advanced-threat-detection-features---other-azure-services"></a>高度な脅威検出機能 - その他の Azure サービス

### <a name="virtual-machine-microsoft-antimalware"></a>仮想マシンの場合: Microsoft マルウェア対策

Azure の [Microsoft マルウェア対策](https://docs.microsoft.com/azure/security/azure-security-antimalware)は、アプリケーションやテナント環境のための単一エージェント ソリューションであり、ユーザーの介入なしにバック グラウンドで実行するように設計されています。 アプリケーションのワークロードのニーズに基づいて、マルウェア対策監視など、基本的な既定のセキュリティまたは高度なカスタム構成で、保護をデプロイできます。 Azure のマルウェア対策は、Azure Virtual Machines のセキュリティ オプションであり、すべての Azure PaaS の仮想マシンに自動的にインストールされます。

**Microsoft マルウェア対策をアプリケーション用にデプロイおよび有効にする Azure の機能**

#### <a name="microsoft-antimalware-core-features"></a>Microsoft マルウェア対策の主な機能

-   **リアルタイム保護** - Cloud Services および Virtual Machine でのアクティビティを監視し、マルウェアの実行を検出してブロックします。

-   **スケジュールに基づくスキャン** - 特定対象のスキャンを定期的に実行し、マルウェアや活動量の多いプログラムを検出します。

-   **マルウェアの駆除** - 悪意のあるファイルの削除や検疫、悪意のあるレジストリ エントリのクリーンアップなど、検出されたマルウェアへの対処を自動的に行います。

-   **シグネチャの更新** - 最新の保護シグネチャ (ウイルスの定義) を自動的にインストールし、事前に定義された頻度で保護を最新の状態に更新します。

-   **マルウェア対策エンジンの更新** - Microsoft マルウェア対策エンジンを自動的に更新します。

-   **マルウェア対策プラットフォームの更新** - Microsoft マルウェア対策プラットフォームを自動的に更新します。

-   **アクティブ保護** - 検出された脅威および疑わしいリソースに関するテレメトリ メタデータを Microsoft Azure に報告して発生中の脅威に迅速に対応し、さらに、Microsoft Active Protection System (MAPS) を使用して同期されたシグネチャをリアルタイムで配信できるようにします。

-   **サンプルのレポート** - Microsoft マルウェア対策サービスにサンプルを提供および報告し、サービスの調整およびトラブルシューティングを可能にします。

-   **除外** – パフォーマンスやその他の理由で、アプリケーションおよびサービスの管理者が、特定のファイル、プロセス、およびドライブを保護やスキャンの対象から除外できるようにします。

-   **マルウェア対策イベントの収集** - マルウェア対策サービスの状態、疑わしいアクティビティ、および実行された修復アクションをオペレーティング システムのイベント ログに記録し、顧客の Azure Storage アカウントにそれらを収集します。

### <a name="azure-sql-database-threat-detection"></a>Azure SQL Database の脅威の検出

[Azure SQL データベースの脅威の検出](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)は、Azure SQL Database サービスに組み込まれている、新しいセキュリティ インテリジェンス機能です。 Azure SQL Database の脅威の検出は、データベースの異常なアクティビティについて学習、プロファイル、および検出を一日中行いながら、データベースへの潜在的な脅威を識別します。

セキュリティ責任者や他の指定された管理者は、不審なデータベースのアクティビティが発生すると、直ちに通知を受け取ることができます。 それぞれの通知では、不審なアクティビティの詳細情報と、脅威に対して推奨されるさらなる調査方法や軽減策が記載されています。

現時点では、Azure SQL Database の脅威の検出では、潜在的な脆弱性および SQL インジェクション攻撃、および異常なデータベース アクセスのパターンを検出します。

脅威の検出の電子メール通知を受け取ると、ユーザーは、メール内の監査ビューアーを開くディープ リンクを使用して関連の監査レコードに移動して表示することができます。または、事前定義された監査 Excel テンプレートを使用して、不審なイベントの発生時近くに記録された、次のような関連の監査レコードを表示することもできます。
-   異常なデータベース アクティビティを伴うデータベース/サーバーの監査ストレージ

-   監査ログの書き込みイベントの時刻に使用された、関連の監査ストレージ テーブル

-   イベント発生後 1 時間の監査レコード

-   イベントの発生時における、同様のイベント ID の監査レコード (一部の検出では省略可能)

SQL Database の脅威検出機能は、次の検出方法論を使用します。

-   **確定的な検出 –**既知の攻撃に一致する不審なパターン (ルール ベース) を SQL クライアントのクエリで検出します。 この方法論で検出される確率は高く、誤判定率は低くなります。ただし、「アトミック検出」のカテゴリに含まれるため範囲は制限されています。

-   **動作検出 –**過去 30 日間に見られなかった、データベースの異常な動作を示す、異常なアクティビティを検出します。  SQL クライアントの異常なアクティビティの例には、失敗したログイン/クエリ、大量のデータ抽出、通常でない Canonical クエリ、データベースへのアクセスに使用された特殊な IP アドレスの急増があります。

### <a name="application-gateway-web-application-firewall"></a>Application Gateway の Web アプリケーション ファイアウォール

[Web アプリケーション ファイアウォール](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)は、[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) の機能で、標準の[アプリケーション配信コントロール機能](https://kemptechnologies.com/in/application-delivery-controllers)に対してアプリケーション ゲートウェイを使用して、Web アプリケーションを保護します。 Web アプリケーション ファイアウォールは、[OWASP の上位 10 件の一般的 Web 脆弱性](https://www.owasp.org/index.php/Top_10_2010-Main)の大部分に対する保護を提供することで、これを実現します。

![Application Gateway の Web アプリケーション ファイアウォール](./media/azure-threat-detection/azure-threat-detection-fig13.png)

-   SQL インジェクションからの保護

-   クロス サイト スクリプティングからの保護

-   一般的な Web 攻撃からの保護 (コマンド インジェクション、HTTP 要求スマグリング、HTTP レスポンス スプリッティング、リモート ファイル インクルード攻撃など)

-   HTTP プロトコル違反に対する保護

-   HTTP プロトコル異常に対する保護 (ホスト ユーザー エージェントと承認ヘッダーが見つからない場合など)

-   ボット、クローラー、スキャナーの防止

-   一般的なアプリケーション構成ミスの検出 (Apache、IIS など)

Application Gateway に WAF を構成することには、次のようなメリットがあります。

-   バックエンド コードを変更しなくても、Web アプリケーションを Web の脆弱性および攻撃から保護できます。

-   アプリケーション ゲートウェイの内側にある複数の Web アプリケーションを同時に保護できます。 アプリケーション ゲートウェイは、1 つのゲートウェイの内側で最大 20 個の Web サイトをホスティングし、すべてを Web 攻撃から保護できます。

-   アプリケーション ゲートウェイの WAF ログで生成されたリアルタイムのレポートを使用して、Web アプリケーションに対する攻撃を監視できます。

-   特定のコンプライアンス制御では、インターネットに接続するすべてのエンド ポイントを WAF ソリューションで保護する必要があります。 WAF が有効になったアプリケーション ゲートウェイを使用することで、これらのコンプライアンス要件を満たすことができます。

### <a name="anomaly-detection--an-api-built-with-azure-machine-learning"></a>Azure Machine Learning で構築される異常検出 API

Azure Machine Learning で構築される異常検出は、時系列データでさまざまな種類の異常なパターンを検出するために役立つ API です。 API は、時系列の各データ ポイントに異常なスコアを割り当てます。これを使用してアラート生成、ダッシュボード内の監視、および、チケット発行システムとの接続ができます。

[異常検出 API](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) が検出できる時系列の異常のタイプには次のものがあります。

-   **急増と急減:** たとえば、サービスへのログインの失敗数や電子商取引サイトのチェックアウトの数を監視している場合は、急増や急減はセキュリティ攻撃やサービス障害を示している可能性があります。

-   **正と負の傾向:** コンピューティングのメモリ使用量を監視する場合、たとえば、空きメモリのサイズの圧縮はメモリ リークの可能性を示している場合があり、サービス キューの長さの監視では、永続的な上昇傾向は、基のソフトウェアに問題が発生している可能性があります。

-   **レベルの変化と動的範囲値の変化:** たとえば、サービスのアップグレード後にサービス遅延のレベルが変化した場合や、アップグレード後の例外のレベルが低下した場合は、監視する価値があります。

API に基づいた機械学習では、次のことができます。

-   **柔軟かつ堅牢な検出:** 異常検出モデルを使用することで、機密度設定の構成や、季節依存または季節に依存しない異常を検出できます。 ユーザーは、自身のニーズに合わせて、検出 API の感度を強化または低下させることで異常検出モデルを調整することができます。 つまり、季節のパターンの有無によって、検出によるデータの異常性が見えやすくなったり見えにくくなります。

-   **スケーラブルかつタイムリーな検出:** 専門家のドメイン知識で設定されるしきい値表示で監視する従来の方法はコストがかかるうえ、動的に変化する数百万件のデータ セットに対応するための拡張性がありません。 この API を使用する異常検出モデルには学習機能があり、過去とリアルタイムの両方のデータから自動的にモデルを調整します。

-   **プロアクティブでアクションにつながる検出:** 動きの遅い傾向とレベルの変更の検出は、初期の異常検出に適用できます。 早期に異常な信号を検出することで、人による調査を指示し、問題の発生領域でアクションを起こすことができます。  さらに、問題の原因を分析して、この異常検出 API サービスに追加するアラート用のツールを開発することができます。

異常検出 API は、サービスの使用状況や KPI の監視、IoT、パフォーマンスの監視、およびネットワーク トラフィックの監視といったさまざまなシナリオで効果的かつ効率的なソリューションです。 次に、この API が役に立ついくつかの一般的なシナリオを示します。
- IT 部門には、イベントの追跡、エラー コード、使用状況のログ、およびパフォーマンス (CPU、メモリ) を適切なタイミングで追跡するツールが必要です。

-   オンラインの商用サイトは、顧客活動、ページ ビュー、クリック数などの情報を追跡する必要があります。

-   公益事業の関連企業は、水道、ガス、電力、その他のリソースの消費量を追跡する必要があります。

-   設備や建築物の管理サービス企業は、温度、湿度、トラフィックなどを監視する必要があります。

-   IoT/製造業者は、ワークフローや品質などを時系列で監視するためのセンサー データを使用する必要があります。

-   コール センターなどのサービス プロバイダーは、サービス要求の傾向、インシデントのボリューム、待機キューの長さなどを監視する必要があります。

-   ビジネス分析グループは、事業 KPI (売上高、顧客センチメント、価格設定など)、異常な動作をリアルタイムで監視する必要があります。

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) は、Microsoft Cloud のセキュリティ スタックの重要なコンポーネントです。 これは、企業がクラウド アプリケーションの可能性を最大限に活用できるように行動するのに役立つ包括的なソリューションですが、アクティビティのさらなる詳細を把握することで制御は維持されます。 また、重要なデータの保護をクラウド アプリケーション全体で強化するうえでも役立ちます。

企業は、shadow IT の発見、リスクの評価、ポリシーの適用、アクティビティの調査、および驚異の停止に役立つツールを使用して、重要なデータの制御を維持しながらもより安全にクラウドに移行できるようにします。

<table style="width:100%">
 <tr>
   <td>発見</td>
   <td>Cloud App Security を使用して shodow IT を発見します。 クラウド環境でアプリ、アクティビティ、ユーザー、データ、およびファイルを発見して可視性を獲得します。 クラウドに接続しているサード パーティのアプリを検出します。</td>
 </tr>
 <tr>
   <td>調査</td>
   <td>クラウド フォレンジック ツールを使用してクラウド アプリケーションを調査します。具体的には、ネットワーク内の高リスク アプリケーション、特定のユーザー、およびファイルに関する詳しい調査を行います。 クラウドから収集されたデータのパターンを検索します。 クラウドを監視するレポートを生成します。</td>

 </tr>
 <tr>
   <td>コントロール</td>
   <td>ネットワーク クラウドのトラフィック全体を最大限に制御するためのポリシーとアラートを設定してリスクを軽減します。 Cloud App Security を使用して、安全で承認された代替クラウド アプリにユーザーが移行できるようにします。</td>

 </tr>
 <tr>
   <td>保護</td>
   <td>Cloud App Security を使用して、アプリケーションの承認と禁止、データ損失の防止、アクセスと共有の制御、およびカスタム レポートやアラートの生成を行います。</td>

 </tr>
 <tr>
   <td>コントロール</td>
   <td>ネットワーク クラウドのトラフィック全体を最大限に制御するためのポリシーとアラートを設定してリスクを軽減します。 Cloud App Security を使用して、安全で承認された代替クラウド アプリにユーザーが移行できるようにします。</td>

 </tr>
</table>


![Cloud App Security](./media/azure-threat-detection/azure-threat-detection-fig14.png)

Cloud App Security は、次の作業によってクラウドの詳細を把握します。

-   Cloud Discovery を使用して、組織が使用しているクラウド環境とクラウド アプリをマップおよび識別します。


-   クラウドでのアプリの使用を承認および禁止します。



-   デプロイが容易で、プロバイダー API を活用するアプリ コネクタを使用して、接続しているアプリの詳細を把握し、管理します。

-   ポリシーを設定して、その後の微調整を行うことで、継続的に制御できるようにします。

Cloud App Security は、これらのソースからデータを収集することでデータに対する高度な分析を実行します。 Cloud App Security は、異常なアクティビティに迅速に通知し、クラウド環境をさらに詳しく把握できるようにします。 Cloud App Security にポリシーを構成することができ、これを使用して、クラウド環境内のすべてを保護することができます。

## <a name="third-party-atd-capabilities-through-azure-marketplace"></a>Azure Marketplace を通じたサードパーティ ATD の機能

### <a name="web-application-firewall"></a>Web アプリケーション ファイアウォール

Web アプリケーション ファイアウォールは、着信する Web トラフィックを検査して、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、アプリケーション DDoS など、Web アプリケーションを対象とした攻撃をブロックします。 さらに、データ損失防止 (DLP) のためにバックエンド Web サーバーからの応答を検査します。 管理者は、統合アクセス制御エンジンを使用して認証、承認、およびアカウンティング (AAA) に対応するきめ細かなアクセス制御ポリシーを作成することができ、これによって企業は強力な認証とユーザー制御の機能を得ることができます。

**ハイライト:**
-   アプリケーションに対する SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、アプリケーション DDoS、その他の攻撃を検出してブロックします。

-   認証およびアクセス制御。

-   発信トラフィックをスキャンして機密データを検出し、情報漏洩を防ぐためにマスクやブロックを行います。

-   Web アプリケーションのコンテンツ配信を、キャッシュ、圧縮、その他のトラフィックの最適化機能などを使用して高速化します。

Azure Marketplace で利用可能な Web アプリケーション ファイアウォールの使用例を次に示します。

[Barracuda Web アプリケーション ファイアウォール、Brocade 仮想 Web アプリケーション ファイアウォール (Brocade vWAF)、Imperva SecureSphere および ThreatSTOP IP ファイアウォールがあります。](https://azure.microsoft.com/marketplace/partners/brocade_communications/brocade-virtual-web-application-firewall-templatevtmcluster/)

## <a name="next-steps"></a>次のステップ

- [Azure Security Center の検出機能](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)

Azure Security Center の高度な検出機能を使用すると、Microsoft Azure リソースをターゲットとするアクティブな脅威を特定し、迅速に対応するうえで必要な知見を得ることができます。

- [Azure SQL Database の脅威の検出](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)

Azure SQL Database の脅威の検出は、そのデータベースに対する潜在的な脅威の問題に対処するのに役立ちます。

