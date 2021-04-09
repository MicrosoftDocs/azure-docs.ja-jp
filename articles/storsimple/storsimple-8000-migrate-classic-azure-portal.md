---
title: StorSimple デバイス マネージャー ストレージ アカウント、サブスクリプションを移行する
description: StorSimple デバイス マネージャー サービスのサブスクリプション、ストレージ アカウントを移行する方法を説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 523788dc727fc5fa32b2f57250fb738352ff6d31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96491522"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>StorSimple デバイス マネージャー サービスに関連付けられているサブスクリプションとストレージ アカウントを移行する

StorSimple サービスを新しい登録や新しいサブスクリプションに移行することが必要な場合があります。 これらの移行シナリオは、アカウントの変更またはデータセンターの変更です。 次の表を使用して、移行の詳しい手順を含め、これらのどのシナリオがサポートされるかを把握してください。

## <a name="account-changes"></a>アカウントの変更

| 移行対象| サポートされています| ダウンタイム| Azure サポート プロセス| アプローチ|
|-----|-----|-----|-----|-----|
| サブスクリプション全体 (StorSimple サービスとストレージ アカウントを含む) を別の登録へ | はい       | いいえ       | **登録の転送**<br>用途:<li>新しい契約で新しい Azure コミットメントを購入する場合。</li><li>すべてのアカウントとサブスクリプションを以前の登録から新しい登録に移行します。 これには、以前のサブスクリプションのすべての Azure サービスが含まれます。</li> | **手順 1: Azure Enterprise 操作のサポート チケットを開く。**<li>[https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) に移動します。</li><li> **[Enrollment Administration]\(登録管理\)** に移動し、**[Transfer from one enrollment to a new enrollment]\(登録間で転送する\)** を選択します。<br>**手順 2: 要求された情報を提供する**<br>包含:<li>転送元の登録番号</li><li> 転送先の登録番号</li><li>転送の発効日|
| StorSimple サービスを既存のアカウントから新しい登録へ    | はい       | いいえ       | **アカウントの転送**<br>次のコマンドを使用します。<li>登録を完全には転送しない場合。</li><li>特定のアカウントのみを新しい登録に移動します。</li>| **手順 1: Azure Enterprise 操作のサポート チケットを開く。**<li>[https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) に移動します。</li><li>**[Enrollment Administration]\(登録管理\)** に移動し、**[Transfer an EA Account to a new enrollment]\(EA アカウントを新規登録に転送する\)** を選択します。<br>**手順 2: 要求された情報を提供する**<br>包含:<li>転送元の登録番号</li><li> 転送先の登録番号</li><li>転送の発効日|
| StorSimple サービスをあるサブスクリプションから別のサブスクリプションへ      | いいえ        |    はい         | なし。手動プロセス|<li>StorSimple デバイスからデータを移行します。</li><li>デバイスを工場出荷時の状態にリセットします。これにより、デバイス上のローカル データがすべて削除されます。</li><li>StorSimple デバイス マネージャー サービスに新しいサブスクリプションでデバイスを登録します。</li><li>データをデバイスに移行します。|
|Azure サブスクリプションの所有権を別のディレクトリに移すことはできますか。 | はい       | いいえ       | 既存のサブスクリプションを Azure AD ディレクトリに関連付ける | 「[既存のサブスクリプションを Azure AD ディレクトリに関連付けるには](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。 すべてが正しく表示されるまで、最大で 10 分かかる場合があります。|
| StorSimple デバイスを、ある StorSimple デバイス マネージャー サービスから別のリージョンのサービスへ      | いいえ        | はい            | なし。手動プロセス |上記と同じです。|
| ストレージ アカウントから新しいサブスクリプションまたはリソース グループへ     | はい        | いいえ             |ストレージ アカウントを別のサブスクリプションまたはリソース グループへ移動する |移動後に、ストレージ アカウント アクセス キーが更新された場合、ユーザーは、StorSimple デバイス マネージャー サービスで移行したストレージ アカウントのアクセス キーを手動で構成する必要があります。|
| クラシック ストレージ アカウントから Azure Resource Manager ストレージ アカウントへ      | はい        | いいえ             |クラシックから Azure Resource Manager に移行する |<li>ストレージ アカウントをクラシックから Azure Resource Manager に移行する詳しい手順については、[クラシック ストレージ アカウントの移行](../virtual-machines/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account)に関するページをご覧ください。</li><li> 移行後にストレージ アカウント アクセス キーが更新された場合、ユーザーは、StorSimple デバイス マネージャー サービスで移行したストレージ アカウントのアクセス キーを同期する必要があります。 これにより、StorSimple デバイスが引き続き正常に機能し、Azure へのプライマリ/バックアップ データを階層化できます。 アクセス キーの同期化の詳しい手順については、[ローテーション ワークフロー](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)に関するページをご覧ください。</li><li> StorSimple Cloud Appliance の場合、クラシック ストレージ アカウントが移行されても、基になる仮想マシンが依然としてクラシックであれば、アプライアンスは正常に機能します。 クラウド アプライアンスの基になる仮想マシンを移行する場合、非アクティブ化と削除の機能は動作しません。</li><li> Azure Portal で新しい StorSimple Cloud Appliance を作成し、その後、以前のクラウド アプライアンスからフェールオーバーする必要があります。 クラシック ストレージ アカウントを使用して新しい Azure Portal で StorSimple Cloud Appliance を作成することはできません。Azure Resource Manager ストレージ アカウントが必要です。 詳細については、「[StorSimple Cloud Appliance のデプロイと管理](storsimple-8000-cloud-appliance-u2.md)」を参照してください。</li>|

## <a name="datacenter-changes"></a>データセンターの変更

| 移行対象| サポートされています|ダウンタイム| Azure サポート プロセス| アプローチ|
|-----|-----|-----|-----|-----|
| StorSimple サービスを、ある Azure データセンターから別の Azure データセンターへ | いいえ | はい |なし。手動プロセス  |<li>StorSimple デバイスからデータを移行します。</li><li>デバイスを工場出荷時の状態にリセットします。これにより、デバイス上のローカル データがすべて削除されます。</li><li>新しい StorSimple デバイス マネージャー サービスに新しいサブスクリプションでデバイスを登録します。</li><li>データをデバイスに移行します。|
| ストレージ アカウントを、ある Azure データセンターから別の Azure データセンターへ | いいえ |はい  |なし。手動プロセス  | 上記と同じです。|

## <a name="next-steps"></a>次のステップ

* [StorSimple デバイス マネージャー サービスのデプロイ](storsimple-8000-manage-service.md)
* [StorSimple 8000 シリーズ デバイスを Azure Portal でデプロイする](storsimple-8000-deployment-walkthrough-u2.md)