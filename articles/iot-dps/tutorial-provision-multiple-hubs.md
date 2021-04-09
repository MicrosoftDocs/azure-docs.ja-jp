---
title: チュートリアル - Azure IoT Hub Device Provisioning Service を使用して、負荷分散されたハブにデバイスをプロビジョニングする
description: このチュートリアルでは、負荷分散された IoT ハブに対する自動デバイス プロビジョニングを、Azure portal から Device Provisioning Service (DPS) を使用して実現する方法について説明します。
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 6cca34774038156cfb01e872e751338c1c96efaa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91316002"
---
# <a name="tutorial-provision-devices-across-load-balanced-iot-hubs"></a>チュートリアル: 負荷分散された IoT ハブへのデバイスのプロビジョニング

このチュートリアルでは、Device Provisioning Service を使用して、負荷分散された複数の IoT ハブにデバイスをプロビジョニングする方法について説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Portal を使用して 2 つ目のデバイスを 2 つ目の IoT ハブにプロビジョニングする 
> * 2 つ目のデバイスに登録リスト エントリを追加する
> * **均等に分散** させるように Device Provisioning Service 割り当てポリシーを設定する
> * 新しい IoT ハブを Device Provisioning Service にリンクする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルは、[デバイスをハブにプロビジョニングする方法](tutorial-provision-device-to-hub.md)に関する前のチュートリアルに基づいています。

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Azure Portal を使用して 2 つ目のデバイスを 2 つ目の IoT ハブにプロビジョニングする

[デバイスをハブにプロビジョニングする方法](tutorial-provision-device-to-hub.md)に関するチュートリアルの手順に従って、2 つ目のデバイスを別の IoT ハブにプロビジョニングします。

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>2 つ目のデバイスに登録リスト エントリを追加する

登録リストは、デバイスで使用する構成証明の方法 (デバイス ID を確認する方法) を Device Provisioning Service に指示します。 次の手順として、2 つ目のデバイスの登録リスト エントリを追加します。 

1. Device Provisioning Service のページで、**[登録を管理します]** をクリックします。 **[Add enrollment list entry]\(登録リスト エントリの追加\)** ページが表示されます。 
2. ページの上部にある **[追加]** をクリックします。
2. 各フィールドに入力し、**[保存]** をクリックします。

## <a name="set-the-device-provisioning-service-allocation-policy"></a>Device Provisioning Service の割り当てポリシーを設定する

割り当てポリシーは、デバイスを IoT ハブに割り当てる方法を決定する Device Provisioning Service 設定です。 次の 3 つの割り当てポリシーがサポートされています。 

1. **Lowest latency\(最も短い待機時間\)**: デバイスに対する待機時間が最も短いハブに基づいて、デバイスを IoT ハブにプロビジョニングします。
2. **Evenly weighted distribution\(均等に重み付けされた分散\)** (既定値): リンクされた各 IoT ハブにデバイスが同程度にプロビジョニングされます。 これが既定の設定です。 デバイスを 1 つの IoT ハブにのみプロビジョニングする場合は、この設定のままでかまいません。 
3. **Static configuration via the enrollment list\(登録リストによる静的構成\)**: 登録リストの目的の IoT ハブの仕様が、Device Provisioning Service レベルの割り当てポリシーよりも優先されます。

次の手順に従って割り当てポリシーを設定します。

1. 割り当てポリシーを設定するには、Device Provisioning Service ページで **[Manage allocation policy]\(割り当てポリシーの管理\)** をクリックします。
2. 割り当てポリシーを **[Evenly weighted distribution]\(均等に重み付けされた分散\)** に設定します。
3. **[保存]** をクリックします。

## <a name="link-the-new-iot-hub-to-the-device-provisioning-service"></a>新しい IoT ハブを Device Provisioning Service にリンクする

Device Provisioning Service と IoT ハブをリンクして、Provisioning Service がデバイスをそのハブに登録できるようにします。

1. **[すべてのリソース]** ページで、先ほど作成した Device Provisioning Service をクリックします。
2. Device Provisioning Service ページで、**[Linked IoT hubs]\(リンクされた IoT ハブ\)** をクリックします。
3. **[追加]** をクリックします。
4. **[Add link to IoT hub]\(IoT ハブへのリンクを追加\)** ページで、ラジオ ボタンを使用して、リンク対象の IoT ハブが現在のサブスクリプションと別のサブスクリプションのどちらにあるかを指定します。 次に、**[IoT ハブ]** ボックスから IoT ハブの名前を選択します。
5. **[保存]** をクリックします。

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure Portal を使用して 2 つ目のデバイスを 2 つ目の IoT ハブにプロビジョニングする 
> * 2 つ目のデバイスに登録リスト エントリを追加する
> * **均等に分散** させるように Device Provisioning Service 割り当てポリシーを設定する
> * 新しい IoT ハブを Device Provisioning Service にリンクする

## <a name="next-steps"></a>次のステップ

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps]()
-->
