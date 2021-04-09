---
title: エクスポート注文での Azure Data Box の返送に関するチュートリアル | Microsoft Docs
description: エクスポート注文の完了後に Azure Data Box を Microsoft に送付する方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/03/2021
ms.author: alkohli
ms.openlocfilehash: 6c2eed9aac614b0765582d5cf373caf50323ef7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99537359"
---
# <a name="tutorial-return-azure-data-box"></a>チュートリアル:Azure Data Box を返送する

このチュートリアルでは、Azure Data Box を返送する方法について説明します。デバイスが Azure データに届くと、データは消去されます。

このチュートリアルでは、次のようなトピックについて説明します。

> [!div class="checklist"]
>
> * 前提条件
> * 発送の準備をする
> * Data Box を Microsoft に送付する
> * Data Box からデータを消去する

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

* 「[チュートリアル: Azure Data Box からデータをコピーする](data-box-deploy-export-copy-data.md)」を完了していること。
* コピージョブが完了していること。 コピージョブが進行中の場合、発送準備を行うことはできません。

## <a name="prepare-to-ship"></a>発送の準備をする

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

次の手順は、デバイスを返送する場所によって決まります。

## <a name="ship-data-box-back"></a>Data Box を返送する

デバイスからのデータ コピーを確実に完了しさせ、 **[発送の準備をする]** の実行を正常終了させます。 デバイスを発送する地域によって手順が異なります。

## <a name="us-canada-europe"></a>[米国、カナダ、ヨーロッパ](#tab/in-us-canada-europe)

米国、カナダ、またはヨーロッパでデバイスを返送する場合は、次の手順を行います。

1. 必ずデバイスの電源をオフにてケーブルを取り外します。 
2. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
3. 配送先住所ラベルが電子インク ディスプレイに表示されていることを確認し、運送業者による集荷をスケジュールします。 ラベルを破損または紛失した場合や、ラベルが電子インク ディスプレイに表示されない場合は、Microsoft サポートにお問い合わせください。 サポートから指示された場合は、Azure portal の **[概要] > [出荷ラベルをダウンロード]** にアクセスしてください。 配送先住所ラベルをダウンロードしてデバイスに貼り付けます。 
4. デバイスを返送する場合は、UPS での集荷をスケジュールします。 集荷のスケジュールを設定するには:

   - 最寄りの UPS (国/地域固有のフリー ダイヤル) に連絡します。
   - 電話で、電子インク ディスプレイまたは印刷ラベルに表示されている返送追跡番号を伝えます。 追跡番号を伝えないと、集荷時に UPS から追加料金が請求されます。
   - 集荷のスケジュール中に問題が発生した場合や、追加料金の支払いを求められた場合は、Azure Data Box Operations にお問い合わせください。 [adbops@microsoft.com](mailto:adbops@microsoft.com) に電子メールを送信します。

    集荷のスケジュールを設定する代わりに、最寄りの持ち込み場所に Data Box を持ち込むこともできます。
4. 運送業者によって Data Box が集荷され、スキャンされると、ポータルの注文状態は "**集荷されました**" に更新されます。 追跡 ID も表示されます。

## <a name="australia"></a>[オーストラリア](#tab/in-australia)

オーストラリアの Azure データセンターには、追加のセキュリティ通知があります。 すべての国内配送には事前通知が必要です。 オーストラリアで発送するには、次の手順を行います。

1. デバイスを返送する際に使用する元の箱を保管しておきます。
2. デバイスへのデータ コピーが完了し、 **[発送準備]** の実行が正常終了することを確認します。
3. デバイスの電源をオフにして、ケーブルを取り外します。
4. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
5. [DHL のリンク](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference)からオンラインで集荷を予約します。

## <a name="japan"></a>[日本](#tab/in-japan)

1. デバイスを返送する際に使用する元の箱を保管しておきます。
2. デバイスの電源をオフにして、ケーブルを取り外します。
3. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
4. 伝票に送信元の情報としてお客様の会社名と住所の情報を記入します。
5. 次のメール テンプレートを使用して Quantium Solutions にメールを送信します。

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

6. 集荷を予約した後、Quantium Solutions からメールの確認を受信します。 確認のメールには、着払伝票に関する情報も含まれています。

必要に応じて、次の情報で Quantium Solutions のサポート (日本語) にお問い合わせください。 

* メール: Customerservice.JP@quantiumsolutions.com
* 電話: 03-5755-0150

## <a name="singapore"></a>[シンガポール](#tab/in-singapore)

1. デバイスを返送する際に使用する元の箱を保管しておきます。
2. 追跡番号 (Data Box のローカル Web UI の [発送準備] ページに参照番号として表示されます) をメモしておきます。 これは、発送準備の手順が正常に完了した後に確認できるようになります。 このページから配送先住所ラベルをダウンロードし、梱包箱に貼り付けます。
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
   >
   > * 午後 3 時より前に受信した場合、集荷は翌営業日の午前 9 時から午後 1 時の間になります。
   > * 午後 3 時より後に受信した場合、集荷は翌営業日の午後 2 時から午後 6 時の間になります。  

## <a name="south-africa"></a>[南アフリカ](#tab/in-sa)

