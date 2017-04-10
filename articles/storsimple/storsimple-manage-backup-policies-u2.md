---
title: "StorSimple バックアップ ポリシーの管理 | Microsoft Docs"
description: "StorSimple Manager サービスを使用して、手動バックアップ、バックアップのスケジュールを作成して、バックアップのリテンション期間を管理する方法について説明します。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 4a2db707-bbfc-425c-bfeb-bc5c97781873
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/10/2016
ms.author: v-sharos
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7f698f1560d99ce9ba0ed125c401f04473170c2b


---
# <a name="use-the-storsimple-manager-service-to-manage-backup-policies-update-2"></a>StorSimple Manager サービスを使用してバックアップ ポリシーを管理する (Update 2)
[!INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>概要
このチュートリアルでは、StorSimple Manager サービスの **[バックアップ ポリシー]** ページを使用して、StorSimple ボリュームのバックアップ プロセスとバックアップのリテンション期間を制御する方法について説明します。 また、手動バックアップを完了する方法についても説明します。

ボリュームをバックアップする場合は、ローカル スナップショットとクラウド スナップショットのどちらを作成するかを選択できます。 ローカル固定ボリュームをバックアップする場合は、クラウド スナップショットを指定することをお勧めします。 離反要因が多いデータ セットに加えて、ローカル固定ボリュームの多数のローカル スナップショットを作成すると、ローカル領域が急激に減少する状態が発生します。 ローカル スナップショットの作成を選択した場合は、最新の状態をバックアップするための毎日のスナップショットの作成回数を少なくし、それらを 1 日間保持した後で削除することをお勧めします。

ローカル固定ボリュームのクラウド スナップショットを作成すると、変更されたデータのみがクラウドにコピーされ、クラウドで重複除去と圧縮が行われます。 

## <a name="the-backup-policies-page"></a>[バックアップ ポリシー] ページ
**[バックアップ ポリシー]** ページでは、バックアップ ポリシーを管理して、ローカル スナップショットとクラウド スナップショットのスケジュールを設定できます  (バックアップ ポリシーは、ボリュームのコレクションに対するバックアップのスケジュールとバックアップのリテンション期間の構成に使用します)。バックアップ ポリシーを使用すると、複数のボリュームのスナップショットを同時に取得できます。 これは、バックアップ ポリシーによって作成されたバックアップがクラッシュ整合コピーになることを意味します。 **[バックアップ ポリシー]** ページには、バックアップ ポリシーとその種類、関連付けられているボリューム、保持されているバックアップの数、およびこれらのポリシーを有効にするオプションが一覧表示されます。

また **[バックアップ ポリシー]** ページでは、次の 1 つ以上のフィールドで既存のバックアップ ポリシーをフィルター処理することもできます。

* **ポリシー名** – ポリシーに関連付けられた名前。 次の種類のポリシーがあります。
  
  * スケジュールされたポリシー。ユーザーが明示的に作成します。
  * 自動ポリシー。ボリュームの作成時にこのボリュームのオプションの既定のバックアップが有効になっている場合に作成されます。 これらのポリシーの名前は、*VolumeName*_Default という形式で指定されます。*VolumeName* は、ユーザーが Azure クラシック ポータルで構成する StorSimple ボリュームの名前です。 自動ポリシーにより、デバイス時刻の 22 時 30分を起点として、1 日のクラウド スナップショットを取得します。
  * インポートされたポリシー。作成元は StorSimple Snapshot Manager です。 このポリシーには、インポート元の StorSimple Snapshot Manager ホストについて説明するタグが付いています。
* **ボリューム** – ポリシーに関連付けられているボリューム。 バックアップ ポリシーに関連付けられているボリュームは、すべてバックアップの作成時にグループ化されます。
* **前回の正常なバックアップ** – このポリシーで取得した、最後の正常なバックアップの日時。
* **次回のバックアップ** – このポリシーによって開始される、次のスケジュールされたバックアップの日時。
* **スケジュール** – バックアップ ポリシーに関連付けられているスケジュールの数。

このページから実行できる、頻繁に使用される操作は次のとおりです。

* バックアップ ポリシーの追加 
* スケジュールの追加または変更 
* バックアップ ポリシーの削除 
* 手動バックアップの取得 
* 複数のボリュームとスケジュールによるカスタム バックアップ ポリシーの作成 

## <a name="add-a-backup-policy"></a>バックアップ ポリシーの追加
自動的にバックアップをスケジュールするためのバックアップ ポリシーを追加します。 StorSimple デバイスのバックアップ ポリシーを追加するには、Azure クラシック ポータルで次の手順を実行します。 ポリシーを追加した後は、スケジュールを定義できます (「 [スケジュールの追加または変更](#add-or-modify-a-schedule)」を参照してください)。

[!INCLUDE [storsimple-add-backup-policy-u2](../../includes/storsimple-add-backup-policy-u2.md)]

![ビデオ](./media/storsimple-manage-backup-policies-u2/Video_icon.png) **ビデオ**

ローカルまたはクラウドのバックアップ ポリシーを作成する方法を説明したビデオについては、 [こちら](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/)を参照してください。

## <a name="add-or-modify-a-schedule"></a>スケジュールの追加または変更
StorSimple デバイスで、既存のバックアップ ポリシーに関連付けられるスケジュールを追加または変更できます。 スケジュールを追加または変更するには、Azure クラシック ポータルで次の手順を実行します。

[!INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule-u2.md)]

## <a name="delete-a-backup-policy"></a>バックアップ ポリシーの削除
StorSimple デバイスからバックアップ ポリシーを削除するには、Azure クラシック ポータルで次の手順を実行します。

[!INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>手動バックアップの取得
1 つのボリュームに対し、オンデマンドの (手動) バックアップを作成するには、Azure クラシック ポータルで次の手順を実行します。

[!INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>複数のボリュームとスケジュールによるカスタム バックアップ ポリシーの作成
複数のボリュームとスケジュールを含むカスタム バックアップ ポリシーを作成するには、Azure クラシック ポータルで次の手順を実行します。

[!INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy-u2.md)]

## <a name="next-steps"></a>次のステップ
[StorSimple Manager サービスを使用した StorSimple デバイスの管理の詳細](storsimple-manager-service-administration.md)




<!--HONumber=Nov16_HO3-->


