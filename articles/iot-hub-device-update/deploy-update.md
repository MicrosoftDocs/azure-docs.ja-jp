---
title: Device Update for Azure IoT Hub を使用して更新プログラムを展開する | Microsoft Docs
description: Device Update for Azure IoT Hub を使用して更新プログラムを展開します。
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 0a11c8e8946229941c1fe60f7f2ce84d9fadb2ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101678634"
---
# <a name="deploy-an-update-using-device-update-for-iot-hub"></a>Device Update for IoT Hub を使用して更新プログラムを展開する

Device Update for IoT Hub を使用して、IoT デバイスに更新プログラムを展開する方法について説明します。

## <a name="prerequisites"></a>前提条件

* [Device Update for IoT Hub が有効になっている IoT ハブにアクセスできること](create-device-update-account.md)。 IoT Hub には、S1 (Standard) レベル以上を使用することをお勧めします。 
* [少なくとも 1 つの更新プログラムが、プロビジョニング済みのデバイスに正常にインポートされていること。](import-update.md) 
* Device Update 用にプロビジョニングされている、IoT Hub 内の IoT デバイス (またはシミュレーター)。
* [更新対象の IoT デバイスにタグが割り当てられていること。デバイスが少なくとも 1 つの更新グループに属していること。](create-update-group.md)
* サポートされているブラウザー:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="deploy-an-update"></a>更新プログラムを展開する

1. [Azure portal](https://portal.azure.com) に移動します

2. IoT Hub の [Device Update]\(デバイスの更新プログラム\) ブレードに移動します。

  :::image type="content" source="media/deploy-update/device-update-iot-hub.png" alt-text="IoT Hub" lightbox="media/deploy-update/device-update-iot-hub.png":::

3. ページの上部にある [グループ] タブを選択します。 デバイス グループの[詳細についてはこちらを参照してください](device-update-groups.md)。 

  :::image type="content" source="media/deploy-update/updated-view.png" alt-text="[グループ] タブ" lightbox="media/deploy-update/updated-view.png":::

4. 更新プログラムのコンプライアンス チャートとグループの一覧を表示します。 デバイス グループで利用可能な新しい更新プログラムが表示され、[Pending Updates]\(保留中の更新プログラム\) の下にその更新プログラムへのリンクが表示されます (場合によっては 1 回更新する必要があります)。 [更新プログラムのコンプライアンスの詳細についてはこちらを参照してください。](device-update-compliance.md) 

5. 利用可能な更新プログラムを選択します。

6. 対象のグループとして、適切なグループが選択されていることを確認します。 展開をスケジュールし、[Deploy update]\(更新プログラムの展開\) を選択します。

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="更新プログラムを選択する" lightbox="media/deploy-update/select-update.png":::

7. コンプライアンス チャートを表示します。 更新が進行中であると表示されます。 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="更新が進行中です" lightbox="media/deploy-update/update-in-progress.png":::

8. デバイスが正常に更新されると、コンプライアンス チャートと展開の詳細が、同じ内容を反映するように更新されていることがわかります。 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="更新に成功しました" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>更新プログラムの展開を監視する

1. ページの上部にある [展開] タブを選択します。

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="[展開] タブ" lightbox="media/deploy-update/deployments-tab.png":::

2. 作成した展開を選択して、展開の詳細を表示します。

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="展開の詳細" lightbox="media/deploy-update/deployment-details.png":::

3. [更新] を選択し、最新の状態の詳細を表示します。 状態が [成功] に変わるまで、このプロセスを続行します。


## <a name="retry-an-update-deployment"></a>更新プログラムの展開を再試行する

何らかの理由で展開が失敗した場合は、失敗したデバイスの展開を再試行できます。 

1. [展開] タブにアクセスし、失敗した展開を選択します。 

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="展開の詳細" lightbox="media/deploy-update/deployment-details.png":::

2. 詳細な展開情報ペインで、"失敗" の [デバイスの状態] をクリックします。

3. [Retry failed devices]\(失敗したデバイスの再試行\) をクリックし、確認通知に同意します。 

## <a name="next-steps"></a>次のステップ

[一般的な問題のトラブルシューティング](troubleshoot-device-update.md)