1. 返送の際にデバイスを梱包するために使用する元の箱を保管しておきます。
2. デバイスのローカル Web UI に表示されている参照番号 (貨物運送状番号) を書き留めておきます。 この数値は、 **[発送準備] の実行** が成功した後に表示されます。
3. デバイスのローカル Web UI で入手できる配送先住所ラベルをダウンロードして印刷し、出荷パッケージに貼り付けます。
4. DHL での集荷を予約するには、以下のいずれかのオプションを選択します。

    * 午後 2:00 前にカスタマー サービスのコンタクト センター ( **+27(0) 11 9213600**) に電話し、オプション 1 を選択して、貨物運送状番号を指定します。
    * 次のテンプレートを使用して、電子メールを [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) に送信します。

    ```output
    To: Priority.Support@dhl.com
    Subject: Pickup request for Microsoft Azure
    Body: Need pick up for the below shipment
      *  DHL tracking number (reference number/waybill number)
      *  Requested pickup date: yyyy/mm/dd;time:HH MM
    ```

    * 代わりに、最寄りの DHL サービス ポイントにパッケージを持ち込むこともできます。

5. 問題が発生した場合は、発生した問題の詳細を記載した電子メールを [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) に送ります。件名には貨物運送状番号を含めます。 +27(0)119213902 に電話することもできます。

## <a name="hong-kong"></a>[香港特別行政区](#tab/in-hk)

1. 返送するデバイスを元の箱に梱包します。
2. デバイスのローカル Web UI に表示されている参照番号 (返品用の追跡番号) を書き留めておきます。 この数値は、 **[発送準備] の実行** が成功した後に表示されます。
3. デバイスのローカル Web UI で入手できる配送先住所ラベルをダウンロードして印刷し、出荷パッケージに貼り付けます。
4. デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
5. **Quantium Solutions** ホットライン ( **(852) 2318 1213**) に、営業時間中 (月曜日から金曜日の午前 9 時から午後 6 時まで) に電話をかけます。  
6. "Microsoft Azure の集荷" であることと、返送ラベルの参照番号および追跡番号 (バー コードの上) を伝え、集荷を手配します。
7. 口頭で集荷スケジュールが確認されます。 配送業者が集荷に来ない場合は、Quantium Solutions ホットラインに別の手配を依頼します。
8. Quantium に集荷を予約したら、次のテンプレートを使用して、確定内容を [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) にも連絡します。

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

問題が発生した場合は、Data Box Operations Asia ([adbo@microsoft.com](mailto:adbo@microsoft.com)) にメールで連絡します。件名ヘッダーにジョブ名を指定し、発生した問題を記載します。

## <a name="united-arab-emirates"></a>[アラブ首長国連邦](#tab/in-uae)

1. デバイスを返送する際に使用する元の箱を保管しておきます。
2. デバイスへのデータ コピーが完了し、 **[発送準備]** の手順が正常に完了したことを確認します。
3. デバイスのローカル Web UI の **[発送準備]** ページにある参照番号をメモしておきます。
4. デバイスの電源をオフにし、ケーブルを取り外します。 デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
6. 返送するデバイスを元の箱に梱包します。
7. [Azure Data Box Operations](mailto:adbops@microsoft.com) にメールを送って、荷物がデータセンターに届いたときに荷物の識別に使用する ID を入手します。
8. 印刷された配送先住所ラベルの返送先住所の横に、はっきりと見えるようにこの ID を記載します。  
9. [DHL Express UAE](https://mydhl.express.dhl/ae/en/home.html#/schedulePickupTab) > 「**Schedule a Pickup (集荷をスケジュールする)** 」にアクセスして、オンラインで集荷を予約します。
   - デバイスのローカル Web UI の **[発送準備]** ページで確認した参照番号を貨物運送状番号フィールドに入力します。
   - 予約は、週 6 日 (金曜日と祝日を除く)、午前 9 時から午後 2 時まで受け付けています。
   - 集荷依頼は、お客様の終業時間の少なくとも 90 分前に行ってください。
10. DHL 予約ツールで問題が発生した場合は、次のいずれかの方法を使用して DHL に連絡できます。
    - 04-2924545 に電話します。
    - 問題の詳細を記載したメールを [ecom.ae@dhl.com](mailto:ecom.ae@dhl.com) に送ります。件名には貨物運送状番号を含めます。
    - DHL カスタマー サポート (600 567567) に電話します。

## <a name="self-managed"></a>[自己管理型](#tab/in-selfmanaged)

日本、シンガポール、韓国、インド、南アフリカ、英国、西ヨーロッパ、またはオーストラリアで Data Box を使用していて、注文の作成時に自己管理型の発送オプションを選択した場合は、次の手順に従います。

1. この手順が正常に完了したら、Data Box のローカル Web UI の [発送準備] ページに表示される承認コードをメモしておきます。
2. デバイスの電源をオフにして、ケーブルを取り外します。 デバイスに付属していた電源ケーブルは巻き取り、デバイスの背面に固定します。
3. デバイスを返送する準備が整ったら、次のテンプレートを使用して Azure Data Box Operations チームにメールを送信します。
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

---

## <a name="erasure-of-data-from-data-box"></a>Data Box からデータを消去する

デバイスが Azure データセンターに到着すると、Data Box は [NIST SP 800-88 Revision 1 のガイドライン](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)に従ってディスク上のデータを消去します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のようなトピックを学習しました。

> [!div class="checklist"]
> * 前提条件
> * 発送の準備をする
> * Data Box を Microsoft に送付する
> * Data Box からデータを消去する

次の記事に進んで、Data Box の管理方法を学習してください。

> [!div class="nextstepaction"]
> [Azure portal で Data Box を管理する](./data-box-portal-admin.md)
