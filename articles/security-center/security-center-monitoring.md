---
title: "Azure Security Center でのセキュリティの監視 | Microsoft Docs"
description: "この記事は、Azure Security Center の監視機能の使用を開始する際に役立ちます。"
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 3cba38d95535ff5ed3cd62aac5c0aa04a310f48c
ms.openlocfilehash: ae263615d5fa262eb8a8ed2e5461d92bec503f1d
ms.lasthandoff: 01/31/2017


---
# <a name="security-health-monitoring-in-azure-security-center"></a>Azure セキュリティ センターでのセキュリティ ヘルスの監視
この記事は、Azure Security Center で監視機能を使用してポリシーに対するコンプライアンスを監視するうえで役立ちます。

## <a name="what-is-security-health-monitoring"></a>セキュリティのヘルスの監視とは
私たちは監視について、状況に対処できるように、イベントが発生するのを監視して待機することだと考えがちです。 セキュリティの監視は、組織の標準やベスト プラクティスを満たしていないシステムを識別するために、リソースを監査するプロアクティブな戦略を持つことを意味します。

## <a name="monitoring-security-health"></a>セキュリティ ヘルスの監視
サブスクリプションのリソースに対して[セキュリティ ポリシー](security-center-policies.md)を有効にすると、Security Center は、リソースのセキュリティを分析して潜在的な脆弱性を特定します。 ネットワークの構成に関する情報は、すぐに利用可能になります。 これに対して、セキュリティ更新プログラムの状態やオペレーティング システムの構成など、仮想マシンの構成に関する情報は、利用可能になるまで&1; 時間以上かかることがあります。 リソースのセキュリティの状態は、**[Resources security health (リソース セキュリティの正常性)]** ブレードに表示されます。問題がある場合には、同じブレードで問題も確認できます。 これらの問題の一覧は、 **[推奨事項]** ブレードでも確認できます。

推奨事項の適用方法の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」を参照してください。

**[Resources security health (リソース セキュリティの正常性)]** タイルでは、リソースのセキュリティの状態を監視することができます。 以下の例では、重大度が高レベルと中レベルの問題が多数あります。いずれも、注意が必要な問題です。 有効になっているセキュリティ ポリシーが、監視されているコントロールのタイプに影響します。

![[Resources security health (リソース セキュリティの正常性)] タイル](./media/security-center-monitoring/security-center-monitoring-fig1-new4-2017.png)

セキュリティ更新プログラムが適用されていない仮想マシンや[ネットワーク セキュリティ グループ](/virtual-network/virtual-networks-nsg.md)がないサブネットなど、対処する必要がある脆弱性が Security Center によって特定された場合、ここに表示されます。

### <a name="monitor-virtual-machines"></a>仮想マシンの監視
**[Resources security health (リソース セキュリティの正常性)]** タイルで **[仮想マシン]** をクリックすると、**[仮想マシン]** ブレードが開き、以下のスクリーンショットのように、利用開始手順と問題の予防手順の詳細に加え、Security Center で監視されるすべての仮想マシンの一覧が表示されます。

![不足しているシステム更新プログラム (仮想マシン別)](./media/security-center-monitoring/security-center-monitoring-fig2-ga.png)

* 利用開始手順
* 仮想マシンの推奨事項
* 仮想マシン

各セクションでは、個別のオプションを選択して、その問題に対処するために推奨される手順の詳細を確認できます。 以下のセクションでは、これらの領域をさらに詳しく説明します。

#### <a name="monitoring-recommendations"></a>監視に関する推奨事項
このセクションには、データ収集用に初期化された仮想マシンの総数と、その現在の状態が表示されます。 すべての仮想マシンのデータ収集が初期化されると、Security Center のセキュリティ ポリシーを受信する準備が完了します。 このエントリをクリックすると **[データ収集のインストール状態]** ブレードが開き、以下のスクリーンショットのように、仮想マシンの名前のほか、**[インストールの状態]** 列でデータ収集の現在の状態を確認できます。

![仮想マシンの初期化の状態](./media/security-center-monitoring/security-center-monitoring-fig3-ga.png)

#### <a name="virtual-machine-recommendations"></a>仮想マシンの推奨事項
このセクションには、Azure Security Center で監視される[各仮想マシンについての推奨事項](security-center-virtual-machine-recommendations.md)が表示されます。 最初の列には、推奨事項が一覧表示されます。 2 番目の列は、その推奨事項の影響を受ける仮想マシンの合計数を表しています。 3 番目の列は、以下のスクリーンショットに示したように、問題の重大度を表します。

