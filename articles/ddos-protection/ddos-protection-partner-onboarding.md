---
title: Azure DDoS Protection Standard との提携
description: Azure DDoS Protection Standard によって可能になるパートナーの営業案件について説明します。
ms.service: ddos-protection
documentationcenter: na
author: yitoh
mms.devlang: na
ms.topic: how-to
ms.date: 08/28/2020
ms.author: kumud
ms.openlocfilehash: a7a21dcaea22ed0d1c077d0c42ddb5b4bd50d24f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539334"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Azure DDoS Protection Standard との提携
この記事では、Azure DDoS Protection Standard によって可能になるパートナーの営業案件について説明します。 この記事は、プロダクト マネージャーと営業開発の担当者が投資の道筋を理解し、パートナーとなる価値提案を分析できるように設計されています。

## <a name="background"></a>バックグラウンド
分散型サービス拒否 (DDoS) 攻撃は、アプリケーションをクラウドに移行している顧客から寄せられる可用性とセキュリティに関する主要な関心事の 1 つです。 DDoS 攻撃の背後には強要とハクティビズムが一般的な動機としてあり、比較的簡単で安価に開始できるため、発生の種類、規模、頻度は一貫して増加しています。

Azure DDoS Protection には、Azure ネットワークのグローバル スケールが利用した、最も高度な DDoS の脅威に対する対策が用意されています。 このサービスでは、仮想ネットワークにデプロイされたアプリケーションとリソースに対する DDoS 軽減機能が強化されています。

テクノロジ パートナーは、Azure DDoS Protection Standard を使用して顧客のリソースをネイティブで保護し、DDoS 攻撃による可用性と信頼性の問題に対応できます。

## <a name="introduction-to-azure-ddos-protection-standard"></a>Azure DDoS Protection Standard の概要
Azure DDoS Protection Standard には、レイヤー 3 およびレイヤー 4 の DDoS 攻撃に対する強化された DDoS 軽減機能が用意されています。 DDoS Protection Standard サービスの主な機能を次に示します。

### <a name="adaptive-real-time-tuning"></a>アダプティブ リアルタイム チューニング
Azure DDoS Protection Standard を使うと、保護されたアプリケーションごとに、アプリケーションのトラフィック プロファイル パターンに基づいて DDoS 軽減ポリシーのしきい値を自動的に調整できます。 このカスタマイズは、次の 2 つのインサイト手法を通じて実行されます。

- お客様ごと (IP ごと) に、レイヤー 3 および 4 のトラフィック パターンを自動学習する。
- 誤検出を最小限に減らす (大量のトラフィックを吸収できる Azure のスケールを活用したもの)。

