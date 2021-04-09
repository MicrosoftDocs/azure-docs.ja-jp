---
title: VM Insights での変更分析
description: VM Insights とアプリケーション変更分析統合を統合すると、仮想マシンに対して行われた変更のうち、パフォーマンスに影響を与えた可能性がある変更を表示できます。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 375812813d704eb9b48d0ed8fbbc65dd5e47da49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046773"
---
# <a name="change-analysis-in-vm-insights"></a>VM Insights での変更分析
VM Insights と[アプリケーション変更分析](../app/change-analysis.md)統合を統合すると、仮想マシンに対して行われた変更のうち、パフォーマンスに影響を与えた可能性がある変更を表示できます。

## <a name="overview"></a>概要
VM の動作が遅く、最近の設定変更によるパフォーマンスへの影響があったかどうかを調査したいとします。 VM Insights を使用して VM のパフォーマンスを確認したところ、過去 1 時間でメモリ使用量が増加していることがわかりました。 変更分析を行うことで、この時期に行われた構成変更がこの増加の原因であるかどうかを判断することができます。

アプリケーション変更分析サービスを使用することで、[Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md) からの変更だけでなく、Azure Resource Manager からのネットワーク セキュリティ ルールなどの入れ子になったプロパティの変更を集約します。 

## <a name="enabling-change-analysis"></a>変更分析を有効にする
VM Insights に変更分析をオンボードするには、*Microsoft.ChangeAnalysis* リソース プロバイダーを登録する必要があります。 このリソース プロバイダーは、Azure portal で VM Insights またはアプリケーション変更分析を初めて起動する際に自動的に登録されます。 アプリケーション変更分析は、リソースに対するパフォーマンスのオーバーヘッドが発生しない無料のサービスです。

## <a name="view-change-analysis"></a>変更分析を表示する
変更分析は、VM Insights の **[パフォーマンス]** または **[マップ]** タブで、 **[変更]** オプションを選択することでアクセスできます。 

[![[Investigate changes]\(変更の調査\)](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


**[Investigate changes]\(変更の調査\)** ボタンをクリックして、VM 用にフィルター処理されたアプリケーション変更分析ページを起動します。 一覧表示された変更を確認して、問題の原因となっている可能性のあるものがないかどうかを確認することができます。 特定の変更について不明な点がある場合は、 **[Change by]\(変更者\)** 列を参照して、変更を行ったユーザーを特定することができます。

[![変更の詳細](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>次のステップ
- [アプリケーション変更分析](../app/change-analysis.md)の詳細についてさらに学習します。
- [Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md) の詳細についてさらに学習します。 

