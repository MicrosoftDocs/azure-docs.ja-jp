---
title: Azure Advisor の概要 | Microsoft Docs
description: Azure Advisor を使用して、Azure のデプロイを最適化します。
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: kasparks
ms.openlocfilehash: 2ccac3bf9a882dc021c6c969946ad9d439a7cf5d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069687"
---
# <a name="introduction-to-azure-advisor"></a>Azure Advisor の概要

Azure Advisor の主な機能について説明し、よく寄せられる質問の回答を示します。

## <a name="what-is-advisor"></a>Advisor とは
Azure Advisor は、個人用に設定されたクラウド コンサルタントで、ベスト プラクティスに従って Azure デプロイメントを最適化します。 Azure のリソースの構成と利用統計情報を分析し、Azure リソースの費用対効果、パフォーマンス、高可用性、およびセキュリティを向上させるために役立つソリューションを推奨します。

Advisor では、以下の項目を実行できます。
* 先の見通しを持ち、処理が可能で、個人用に設定されたベスト プラクティスの推奨事項を取得する。 
* リソースのパフォーマンス、セキュリティ、および高可用性を向上させながら、総合的な Azure の支出を削減する機会を捉える。
* アクション提案をインラインで含めた推奨事項を取得する。

Advisor は、[Azure Portal](https://aka.ms/azureadvisordashboard) を通してアクセスできます。 [ポータル](https://portal.azure.com)にサインインし、ナビゲーション メニューの **[Advisor]** を見つけるか、 **[すべてのサービス]** メニューで Advisor を検索します。

Advisor ダッシュボードに、すべてのサブスクリプションの個人用に設定された推奨事項が表示されます。  フィルターを適用して、特定のサブスクリプションやリソースの種類の推奨事項を表示できます。  推奨事項は、4 つのカテゴリに分割されています。 

* **高可用性**: ビジネスに不可欠なアプリケーションの継続稼働を確保し、さらに向上させることができます。 詳細については、「[Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)」を参照してください。
* **セキュリティ**: セキュリティ侵害に至る可能性がある脅威と脆弱性を検出します。 詳細については、「[Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)」を参照してください。
* **パフォーマンス**: アプリケーションの速度を向上させます。 詳細については、「[Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)」を参照してください。
* **コスト**: Azure の全体的な支出を最適化し、削減します。 詳細については、「[Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)」を参照してください。

  ![Advisor の推奨事項の種類](./media/advisor-overview/advisor-dashboard.png)

カテゴリをクリックして、そのカテゴリ内の推奨事項の一覧を表示し、推奨事項を選択して詳細を確認できます。  また、実行できるアクションを確認して、機会を活用したり、問題を解決したりできます。

![Advisor の推奨事項のカテゴリ](./media/advisor-overview/advisor-ha-category-example.png) 

推奨事項を実装するには、その推奨事項の推奨されるアクションを選択します。  推奨事項を実装したり、実装に役立つドキュメントを参照したりできる、シンプルなインターフェイスが開きます。  推奨事項の実装後、Advisor が認識するまで最大 1 日かかることがあります。

推奨事項のアクションをすぐに実行しない場合は、推奨事項を一定期間後に延期することも、無視することもできます。  特定のサブスクリプションまたはリソース グループの推奨事項を受け取らない場合は、指定したサブスクリプションとリソース グループの推奨事項だけを生成するように Advisor を構成できます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-do-i-access-advisor"></a>Advisor にアクセスする方法は?
Advisor は、[Azure Portal](https://aka.ms/azureadvisordashboard) を通してアクセスできます。 [ポータル](https://portal.azure.com)にサインインし、ナビゲーション メニューの **[Advisor]** を見つけるか、 **[すべてのサービス]** メニューで Advisor を検索します。

仮想マシンのリソース インターフェイスを使用して、Advisor の推奨事項を表示することもできます。 仮想マシンを選択し、メニューで [Advisor の推奨事項] までスクロールします。 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Advisor にアクセスするために必要なアクセス許可は?
 
サブスクリプションの*所有者*、*共同作成者*、または*閲覧者*として Advisor の推奨事項にアクセスできます。

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Advisor が推奨事項を提供するリソースは?

Advisor では、Application Gateway、App Services、可用性セット、Azure Cache、Azure Data Factory、Azure Database for MySQL、Azure Database for PostgreSQL、Azure Database for MariaDB、Azure ExpressRoute、Azure Cosmos DB、Azure パブリック IP アドレス、SQL Data Warehouse、SQL サーバー、ストレージ アカウント、Traffic Manager プロファイル、および仮想マシンに対する推奨事項が提供されます。

Azure Advisor には、[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations) からの推奨事項も含まれ、別のリソースの種類に対する推奨事項が含まれる可能性があります。

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>推奨事項は延期したり無視したりできるか?

推奨事項を延期するか無視するには、 **[延期]** リンクをクリックします。 延期期間を指定するか、 **[Never]** を選択して推奨事項を無視できます。

## <a name="next-steps"></a>次の手順

Advisor の推奨事項の詳細については、以下を参照してください。

* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)
