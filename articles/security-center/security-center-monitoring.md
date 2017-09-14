---
title: "Azure Security Center でのセキュリティの監視 | Microsoft Docs"
description: "この記事は、Azure Security Center の監視機能の使用を開始する際に役立ちます。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 4752199803f1fab04608e6a8618e3294e85fb38a
ms.contentlocale: ja-jp
ms.lasthandoff: 09/07/2017

---
# <a name="security-health-monitoring-in-azure-security-center"></a>Azure セキュリティ センターでのセキュリティ ヘルスの監視
この記事は、Azure Security Center で監視機能を使用してポリシーに対するコンプライアンスを監視するうえで役立ちます。

## <a name="what-is-security-health-monitoring"></a>セキュリティのヘルスの監視とは
私たちは監視について、状況に対処できるように、イベントが発生するのを監視して待機することだと考えがちです。 セキュリティの監視は、組織の標準やベスト プラクティスを満たしていないシステムを識別するために、リソースを監査するプロアクティブな戦略を持つことを意味します。

## <a name="monitoring-security-health"></a>セキュリティ ヘルスの監視
サブスクリプションのリソースに対して[セキュリティ ポリシー](security-center-policies.md)を有効にすると、Security Center は、リソースのセキュリティを分析して潜在的な脆弱性を特定します。 ネットワークの構成に関する情報は、すぐに利用可能になります。 VM とコンピューターの構成に関する情報 (セキュリティ更新プログラムの状態、オペレーティング システムの構成など) については、エージェントがインストールされている VM とコンピューターの台数によっては、収集して利用可能になるまでに 1 時間以上かかる場合があります。 リソースのセキュリティの状態は、**[Prevention]\(防止\)** セクションに表示されます。問題がある場合には、同じブレードで問題も確認できます。 これらの問題の一覧は、**[Recommendations]\(推奨事項\)** タイルでも確認できます。

推奨事項の適用方法の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」を参照してください。

**[Prevention]\(防止\)** セクションでは、ご利用のリソースに関するセキュリティの状態を監視できます。 以下の例のように、各リソースのタイル (コンピューティング、ネットワーク、ストレージとデータ、アプリケーション) で、特定された問題の総数を確認できます。

