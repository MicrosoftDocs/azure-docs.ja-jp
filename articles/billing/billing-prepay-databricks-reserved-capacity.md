---
title: 事前購入を利用して Azure Databricks のコストを最適化する
description: コスト削減のために容量が予約された Azure Databricks 料金を前払いする方法について説明します。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 99eb4de86aa227d558bec54d011a0b1548d27cf0
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "67810054"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>事前購入を利用して Azure Databricks のコストを最適化する

1 年または 3 年間の Azure Databricks コミット ユニット (DBCU) を事前に購入すると、Azure Databricks ユニット (DBU) のコストを節約することができます。 購入済みの DBCU は、購入期間中はいつでも使用できます。 VM とは異なり、事前購入したユニットは 1 時間単位で期限切れにならず、購入期間中はいつでも使用できます。

Azure Databricks の使用状況はすべて、事前購入済み DBU から自動的に差し引かれます。 事前購入割引を受けるために、事前購入済みプランを DBU の使用状況の Azure Databricks ワークスペースに再デプロイしたり、割り当てたりする必要はありません。

事前購入割引は、DBU の使用状況にのみ適用されます。 コンピューティング、ストレージ、およびネットワーキングなどの他の料金は別途課金されます。

## <a name="determine-the-right-size-to-buy"></a>購入する適切なサイズの判断

Databricks の事前購入は、すべての Databricks ワークロードおよびレベルに適用されます。 事前購入は、前払いの Databricks コミット ユニットのプールと考えることができます。 ワークロードまたはレベルには関係なく、使用状況がプールから差し引かれます。 使用状況は、次の比率で差し引かれます。

| **ワークロード** | **DBU の適用率 - Standard レベル** | **DBU の適用率 - Premium レベル** |
| --- | --- | --- |
| データ分析 | 0.4 | 0.55 |
| Data Engineering | 0.15 | 0.30 |
| Data Engineering Light | 0.07 | 0.22 |

たとえば、Data Analytics — Standard レベルの数量が使用された場合、事前購入済み Databricks コミット ユニットは 0.4 ユニット差し引かれます。

購入する前に、さまざまなワークロードとレベルについて、消費される DBU の合計数量を計算します。 前述の比率を使用して DBCU に正規化し、今後 1 年または 3 年間の合計使用量の予測を実行します。

## <a name="purchase-databricks-commit-units"></a>Databricks コミット ユニットの購入

Databricks プランは [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D) 内で購入できます。 予約容量を購入するには、少なくとも 1 つのエンタープライズ サブスクリプションに対して所有者ロールを所持している必要があります。

- 現時点では、事前購入はマイクロソフト エンタープライズ契約のお客様のみご利用いただけます。
- 少なくとも 1 つのエンタープライズ サブスクリプションに対する所有者ロールに属している必要があります。
- Enterprise サブスクリプションの場合、[EA ポータル](https://ea.azure.com/)で **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。

**購入方法:**

1. [Azure ポータル](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D)にアクセスします。
1. サブスクリプションを選択します。 **[サブスクリプション]** リストを使用して、予約容量の支払いに使用するサブスクリプションを選択します。 サブスクリプションの支払方法に対して、予約容量の初期コストが課金されます。 登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。
1. スコープを選択します。 **[スコープ]** リストを使用して、サブスクリプション スコープを選択します。
    - **単一のリソース グループのスコープ** — 選択されたリソース グループ内の一致するリソースにのみ、予約割引を適用します。
    - **単一サブスクリプション** - 選択されたサブスクリプションの一致するリソースに予約割引を適用します。
    - **共有スコープ** - 課金コンテキスト内にある有効なサブスクリプションの一致するリソースに予約割引を適用します。 マイクロソフト エンタープライズ契約のお客様の場合、課金コンテキストは登録です。
1. 購入する Azure Databricks コミット ユニット数を選択し、購入を完了します。


![Azure portal 内での Azure Databricks の購入を示す例](./media/billing-prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>スコープと所有権の変更

購入後に予約に次の種類の変更を加えることができます。

- 予約スコープの更新
- ロールベースのアクセス

Databricks コミット ユニットの事前購入を分割またはマージすることはできません。 予約の管理について詳しくは、[購入後の予約の管理](billing-manage-reserved-vm-instance.md)に関する記事をご覧ください。

## <a name="cancellations-and-exchanges"></a>キャンセルと交換

Databricks の事前購入プランでは、キャンセルと交換はサポートされていません。 すべての購入は確定です。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次の手順

- Azure の予約の詳細については、次の記事を参照してください。
  - [Azure の予約とは](billing-save-compute-costs-reservations.md)
  - [Azure Databricks の事前購入 DBCU 割引が適用される方法を理解する](billing-reservation-discount-databricks.md)
  - [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)
