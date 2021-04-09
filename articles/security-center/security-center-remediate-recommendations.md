---
title: Azure Security Center でレコメンデーションを修復する | Microsoft Docs
description: この記事では、リソースを保護してセキュリティ ポリシーを満たすために、Azure Security Center でレコメンデーションに対応する方法について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: f382646c889d004738064cae2d09fd66d897b110
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438269"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Azure Security Center の修復レコメンデーション

レコメンデーションは、リソースのセキュリティを強化する方法を提案します。 レコメンデーションに記載されている修復手順に従って、レコメンデーションを実装します。

## <a name="remediation-steps"></a>修復手順 <a name="remediation-steps"></a>

すべてのレコメンデーションを確認したら、最初に修復するレコメンデーションを決定します。 セキュリティ スコアを上げる可能性が最も高いセキュリティ制御を最優先にすることをお勧めします。

1. 一覧から、レコメンデーションを選択します。

1. 「**修復の手順**」セクションの指示に従ってください。 レコメンデーションごとに、独自の指示のセットがあります。 HTTPS 経由のトラフィックのみを許可するようにアプリケーションを構成するための修復手順のスクリーンショットを次に示します。

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png" alt-text="レコメンデーションの手動修復手順" lightbox="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png":::

1. 完了すると、問題が解決されたかどうかを示す通知が表示されます。

## <a name="quick-fix-remediation"></a>クイック修復のレコメンデーション

修復を単純化し、環境のセキュリティを向上させる (そしてセキュリティ スコアを上げる) ため、多くのレコメンデーションにはクイック修正オプションが含まれています。

クイック修正を使用すると、複数のリソースに関するレコメンデーションを迅速に修復できます。

> [!TIP]
> クイック修正ソリューションは、特定のレコメンデーションにのみ使用できます。 使用可能なクイック修正があるレコメンデーションを確認するには、レコメンデーションの一覧に対して **対応アクション** フィルターを使用します。
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="レコメンデーション リストの上にあるフィルターを使用して、クイック修正オプションがあるレコメンデーションを見つける":::

クイック修正ソリューションを実装するには:

1. **クイック修復** ラベルを含むレコメンデーションの一覧から レコメンデーションを選択します。

    [![クイック修復を選択します](media/security-center-remediate-recommendations/security-center-quick-fix-select.png)](media/security-center-remediate-recommendations/security-center-quick-fix-select.png#lightbox)。

1. **[正常でないリソース]** タブで、レコメンデーションを実装するリソースを選択し、 **[修復]** を選択します。

    > [!NOTE]
    > 一覧表示されるリソースの一部は、変更するための適切なアクセス許可を持っていないために無効な場合があります。

1. 確認ボックスで、修復の詳細と影響を確認します。

    ![クイック修復](./media/security-center-remediate-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > 影響は、 **[修復]** をクリックした後に開かれる **[リソースの修復]** ウィンドウの灰色のボックスに表示されます。 ここには、クイック修復を進めたときに実行される変更内容が表示されます。

1. 必要に応じて関連するパラメーターを挿入し、修復を承認します。

    > [!NOTE]
    > 修復が完了してから **[正常なリソース]** タブにリソースが表示されるまでに数分かかる場合があります。修復アクションを表示するには、[アクティビティ ログ](#activity-log)を確認します。

1. 完了すると、修復が成功したかどうかを知らせる通知が表示されます。

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>アクティビティ ログでのクイック修復のログ<a name="activity-log"></a>

修復操作では、テンプレートのデプロイまたは REST PATCH API の呼び出しを使用して、リソースに構成を適用します。 これらの操作は、[Azure のアクティビティ ログ](../azure-resource-manager/management/view-activity-logs.md)に記録されます。


## <a name="next-steps"></a>次のステップ

このドキュメントでは、Security Center でレコメンデーションを修復する方法について説明しました。 セキュリティ センターの詳細については、次のページを参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md) - Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています
* [セキュリティ ポリシー、イニシアティブ、および推奨事項とは](security-policy-concept.md)