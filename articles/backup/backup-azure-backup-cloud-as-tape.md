---
title: Azure Backup を使用してテープのインフラストラクチャを置換する
description: Azure でのデータのバックアップと復元を可能にするテープのようなセマンティクスを Azure Backup が提供する方法について説明します。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/30/2017
ms.author: dacurwin
ms.openlocfilehash: 3be3a2e3355793a8d0b4fcaf0e7f62668f78f0c8
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954877"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>テープから Azure クラウドに長期ストレージを移動する
Azure Backup と System Center Data Protection Manager のユーザーは以下を実行できます。

* 組織のニーズに最適なスケジュールでのデータのバックアップ。
* 長期間にわたるバックアップ データの保持
* 長期間保有する必要があるものを (テープの代わりに) Azure で保持。

この記事では、顧客がバックアップと保有ポリシーを有効にする方法について説明します。 その長期間保有のニーズに対処するためにこれまでテープ使用してきた顧客にとっては、この機能を使用するための強力かつ有効な代替方法を活用できるようになりました。 Azure Backup の最新のリリース ( [ここ](https://aka.ms/azurebackup_agent)からアクセスできます) では、この機能が有効になっています。 System Center DPM の顧客は、Azure Backup サービスで DPM を使用する前に、少なくとも DPM 2012 R2 UR5 に更新する必要があります。

## <a name="what-is-the-backup-schedule"></a>Backup スケジュールとは
Backup スケジュールは、バックアップ操作の頻度を示します。 たとえば、次の画面の設定は、毎日午後 6 時と、午前 0 時にバックアップが実行されることを示しています。

![毎日のスケジュール](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

顧客は週ごとのバックアップをスケジュールすることもできます。 たとえば、以次の画面の設定は、隔週の日曜日と水曜日の午前 9 時 30 分と午前 1 時にバックアップが実行されることを示しています。

![週単位のスケジュール](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>保有ポリシーとは
保有ポリシーは、バックアップを格納する必要がある期間を指定します。 顧客は、すべてのバックアップ ポイントの「フラット ポリシー」を指定するのでなく、バックアップが作成された時期に基づいて別の保有ポリシーを指定できます。 たとえば、毎日実行されるバックアップ ポイントは、運用上の復旧ポイントとして機能し、90 日間保持されます。 各四半期の最後に実行されるバックアップ ポイントは、監査の目的で長期間保持されます。

![保有ポリシー](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

このポリシーで指定された「保有ポイント」の合計数は、90 (1 日のポイント) + 40 (10 年間の四半期ごとに 1 つ) = 130 です。

## <a name="example--putting-both-together"></a>例 - 両方を一緒にまとめる
![サンプル画面](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **日単位の保持ポリシー**:毎日バックアップが作成され、7 日間保持されます。
2. **週単位の保持ポリシー**:毎日午前 0 時と土曜日の午後 6 時にバックアップが作成され、4 週間保持されます
3. **月単位の保持ポリシー**:毎月最後の土曜日の午前 0 時と午後 6 時にバックアップが作成され、12 か月保持されます
4. **年単位の保持ポリシー**:毎年 3 月の最後の土曜日の午前 0 時にバックアップが作成され、10 年間保持されます

前の図にある "保有ポイント" の合計数 (顧客がデータを復元できるポイント) は次のように計算されます。

* 7 日間で 1 日あたり 2 ポイント = 14 回復ポイント
* 4 週間で 1 週あたり 2 ポイント = 8 回復ポイント
* 12 か月間で 1 か月あたり 2 ポイント = 24 回復ポイント
* 10 年間で 1 年あたり 1 ポイント = 10 回復ポイント

回復ポイントの合計数は 56 です。

> [!NOTE]
> Azure Backup を使用して、保護されたインスタンスごとに最大 9,999 個の復旧ポイントを作成できます。 保護されたインスタンスとは、Azure へのバックアップを行うコンピューター、サーバー (物理または仮想)、またはワークロードです。
>

## <a name="advanced-configuration"></a>詳細な構成
前の画面の **[変更]** をクリックすることによって、顧客はより柔軟に保持スケジュールを指定することができます。

![[変更]](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>次の手順
Azure Backup の詳細については、以下をご覧ください。

* [Azure Backup の概要](backup-introduction-to-azure-backup.md)
* [Azure Backup を試す](backup-try-azure-backup-in-10-mins.md)
