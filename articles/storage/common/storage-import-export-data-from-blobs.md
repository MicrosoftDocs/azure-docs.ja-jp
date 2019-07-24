---
title: Azure Import/Export を使用して Azure BLOB からデータをエクスポートする | Microsoft Docs
description: Azure portal でエクスポート ジョブを作成して、Azure BLOB からデータを転送する方法について説明します。
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: fc02e830953f8612a077fb219c7fef4e86bc3827
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263837"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Azure Import/Export サービスを使用して Azure Blob Storage からデータをエクスポートする
この記事では、Azure Import/Export サービスを使用して大量のデータを Azure Blob Storage から安全にエクスポートする手順について説明します。 このサービスを利用するには、Azure データセンターに空のドライブを送付する必要があります。 このサービスでは、ストレージ アカウントからドライブにデータをエクスポートし、ドライブを返送します。

## <a name="prerequisites"></a>前提条件

Azure Blob Storage からデータを転送するエクスポート ジョブを作成する前に、このサービスの次の前提条件の一覧を慎重に確認してください。 前提条件は、以下のとおりです。

- Import/Export サービスに使用できるアクティブな Azure サブスクリプションがある。
- Azure Storage アカウントが少なくとも 1 つある。 [Import/Export サービスでサポートしているストレージ アカウントとストレージの種類](storage-import-export-requirements.md)の一覧を参照してください。 新しいストレージ アカウントの作成については、「 [ストレージ アカウントの作成方法](storage-quickstart-create-account.md)」を参照してください。
- 十分な数の[サポートされている種類](storage-import-export-requirements.md#supported-disks)のディスクがある。
- FedEx または DHL のアカウントを用意します。 FedEx または DHL 以外の運送業者を使用する場合、`adbops@microsoft.com` に Azure Data Box Operations チームまでお問い合わせください。 
    - アカウントは、有効で、残高があり、差出人住所の機能を持っている必要があります。
    - エクスポート ジョブの追跡番号を生成します。
    - すべてのジョブに個別の追跡番号が必要です。 同じ追跡番号を持つ複数のジョブはサポートされていません。 
    - 運送業者アカウントがいない場合、次に移動します。
        - [FedEX アカウントを作成するか](https://www.fedex.com/en-us/create-account.html)、または 
        - [DHL アカウントを作成します](http://www.dhl-usa.com/en/express/shipping/open_account.html)。

## <a name="step-1-create-an-export-job"></a>手順 1:エクスポート ジョブの作成

以下の手順を実行して、Azure portal でエクスポート ジョブを作成します。

1. https://portal.azure.com/ にログオンします。
2. **[すべてのサービス] > [ストレージ] > [インポート/エクスポート ジョブ]** の順に移動します。 

    ![インポート/エクスポートへの移動](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. **[インポート/エクスポート ジョブの作成]** をクリックします。

    ![[インポート/エクスポート ジョブ] をクリックします](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. **[基本]** で次のようにします。
    
    - **[Azure からエクスポート]** を選択します。 
    - エクスポート ジョブのわかりやすい名前を入力します。 選択した名前は、ジョブの進行状況の追跡に使用します。 
        - この名前には、小文字のアルファベット、数字、ハイフン、アンダースコアのみを使用できます。
        - 名前はアルファベットから始める必要があります。スペースを含めることはできません。 
    - サブスクリプションを選択します。
    - リソース グループを入力または選択します。

        ![基本](./media/storage-import-export-data-from-blobs/export-from-blob3.png) 
    
3. **[ジョブの詳細]** で次の操作を実行します。

    - エクスポートするデータが存在するストレージ アカウントを選択します。 所在地に近いストレージ アカウントを使用します。
    - 配送場所は、選んだストレージ アカウントのリージョンに基づいて自動的に入力されます。 
    - ストレージ アカウントから空のドライブにエクスポートする BLOB データを指定します。 
    - ストレージ アカウントの BLOB データについて **[すべてをエクスポートする]** を選択します。
    
         ![[すべてをエクスポートする]](./media/storage-import-export-data-from-blobs/export-from-blob4.png) 

    - エクスポートするコンテナーと BLOB を指定できます。
        - **エクスポートする BLOB を指定するには**:**[等しい]** セレクターを使用します。 コンテナー名から始まる BLOB への相対パスを指定します。 ルート コンテナーを指定するには、" *$root* " を使用します。
        - **プレフィックスで始まるすべての BLOB を指定するには**:**[指定値で始まる]** セレクターを使用します。 スラッシュ "/" で始まるプレフィックスを指定します。 プレフィックスには、コンテナー名のプレフィックス、コンテナー名全体、またはコンテナー名全体の後に BLOB 名のプレフィックスを付けた名前を指定できます。 次のスクリーンショットで示すように、処理中にエラーが発生しないように、有効な形式で BLOB のパスを指定する必要があります。 詳細については、「[有効な BLOB パスの例](#examples-of-valid-blob-paths)」を参照してください。 
   
           ![選択したコンテナーと BLOB をエクスポートする](./media/storage-import-export-data-from-blobs/export-from-blob5.png) 

    - BLOB リスト ファイルからエクスポートすることができます。

        ![BLOB リスト ファイルからエクスポートする](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  
   
   > [!NOTE]
   > データ コピー時にエクスポートする BLOB が使用中の場合、Azure Import/Export サービスは BLOB のスナップショットを作成し、スナップショットをコピーします。
 

4. **[差出人住所の詳細]** で次の操作を実行します。

    - ドロップダウン リストから運送業者を選択します。 FedEx または DHL 以外の運送業者を使用する場合は、ドロップダウンから既存のオプションを選びます。 `adbops@microsoft.com` で Azure Data Box Operations チームに使用する予定の運送業者に関する情報を連絡してください。
    - その運送業者で作成した有効な運送業者アカウント番号を入力します。 Microsoft は、インポート ジョブの完了後、このアカウントを使ってドライブを返送します。 
    - 完全かつ有効な連絡先の名前、電話番号、電子メール、番地、市区町村、郵便番号、都道府県、国/地域を指定します。

        > [!TIP] 
        > 1 人のユーザーの電子メール アドレスを指定する代わりに、グループ メール アドレスを提供します。 これにより、管理者が離れる場合でも、通知を受信します。
   
5. **[概要]** で次の操作を実行します。

    - ジョブの詳細を確認します。
    - ジョブ名と、Azure にディスクを送付するために使用する Azure データセンターの送付先住所をメモします。 

        > [!NOTE] 
        > ディスクは常に、Azure Portal に示されているデータ センターに送付します。 誤って別のデータ センターにディスクが発送された場合、ジョブは処理されません。

    - **[OK]** をクリックして、エクスポート ジョブの作成を完了します。

## <a name="step-2-ship-the-drives"></a>手順 2:ドライブを送付する

必要なドライブ数がわからない場合は、「[ドライブ数を確認する](#check-the-number-of-drives)」を参照してください。 ドライブの数がわかっている場合は、ドライブの送付に進みます。

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>手順 3:追跡情報を使用してジョブを更新する

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>手順 4:ディスクを受け取る
ダッシュボードでジョブの完了が報告されると、ディスクが返送され、ポータルで配送の追跡番号を利用できるようになります。

1. エクスポートされたデータが保存されたドライブを受け取ったら、BitLocker キーを取得してドライブのロックを解除する必要があります。 Azure portal でエクスポート ジョブに移動します。 **[インポート/エクスポート]** タブをクリックします。 
2. リストからエクスポート ジョブを選択してクリックします。 **BitLocker キー**に移動し、キーをコピーします。
   
   ![エクスポート ジョブの BitLocker キーの表示](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. BitLocker キーを使用してディスクのロックを解除します。

これでエクスポートは完了です。 この時点でジョブを削除するか、90 日後に自動的に削除することができます。


## <a name="check-the-number-of-drives"></a>ドライブ数を確認する

この*省略可能*な手順は、エクスポート ジョブに必要なドライブ数を決定するために役立ちます。 [サポートされている OS バージョン](storage-import-export-requirements.md#supported-operating-systems)を実行している Windows システム上でこの手順を実行します。

1. Windows システムに [WAImportExport バージョン 1 をダウンロード](https://aka.ms/waiev1)します。 
2. 既定のフォルダー `waimportexportv1` に解凍します。 たとえば、「 `C:\WaImportExportV1` 」のように入力します。
3. 管理者特権を使用して PowerShell またはコマンド ライン ウィンドウを開きます。 解凍されたフォルダーにディレクトリを変更するには、次のコマンドを実行します。
    
    `cd C:\WaImportExportV1`

4. 選択した BLOB に必要なディスク数を確認するには、次のコマンドを実行します。

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    パラメーターについては次の表で説明します。
    
    |コマンド ライン パラメーター|説明|  
    |--------------------------|-----------------|  
    |**/logdir:**|省略可能。 ログ ディレクトリ。 詳細ログ ファイルは、このディレクトリに書き込まれます。 指定されていない場合、現在のディレクトリがログ ディレクトリとして使用されます。|  
    |**/sn:**|必須。 エクスポート ジョブのストレージ アカウント名です。|  
    |**/sk:**|コンテナー SAS が指定されていない場合のみ必須。 エクスポート ジョブのストレージ アカウントのアカウント キーです。|  
    |**/csas:**|ストレージ アカウント キーが指定されていない場合のみ必須。 エクスポート ジョブでエクスポートされる BLOB を一覧表示するためのコンテナー SAS です。|  
    |**/ExportBlobListFile:**|必須。 エクスポートする BLOB の BLOB パスや BLOB パスのプレフィックスの一覧を含む XML ファイルへのパス。 インポート/エクスポート サービス REST API の [Put Job](/rest/api/storageimportexport/jobs) 操作の `BlobListBlobPath` 要素で使用されるファイル形式です。|  
    |**/DriveSize:**|必須。 エクスポート ジョブに使用するドライブのサイズ (*例:* 500 GB、1.5 TB)。|  

    「[PreviewExport コマンドの例](#example-of-previewexport-command)」を参照してください。
 
5. エクスポート ジョブ用に送付するドライブの読み取り/書き込みが可能であることを確認します。

### <a name="example-of-previewexport-command"></a>PreviewExport コマンドの例

次の例は `PreviewExport` コマンドを示しています。  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
エクスポート BLOB 一覧ファイルには、次のように BLOB 名や BLOB のプレフィックスが含まれる場合があります。  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Azure Import/Export ツールでは、エクスポートするすべての BLOB を一覧表示し、必要なオーバーヘッドを考慮に入れて指定したサイズのドライブにそれらを詰め込む方法を計算し、BLOB とドライブの使用状況の情報を保持するために必要なドライブの数を推定します。  
  
情報ログを省略した出力の例を次に示します。  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```

## <a name="examples-of-valid-blob-paths"></a>有効な BLOB パスの例

次の表は、有効な BLOB のパスの例を示しています。
   
   | セレクター | BLOB パス | 説明 |
   | --- | --- | --- |
   | [指定値で始まる] |/ |ストレージ アカウントのすべての BLOB をエクスポートする |
   | [指定値で始まる] |/$root/ |ルート コンテナー内のすべての BLOB をエクスポートする |
   | [指定値で始まる] |/book |プレフィックス " **book** |
   | [指定値で始まる] |/music/ |コンテナー " **music** |
   | [指定値で始まる] |/music/love |コンテナー "**music**" 内の、プレフィックス "**love**" で始まるすべての BLOB をエクスポートする |
   | [等しい] |$root/logo.bmp |ルート コンテナー内の BLOB " **logo.bmp** " をエクスポートする |
   | [等しい] |videos/story.mp4 |コンテナー "**videos**" 内の BLOB "**story.mp4**" をエクスポートする |

## <a name="next-steps"></a>次の手順

* [ジョブとドライブの状態の表示](storage-import-export-view-drive-status.md)
* [Import/Export の要件の確認](storage-import-export-requirements.md)


