---
title: Azure Advisor の推奨事項ダイジェスト
description: アクティブな推奨事項の概要を定期的に取得します
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 1c82abf3c8bea07941f860b94fcfe92954fb7641
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579819"
---
# <a name="configure-periodic-summary-for-recommendations"></a>推奨事項の定期的な概要を構成する

Advisor の **推奨事項ダイジェスト** では、異なるカテゴリにわたって最新のアクティブな推奨事項を簡単かつプロアクティブに取得する手段が提供されます。 この機能を使用すると、さまざまなカテゴリについてのすべてのアクティブな推奨事項の概要が定期的に通知されるように構成することができます。 アクション グループを使用して、メールや SMS などの必要な通知チャネルを選択できます。 この記事では、Advisor の推奨事項に対して **推奨事項ダイジェスト** を設定する方法について説明します。


## <a name="setting-up-your-recommendation-digest"></a>推奨事項ダイジェストの設定 

**推奨事項ダイジェスト** の作成エクスペリエンスを使用すると、概要を構成できます。 構成には以下のパラメーターを選択できます。
1. カテゴリ: コスト、高可用性、パフォーマンス、オペレーショナル エクセレンスなどの推奨事項カテゴリがあります。 この機能は、セキュリティの推奨事項ではまだ使用できません。
2. ダイジェストの頻度: 概要通知の頻度を、週単位、隔週単位、月単位から指定できます。
3. アクション グループ: 既存のアクション グループを選択するか、新しいアクション グループを作成できます。 アクション グループの詳細については、[アクション グループの作成と管理](../azure-monitor/alerts/action-groups.md)に関する記事をご覧ください。
4. ダイジェストの言語
5. 推奨事項ダイジェストの名前: ダイジェストを追跡および監視しやすいように、ユーザー フレンドリな文字列を使用できます。

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Azure portal で推奨事項ダイジェストを作成する手順

**推奨事項ダイジェスト** を作成する手順を次に示します。
* **手順 1:** Azure portal で **[Advisor]** に移動し、 **[監視]** セクションで **[推奨事項ダイジェスト]** を選択します 

   ![推奨事項ダイジェストのエントリ ポイント](./media/digest-0.png)

* **手順2:** 次のように、上部のバーで **[新しい推奨事項ダイジェスト]** を選択します。

   ![推奨事項ダイジェストを作成する](./media/digest-5.png)

* **手順 3:** **[スコープ]** セクションで、ダイジェストの **[サブスクリプション]** を選択します

   ![推奨事項ダイジェストの入力を指定する](./media/digest-1.png)

* **手順 4:** **[条件]** セクションで、**カテゴリ**、**頻度**、**言語** などの構成を選択します

   ![推奨事項ダイジェストの入力で条件を指定する](./media/digest-2.png)

* **手順 5:** **[アクション グループ]** セクションで、ダイジェストの **アクション グループ** を選択します。 詳しくは、[アクション グループの作成と管理](../azure-monitor/alerts/action-groups.md)に関する記事をご覧ください

   ![推奨事項ダイジェストの入力でアクション グループを指定する](./media/digest-3.png)

* **手順 6:** **ダイジェストの詳細** に関するこの最後のセクションでは、推奨事項ダイジェストに名前と状態を割り当てることができます。 **[Create recommendation digest]\(推奨事項ダイジェストの作成\)** をクリックして、セットアップを完了します。
   ![推奨事項ダイジェストの作成を完了する](./media/digest-4.png)

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項について詳しくは、以下を参照してください。
* [Azure Advisor の概要](advisor-overview.md)
* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
* [Advisor の優れた運用の推奨事項](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
