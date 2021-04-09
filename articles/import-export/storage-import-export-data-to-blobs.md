---
title: Azure Import/Export を使用した Azure Blob へのデータの転送 | Microsoft Docs
description: Azure portal でインポート ジョブとエクスポート ジョブを作成し、Azure BLOB との間でデータを転送する方法について説明します。
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/15/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 74f5565ba9dfa48dabfe56c25e3ef30a8caafe14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563285"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Azure Import/Export サービスを使用して Azure Blob Storage にデータをインポートする

この記事では、Azure Import/Export サービスを使用して大量のデータを Azure Blob Storage に安全にインポートする手順について説明します。 Azure BLOB にデータをインポートするには、データが保存されている暗号化されたディスク ドライブを Azure データセンターに送付する必要があります。

## <a name="prerequisites"></a>前提条件

Azure Blob Storage にデータを転送するインポート ジョブを作成する前に、このサービスの次の前提条件の一覧を慎重に確認してください。
次の手順が必要です。

* Import/Export サービスに使用できるアクティブな Azure サブスクリプションがある。
* ストレージ コンテナーがある Azure ストレージ アカウントを少なくとも 1 つは持っている。 [Import/Export サービスでサポートしているストレージ アカウントとストレージの種類](storage-import-export-requirements.md)の一覧を参照してください。
  * 新しいストレージ アカウントの作成については、「 [ストレージ アカウントの作成方法](../storage/common/storage-account-create.md)」を参照してください。
  * ストレージ コンテナーについては、[ストレージ コンテナーの作成](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)に関するセクションを参照してください。