![[Resources security health (リソース セキュリティの正常性)] タイル](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute"></a>コンピューティングの監視
**[コンピューティング]** タイルをクリックすると、次の 3 つのタブが表示されます。

- **[概要]**: 監視と推奨事項です。
- **[VM およびコンピューター]**: すべての仮想マシンとコンピューター、およびその現在のセキュリティ状態が一覧表示されます。
- **[クラウド サービス]**: Security Center によって監視されているすべての Web および worker ロールの一覧です。

![不足しているシステム更新プログラム (仮想マシン別)](./media/security-center-monitoring/security-center-monitoring-fig1-sep2017.png)

各タブには複数のセクションを表示できます。各セクションでは、個別のオプションを選択して、特定の問題に対処するために推奨される手順の詳細を確認できます。 

#### <a name="monitoring-recommendations"></a>監視に関する推奨事項
このセクションには、自動プロビジョニング用に初期化された仮想マシンとコンピューターの総数と、その現在の状態が表示されます。 このエントリをクリックすると、"**VM エージェントが見つからないか、応答していません**" という画面が表示されます。 

![不足しているシステム更新プログラム (仮想マシン別)](./media/security-center-monitoring/security-center-monitoring-fig1-new003-2017.png)


#### <a name="recommendations"></a>推奨事項
このセクションには、Azure Security Center で監視される[各仮想マシンとコンピューターについての推奨事項](security-center-virtual-machine-recommendations.md)が表示されます。 最初の列には、推奨事項が一覧表示されます。 2 番目の列は、その推奨事項の影響を受ける仮想マシンとコンピューターの合計数を表しています。 3 番目の列は、以下のスクリーンショットに示したように、問題の重大度を表します。

![仮想マシンの推奨事項](./media/security-center-monitoring/security-center-monitoring-fig2-sep2017.png)

> [!NOTE]
> **ネットワーク トポロジ**の一覧の **[Networking Health]\(ネットワークの正常性\)** に表示されるのは、少なくとも 1 つのパブリック エンドポイントを備えた仮想マシンだけです。
>

各推奨事項にはいくつかのアクションが示されており、クリックして実行できます。 たとえば、**[システムの更新プログラムがありません]** をクリックすると、パッチが適用されていない仮想マシンとコンピューター、および不足している更新プログラムの重大度が、以下のスクリーンショットのように一覧表示されます。

![仮想マシンで不足しているシステム更新プログラム](./media/security-center-monitoring/security-center-monitoring-fig9-sep2017.png)

**[システムの更新プログラムがありません]** には、Windows と Linux についてそれぞれ、重大な更新プログラムの概要がグラフ形式で表示されます。 2 つ目の領域には、次の情報を含んだ表が表示されます。

* **[名前]**: 不足している更新プログラムの名前。
* **[VM とコンピューターの数]**: この更新プログラムが適用されていない VM とコンピューターの合計台数です。
* **[状態]**: 推奨事項の現在の状態。
  * **[オープン]**: 推奨事項への対処がまだ行われていない。
  * **[処理中]**: 現在、リソースへの推奨事項の適用を進めており、ユーザーのアクションは不要。
  * **解決**: 推奨事項が既に完了済み  (問題が解決されていると、エントリが灰色表示になります)。
* **[重大度]**: 特定の推奨事項の重大度を示します。
  * **[高]**: 重要なリソース (アプリケーション、仮想マシン、ネットワーク セキュリティ グループ) に脆弱性が存在しており、注意が必要。
  * **[中]**: プロセスを完了または脆弱性を排除するために重大ではないまたは追加の手順が必要。
  * **[低]**: 対処する必要はあるものの、直ちに注意する必要はない脆弱性が存在する  (既定では、重要度の低い推奨事項は表示されないが、重要度の低い推奨事項にフィルターを適用すると表示できる)。

推奨事項の詳細を表示するには、不足している更新プログラムの名前を一覧内でクリックします。 

![特定の仮想マシンに足りないシステム更新プログラム](./media/security-center-monitoring/security-center-monitoring-fig4-sep2017.png)

> [!NOTE]
> ここに示したセキュリティの推奨事項は、**[推奨事項]** オプションに表示されているものと同じです。 推奨事項の解決方法の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」の記事を参照してください。 これは仮想マシンとコンピューターだけでなく、**[リソース正常性]** タイルで利用できるすべてのリソースにも適用されます。
>

#### <a name="vms--computers-section"></a>[VM とコンピューター] セクション
[VM とコンピューター] セクションには、すべての仮想マシンとコンピューターを対象とした推奨事項の概要が表示されます。 各列は、以下のスクリーンショットに示したように、推奨事項の 1 つのセットを表します。

![すべての仮想マシンと推奨事項の概要](./media/security-center-monitoring/security-center-monitoring-fig5-sep2017.png)

この一覧には、4 種類のアイコンが使用されます。それぞれのアイコンの意味は次のとおりです。

![アイコン 1](./media/security-center-monitoring/security-center-monitoring-icon1.png) Azure 以外のコンピューター。

![アイコン 2](./media/security-center-monitoring/security-center-monitoring-icon2.png) Azure Resource Manager VM。

![アイコン 3](./media/security-center-monitoring/security-center-monitoring-icon3.png) Azure クラシック VM。

![アイコン 4](./media/security-center-monitoring/security-center-monitoring-icon4.png) 表示されているサブスクリプションに属しているワークスペースからのみ確認できる VM。 たとえば、このサブスクリプションのワークスペースの管理下にある他のサブスクリプションの VM や、SCOM ダイレクト エージェントがインストールされた VM のうちリソース ID のないものが該当します。

各推奨事項の下に表示されるアイコンにより、注意が必要な仮想マシンとコンピューター、その推奨事項の種類をすばやく把握できます。 この画面に表示されるオプションは、**[フィルター]** オプションを使用して選択することもできます。

![filter](./media/security-center-monitoring/security-center-monitoring-fig6-sep2017.png)

前の例には、1 つの仮想マシンにエンドポイント保護に関する重大な推奨事項があります。 その仮想マシンに関する情報を表示する場合には、クリックしてください。

![仮想マシンのセキュリティの詳細](./media/security-center-monitoring/security-center-monitoring-fig7-sep2017.png)

ここには仮想マシンまたはコンピューターのセキュリティに関する詳細が表示されます。 一番下には、推奨されるアクションと、各問題の重大度が表示されます。

#### <a name="cloud-services-section"></a>[クラウド サービス] セクション
クラウド サービスについては、オペレーティング システムのバージョンが最新でないときに、次のスクリーンショットに示したような推奨事項が作成されます。

![クラウド サービスの正常性状態](./media/security-center-monitoring/security-center-monitoring-fig1-new006-2017.png)

シナリオで推奨事項が発生した場合 (前の例には該当しません)、推奨事項の手順に従ってオペレーティング システムのバージョンを更新する必要があります。 更新プログラムが利用できる場合はアラートが表示されます (問題の重大度により、赤またはオレンジになります)。 WebRole1 (IIS に自動的にデプロイされた Web アプリで Windows Server を実行) または WorkerRole1 (IIS に自動的にデプロイされた Web アプリで Windows Server を実行) の行でこのアラートをクリックすると、次のスクリーンショットに示すとおり、推奨事項に関する詳細が表示されます。

![クラウド サービスの詳細](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png)

この推奨事項に関するより正確な説明を確認する場合は、**[説明]** 列の **[OS バージョンの更新]** をクリックしてください。 

![クラウド サービスの推奨事項](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

### <a name="monitor-virtual-networks"></a>仮想ネットワークの監視
**[ネットワーク]** タイルをクリックすると、**[ネットワーク]** ブレードが開き、以下のスクリーンショットに示すような詳細が表示されます。

![Networking blade](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>ネットワークの推奨事項
仮想マシンのリソースの正常性情報と同様に、ここには、上部に問題をまとめた一覧、下部に監視対象のネットワークの一覧が表示されます。

ネットワーク状態の内訳のセクションには、潜在的なセキュリティの問題と [推奨事項](security-center-network-recommendations.md)の一覧が表示されます。 次のような問題が発生する可能性があります。

* 次世代ファイアウォール (NGFW) がインストールされていない
* サブネットでネットワーク セキュリティ グループが有効になっていない
* 仮想マシンでネットワーク セキュリティ グループが有効になっていない
* パブリックの外部エンドポイントを通じた外部アクセスが制限される
* インターネット接続エンドポイントが正常である

推奨事項をクリックすると、推奨事項の詳細を確認できます。以下に例を示します。

![[ネットワーク] に表示される推奨事項の詳細](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

この例では、**[サブネット用の欠けているネットワーク セキュリティ グループを構成する]** に、ネットワーク セキュリティ グループの保護が存在しないサブネットと仮想マシンの一覧が表示されています。 ネットワーク セキュリティ グループを適用するサブネットをクリックすると、**[ネットワーク セキュリティ グループの選択]** が表示されます。 ここでは、サブネットに最適なネットワーク セキュリティ グループを選択したり、新しいネットワーク セキュリティ グループを作成したりできます。

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

### <a name="monitor-storage--data"></a>ストレージとデータの監視

**[防止]** セクションの **[Storage & data]\(ストレージとデータ\)** をクリックすると、**[データ リソース]** が開き、SQL とストレージの推奨事項が表示されます。 また、データベースの全般的な正常性状態に関する [推奨事項](security-center-sql-service-recommendations.md) も示されます。 ストレージの暗号化の詳細については、「[Enable encryption for Azure storage account in Azure Security Center (Azure Security Center における Azure ストレージ アカウントの暗号化の有効化)](security-center-enable-encryption-for-storage-account.md)」を参照してください。

![データ リソース](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

**[SQL Recommendations (SQL の推奨事項)]** で任意の推奨事項をクリックすると、問題解決のためのアクションの詳細を確認できます。 次の例では、**[Database Auditing & Threat detection on SQL databases (データベースの監査と SQL Database の脅威の検出)]** という推奨事項を展開した状態を示しています。

![SQL の推奨事項の詳細](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

**[SQL データベースの監査と脅威検出を有効にする]** には、次の情報が表示されます。

* SQL データベースの一覧
* SQL データベースが配置されているサーバー
* この設定がサーバーから継承されたものか、このデータベースに固有の設定かに関する情報
* 現在の状態
* 問題の重大度

この推奨事項に対処するためにデータベースをクリックすると、以下に示す **[監査と脅威検出]** が開きます。

![監査と脅威検出](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

監査を有効にするには、**[監査]** オプションの **[オン]** を選択します。

### <a name="monitor-applications"></a>アプリケーションの監視

Azure ワークロードに、[(Azure Resource Manager で作成した) 仮想マシン](../azure-resource-manager/resource-manager-deployment-model.md)に配置されたアプリケーションがあり、Web ポート (TCP ポート 80 および 443) が公開されている場合、Security Center ではこれらを監視して、潜在的なセキュリティ問題を特定し、修復手順を推奨することができます。 **[アプリケーション]** タイルをクリックすると、**[アプリケーション]** が開き、**アプリケーションの推奨事項**のセクションに一連の推奨事項が示されます。 さらに、ホスト (IP/ドメイン) ごとのアプリケーションの内訳および WAF ソリューションがインストールされているかどうかの状態も表示されます。

![アプリケーションのセキュリティのヘルス](./media/security-center-monitoring/security-center-monitoring-fig8-sep2017.png)

他の推奨事項の場合と同様に、推奨事項をクリックすると、問題と修復方法の詳細を確認できます。 以下の図の例は、安全でないと判定された Web アプリケーションを示したものです。 安全でないと判定されたアプリケーションを選択すると、次のオプションが利用できるようになります。

![詳細](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

ここには、このアプリケーションのすべての推奨事項が一覧表示されます。 **[Web アプリケーション ファイアウォールを追加する]** をクリックすると、以下のスクリーンショットに示すように **[Web アプリケーション ファイアウォールの追加]** が開き、パートナーの Web アプリケーション ファイアウォール (WAF) をインストールするためのオプションが表示されます。

![[Web アプリケーション ファイアウォールの追加] ダイアログ ボックス](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## <a name="see-also"></a>関連項目
この記事では、Azure Security Center での監視機能の使用方法について説明しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* 「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」: Azure Security Center でセキュリティ設定を構成する方法について説明します。
* 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」: セキュリティの警告の管理と対応の方法について説明しています。
* 「[Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)」: パートナー ソリューションの正常性状態を監視する方法について説明しています。
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」: このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/): Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

