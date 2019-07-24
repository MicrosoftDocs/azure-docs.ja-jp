---
title: Azure Data Box Edge の帯域幅スケジュールの管理 | Microsoft Docs
description: Azure portal を使用して Azure Data Box Edge の帯域幅のスケジュールを管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: f7b762d5502986c306de240519688aa639f58445
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999656"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-edge"></a>Azure portal を使用して Azure Data Box Edge の帯域幅のスケジュールを管理する  

この記事では、Azure Data Box Edge のユーザーを管理する方法について説明します。 帯域幅のスケジュールを使用すると、複数の時刻スケジュールにわたって、ネットワーク帯域幅の使用量を構成することができます。 これらのスケジュールは、デバイスとクラウド間のアップロード操作とダウンロード操作に適用することができます。

Azure portal を使用して Data Box Edge の帯域幅スケジュールを追加、変更、または削除することができます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * スケジュールの追加
> * スケジュールの変更
> * スケジュールの削除


## <a name="add-a-schedule"></a>スケジュールの追加

スケジュールを追加するには、Azure portal で次の手順を実行します。

1. Azure portal の Data Box Edge リソースで、**[帯域幅]** に移動します。
2. 右側のウィンドウで、**[+ スケジュールの追加]** を選択します。

    ![帯域幅の選択](media/data-box-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. **[スケジュールの追加]** で、以下の操作を行います。 

   1. スケジュールの **[開始日]**、**[終了日]**、**[開始時刻]**、および **[終了時刻]** を指定します。
   2. このスケジュールを 1 日中実行する場合は、**[終日]** オプションをオンにします。
   3. **[帯域幅のレート]** は、クラウドに関連する操作 (アップロードとダウンロード) でデバイスが使用する帯域幅 (メガビット/秒 (Mbps)) です。 このフィールドには 20 から 1,000,000,007 の値を指定します。
   4. アップロードとダウンロードの日付を調整しない場合は、**[無制限の帯域幅]** をオンにします。
   5. **[追加]** を選択します。

      ![スケジュールの追加](media/data-box-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. 指定したパラメーターでスケジュールが作成されます。 このスケジュールは、ポータルで、帯域幅のスケジュールの一覧に表示されます。

    ![帯域幅スケジュールの更新された一覧](media/data-box-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>スケジュールの編集

帯域幅のスケジュールを編集するには、次の手順を実行します。

1. Azure portal で Data Box Edge リソースに移動し、**[帯域幅]** に移動します。 
2. 帯域幅のスケジュールの一覧で、変更するスケジュールを選択します。
    ![帯域幅スケジュールの選択](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. 必要な変更を加えたら、変更を保存します。

    ![ユーザーを変更する](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. スケジュールを変更した後、スケジュールの一覧は、変更されたスケジュールを反映するように更新されます。

    ![ユーザーを変更する](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>スケジュールの削除

Data Box Edge デバイスに関連付けられている帯域幅のスケジュールを削除するには、次の手順を実行します。

1. Azure portal で Data Box Edge リソースに移動し、**[帯域幅]** に移動します。  

2. 帯域幅のスケジュールの一覧で、削除するスケジュールを選択します。 **[スケジュールの編集]** で **[削除]** を選択します。 確認を求められたら、**[はい]** を選択します。

   ![ユーザーの削除](media/data-box-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. スケジュールを削除した後、スケジュールの一覧が更新されます。


## <a name="next-steps"></a>次の手順

- [共有を管理する](data-box-edge-manage-shares.md)方法を確認します。
