---
title: Azure Lab Services のクラスルーム ラボ - FAQ | Microsoft Docs
description: Azure Lab Services のクラスルーム ラボについてよく寄せられる質問の回答を見つけます。
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 411037dd97350d877aff4e2d094c3408f168f9fd
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648576"
---
# <a name="classroom-labs-in-azure-lab-services---frequently-asked-questions-faq"></a>Azure Lab Services のクラスルーム ラボ - よく寄せられる質問 (FAQ)
Azure Lab Services のクラスルーム ラボについて特に多く寄せられる質問にお答えします。 

## <a name="quotas"></a>Quotas (クォータ)

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>クォータは、ユーザーごと、1 週間ごと、またはラボの全期間のいずれに対して指定されますか。 
ラボに設定するクォータは、ラボの全期間について、各学生を対象としています。 また、[スケジュールされている VM の実行時間](how-to-create-schedules.md)は、ユーザーに割り当てられるクォータにカウントされません。 クォータは、学生が VM で消費することをスケジュールされている時間以外の時間です。  クォータの詳細については、「[ユーザーのクォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)」を参照してください。

## <a name="schedules"></a>スケジュール

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>スケジュールを設定すると、ラボ内のすべての VM が自動的に開始されますか。 
いいえ。 すべての VM ではありません。 スケジュールに基づいてユーザーに割り当てられている VM のみです。 ユーザーに割り当てられていない VM は自動的に開始されません。 これは仕様です。 

## <a name="lab-accounts"></a>ラボ アカウント

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>アドレス範囲が利用できないためにラボを作成できないのはなぜですか。 
クラスルーム ラボでは、Azure portal でラボ アカウントを作成するときに指定する IP アドレス範囲内にラボ VM を作成できます。 アドレス範囲が指定されている場合、各ラボは、ラボ VM 用に 512 個の IP アドレスが割り当てられた後に作成されます。 ラボ アカウントのアドレス範囲は、ラボ アカウントで作成するすべてのラボを格納するのに十分な大きさである必要があります。 

たとえば、/19 - 10.0.0.0/19 のブロックがある場合、このアドレス範囲は、8192 個の IP アドレスと 16 個のラボ (8192/512 = 16 個のラボ) に対応します。 この場合、ラボの作成は 17 個目のラボの作成で失敗します。

## <a name="blog-post"></a>ブログ記事
[Azure Lab Services ブログ](https://azure.microsoft.com/blog/tag/azure-lab-services/)に登録してください。

## <a name="update-notifications"></a>更新通知
Lab Services の新機能について最新情報を入手するには、[Lab Services の更新情報](https://azure.microsoft.com/updates/?product=lab-services)に登録してください。

## <a name="general"></a>全般
### <a name="what-if-my-question-isnt-answered-here"></a>ここに質問の答えがない場合はどうすればいいですか。
ご自分の質問がここに表示されていない場合はご連絡ください。答えを見つけるお手伝いをします。

- この FAQ の末尾で質問を投稿してください。 
- さらに多くの人々と交流するには、[Azure Lab Services - Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-lab-services)に質問を投稿してください。 
- 機能を要求する場合は、要求とアイデアを [Azure Lab Services のユーザーの声](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)に送信してください。

