---
title: "Azure Advisor の概要 | Microsoft Docs"
description: "Azure Advisor を使用して、Azure のデプロイを最適化します。"
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 35678142550f9f887562f311a5e7d9516495cf53
ms.contentlocale: ja-jp
ms.lasthandoff: 03/31/2017

---

# <a name="introduction-to-azure-advisor"></a>Azure Advisor の概要

Azure Advisor の主な機能と、よく寄せられる質問について説明します。

## <a name="what-is-advisor"></a>Advisor とは
Azure Advisor は、個人用に設定されたクラウド コンサルタントで、ベスト プラクティスに従って Azure デプロイメントを最適化します。 Azure のリソースの構成と利用統計情報を分析し、Azure リソースの費用対効果、パフォーマンス、高可用性、およびセキュリティを向上させるために役立つソリューションを推奨します。

Advisor では、以下の項目を実行できます。
* 先の見通しを持ち、処理が可能で、個人用に設定されたベスト プラクティスの推奨事項を取得する。 
* リソースのパフォーマンス、セキュリティ、および高可用性を向上させながら、総合的な Azure の支出を削減する機会を捉える。
* アクション提案をインラインで含めた推奨事項を取得する。

Advisor は、[Azure Portal](https://aka.ms/azureadvisordashboard) を通してアクセスできます。 [ポータル](https://portal.azure.com)にサインインして、**[参照]** を選択し、**[Azure Advisor]** までスクロールします。 Advisor ダッシュボードに、選択したサブスクリプションの個人向けの推奨事項が表示されます。 

推奨事項は、4 つのカテゴリに分割されています。 

* **高可用性** – ビジネスに不可欠なアプリケーションの継続稼働を保証し、さらに向上させることができます。 詳細については、「[Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)」を参照してください。

* **セキュリティ** – セキュリティ侵害に至る可能性がある脅威と脆弱性を検出します。 詳細については、「[Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)」を参照してください。

* **パフォーマンス** – アプリケーションの速度を向上させます。 詳細については、「[Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)」を参照してください。

* **コスト** – 総合的な Azure の支出を最適化し、削減します。 詳細については、「[Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)」を参照してください。

  ![Advisor の推奨事項の種類](./media/advisor-overview/advisor-all-tab-examples.png)

> [!NOTE]
> Advisor の推奨事項にアクセスするには、最初に Advisor に*サブスクリプションを登録する*必要があります。 *サブスクリプションの所有者*が Advisor ダッシュボードを起動して **[推奨事項の取得]** ボタンをクリックすると、サブスクリプションが登録されます。 これは *1 回限りの操作*です。 サブスクリプションが登録されると、サブスクリプション、リソース グループ、または特定のリソースの*所有者*、*共同作成者*、または*閲覧者*として Advisor の推奨事項にアクセスできます。

推奨事項をクリックすると、追加情報を確認できます。 また、実行できるアクションを確認して、機会を活用したり、問題を解決したりできます。 

Advisor は、推奨事項をインライン アクションまたはドキュメント リンク付きで提示します。 インライン アクションをクリックすると、“ガイド付きの手順” に従ってアクションを実装できます。 ドキュメント リンクをクリックすると、アクションを手動で実装する方法を説明するドキュメントが表示されます。 

Advisor では、1 時間ごとに推奨事項が更新されます。 推奨事項に関するアクションをただちに実行する予定がない場合は、推奨事項を一定期間後に再通知させるか、無視することができます。 

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-do-i-access-advisor"></a>Advisor にアクセスする方法は?
Advisor は、[Azure Portal](https://aka.ms/azureadvisordashboard) を通してアクセスできます。 [ポータル](https://portal.azure.com)にサインインして、**[参照]** を選択し、**[Azure Advisor]** までスクロールします。 Advisor ダッシュボードに、選択したサブスクリプションの個人向けの推奨事項が表示されます。 

仮想マシンの [リソース] ブレードから Advisor の推奨事項を表示することもできます。 仮想マシンを選択し、メニューで [Advisor の推奨事項] までスクロールします。 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Advisor にアクセスするために必要なアクセス許可は?

Advisor の推奨事項にアクセスするには、最初に Advisor に*サブスクリプションを登録する*必要があります。 *サブスクリプションの所有者*が Advisor ダッシュボードを起動して **[推奨事項の取得]** ボタンをクリックすると、サブスクリプションが登録されます。 これは *1 回限りの操作*です。 サブスクリプションが登録されると、サブスクリプション、リソース グループ、または特定のリソースの*所有者*、*共同作成者*、または*閲覧者*として Advisor の推奨事項にアクセスできます。

### <a name="how-often-are-advisor-recommendations-updated"></a>Advisor の推奨事項が更新される頻度は?

Advisor の推奨事項は、1 時間ごとに更新されます。

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Advisor が推奨事項を提供するリソースは?

Advisor は、仮想マシン、可用性セット、アプリケーション ゲートウェイ、App Services、SQL Server、SQL Database、および Redis Cache に関する推奨事項を提供します。

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>推奨事項は再通知したり無視したりできるか?

推奨事項を再通知するか無視するには、**[再通知]** ボタンまたはリンクをクリックします。 再通知期間を指定するか、**[Never]** を選択して推奨事項を無視できます。

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項の詳細については、以下を参照してください。

* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)

