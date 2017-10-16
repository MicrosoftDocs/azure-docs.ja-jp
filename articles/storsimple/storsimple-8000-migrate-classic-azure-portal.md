---
title: "StorSimple デバイス マネージャー サービスのストレージ アカウント、サブスクリプションの移行 | Microsoft Docs"
description: "StorSimple デバイス マネージャー service8000 のサブスクリプション、ストレージ アカウントを移行する方法を説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: a64cf0ba2a4646a03c864193fb93de4f54f38039
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>StorSimple デバイス マネージャー サービスに関連付けられているサブスクリプションとストレージ アカウントを移行する

StorSimple サービスを新しい登録や新しいサブスクリプションに移行することが必要な場合があります。 これらの移行シナリオは、アカウントの変更またはデータセンターの変更です。 次の表を使用して、移行の詳しい手順を含め、これらのどのシナリオがサポートされるかを把握してください。

## <a name="account-changes"></a>アカウントの変更

| 移行対象| サポートされています| ダウンタイム| Azure サポート プロセス| アプローチ|
|-----|-----|-----|-----|-----|
| サブスクリプション全体 (StorSimple サービスとストレージ アカウントを含む) を別の登録へ | あり       | いいえ       | **登録の転送**<br>用途:<li>新しい契約で新しい Azure コミットメントを購入する場合。</li><li>すべてのアカウントとサブスクリプションを以前の登録から新しい登録に移行します。 これには、以前のサブスクリプションのすべての Azure サービスが含まれます。</li> | **手順 1: Azure Enterprise 操作のサポート チケットを開く。**<li>[http://aka.ms/AzureEnt](http://aka.ms/AzureEnt) に移動します。</li><li> **[Enrollment Administration]\(登録管理\)** に移動し、**[Transfer from one enrollment to a new enrollment]\(登録間で転送する\)** を選択します。<br>**手順 2: 要求された情報を提供する**<br>以下が含まれます。<li>転送元の登録番号</li><li> 転送先の登録番号</li><li>転送の発効日|
| StorSimple サービスを既存のアカウントから新しい登録へ    | あり       | いいえ       | **アカウントの転送**<br>次のコマンドを使用します。<li>登録を完全には転送しない場合。</li><li>特定のアカウントのみを新しい登録に移動します。</li>| **手順 1: Azure Enterprise 操作のサポート チケットを開く。**<li>[http://aka.ms/AzureEnt](http://aka.ms/AzureEnt) に移動します。</li><li>**[Enrollment Administration]\(登録管理\)** に移動し、**[Transfer an EA Account to a new enrollment]\(EA アカウントを新規登録に転送する\)** を選択します。<br>**手順 2: 要求された情報を提供する**<br>以下が含まれます。<li>転送元の登録番号</li><li> 転送先の登録番号</li><li>転送の発効日|
| StorSimple サービスをあるサブスクリプションから別のサブスクリプションへ      | いいえ        |    あり         | なし。手動プロセス|<li>StorSimple デバイスからデータを移行します。</li><li>デバイスを工場出荷時の状態にリセットします。これにより、デバイス上のローカル データがすべて削除されます。</li><li>StorSimple デバイス マネージャー サービスに新しいサブスクリプションでデバイスを登録します。</li><li>データをデバイスに移行します。|
| StorSimple デバイスを、ある StorSimple デバイス マネージャー サービスから別のリージョンのサービスへ      | いいえ        | あり            | なし。手動プロセス |上記と同じです。|

## <a name="datacenter-changes"></a>データセンターの変更

| 移行対象| サポートされています|ダウンタイム| Azure サポート プロセス| アプローチ|
|-----|-----|-----|-----|-----|-----|
| StorSimple サービスを、ある Azure データセンターから別の Azure データセンターへ | いいえ | あり |なし。手動プロセス  |<li>StorSimple デバイスからデータを移行します。</li><li>デバイスを工場出荷時の状態にリセットします。これにより、デバイス上のローカル データがすべて削除されます。</li><li>新しい StorSimple デバイス マネージャー サービスに新しいサブスクリプションでデバイスを登録します。</li><li>データをデバイスに移行します。|
| ストレージ アカウントを、ある Azure データセンターから別の Azure データセンターへ | いいえ |あり  |なし。手動プロセス  | 上記と同じです。|

## <a name="next-steps"></a>次のステップ

* [StorSimple デバイス マネージャー サービスのデプロイ](storsimple-8000-manage-service.md)
* [StorSimple 8000 シリーズ デバイスを Azure Portal でデプロイする](storsimple-8000-deployment-walkthrough-u2.md)
