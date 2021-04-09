---
title: Azure API Management インスタンスの自動スケールの構成 | Microsoft Docs
description: このトピックでは、Azure API Management インスタンスの自動スケール動作を設定する方法について説明します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 41a97c639d9148f2bff3e4f530363ea05a65a7b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018251"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Azure API Management インスタンスを自動的にスケーリングする  

Azure API Management サービス インスタンスは、一連の規則に基づいて自動的にスケーリングできます。 この動作は Azure Monitor を使用して有効化および構成でき、Azure API Management サービスの **Standard** および **Premium** レベルでのみサポートされています。

この記事では、自動スケールの構成手順を説明し、自動スケール規則の最適な構成を提案します。

> [!NOTE]
> **従量課金** レベルの API Management サービスは、トラフィックに基づいて自動的にスケールされます。追加の構成は必要ありません。

## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、以下が必要です。

+ 有効な Azure サブスクリプションを持っている。
+ Azure API Management インスタンスがある。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。
+ [Azure API Management インスタンスの容量](api-management-capacity.md)の概念を理解している。
+ [Azure API Management インスタンスの手動スケーリングのプロセス](upgrade-and-scale.md)について、コストの影響を含めて理解している。

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Azure API Management の自動スケールの制限事項

自動スケールの動作を構成する前に、スケーリングの決定に関する特定の制限および影響を考慮する必要があります。

+ 自動スケールは、Azure API Management サービスの **Standard** および **Premium** レベルでのみ有効化できます。
+ 価格レベルもサービス インスタンスの最大ユニット数を指定します。
+ スケーリング処理には少なくとも 20 分ほどかかります。
+ サービスが別の操作によってロックされている場合、スケーリング要求は失敗し、自動的に再試行します。
+ マルチリージョン デプロイのサービスの場合、**プライマリの場所** にあるユニットのみスケーリング可能です。 他の場所にあるユニットはスケーリングできません。

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Azure API Management サービスの自動スケールの有効化と構成

Azure API Management サービスの自動スケーリングを構成するには、次の手順に従います。

1. Azure Portal で **[Monitor]\(モニター\)** インスタンスに移動します。

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. 左側のメニューから **[自動スケール]** を選択します。

    ![Azure Monitor 自動スケールのリソース](media/api-management-howto-autoscale/02.png)

3. ドロップダウン メニューのフィルターに基づいて、Azure API Management サービスを探します。
4. 目的の Azure API Management サービス インスタンスを選択します。
5. 新しく開いたセクションで、 **[自動スケールの有効化]** ボタンをクリックします。

    ![Azure Monitor 自動スケール有効](media/api-management-howto-autoscale/03.png)

6. **[Rules]\(規則\)** セクションで、 **[+ Add a rule]\(+ 規則の追加\)** をクリックします。

    ![Azure Monitor 自動スケールの規則追加](media/api-management-howto-autoscale/04.png)

7. 新しいスケールアウト規則を定義します。

   たとえば、過去 30 分間の平均容量メトリックが 80% を超えたら Azure API Management ユニットの追加をトリガーするスケールアウト規則を定義できます。 次の表に、そのような規則の構成を示します。

    | パラメーター             | 値             | Notes                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | メトリックのソース         | 現在のリソース  | 現在の Azure API Management リソース メトリックに基づく規則を定義します。                                                                                                                                                                                                     |
    | *条件*            |                   |                                                                                                                                                                                                                                                                                 |
    | 時間の集計      | Average           |                                                                                                                                                                                                                                                                                 |
    | メトリックの名前           | 容量          | 容量メトリックは、Azure API Management インスタンスのリソース使用状況を反映する Azure API Management メトリックです。                                                                                                                                                            |
    | 時間グレインの統計  | Average           |                                                                                                                                                                                                                                                                                 |
    | 演算子              | より大きい      |                                                                                                                                                                                                                                                                                 |
    | Threshold             | 80%               | 平均容量メトリックのしきい値。                                                                                                                                                                                                                                 |
    | 期間 (分) | 30                | 容量メトリックの平均値を算出するタイムスパンは使用パターンに固有です。 期間を長くすればするほど、反応は平滑化されます (断続的なスパイクがスケールアウトの決定にあまり影響しなくなります)。 ただし、スケールアウトのトリガーも遅れることになります。 |
    | *操作*              |                   |                                                                                                                                                                                                                                                                                 |
    | 操作             | カウントを増やす量 |                                                                                                                                                                                                                                                                                 |
    | インスタンス数        | 1                 | Azure API Management インスタンスを 1 ユニットずつスケールアウトします。                                                                                                                                                                                                                          |
    | クールダウン (分)   | 60                | Azure API Management サービスのスケールアウトには少なくとも 20 分ほどかかります。ほとんどの場合、クールダウン期間を 60 分にすれば、多くのスケールアウトがトリガーされなくなります。                                                                                                  |

8. **[追加]** をクリックして規則を保存します。

    ![Azure Monitor スケールアウト規則](media/api-management-howto-autoscale/05.png)

9. **[+ Add a rule]\(+ 規則の追加\)** をもう一度クリックします。

    次に、スケールイン規則を定義する必要があります。 この規則は、API の使用量が減少したときにリソースが無駄にならないことを保証します。

10. 新しいスケールイン規則を定義します。

    たとえば、過去 30 分間の平均容量メトリックが 35% を下回ったら Azure API Management ユニットの削除をトリガーするスケールイン規則を定義できます。 次の表に、そのような規則の構成を示します。

    | パラメーター             | 値             | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | メトリックのソース         | 現在のリソース  | 現在の Azure API Management リソース メトリックに基づく規則を定義します。                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *条件*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 時間の集計      | Average           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | メトリックの名前           | 容量          | スケールアウト規則に使用したものと同じメトリックです。                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | 時間グレインの統計  | Average           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 演算子              | より小さい         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Threshold             | 35%               | スケールアウト規則と同様、この値は Azure API Management の使用状況パターンに大きく依存します。 |
    | 期間 (分) | 30                | スケールアウト規則に使用したものと同じ値です。                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *操作*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 操作             | カウントを減らす量 | スケールアウト規則に使用したものの逆です。                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | インスタンス数        | 1                 | スケールアウト規則に使用したものと同じ値です。                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | クールダウン (分)   | 90                | スケールインはスケールアウトよりも控え目であることが望ましいため、クール ダウン期間を長くしてください。                                                                                                                                                                                                                                                                                                                                                                                                    |

11. **[追加]** をクリックして規則を保存します。

    ![Azure Monitor スケールイン規則](media/api-management-howto-autoscale/06.png)

12. Azure API Management ユニットの **最大** 数を設定します。

    > [!NOTE]
    > Azure API Management には、インスタンスをスケールアウトできるユニット数の制限があります。 制限はサービス レベルによって異なります。

    ![Azure API Management ユニットの最大数を設定する場所が強調表示されているスクリーンショット。](media/api-management-howto-autoscale/07.png)

13. **[保存]** をクリックします。 自動スケールが構成されました。

## <a name="next-steps"></a>次のステップ

- [複数の Azure リージョンに Azure API Management サービス インスタンスをデプロイする方法](api-management-howto-deploy-multi-region.md)
- [クラウドの支出を最適化して節約する](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)