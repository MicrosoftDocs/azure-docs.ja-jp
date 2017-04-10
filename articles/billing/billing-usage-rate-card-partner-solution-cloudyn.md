---
title: "Cloudyn は Microsoft Azure Usage API と RateCard API を使用してユーザー向けに ITFM を提供 | Microsoft Docs"
description: "Microsoft Azure 課金パートナーの Cloudyn が Azure Billing API を製品に統合した独自の事例について説明します。  この事例は、Cloudyn for Azure サービスの使用または試用に関心を持っている Azure と Cloudyn のユーザーに特に役立ちます。"
services: 
documentationcenter: 
author: BryanLa
manager: ruchic
editor: 
tags: billing
ms.assetid: f1397397-7e92-4c20-9862-ab6b93afefb7
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 02/03/2017
ms.author: mobandyo;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 09c18ca6a967c2930ddd2b16d37f0bc606712ea1
ms.openlocfilehash: c4bb977fc201a1da037ee174c883a5174633d45b


---
# <a name="microsoft-azure-usage-and-ratecard-apis-enable-cloudyn-to-provide-itfm-for-customers"></a>Cloudyn は Microsoft Azure Usage API と RateCard API を使用してユーザー向けに ITFM を提供
Microsoft 開発パートナーであり、クラウド管理機能のトップクラスのプロバイダーである Cloudyn は、新しい Microsoft Resource Usage API と RateCard API のプライベート プレビューに選択されました。  Usage API を使用すると、サブスクリプションの見積 Azure 消費データにアクセスできます。 RateCard API は、Enterprise Agreement (EA) 以外のユーザー向けに、すべての Azure サービスの詳細な料金情報を提供します。 これらの API は統合されているので、Cloudyn が提供するツールなど、IT 財務管理 (ITFM) ツールの入力に完全な情報ベースを提供できます。

## <a name="introduction"></a>はじめに
Usage API のデータと、RateCard API のデータ (使用状況[単位]料金[$単位] = 詳細な使用状況とコスト) のいわゆる "かけ算" によって、現在 Azure で使用できる最も細かく正確で信頼性が高い情報が作成されます。

![ITFM の概要][1]

これらの API を使用すると、ユーザーの使用状況とコストに関する主要な情報を取得できます。また、Cloudy では、シンプルなプログラムを使った方法でユーザー アカウントを分析し、ユーザーに対して多様な ITFM タスクを実行できます。

