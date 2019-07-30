---
title: Azure Import/Export を使用した Azure Files へのデータの転送 | Microsoft Docs
description: Azure portal でインポート ジョブを作成し、データを Azure Files に転送する方法について説明します。
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 28026a429643c62434ddfd7591126169857a7371
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61479074"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Azure Import/Export サービスを使用してデータを Azure Files にインポートする

この記事では、Azure Import/Export サービスを使用して大量のデータを Azure Files に安全にインポートする手順を説明します。 このサービスを使用してデータをインポートするには、データが含まれているサポート対象のディスク ドライブを Azure データセンターに送付する必要があります。  

Import/Export サービスでは、Azure Files の Azure Storage へのインポートのみをサポートしています。 Azure Files のエクスポートはサポートしていません。

## <a name="prerequisites"></a>前提条件

インポート ジョブを作成して Azure Files にデータを転送する前に、次の前提条件の一覧を慎重に確認し、すべてを満たしてください。 前提条件は、以下のとおりです。

- Import/Export サービスで使用するアクティブな Azure サブスクリプションがある。
- Azure Storage アカウントが少なくとも 1 つある。 [Import/Export サービスでサポートしているストレージ アカウントとストレージの種類](storage-import-export-requirements.md)の一覧を参照してください。 新しいストレージ アカウントの作成については、「 [ストレージ アカウントの作成方法](storage-quickstart-create-account.md)」を参照してください。
- 十分な数の[サポートされている種類](storage-import-export-requirements.md#supported-disks)のディスクがある。 
- [サポートされている OS バージョン](storage-import-export-requirements.md#supported-operating-systems)を実行している Windows システムがある。
- Windows システムで [WAImportExport バージョン 2 をダウンロード](https://aka.ms/waiev2)してください。 既定のフォルダー `waimportexport` に解凍します。 たとえば、「 `C:\WaImportExport` 」のように入力します。
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

1. ディスク ドライブを SATA コネクタを介して Windows システムに接続します。
2. 各ドライブに 1 つの NTFS ボリュームを作成します。 ボリュームにドライブ文字を割り当てます。 マウントポイントを使用しないでください。
3. ツールが配置されているルート フォルダーにある *dataset.csv* ファイルを変更します。 インポート対象がファイルとフォルダーのどちらか一方か両方かに応じて、次の例のようなエントリを *dataset.csv* ファイルに追加します。  

   - **ファイルをインポートするには**:次の例では、コピーするデータは C: ドライブにあります。 ファイル *MyFile1.txt* は *MyAzureFileshare1* のルートにコピーされます。 *MyAzureFileshare1* は、存在しない場合、Azure Storage アカウントに作成されます。 フォルダー構造は維持されます。

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
       ```
   - **フォルダーをインポートするには**:*MyFolder2* の下にあるすべてのファイルとフォルダーがファイル共有に再帰的にコピーされます。 フォルダー構造は維持されます。

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
       ```
     インポートするフォルダーまたはファイルに対応する複数のエントリを同じファイル内に作成できます。 

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
                        
       ```
     詳しくは、[データセット CSV ファイルの準備](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file)に関する記事をご覧ください。
    

4. ツールが配置されているルート フォルダーにある *driveset.csv* ファイルを変更します。 次の例のようなエントリを *driveset.csv* ファイルに追加します。 ドライブセット ファイルには、ツールが準備するディスクの一覧を正しく選択できるように、ディスクとそれに対応するドライブ文字の一覧が含まれています。

    この例では、2 つのディスクが接続され、ベーシック NTFS ボリューム G:\ と H:\ が作成されていることを前提としています。 H:\ は暗号化されていませんが、G:\ は既に暗号化されています。 このツールでは、(G:\) ではなく) H:\ のみをホストしているディスクをフォーマットし、暗号化します。

   - **暗号化されていないディスクの場合**:*Encrypt* を指定して、ディスクの BitLocker 暗号化を有効にします。

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```
    
   - **既に暗号化されているディスクの場合**:*AlreadyEncrypted* を指定し、BitLocker キーを指定します。

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     複数のドライブに対応する複数のエントリを同じファイル内に作成できます。 詳しくは、[ドライブセット CSV ファイルの準備](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file)に関する記事をご覧ください。 

5. `PrepImport` オプションを使用して、ディスク ドライブにデータをコピーして準備します。 最初のコピー セッションで新しいコピー セッションを使用してディレクトリやファイルをコピーするために、次のコマンドを実行します。

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   インポートの例を以下に示します。
  
       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```
 
6. コマンド行を実行するたびに、`/j:` パラメーターで指定した名前のジャーナル ファイルが作成されます。 準備した各ドライブには、インポート ジョブを作成するときにアップロードする必要があるジャーナル ファイルがあります。 ジャーナル ファイルのないドライブは処理されません。

    > [!IMPORTANT]
    > - ディスクの準備が完了したら、ディスク ドライブ上のデータやジャーナル ファイルを変更しないでください。

その他のサンプルについては、「[ジャーナル ファイルのサンプル](#samples-for-journal-files)」を参照してください。

## <a name="step-2-create-an-import-job"></a>手順 2:インポート ジョブの作成 

次の手順を実行して、Azure portal でインポート ジョブを作成します。
1. [https://portal.azure.com/ ](https://portal.azure.com/) にログオンします。
2. **[すべてのサービス] > [ストレージ] > [インポート/エクスポート ジョブ]** の順に移動します。 

    ![インポート/エクスポートへの移動](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. **[インポート/エクスポート ジョブの作成]** をクリックします。

    ![[インポート/エクスポート ジョブ] をクリックします](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. **[基本]** で次のようにします。

    - **[Azure へインポート]** を選択します。
    - インポート ジョブのわかりやすい名前を入力します。 この名前を使って、ジョブの進行中および完了後にジョブを追跡します。
        -  この名前には、小文字、数字、ハイフン、アンダースコアのみを使用できます。
        -  名前はアルファベットから始める必要があります。スペースを含めることはできません。 
    - サブスクリプションを選択します。
    - リソース グループを選択します。 

        ![インポート ジョブの作成 - ステップ 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. **[ジョブの詳細]** で次の操作を実行します。
    
    - 上記の「[手順 1:ドライブを準備する](#step-1-prepare-the-drives)」で作成したジャーナル ファイルをアップロードします。 
    - データのインポート先となるストレージ アカウントを選択します。 
    - 配送場所は、選んだストレージ アカウントのリージョンに基づいて自動的に入力されます。
   
       ![インポート ジョブを作成する - 手順 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. **[差出人住所の詳細]** で次の操作を実行します。

    - ドロップダウン リストから運送業者を選択します。 FedEx または DHL 以外の運送業者を使用する場合は、ドロップダウンから既存のオプションを選びます。 `adbops@microsoft.com` で Azure Data Box Operations チームに使用する予定の運送業者に関する情報を連絡してください。
    - その運送業者で作成した有効な運送業者アカウント番号を入力します。 Microsoft は、インポート ジョブの完了後、このアカウントを使ってドライブを返送します。 
    - 完全かつ有効な連絡先の名前、電話番号、電子メール、番地、市区町村、郵便番号、都道府県、国/地域を指定します。

        > [!TIP] 
        > 1 人のユーザーの電子メール アドレスを指定する代わりに、グループ メール アドレスを提供します。 これにより、管理者が離れる場合でも、通知を受信します。

       ![インポート ジョブの作成 - ステップ 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. **[概要]** で次の操作を実行します。

    - Azure にディスクを送付するために使用する Azure データセンターの送付先住所を指定します。 ジョブの名前と完全な住所が出荷ラベルに示されていることを確認します。
    - **[OK]** をクリックして、インポート ジョブの作成を完了します。

        ![インポート ジョブを作成する - 手順 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>手順 3:ドライブを Azure データセンターに送付する 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>手順 4:追跡情報を使用してジョブを更新する

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>手順 5:Azure へのデータのアップロードを確認する

完了するまでジョブを監視します。 ジョブが完了したら、データが Azure にアップロードされたことを確認します。 アップロードが成功したことを確認した後にのみ、オンプレミスのデータを削除します。

## <a name="samples-for-journal-files"></a>ジャーナル ファイルのサンプル

**ドライブを追加する**には、以下のように、新しいドライブセット ファイルを作成し、コマンドを実行します。 

*InitialDriveset .csv* ファイルで指定した以外のディスク ドライブに対する後続のコピー セッションについては、新しいドライブセット *.csv* ファイルを指定し、それを `AdditionalDriveSet` パラメーターの値として指定します。 **同じジャーナル ファイル**名を使用し、**新しいセッション ID** を指定します。 AdditionalDriveset CSV ファイルの形式は、InitialDriveSet の形式と同じです。

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

インポートの例を以下に示します。

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


追加のデータを同じドライブセットに追加するには、後続のコピー セッションで PrepImport コマンドを使用して、追加のファイルやディレクトリをコピーします。

*InitialDriveset.csv* ファイルで指定した同じハード ディスク ドライブに対する後続のコピー セッションでは、**同じジャーナル ファイル**名を指定し、**新しいセッション ID** を指定します。ストレージ アカウント キーを指定する必要はありません。

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

インポートの例を以下に示します。

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>次の手順

* [ジョブとドライブの状態の表示](storage-import-export-view-drive-status.md)
* [Import/Export の要件の確認](storage-import-export-requirements.md)


