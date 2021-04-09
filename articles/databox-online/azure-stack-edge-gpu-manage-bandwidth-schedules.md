---
title: Azure Stack Edge Pro GPU で帯域幅のスケジュールを管理する | Microsoft Docs
description: Azure portal を使用して Azure Stack Edge Pro GPU の帯域幅のスケジュールを管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 7b091ff1ec825ac2292345183eb77bc37f02f6e0
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "102637140"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-stack-edge-pro-gpu"></a>Azure portal を使用して Azure Stack Edge Pro GPU の帯域幅のスケジュールを管理する 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro の帯域幅のスケジュールを管理する方法について説明します。 帯域幅のスケジュールを使用すると、複数の時刻スケジュールにわたって、ネットワーク帯域幅の使用量を構成することができます。 これらのスケジュールは、デバイスとクラウド間のアップロード操作とダウンロード操作に適用することができます。

Azure portal を使用して Azure Stack Edge Pro の帯域幅スケジュールを追加、変更、または削除することができます。

この記事では、次のことについて説明します。 

> [!div class="checklist"]
> * スケジュールの追加
> * スケジュールの変更
> * スケジュールの削除


## <a name="add-a-schedule"></a>スケジュールの追加

スケジュールを追加するには、Azure portal で次の手順を実行します。

1. Azure portal で Azure Stack Edge リソースの **[帯域幅]** にアクセスします。
2. 右側のウィンドウで、 **[+ スケジュールの追加]** を選択します。

    ![帯域幅の選択](media/azure-stack-edge-gpu-manage-bandwidth-schedules/add-schedule-1.png)

3. **[スケジュールの追加]** で、以下の操作を行います。

   1. スケジュールの **[開始日]** 、 **[終了日]** 、 **[開始時刻]** 、および **[終了時刻]** を指定します。
   2. このスケジュールを 1 日中実行する場合は、 **[終日]** オプションをオンにします。
   3. **[帯域幅のレート]** は、クラウドに関連する操作 (アップロードとダウンロード) でデバイスが使用する帯域幅 (メガビット/秒 (Mbps)) です。 このフィールドには 64 から 2,147,483,647 までの値を指定します。
   4. アップロードとダウンロードの日付を調整しない場合は、 **[無制限の帯域幅]** をオンにします。
   5. **[追加]** を選択します。

      ![スケジュールの追加](media/azure-stack-edge-gpu-manage-bandwidth-schedules/add-schedule-2.png)

3. 指定したパラメーターでスケジュールが作成されます。 このスケジュールは、ポータルで、帯域幅のスケジュールの一覧に表示されます。

    ![帯域幅スケジュールの更新された一覧](media/azure-stack-edge-gpu-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>スケジュールの編集

帯域幅のスケジュールを編集するには、次の手順を実行します。

1. Azure portal で Azure Stack Edge リソースに移動し、 **[帯域幅]** に移動します。
2. 帯域幅のスケジュールの一覧で、変更するスケジュールを選択します。

   ![帯域幅スケジュールの選択](media/azure-stack-edge-gpu-manage-bandwidth-schedules/modify-schedule-1.png)

3. 必要な変更を加えたら、変更を保存します。

    ![ユーザーの変更](media/azure-stack-edge-gpu-manage-bandwidth-schedules/modify-schedule-2.png)

4. スケジュールを変更した後、スケジュールの一覧は、変更されたスケジュールを反映するように更新されます。

    ![ユーザーの変更 2](media/azure-stack-edge-gpu-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>スケジュールの削除

Azure Stack Edge Pro デバイスに関連付けられている帯域幅のスケジュールを削除するには、次の手順を実行します。

1. Azure portal で Azure Stack Edge リソースに移動し、 **[帯域幅]** に移動します。  

2. 帯域幅のスケジュールの一覧で、削除するスケジュールを選択します。 **[スケジュールの編集]** で **[削除]** を選択します。 確認を求められたら、 **[はい]** を選択します。

   ![ユーザーの削除](media/azure-stack-edge-gpu-manage-bandwidth-schedules/delete-schedule-2.png)

3. スケジュールを削除した後、スケジュールの一覧が更新されます。


## <a name="next-steps"></a>次のステップ

- [共有を管理する](azure-stack-edge-gpu-manage-shares.md)方法を確認します。
