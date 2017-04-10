---
title: "Azure サブスクリプションの課金アラートまたはクレジット アラートの設定 | Microsoft Docs"
description: "予想外の課金を回避するために Azure の請求に関する通知を設定する方法について説明します。"
keywords: "クレジット アラート,課金アラート"
services: 
documentationcenter: 
author: vikdesai
manager: vikdesai
editor: 
tags: billing
ms.assetid: 9b7b3eeb-cd9d-4690-86a3-51b1e2a8974f
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: vikdesai
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 32a2508997d7f50b0b2989d76d0574951d81a73f
ms.openlocfilehash: a2eb813bc96cd58762ce077d2c0710ed4bc7bde6
ms.lasthandoff: 03/02/2017

---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>Microsoft Azure サブスクリプションの課金アラートまたはクレジット アラートの設定
Azure サブスクリプションのアカウント管理者である場合は、Azure 課金アラート サービスを使用して、Azure アカウントの課金アクティビティを監視、管理するのに役立つ、カスタマイズされた課金アラートを作成できます。

このサービスはプレビュー段階なので、まずプレビュー機能のページで機能を有効にする必要があります。

## <a name="set-the-alert-threshold-and-email-recipients"></a>アラートのしきい値と電子メールの受信者を設定する
1. [プレビュー機能のページ](https://account.windowsazure.com/PreviewFeatures) にアクセスして、**課金アラート サービス**を有効にします。

1. サブスクリプションで課金サービスが有効になったという確認メールを受信したら、アカウント ポータルの [[サブスクリプション] ページ](https://account.windowsazure.com/Subscriptions) を開きます。 監視するサブスクリプションをクリックし、 **[アラート]**をクリックします。

    ![アラートが強調表示されている、Azure アカウント センターのサブスクリプション ビューのスクリーン ショット][Image1]

2. 次に、**[アラートの追加]** をクリックして最初のアラートを作成します。 サブスクリプションあたり、合計&5; つの課金アラートを設定できます。それぞれのアラートに対して、別のしきい値と最大&2; 名までの電子メール受信者を設定できます。

    ![通知を追加するアラート ビューのスクリーン ショット][Image2]

3. アラートを追加するときは、一意の名前を付け、使用金額のしきい値を選択して、アラート送信先の電子メール アドレスを選択します。 しきい値を設定するとき、**[アラート対象]** リストから **[請求合計]** または **[通貨クレジット]** のいずれかを選択できます。 請求合計の場合は、サブスクリプションの使用金額がしきい値を超えるとアラートが送信されます。 通貨クレジットの場合は、通貨クレジットが制限を下回るとアラートが送信されます。 通貨クレジットは、通常、無料試用版と Visual Studio のサブスクリプションに適用されます。

    ![受信者を設定するアラート追加ビューのスクリーン ショット][Image3]

Azure は任意の電子メール アドレスをサポートしていますが、電子メール アドレスが有効であるかは確認されないため、入力ミスがないようダブルチェックしてください。

## <a name="check-on-your-alerts"></a>アラートを確認する
アラートを設定した後、アカウント センターで一覧表示され、設定可能な残数も表示されます。 それぞれのアラートに対し、送信された日付と時刻、アラートが請求合計または通貨クレジットであるか、設定した制限が表示されます。 日付と時刻形式は 24 時間協定世界時 (UTC) で、日付は yyyy-mm-dd の形式です。 アラートを編集するには、リストのアラートのプラス記号をクリックします。アラートを削除するには、ごみ箱アイコンをクリックします。

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>エンタープライズ契約 (EA) のお客様に対する課金アラート
EA のお客様は、加入契約の下にある各部署ごとに、使用金額クォータを設定してアラートを取得できます。 ご利用を開始するには EA ポータルの「[	部署課金クォータ](https://ea.azure.com/helpdocs/departmentSpendingQuotas)」をご覧ください。

## <a name="learn-more-about-azure-cost-management"></a>Azure コスト管理の詳細情報
- [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)や[総保有コスト計算ツール](https://aka.ms/azure-tco-calculator)を使用してコストを見積もり、サービスを追加するときにもコストを見積もる
- [Azure Portal で使用状況とコストを定期的に確認する](billing-getting-started.md#costs)
- [Azure Advisor のコストに関する推奨事項](../advisor/advisor-cost-recommendations.md)を有効にする

詳細については、「[Azure コスト管理のガイダンス](billing-getting-started.md)」を参照してください。

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 

