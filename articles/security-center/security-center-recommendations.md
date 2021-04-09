---
title: Security recommendations in Azure Security Center
description: このドキュメントでは、Azure セキュリティ センターでの推奨事項に従ってご使用の Azure のリソースを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 57760443746e111750e74ef55fc18729f6ba32c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100340"
---
# <a name="review-your-security-recommendations"></a>セキュリティの推奨事項を確認する

このトピックでは、Azure セキュリティ センターで推奨事項を確認し、理解し、Azure リソースの保護に役立てる方法について説明します。

## <a name="monitor-recommendations"></a>推奨事項の監視<a name="monitor-recommendations"></a>

Security Center はリソースのセキュリティの状態を分析して、潜在的な脆弱性を特定します。 

1. Security Center のメニューから **[推奨事項]** ページを開いて、お使いの環境に適用できる推奨事項を確認します。 推奨事項は、セキュリティ コントロールにグループ化されています。

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="セキュリティ コントロールにグループ化された推奨事項" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. リソースの種類、重大度、環境、あるいは自分にとって重要なその他の条件に固有の推奨を見つけるには、推奨事項一覧に任意のフィルターを使用してください。

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Azure Security Center 推奨事項一覧を絞り込むためのフィルター":::

1. コントロールを展開し、特定の推奨事項を選択して [推奨事項の詳細] ページを表示します。

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="推奨事項の詳細ページ。" lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    このページには次の項目が含まれています。

    1. サポートされている推奨事項については、上部のツールバーに次のボタンのいずれか、またはすべてが表示されます。
        - **適用** および **拒否** (「[適用/拒否の推奨事項を使用した構成ミスの防止](prevent-misconfigurations.md)」を参照)
        - 基になるポリシーの Azure Policy エントリに直接アクセスするための **ポリシー定義の表示**
    1. **重大度インジケーター**
    1. **更新間隔** (該当する場合)
    1. この推奨事項に対して除外が存在する場合は、除外されたリソースの数を示す **除外されたリソースの数**
    1. **説明** - 問題の簡単な説明
    1. **修正手順** - 影響を受けるリソースのセキュリティの問題を修正するために必要な手動の手順の説明。 "クイック修正" を含む推奨事項の場合は、推奨される修正プログラムをリソースに適用する前に、 **[修正ロジックを表示]** を選択することができます。 
    1. **影響を受けるリソース** - リソースはタブにグループ化されています。
        - **正常なリソース** - 影響を受けていないか、既に問題を修正した関連するリソース。
        - **異常なリソース** – 特定された問題の影響を受けているリソース。
        - **適用されないリソース** – 推奨事項で明確な回答を得ることができないリソース。 [適用されないリソース] タブには、各リソースの理由も含まれています。 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="理由が表示されている [適用されないリソース] タブ。":::
    1. 推奨事項を修復するか、ロジック アプリをトリガーするアクション ボタン。

## <a name="preview-recommendations"></a>推奨事項のプレビュー

**プレビュー** のフラグが設定されている推奨事項は、セキュリティ スコアの計算からは除外されます。

それでも、その推奨事項はプレビュー期間が終了した時点でスコアに反映されるため、可能な限り修復しておく必要があります。

プレビューの推奨事項の例を次に示します。

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="プレビュー フラグが設定された推奨事項":::
 
## <a name="next-steps"></a>次のステップ

このドキュメントでは、セキュリティ センターのセキュリティに関する推奨事項について説明しました。 関連情報:

- [推奨事項の修正](security-center-remediate-recommendations.md) - Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明します。
- [適用/拒否の推奨事項を使用した構成ミスの防止](prevent-misconfigurations.md)。
- [Security Center トリガーへの応答の自動化](workflow-automation.md) - 推奨事項への応答を自動化します
- [推奨事項からリソースを除外する](exempt-resource.md)
- [セキュリティの推奨事項 - リファレンス ガイド](recommendations-reference.md)