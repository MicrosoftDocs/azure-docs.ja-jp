---
title: Azure の高度な脅威検出 | Microsoft Docs
description: Azure AD Identity Protection とその機能について説明します。
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 2a6a0e6219a45821e2a4416a4e563aa6edb86eba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67127166"
---
# <a name="azure-advanced-threat-detection"></a>Azure の高度な脅威検出

Azure では、Azure Active Directory (Azure AD)、Azure Monitor ログ、Azure Security Center などのサービスを通じて、高度な脅威検出を行う組み込み機能を提供しています。 このセキュリティ サービスと機能のコレクションにより、Azure デプロイの内部で起きている事象をシンプルかつ迅速に把握する方法が提供されます。

Azure では、アプリのデプロイ要件を満たすセキュリティを構成およびカスタマイズするための幅広いオプションを提供します。 この記事では、これらの要件を満たす方法について説明します。

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) は、[Azure Active Directory Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) エディションの機能であり、リスク イベントと組織の ID に影響する可能性がある潜在的な脆弱性に関する概要を提供します。 Identity Protection は、[Azure AD 異常アクティビティ レポート](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports)を通じて利用できる、既存の Azure AD 異常検出機能を使用して、リアルタイムの異常を検出できる新しいリスク イベントの種類を紹介します。

![Azure AD Identity Protection の図](./media/azure-threat-detection/azure-threat-detection-fig1.png)

Identity Protection ではアダプティブな機械学習アルゴリズムとヒューリスティックを使用して、ID が侵害されていることを示している可能性のある異常とリスク イベントを検出します。 このデータを使用して、Identity Protection によりレポートとアラートが生成されます。ユーザーはこれらのリスク イベントを調査して、適切な修復または軽減のアクションを実行することができます。

Azure Active Directory Identity Protection は単なる監視とレポート作成のツールではありません。 リスク イベントを基にして、Identity Protection では各ユーザーのユーザー リスク レベルを計算します。これにより、リスク ベースのポリシーを構成して組織の ID を自動的に保護することができます。

これらのリスクに基づくポリシーと、Azure Active Directory および [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) によって提供される他の[条件付きアクセス コントロール](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access)により、パスワードのリセットや多要素認証の適用などのアダプティブ修復アクションを自動的にブロックまたは提供できます。

### <a name="identity-protection-capabilities"></a>Identity Protection の機能

Azure Active Directory Identity Protection は単なる監視とレポート作成のツールではありません。 指定したリスク レベルに達したときに、検出された問題が自動的に対処されるようにリスク ベースのポリシーを構成することで、組織の ID を保護できます。 こうしたポリシーと、Azure Active Directory および EMS によって提供される他の条件付きアクセス コントロールにより、パスワードのリセットや多要素認証の適用などのアダプティブ修復アクションを自動的にブロックまたは開始できます。

Azure の Identity Protection によってユーザーのアカウントや ID を保護できる方法の例には次のようなものがあります。

