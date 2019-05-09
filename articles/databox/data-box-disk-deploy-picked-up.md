---
title: Microsoft Azure Data Box Disk の返送| Microsoft Docs
description: このチュートリアルでは、Azure Data Box Disk を Microsoft に返送する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 7c62ec722e5bb909c13b21a7b98d86dd72d9e665
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59425952"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>チュートリアル:Azure Data Box Disk の返送と Azure へのデータ アップロードの確認

これは、Azure Data Box Disk のデプロイについて取り上げたシリーズの最後のチュートリアルです。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Data Box Disk を Microsoft に発送する
> * Azure へのデータのアップロードを確認する
> * Data Box Disk からデータを消去する

## <a name="prerequisites"></a>前提条件

開始する前に、「[チュートリアル:Azure Data Box Disk へのデータのコピーと検証](data-box-disk-deploy-copy-data.md)」を参照してください。

## <a name="ship-data-box-disk-back"></a>Data Box Disk を返送する

1. データの検証が完了したら、ディスクの接続を解除します。 接続ケーブルを取り外してください。
2. すべてのディスクと接続ケーブルをエアー クッションで包んで梱包箱に詰めます。 アクセサリが不足している場合は、料金がかかることがあります。
    - 最初の配送時に使われていた梱包を再利用してください。  
    - ディスクはエアー クッションでしっかりと包んで梱包することをお勧めします。
    - 箱内の物があまり動かないように、すき間が少なくなるようにしてください。

次の手順は、デバイスを返送する場所によって決まります。

### <a name="pick-up-in-us-canada"></a>米国、カナダで集荷する

米国またはカナダでデバイスを返送するには、次の手順を実行します。

