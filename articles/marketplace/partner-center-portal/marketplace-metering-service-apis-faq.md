---
title: 測定サービス API に関する FAQ - Microsoft コマーシャル マーケットプレース
description: Microsoft AppSource および Azure Marketplace の SaaS オファー用の測定サービス API についてよく寄せられる質問。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/01/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: ddec23f695396b8322d51da62158a97456096ae8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87320002"
---
# <a name="marketplace-metered-billing-apis---faq"></a>マーケットプレースの従量制課金 API - FAQ

顧客が、従量制課金が含まれる SaaS サービスまたはマネージド アプリ プランの Azure アプリケーションをサブスクライブしたら、パートナーは使用されている請求ディメンションごとに使用量を追跡します。  顧客が選択した条件に対して設定されている含まれる数量を消費量が超えた場合、サービスでは Microsoft に対する使用状況イベントが生成されます。

## <a name="for-both-saas-offers-and-managed-apps"></a>SaaS オファーとマネージド アプリの両方

### <a name="how-often-is-it-expected-to-emit-usage"></a>使用状況はどのくらいの頻度で生成されますか?

最善の場合で、過去 1 時間に使用量がある場合にのみ、1 時間ごとに過去 1 時間の使用状況が生成されます。

### <a name="is-there-a-maximal-period-between-one-emission-and-the-next-one"></a>ある出力と次の出力の間に最大の期間がありますか?

そのような制限はありません。 発生時にのみ使用状況を生成します。 たとえば、サブスクリプションの有効期間ごとに 1 単位の使用状況だけを送信する必要がある場合は、これを行うことができます。

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>イベントが発生してから使用状況イベントが Microsoft に送信されるまでの最大遅延時間はどれくらいですか?

最善の場合で、使用状況イベントは、過去 1 時間に発生したイベントに対して 1 時間ごとに生成されます。 ただし、遅延が想定されます。 許容される最大遅延時間は 24 時間で、これを過ぎると使用状況イベントは受け入れられません。 時間単位の使用状況を収集し、1 時間の終わりに 1 つのイベントとしてを生成するのがベスト プラクティスです。

たとえば、使用状況イベントがある日の午後 1 時に発生した場合、翌日の午後 1 時までは、このイベントに関連付けられた使用状況イベントが送信される可能性があります。  使用状況を送信するシステムがダウンした場合、復旧した後、忠実性を損なうことなく使用状況が発生した時間間隔で使用状況イベントが送信されます。

実際の使用から 24 時間が経過した場合でも、後の使用状況イベントで使用されたユニットを生成できます。  ただし、この方法を使用すると、エンド カスタマーに対する課金イベント レポートの信頼性が低下する可能性があります。  日、週、月に 1 回測定出力を送信するのは避けることをお勧めします。  そのようにすると、顧客が実際の使用状況を把握したり、使用状況イベントに関して発生する可能性のある問題や質問を解決したりするのが困難になります。

1 時間ごとに使用状況を送信するもう 1 つの理由は、発行元が毎日、毎週、毎月の出力イベントを送信する前に、ユーザーがサブスクリプションをキャンセルする状況を回避するためです。

### <a name="what-happens-when-you-send-more-than-one-usage-event-in-the-same-hour"></a>同じ時間に複数の使用状況イベントを送信するとどうなりますか?

1 時間の間隔で受け入れられる使用状況イベントは 1 つだけです。 時間間隔は 0 分から始まり、59 分で終わります。  同じ時間で複数の使用状況イベントが生成された場合、後続の使用状況イベントは重複として削除されます。

### <a name="what-happens-when-the-customer-cancels-the-purchase-within-the-time-allowed-by-the-cancellation-policy"></a>キャンセル ポリシーで許可されている期間内に顧客が購入をキャンセルした場合、どうなりますか?

定額の料金は課金されませんが、超過分の使用量は課金されます。

### <a name="can-custom-meter-plans-be-used-for-one-time-payments"></a>1 回限りの支払いにカスタム測定プランを使用できますか?

はい。カスタム ディメンションを 1 回の支払いの 1 単位として定義し、顧客ごとに 1 回だけ生成することができます。

### <a name="can-custom-meter-plans-be-used-to-tiered-pricing-model"></a>カスタム測定プランは、階層価格モデルに使用できますか?

