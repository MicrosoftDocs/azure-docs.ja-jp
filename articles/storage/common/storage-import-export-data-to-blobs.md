---
title: Azure Import/Export を使用した Azure Blob へのデータの転送 | Microsoft Docs
description: Azure portal でインポート ジョブとエクスポート ジョブを作成し、Azure BLOB との間でデータを転送する方法について説明します。
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/06/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 72a91fefc26e9c0b6d5a91223119815c4fcb9551
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808589"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Azure Import/Export サービスを使用して Azure Blob Storage にデータをインポートする

この記事では、Azure Import/Export サービスを使用して大量のデータを Azure Blob Storage に安全にインポートする手順について説明します。 Azure BLOB にデータをインポートするには、データが保存されている暗号化されたディスク ドライブを Azure データセンターに送付する必要があります。  

## <a name="prerequisites"></a>前提条件

Azure Blob Storage にデータを転送するインポート ジョブを作成する前に、このサービスの次の前提条件の一覧を慎重に確認してください。 前提条件は、以下のとおりです。

- Import/Export サービスに使用できるアクティブな Azure サブスクリプションがある。
- ストレージ コンテナーがある Azure ストレージ アカウントを少なくとも 1 つは持っている。 [Import/Export サービスでサポートしているストレージ アカウントとストレージの種類](storage-import-export-requirements.md)の一覧を参照してください。 
    - 新しいストレージ アカウントの作成については、「 [ストレージ アカウントの作成方法](storage-quickstart-create-account.md)」を参照してください。 
    - ストレージ コンテナーについては、[ストレージ コンテナーの作成](../blobs/storage-quickstart-blobs-portal.md#create-a-container)に関するセクションを参照してください。
- 十分な数の[サポートされている種類](storage-import-export-requirements.md#supported-disks)のディスクがある。 
- [サポートされている OS バージョン](storage-import-export-requirements.md#supported-operating-systems)を実行している Windows システムがある。 
- Windows システムで BitLocker を有効にする。 [BitLocker を有効にする方法](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)に関するページを参照してください。
- Windows システムに [WAImportExport バージョン 1 をダウンロード](https://aka.ms/waiev1)します。 既定のフォルダー `waimportexportv1` に解凍します。 たとえば、「 `C:\WaImportExportV1` 」のように入力します。
- FedEx または DHL のアカウントを用意します。 FedEx または DHL 以外の運送業者を使用する場合、`adbops@microsoft.com` から Azure Data Box Operations チームまでお問い合わせください。  
    - アカウントは、有効で、残高があり、差出人住所の機能を持っている必要があります。
    - エクスポート ジョブの追跡番号を生成します。
    - すべてのジョブに個別の追跡番号が必要です。 同じ追跡番号を持つ複数のジョブはサポートされていません。
    - 運送業者アカウントがいない場合、次に移動します。
        - [FedEX アカウントを作成するか](https://www.fedex.com/en-us/create-account.html)、または 
        - [DHL アカウントを作成します](http://www.dhl-usa.com/en/express/shipping/open_account.html)。

## <a name="step-1-prepare-the-drives"></a>手順 1:ドライブを準備する

この手順では、ジャーナル ファイルを生成します。 ジャーナル ファイルには、ドライブのシリアル番号、暗号化キー、ストレージ アカウントの詳細などの基本情報が保存されます。 

次の手順を実行して、ドライブを準備します。

1.  ディスク ドライブを SATA コネクタを介して Windows システムに接続します。
1.  各ドライブに 1 つの NTFS ボリュームを作成します。 ボリュームにドライブ文字を割り当てます。 マウントポイントを使用しないでください。
2.  NTFS ボリュームの BitLocker 暗号化を有効にします。 Windows Server システムを使用している場合は、[Windows Server 2012 R2 で BitLocker を有効にする方法](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)に関するページを参照してください。
3.  暗号化されたボリュームにデータをコピーします。 ドラッグ アンド ドロップや、Robocopy などのコピーツールを使用します。
4.  管理者特権を使用して PowerShell またはコマンド ライン ウィンドウを開きます。 解凍されたフォルダーにディレクトリを変更するには、次のコマンドを実行します。
    
    `cd C:\WaImportExportV1`
5.  ドライブの BitLocker キーを取得するには、次のコマンドを実行します。
    
    `manage-bde -protectors -get <DriveLetter>:`
6.  ディスクを準備するには、次のコマンドを実行します。 **データ サイズによっては、数時間から数日かかることがあります。** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite 
    ```
    ジャーナル ファイルは、ツールを実行したフォルダーと同じフォルダーに作成されます。 *.xml* ファイル (ツールを実行するフォルダー) と *drive-manifest.xml* ファイル (データが保存されているフォルダー) の 2 つのファイルも作成されます。
    
    使用されるパラメーターについては、次の表で説明します。

    |オプション  |説明  |
    |---------|---------|
    |/j:     |ジャーナル ファイルの名前 (拡張子は .jrn)。 ジャーナル ファイルはドライブごとに 1 つ生成されます。 ジャーナル ファイル名としてディスクのシリアル番号を使用することをお勧めします。         |
    |/id:     |セッション ID。 コマンドの各インスタンスに一意のセッション番号を使用します。      |
    |/t:     |送付するディスクのドライブ文字。 例: ドライブ `D`。         |
    |/bk:     |ドライブの BitLocker キー。 `manage-bde -protectors -get D:` の出力からの数値パスワードです      |
    |/srcdir:     |送付するディスクのドライブ文字の末尾に `:\` を付けます。 たとえば、「 `D:\` 」のように入力します。         |
    |/dstdir:     |Azure Storage 内の保存先コンテナーの名前。         |
    |/blobtype:     |このオプションは、データをインポートする BLOB の種類を指定します。 これは、ブロック BLOB の場合は `BlockBlob` で、ページ BLOB の場合は `PagaBlob` です。         |
    |/skipwrite:     |コピーする必要がある新しいデータがなく、ディスク上の既存のデータを準備する必要があることを指定するオプション。          |
    |/enablecontentmd5:     |有効にすると、このオプションにより、MD5 が計算されて、各 BLOB で `Content-md5` プロパティとして設定されます。 このオプションは、データが Azure にアップロードされた後に、`Content-md5` フィールドを使用する場合にのみ使用してください。 <br> このオプションはデータ整合性チェック (既定で実行) には影響しません。 この設定により、クラウドにデータをアップロードするためにかかる時間が長くなります。          |
7. 送付する必要があるディスクごとに前の手順を繰り返します。 コマンド行の実行ごとに、指定された名前のジャーナル ファイルが作成されます。
    
    > [!IMPORTANT]
    > - ツールが存在するフォルダーと同じフォルダーに、ジャーナル ファイルと共に `<Journal file name>_DriveInfo_<Drive serial ID>.xml` ファイルも作成されます。 ジャーナル ファイルが大きすぎる場合、ジョブの作成時にジャーナル ファイルではなく .xml ファイルが使用されます。 

## <a name="step-2-create-an-import-job"></a>手順 2:インポート ジョブの作成

次の手順を実行して、Azure portal でインポート ジョブを作成します。

1. [https://portal.azure.com/](https://portal.azure.com/ ) にログオンします。
2. **[すべてのサービス] > [ストレージ] > [インポート/エクスポート ジョブ]** の順に移動します。 
    
    ![インポート/エクスポートへの移動](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. **[インポート/エクスポート ジョブの作成]** をクリックします。

    ![[インポート/エクスポート ジョブの作成] をクリックします](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. **[基本]** で次のようにします。

   - **[Azure へインポート]** を選択します。
   - インポート ジョブのわかりやすい名前を入力します。 この名前は、ジョブの進行状況の追跡に使用します。
       - 名前には小文字、数字、ハイフンのみを含めることができます。
       - 名前はアルファベットから始める必要があります。スペースを含めることはできません。
   - サブスクリプションを選択します。
   - リソース グループを入力または選択します。  

     ![インポート ジョブを作成する - 手順 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. **[ジョブの詳細]** で次の操作を実行します。

    - ドライブの準備中に取得したドライブのジャーナル ファイルをアップロードします。 `waimportexport.exe version1` を使用した場合、準備したドライブごとに 1 つのファイルをアップロードします。 ジャーナル ファイルのサイズが 2 MB を超える場合は、ジャーナル ファイルと共に作成された `<Journal file name>_DriveInfo_<Drive serial ID>.xml` を使用することもできます。 
    - データの保存先ストレージ アカウントを選択します。 
    - 配送場所は、選んだストレージ アカウントのリージョンに基づいて自動的に入力されます。
   
   ![インポート ジョブを作成する - 手順 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. **[差出人住所の詳細]** で次の操作を実行します。

   - ドロップダウン リストから運送業者を選択します。 FedEx または DHL 以外の運送業者を使用する場合は、ドロップダウンから既存のオプションを選びます。 `adbops@microsoft.com` で Azure Data Box Operations チームに使用する予定の運送業者に関する情報を連絡してください。
   - その運送業者で作成した有効な運送業者アカウント番号を入力します。 Microsoft は、インポート ジョブの完了後、このアカウントを使ってドライブを返送します。 アカウント番号をお持ちでない場合は、[FedEx](https://www.fedex.com/us/oadr/) または [DHL](https://www.dhl.com/) の運送業者アカウントを作成してください。
   - 完全かつ有効な連絡先の名前、電話番号、電子メール、番地、市区町村、郵便番号、都道府県、国/地域を指定します。 
        
       > [!TIP] 
       > 1 人のユーザーの電子メール アドレスを指定する代わりに、グループ メール アドレスを提供します。 これにより、管理者が離れる場合でも、通知を受信します。

     ![インポート ジョブの作成 - ステップ 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. **[概要]** で次の操作を実行します。

   - 概要に表示されているジョブ情報を確認します。 ジョブ名と、Azure にディスクを送付するために使用する Azure データセンターの送付先住所をメモします。 この情報は、後で配送先住所ラベルに使用します。
   - **[OK]** をクリックしてインポート ジョブを作成します。

     ![インポート ジョブを作成する - 手順 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>手順 3:ドライブを送付する 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>手順 4:追跡情報を使用してジョブを更新する

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>手順 5:Azure へのデータのアップロードを確認する

完了するまでジョブを監視します。 ジョブが完了したら、データが Azure にアップロードされたことを確認します。 アップロードが成功したことを確認した後にのみ、オンプレミスのデータを削除します。

## <a name="next-steps"></a>次の手順

* [ジョブとドライブの状態の表示](storage-import-export-view-drive-status.md)
* [Import/Export の要件の確認](storage-import-export-requirements.md)


