---
title: Azure Data Box の返送に関するチュートリアル | Microsoft Docs
description: このチュートリアルでは、Azure Data Box を返送する方法について説明します。これには、発送の準備、Data Box の発送、データ アップロードの検証、Data Box からのデータの消去などが含まれます。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/02/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 228f837a8826612bbbadf2ca8c5ef339ab248397
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524994"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>チュートリアル:Azure Data Box の返送と Azure へのデータ アップロードの確認

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>Data Box の返送と Azure へのデータ アップロードの確認

::: zone-end

::: zone target="docs"

このチュートリアルでは、Azure Data Box を返送し、Azure にアップロードされたデータを確認する方法について説明します。

このチュートリアルでは、次のようなトピックについて説明します。

> [!div class="checklist"]
>
> * 前提条件
> * 発送の準備をする
> * Data Box を Microsoft に送付する
> * Azure へのデータのアップロードを確認する
> * Data Box からデータを消去する

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

* 「[チュートリアル: Azure Data Box にデータをコピーして確認する](data-box-deploy-copy-data.md)」を完了していることを確認してください。
* コピー ジョブが完了し、 **[接続とコピー]** ページにエラーがないこと。 コピー ジョブが進行中の場合、または **[接続とコピー]** ページにエラーがある場合、**発送準備** を行うことはできません。

## <a name="prepare-to-ship"></a>発送の準備をする

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

データのコピーが完了したら、デバイスを準備して発送します。 デバイスが Azure データセンターに到着すると、データが自動的に Azure にアップロードされます。

## <a name="prepare-to-ship"></a>発送の準備をする

発送を準備する前に、コピー ジョブが完了していることを確認します。

1. ローカル Web UI の **[出荷準備]** ページに移動し、出荷準備を開始します。
2. ローカル Web UI からデバイスをオフにします。 ケーブルをデバイスから取り外します。

次の手順は、デバイスを返送する場所によって決まります。

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Data Box を返送する

デバイスへのデータ コピーが完了し、**発送準備** が正常に実行されたことを確認します。 デバイスを発送する地域によって手順が異なります。

::: zone-end

## <a name="us-canada-europe"></a>[米国、カナダ、ヨーロッパ](#tab/in-us-canada-europe)

米国、カナダ、またはヨーロッパでデバイスを返送する場合は、次の手順を行います。

1. 必ずデバイスの電源をオフにてケーブルを取り外します。
2. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
3. 配送先住所ラベルが電子インク ディスプレイに表示されていることを確認し、運送業者の集荷をスケジュールします。 ラベルを破損または紛失した場合、またはラベルが電子インク ディスプレイに表示されていない場合、Microsoft サポートにお問い合わせください。 サポートから指示された場合は、Azure portal の **[概要] > [出荷ラベルをダウンロード]** にアクセスしてください。 配送先住所ラベルをダウンロードして、デバイスに貼り付けます。 
4. デバイスを返送する場合は、UPS での集荷をスケジュールします。 集荷のスケジュールを設定するには:

    * 最寄りの UPS (国/地域固有のフリー ダイヤル) に連絡します。
    * 電話で、電子インク ディスプレイまたは印刷ラベルに表示されている返送追跡番号を伝えます。 追跡番号を伝えないと、集荷時に UPS から追加料金が請求されます。
    * 集荷のスケジュール中に問題が発生した場合や、追加料金の支払いを求められた場合は、Azure Data Box Operations にお問い合わせください。 [adbops@microsoft.com](mailto:adbops@microsoft.com) に電子メールを送信します。

    集荷のスケジュールを設定する代わりに、最寄りの持ち込み場所に Data Box を持ち込むこともできます。
4. 運送業者によって Data Box が集荷され、スキャンされると、ポータルの注文状態は "**集荷されました**" に更新されます。 追跡 ID も表示されます。

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する

Azure へのアップロードが完了すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。

::: zone-end

## <a name="australia"></a>[オーストラリア](#tab/in-australia)

オーストラリアの Azure データセンターには、追加のセキュリティ通知があります。 すべての国内配送には事前通知が必要です。 オーストラリアで発送するには、次の手順を行います。

1. デバイスを返送する際に使用する元の箱を保管しておきます。
2. デバイスへのデータ コピーが完了し、 **[発送準備]** の実行が正常終了することを確認します。
3. デバイスの電源をオフにして、ケーブルを取り外します。
4. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
5. [DHL のリンク](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference)からオンラインで集荷を予約します。

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する

Azure へのアップロードが完了すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。

::: zone-end

## <a name="japan"></a>[日本](#tab/in-japan)

1. デバイスを返送する際に使用する元の箱を保管しておきます。
2. デバイスの電源をオフにして、ケーブルを取り外します。
3. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
4. 伝票に送信元の情報としてお客様の会社名と住所の情報を記入します。
5. 次のメール テンプレートを使用して、Quantium Solutions にメールを送信します。

    * 日本郵便の着払伝票が含まれていなかった場合、または紛失した場合は、このメールにそのことを記載します。 Quantium Solutions Japan が日本郵便に集荷を依頼し、集荷時に伝票を持って行くように伝えます。
    * 複数の注文がある場合は、必ず個別に集荷するようにメールを送信します。

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body:
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

6. 集荷を予約したら、Quantium Solutions から確認メールが届きます。 確認のメールには、着払伝票に関する情報も含まれています。

必要に応じて、次の情報で Quantium Solutions のサポート (日本語) に問い合わせることができます。

* メール: Customerservice.JP@quantiumsolutions.com 
* 電話: 03-5755-0150 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する
 
Azure へのアップロードが完了すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。

::: zone-end

## <a name="singapore"></a>[シンガポール](#tab/in-singapore)

