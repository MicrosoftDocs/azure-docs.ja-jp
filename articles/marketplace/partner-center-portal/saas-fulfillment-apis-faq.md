---
title: SaaS Fulfillment API に関する FAQ - Microsoft コマーシャル マーケットプレース
description: Azure ユーザーが SaaS オファーにサブスクライブできるようにするための Microsoft コマーシャル マーケットプレースの統合要件をいくつか説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 4c5d8b438764fa9aa3838b2225c63d412afc519b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "88606805"
---
# <a name="common-questions-about-saas-fulfillment-apis"></a>SaaS Fulfillment API についてよく寄せられる質問

この記事では、Azure ユーザーが SaaS オファーにサブスクライブできるようにするための Microsoft コマーシャル マーケットプレースの統合要件をいくつか説明します。

## <a name="discovery-experience"></a>検出エクスペリエンス

SaaS オファーが発行されると、Azure ユーザーが Azure Marketplace で見つけられるようになります。 ユーザーは、製品の種類 (SaaS) に基づいてオファーをフィルター処理し、関心のある SaaS サービスを見つけることができます。

## <a name="purchase-experience"></a>購入エクスペリエンス

ユーザーは、特定の SaaS サービスに関心を持つと、Azure Marketplace からそれにサブスクライブできます。

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Azure ユーザーが Azure Marketplace で SaaS オファーにサブスクライブするとは、どういう意味ですか?

ユーザーが SaaS サービスに関連する使用条件とプライバシーに関する声明を表示し、Microsoft の請求書で、あなた (SaaS オファーの発行元) によって設定された請求条件に従って、その料金を支払うことに同意することを意味します。 ユーザーは、Azure の既存の支払いプロファイルを使用して、SaaS サービスの利用に対して支払いを行うことができます。

この機能は、いくつかの点で役立ちます。 ユーザーは、信頼できるソースとして Microsoft Cloud Platform を使用して 1 か所で検出とサブスクライブを行うことができます。使用しようとしているすべての ISV ソフトウェアを入念に調べる必要はありません。 また、ユーザーは、既存の支払いプロファイルを使用できます。ISV ソフトウェアごとに個別に明示的に支払いを行う必要はありません。

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>オファーにサブスクライブすると、ユーザーに対して自動的に課金が行われますか?

SaaS オファーにサブスクライブしている間、ユーザーは Microsoft プラットフォームを通じた SaaS サービスの使用料の支払いに同意します。 ただし、課金が開始されるのは、オファーが使用された場合のみです。 ユーザーは、SaaS オファーの使用を開始するためには、そのオファーにアクセスして、アカウントの作成を確認する必要があります。 その後、このユーザーの SaaS サブスクリプションに対して課金を開始するよう Microsoft に通知します。

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>ユーザーが SaaS オファーにサブスクライブすると、どのように通知されますか?

Azure ユーザーは、オファーにサブスクライブした後、Azure ですべてのオファーを検出および管理できます。 既定では、新しくサブスクライブされた SaaS オファーの状態は **"Provisioning, fulfillment pending" (プロビジョニング中、フルフィルメント保留中)** と表示されます。 この状態では、Azure ユーザーは、Azure portal で SaaS サブスクリプション管理エクスペリエンスを参照するために、 **[アカウントを構成]** するためのアクションが求められます。

ユーザーが **[アカウントの構成]** を選択すると、SaaS サービスの Web サイトにリダイレクトされます。 この URL は、発行元がオファーの発行時に構成したものです。 このページは、発行元のランディング ページと呼ばれます。 Azure ユーザーは、Azure の既存の AAD 資格情報に基づいて、SaaS ランディング ページにサインインします。

Azure ユーザーがランディング ページにリダイレクトされると、トークンがクエリ URL に追加されます。 このトークンは有効期間が短く、24 時間有効です。 その後、このトークンが存在していることを検出し、Microsoft の API を呼び出して、トークンに関連する追加のコンテキストを取得できます。

![ユーザーのサブスクリプション フロー](media/saas-metering-service-integration-flow-a.png)

SaaS オファーのライフサイクルでトランザクション シナリオを処理するための API コントラクトの詳細については、[SaaS Fulfillment API](pc-saas-fulfillment-api-v2.md) に関するページを参照してください。

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>ユーザーが Azure でサブスクライブする SaaS オファーはどのように確認しますか?

`Resolve` API への応答には、SaaS サブスクリプションに関連するオファーとプラン情報が含まれます。

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Azure ユーザーは、この Azure サブスクリプションに関連するプランをどのように変更できますか?

* Azure ユーザーは、SaaS サブスクリプションに関連するプランを、SaaS エクスペリエンスで直接変更することも、Microsoft プラットフォームを使用して変更することもできます。

* 変換は、課金サイクル中いつでも実行できます。 変換の確認を求めるメッセージが表示されます。これは、確認が完了すると有効になります。

* 前払いプラン (**月次** または **年間**) の料金は、日割りで計算されます。 変換の時点までに発生した超過分は、次の請求書で請求されます。 新しいプランに基づいて新しい超過分が生成されます。

>[!Note]
>特定の変換パスをサポートしない場合、ダウングレードをブロックできます。

次のシーケンスは、Azure の顧客が SaaS エクスペリエンスのプランを変更する場合のフローを示しています。

![ユーザーのプラン変更フロー](media/saas-metering-service-integration-flow-b.png)

次のシーケンスは、Azure の顧客が Microsoft のオンライン ストアのプランを変更する場合のフローを示しています。

![顧客のオンライン ストアのプラン変更のフロー](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Azure ユーザーは、Azure サブスクリプションに関連するプランからどのように登録を解除できますか?

Azure ユーザーは、購入した SaaS オファーから、SaaS エクスペリエンスで直接登録を解除することも、Microsoft プラットフォームを使用して登録を解除することもできます。 ユーザーが登録を解除すると、次の請求サイクルからは課金されなくなります。

次のシーケンスは、Azure ユーザーが SaaS エクスペリエンスで SaaS オファーの登録を解除する場合のフローを取り込んだものです。

![ユーザーが SaaS エクスペリエンスで登録を解除する](media/saas-metering-service-integration-flow-d.png)

次のシーケンスは、Azure ユーザーが Microsoft のオンライン ストアで登録を解除する場合のフローを示しています。

![顧客が Microsoft のオンライン ストアで登録を解除する](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>次のステップ

[Marketplace の測定サービス API](./marketplace-metering-service-apis.md)