![アダプティブ リアルタイム チューニング](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>攻撃分析、テレメトリ、監視、アラート
Azure DDoS Protection は、ユーザーの介入がなくても DDoS 攻撃を識別して軽減します。

- 保護されているリソースが Azure Security Center の対象となるサブスクリプションにある場合、保護されているアプリケーションに対する DDoS 攻撃が検出され、軽減されるたびに、DDoS Protection Standard からセキュリティ センターに自動的にアラートが送信されます。
- 保護されているパブリック IP に対してリスク軽減がアクティブになった場合に通知を受け取るには、メトリック Under DDoS attack or not (DDoS 攻撃中かどうか) に対して[アラートを構成する](alerts.md)ことができます。
- さらに、他の DDoS メトリックのアラートを作成し、[攻撃のテレメトリを構成](telemetry.md)して、攻撃の規模、ドロップしたトラフィック、攻撃ベクトル、主な寄与要素、その他の詳細を把握できます。

![DDoS メトリック](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS Rapid Response (DRR)
DDoS Protection Standard のお客様は、アクティブな攻撃中に [Rapid Response チーム](ddos-rapid-response.md)にアクセスできます。 DRR では、攻撃中の攻撃の調査と攻撃後の分析に関して支援を受けることができます。

### <a name="sla-guarantee-and-cost-protection"></a>SLA の保証とコスト保護
DDoS Protection Standard サービスには 99.99% の SLA が適用され、コスト保護により、ドキュメント化された攻撃中にスケールアウトするためのリソース クレジットが提供されます。 詳細については、「[Azure DDoS Protection の SLA](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/)」を参照してください。

## <a name="featured-partner-scenarios"></a>おすすめのパートナー シナリオ
Azure DDoS Protection Standard と統合することで得られる主な利点は次のとおりです。

- パートナーが顧客に提供するサービス (ロード バランサー、Web アプリケーション ファイアウォール、ファイアウォールなど) は、バックエンドの Azure DDoS Protection Standard によって自動的に保護されます (ホワイト ラベル付きになります)。
- パートナーは、Azure DDoS Protection Standard の攻撃分析とテレメトリにアクセスして、自社製品と統合し、統一されたカスタマー エクスペリエンスを実現できます。  
- パートナーは、Azure Rapid Response がない場合でも、DDoS 関連の問題について DDoS Rapid Response サポートを利用できます。
- パートナーの保護されたアプリケーションは、DDoS 攻撃が発生した場合の DDoS SLA 保証とコスト保護に支えられています。

## <a name="technical-integration-overview"></a>技術的な統合の概要
Azure DDoS Protection Standard パートナーの営業案件は、Azure portal、API、および CLI/PS を介して利用できます。

### <a name="integrate-with-ddos-protection-standard"></a>DDoS Protection Standard と統合する
パートナーが Azure DDoS Protection Standard との統合を構成するには、次の手順が必要です。
1. 目的の (パートナー) サブスクリプションで DDoS Protection プランを作成します。 詳細な手順については、[DDoS Standard 保護計画の作成](manage-ddos-protection.md#create-a-ddos-protection-plan)に関する記事を参照してください。
   > [!NOTE]
   > 特定のテナントに対して作成する必要がある DDoS Protection プランは 1 つだけです。 
2. ロード バランサー、ファイアウォール、Web アプリケーション ファイアウォールなど、パブリック エンドポイントを使用して (パートナーの) サブスクリプションにサービスを展開します。 
3. 最初の手順で作成した DDoS Protection プランを使用して、パブリック エンドポイントを持つサービスの仮想ネットワークで Azure DDoS Protection Standard を有効にします。 詳細な手順については、[DDoS Standard 保護計画を有効にする](manage-ddos-protection.md#enable-ddos-protection-for-an-existing-virtual-network)方法に関する記事を参照してください。
   > [!IMPORTANT] 
   > 仮想ネットワークで Azure DDoS Protection Standard を有効にすると、その仮想ネットワーク内のすべてのパブリック IP が自動的に保護されます。 これらのパブリック IP の発信元は、Azure (クライアント サブスクリプション) の内部または Azure の外部の可能性があります。 
4. 必要に応じて、Azure DDoS Protection Standard のテレメトリと攻撃分析をアプリケーション固有の顧客向けダッシュボードに統合します。 テレメトリの使用の詳細については、「[DDoS 保護テレメトリの表示と構成](telemetry.md)」を参照してください。 

### <a name="onboarding-guides-and-technical-documentation"></a>オンボード ガイドと技術ドキュメント

- [Azure DDoS Protection 製品のページ](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS Protection のドキュメント](ddos-protection-overview.md)
- [Azure DDoS Protection API リファレンス](/rest/api/virtualnetwork/ddosprotectionplans)
- [Azure 仮想ネットワーク API リファレンス](/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>ヘルプの参照

- Azure DDoS Protection Standard とのアプリケーション、サービス、または製品の統合について不明な点がある場合は、[Azure セキュリティ コミュニティ](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)に問い合わせてください。
- [Stack Overflow](https://stackoverflow.com/tags/azure-ddos/) に関するディスカッションに従ってください。

### <a name="get-to-market"></a>市場に投入する

- Microsoft と提携するための主なプログラムは、[Microsoft Partner Network](https://partner.microsoft.com/) です。 \- Microsoft Graph Security の統合は、[MPN 独立系ソフトウェア ベンダー (ISV)](https://partner.microsoft.com/saas-solution-guide) トラックに分類されます。
- [Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) は、Microsoft セキュリティ パートナー専用のプログラムです。セキュリティ製品を充実させ、Microsoft セキュリティ製品との統合を顧客が見つけやすくなります。

## <a name="next-steps"></a>次のステップ
既存のパートナーとの統合について確認してください。

- [Barracuda WAF-as-a-service](https://www.barracuda.com/waf-as-a-service)
- [Radware の Azure Cloud WAF](https://www.radware.com/resources/microsoft-azure/)
