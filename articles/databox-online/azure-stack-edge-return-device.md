---
title: Azure Stack Edge Pro デバイスを返却する | Microsoft Docs
description: データをワイプして Azure Stack Edge Pro デバイスを返却してから、そのデバイスに関連付けられているリソースを削除する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.openlocfilehash: cb11d7d3b2da9ab793cb18814e4021ea7afeb806
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443592"
---
# <a name="return-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro デバイスを返却する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、データをワイプして、ご利用の Azure Stack Edge Pro デバイスを返却する方法について説明します。 デバイスを返却した後、デバイスと関連付けられたリソースを削除することもできます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
>
> * デバイス上のデータ ディスクのデータを削除する
> * Azure portal でデバイスの返却を開始する
> * デバイスを選択して集荷をスケジュールする
> * Azure portal でリソースを削除する

## <a name="erase-data-from-the-device"></a>デバイスからデータを消去する

デバイス上のデータ ディスクのデータを削除するには、ご利用のデバイスをリセットする必要があります。

リセットする前に、必要に応じて、デバイス上のローカル コピーを作成します。 デバイスから Azure Storage コンテナーにデータをコピーできます。 

デバイスがリセットされる前でも、デバイスの返却を開始できます。

ローカル Web UI または PowerShell でデバイスをリセットできます。 PowerShell の手順については、「[デバイスをリセットする](./azure-stack-edge-connect-powershell-interface.md#reset-your-device)」を参照してください。

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

> [!NOTE]
> - 新しいデバイスに交換またはアップグレードする場合は、新しいデバイスを受け取ってからご利用のデバイスをリセットすることをお勧めします。
> - デバイスのリセットでは、デバイスからすべてのローカル データが削除されるだけです。 クラウド内にあるデータは削除されることなく、[料金](https://azure.microsoft.com/pricing/details/storage/)がかかります。 このデータは、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) などのクラウド ストレージ管理ツールを使用して、個別に削除する必要があります。

## <a name="initiate-device-return"></a>デバイスの返却を開始する

返却プロセスを開始するには、次の手順を行います。

1. Azure portal で Azure Stack Edge Pro または Data Box Gateway リソースにアクセスします。 **[概要]** で、右ペインのコマンド バーに移動し、 **[デバイスの返却]** を選択します。 

    ![デバイスの返却 1](media/azure-stack-edge-return-device/return-device-1.png)  

2. **[デバイスの返却]** ブレードの **[基本的な詳細]** で:

    1. デバイスのシリアル番号を指定します。 デバイスのシリアル番号を取得するには、デバイスのローカル Web UI に移動し、 **[概要]** に移動します。  
    
       ![デバイスのシリアル番号 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. サービス タグ番号を入力します。 サービス タグ番号は、デバイスに固有の 5 文字以上の識別子です。 サービス タグは、デバイスの (デバイスに向かって) 右下隅にあります。 情報タグ (スライド式のラベル パネル) を引き出します。 このパネルには、サービス タグ、NIC、MAC アドレスなどのシステム情報が含まれています。 
    
       ![サービス タグ番号 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. ドロップダウン リストから、返却の理由を選択します。

       ![デバイスの返却 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. **[Shipping details]\(発送元の詳細\)** の下で:

    1. ご自分の氏名、会社名、および会社の完全な住所を入力します。 勤務先電話番号 (市外局番を含む) と通知用の電子メール ID を入力します。
    2. 返送用ボックスが必要な場合は、要求することができます。 **[Need an empty box to return]\(返却するために空き箱が必要\)** の質問に **[はい]** と回答します。

    ![デバイスの返却 3](media/azure-stack-edge-return-device/return-device-3.png)

4. **プライバシー条項** を確認し、確認してプライバシー条項に同意したという注記の横にあるチェックボックスをオンにします。

5. **[Initiate return]\(返却の開始\)** を選択します。

    ![デバイスの返却 4](media/azure-stack-edge-return-device/return-device-4.png) 

6. デバイスの返却の詳細がキャプチャされたら、Azure Stack Edge Pro 運用チームにメールで通知できます。 メール アプリケーションがインストールされ、構成されていることを前提として、メール アプリケーションを使用できます。 また、データをコピーして作成し、メールを送信することもできます。

    ![デバイスの返却 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Azure Stack Edge Pro 運用チームがメールを受信すると、返送ラベルが送られてきます。 このラベルを受け取ったら、運送業者とデバイスの集荷をスケジュールできます。 

## <a name="schedule-a-pickup"></a>集荷をスケジュールする

集荷をスケジュールするには、次の手順を行います。

1. デバイスをシャットダウンします。 ローカル Web UI で、 **[メンテナンス] > [Power settings]\(電源設定\)** に移動します。
2. **[シャットダウン]** を選択します。 確認を求めるメッセージが表示されたら、 **[はい]** をクリックして続行します。 詳細については、「[電源を管理する](../databox-gateway/data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)」を参照してください。
3. 電源ケーブルを外して、デバイスからネットワーク ケーブルをすべて取り外します。
4. 自分の箱または Azure から受け取った空き箱を使用して、出荷パッケージを準備します。 デバイスとそのデバイスに同梱されていた電源コードを箱に入れます。
5. Azure から受け取った出荷ラベルをパッケージに貼り付けます。
6. リージョンの運送業者で集荷をスケジュールします。 米国でデバイスを返却する場合、UPS または FedEx の運送業者をご利用になれます。 UPS に集荷をスケジュールするには:

    1. 最寄りの UPS (国/地域固有のフリー ダイヤル) に連絡します。
    2. 電話で、印刷ラベルに表示されている返送追跡番号を伝えます。
    3. 追跡番号を伝えないと、集荷時に UPS から追加料金が請求されます。

    集荷のスケジュールを設定する代わりに、最寄りの持ち込み場所に Azure Stack Edge Pro を持ち込むこともできます。

## <a name="delete-the-resource"></a>リソースを削除する

デバイスが Azure データセンターに到着した後、デバイスは破損や改ざんの兆候がないかを検証されます。

- デバイスがそのまま良好な状態で到着した場合、課金メーターはそのリソースに対して停止されます。 Azure Stack Edge Pro 運用チームが、デバイスが返却されたことを確認するためにご連絡します。 その後、Azure portal でデバイスと関連付けられたリソースを削除することができます。
- デバイスが大幅に破損して到着した場合、罰金が適用される可能性があります。 詳細については、[紛失または破損しているデバイスに関する FAQ](https://azure.microsoft.com/pricing/details/databox/edge/) に関するページと[製品のサービス使用条件](https://www.microsoft.com/licensing/product-licensing/products)に関するページを参照してください。  


次の場合に Azure portal でデバイスを削除できます。

- 注文後、デバイスが Microsoft によって準備される前。
- デバイスを Microsoft に返送し、Azure Stack Edge Pro 運用チームからデバイスが返されたことを確認する連絡があった後。 返されたデバイスが Azure データセンターでの物理的な検査に合格するまで、運用チームが連絡することはありません。

別のサブスクリプションまたは場所に対してデバイスを有効にしている場合、Microsoft によって 1 営業日以内に新しいサブスクリプションまたは場所に注文が移動されます。 注文が移動された後、このリソースを削除できます。


Azure portal でデバイスとリソースを削除するには、次の手順を行います。

1. Azure portal で自分のリソースに移動し、 **[概要]** に移動します。 コマンド バーで、 **[削除]** を選択します。

    ![[削除] の選択](media/azure-stack-edge-return-device/delete-resource-1.png)

2. **[デバイスの削除]** ブレードで、削除するデバイスの名前を入力して **[削除]** を選択します。

    ![削除の確定](media/azure-stack-edge-return-device/delete-resource-2.png)

デバイスと関連付けられたリソースが正常に削除されたら、通知されます。


## <a name="next-steps"></a>次の手順

- [Azure Stack Edge Pro デバイスを交換する](azure-stack-edge-replace-device.md)方法について説明します。