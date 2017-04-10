---
title: "Azure Advisor の概要 | Microsoft Docs"
description: "Azure Advisor を使用して、Azure のデプロイを最適化します。"
services: advisor
documentationcenter: NA
author: kumudd
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
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: 3c4f6d13dd43ae557b177883e14044e637bd803b
ms.lasthandoff: 02/21/2017

---

# <a name="introduction-to-azure-advisor"></a>Azure Advisor の概要

Azure Advisor の主な機能とよく寄せられる質問について説明します。

## <a name="what-is-azure-advisor"></a>Azure Advisor とは
Azure Advisor は、ベスト プラクティスに従って Azure のデプロイを最適化できるようにする、個人用に設定されたクラウド コンサルタントです。 Azure のリソースの構成と使用率テレメトリを分析し、リソースの費用対効果、パフォーマンス、高可用性、およびセキュリティを向上させるために役立つソリューションを推奨します。

Azure advisor では、以下を実行できます。
-    先を見越したすぐに実施可能な個人向けのベスト プラクティスの推奨事項を取得する 
-    リソースのパフォーマンス、セキュリティ、および高可用性を向上させながら、総合的な Azure の支出を削減する
-    インライン アクション付きの推奨事項を取得する

Advisor は、[Azure Portal](https://aka.ms/azureadvisordashboard) を通してアクセスできます。 [ポータル](https://portal.azure.com)にサインインし、**[参照]** を選択し、**[Azure Advisor]** までスクロールします。 Advisor ダッシュボードに、選択したサブスクリプションの個人向けの推奨事項が表示されます。 推奨事項は、4 つのカテゴリに分割されています。 

-   **高可用性** – ビジネスに不可欠なアプリケーションの継続稼働を保証し、さらに向上させることができます。 詳細については、「[Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)」を参照してください。

-   **セキュリティ** – 潜在的なセキュリティ違反に至る可能性がある脅威と脆弱性を検出します。 詳細については、「[Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)」を参照してください。

-   **パフォーマンス** – アプリケーションのスピードを強化します。 詳細については、「[Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)」を参照してください。

-   **コスト** – 総合的な Azure の支出を最適化し、削減します。 詳細については、「[Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)」を参照してください。

  ![Advisor の推奨事項の種類](./media/advisor-overview/advisor-all-tab-examples.png)

> [!NOTE]
> Advisor の推奨事項にアクセスするには、最初にサブスクリプションを Advisor に**登録**する必要があります。 **サブスクリプションの所有者**が Advisor ダッシュボードを起動して **[推奨の取得]** ボタンをクリックすると、サブスクリプションが登録されます。 これは **1 回限りの操作**です。 サブスクリプションが登録されると、サブスクリプション、リソース グループ、特定のリソースの**所有者**、**共同作成者**、または**閲覧者**が Advisor の推奨事項にアクセスできます。

推奨事項をクリックすることで、追加情報を確認できます。 機会を活用したり、問題を解決したりするために実行できるアクションを確認することもできます。 Advisor は、推奨事項をインライン アクションまたはドキュメント リンク付きで提示します。 インライン アクションをクリックすると、“ガイド付きの手順” に従ってアクションを実装できます。 ドキュメント リンクをクリックすると、アクションを手動で実装する方法について説明するドキュメントが表示されます。 

Advisor は、1 時間ごとに推奨事項を更新します。 推奨事項に関するアクションを今すぐ実行しない場合は、推奨事項を一定期間再通知するか、無視できます。 

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-do-i-access-advisor"></a>Advisor にアクセスする方法は?
Advisor は、Azure Portal を通してアクセスできます。 ポータルにサインインし、**[参照]** を選択し、**[Azure Advisor]** までスクロールします。 仮想マシンの [リソース] ブレードから Advisor の推奨事項を表示することもできます。 仮想マシンを選択し、メニューで [Advisor の推奨事項] までスクロールします。 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Advisor にアクセスするために必要なアクセス許可は?

Advisor の推奨事項にアクセスするには、最初にサブスクリプションを Advisor に**登録**する必要があります。 サブスクリプションの所有者が Advisor ダッシュボードを起動して **[推奨の取得]** ボタンをクリックすると、サブスクリプションが登録されます。 これは **1 回限りの操作**です。 サブスクリプションが登録されると、サブスクリプション、リソース グループ、特定のリソースの**所有者**、**共同作成者**、または**閲覧者**が Advisor の推奨事項にアクセスできます。

### <a name="how-often-are-advisor-recommendations-updated"></a>Advisor の推奨事項が更新される頻度は?

Advisor の推奨事項は、1 時間ごとに更新されます。

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Advisor が推奨事項を提供するリソースは?

Advisor は、仮想マシン、可用性セット、Application Gateway、App Services、SQL Server、SQL Database、および Redis Cache に関する推奨事項を提供します。

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>推奨事項は再通知したり無視したりできるか?

推奨事項を再通知するか無視するには、**[再通知]** ボタンまたはリンクをクリックします。 再通知期間を指定するか、**[Never]** を選択して推奨事項を無視できます。

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項の詳細については、次のリソースをご覧ください。

-  [Advisor の使用を開始する](advisor-get-started.md)
-  [Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)
-  [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
-  [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
-  [Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)

