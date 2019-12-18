---
title: チュートリアル - ポータルで Azure IoT Hub Device Provisioning Service のためのクラウドを設定する
description: このチュートリアルでは、IoT Hub Device Provisioning Service (DPS) を使用して [Azure portal](https://portal.azure.com) でデバイス プロビジョニングのためのクラウド リソースを設定する方法について説明します。
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 925d1178ad630699cfd3d9e48677c0f029fc75af
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976725"
---
# <a name="tutorial-configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>チュートリアル:IoT Hub Device Provisioning Service を使用したデバイス プロビジョニングのためのクラウド リソースの構成

このチュートリアルでは、IoT Hub Device Provisioning Service を使用した自動デバイス プロビジョニングのためのクラウドを設定する方法について説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Portal を使用して IoT Hub Device Provisioning Service を作成し、ID スコープを取得する
> * IoT Hub の作成
> * IoT ハブを Device Provisioning Service にリンクする
> * Device Provisioning Service で割り当てポリシーを設定する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Device Provisioning Service インスタンスを作成し、ID スコープを取得する

新しい Device Provisioning Service インスタンスを作成するには、次の手順に従います。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。

2. 検索ボックスに「**device provisioning**」と入力します。 

3. **[IoT Hub Device Provisioning Service]** をクリックします。

4. **IoT Hub Device Provisioning Service** フォームに次の情報を入力します。
    
   | Setting       | 推奨値 | Description | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Name** | 一意の名前 | -- | 
   | **サブスクリプション** | 該当するサブスクリプション  | サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。 |
   | **[リソース グループ]** | myResourceGroup | 有効なリソース グループ名については、[名前付け規則と制限](/azure/architecture/best-practices/resource-naming)に関するページを参照してください。 |
   | **Location** | 有効な場所 | リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。 |   

   ![Device Provisioning Service に関する基本的な情報をポータルで入力](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. **Create** をクリックしてください。 しばらくすると、Device Provisioning Service インスタンスが作成され、 **[概要]** ページが表示されます。

6. 新しいサービス インスタンスの **[概要]** ページで、後で使用するために **[ID スコープ]** の値をコピーします。 この値は、登録 ID の識別に使用されます。この値により、登録 ID が一意であることが保証されます。

7. また、後で使用するために、 **[サービス エンドポイント]** の値もコピーします。 

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>IoT ハブに対する接続文字列を取得する

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

IoT Hub の作成は以上です。以降の作業に必要なホスト名と IoT Hub 接続文字列が得られました。

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>Device Provisioning Service を IoT ハブにリンクする

次に、IoT Hub Device Provisioning Service と IoT ハブをリンクして、Device Provisioning Service がデバイスをそのハブに登録できるようにします。 Device Provisioning Service は、このサービスにリンクされている IoT ハブにのみデバイスをプロビジョニングできます。 次の手順に従います。

1. **[すべてのリソース]** ページで、先ほど作成した Device Provisioning Service インスタンスをクリックします。

2. Device Provisioning Service ページで、 **[Linked IoT hubs]\(リンクされた IoT ハブ\)** をクリックします。

3. **[追加]** をクリックします。

4. **[IoT Hub へのリンクを追加します]** ページで、以下の情報を入力して、 **[保存]** をクリックします。

    * **サブスクリプション:** IoT ハブを含むサブスクリプションが選択されていることを確認してください。 別のサブスクリプション内にある IoT ハブにリンクすることもできます。

    * **[IoT Hub]:** この Device Provisioning Service インスタンスとリンクする IoT ハブの名前を選択します。

    * **[アクセス ポリシー]:** IoT ハブとのリンクを確立するために使用する資格情報として **[iothubowner]** を選択します。

   ![ポータルで Device Provisioning Service にハブ名をリンク](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Device Provisioning Service で割り当てポリシーを設定する

割り当てポリシーは、デバイスを IoT ハブに割り当てる方法を決定する IoT Hub Device Provisioning Service 設定です。 次の 3 つの割り当てポリシーがサポートされています。 

1. **[最短待機時間]** : デバイスに対する待ち時間が最も短いハブに基づいて、デバイスを IoT ハブにプロビジョニングします。

2. **[加重が均等に分布]** (既定): リンクされた各 IoT Hub にデバイスが同程度にプロビジョニングされます。 これは、既定の設定です。 デバイスを 1 つの IoT ハブにのみプロビジョニングする場合は、この設定のままでかまいません。 

3. **[登録リストを経由する静的構成]** : 登録リストの目的の IoT ハブの仕様が、Device Provisioning Service レベルの割り当てポリシーよりも優先されます。

割り当てポリシーを設定するには、Device Provisioning Service ページで **[Manage allocation policy]\(割り当てポリシーの管理\)** をクリックします。 割り当てポリシーが **[Evenly weighted distribution]\(均等に重み付けされた分散\)** (既定値) に設定されていることを確認します。 設定を変更した場合は、作業が終わったら **[保存]** をクリックします。

![割り当てポリシーを管理する](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このコレクションの他のチュートリアルは、このチュートリアルに基づいています。 引き続きクイック スタートまたはチュートリアルの作業を行う場合は、このチュートリアルで作成したリソースをクリーンアップしないでください。 作業を続行しない場合は、次の手順に従って、このチュートリアルで作成したすべてのリソースを Azure Portal で削除します。

1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、IoT Hub Device Provisioning Service インスタンスを選択します。 **[すべてのリソース]** ページの上部にある **[削除]** をクリックします。  

2. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、IoT ハブを選択します。 **[すべてのリソース]** ページの上部にある **[削除]** をクリックします。
 
## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure Portal を使用して IoT Hub Device Provisioning Service を作成し、ID スコープを取得する
> * IoT Hub の作成
> * IoT ハブを Device Provisioning Service にリンクする
> * Device Provisioning Service で割り当てポリシーを設定する

次のチュートリアルに進み、プロビジョニングするデバイスを設定する方法を学習してください。

> [!div class="nextstepaction"]
> [プロビジョニングするデバイスを設定する](tutorial-set-up-device.md)
