---
title: "Azure IoT Hub Device Provisioning Service を使用して負荷分散された IoT ハブにデバイスをプロビジョニングする | Microsoft Docs"
description: "Azure Portal での負荷分散された IoT ハブへの DPS による自動デバイス プロビジョニング"
services: iot-dps
keywords: 
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 4842944cd0d980fb7e817165da23b9c3c4037e94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="provision-devices-across-load-balanced-iot-hubs"></a>負荷分散された IoT ハブへのデバイスのプロビジョニング

このチュートリアルでは、Device Provisioning Service (DPS) を使用して、負荷分散された複数の IoT ハブにデバイスをプロビジョニングする方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Portal を使用して 2 つ目のデバイスを 2 つ目の IoT ハブにプロビジョニングする 
> * 2 つ目のデバイスに登録リスト エントリを追加する
> * **均等に分散**させるように DPS 割り当てポリシーを設定する
> * 新しい IoT ハブを DPS にリンクする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルは、[デバイスをハブにプロビジョニングする方法](tutorial-provision-device-to-hub.md)に関する前のチュートリアルに基づいています。

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Azure Portal を使用して 2 つ目のデバイスを 2 つ目の IoT ハブにプロビジョニングする

[デバイスをハブにプロビジョニングする方法](tutorial-provision-device-to-hub.md)に関するチュートリアルの手順に従って、2 つ目のデバイスを別の IoT ハブにプロビジョニングします。

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>2 つ目のデバイスに登録リスト エントリを追加する

登録リストは、デバイスで使用する構成証明の方法 (デバイス ID を確認する方法) を DPS に指示します。 次の手順として、2 つ目のデバイスの登録リスト エントリを追加します。 

1. DPS のページで、**[Manage enrollments]\(登録の管理\)** をクリックします。 **[Add enrollment list entry]\(登録リスト エントリの追加\)** ページが表示されます。 
2. ページの上部にある **[追加]** をクリックします。
2. 各フィールドに入力し、**[保存]** をクリックします。

## <a name="set-the-dps-allocation-policy"></a>DPS 割り当てポリシーを設定する

割り当てポリシーは、デバイスを IoT ハブに割り当てる方法を決定する DPS 設定です。 次の 3 つの割り当てポリシーがサポートされています。 

1. **Lowest latency\(最も短い待機時間\)**: デバイスに対する待機時間が最も短いハブに基づいて、デバイスを IoT ハブにプロビジョニングします。
2. **Evenly weighted distribution\(均等に重み付けされた分散\)** (既定値): リンクされた各 IoT ハブにデバイスが同程度にプロビジョニングされます。 これは、既定の設定です。 デバイスを 1 つの IoT ハブにのみプロビジョニングする場合は、この設定のままでかまいません。 
3. **Static configuration via the enrollment list\(登録リストによる静的構成\)**: 登録リストの目的の IoT ハブの仕様が、DPS レベルの割り当てポリシーよりも優先されます。

次の手順に従って割り当てポリシーを設定します。

1. 割り当てポリシーを設定するには、DPS ページで **[Manage allocation policy]\(割り当てポリシーの管理\)** をクリックします。
2. 割り当てポリシーを **[Evenly weighted distribution]\(均等に重み付けされた分散\)** に設定します。
3. [ **Save**] をクリックします。

## <a name="link-the-new-iot-hub-to-dps"></a>新しい IoT ハブを DPS にリンクする

DPS と IoT ハブをリンクして、DPS がその ハブにデバイスを登録できるようにします。

1. **[すべてのリソース]** ページで、以前に作成した DPS をクリックします。
2. DPS ページで、**[Linked IoT hubs]\(リンクされた IoT ハブ\)** をクリックします。
3. **[追加]**をクリックします。
4. **[Add link to IoT hub]\(IoT ハブへのリンクを追加\)** ページで、ラジオ ボタンを使用して、リンク対象の IoT ハブが現在のサブスクリプションと別のサブスクリプションのどちらにあるかを指定します。 次に、**[IoT ハブ]** ボックスから IoT ハブの名前を選択します。
5. [ **Save**] をクリックします。

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure Portal を使用して 2 つ目のデバイスを 2 つ目の IoT ハブにプロビジョニングする 
> * 2 つ目のデバイスに登録リスト エントリを追加する
> * **均等に分散**させるように DPS 割り当てポリシーを設定する
> * 新しい IoT ハブを DPS にリンクする

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
-->