1. 梱包箱に貼り付けられている透明のビニール袋に入った返送ラベルを使用します。 ラベルを破損または紛失した場合:
    - **[概要] > [出荷ラベルをダウンロード]** に移動します。

        ![配送先住所ラベルのダウンロード](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        この操作により、次に示した返送ラベルがダウンロードされます。

        ![配送先住所ラベルの例](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - デバイスにラベルを貼り付けます。

2. 梱包箱を封印し、返送ラベルが見えることを確認します。
3. UPS で集荷のスケジュールを設定します。


### <a name="pick-up-in-europe"></a>ヨーロッパで集荷する

ヨーロッパでデバイスを返送するには、次の手順を実行します。

1. 梱包箱に貼り付けられている透明のビニール袋に入った返送ラベルを使用します。 ラベルを破損または紛失した場合:
    - **[概要] > [出荷ラベルをダウンロード]** に移動します。

        ![配送先住所ラベルのダウンロード](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        この操作により、次に示した返送ラベルがダウンロードされます。

        ![配送先住所ラベルの例](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - デバイスにラベルを貼り付けます。

2. 梱包箱を封印し、返送ラベルが見えることを確認します。
3. ヨーロッパで DHL を使ってデバイスを返送する場合は、DHL の Web サイトにアクセスし、航空貨物運送状番号を指定して、DHL に集荷を依頼します。
4. 該当する国の DHL Express の Web サイトにアクセスし、**[Book a Courier Collection]\(宅配便の予約\) > [eReturn Shipment]\(電子返送\)** を選択します。

    ![DHL 電子返送](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. 貨物運送状番号を指定し、**[Schedule Pickup]\(集荷のスケジュール\)** をクリックして集荷の手配を行います。

      ![集荷のスケジュール](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>アジア太平洋地域で集荷する

この地域には、日本、韓国、オーストラリア向けの手順があります。

#### <a name="pick-up-in-australia"></a>オーストラリアで集荷する

オーストラリアの Azure データセンターには、追加のセキュリティ通知があります。 すべての国内配送には事前通知が必要です。 オーストラリアで集荷する場合は、次の手順を実行します。

1. `adbops@microsoft.com` にメールを送信し、一意の国内配送用 ID、つまり TAU コードが記載された配送先住所ラベルを依頼します。 ラベルを目的の日付に入手するには、配送予定日の少なくとも 3 日前に依頼します。
2. メールの件名は、「*Request for reverse shipping label with TAU code (TAU コードが記載された返送用の配送先住所ラベルの依頼)*」にすることをお勧めします。 メールには、必ず次の詳細情報を含めてください。 

    - 注文の名前
    - Address
    - 連絡先の名前

#### <a name="pick-up-in-japan"></a>日本で集荷する

1. 必ず返送用の日本郵便の着払伝票を同封してください。
2. 伝票に送信元の情報としてお客様の会社名と住所の情報を記入します。
3. 日本郵便に集荷依頼の電話をかけます (フリー ダイヤルで 0800-0800-111)。 集荷する住所の 7 桁の郵便番号をダイヤルすると、最寄りの郵便局に転送されます。
    - 集荷を利用できる時間は、郵便局によって異なります。
    - 日本郵便の着払ゆうパックを利用した配送であることを知らせます。
    - 同封されていた着払伝票を使用します。
4. 日本郵便の着払伝票が同封されていなかった場合は、*Quantium Solutions* (`Customerservice.JP@quantiumsolutions.com`) にメールを送信してください。 *Quantium Solutions* が日本郵便に集荷を依頼し、集荷時に伝票を持って行くように伝えます。
    - 日本郵便が持ってくる着払伝票の備考欄に参照番号を記入します。
    - 次のように、配送先住所を入力します。   
        ```
        3F N7 Prologis Park Tokyo Ohta, 1-3-6 Tokai Ohta-ku, Tokyo 143-0001
        Microsoft Service Center c/o Quantium Solutions Japan
        TEL: 03-5755-0150
        ```

着払伝票が見つからない場合は、メールで集荷を依頼できます。 次のメール テンプレートを使用して集荷を依頼してください。

```
To: Customerservice.JP@quantiumsolutions.com
Subject: Pickup request for Azure Data Box Disk｜Job Name： 
Body: 
- Azure Data Box Disk job name：
- Reference number:  
- Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
```

#### <a name="pick-up-in-korea"></a>韓国で集荷する

1. 必ず返品用の伝票を同封してください。
2. 集荷を依頼するには:
    1. 営業時間中 (月曜日から金曜日の午前 10 時から午後 5 時) に *Quantium Solutions International* ホットライン (070-8231-1418) に電話をかけます。 *Microsoft の集荷*であることと伝票番号を伝え、集荷を手配します。  
    2. ホットラインにつながらない場合は、`microsoft@rocketparcel.com` にメールを送信します。メールの件名に「*Microsoft Pickup (Microsoft の集荷)*」、参照として伝票番号を入力します。
    3. 配送業者が集荷に来ない場合は、*Quantium Solutions International* ホットラインに別の手配を依頼します。 

## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

運送業者によってディスクが集荷されると、ポータルの注文状態が "**集荷されました**" に更新されます。 追跡 ID も表示されます。

![ディスクが集荷済み](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Microsoft がディスクを受け取ってスキャンすると、ジョブの状態が "**受取済み**" に更新されます。 

![ディスク受取済み](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Azure データセンター内のサーバーにディスクが接続されると、データが自動的にコピーされます。 データのサイズによっては、コピー処理が完了するまでに数時間から数日かかる場合があります。 コピー ジョブの進行状況は、ポータルで監視することができます。

コピーが完了すると、注文の状態が "**完了**" に更新されます。

![データのコピーが完了](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

コピー元からデータを削除する前に、データがストレージ アカウントに存在することを確認します。 データは次の場所にあります。

- お使いの Azure Storage アカウント。 データを Data Box にコピーする場合は、そのデータがタイプに応じて Azure Storage アカウントの次のいずれかのパスにアップロードされます。

  - ブロック BLOB およびページ BLOB の場合: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files の場合: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    あるいは、Azure Portal で Azure ストレージ アカウントにアクセスし、そこから移動することもできます。

- マネージド ディスク リソース グループ。 マネージド ディスクを作成するとき、VHD はページ BLOB としてアップロードされた後、マネージド ディスクに変換されます。 マネージド ディスクは、注文の作成時に指定されたリソース グループに接続されています。

  - Azure のマネージド ディスクへのコピーが正常に完了した場合は、Azure portal の **[注文の詳細]** に移動して、マネージド ディスクに指定されているリソース グループをメモすることができます。

      ![[注文の詳細] を表示する](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    メモしたリソース グループに移動し、目的のマネージド ディスクを見つけます。

      ![マネージド ディスク用のリソース グループ](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - VHDX または動的/差分 VHD をコピーした場合、VHDX/VHD はブロック BLOB としてステージング ストレージ アカウントにアップロードされます。 ステージング環境の **[ストレージ アカウント] > [BLOB]** に移動し、適切なコンテナー (StandardSSD、StandardHDD、または PremiumSSD) を選択します。 VHDX/VHD は、ステージング ストレージ アカウントにブロック BLOB として表示されます。

Azure にデータがアップロードされたことを確認するには、次の手順を実行します。

1. ディスクの注文に関連付けられているストレージ アカウントに移動します。
2. **[Blob service] > [BLOB の参照]** に移動します。 コンテナーの一覧が表示されます。 *BlockBlob* フォルダーと *PageBlob* フォルダーに作成したサブフォルダーに対応して、同じ名前のコンテナーがご利用のストレージ アカウントに作成されます。
    Azure の名前付け規則にフォルダー名が準拠していない場合、Azure へのデータのアップロードに失敗します。

4. データセット全体が読み込み済みであることを確認するには、Microsoft Azure Storage Explorer を使用します。 ディスク レンタル注文に対応するストレージ アカウントをアタッチし、BLOB コンテナーの一覧に注目します。 いずれかのコンテナーを選択し、**[その他]** をクリックして **[Folder statistics]\(フォルダーの統計情報\)** をクリックします。 **[アクティビティ]** ウィンドウに、そのフォルダーの統計情報 (BLOB 数、合計 BLOB サイズなど) が表示されます。 合計 BLOB サイズ (バイト単位) がデータセットのサイズと一致している必要があります。

    ![フォルダーの統計情報を Storage Explorer で表示](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Data Box Disk からデータを消去する

コピーが完了し、Azure Storage アカウントにデータが存在することをお客様が確認した後、ディスクは NIST 標準に従って確実に消去されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box Disk に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * Data Box Disk を Microsoft に発送する
> * Azure へのデータのアップロードを確認する
> * Data Box Disk からデータを消去する


次の記事に進み、Azure portal で Data Box Disk を管理する方法について学習してください。

> [!div class="nextstepaction"]
> [Azure portal を使用して Azure Data Box Disk を管理する](./data-box-portal-ui-admin.md)


