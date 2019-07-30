---
title: Seller Insights に関する FAQ
description: Cloud パートナー ポータルの Seller Insights 機能に関してよく寄せられる質問。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 2719b6b47225576f2eadeb5e5b40b3aa7b39444d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943066"
---
<a name="seller-insights-faq"></a>Seller Insights に関する FAQ
===================

この記事では、Seller Insights で一般的なユーザーの手順と Seller Insights に関する質問を紹介します。


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>ダウンロードしたトランザクション ファイル内の値の定義を確認する
------------------------------------------------------------------

トランザクション ファイル内のメトリック値の定義は、「[Seller Insights Definitions (Seller Insights の定義)](./si-insights-definitions-v4.md)」で確認できます。


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>支払いがあったトランザクションの顧客の詳細を確認する
-------------------------------------------------------------

支払いモジュールからトランザクションをダウンロードした後、 **[Payout Status]\(支払いの状態\)** というラベルの付いた列を見つけ、[Paid Out]\(支払い済み\) という値のみを表示するフィルターを適用します。 顧客の詳細を含む次の列が表示されます。 **[Company Name]\(会社名\)** 、 **[Customer Email]\(顧客のメール\)** 、 **[Customer Country]\(顧客の国\)** 、 **[Customer State]\(顧客の州\)** 、および **[Customer Postal Code]\(顧客の郵便番号\)** 。


<a name="calculate-my-open-accounts-receivable"></a>未決済の売掛金を計算する
-------------------------------------

支払いモジュールからトランザクションをダウンロードした後、 **[Payout Status]\(支払いの状態\)** というラベルの付いた列を見つけ、[Upcoming Payout]\(支払い予定\) と [Not Ready for Payout]\(支払い準備が未完了\) という値のみを表示するフィルターを適用します。 次に、 **[Payout Amount (PC)]\(支払い額 (PC)\)** というラベルの付いた列を合計します。


<a name="calculate-revenue-by-customer-usage-period"></a>顧客の使用期間別に収益を計算する
------------------------------------------

支払いモジュールからトランザクションをダウンロードした後、 **[Transaction Status]\(トランザクションの状態\)** というラベルの付いた列を見つけ、[支払い済み] という値を表示するフィルターを適用します。   一覧表示された各トランザクションについて、 **[Payout Amount (PC)]\(支払い額 (PC)\)**   というラベルの付いた列に、支払われた金額が表示されます。  トランザクションに関連付けられている使用期間を見積もるには、 **[課金日]** 列を使用します。これは、トランザクションが適用される期間の最終使用日の近似値です。


<a name="calculate-your-bad-debt"></a>貸し倒れ金を計算する
---------------------

支払いモジュールからトランザクションをダウンロードした後、 **[Final Collection Status]\(集金の最終状態\)** というラベルの付いた列を見つけ、[Write Off]\(損金処理\) という値のみを表示するフィルターを適用します。 次に、 **[Payout Amount (PC)]\(支払い額 (PC)\)** というラベルの付いた列を合計します。


<a name="view-payout-or-customer-contact-information"></a>支払いまたは顧客の連絡先情報を表示する
-------------------------------------------

"所有者" ロールを持つユーザーとしてサインインします ("共同作成者" ロールではありません)。 支払いおよび顧客の情報を参照できるのは所有者ロールのみです。 ユーザー ロールの詳細については、[ユーザーの管理](./cloud-partner-portal-manage-users.md)に関する記事を参照してください。


<a name="calculate-my-advance-payouts"></a>前払いの支払いを計算する
----------------------------

支払いモジュールからトランザクションをダウンロードした後、 **[トランザクションの種類]** というラベルの付いた列を見つけ、[料金] という値のみを表示するフィルターを適用します。 次に、 **[Final Collection Status]\(集金の最終状態\)** というラベルの付いた列を見つけ、[In Progress]\(進行中\) という値のみを表示するフィルターを適用します。 最後に、 **[Payout Amount (PC)]\(支払い額 (PC)\)** 列を合計して、顧客から集金する前に支払われたすべての前払い金を計算します。


<a name="calculate-customer-refunds"></a>顧客への払い戻しを計算する
--------------------------

支払いモジュールからトランザクションをダウンロードした後、 **[Final Collection Status]\(集金の最終状態\)** というラベルの付いた列を見つけ、[払戻] という値のみを表示するフィルターを適用します。 **[Charge Amount (PC)]\(請求金額 (PC)\)** 列を合計して、顧客に対して処理されたすべての払い戻し額を計算します。


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Microsoft チャネル パートナーに関連するトランザクションを識別する
----------------------------------------------------------------

**[Azure License Type]\(Azure ライセンスの種類\)** 列に [Enterprise through Reseller]\(リセラー経由のエンタープライズ\) と [Cloud Solution Provider]\(クラウド ソリューション プロバイダー\) という値を表示するフィルターを適用した結果表示されるすべてのトランザクションが Microsoft チャネル パートナーに関連します。 パートナーの詳細については、支払いモジュールのダウンロードと顧客モジュールのダウンロード内で**リセラーの名前**と**メール**を確認できます。


<a name="identify-trial-usage-and-trial-conversions"></a>評価版の使用状況と評価版からの切り替えを識別する
------------------------------------------

注文、使用、および支払いモジュールのダウンロードに**評価版の終了日**が含まれるようになりました。この日付は、その特定の注文の試用期間がいつ終了したかを把握するのに役立ちます (該当する場合)。 評価版の使用状況と注文を確認するには、ダウンロード内で **[SKU Billing Type]\(SKU の課金の種類\)** 列を見つけ、[Trial]\(評価版\) という値のみを表示するフィルターを適用します。 評価版からの切り替えを確認するには、ダウンロード内で **[評価版の終了日]** 列を見つけ、**評価版の終了日**が今日の日付を過ぎていて、かつ **[Cancel Date]\(取り消し日\)** 列が空であるか**評価版の終了日**より後の日付の注文のみを表示するフィルターを適用します。


<a name="when-is-my-monthly-payout-calculated"></a>月単位の支払い金額はいつ計算されますか
------------------------------------

支払いは、前月の最後の暦日までに支払い準備が整ったすべての金額について毎月 15 日までに実行されます。 Microsoft は、月の 3 日目に前月の支払い金額を計算し、ダウンロード内の該当するすべての課金トランザクションについて、 **[Payout Status]\(支払いの状態\)** 列を [Upcoming Payout]\(支払い予定\) に更新します。 これらのトランザクションは、銀行口座に支払い要求が送信されるまでその状態に保持されます。銀行口座に支払い要求が送信された時点で、 **[Payout Status]\(支払いの状態\)** が [Paid Out]\(支払い済み\) に更新され、[Payout Date]\(支払い日\) が更新されて銀行に支払い要求を提出した日付が表示されます。


<a name="calculate-customer-acquisition-and-loss"></a>顧客の獲得と損失を計算する
---------------------------------------

顧客が最初に貴社のプランのいずれかを購入した日付を確認するには、顧客のダウンロード内で **[Date Acquired]\(獲得日\)** 列を探します。 同様に、貴社によって公開されたいずれかのプランを顧客が保有しなくなった日付を確認するには、顧客ダウンロード内で **[Date Lost]\(損失日\)** 列を探します。


<a name="finding-more-help"></a>さらにサポートが必要な場合
-----------------

- [Seller Insights の定義](./si-insights-definitions-v4.md) - メトリックとデータの定義を確認できます

- [Seller Insights の概要](./si-getting-started.md) - Seller Insights の機能の紹介。