1. デバイスを返送する際に使用する元の箱を保管しておきます。
2. 追跡番号 (Data Box のローカル Web UI の **[発送準備]** ページに参照番号として表示されます) をメモしておきます。 追跡番号は、**発送準備** の手順が正常に完了した後に提供されます。 このページから配送先住所ラベルをダウンロードし、梱包箱に貼り付けます。
3. デバイスの電源をオフにして、ケーブルを取り外します。
4. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。 
5. 次の電子メール テンプレートと追跡番号を使用して、SingPost のカスタマー サービスに電子メールを送信します。

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > 営業日に受信した予約の取り扱い:
   > * 午後 3 時より前に受信した場合、集荷は翌営業日の午前 9 時から午後 1 時の間になります。
   > * 午後 3 時より後に受信した場合、集荷は翌営業日の午後 2 時から午後 6 時の間になります。  

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する

Azure へのアップロードが完了すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。

::: zone-end

## <a name="south-africa"></a>[南アフリカ](#tab/in-sa)

1. 返送するデバイスを元の箱に梱包します。
2. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
3. 追跡番号 (Data Box のローカル Web UI の **[発送準備]** ページに参照番号として表示されます) をメモしておきます。 追跡番号は、"発送準備" の手順が正常に完了した後に提供されます。 このページから配送先住所ラベルをダウンロードし、梱包箱に貼り付けます。
4. Azure Data Box Operations に返送コードを要求します。 パッケージをデータセンターに返送するには、返送コードが必要です。 [adbops@microsoft.com](mailto:adbops@microsoft.com) に電子メールを送信します。 配送先住所ラベルの返送先住所の横に、はっきりと見えるようにこのコードを記載します。
5. 次のいずれかの方法を使用して、DHL で集荷を予約します。
 
   * [DHL Express South Africa **の「** Schedule a Pickup (集荷をスケジュールする)](https://mydhl.express.dhl/za/en/schedule-pickup.html#/schedule-pickup#label-reference)」にアクセスして、オンラインで集荷を予約します。
   * 次のテンプレートを使用して、電子メールを [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) に送信します。

     ```output
     To: Priority.Support@dhl.com
     Subject: Pickup request for Microsoft Azure
     Body: Need pick up for the below shipment
       *  DHL tracking number: (reference number/waybill number)
       *  Requested pickup date: yyyy/mm/dd;time:HH MM
       *  Shipper contact: (company name)
       *  Contact person: 
       *  Phone number: 
       *  Full physical address: 
       *  Item to be collected: Azure Dt
     ```

    * または、最寄りの DHL サービス ポイントにパッケージを持ち込みます。

6. 問題が発生した場合は、問題の詳細を記載した電子メールを [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) に送ります。件名には貨物運送状番号を含めます。 +27(0)119213902 に電話することもできます。

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する

Azure へのアップロードが完了すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。

::: zone-end

## <a name="hong-kong"></a>[香港特別行政区](#tab/in-hk)

1. 返送するデバイスを元の箱に梱包します。
2. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
3. 営業時間中 (月曜日から金曜日の午前 9 時から午後 6 時まで) に、**Quantium Solutions** ホットライン ( **(852) 2318 1213**) に電話をかけます。  
4. "Microsoft Azure の集荷" であることと、返送ラベルの参照番号および追跡番号 (バー コードの上) を伝え、集荷を手配します。
5. 集荷スケジュールが口頭で確認されます。 配送業者が集荷に来ない場合は、Quantium Solutions ホットラインに別の手配を依頼します。
6. Quantium Solutions に集荷を予約したら、次のテンプレートを使用して、確定内容を [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) に伝えます。

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium Solutions.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

問題が発生した場合は、問題の詳細を記載した電子メールを Data Box Operations Asia ([adbo@microsoft.com](mailto:adbo@microsoft.com)) に送ります。件名にはジョブ名を含めます。

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する
 
Azure へのアップロードが完了すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。

::: zone-end

<!--## [In Korea](#tab/in-korea) 

1. Retain the original box used to ship the device for return shipment.
2. Note down the tracking number (shown as reference number on the **Prepare to Ship** page of the Data Box local web UI). The tracking number is available after the **Prepare to ship** step successfully completes. Download the shipping label from this page and paste on the packing box. 
3. Power off the device and remove the cables.
4. Spool and securely place the power cord that was provided with the device in the back of the device. 

Request pickup  
If consignment note is present:  

1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number to arrange for a collection.
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.  
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.  
4. You will receive an email confirmation for the pickup schedule.  

Exception process
If the consignment note is not present:
1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number. Specify that you need a new consignment note to arrange for a collection. Provide sender (customer), receiver information (Azure datacenter), and reference number (service request number).
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.
4. You get a verbal confirmation if request is made via telephone.  
::: zone target="chromeless"

## Verify data upload to Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## Erasure of data from Data Box
 
Once the upload to Azure is complete, the Data Box erases the data on its disks as per the [NIST SP 800-88 Revision 1 guidelines](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end
-->

## <a name="self-managed"></a>[自己管理型](#tab/in-selfmanaged)

米国政府、日本、シンガポール、韓国、インド、南アフリカ、イギリス、西ヨーロッパ、オーストラリアで Data Box を使用しており、注文の作成時に自己管理の出荷を選択した場合は、次の手順に従います。

1. この手順が正常に完了したら、Data Box のローカル Web UI の **[発送準備]** ページに表示される承認コードをメモしておきます。
2. デバイスの電源をオフにして、ケーブルを取り外します。 デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
3. デバイスを返送する準備が整ったら、次のテンプレートを使用して Azure Data Box Operations チームにメールを送信します。

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box drop-off for order: 'orderName'
    Body:
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する
 
Azure へのアップロードが完了すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。

::: zone-end

---

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