* 十分な数の[サポートされている種類](storage-import-export-requirements.md#supported-disks)のディスクがある。
* [サポートされている OS バージョン](storage-import-export-requirements.md#supported-operating-systems)を実行している Windows システムがある。
* Windows システムで BitLocker を有効にする。 [BitLocker を有効にする方法](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)に関するページを参照してください。
* Windows システムに[最新の WAImportExport バージョン 1 をダウンロード](https://www.microsoft.com/download/details.aspx?id=42659)します。 最新バージョンのツールには、BitLocker キーの外部プロテクター、および更新されたロック解除モード機能を許可する、セキュリティ更新プログラムがあります。

  * 既定のフォルダー `waimportexportv1` に解凍します。 たとえば、「 `C:\WaImportExportV1` 」のように入力します。
* FedEx または DHL のアカウントを用意します。 FedEx または DHL 以外の運送業者を使用する場合、`adbops@microsoft.com` から Azure Data Box Operations チームまでお問い合わせください。
  * アカウントは、有効で、残高があり、差出人住所の機能を持っている必要があります。
  * エクスポート ジョブの追跡番号を生成します。
  * すべてのジョブに個別の追跡番号が必要です。 同じ追跡番号を持つ複数のジョブはサポートされていません。
  * 運送業者アカウントがいない場合、次に移動します。
    * [FedEx アカウントを作成するか](https://www.fedex.com/en-us/create-account.html)、または
    * [DHL アカウントを作成します](http://www.dhl-usa.com/en/express/shipping/open_account.html)。

## <a name="step-1-prepare-the-drives"></a>手順 1:ドライブを準備する

この手順では、ジャーナル ファイルを生成します。 ジャーナル ファイルには、ドライブのシリアル番号、暗号化キー、ストレージ アカウントの詳細などの基本情報が保存されます。

次の手順を実行して、ドライブを準備します。

1. ディスク ドライブを SATA コネクタを介して Windows システムに接続します。
2. 各ドライブに 1 つの NTFS ボリュームを作成します。 ボリュームにドライブ文字を割り当てます。 マウントポイントを使用しないでください。
3. NTFS ボリュームの BitLocker 暗号化を有効にします。 Windows Server システムを使用している場合は、[Windows Server 2012 R2 で BitLocker を有効にする方法](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)に関するページを参照してください。
4. 暗号化されたボリュームにデータをコピーします。 ドラッグ アンド ドロップや、Robocopy などのコピーツールを使用します。 ジャーナル ファイル ( *.jrn*) は、ツールを実行したフォルダーと同じフォルダーに作成されます。

   ドライブがロックされていて、ドライブのロックを解除する必要がある場合は、ユース ケースによってロック解除の手順が異なる可能性があります。

   * 事前に暗号化されたドライブにデータを追加した場合 (暗号化に WAImportExport ツールが使用されていない場合) は、ポップアップで BitLocker キー (指定した数字のパスワード) を使用してドライブのロックを解除します。

   * WAImportExport ツールによって暗号化されたドライブにデータを追加した場合は、次のコマンドを使用してドライブのロックを解除します。

        `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. 管理特権を使用して PowerShell またはコマンド ライン ウィンドウを開きます。 解凍されたフォルダーにディレクトリを変更するには、次のコマンドを実行します。

    `cd C:\WaImportExportV1`
6. ドライブの BitLocker キーを取得するには、次のコマンドを実行します。

    `manage-bde -protectors -get <DriveLetter>:`
7. ディスクを準備するには、次のコマンドを実行します。 **データ サイズによっては、ディスクの準備に数時間から数日かかることがあります。**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
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
    |/blobtype:     |このオプションは、データをインポートする BLOB の種類を指定します。 ブロック BLOB の場合、BLOB の種類は `BlockBlob` で、ページ BLOB の場合は `PageBlob` です。         |
    |/skipwrite:     | コピーする必要がある新しいデータがなく、ディスク上の既存のデータを準備する必要があることを指定します。          |
    |/enablecontentmd5:     |有効にすると、このオプションにより、MD5 が計算されて、各 BLOB で `Content-md5` プロパティとして設定されます。 このオプションは、データが Azure にアップロードされた後に、`Content-md5` フィールドを使用する場合にのみ使用してください。 <br> このオプションはデータ整合性チェック (既定で実行) には影響しません。 この設定により、クラウドにデータをアップロードするためにかかる時間が長くなります。          |
8. 送付する必要があるディスクごとに前の手順を繰り返します。 コマンド行の実行ごとに、指定された名前のジャーナル ファイルが作成されます。

    > [!IMPORTANT]
    > * ツールが存在するフォルダーと同じフォルダーに、ジャーナル ファイルと共に `<Journal file name>_DriveInfo_<Drive serial ID>.xml` ファイルも作成されます。 ジャーナル ファイルが大きすぎる場合、ジョブの作成時にジャーナル ファイルではなく .xml ファイルが使用されます。
   > * ポータルで許可されるジャーナル ファイルの最大サイズは 2 MB です。 ジャーナル ファイルがこの制限を超えると、エラーが返されます。

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

   1. 上記の「[手順 1: ドライブを準備する](#step-1-prepare-the-drives)」で作成したジャーナル ファイルをアップロードします。 `waimportexport.exe version1` を使用した場合、準備したドライブごとに 1 つのファイルをアップロードします。 ジャーナル ファイルのサイズが 2 MB を超える場合は、ジャーナル ファイルと共に作成された `<Journal file name>_DriveInfo_<Drive serial ID>.xml` を使用することもできます。
   1. 注文の宛先となる Azure リージョンを選択します。
   1. インポート用のストレージ アカウントを選択します。
      
      配送場所は、選んだストレージ アカウントのリージョンに基づいて自動的に入力されます。
   1. 詳細ログを保存しない場合は、 **[Save verbose log in the 'waimportexport' blob container]\(詳細ログを 'waimportexport' BLOB コンテナーに保存する\)** オプションをオフにします。

   ![インポート ジョブを作成する - 手順 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png).

   **[次へ: 出荷 >]** を選択して続行します。

6. **[出荷]** で、次の手順に従います。

   1. ドロップダウン リストから運送業者を選択します。 FedEx または DHL 以外の運送業者を使用する場合は、ドロップダウンから既存のオプションを選びます。 `adbops@microsoft.com` で Azure Data Box Operations チームに使用する予定の運送業者に関する情報を連絡してください。
   1. その運送業者で作成した有効な運送業者アカウント番号を入力します。 Microsoft は、インポート ジョブの完了後、このアカウントを使ってドライブを返送します。 アカウント番号をお持ちでない場合は、[FedEx](https://www.fedex.com/us/oadr/) または [DHL](https://www.dhl.com/) の運送業者アカウントを作成してください。
   1.  完全かつ有効な連絡先の名前、電話番号、電子メール、番地、市区町村、郵便番号、都道府県、国/地域を指定します。

       > [!TIP]
       > 1 人のユーザーの電子メール アドレスを指定する代わりに、グループ メール アドレスを提供します。 これにより、管理者が離れる場合でも、通知を受信します。

   ![インポート ジョブの作成 - ステップ 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   **[確認と作成]** を選択して続行します。

7. 注文の概要で、次の手順に従います。

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
    az storage account create --resource-group myierg --name myssdocsstorage --https-only
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

## <a name="step-3-optional-configure-customer-managed-key"></a>手順 3 (省略可能):カスタマー マネージド キーを構成する

Microsoft マネージド キーを使用してドライブの BitLocker キーを保護する場合は、この手順をスキップして次の手順に進みます。 BitLocker キーを保護するために独自のキーを構成するには、[Azure portal を使用して、Azure Import/Export 用に Azure Key Vault でカスタマー マネージド キーを構成する](storage-import-export-encryption-key-portal.md)手順に従います。

## <a name="step-4-ship-the-drives"></a>手順 4:ドライブを送付する

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>手順 5:追跡情報を使用してジョブを更新する

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>手順 6:Azure へのデータのアップロードを確認する

完了するまでジョブを監視します。 ジョブが完了したら、データが Azure にアップロードされたことを確認します。 アップロードが成功したことを確認した後にのみ、オンプレミスのデータを削除します。

## <a name="next-steps"></a>次のステップ

* [ジョブとドライブの状態の表示](storage-import-export-view-drive-status.md)
* [Import/Export の要件の確認](storage-import-export-requirements.md)
