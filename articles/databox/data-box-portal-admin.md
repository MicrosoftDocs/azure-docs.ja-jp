---
title: Azure Data Box ポータル管理ガイド | Microsoft Docs
description: Azure portal を使用して Azure Data Box を管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 483e1e0f6a472132446b20a161d012e69d83c4ac
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607111"
---
# <a name="use-the-azure-portal-to-administer-your-data-box"></a>Azure portal を使用して Data Box を管理する

この記事では、Data Box で実行できる一部の複雑なワークフローと管理タスクについて説明します。 Data Box の管理は、Azure portal またはローカル Web UI から行うことができます。 

この記事では、Azure portal を使用して実行できるタスクについて説明します。 Azure portal を使用して、注文を管理したり、Data Box を管理したり、完了まで注文の状態を追跡したりします。


## <a name="cancel-an-order"></a>注文のキャンセル

発注後、さまざまな理由から注文をキャンセルしなければならないことがあります。 キャンセルできるのは、処理される前の注文だけです。 注文が処理されて Data Box が準備された後は、注文をキャンセルできません。 

注文をキャンセルするには、次の手順を実行します。

1.  **[概要] > [キャンセル]** に移動します。 

    ![注文のキャンセル 1](media/data-box-portal-admin/cancel-order1.png)

2.  注文のキャンセル理由を入力します。  

    ![注文のキャンセル 2](media/data-box-portal-admin/cancel-order2.png)

3.  注文のキャンセル後、ポータルで注文の状態が更新され、**取り消し済み**として表示されます。 

## <a name="clone-an-order"></a>注文の複製

特定の状況では複製機能が役立ちます。 たとえば、ユーザーが Data Box を使って何らかのデータを転送済みだとします。 その後、生成されるデータが増えていくと、それを Azure に転送するために別の Data Box が必要になります。 このようなケースでは、同じ注文をそのまま複製することができます。

注文を複製するには、次の手順を実行します。

1.  **[概要] > [複製]** に移動します。 

    ![注文の複製 1](media/data-box-portal-admin/clone-order1.png)

2.  注文の詳細はすべてそのままの状態で維持されます。 複製された注文は、元の名前の最後に "*-Clone*" を追加した名前になります。 プライバシー情報を確認済みであることへの同意として、チェック ボックスをオンにします。 **Create** をクリックしてください。

複製は数分で作成され、ポータルに新しい注文が反映されます。


## <a name="delete-order"></a>注文の削除

注文が完了した時点で、その注文を削除したい場合があります。 注文には、お客様の名前、住所、連絡先情報など、個人情報が含まれています。 この個人情報は、注文が削除されると削除されます。

削除できるのは、完了済みまたはキャンセル済みの注文だけです。 注文を削除するには次の手順を実行します。

1. **[すべてのリソース]** に移動します。 目的の注文を検索します。

2. 削除する注文をクリックし、**[概要]** に移動します。 コマンド バーの **[削除]** をクリックします。

    ![Data Box 注文の削除 1](media/data-box-portal-admin/delete-order1.png)

3. 注文の削除を確認するメッセージが表示されたら注文の名前を入力します。 **[削除]** をクリックします。

## <a name="download-shipping-label"></a>配送先住所ラベルのダウンロード

Data Box の E インク表示が動作しておらず、返送ラベルが表示されない場合は、配送先住所ラベルをダウンロードしなければならないことがあります。 

配送先住所ラベルをダウンロードするには、次の手順を実行します。

1.  **[概要] > [出荷ラベルをダウンロード]** に移動します。 このオプションは、デバイスの発送後にのみ選択できます。 

    ![配送先住所ラベルのダウンロード](media/data-box-portal-admin/download-shipping-label.png)

