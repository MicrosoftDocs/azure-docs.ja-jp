---
title: "Azure Portal における Device Provisioning の設定 | Microsoft Docs"
description: "Azure クイックスタート - Azure Portal で Azure IoT Hub Device Provisioning Service を設定する"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 519ea43bfaa9d61de6ff962d838c197eb9e4edc0
ms.contentlocale: ja-jp
ms.lasthandoff: 09/07/2017

---

# <a name="set-up-the-iot-hub-device-provisioning-service-preview-with-the-azure-portal"></a>Azure Portal で IoT Hub Device Provisioning Service (プレビュー) を設定する

以下の手順では、デバイスのプロビジョニングに使用する Azure クラウド リソースをポータルから設定する方法について説明しています。 IoT ハブの作成と新しい IoT Hub Device Provisioning Service の作成、さらにその 2 つのサービスのリンクなどが、ここでの作業の対象となります。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

[Azure Portal](https://portal.azure.com/) にログインします。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

1. Azure Portal の左上隅にある **[新規]** ボタンをクリックします。

2. **[モノのインターネット]** を選択し、**[IoT Hub]** を選択して **[作成]** ボタンをクリックします。 

3. IoT ハブに**名前**を付けます。 価格のオプションを選択し、[IoT Hub ユニット](https://azure.microsoft.com/pricing/details/iot-hub/)を入力します。次に、デバイスからクラウドへのメッセージに使用するパーティションの数を [Device-to-cloud パーティション] で選択し、このリソースに使用するサブスクリプションを選択します。 新規のリソース グループまたは既存のリソース グループの名前を入力し、場所を選択します。 完了したら **[作成]** をクリックします。

    ![ポータルのブレードで IoT ハブに関する基本情報を入力する](./media/quick-setup-auto-provision/create-iot-hub-portal.png)  

4. IoT ハブが正常にデプロイされると、ハブの概要ブレードが自動的に表示されます。


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service の新しいインスタンスを作成する

1. Azure Portal の左上隅にある **[新規]** ボタンをクリックします。

2. *[Marketplace を検索]* で **Device Provisioning Service** を探します。 **[IoT Device Provisioning Service (preview)]\(IoT Device Provisioning Service (プレビュー)\)** を選択し、**[作成]** ボタンをクリックします。 

3. **[名前]** に、Device Provisioning Service インスタンスの名前を入力します。 このインスタンスに使用するサブスクリプションを選択し、新しいリソース グループまたは既にあるリソース グループの名前を入力します。 場所を選択します。 完了したら **[作成]** をクリックします。

    ![DPS インスタンスに関する基本的な情報をポータルのブレードに入力](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. サービスが正常にデプロイされると、対応する概要ブレードが自動的に表示されます。


## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>IoT ハブと Device Provisioning サービスとをリンクさせる

1. Azure Portal の左側のメニューにある **[すべてのリソース]** ボタンをクリックします。 前のセクションで作成した Device Provisioning Service インスタンスを選択します。  

2. Device Provisioning Service の概要ブレードで **[Linked IoT hubs]\(リンクされた IoT ハブ\)** を選択します。 一番上の **[+ 追加]** ボタンをクリックします。 

3. ポータルの **[Add link to IoT hub or pool]\(IoT ハブまたはプールにリンクを追加\)** ブレードで、現在のサブスクリプションを入力するか、または別のサブスクリプションの名前と接続文字列を入力します。 **[リソースの種類]** として *[IoT Hub]* を選択し、ドロップダウン リストからハブの名前を選択します。 完了したら、**[保存]** をクリックします。 

    ![ポータルのブレードでハブ名を DPS インスタンスにリンク](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. 選択したハブが **[Linked IoT hubs]\(リンクされた IoT ハブ\)** ブレードに表示されます。 



## <a name="clean-up-resources"></a>リソースのクリーンアップ

このコレクションの他のクイックスタートは、このクイックスタートに基づいています。 引き続きクイックスタートまたはチュートリアルの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 これ以上作業を行わない場合は、次の手順に従って、このクイックスタートで作成したすべてのリソースを Azure Portal で削除してください。

1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、Device Provisioning サービスを選択します。 **[すべてのリソース]** ブレードの上部にある **[削除]** をクリックします。  
2. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、IoT ハブを選択します。 **[すべてのリソース]** ブレードの上部にある **[削除]** をクリックします。  

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT ハブと Device Provisioning Service インスタンスをデプロイし、この 2 つのリソースをリンクさせました。 ここで行った設定を使用して、シミュレートされたデバイスをプロビジョニングする方法については、シミュレートされたデバイスの作成に関するクイックスタートを参照してください。

> [!div class="nextstepaction"]
> [シミュレートされたデバイスを作成するためのクイックスタート](./quick-create-simulated-device.md)

