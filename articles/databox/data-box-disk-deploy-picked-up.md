---
title: Azure Data Box Disk の返送に関するチュートリアル | Microsoft Docs
description: このチュートリアルでは、Azure Data Box Disk を Microsoft に返送する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 9e3159a083d21e4a7a81df2fcf7a7d5db882b757
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976913"
---
::: zone target="docs"

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
3. UPS で集荷のスケジュールを設定します。 集荷のスケジュールを設定するには:

    - 最寄りの UPS (国/地域固有のフリー ダイヤル) に連絡します。
    - 電話で、印刷ラベルに表示されている返送追跡番号を伝えます。
    - 追跡番号を伝えないと、集荷時に UPS から追加料金が請求されます。
    - 集荷のスケジュールを設定する代わりに、最寄りの持ち込み場所に Data Box Disk を持ち込むこともできます。


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
4. 該当する国/地域の DHL Express の Web サイトにアクセスし、 **[Book a Courier Collection]\(宅配便の予約\) > [eReturn Shipment]\(電子返送\)** の順に選択します。

    ![DHL 電子返送](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. 貨物運送状番号を指定し、 **[Schedule Pickup]\(集荷のスケジュール\)** をクリックして集荷の手配を行います。

      ![集荷のスケジュール](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>アジア太平洋地域で集荷する

この地域には、日本、韓国、オーストラリア、シンガポール向けの手順があります。

#### <a name="pick-up-in-australia"></a>オーストラリアで集荷する

オーストラリアの Azure データセンターには、追加のセキュリティ通知があります。 すべての国内配送には事前通知が必要です。 オーストラリアで集荷する場合は、次の手順を実行します。

1. `adbops@microsoft.com` にメールを送信し、一意の国内配送用 ID、つまり TAU コードが記載された配送先住所ラベルを依頼します。 ラベルを目的の日付に入手するには、配送予定日の少なくとも 3 日前に依頼します。
2. メールの件名は、「*Request for reverse shipping label with TAU code (TAU コードが記載された返送用の配送先住所ラベルの依頼)* 」にすることをお勧めします。 メールには、必ず次の詳細情報を含めてください。 

    - 注文の名前
    - Address
    - 連絡先の名前

#### <a name="pick-up-in-japan"></a>日本で集荷する

1. 伝票に送信元の情報としてお客様の会社名と住所の情報を記入します。
2. 次のメール テンプレートを使用して Quantium Solutions にメールを送信します。

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```
    - **大阪で集荷する場合**は、電子メール テンプレートの件名を `Pickup request for Microsoft Azure OSA` に変更します。
    - 日本郵便の着払伝票が含まれていなかった場合、または紛失した場合は、このメールにそのことを記載します。 Quantium Solutions Japan が日本郵便に集荷を依頼し、集荷時に伝票を持って行くように伝えます。
    - 複数の注文がある場合は、必ず個別に集荷するようにメールを送信します。

3. 集荷を予約した後、Quantium Solutions からメールの確認を受信します。 確認のメールには、着払伝票に関する情報も含まれています。

必要に応じて、次の情報で Quantium Solutions のサポート (日本語) にお問い合わせください。 

- メール: Customerservice.JP@quantiumsolutions.com 
- 電話： 03-5755-0150 

#### <a name="pick-up-in-korea"></a>韓国で集荷する

1. 必ず返品用の伝票を同封してください。
2. 伝票があるときに集荷を依頼するには:
    1. 営業時間中 (月曜日から金曜日の午前 10 時から午後 5 時) に *Quantium Solutions International* ホットライン (070-8231-1418) に電話をかけます。 "*Microsoft Azure の集荷*" であることとサポート リクエスト番号を伝え、集荷を手配します。  
    2. ホットラインにつながらない場合は、`microsoft@rocketparcel.com` にメールを送信します。メールの件名に「*Microsoft Azure Pickup (Microsoft Azure の集荷)* 」、参照としてサポート リクエスト番号を入力します。
    3. 配送業者が集荷に来ない場合は、*Quantium Solutions International* ホットラインに別の手配を依頼します。 
    4. 集荷スケジュールの確認メールが届きます。
3. 伝票がない場合にのみ、この手順を実行してください。 集荷を依頼するには:
    1. 営業時間中 (月曜日から金曜日の午前 10 時から午後 5 時) に *Quantium Solutions International* ホットライン (070-8231-1418) に電話をかけます。 "*Microsoft Azure の集荷*" であることとサポート リクエスト番号を伝え、集荷を手配します。 集荷を手配するには新しい伝票が必要であることを指定します。 送付元 (お客様)、受取先の情報 (Azure データセンター)、および参照番号 (サービス リクエスト番号) を指定します。 
    2. ホットラインにつながらない場合は、`microsoft@rocketparcel.com` にメールを送信します。メールの件名に「*Microsoft Azure Pickup (Microsoft Azure の集荷)* 」、参照としてサポート リクエスト番号を入力します。
    3. 配送業者が集荷に来ない場合は、*Quantium Solutions International* ホットラインに別の手配を依頼します。 
    4. 電話でのリクエストの場合は、口頭で確認を受け取ります。

### <a name="pick-up-in-singapore"></a>シンガポールでの集荷

1. 配送先住所ラベルを印刷し、箱に貼り付けます。 ラベルを破損または紛失した場合:
    - **[概要] > [出荷ラベルをダウンロード]** に移動します。

        ![配送先住所ラベルのダウンロード](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        この操作により、次に示した返送ラベルがダウンロードされます。

        ![配送先住所ラベルの例](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - デバイスにラベルを貼り付けます。 ラベルが見えることを確認します。

2. 集荷を依頼するには:
    - **SingPost** ホットラインには、営業時間内 (月曜日から金曜日の午前 9 時から午後 5 時まで) に **6845 6485** まで電話をかけます。  
    - "*Microsoft Azure の集荷*" であることとサポート リクエスト番号 (返送用配送先住所ラベルには追跡番号) を伝え、集荷を手配します。 
    - 口頭で集荷スケジュールが確認されます。 
    - 配送業者が集荷に来ない場合は、**SingPost** (**6845 6485**) に別の手配を依頼します。 
3. 配送業者に渡します。 


## <a name="verify-data-upload-to-azure"></a>Azure へのデータのアップロードを確認する

運送業者によってディスクが集荷されると、ポータルの注文状態が "**集荷されました**" に更新されます。 追跡 ID も表示されます。

![ディスクが集荷済み](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Microsoft がディスクを受け取ってスキャンすると、ジョブの状態が "**受取済み**" に更新されます。 

![ディスク受取済み](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Azure データセンター内のサーバーにディスクが接続されると、データが自動的にコピーされます。 データのサイズによっては、コピー処理が完了するまでに数時間から数日かかる場合があります。 コピー ジョブの進行状況は、ポータルで監視することができます。

コピーが完了すると、注文の状態が "**完了**" に更新されます。

![データのコピーが完了](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

コピーがエラーで完了した場合は、[アップロード エラーのトラブルシューティング](data-box-disk-troubleshoot-upload.md)に関するページを参照してください。

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

4. データセット全体が読み込み済みであることを確認するには、Microsoft Azure Storage Explorer を使用します。 ディスク レンタル注文に対応するストレージ アカウントをアタッチし、BLOB コンテナーの一覧に注目します。 いずれかのコンテナーを選択し、 **[その他]** をクリックして **[Folder statistics]\(フォルダーの統計情報\)** をクリックします。 **[アクティビティ]** ウィンドウに、そのフォルダーの統計情報 (BLOB 数、合計 BLOB サイズなど) が表示されます。 合計 BLOB サイズ (バイト単位) がデータセットのサイズと一致している必要があります。

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

::: zone-end


::: zone target="chromeless"

# <a name="return-the-disk-and-verify-upload-to-azure"></a>Azure へのディスクの返却とアップロードの確認

## <a name="ship-to-azure"></a>Azure への発送

1. データの検証が完了したら、ディスクの接続を解除します。 接続ケーブルを取り外してください。
2. すべてのディスクと接続ケーブルをエアー クッションで包んで梱包箱に詰めます。 アクセサリが不足している場合は、料金がかかることがあります。
    - 最初の配送時に使われていた梱包を再利用してください。  
    - ディスクはエアー クッションでしっかりと包んで梱包することをお勧めします。
    - 箱内の物があまり動かないように、すき間が少なくなるようにしてください。
3. 次の手順は、デバイスを返送する場所によって決まります。
    - [米国およびカナダでデバイスを返送する場合は、UPS での集荷をスケジュールします](data-box-disk-deploy-picked-up.md#pick-up-in-us-canada)。
    - [ヨーロッパで DHL での集荷をスケジュールする](data-box-disk-deploy-picked-up.md#pick-up-in-europe)場合は、DHL の Web サイトにアクセスし、航空貨物運送状番号を指定します。
    - オーストラリア、日本、韓国、シンガポールなどの[オーストラリア太平洋地域の国で集荷をスケジュール](data-box-disk-deploy-picked-up.md#pick-up-in-asia-pacific-region)します。
4. 運送業者によってディスクが集荷されると、ポータルの注文状態が更新され、追跡 ID が表示されます。

## <a name="verify-upload-to-azure"></a>Azure へのアップロードを確認する

データが Azure にアップロードされた後、コピー元からデータを削除する前に、データがストレージ アカウントに存在することを確認します。 データは次の場所にあります。

- お使いの Azure Storage アカウント。 データを Data Box にコピーする場合は、そのデータがタイプに応じて Azure Storage アカウントの次のいずれかのパスにアップロードされます。

    - **ブロック BLOB とページ BLOB の場合**: https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt

    - **Azure Files の場合**: https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt

    あるいは、Azure Portal で Azure ストレージ アカウントにアクセスし、そこから移動することもできます。

- マネージド ディスク リソース グループ。 マネージド ディスクを作成するとき、VHD はページ BLOB としてアップロードされた後、マネージド ディスクに変換されます。 マネージド ディスクは、注文の作成時に指定されたリソース グループに接続されています。

::: zone-end