はい、1 つの価格レベルを表す各カスタム ディメンションで実装できます。

たとえば、Contoso の料金は、最初の 1000 メールについては 1 メールあたり $0.5、1000 メールから 5000 メールまでは 1 メールあたり $0.4、5000 メールを超えると 1 メールあたり $0.2 になります。 この場合、3 つのメール価格レベルに対応する 3 つのカスタム ディメンションを定義できます。 メールの数が 1000 未満である限り、最初のディメンションの単位を生成し、メールの数が 1000 から 5000 の場合は 2 番目のディメンションの単位、5000 メールを超えたら 3 番目のディメンションの単位を生成します。

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Marketplace の測定サービスで障害が発生した場合はどうなりますか?

ISV がカスタム測定を送信してエラーを受信し、Microsoft 側での問題によって発生した可能性がある場合は (通常、以前はエラーなしで類似イベントが受け入れられた場合)、ISV は少し待ってから、出力を再試行する必要があります。

エラーが引き続き発生する場合は、その後 1 時間にわたってそのカスタム測定を再送信します (数量を累積します)。 エラー以外の応答を受信するまでこのプロセスを継続します。

## <a name="for-saas-offers-only"></a>SaaS オファーの場合のみ

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>既に登録が解除されている SaaS サブスクリプションの使用状況を生成するとどうなりますか?

SaaS サブスクリプションが削除された後で Marketplace プラットフォームに対して生成された使用状況イベントは受け入れられません。

使用状況は、サブスクライブ済み状態のサブスクリプションに対してのみ出力できます (`PendingFulfillmentStart`、`Suspended`、または `Unsubscribed` の状態のサブスクリプションでは生成されません)。

唯一の例外は、SaaS サブスクリプションが取り消される前の時間の使用状況を報告する場合です。

たとえば、顧客が今日の午後 3 時に SaaS サブスクリプションを取り消しました。 現在は午後 5 時ですが、発行元は、この SaaS サブスクリプションに対する、昨日の午後 6 時から今日の午後 3 時までの間の使用状況を生成できます。

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>アクティブなサブスクリプションと登録解除されたサブスクリプションを含む、すべての SaaS サブスクリプションの一覧を取得できますか?

はい。[GET Subscriptions List API](pc-saas-fulfillment-api-v2.md#subscription-apis) を呼び出すと、すべての SaaS サブスクリプションの一覧が含まれます。 各 SaaS サブスクリプションに対する応答の状態フィールドで、サブスクリプションがアクティブであるか、または登録解除されているかが、示されています。

### <a name="are-the-start-and-end-dates-of-saas-subscription-term-and-overage-usage-emission-connected"></a>SaaS サブスクリプション期間の開始日および終了日と、超過分使用状況の出力は接続されていますか?

超過分イベントは、*サブスクライブ済み* 状態の既存の SaaS サブスクリプションに対して、いつでも生成できます。 課金プランで定義されているポリシーに基づいて使用状況イベントを生成するのは、発行元の責任です。 超過分は、SaaS サブスクリプション期間で定義されている日付に基づいて計算する必要があります。 

たとえば、ある発行元が定義している SaaS プランでは、毎月 1000 メールまでは $100 の定額であり、1000 メールを超えるとカスタム ディメンションによってすべてのメールに $1 が請求されます。

顧客が 1 月 6 日にサブスクリプションを購入してアクティブ化した場合、定額に含まれる 1000 メールはこの日からカウントされます。 このため、2 月 5 日 (サブスクリプションの最初の月の終わり) までに 900 メールのみが送信された場合、顧客は、このサブスクリプションの最初の月に対しては定額料金のみを支払い、発行元は 1 月 6 日から 2 月 5 日の間については超過分の使用状況イベントを生成しません。 2 月 6 日に、サブスクリプションは自動的に更新され、カウントが再び開始されます。 顧客が 2 月 15 日に 1000 メールに達した場合、3 月 5 日までに送信される残りのメールは、発行元によって生成される超過分使用量イベントに基づいて、超過分として請求されます (メールあたり $1)。

## <a name="next-steps"></a>次のステップ

- 詳細については、[Marketplace の測定サービス API](./marketplace-metering-service-apis.md) に関する記事をご覧ください。