## <a name="integrating-cloudyn-with-the-ratecard-and-usage-apis"></a>Cloudyn と RateCard API および Usage API との統合
RateCard API には、いくつかの入力パラメーターが必要です (リージョン情報、通貨、ロケールなど)。その中で最も重要なパラメーターは OfferDurableID です。OfferDurableID では、ユーザーが使用している Azure プラン タイプを指定します (従量課金制、以前の 6 か月および 12 か月のコミットメント プラン、MSDN プラン、MPN プラン、キャンペーン プランなど)。 OfferDurableID は、[Azure の使用状況と課金ポータル](https://account.windowsazure.com/Subscriptions)で、サブスクリプションの下の "プラン ID" で確認できます。

[Cloudyn for Azure](https://www.cloudyn.com/microsoft-azure/) サービスを登録すると、OfferDurableID コードを追加できます。追加した後は、Cloudyn から RateCard API 経由で関連する料金情報を取得することができます。  各種プラン タイプについては、「[Microsoft Azure プランの詳細](https://azure.microsoft.com/support/legal/offer-details/)」ページを参照してください。

![Cloudyn ITFM エンジンの概要][2]

Cloudyn は、Azure Performance API に加え、Usage API と RateCard API の両方を使用して、視覚化、分析、アラート、レポート、コスト管理、実行可能な推奨事項という層を追加することで、信頼性の高いエンタープライズ クラウド ITFM ツールを Azure ユーザーに提供しています。

## <a name="cloudyn-itfm-use-cases-enabled-by-usage-and-ratecard-api-integration"></a>Usage API と RateCard API の統合によって可能になる Cloudyn ITFM の使用事例
Usage API と RateCard API で可能になる一般的な Cloudyn ITFM の使用事例を次に示します。

* **コスト分析** - 任意のネイティブ識別ディメンション (プロバイダー、サービス、アカウント、リージョンなど) にクラウド コストを分類できます。 Azure Usage API と RateCard API を使用すると、アカウントあたりの使用状況およびコストのデータをより細かく分類できるので、この作業が簡単になります。分類したデータは、Cloudyn でグループ化およびフィルター処理され、グラフ形式や表形式でユーザーに表示されます。

![コスト分析の円グラフ][3]

* **360 度のコスト割り当て** - 財務管理者と IT 管理者は、クラウドのデプロイに関する実際のコストの内訳、推進力、動向を把握できます。 また、管理者はデプロイ費用をビジネス ユニット、部門、リージョンなどに簡単に関連付けられるようになり、従来よりもクラウド コストを細かく把握し、企業のチャージバックとショーバックが容易になります。 Azure Usage API と RateCard API は、Cloudyn のコスト割り当てエンジンの入力として機能します。また、タグ付けされていないリソースとタグ付けできないリソースを割り当てる方法とビジネス ロジックを定義することで、これらの API を補完できます。

![コスト配分 360度グラフ][4]

* **コスト効率が高いサイズ設定** - あまり使用されていない仮想マシンの適切なサイズ設定の推奨がわかるので、サイズやプロビジョニングが過剰なコンピューターにかかる費用を削減できます。 サイズ設定には、仮想マシンの CPU と RAM のメトリック (Performance API 経由)、実行時間 (Usage API 経由)、およびコスト (RateCard API 経由) を確認します。 Cloudyn は、あまり使用されていない CPU または RAM リソース (Performance) に基づいて適切なサイズ設定の推奨を提供し、VM 間の料金の差分 (RateCard) に、あまり使用されていないコンピューターの実際の利用時間 (Usage) をかけて節約の見積額を計算します。

![コスト効率の高いサイズ設定][5]

* **クラウド移行に関する推奨事項** - クラウド移行に関する財務上のアドバイスを提供します。 ユーザーが現在主要なクラウド ベンダーにデプロイしているクラウド リソースの現在のコストを確認し、Azure に同等のデプロイを行った場合のコストと比較します。 より細かく、リソースごとに、財務ベースの Azure 移行に関する推奨事項を提供します。 (パフォーマンス メトリックとユーザーの設定に基づいて) Azure に必要な同等のデプロイを評価した後、Cloudyn は RateCard API を使用して Azure の同等のデプロイのコストを評価します。
* **パフォーマンス レポート** - Azure Performance API で有効になります。パフォーマンス レポートには、CPU と RAM の使用率に基づいてさまざまな機能を提供され、最適化に関する推奨事項が示されます。 次に、インスタンス使用率レポートの例を示します。インスタンスは平均 CPU 使用率で分類されています。

![パフォーマンス レポート][6]

* **カテゴリ マネージャー** - 未整理のクラウド リソースに順序を付けることができる Cloudyn の強力な機能です。 実際の業務に合わせて、測定とレポートを効率化する一意のカテゴリ (タグ) を自由に作成できます。 さらに、一貫性のないタグ付け (誤植などの矛盾) の調整と分類を簡単にすることができます。また、タグが付けられていないリソースを自動的に検出して、コストに正確な属性を付けることができます。

![カテゴリ マネージャー][7]

## <a name="video"></a>ビデオ
Azure ユーザーが Cloudyn for Azure と Azure Billing API を使用して Azure 消費データを把握する方法を説明している短時間のビデオです。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Cloudyn-Provides-Cloud-ITFM-Tools-Via-Microsoft-Azure-APIs/player]
> 
> 

## <a name="next-steps"></a>次のステップ
* 無料の [Cloudyn for Azure](https://www.cloudyn.com/microsoft-azure/) 試用版を利用し、Microsoft Azure クラウド デプロイ用にカスタマイズしたレポートと分析を使用してコストを透明化する方法について説明します。
* Azure Resource Usage API と RateCard API の概要については、「 [Microsoft Azure のリソース消費を把握する](billing-usage-rate-card-overview.md) 」を参照してください。
* 両方の API の詳細については、「 [Azure Billing REST API リファレンス](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) 」を参照してください (Azure リソース マネージャーに用意されている API セットに含まれています)。
* サンプル コードをすぐに確認するには、「 [Azure コード サンプル](https://azure.microsoft.com/documentation/samples/?term=billing)」の「Microsoft Azure Billing API コード サンプル」を参照してください。

## <a name="learn-more"></a>詳細情報
* Microsoft Azure エンタープライズ契約 (EA) プランの詳細については、「 [エンタープライズ向け Azure のライセンス](https://azure.microsoft.com/pricing/enterprise-agreement/)
* Azure リソース マネージャーの詳細については、「 [Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md) 」を参照してください。
* クラウドの使用量を把握するために必要なその他のツール群については、Gartner の記事「 [IT 財務管理 (ITFM) ツールの市場ガイド](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb)」を参照してください。

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png



<!--HONumber=Feb17_HO2-->


