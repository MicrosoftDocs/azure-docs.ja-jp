---
title: Azure Import/Export を使用した Azure Files へのデータの転送 | Microsoft Docs
description: Azure portal でインポート ジョブを作成し、データを Azure Files に転送する方法について説明します。
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: b62c3c4be4fdffd9f509b86d248cd028518ae89a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181943"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Azure Import/Export サービスを使用してデータを Azure Files にインポートする

この記事では、Azure Import/Export サービスを使用して大量のデータを Azure Files に安全にインポートする手順を説明します。 このサービスを使用してデータをインポートするには、データが含まれているサポート対象のディスク ドライブを Azure データセンターに送付する必要があります。

Import/Export サービスでは、Azure Files の Azure Storage へのインポートのみをサポートしています。 Azure Files のエクスポートはサポートしていません。

## <a name="prerequisites"></a>前提条件

インポート ジョブを作成して Azure Files にデータを転送する前に、次の前提条件の一覧を慎重に確認し、すべてを満たしてください。 次の手順が必要です。

- Import/Export サービスで使用するアクティブな Azure サブスクリプションがある。
- Azure Storage アカウントが少なくとも 1 つある。 [Import/Export サービスでサポートしているストレージ アカウントとストレージの種類](storage-import-export-requirements.md)の一覧を参照してください。 新しいストレージ アカウントの作成については、「 [ストレージ アカウントの作成方法](../storage/common/storage-account-create.md)」を参照してください。
- 十分な数の[サポートされている種類](storage-import-export-requirements.md#supported-disks)のディスクがある。
- [サポートされている OS バージョン](storage-import-export-requirements.md#supported-operating-systems)を実行している Windows システムがある。
- Windows システムで [WAImportExport バージョン 2 をダウンロード](https://aka.ms/waiev2)してください。 既定のフォルダー `waimportexport` に解凍します。 たとえば、「 `C:\WaImportExport` 」のように入力します。
- FedEx または DHL のアカウントを用意します。 FedEx または DHL 以外の運送業者を使用する場合、`adbops@microsoft.com` から Azure Data Box Operations チームまでお問い合わせください。
    - アカウントは、有効で、残高があり、差出人住所の機能を持っている必要があります。
    - エクスポート ジョブの追跡番号を生成します。
    - すべてのジョブに個別の追跡番号が必要です。 同じ追跡番号を持つ複数のジョブはサポートされていません。
    - 運送業者アカウントをお持ちでない場合は、次に移動します。
        - [FedEx アカウントを作成するか](https://www.fedex.com/en-us/create-account.html)、または
        - [DHL アカウントを作成します](http://www.dhl-usa.com/en/express/shipping/open_account.html)。


## <a name="step-1-prepare-the-drives"></a>手順 1:ドライブを準備する

この手順では、ジャーナル ファイルを生成します。 ジャーナル ファイルには、ドライブのシリアル番号、暗号化キー、ストレージ アカウントの詳細などの基本情報が保存されます。

次の手順を実行して、ドライブを準備します。

1. ディスク ドライブを SATA コネクタを介して Windows システムに接続します。
2. 各ドライブに 1 つの NTFS ボリュームを作成します。 ボリュームにドライブ文字を割り当てます。 マウントポイントを使用しないでください。
3. ツールが配置されているルート フォルダーにある *dataset.csv* ファイルを変更します。 インポート対象がファイルとフォルダーのどちらか一方か両方かに応じて、次の例のようなエントリを *dataset.csv* ファイルに追加します。

   - **ファイルをインポートするには**: 次の例では、コピーするデータは F: ドライブにあります。 ファイル *MyFile1.txt* は *MyAzureFileshare1* のルートにコピーされます。 *MyAzureFileshare1* は、存在しない場合、Azure Storage アカウントに作成されます。 フォルダー構造は維持されます。

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
     詳しくは、[データセット CSV ファイルの準備](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import)に関する記事をご覧ください。


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

     複数のドライブに対応する複数のエントリを同じファイル内に作成できます。 詳しくは、[ドライブセット CSV ファイルの準備](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import)に関する記事をご覧ください。

5. `PrepImport` オプションを使用して、ディスク ドライブにデータをコピーして準備します。 最初のコピー セッションで新しいコピー セッションを使用してディレクトリやファイルをコピーするために、次のコマンドを実行します。

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]/DataSet:<dataset.csv>
    ```

   インポートの例を以下に示します。

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. コマンド行を実行するたびに、`/j:` パラメーターで指定した名前のジャーナル ファイルが作成されます。 準備した各ドライブには、インポート ジョブを作成するときにアップロードする必要があるジャーナル ファイルがあります。 ジャーナル ファイルのないドライブは処理されません。

    > [!IMPORTANT]
    > - ディスクの準備が完了したら、ディスク ドライブ上のデータやジャーナル ファイルを変更しないでください。

その他のサンプルについては、「[ジャーナル ファイルのサンプル](#samples-for-journal-files)」を参照してください。

## <a name="step-2-create-an-import-job"></a>手順 2:インポート ジョブの作成

### <a name="portal"></a>[ポータル](#tab/azure-portal)

次の手順を実行して、Azure portal でインポート ジョブを作成します。
1. https://portal.azure.com/ にログオンします。
2. **インポート/エクスポート ジョブ** を検索します。

    ![インポート/エクスポート ジョブの検索](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. **[+新規]** を選択します。

    ![[新規] を選択して新しく作成する ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. **[基本]** で次のようにします。

   1. サブスクリプションを選択します。
   1. リソース グループを選択するか、 **[新規作成]** を選択して新しく作成します。
   1. インポート ジョブのわかりやすい名前を入力します。 この名前は、ジョブの進行状況の追跡に使用します。
       * 名前には小文字、数字、ハイフンのみを含めることができます。
       * 名前はアルファベットから始める必要があります。スペースを含めることはできません。
   1. **[Azure へインポート]** を選択します。

    ![インポート ジョブを作成する - 手順 1](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

   **[次へ: ジョブの詳細 >]** を選択して続行します。

5. **[ジョブの詳細]** で次の操作を実行します。

   1. 上記の「[手順 1: ドライブを準備する](#step-1-prepare-the-drives)」で作成したジャーナル ファイルをアップロードします。
   1. 注文の宛先となる Azure リージョンを選択します。
   1. インポート用のストレージ アカウントを選択します。

      配送場所は、選んだストレージ アカウントのリージョンに基づいて自動的に入力されます。

   1. 詳細ログを保存しない場合は、 **[Save verbose log in the 'waimportexport' blob container]\(詳細ログを 'waimportexport' BLOB コンテナーに保存する\)** オプションをオフにします。


   ![インポート ジョブを作成する - 手順 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png)

   **[次へ: 出荷 >]** を選択して続行します。

4. **[出荷]** で、次の手順に従います。

    1. ドロップダウン リストから運送業者を選択します。 FedEx または DHL 以外の運送業者を使用する場合は、ドロップダウンから既存のオプションを選びます。 Azure Data Box Operations チーム (`adbops@microsoft.com`) に、使用する予定の運送業者に関する情報をご連絡ください。
    1. その運送業者で作成した有効な運送業者アカウント番号を入力します。 Microsoft は、インポート ジョブの完了後、このアカウントを使ってドライブを返送します。
    1. 完全かつ有効な連絡先の名前、電話番号、電子メール、番地、市区町村、郵便番号、都道府県、国/地域を指定します。

        > [!TIP]
        > 1 人のユーザーの電子メール アドレスを指定する代わりに、グループ メール アドレスを提供します。 これにより、管理者が離れる場合でも、通知を受信します。

    ![インポート ジョブの作成 - ステップ 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   **[確認と作成]** を選択して続行します。

5. 注文の概要で、次の手順に従います。

   1. **[使用条件]** を確認し、[指定したすべての情報が正しいことを確認しました。上記の利用規約に同意します] を選択します。 その後、検証が実行されます。
   1. 概要に表示されているジョブ情報を確認します。 ジョブ名と、Azure にディスクを送付するために使用する Azure データセンターの送付先住所をメモします。 この情報は、後で配送先住所ラベルに使用します。
   1. **［作成］** を選択します

        ![インポート ジョブを作成する - 手順 4](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

次の手順を使用して、Azure CLI でインポート ジョブを作成します。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>ジョブの作成

1. [az extension add](/cli/azure/extension#az_extension_add) コマンドを使用して、[az import-export](/cli/azure/ext/import-export/import-export) 拡張機能を追加します。

    ```azurecli
    az extension add --name import-export
    ```

1. 既存のリソース グループを使用することも、リソース グループを作成することもできます。 リソース グループを作成するには、[az group create](/cli/azure/group#az_group_create) コマンドを実行します。

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. 既存のストレージ アカウントを使用することも、ストレージ アカウントを作成することもできます。 ストレージ アカウントを作成するには、[az storage account create](/cli/azure/storage/account#az_storage_account_create) コマンドを実行します。

    ```azurecli
    az storage account create -resource-group myierg -name myssdocsstorage --https-only
    ```

1. ディスクを受け取ることができる場所の一覧を取得するには、[az import-export location list](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) コマンドを使用します。

    ```azurecli
    az import-export location list
    ```

1. [az import-export location show](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_show) コマンドを使用して、リージョンの場所を取得します。

    ```azurecli
    az import-export location show --location "West US"
    ```

1. 次の [az import-export create](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) コマンドを実行して、インポート ジョブを作成します。

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name MyIEjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --drive-list bit-locker-key=439675-460165-128202-905124-487224-524332-851649-442187 \
            drive-header-hash= drive-id=AZ31BGB1 manifest-file=\\DriveManifest.xml \
            manifest-hash=69512026C1E8D4401816A2E5B8D7420D \
        --type Import \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --return-shipping carrier-name=FedEx carrier-account-number=123456789 \
        --storage-account myssdocsstorage
    ```

   > [!TIP]
   > 1 人のユーザーの電子メール アドレスを指定する代わりに、グループ メール アドレスを提供します。 これにより、管理者が離れる場合でも、通知を受信します。


1. [az import-export list](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) コマンドを使用して、リソース グループ myierg のすべてのジョブを表示します。

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. ジョブを更新するかジョブをキャンセルするには、[az import-export update](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update) コマンドを実行します。

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

以下の手順を使用して、Azure PowerShell でインポート ジョブを作成します。

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> **Az.ImportExport** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、これを別途インストールする必要があります。 この PowerShell モジュールは、一般提供されると、将来の Az PowerShell モジュール リリースに含まれ、既定で Azure Cloud Shell 内から使用できるようになります。

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>ジョブの作成

1. 既存のリソース グループを使用することも、リソース グループを作成することもできます。 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを実行して、リソース グループを作成します。

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myierg -Location westus
   ```

1. 既存のストレージ アカウントを使用することも、ストレージ アカウントを作成することもできます。 ストレージ アカウントを作成するには、[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) コマンドレットを実行します。

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName myierg -AccountName myssdocsstorage -SkuName Standard_RAGRS -Location westus -EnableHttpsTrafficOnly $true
   ```

1. ディスクを出荷できる場所の一覧を取得するには、[Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) コマンドレットを使用します。

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. `Get-AzImportExportLocation` コマンドレットを `Name` パラメーターと使用して、リージョンの場所を取得します。

   ```azurepowershell-interactive
   Get-AzImportExportLocation -Name westus
   ```

1. 次の [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) の例を実行して、インポート ジョブを作成します。

   ```azurepowershell-interactive
   $driveList = @(@{
     DriveId = '9CA995BA'
     BitLockerKey = '439675-460165-128202-905124-487224-524332-851649-442187'
     ManifestFile = '\\DriveManifest.xml'
     ManifestHash = '69512026C1E8D4401816A2E5B8D7420D'
     DriveHeaderHash = 'AZ31BGB1'
   })

   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'MyIEjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      DriveList = $driveList
      JobType = 'Import'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      ReturnShippingCarrierName = 'FedEx'
      ReturnShippingCarrierAccountNumber = '123456789'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

   > [!TIP]
   > 1 人のユーザーの電子メール アドレスを指定する代わりに、グループ メール アドレスを提供します。 これにより、管理者が離れる場合でも、通知を受信します。

1. [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) コマンドレットを使用して、myierg リソース グループのすべてのジョブを表示します。

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. ジョブを更新するかジョブをキャンセルするには、[Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) コマンドレットを実行します。

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>手順 3: ドライブを Azure データセンターに送付する

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>手順 4: 追跡情報を使用してジョブを更新する

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>手順 5: Azure へのデータのアップロードを確認する

完了するまでジョブを監視します。 ジョブが完了したら、データが Azure にアップロードされたことを確認します。 オンプレミスのデータは、アップロードが成功したことを確認した後にのみ削除してください。

## <a name="samples-for-journal-files"></a>ジャーナル ファイルのサンプル

**ドライブを追加する** には、以下のように、新しいドライブセット ファイルを作成し、コマンドを実行します。

*InitialDriveset .csv* ファイルで指定したディスク ドライブ以外に対する後続のコピー セッションについては、新しいドライブセット *.csv* ファイルを指定し、それを `AdditionalDriveSet` パラメーターの値として指定します。 **同じジャーナル ファイル** 名を使用し、**新しいセッション ID** を指定します。 AdditionalDriveset CSV ファイルの形式は、InitialDriveSet の形式と同じです。

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

インポートの例を以下に示します。

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


追加のデータを同じドライブセットに追加するには、後続のコピー セッションで PrepImport コマンドを使用して、追加のファイルやディレクトリをコピーします。

*InitialDriveset.csv* ファイルで指定した同じハード ディスク ドライブに対する後続のコピー セッションでは、**同じジャーナル ファイル** 名を指定し、**新しいセッション ID** を指定します。ストレージ アカウント キーを指定する必要はありません。

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

インポートの例を以下に示します。

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>次のステップ

* [ジョブとドライブの状態の表示](storage-import-export-view-drive-status.md)
* [Import/Export の要件の確認](storage-import-export-requirements.md)