[リスク イベントとリスクの高いアカウントの検出](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   機械学習とヒューリスティック ルールを使用して 6 種類のリスク イベントを検出します。
-   ユーザー リスク レベルを計算します。
-   脆弱性を目立たせることにより全体的なセキュリティ対策を向上させるためのカスタム推奨事項を提供します。

[リスク イベントの調査](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   リスク イベントの通知を送信します。
-   関連情報とコンテキスト情報を使用してリスク イベントを調査します。
-   調査を追跡するための基本的なワークフローを提供します。
-   パスワード リセットなどの修復アクションへの簡単なアクセスを提供します。

[リスクに基づく条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
-   サインインのブロックまたは多要素認証チャレンジの要求によりリスクの高いサインインを抑制します。
-   リスクの高いユーザー アカウントをブロックまたはセキュリティで保護します。
-   多要素認証への登録をユーザーに要求します。

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

組織内のアクセスは、[Azure Active Directory Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) を使用して管理、制御、監視することができます。 その機能には、Azure AD をはじめとする Microsoft Online Services (Office 365、Microsoft Intune など) 内のリソースへのアクセスが含まれます。

![Azure AD Privileged Identity Management の図](./media/azure-threat-detection/azure-threat-detection-fig2.png)

PIM は以下のことに役立ちます。

-   Azure AD 管理者に関するアラートとレポートを受け取り、Office 365 や Intune などの Microsoft Online Services にジャスト イン タイム (JIT) で管理アクセスを行う。

-   管理者のアクセス履歴と管理者の割り当ての変更に関するレポートを取得する。

-   特権ロールへのアクセスに関するアラートを受け取る。

## <a name="azure-monitor-logs"></a>Azure Monitor ログ

[Azure Monitor ログ](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)は、オンプレミスのインフラストラクチャやクラウド インフラストラクチャの管理および保護に役立つ、Microsoft クラウドベースの IT 管理ソリューションです。 Azure Monitor ログはクラウドベースのサービスとして実装されるため、インフラストラクチャ サービスに最小限の投資をするだけで、すぐに稼働させることができます。 新しいセキュリティ機能は自動的に配信されるため、継続的なメンテナンスやアップグレードのコストが節約されます。

Azure Monitor ログは、価値のある独自のサービスに加え、[System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/) などの System Center のコンポーネントと連携して、セキュリティ管理のための既存の投資をクラウドに拡張できます。 System Center と Azure Monitor ログを連携させることで、本格的なハイブリッド管理を実現できます。

### <a name="holistic-security-and-compliance-posture"></a>包括的なセキュリティおよびコンプライアンスの体制

[Log Analytics の [セキュリティおよび監査] ダッシュボード](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)では、注意を必要とする重要な問題向けの組み込みの検索クエリと共に、組織の IT セキュリティ対策への包括的な視点が提供されます。 [セキュリティおよび監査] ダッシュボードは、Azure Monitor ログにおけるすべてのセキュリティ関連機能のホーム画面です。 この画面では、コンピューターのセキュリティの状態について大まかな情報を得ることができます。 また、過去の 24 時間、7 日間、またはそれ以外のカスタム期間に発生したすべてのイベントを表示することができます。

Azure Monitor ログのダッシュボードは、あらゆる環境における包括的なセキュリティ体制をすばやく簡単に理解するのに役立ちます。これはすべて、ソフトウェアの更新プログラムの評価、マルウェア対策の評価、構成基準などを含む IT オペレーションのコンテキストの中で行われます。 セキュリティ ログ データに容易にアクセスできるため、セキュリティとコンプライアンスを目的とする監査プロセスが合理化されます。

![Log Analytics の [セキュリティおよび監査] ダッシュボード](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

Log Analytics の [セキュリティおよび監査] ダッシュボードは、次の 4 つの主要カテゴリで構成されています。

-   **セキュリティ ドメイン**: 時間の経過に伴うセキュリティ レコードを詳しく調査できます。また、マルウェアの評価へのアクセス、評価の更新、ネットワーク セキュリティ、ID、アクセス情報の表示、セキュリティ イベントが発生したコンピューターの表示を行うことができ、Azure Security Center のダッシュボードにも簡単にアクセスできます。

-   **注目に値する問題**: アクティブな問題の数と問題の重要度をすばやく特定できます。

-   **検出 (プレビュー)** : リソースに対して攻撃が発生したときにセキュリティ アラートを表示することで、攻撃パターンを特定できます。

-   **脅威インテリジェンス**: 悪意のあるアウトバウンド IP トラフィックを持つサーバーの総数、悪意のある脅威の種類、IP の場所のマップを表示することで、攻撃パターンを特定できます。

-   **一般的なセキュリティ クエリ**: 環境を監視するために使用できる最も一般的なセキュリティ クエリが一覧表示されます。 任意のクエリを選択すると、[検索] ウィンドウが開き、そのクエリの結果が表示されます。

### <a name="insight-and-analytics"></a>分析情報と分析
[Azure Monitor ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)の核となる機能は、Azure でホストされているリポジトリです。

![分析情報と分析の図](./media/azure-threat-detection/azure-threat-detection-fig4.png)

データ ソースを構成し、ソリューションをサブスクリプションに追加することによって、接続されているソースからリポジトリにデータを収集します。

![Azure Monitor ログ ダッシュ ボード](./media/azure-threat-detection/azure-threat-detection-fig5.png)

データ ソースとソリューションは、独自のプロパティのセットを持つレコードの種類をそれぞれ別々に作成しますが、リポジトリに対するクエリでまとめて分析することもできます。 同じツールとメソッドを使用することで、さまざまなソースによって収集された各種のデータを使用することができます。


Azure Monitor ログとのやり取りのほとんどは、任意のブラウザーで実行する Azure portal を通じて行います。ここから構成設定や複数のツールにアクセスして収集したデータの分析や操作ができる機能が提供されます。 ポータルでは、以下を使用できます。
* [ログ検索](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches)。収集されたデータを分析するためのクエリを構築します。
* [ダッシュボード](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)。最も重要な検索のグラフィカル表示でカスタマイズできます。
* [ソリューション](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)。追加の機能と分析ツールが提供されます。

![分析ツール](./media/azure-threat-detection/azure-threat-detection-fig6.png)

ソリューションにより、Azure Monitor ログに機能が追加されます。 これらは主にクラウドで実行し、Log Analytics リポジトリで収集されたデータの分析を提供します。 また、ソリューションでは、新しいレコードの種類を収集対象として定義することもできます。それらは Log Analytics ダッシュボード内でソリューションによって提供される追加のユーザー インターフェイスを使用するか、ログ検索を使用して分析できます。

[セキュリティおよび監査] ダッシュボードはこのような種類のソリューションの一例です。

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automation and Control: セキュリティ構成の誤差に関するアラート

Azure Automation は、PowerShell に基づいた Runbook を使用して管理プロセスを自動化し、クラウドで実行します。 ローカル データセンターのサーバーで Runbook を実行してローカル リソースを管理することもできます。 Azure Automation では、PowerShell Desired State Configuration (DSC) によって構成を管理します。

![Azure Automation の図](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Azure でホストされる DSC リソースを作成して管理し、クラウドやオンプレミス システムに適用できます。 そのためには、その構成を定義して自動的に適用するか、セキュリティ構成がポリシー内に確実に留まるようにするのに役立つ誤差に関するレポートを取得することができます。

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center によって、Azure リソースを保護します。 Azure サブスクリプション間のセキュリティ監視とポリシー管理を総合的に提供します。 このサービス内では、Azure サブスクリプションと[リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)の両方に対してポリシーを定義することで、より詳細な定義が可能になります。

![Azure Security Center の図](./media/azure-threat-detection/azure-threat-detection-fig8.png)

マイクロソフトのセキュリティ研究員は、絶えず脅威に目を光らせています。 クラウドやオンプレミスにおけるマイクロソフトのグローバル プレゼンスから得た広範なテレメトリにアクセスすることが彼らには許されています。 この広範かつ多様なデータセットのコレクションによって、マイクロソフトは、そのオンプレミスの消費者向け/企業向け製品からオンライン サービスに至るまで、攻撃の新しいパターンや傾向を把握することができます。

このため、Security Center は、攻撃者が新たにより高度な攻撃を仕掛けてくるたびに検出アルゴリズムを迅速に更新することができます。 この方法は、刻々と進化する脅威の環境に後れを取らないように対処するのに役立ちます。

![Security Center の脅威検出](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

Security Center の脅威検出は、Azure のリソースやネットワーク、接続されているパートナー ソリューションからセキュリティ情報を自動的に収集することによって機能します。 これは、複数のソースからの情報を関連付けることで情報を分析して脅威を識別します。

Security Center では、セキュリティの警告に優先順位が、脅威に対処するための推奨事項と共に割り当てられます。

Security Center には、シグネチャ ベースの手法とは比較にならない高度なセキュリティ分析が採用されています。 ビッグ データおよび[機械学習](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/)における革新的テクノロジを使用して、クラウド ファブリック全体でイベントが評価されます。 高度な分析では、手作業に頼った手法や攻撃の進化を予測する手法では特定できない脅威も検出できます。 こうしたセキュリティ分析の種類については次のセクションで説明します。

### <a name="threat-intelligence"></a>脅威インテリジェンス

Microsoft は、膨大なグローバル脅威インテリジェンスにアクセスできます。

Azure、Office 365、Microsoft CRM online、Microsoft Dynamics AX、outlook.com、MSN.com、Microsoft Digital Crimes Unit (DCU)、および Microsoft セキュリティ レスポンス センター (MSRC) などの複数のソースから製品利用統計情報が送られてきます。

![脅威インテリジェンスの検索結果](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

また研究員も、大手クラウド サービス プロバイダー間で共有されている脅威インテリジェンス情報を入手しているほか、サード パーティから脅威インテリジェンス フィードをサブスクライブしています。 Azure Security Center はこの情報を基に、既知の有害因子から生じる脅威について利用者に警告を発することができます。 次に例をいくつか示します。

-   **機械学習の力の利用**: Azure Security Center は、クラウド ネットワークのアクティビティに関する大量のデータにアクセスし、このデータを使用して Azure のデプロイを対象とする脅威を検出することができます。

-   **ブルート フォース検出**: 機械学習を使用してリモート アクセスが試行された履歴のパターンを作成します。これによって Secure Shell (SSH)、Remote Desktop Protocol (RDP)、および SQL の各ポートに対するブルート フォース攻撃を検出できます。

-   **アウトバウンド DDoS およびボットネットの検出**: クラウド リソースを対象とした攻撃の主な目的は、これらのリソースの計算機能を使用して他の攻撃を実行することです。

-   **新しい行動分析サーバーおよび VM**: サーバーまたは仮想マシンが一度侵害されると、攻撃者は、検出を逃れ、繰り返しセキュリティ制御を回避しながら、そのシステムに対して悪意のあるコードを実行するためにさまざまな手法を仕掛けます。

-   **Azure SQL Database の脅威の検出**: Azure SQL Database の脅威の検出では、データベースへのアクセスや攻撃を目的とした通常とは異なる潜在的に有害な試行であることを示す、異常なデータベースのアクティビティが識別されます。

### <a name="behavioral-analytics"></a>行動分析

行動分析は、データを分析し、既知のパターンのコレクションと照らして比較する手法です。 ただし、これらのパターンはただのシグネチャではありません。 大量のデータセットに適用された複雑な機械学習アルゴリズムを通じてパターンが決定されています。

![行動分析の検索結果](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)

また、パターンの特定にあたっては、専門のアナリストによって悪質な行動が緻密に分析されます。 Azure Security Center は、行動分析を使用して仮想マシンのログ、仮想ネットワーク デバイスのログ、ファブリック ログ、クラッシュ ダンプ、およびその他のソースを分析し、これに基づいて侵害されたリソースを識別することができます。

また、他のシグナルとの間には、蔓延している攻撃の裏付けとなる兆候を確認できるパターンが関連付けられています。 この相関関係によって、セキュリティ侵害の証拠として確立されたインジケーターと一致しているイベントが特定されます。

次に例をいくつか示します。
-   **疑わしいプロセスの実行**:攻撃者は、悪質なソフトウェアを秘かに実行するためにいくつかの手法を用います。 たとえば、マルウェアに対して正規のシステム ファイルと同じ名前を付けて本来とは異なる場所に配置したり、無害なファイルの名前に類似した名前を使用したり、ファイルの本当の拡張子を隠したりすることが考えられます。 Security Center はプロセスの動作をモデル化し、そのプロセスの実行を監視することで、こうした異常を検出します。

-   **マルウェアおよび脆弱性の悪用の試行を非表示にする**:巧妙なマルウェアは、ディスクへの書き込みやディスクに格納されているソフトウェア コンポーネントの暗号化をまったくしないために、従来のマルウェア対策製品から逃れることができます。 しかし、マルウェアは動作するためにメモリにトレースを残すので、メモリを分析するとこのようなマルウェアを検出できます。 ソフトウェアがクラッシュすると、クラッシュ時のメモリが部分的にクラッシュ ダンプにキャプチャされます。 クラッシュ ダンプにメモリを分析することで、Azure Security Center は、ソフトウェアの脆弱性を悪用したり、機密データにアクセスしたり、侵害したマシン内部でひそかに生き残るために使用されたテクニックを、マシンのパフォーマンスに影響を与えずに検出することができます。

-   **水平移動と内部偵察:** 攻撃者は、侵害したネットワーク内にとどまったり、貴重なデータを探して奪取したりするために、侵害したマシンから同一ネットワーク内の他のマシンに水平移動を試みることがよくあります。 Security Center は、攻撃者がネットワーク内部で足がかりを拡大するために試行するリモート コマンドの実行、ネットワーク プローブ、アカウントの列挙などを見つけるために、プロセスとログインのアクティビティを監視します。

-   **悪質な PowerShell スクリプト**: 攻撃者が標的の仮想マシン上で悪質なコードを実行するために、さまざまな目的で PowerShell を使用することがあります。 疑わしい活動の兆候がないか、PowerShell のアクティビティは Security Center によって調査されます。

-   **送信方向の攻撃**:攻撃の踏み台として利用するために、クラウド リソースを攻撃者が標的にすることは少なくありません。 たとえば、侵入した仮想マシンを利用して、他の仮想マシンにブルート フォース攻撃を仕掛けたり、スパムを送信したり、開放ポートやインターネット上の他のデバイスをスキャンしたりする場合があります。 Security Center では、ネットワーク トラフィックに機械学習を適用することで、正常な範囲を逸脱した送信ネットワーク通信を検出することができます。 スパムが検出された場合、Security Center では、普通ではない電子メール トラフィックと Office 365 から得られたインテリジェンスとの相関を明らかにすることで、不正目的のメールであるか、正当な電子メール キャンペーンによるものであるかも判断します。

### <a name="anomaly-detection"></a>異常検出

Azure Security Center での脅威の特定には、異常検出も使用されます。 行動分析は、大規模なデータセットから導いた既知のパターンに依存します。これとは対照的に、異常検出は "独自色" が強く、個々の環境に固有の基準に重点が置かれます。 機械学習を適用して個々の環境の正常なアクティビティを突き止めたうえで、セキュリティ イベントの可能性を示す異常な条件とは何かを定義する規則を作成します。 次に例を示します。

-   **受信方向の RDP/SSH ブルート フォース攻撃**:同じ環境でも、日々数多くのユーザーがログインする稼働率の高い仮想マシンもあれば、ほとんどまたはまったくログインされない仮想マシンもあります。 Azure Security Center は、これらの仮想マシンにおける基準となるログイン アクティビティを決定したり、機械学習で通常のログイン アクティビティの範囲を定義することができます。 ログインに関連する特性で基準との不一致がある場合は、アラートが生成される場合があります。 前述のように、何をもって有意とするかは、機械学習によって突き止められます。

### <a name="continuous-threat-intelligence-monitoring"></a>脅威インテリジェンスの継続的監視

Azure Security Center は、世界中のセキュリティ リサーチ チームやデータ サイエンス チームと連携して脅威に関する状況の変化を絶えず監視しています。 たとえば次のような取り組みが行われています。

-   **脅威インテリジェンスの監視**:脅威インテリジェンスには、既存の脅威や新たに発生した脅威に関するメカニズム、インジケーター、示唆、即時に利用可能なアドバイスが含まれます。 こうした情報はセキュリティ コミュニティから得られるほか、Microsoft も、社内や社外のソースから提供される脅威インテリジェンスを絶えず監視しています。

-   **シグナルの共有**:クラウドとオンプレミスのサービス、サーバー、クライアント エンドポイント デバイスに及ぶ幅広い Microsoft のポートフォリオからセキュリティ チームが得た分析情報が共有され、分析されます。

-   **Microsoft のセキュリティ スペシャリスト**:フォレンジクスや Web 攻撃検出など、専門のセキュリティ分野に従事する Microsoft 内のさまざまなチームと絶えず連携します。

-   **検出のチューニング**:実際のユーザーのデータセットに対してアルゴリズムが実行され、セキュリティ研究員がユーザーと連携してその結果を検証します。 機械学習アルゴリズムの精度を高めるために、真陽性と偽陽性が使用されます。

こうしたさまざまな取り組みの結果、新しく改善された検出が可能となり、その恩恵はすぐに得ることができるようになりました。 ユーザーが措置を講じる必要はありません。

## <a name="advanced-threat-detection-features-other-azure-services"></a>高度な脅威検出: 他の Azure サービス

### <a name="virtual-machines-microsoft-antimalware"></a>仮想マシン:Microsoft マルウェア対策

Azure の [Microsoft マルウェア対策](https://docs.microsoft.com/azure/security/azure-security-antimalware)は、アプリケーションやテナント環境のための単一エージェント ソリューションであり、ユーザーの介入なしにバック グラウンドで実行するように設計されています。 アプリケーションのワークロードのニーズに基づいて、マルウェア対策監視など、基本的な既定のセキュリティまたは高度なカスタム構成で、保護をデプロイできます。 Azure のマルウェア対策は、Azure Virtual Machines のセキュリティ オプションであり、すべての Azure PaaS 仮想マシンに自動的にインストールされます。

#### <a name="microsoft-antimalware-core-features"></a>Microsoft マルウェア対策の主な機能

Microsoft マルウェア対策をアプリケーション用にデプロイして有効にする Azure の機能を次に示します。

-   **リアルタイム保護**:クラウド サービスおよび仮想マシンでのアクティビティを監視し、マルウェアの実行を検出してブロックします。

-   **スケジュールに基づくスキャン**:特定対象のスキャンを定期的に実行し、マルウェアや活動量の多いプログラムを検出します。

-   **マルウェアの駆除**:悪意のあるファイルの削除や検疫、悪意のあるレジストリ エントリのクリーンアップなど、検出されたマルウェアへの対処を自動的に行います。

-   **シグネチャの更新**:最新の保護シグネチャ (ウイルスの定義) を自動的にインストールし、事前に定義された頻度で保護を確実に最新の状態に更新します。

-   **マルウェア対策エンジンの更新**: Microsoft マルウェア対策エンジンを自動的に更新します。

-   **マルウェア対策プラットフォームの更新**:Microsoft マルウェア対策プラットフォームを自動的に更新します。

-   **アクティブな保護**:検出された脅威および疑わしいリソースに関するテレメトリ メタデータを Microsoft Azure にレポートして発生中の脅威に迅速に対応し、Microsoft Active Protection System を使用して同期されたシグネチャをリアルタイムで配信できるようにします。

-   **サンプルのレポート**:Microsoft マルウェア対策サービスにサンプルを提供およびレポートし、サービスの調整およびトラブルシューティングを可能にします。

-   **除外**:パフォーマンスやその他の理由で、アプリケーションおよびサービスの管理者が特定のファイル、プロセス、およびドライブを保護とスキャンから除外できるようにします。

-   **マルウェア対策イベントの収集**:マルウェア対策サービスの状態、疑わしいアクティビティ、実行された修復アクションをオペレーティング システムのイベント ログに記録し、顧客の Azure ストレージ アカウントにそれらを収集します。

### <a name="azure-sql-database-threat-detection"></a>Azure SQL Database の脅威の検出

[Azure SQL データベースの脅威の検出](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)は、Azure SQL Database サービスに組み込まれている、新しいセキュリティ インテリジェンス機能です。 Azure SQL Database の脅威の検出は、データベースの異常なアクティビティについて学習、プロファイル、検出を一日中行いながら、データベースへの潜在的な脅威を識別します。

セキュリティ責任者や他の指定された管理者は、不審なデータベースのアクティビティが発生すると、直ちに通知を受け取ることができます。 それぞれの通知では、不審なアクティビティの詳細情報と、脅威に対して推奨されるさらなる調査方法や軽減策が記載されています。

現時点では、Azure SQL Database の脅威の検出では、潜在的な脆弱性および SQL インジェクション攻撃、および異常なデータベース アクセスのパターンを検出します。

脅威の検出を通知する電子メールを受信したとき、ユーザーはメール内のディープ リンクを介して関連する監査レコードに移動してそれを表示することができます。 このリンクをクリックすると、監査ビューアーまたは事前に構成された Excel 監査テンプレートが開きます。これらは、以下の要素に従って、疑わしいイベントが発生した時刻前後の関連する監査レコードを表示します。

-   異常なデータベース アクティビティを伴うデータベース/サーバーの監査ストレージ。

-   監査ログの書き込みイベントの時刻に使用された、関連の監査ストレージ テーブル。

-   イベントの発生直後の時間の監査レコード。

-   イベントの発生時における、同様のイベント ID の監査レコード (一部の検出では省略可能)。

SQL Database の脅威検出機能では、次の検出手法のいずれかが使用されます。

-   **確定的な検出**: 既知の攻撃に一致する不審なパターン (ルール ベース) を SQL クライアントのクエリで検出します。 この手法で検出される確率は高く、誤判定率は低くなります。ただし、”アトミック検出” のカテゴリに含まれるため範囲は制限されています。

-   **動作検出**: 過去 30 日間に見られなかった、データベースの異常な動作を示す、異常なアクティビティを検出します。 SQL クライアントの異常なアクティビティの例には、失敗したログインまたはクエリ、大量のデータ抽出、通常でない Canonical クエリ、またはデータベースへのアクセスに使用された特殊な IP アドレスの急増があります。

### <a name="application-gateway-web-application-firewall"></a>Application Gateway の Web アプリケーション ファイアウォール

[Web アプリケーション ファイアウォール (WAF)](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) は、[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) の機能で、標準の[アプリケーション配信コントロール](https://kemptechnologies.com/in/application-delivery-controllers)機能に対してアプリケーション ゲートウェイを使用して、Web アプリケーションを保護します。 Web アプリケーション ファイアウォールは、[Open Web Application Security Project (OWASP) の上位 10 件の一般的 Web 脆弱性](https://www.owasp.org/index.php/Top_10_2010-Main)の大部分に対する保護を提供することで、これを実現します。

![Application Gateway の Web アプリケーション ファイアウォールの図](./media/azure-threat-detection/azure-threat-detection-fig13.png)

保護には次のものがあります。

-   SQL インジェクションからの保護。

-   クロス サイト スクリプティングからの保護。

-   一般的な Web 攻撃からの保護 (コマンド インジェクション、HTTP 要求スマグリング、HTTP レスポンス スプリッティング、リモート ファイル インクルード攻撃など)。

-   HTTP プロトコル違反に対する保護。

-   HTTP プロトコル異常に対する保護 (ホスト ユーザー エージェントと承認ヘッダーが見つからない場合など)。

-   ボット、クローラー、スキャナーの防止。

-   一般的なアプリケーション構成ミスの検出 (Apache、IIS など)。

アプリケーション ゲートウェイに WAF を構成することには、次のようなメリットがあります。

-   バックエンド コードを変更しなくても、Web アプリケーションを Web の脆弱性および攻撃から保護できます。

-   アプリケーション ゲートウェイの内側にある複数の Web アプリケーションを同時に保護できます。 アプリケーション ゲートウェイでは、最大 20 の Web サイトをホスティングします。

-   アプリケーション ゲートウェイの WAF ログで生成されたリアルタイムのレポートを使用して、Web アプリケーションに対する攻撃を監視できます。

-   コンプライアンス要件を満たすのに役立ちます。 特定のコンプライアンス制御では、インターネットに接続するすべてのエンド ポイントを WAF ソリューションで保護する必要があります。

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>異常検出 API: Azure Machine Learning で構築

異常検出 API は、時系列データ内のさまざまな異常パターンを検出するために役立つ API です。 API は、時系列の各データ ポイントに異常なスコアを割り当てます。これを使用してアラート生成、ダッシュボード内の監視、チケット発行システムとの接続ができます。

[異常検出 API](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) が検出できる時系列の異常のタイプには次のものがあります。

-   **急増と急減**: サービスへのログインの失敗数や電子商取引サイトのチェックアウトの数を監視している場合、急増や急減はセキュリティ攻撃やサービス障害を示している可能性があります。

-   **正と負の値の傾向**:コンピューティングでのメモリの使用量を監視する場合、空きメモリのサイズの縮小はメモリ リークの可能性を示しています。 サービス キューの長さの監視では、永続的な上昇傾向は、基のソフトウェアに問題が発生している可能性があることを示しています。

-   **レベルの変化と動的範囲値の変化**: サービスのアップグレード後にサービス遅延のレベルが変化した場合や、アップグレード後の例外のレベルが低下した場合は、監視する価値があります。

API に基づいた機械学習では、次のことができます。

-   **柔軟かつ堅牢な検出**: 異常検出モデルを使用することで、機密度設定の構成や、季節依存または季節に依存しないデータ セットの異常を検出できます。 ユーザーは、自身のニーズに合わせて、検出 API の感度を強化または低下させることで異常検出モデルを調整することができます。 つまり、季節のパターンの有無によって、検出によるデータの異常性が見えやすくなったり見えにくくなります。

-   **スケーラブルかつタイムリーな検出**: 専門家のドメイン知識で設定されるしきい値表示で監視する従来の方法はコストがかかるうえ、動的に変化する数百万件のデータ セットに対応するための拡張性がありません。 この API を使用する異常検出モデルには学習機能があり、過去とリアルタイムの両方のデータから自動的にモデルを調整します。

-   **プロアクティブでアクションにつながる検出**: 動きの遅い傾向とレベルの変更の検出は、初期の異常検出に適用できます。 早期に異常な信号を検出することで、人による調査を指示し、問題の発生領域でアクションを起こすことができます。 さらに、問題の原因を分析して、この異常検出 API サービスに追加するアラート用のツールを開発することができます。

異常検出 API は、サービスの使用状況や KPI の監視、IoT、パフォーマンスの監視、ネットワーク トラフィックの監視といったさまざまなシナリオで効果的かつ効率的なソリューションです。 次に、この API が役に立ついくつかの一般的なシナリオを示します。

- IT 部門には、イベントの追跡、エラー コード、使用状況のログ、パフォーマンス (CPU、メモリ) を適切なタイミングで追跡するツールが必要です。

-   オンラインの商用サイトは、顧客活動、ページ ビュー、クリック数などの情報を追跡する必要があります。

-   公益事業の関連企業は、水道、ガス、電力、その他のリソースの消費量を追跡する必要があります。

-   設備や建築物の管理サービス企業は、温度、湿度、トラフィックなどを監視する必要があります。

-   IoT/製造業者は、ワークフローや品質などを時系列で監視するためのセンサー データを使用する必要があります。

-   コール センターなどのサービス プロバイダーは、サービス要求の傾向、インシデントのボリューム、待機キューの長さなどを監視する必要があります。

-   ビジネス分析グループは、事業 KPI (売上高、顧客センチメント、価格設定など)、異常な動作をリアルタイムで監視する必要があります。

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) は、Microsoft Cloud のセキュリティ スタックの重要なコンポーネントです。 これは、企業がクラウド アプリケーションの可能性を最大限に活用できるように行動するのに役立つ包括的なソリューションです。 アクティビティのさらなる詳細を把握することで制御は維持されます。 また、重要なデータの保護をクラウド アプリケーション全体で強化するうえでも役立ちます。

企業は、shadow IT の発見、リスクの評価、ポリシーの適用、アクティビティの調査、および驚異の停止に役立つツールを使用して、重要なデータの制御を維持しながらもより安全にクラウドに移行できるようにします。

| | |
|---|---|
| 発見 | Cloud App Security を使用して shodow IT を発見します。 クラウド環境でアプリ、アクティビティ、ユーザー、データ、およびファイルを発見して可視性を獲得します。 クラウドに接続しているサード パーティのアプリを検出します。|
|調査 | クラウド フォレンジック ツールを使用してクラウド アプリケーションを調査します。具体的には、ネットワーク内の高リスク アプリケーション、特定のユーザー、およびファイルに関する詳しい調査を行います。 クラウドから収集されたデータのパターンを検索します。 クラウドを監視するレポートを生成します。 |
| コントロール | ネットワーク クラウドのトラフィック全体を最大限に制御するためのポリシーとアラートを設定してリスクを軽減します。 Cloud App Security を使用して、安全で承認された代替クラウド アプリにユーザーが移行できるようにします。 |
| 保護 | Cloud App Security を使用して、アプリケーションの承認と禁止、データ損失の防止、アクセスと共有の制御、およびカスタム レポートやアラートの生成を行います。 |
| コントロール | ネットワーク クラウドのトラフィック全体を最大限に制御するためのポリシーとアラートを設定してリスクを軽減します。 Cloud App Security を使用して、安全で承認された代替クラウド アプリにユーザーが移行できるようにします。 |
| | |


![Cloud App Security の図](./media/azure-threat-detection/azure-threat-detection-fig14.png)

Cloud App Security は、次の作業によってクラウドの詳細を把握します。

-   Cloud Discovery を使用して、組織が使用しているクラウド環境とクラウド アプリをマップおよび識別します。

-   クラウドでのアプリの使用を承認および禁止します。

-   デプロイが容易で、プロバイダー API を活用するアプリ コネクタを使用して、接続しているアプリの詳細を把握し、管理します。

-   ポリシーを設定して、その後の微調整を行うことで、継続的に制御できるようにします。

Cloud App Security は、これらのソースからデータを収集することでデータに対する高度な分析を実行します。 Cloud App Security は、異常なアクティビティに迅速に通知し、クラウド環境をさらに詳しく把握できるようにします。 Cloud App Security にポリシーを構成することができ、これを使用して、クラウド環境内のすべてを保護することができます。

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Azure Marketplace を介したサード パーティ製の高度な脅威検出機能

### <a name="web-application-firewall"></a>Web アプリケーション ファイアウォール

Web アプリケーション ファイアウォールは、着信する Web トラフィックを検査して、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、アプリケーション DDoS 攻撃など、Web アプリケーションを対象とした攻撃をブロックします。 さらに、データ損失防止 (DLP) のためにバックエンド Web サーバーからの応答を検査します。 管理者は、統合アクセス制御エンジンを使用して認証、承認、アカウンティング (AAA) に対応するきめ細かなアクセス制御ポリシーを作成することができ、これによって企業は強力な認証とユーザー制御の機能を得ることができます。

Web アプリケーション ファイアウォールには、次のような利点があります。

-   アプリケーションに対する SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、アプリケーション DDoS、その他の攻撃を検出してブロックします。

-   認証およびアクセス制御。

-   発信トラフィックをスキャンして機密データを検出し、情報漏洩を防ぐためにマスクやブロックを行います。

-   Web アプリケーションのコンテンツ配信を、キャッシュ、圧縮、その他のトラフィックの最適化機能などを使用して高速化します。

Azure Marketplace で使用可能な Web アプリケーション ファイアウォールの例については、[Barracuda WAF、Brocade 仮想 Web アプリケーション ファイアウォール (vWAF)、Imperva SecureSphere、ThreatSTOP IP ファイアウォール](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Security Center の検出機能](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities): Azure リソースをターゲットとするアクティブな脅威を特定し、迅速に対応するうえで必要な分析情報を取得するのに役立ちます。

- [Azure SQL Database の脅威の検出](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): ご利用のデータベースに対する潜在的な脅威の問題に対処するのに役立ちます。
