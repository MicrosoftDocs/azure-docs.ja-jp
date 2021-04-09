---
title: 仮想マシン (VM) イメージを Azure Marketplace のマネージド ディスク ストレージに移行予定
description: より高速で信頼性の高いストレージと、マーケットプレースの新しいフィーチャーと機能のサポートを提供するために、マーケットプレースの VM イメージをマネージド ディスク ストレージに移行します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
author: kriti-ms
ms.author: krsh
ms.openlocfilehash: e3d20b5e9206cf05d749f75982a07937a8caf078
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201092"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Azure Marketplace 上の仮想マシン (VM) イメージをマネージド ディスク ストレージに移行します

より高速で信頼性の高いストレージと、マーケットプレースの新しいフィーチャーと機能のサポートを提供するために、マーケットプレースの VM イメージをマネージド ディスク ストレージに移行します。

2020年 1 月 2 日以降、VM イメージを段階的にマネージド ディスク ストレージに移行します。 最初のフェーズでは、過去 90日間に新しいデプロイや実行中の VM がないイメージのみを移行します。 イメージを移行する前に、どのイメージをいつ移行するかを発行元にお知らせするメールを送ります。

発行元またはコンシューマーは何も行う必要はなく、ユーザーは影響を受けません。 Marketplace のオファーは引き続きご利用いただけます。また、お客様は、移行中および移行後も、これらのイメージからマネージド VM をデプロイできます。

ご質問がある場合は、[お問い合わせください](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)。

## <a name="faqs"></a>FAQ

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>VM イメージのユーザーへのサービスは停止しますか?

VM イメージのユーザーへのサービスは停止しません。 

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>プロセスが完了するまでにどのくらいの時間がかかりますか?

移行が完了するまでに最大で 24 時間かかる場合があります。

### <a name="do-i-need-to-take-any-action"></a>何らかのアクションをとる必要はありますか?

いいえ。 発行元またはコンシューマーは何も行う必要はありません。

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>マネージド ディスク ストレージへの移行後、別の方法でクラウド ポータル API を呼び出すようにシステムを更新する必要がありますか?

いいえ。 既存の API 呼び出しは引き続き機能します。

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>すべての VM イメージが同時にマネージド ディスクに移行されますか?

すべての VM イメージを同じ日に移行します。 移行が完了した時点で、お客様にお知らせします。

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>VM イメージの移行の予定日を後日に変更するよう要求できますか?

予定された日にイメージを移行することをお勧めします。 ただし、ご心配な点がある場合は、移行のスケジュール変更について弊社までお問い合わせください。

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>移行中に VM イメージへの更新を発行できますか。

移行中に VM イメージを更新することはできません。

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>VM イメージをマネージド ディスクに移動した後、公開のプロセスは変わりますか?

いいえ、公開のプロセスは変わりません。 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>発行元は、オファーをマネージド ディスクに移動できますか。

いいえ。発行元はオファーをマネージド ディスクに移動できません。 待つと、イメージは自動的に移動されます。 Microsoft 側で変更を加える前に、発行元に通知を送信します。
