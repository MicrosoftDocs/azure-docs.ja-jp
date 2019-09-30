---
title: Azure Security Center のセキュリティ アラート | Microsoft Docs
description: このトピックでは、セキュリティ アラートとは何かと、Azure Security Center で利用できるさまざまな種類について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 3b4b02574c028822d25d841376b127a718243b2e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202563"
---
# <a name="security-alerts-in-azure-security-center"></a>Azure Security Center のセキュリティ アラート

Azure Security Center には、さまざまなリソースの種類に対する各種のアラートがあります。 Azure Security Center では、Azure にデプロイされたリソースに対して、またオンプレミス環境とハイブリッド クラウド環境にデプロイされたリソースに対してもアラートが生成されます。

高度な検出は、Azure Security Center の Standard レベルで利用できます。 無料試用版が提供されています。 アップグレードは、 [[セキュリティ ポリシー]](security-center-pricing.md)の [価格レベル] の選択から実行できます。 価格の詳細については、 [Security Center のページ](https://azure.microsoft.com/pricing/details/security-center/) を参照してください。

## 今日の脅威への対応 <a name="respond-threats"> </a>

脅威を取り巻く状況は、この 20 年で様変わりしました。 従来、企業が警戒すべきことは一般に Web サイトの改ざんのみで、その攻撃も興味本位の個人によるものが大半でした。 その頃と比べ今日の攻撃は、はるかに進化しており、組織化されています。 明らかに金銭や戦略的な目的をもって攻撃が遂行されるケースも少なくありません。 攻撃に利用されるリソースも増えてきました。国家や犯罪組織によって資金提供されている場合があるためです。

このような移り変わる現実に導かれ、攻撃者集団はかつてないほど高い技術力を身に付けています。 もはや彼らのねらいは、Web の改ざんではありません。 情報や金融口座、プライベート データの盗難がねらいです。いずれも一般市場で現金を得たり、特定の事業や政治、軍事的な立場を利用したりする目的で使用されます。 金銭目的のこのような攻撃者以上にやっかいな存在は、インフラストラクチャや人に危害を加えるためにネットワークを侵害する攻撃者です。

そうした攻撃に対応するために多くの組織が実施しているさまざまな解決策は、既に知られている攻撃のシグネチャを探すことで企業ネットワークの境界領域やエンドポイントを防御する対症療法が中心です。 このような解決策では、精度の低いアラートが大量に生成されることが多く、セキュリティ アナリストが優先順位を付けて調査しなければなりません。 しかし、そのようなアラートに対応できるだけの時間とノウハウを持った組織はまれで、多くのアラートは未解決のまま放置されます。  

さらに、攻撃者の手法は着々と進化を遂げています。シグネチャを使用した防御はその多くが攻略され、[クラウド環境への対応](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/)も進んでいます。 発生している脅威を従来以上に早く特定し、検出と対応にかかる時間を短縮する新しいアプローチが必要です。

## <a name="what-are-security-alerts"></a>セキュリティの警告とは何か

アラートは、Security Center がリソース上の脅威を検出したときに生成する通知です。 Security Center では、アラートに優先順位を付け、問題の迅速な調査に必要な情報と共に一覧表示します。 Security Center は、攻撃をどのように修復できるかに関する推奨事項も提供します。

## Security Center での脅威の検出方法 <a name="detect-threats"> </a>

マイクロソフトのセキュリティ研究員は、絶えず脅威に目を光らせています。 クラウドやオンプレミスでの Microsoft のグローバルなプレゼンスにより、Microsoft は広範なテレメトリにアクセスすることが許されています。 この広範かつ多様なデータセットのコレクションによって、オンプレミスの消費者向け/企業向け製品からオンライン サービスに至るまで、攻撃の新しいパターンや傾向を把握することができます。 攻撃者によって脆弱性の悪用手法が次々に生み出され、しだいに複雑化する中で、Security Center は、その検出アルゴリズムを更新することができるのです。 この手法が、目まぐるしい脅威の環境の変化に対応するために役立てられます。

Security Center では、真の脅威を検出し、誤検知を減らすために、Azure のリソースやネットワークから、ログ データを収集、分析、統合します。 接続されているパートナー ソリューション (ファイアウォールやエンドポイント保護ソリューションなど) とも連動します。 Security Center は、この情報を分析し、多くの場合、複数の情報源から得た情報との関連性を探りながら、脅威を特定します。

![Security Center Data collection and presentation](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Security Center には、シグネチャ ベースの手法とは比較にならない高度なセキュリティ分析が採用されています。 ビッグ データや [機械学習](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) における革新的テクノロジを活かし、クラウド ファブリック全体にわたってイベントが評価されるので、手作業に頼った手法や攻撃の進化を予測する手法では特定できない脅威でも検出することができます。 こうしたセキュリティ分析の例を次に示します。

* **統合された脅威インテリジェンス**:グローバルな脅威インテリジェンスを活用し、既知の有害因子を探します。情報源としては、Microsoft 製品とサービス、Microsoft Digital Crimes Unit (DCU)、Microsoft Security Response Center (MSRC)、外部フィードがあります。
* **行動分析**:既知のパターンを適用することによって悪質な行動を検出します。
* **異常検出**:統計プロファイルを使用して、過去のベースラインを構築します。 確立された基準からの逸脱に、攻撃ベクトルとの一致が疑われる場合、警告が生成されます。

次のトピックでは、各分析について詳しく説明します。

### <a name="integrated-threat-intelligence"></a>統合された脅威インテリジェンス

Microsoft は、膨大なグローバル脅威インテリジェンスを保有しています。 Azure、Office 365、Microsoft CRM オンライン、Microsoft Dynamics AX、outlook.com、MSN.com、Microsoft Digital Crimes Unit (DCU)、Microsoft セキュリティ レスポンス センター (MSRC) などさまざまなソースからテレメトリが集められます。 また研究員も、大手クラウド サービス プロバイダー間で共有されている脅威インテリジェンス情報を入手しているほか、サード パーティから脅威インテリジェンス フィードをサブスクライブしています。 Azure Security Center はこの情報を基に、既知の有害因子から生じる脅威について利用者に警告を発することができます。

### <a name="behavioral-analytics"></a>行動分析

行動分析は、データを分析し、既知のパターンのコレクションと照らして比較する手法です。 ただし、これらのパターンはただのシグネチャではありません。 大量のデータセットに適用された複雑な機械学習アルゴリズムを通じてパターンが決定されています。 また、パターンの特定にあたっては、専門のアナリストによって悪質な行動が緻密に分析されます。 Azure Security Center は行動分析を使用し、仮想マシンのログや仮想ネットワーク デバイスのログ、ファブリック ログ、クラッシュ ダンプなど各種ソースの分析に基づいて、セキュリティの侵害されたリソースを特定します。

また、他のシグナルとの間には、蔓延している攻撃の裏付けとなる兆候を確認できる相関関係が存在します。 この相関関係によって、セキュリティ侵害の証拠として確立されたインジケーターと一致しているイベントが特定されます。 

### <a name="anomaly-detection"></a>異常検出

Azure Security Center での脅威の特定には、異常検出も使用されます。 行動分析は、大規模なデータセットから導いた既知のパターンに依存します。これとは対照的に、異常検出は "独自色" が強く、個々の環境に固有の基準に重点が置かれます。 機械学習を適用して個々の環境の正常なアクティビティを突き止めたうえで、セキュリティ イベントの可能性を示す異常な条件とは何かを定義する規則を作成します。

## <a name="continuous-monitoring-and-assessments"></a>継続的な監視と評価

Azure Security Center では、脅威に関する状況の変化を継続的に監視する Microsoft が全社にわたり配置するセキュリティ調査/データ サイエンス チームを活用しています。 たとえば次のような取り組みが行われています。

* **脅威インテリジェンスの監視**:脅威インテリジェンスには、既存の脅威や新たに発生した脅威に関するメカニズム、インジケーター、示唆、即時に利用可能なアドバイスが含まれます。 こうした情報はセキュリティ コミュニティから得られるほか、マイクロソフトも、社内や社外のソースから提供される脅威インテリジェンスを絶えず監視しています。
* **シグナルの共有**:クラウドとオンプレミス向けに Microsoft が保有するさまざまなサービス、サーバー、クライアント エンドポイント デバイスを通じてセキュリティ チームが得た知見は共有され、分析されます。
* **Microsoft のセキュリティ スペシャリスト**:フォレンジクスや Web 攻撃検出など、専門のセキュリティ分野に従事する Microsoft 内のさまざまなチームと絶えず連携します。
* **検出のチューニング**:実際のユーザーのデータセットに対してアルゴリズムが実行され、セキュリティ研究員がユーザーと連携してその結果を検証します。 機械学習アルゴリズムの精度を高めるために、真陽性と偽陽性が使用されます。

こうしたさまざまな取り組みを通じて、これまでできなかったような検出が可能となり、検出の精度が向上しています。しかもその恩恵はすぐに得ることができます。ユーザーが措置を講じる必要はありません。

## セキュリティ アラートの種類 <a name="security-alert-types"> </a>

次のトピックでは、リソースの種類に応じてさまざまなアラートを説明します。

* [IaaS VM とサーバーのアラート](security-center-alerts-iaas.md)
* [ネイティブ コンピューティングのアラート](security-center-alerts-compute.md)
* [データ サービスのアラート](security-center-alerts-data-services.md)

次のトピックでは、Security Center が、Azure にデプロイされたリソースに追加の保護レイヤーを適用するために、Azure インフラストラクチャとの統合から収集したさまざまなテレメトリを活用する方法について説明します。

* [サービス層のアラート](security-center-alerts-service-layer.md)
* [Azure のセキュリティ製品との統合](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>セキュリティ インシデントとは

セキュリティ インシデントは、各アラートの個別の一覧ではなく、関連するアラートのコレクションです。 Security Center では、[クラウドのスマート アラートの関連付け](security-center-alerts-cloud-smart.md)を使用して、さまざまなアラートと忠実度が低いシグナルをセキュリティ インシデントに関連付けます。

Security Center はインシデントを使用して、攻撃活動とすべての関連するアラートを単一のビューに表示します。 このビューにより、攻撃者がどのようなアクションを実行し、どのリソースが影響を受けたかを迅速に把握できます。 詳細については、[クラウドのスマート アラートの関連付け](security-center-alerts-cloud-smart.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

この記事では、Security Center で利用できるさまざまな種類のアラートについて説明しました。 詳細については、次を参照してください。

* [Azure Security Center 計画および運用ガイド](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center のよく寄せられる質問 (FAQ)](https://docs.microsoft.com/azure/security-center/security-center-faq)。

