---
title: StorSimple 8000 シリーズ バックアップ ポリシーの管理 | Microsoft Docs
description: StorSimple デバイス マネージャー サービスを使用して、StorSimple 8000 シリーズ デバイスで手動バックアップ、バックアップのスケジュールを作成して、バックアップのリテンション期間を管理する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 9ec2bbef88ed185c3927676ad2596269783d1850
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85515635"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Azure ポータルで StorSimple デバイス マネージャー サービスを使用してバックアップ ポリシーを管理する


## <a name="overview"></a>概要

このチュートリアルでは、StorSimple デバイス マネージャー サービスの **[バックアップ ポリシー]** ブレードを使用して、StorSimple ボリュームのバックアップ プロセスとバックアップのリテンション期間を制御する方法について説明します。 また、手動バックアップを完了する方法についても説明します。

ボリュームをバックアップする場合は、ローカル スナップショットとクラウド スナップショットのどちらを作成するかを選択できます。 ローカル固定ボリュームをバックアップする場合は、クラウド スナップショットを指定することをお勧めします。 離反要因が多いデータ セットに加えて、ローカル固定ボリュームの多数のローカル スナップショットを作成すると、ローカル領域が急激に減少する状態が発生します。 ローカル スナップショットの作成を選択した場合は、最新の状態をバックアップするための毎日のスナップショットの作成回数を少なくし、それらを 1 日間保持した後で削除することをお勧めします。

ローカル固定ボリュームのクラウド スナップショットを作成すると、変更されたデータのみがクラウドにコピーされ、クラウドで重複除去と圧縮が行われます。

## <a name="the-backup-policy-blade"></a>[バックアップ ポリシー] ブレード

StorSimple デバイスの **[バックアップ ポリシー]** ブレードでは、バックアップ ポリシーを管理して、ローカル スナップショットとクラウド スナップショットのスケジュールを設定できます。 バックアップ ポリシーは、ボリュームのコレクションに対するバックアップのスケジュールとバックアップのリテンション期間の構成に使用します。 バックアップ ポリシーを使用すると、複数のボリュームのスナップショットを同時に取得できます。 これは、バックアップ ポリシーによって作成されたバックアップがクラッシュ整合コピーになることを意味します。

また [バックアップ ポリシー] 表形式の一覧では、次の 1 つ以上のフィールドで既存のバックアップ ポリシーをフィルター処理することもできます。

* **ポリシー名** – ポリシーに関連付けられた名前です。 次の種類のポリシーがあります。

  * スケジュールされたポリシー。ユーザーが明示的に作成します。
  * インポートされたポリシー。作成元は StorSimple Snapshot Manager です。 このポリシーには、インポート元の StorSimple Snapshot Manager ホストについて説明するタグが付いています。

  > [!NOTE]
  > ボリュームの作成時の自動または既定のバックアップ ポリシーは無効になりました。

* **最終正常バックアップ日時** – このポリシーを使用して最後に成功したバックアップの日時です。

* **次回のバックアップ** – このポリシーによって開始される次回のバックアップの予定日時です。

* **ボリューム** – ポリシーに関連付けられたボリュームです。 バックアップ ポリシーに関連付けられているボリュームは、すべてバックアップの作成時にグループ化されます。

* **スケジュール** – バックアップ ポリシーに関連付けられたスケジュールの個数です。

バックアップ ポリシーに実行できる、頻繁に使用される操作は次のとおりです。

* バックアップ ポリシーを追加する
* スケジュールの追加または変更
* ボリュームの追加または削除
* バックアップ ポリシーの削除
* 手動バックアップの取得

## <a name="add-a-backup-policy"></a>バックアップ ポリシーを追加する

自動的にバックアップをスケジュールするためのバックアップ ポリシーを追加します。 最初にボリュームを作成するとき、ボリュームに関連付けられた既定のバックアップ ポリシーはありません。 ボリューム データを保護するには、バックアップ ポリシーを追加して割り当てる必要があります。

StorSimple デバイスのバックアップ ポリシーを追加するには、Azure ポータルで次の手順を実行します。 ポリシーを追加した後は、スケジュールを定義できます (「 [スケジュールの追加または変更](#add-or-modify-a-schedule)」を参照してください)。

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>スケジュールの追加または変更

StorSimple デバイスで、既存のバックアップ ポリシーに関連付けられるスケジュールを追加または変更できます。 スケジュールを追加または変更するには、Azure ポータルで次の手順を実行します。

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>ボリュームの追加または削除

StorSimple デバイスでバックアップ ポリシーに割り当てられたボリュームを追加または削除できます。 ボリュームを追加または削除するには、Azure ポータルで次の手順を実行します。

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>バックアップ ポリシーの削除

StorSimple デバイスからバックアップ ポリシーを削除するには、Azure ポータルで次の手順を実行します。

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>手動バックアップの取得

1 つのボリュームに対し、オンデマンドの (手動) バックアップを作成するには、Azure ポータルで次の手順を実行します。

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>次のステップ

[StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理](storsimple-8000-manager-service-administration.md)の詳細

