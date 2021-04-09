---
title: DDoS 対応戦略のコンポーネント
description: Azure DDoS Protection Standard を使用して DDoS 攻撃に対応する方法について説明します。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 2b31a8aa8b126c228ac7e9c3ca182300c710b098
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97814059"
---
# <a name="components-of-a-ddos-response-strategy"></a>DDoS 対応戦略のコンポーネント

Azure リソースを狙った DDoS 攻撃では通常、ユーザーの観点から必要とされる対応は最小限で済みます。 それでも、インシデント対応戦略の一環として DDoS 軽減を組み込むことで、ビジネス継続性への影響を最小限にできます。

## <a name="microsoft-threat-intelligence"></a>Microsoft の脅威インテリジェンス

Microsoft では、広範な脅威インテリジェンス ネットワークを構築しています。 このネットワークでは、Microsoft のオンライン サービスをサポートする広範なセキュリティ コミュニティ、Microsoft パートナー、およびインターネット セキュリティ コミュニティ内のリレーションシップの集合的知識が使用されます。 

Microsoft は、重要なインフラストラクチャ プロバイダーとして、脅威に関する警告を早期に受信します。 Microsoft では、自社のオンライン サービスとグローバルな顧客ベースから脅威インテリジェンスを収集し、 それらすべての脅威インテリジェンスを Azure DDoS Protection 製品に組み込んでいます。

また、Microsoft Digital Crimes Unit (DCU) では、ボットネットに対する攻撃的な戦略を実施しています。 ボットネットは、DDoS 攻撃のコマンドを発する一般的な制御元です。

## <a name="risk-evaluation-of-your-azure-resources"></a>Azure リソースのリスク評価

継続的に DDoS 攻撃のリスクの範囲を把握することが不可欠です。 次のことを定期的に確認するようにしてください。

- パブリックに使用可能な新しい Azure リソースのうち、保護が必要なものはあるか。

- サービスに単一障害点はあるか。 

- どのようにすれば、サービスを分離して攻撃の影響を制限しながら、有効な顧客がサービスを利用できるようにすることができるか。

- DDoS Protection Standard を有効にする必要があるのに有効になっていない仮想ネットワークがあるか。 

- サービスは複数のリージョンをまたがるフェールオーバーについてアクティブ/アクティブになっているか。

アプリケーションの通常の動作を理解し、DDoS 攻撃中にアプリケーションが想定の動作をしなくなった場合の対応を準備することが不可欠です。 クライアントの動作を模倣するビジネスクリティカルなアプリケーションに監視を構成し、関連する異常が検出された場合に通知されるようにします。 アプリケーションの正常性に関する分析情報を得るには、[監視と診断のベスト プラクティス](/azure/architecture/best-practices/monitoring#monitoring-and-diagnostics-scenarios)に関するセクションを参照してください。

[Azure Application Insights](../azure-monitor/app/app-insights-overview.md) は、複数のプラットフォームで使用できる Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 Application Insights を使用して、実行中の Web アプリケーションを監視します。 パフォーマンスに異常があると、自動的に検出されます。 組み込まれている分析ツールを使えば、問題を診断し、ユーザーがアプリを使用して実行している操作を把握できます。 Application Insights は、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。

## <a name="customer-ddos-response-team"></a>お客様の DDoS 対応チーム

DDoS 対応チームを作成することは、攻撃に効果的かつ迅速に対応するために重要なステップです。 計画と実行の両方を監視する組織内の担当者を確認しましょう。 この DDoS 応答チームは、Azure DDoS Protection Standard サービスのことを完全に理解している必要があります。 チームが内部や外部の顧客 (Microsoft サポート チームを含む) と連携して攻撃を特定し、緩和できる体制にあることを確認してください。 

サービスの可用性と継続性に関する計画の一部としてシミュレーション演習を使用することをお勧めします。これらの演習には、スケール テストを含める必要があります。 Azure パブリック エンドポイントに対して DDoS テスト トラフィックをシミュレートする方法については、「[シミュレーションを通じてテストする](test-through-simulations.md)」を参照してください。

## <a name="alerts-during-an-attack"></a>攻撃時のアラート

Azure DDoS Protection Standard は、ユーザーの介入がなくても DDoS 攻撃を識別して軽減します。 保護されているパブリック IP に対してリスク軽減がアクティブになった場合に通知を受け取るには、[アラートを構成する](alerts.md)ことができます。

### <a name="when-to-contact-microsoft-support"></a>どのようなときに Microsoft サポートに問い合わせるか

Azure DDoS Protection Standard のお客様は、DDoS Rapid Response (DRR) チームにアクセスできます。このチームは、攻撃中の攻撃の調査、攻撃後の分析を支援します。 DRR チームをいつ関与させるべきかなど、詳細については、「[DDoS Rapid Response](ddos-rapid-response.md)」を参照してください。

## <a name="post-attack-steps"></a>攻撃後の手順

攻撃を受けた後で事後分析を行い、必要に応じて DDoS 対応戦略を調整するのは、常によい戦略です。 また、以下の点を考慮してください。

- スケーラブルなアーキテクチャがないため、サービスまたはユーザーのエクスペリエンスに中断が発生したか。

- どのアプリケーションまたはサービスが最も影響を受けたか。

- DDoS 対応戦略の効果はどの程度で、どうすれば改善できるか。

DDoS 攻撃を受けている可能性がある場合は、通常の Azure サポート チャネルを通じてエスカレートしてください。

## <a name="next-steps"></a>次のステップ

- [DDoS 保護プランの作成](manage-ddos-protection.md)方法について説明します。