2.  次の返送ラベルがダウンロードされます。 ラベルを保存し、印刷します。ラベルを折りたたみ、デバイスのクリア カバーに挿入します。 ラベルが見えることを確認します。 以前の出荷で使用したステッカーをデバイスからはがします。

    ![配送先住所ラベルの例](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>配送先住所の編集

発注後に配送先住所の編集が必要になることも考えられます。 編集できるのは、デバイスが発送されるまでとなります。 デバイスの発送後は、このオプションは使用できなくなります。

注文を編集するには、次の手順を実行します。

1. **[注文の詳細] > [配送先住所の編集]** に移動します。

    ![配送先住所の編集 1](media/data-box-portal-admin/edit-shipping-address1.png)

2. 配送先住所を編集して検証し、変更を保存します。

    ![配送先住所の編集 2](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>通知の詳細の編集

注文の状態を知らせるメールの宛先となるユーザーを変更しなければならないことがあります。 たとえばデバイスが発送または集荷されたときに、あるユーザーに連絡する必要があるとしましょう。 一方、データのコピーが完了したときの連絡先は、別のユーザーにする必要があります。Azure Storage アカウントにデータが存在することを別のユーザーが確認したうえで、コピー元からデータを削除するためです。 このような場合は、通知の詳細を編集することができます。

通知の詳細を編集するには、次の手順を実行します。

1. **[注文の詳細] > [通知の詳細の編集]** に移動します。

    ![通知の詳細の編集 1](media/data-box-portal-admin/edit-notification-details1.png)

2. これで通知の詳細を編集し、変更を保存できるようになります。
 
    ![通知の詳細の編集 2](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="download-order-history"></a>注文履歴のダウンロード

Data Box の注文が完了すると、デバイス ディスク上のデータは消去されます。 デバイスのクリーンアップが完了したら、Azure portal で注文履歴をダウンロードできます。

注文履歴をダウンロードするには、次の手順を実行します。

1. Data Box の注文で、**[概要]** に移動します。 注文が完了していることを確認します。 注文が完了し、デバイスのクリーンアップが完了している場合は、**[注文の詳細]** に移動します。 **[注文履歴のダウンロード]** オプションが使用可能になっています。

    ![注文履歴のダウンロード](media/data-box-portal-admin/download-order-history-1.png)

2. **[注文履歴のダウンロード]** をクリックします。 ダウンロードした履歴には、運送業者の追跡ログの記録が含まれています。 このログの一番下までスクロールすると、次の項目へのリンクが表示されます。
    
   - **コピー ログ** - Data Box から Azure Storage アカウントへのデータのコピー中にエラーが出力されたファイルのリストが含まれています。
   - **監査ログ** - Data Box が Azure データセンターの外部にあるときの Data Box の電源投入と共有へのアクセスに関する情報が含まれています。
   - **BOM ファイル** - **発送の準備**中にダウンロードでき、ファイル名、ファイル サイズ、およびファイル チェックサムを持つファイルの一覧 (ファイル マニフェストとも呼ばれます) が含まれています。

       ```
       -------------------------------
       Microsoft Data Box Order Report
       -------------------------------
       Name                                               : DataBoxTestOrder                              
       StartTime(UTC)                                     : 10/31/2018 8:49:23 AM +00:00                       
       DeviceType                                         : DataBox                                           
       -------------------
       Data Box Activities
       -------------------
       Time(UTC)                 | Activity                       | Status          | Description  
       
       10/31/2018 8:49:26 AM     | OrderCreated                   | Completed       |                                                   
       11/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |                                                   
       11/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 11/3/2018 1:36:43        PM at AMSTERDAM-NLD                                                                                
       11/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 11/4/2018        8:23:30 PM at AMSTERDAM-NLD                                                                        
       11/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time :          11/4/2018 11:43:34 PM at AMSTERDAM-NLD                                                               
       11/5/2018 1:38:20 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LEIPZIG-DEU. Local Time :        11/5/2018 1:38:20 AM at LEIPZIG-DEU                                                                
       11/5/2018 2:31:07 AM      | ShippingToCustomer             | InProgress      | Processed at LEIPZIG-DEU. Local Time : 11/5/2018          2:31:07 AM at LEIPZIG-DEU                                                                            
       11/5/2018 4:05:58 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LEIPZIG-DEU. Local Time :            11/5/2018 4:05:58 AM at LEIPZIG-DEU                                                                    
       11/5/2018 4:35:43 AM      | ShippingToCustomer             | InProgress      | Transferred through LUTON-GBR. Local Time :              11/5/2018 4:35:43 AM at LUTON-GBR                                                                         
       11/5/2018 4:52:15 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LUTON-GBR. Local Time :              11/5/2018 4:52:15 AM at LUTON-GBR                                                                        
       11/5/2018 5:47:58 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 10/5/2018 5:47:58 AM at LONDON-HEATHROW-GBR                                                
       11/5/2018 6:27:37 AM      | ShippingToCustomer             | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            11/5/2018 6:27:37 AM at LONDON-HEATHROW-GBR                                                            
       11/5/2018 6:39:40 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 11/5/2018 6:39:40 AM at LONDON-HEATHROW-GBR                                                    
       11/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in LAMBETH-GBR. Local        Time : 11/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
       11/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 11/5/2018            9:13:24 AM at LAMBETH-GBR                                                                               
       11/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 11/5/2018        12:03:04 PM at LAMBETH-GBR                                                                          
       1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25        PM at LAMBETH-GBR                                                                                       
       1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at LAMBETH-GBR. Local Time : 1/25/2019          8:03:55 PM at LAMBETH-GBR                                                                            
       1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in LAMBETH-GBR. Local Time :            1/25/2019 8:04:58 PM at LAMBETH-GBR                                                                    
       1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
       1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
       1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR                                                   
       1/26/2019 2:17:10 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility BRUSSELS-BEL. Local Time        : 1/26/2019 2:17:10 PM at BRUSSELS-BEL                                                              
       1/26/2019 2:31:57 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        2:31:57 PM at BRUSSELS-BEL                                                                          
       1/26/2019 3:37:53 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        3:37:53 PM at BRUSSELS-BEL                                                                          
       1/27/2019 11:01:45 AM     | ShippingToDataCenter           | InProgress      | Departed Facility in BRUSSELS-BEL. Local Time :          1/27/2019 11:01:45 AM at BRUSSELS-BEL                                                                 
       1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD.            Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
       1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019            9:07:57 AM at AMSTERDAM-NLD                                                                             
       1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019            1:35:56 PM at AMSTERDAM-NLD                                                                            
       1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019        2:57:48 PM at AMSTERDAM-NLD                                                                         
       1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |                                              
       1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully                  
       1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.                               
       1/29/2019 4:55:00 PM      | DataCopy                       | Started         |                                                 
       2/2/2019 7:07:34 PM       | DataCopy                       | Completed       | Copy Completed.                                   
       2/4/2019 7:47:32 PM       | SecureErase                    | Started         |                                                  
       2/4/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:DEVICESERIALNO has been sanitized          according to NIST 800-88 Rev 1.                                                                       

       ------------------
       Data Box Log Links
       ------------------

       Account Name         : Gus                                                       
       Copy Logs Path       : databoxcopylog/DataBoxTestOrder_CHC533180024_CopyLog_73a81b2d613547a28ecb7b1612fe93ca.xml
       Audit Logs Path      : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024
       BOM Files Path       : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024      
       ```
     次に、ストレージ アカウントに移動し、コピー ログを表示できます。

![ストレージ アカウント内のログ](media/data-box-portal-admin/logs-in-storage-acct-2.png)

また、監査ログと BOM ファイルを含む生産物流管理ログを表示することもできます。

![ストレージ アカウント内のログ](media/data-box-portal-admin/logs-in-storage-acct-1.png)

## <a name="view-order-status"></a>注文の状態の確認

ポータル内でデバイスの状態が変化すると、メールで通知されます。

|注文の状態 |説明 |
|---------|---------|
|注文済み     | 正常に発注されました。 <br>デバイスが使用可能な場合、Microsoft は出荷用のデバイスを特定し、デバイスを準備します。 <br> デバイスがただちに入手できない場合、デバイスが入手可能になったときに注文が処理されます。 注文の処理は数日から 2 ～ 3 か月かかることがあります。 注文を 90 日以内に満たすことができない場合、注文が取り消されて通知されます。         |
|処理済み     | 注文処理は完了しています。 注文に応じて、デバイスを出荷する準備がデータ センター内で行われます。         |
|出荷しました     | 注文は発送済みです。 出荷を追跡するには、ポータルの注文に表示されている追跡 ID を使用します。        |
|配信済み     | 出荷は、注文に指定された住所に配達済みです。        |
|集荷されました     |返送する荷物が運送業者によって集荷されてスキャンされました。         |
|受取済み     | デバイスは Azure データ センターで受け取られてスキャンされます。 <br> 出荷が検査されると、デバイスのアップロードが開始されます。      |
|データのコピー     | データのコピーが進行中です。 Azure portal で、注文のコピーの進行状況を追跡します。 <br> データのコピーが完了するまでお待ちください。 |
|完了       |注文が正常に完了しました。<br> オンプレミスのデータをサーバーから削除する前に、データが Azure に存在することを確認してください。         |
|完了 (エラーあり)| データのコピーが完了しましたが、コピー中にエラーが発生しました。 <br> Azure portal に示されているパスを使用してコピー ログを確認してください。   |
|Canceled            |注文はキャンセルされました。 <br> お客様によって注文がキャンセルされたか、またはエラーが発生したために、サービスによって注文がキャンセルされました。 注文を 90 日以内に満たすことができない場合も、注文が取り消されて通知されます。     |
|クリーンアップ | デバイスディスク上のデータは消去されます。 デバイスのクリーンアップは、注文履歴が Azure portal でダウンロード可能なときに完了したと見なされます。|



## <a name="next-steps"></a>次の手順

- [Data Box の問題のトラブルシューティング](data-box-faq.md)に関するページを参照してください。