![仮想マシンの推奨事項](./media/security-center-monitoring/security-center-monitoring-fig4-ga.png)

> [!NOTE]
> **ネットワーク トポロジ**の一覧の **[Networking Health (ネットワークの正常性)]** ブレードに表示されるのは、少なくとも&1; つのパブリック エンドポイントを持つ仮想マシンだけです。
>
>

各推奨事項にはいくつかのアクションが示されており、クリックして実行できます。 たとえば、**[システムの更新プログラムがありません]** をクリックすると、**[システムの更新プログラムがありません]** ブレードが開きます。 以下のスクリーンショットに示すように、ここには修正プログラムが不足している仮想マシンと、不足している更新プログラムの重大度が表示されます。

![仮想マシンで不足しているシステム更新プログラム](./media/security-center-monitoring/security-center-monitoring-fig5-ga.png)

**[システムの更新プログラムがありません]** ブレードには、次の情報が表形式で表示されます。

* **[仮想マシン]**: 更新プログラムが不足している仮想マシンの名前。
* **[システムの更新プログラム]**: 不足しているシステム更新プログラムの件数。
* **[最終スキャン時刻]**: Security Center で仮想マシンの更新プログラムが最後にスキャンされた時刻。
* **[状態]**: 推奨事項の現在の状態。
  * **[オープン]**: 推奨事項への対処がまだ行われていない。
  * **[処理中]**: 現在、リソースへの推奨事項の適用を進めており、ユーザーのアクションは不要。
  * **解決**: 推奨事項が既に完了済み  (問題が解決されていると、エントリが灰色表示になります)。
* **[重大度]**: 特定の推奨事項の重大度を示します。
  * **[高]**: 重要なリソース (アプリケーション、仮想マシン、ネットワーク セキュリティ グループ) に脆弱性が存在しており、注意が必要。
  * **[中]**: プロセスを完了または脆弱性を排除するために重大ではないまたは追加の手順が必要。
  * **[低]**: 対処する必要はあるものの、直ちに注意する必要はない脆弱性が存在する  (既定では、重要度の低い推奨事項は表示されないが、重要度の低い推奨事項にフィルターを適用すると表示できる)。

推奨事項の詳細を表示するには、仮想マシンの名前をクリックします。 以下のスクリーンショットに示したように、その仮想マシンについて新しいブレードが開き、更新プログラムの一覧が表示されます。

![特定の仮想マシンに足りないシステム更新プログラム](./media/security-center-monitoring/security-center-monitoring-fig6-ga.png)

> [!NOTE]
> ここに示したセキュリティの推奨事項は、**[推奨事項]** ブレードに表示されているものと同じです。 推奨事項の解決方法の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」の記事を参照してください。 これは仮想マシンだけでなく、**[リソース正常性]** タイルで利用できるすべてのリソースにも適用されます。
>
>

#### <a name="virtual-machines-section"></a>[仮想マシン] セクション
[仮想マシン] セクションには、すべての仮想マシンと推奨事項の概要が表示されます。 各列は、以下のスクリーンショットに示したように、推奨事項の&1; つのセットを表します。

![すべての仮想マシンと推奨事項の概要](./media/security-center-monitoring/security-center-monitoring-fig7-ga.png)

各推奨事項の下に表示されるアイコンにより、注意が必要な仮想マシンと、その推奨事項の種類をすばやく把握できます。

前の例には、1 つの仮想マシンにエンドポイント保護に関する重大な推奨事項があります。 その仮想マシンに関する情報を表示する場合には、クリックしてください。 以下のスクリーンショットに示すように、この仮想マシンに関するブレードが新しく開きます。

![仮想マシンのセキュリティの詳細](./media/security-center-monitoring/security-center-monitoring-fig8-ga.png)

このブレードには仮想マシンのセキュリティに関する詳細情報が表示されます。 このブレードの下部には、推奨されるアクションと、各問題の重大度が表示されます。

#### <a name="cloud-services-preview-section"></a>[Cloud services (Preview) (クラウド サービス (プレビュー))] セクション
仮想マシンの **[セキュリティの正常性]** タイルには、クラウド サービスの正常性状態が表示されます。 オペレーティング システムのバージョンが最新でないときは、以下のスクリーンショットに示したような推奨事項が作成されます。

![クラウド サービスの正常性状態](./media/security-center-monitoring/security-center-monitoring-fig8-new2.png)

推奨事項内の手順に従って、オペレーティング システムのバージョンを更新する必要があります。 たとえば、WebRole1 (IIS に自動的にデプロイされた Web アプリで Windows Server を実行) の赤いアラートをクリックした場合、または WorkerRole1 (IIS に自動的にデプロイされた Web アプリで Windows Server を実行) をクリックした場合には、以下のスクリーンショットに示したブレードが新しく開き、推奨事項に関する詳細が表示されます。

![クラウド サービスの詳細](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png)

この推奨事項に関するより正確な説明を確認する場合は、**[説明]** 列の **[OS バージョンの更新]** をクリックしてください。 **[OS バージョンの更新 (プレビュー)]** ブレードが開き、詳細が表示されます。

![クラウド サービスの推奨事項](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

### <a name="monitor-virtual-networks"></a>仮想ネットワークの監視
**[Resources security health (リソース セキュリティの正常性)]** タイルで **[ネットワーク]** をクリックすると、**[ネットワーク]** ブレードが開き、以下のスクリーンショットに示すような詳細が表示されます。

![Networking blade](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>ネットワークの推奨事項
仮想マシンのリソースの正常性情報と同様に、このブレードでは、上部に問題をまとめた一覧、下部に監視対象のネットワークの一覧が表示されます。

ネットワーク状態の内訳のセクションには、潜在的なセキュリティの問題と [推奨事項](security-center-network-recommendations.md)の一覧が表示されます。 次のような問題が発生する可能性があります。

* 次世代ファイアウォール (NGFW) がインストールされていない
* サブネットでネットワーク セキュリティ グループが有効になっていない
* 仮想マシンでネットワーク セキュリティ グループが有効になっていない
* パブリックの外部エンドポイントを通じた外部アクセスが制限される
* インターネット接続エンドポイントが正常である

推奨事項をクリックすると、新しいブレードが開いて推奨事項の詳細を確認できます。以下に例を示します。

![[ネットワーク] ブレードに表示される推奨事項の詳細](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

この例では、**[サブネット用の欠けているネットワーク セキュリティ グループを構成する]** ブレードに、ネットワーク セキュリティ グループの保護が存在しないサブネットと仮想マシンの一覧が表示されています。 ネットワーク セキュリティ グループを適用するサブネットをクリックすると、別のブレードが開きます。

**[ネットワーク セキュリティ グループの選択]** ブレードでは、サブネットに最適なネットワーク セキュリティ グループを選択したり、新しいネットワーク セキュリティ グループを作成したりできます。

#### <a name="internet-facing-endpoints-section"></a>[Internet facing endpoints (インターネット接続エンドポイント)] セクション
**[インターネットに接続するエンドポイント]** セクションでは、インターネット接続エンドポイントで現在構成されている仮想マシンとその現在の状態を確認できます。

![インターネット接続エンドポイントで構成されている仮想マシンとその状態](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

この表には、仮想マシンを表すエンドポイント名、インターネット接続 IP アドレス、ネットワーク セキュリティ グループと NGFW の現在の重大度ステータスが示されています。 この表は、項目が以下の重大度に従って並んでいます。

* 赤 (最上位): 優先度が高く、直ちに対処する必要があります
* オレンジ: 優先度は中程度で、できるだけ早く対処する必要があります
* 緑 (最下位): 正常な状態です

#### <a name="networking-topology-section"></a>[Networking topology (ネットワーク トポロジ)] セクション
**[ネットワーク トポロジ]** セクションには、以下のスクリーンショットのように、リソースが階層として表示されます。

![[ネットワーク トポロジ] セクション内のリソースの階層表示](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

このテーブルは、項目 (仮想マシンとサブネット) が以下の重大度に従って順に並んでいます。

* 赤 (最上位): 優先度が高く、直ちに対処する必要があります
* オレンジ: 優先度は中程度で、できるだけ早く対処する必要があります
* 緑 (最下位): 正常な状態です

このトポロジ ビューでは、最初のレベルに[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)、[仮想ネットワーク ゲートウェイ](/vpn-gateway/vpn-gateway-site-to-site-create.md)、[仮想ネットワーク (クラシック)](/virtual-network/virtual-networks-create-vnet-classic-pportal.md) が表示されます。 2 番目のレベルにはサブネット、3 番目のレベルにはそれらのサブネットに属している仮想マシンが置かれます。 右側の列には、これらのリソースのネットワーク セキュリティ グループの現在の状態が表示されます。以下に例を示します。

![[ネットワーク トポロジ] セクションに表示されるネットワーク セキュリティ グループの状態](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

このブレードの下部には、この仮想マシンに対する推奨事項が表示されます。推奨事項の内容は、前に説明したものとほぼ同じです。 ここでは、推奨事項をクリックして詳細を確認したり、必要なセキュリティ制御または構成を適用したりできます。

### <a name="monitor-data"></a>データの監視

**[Resources security health (リソースのセキュリティ正常性)]** タイルの **[SQL & Data (SQL & データ)]** をクリックすると、**[Data Resources (データ リソース)]** ブレードが開いて SQL とストレージの推奨事項が表示されます。 また、データベースの全般的な正常性状態に関する [推奨事項](security-center-sql-service-recommendations.md) も示されます。 ストレージの暗号化の詳細については、「[Enable encryption for Azure storage account in Azure Security Center (Azure Security Center における Azure ストレージ アカウントの暗号化の有効化)](security-center-enable-encryption-for-storage-account.md)」を参照してください。

![データ リソース](./media/security-center-monitoring/security-center-monitoring-fig13-ga-new.png)

**[SQL Recommendations (SQL の推奨事項)]** で任意の推奨事項をクリックすると、問題解決のためのアクションの詳細を確認できます。 次の例では、**[Database Auditing & Threat detection on SQL databases (データベースの監査と SQL Database の脅威の検出)]** という推奨事項を展開した状態を示しています。

![SQL の推奨事項の詳細](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

**[Enable Auditing & Threat detection on SQL databases (SQL データベースの監査と脅威の検出を有効にする)]** ブレードには、次の情報が表示されます。

* SQL データベースの一覧
* SQL データベースが配置されているサーバー
* この設定がサーバーから継承されたものか、このデータベースに固有の設定かに関する情報
* 現在の状態
* 問題の重大度

この推奨事項に対処するためにデータベースをクリックすると、以下に示す **[監査と脅威検出]** ブレードが開きます。

![[監査と脅威検出] ブレード](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

監査を有効にするには、**[監査]** オプションの **[オン]** を選択します。

### <a name="monitor-applications"></a>アプリケーションの監視

Azure ワークロードに、[(Azure Resource Manager で作成した) 仮想マシン](../azure-resource-manager/resource-manager-deployment-model.md)に配置されたアプリケーションがあり、Web ポート (TCP ポート 80 および 443) が公開されている場合、Security Center ではこれらを監視して、潜在的なセキュリティ問題を特定し、修復手順を推奨することができます。 **[アプリケーション]** タイル内をクリックすると、**[アプリケーション]** ブレードが開き、**アプリケーションの推奨事項**のセクションに一連の推奨事項が示されます。 さらに、以下のスクリーンショットのように、ホスト/仮想 IP ごとのアプリケーションの内訳も示されます。

![アプリケーションのセキュリティのヘルス](./media/security-center-monitoring/security-center-monitoring-fig16-ga.png)

他の推奨事項の場合と同様に、推奨事項をクリックすると、問題と修復方法の詳細を確認できます。 以下の図の例は、安全でないと判定された Web アプリケーションを示したものです。 安全でないと判定されたアプリケーションを選択すると、利用可能なオプションが示された別のブレードが開きます。

![安全でないアプリに関する詳細](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

このブレードには、このアプリケーションのすべての推奨事項が一覧表示されます。 **[Web アプリケーション ファイアウォールを追加する]** をクリックすると、以下のスクリーンショットに示すように **[Web アプリケーション ファイアウォールの追加]** ブレードが開き、パートナーの Web アプリケーション ファイアウォール (WAF) をインストールするためのオプションが表示されます。

![[Web アプリケーション ファイアウォールの追加] ダイアログ ボックス](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## <a name="see-also"></a>関連項目
この記事では、Azure Security Center での監視機能の使用方法について説明しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* 「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」: Azure Security Center でセキュリティ設定を構成する方法について説明します。
* 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」: セキュリティの警告の管理と対応の方法について説明しています。
* 「[Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)」: パートナー ソリューションの正常性状態を監視する方法について説明しています。
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」: このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/): Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

