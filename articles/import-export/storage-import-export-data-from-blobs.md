---
title: Azure Import/Export を使用して Azure BLOB からデータをエクスポートする | Microsoft Docs
description: Azure portal でエクスポート ジョブを作成して、Azure BLOB からデータを転送する方法について説明します。
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: e878be5351362923e163c0a6f617b96ab72a36d8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177551"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Azure Import/Export サービスを使用して Azure Blob Storage からデータをエクスポートする

この記事では、Azure Import/Export サービスを使用して大量のデータを Azure Blob Storage から安全にエクスポートする手順について説明します。 このサービスを利用するには、Azure データセンターに空のドライブを送付する必要があります。 このサービスでは、ストレージ アカウントからドライブにデータをエクスポートし、ドライブを返送します。

## <a name="prerequisites"></a>前提条件

Azure Blob Storage からデータを転送するエクスポート ジョブを作成する前に、このサービスの次の前提条件の一覧を慎重に確認してください。
次の手順が必要です。

- Import/Export サービスに使用できるアクティブな Azure サブスクリプションがある。
- Azure Storage アカウントが少なくとも 1 つある。 [Import/Export サービスでサポートしているストレージ アカウントとストレージの種類](storage-import-export-requirements.md)の一覧を参照してください。 新しいストレージ アカウントの作成については、「 [ストレージ アカウントの作成方法](../storage/common/storage-account-create.md)」を参照してください。
- 十分な数の[サポートされている種類](storage-import-export-requirements.md#supported-disks)のディスクがある。
- FedEx または DHL のアカウントを用意します。 FedEx または DHL 以外の運送業者を使用する場合、`adbops@microsoft.com` から Azure Data Box Operations チームまでお問い合わせください。
  - アカウントは、有効で、残高があり、差出人住所の機能を持っている必要があります。
  - エクスポート ジョブの追跡番号を生成します。
  - すべてのジョブに個別の追跡番号が必要です。 同じ追跡番号を持つ複数のジョブはサポートされていません。
  - 運送業者アカウントがいない場合、次に移動します。
    - [FedEx アカウントを作成するか](https://www.fedex.com/en-us/create-account.html)、または
    - [DHL アカウントを作成します](http://www.dhl-usa.com/en/express/shipping/open_account.html)。

## <a name="step-1-create-an-export-job"></a>手順 1:エクスポート ジョブの作成

### <a name="portal"></a>[ポータル](#tab/azure-portal)

以下の手順を実行して、Azure portal でエクスポート ジョブを作成します。

1. <https://portal.azure.com/> にログオンします。
2. **インポート/エクスポート ジョブ** を検索します。

    ![インポート/エクスポート ジョブを検索する](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. **[+新規]** を選択します。

    ![[+新規] を選択して新しく作成する ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. **[基本]** で次のようにします。

   1. サブスクリプションを選択します。
   1. リソース グループを選択するか、 **[新規作成]** を選択して新しく作成します。
   1. インポート ジョブのわかりやすい名前を入力します。 この名前は、ジョブの進行状況の追跡に使用します。
       * 名前には小文字、数字、ハイフンのみを含めることができます。
       * 名前はアルファベットから始める必要があります。スペースを含めることはできません。

   1. **[Azure からエクスポート]** を選択します。

    ![エクスポート注文の [基本] オプション](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

    **[次へ: ジョブの詳細 >]** を選択して続行します。

5. **[ジョブの詳細]** で次の操作を実行します。

   1. データが現在存在する Azure リージョンを選択します。
   1. エクスポートするデータがあるストレージ アカウントを選択します。 所在地に近いストレージ アカウントを使用します。

      配送場所は、選んだストレージ アカウントのリージョンに基づいて自動的に入力されます。

   1. ストレージ アカウントから空のドライブにエクスポートする BLOB データを指定します。 以下の 3 つの方法のいずれかを選択します。

      - ストレージ アカウントの BLOB データについて **[すべてをエクスポートする]** を選択します。

        ![[すべてをエクスポートする]](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

      - **[選択されているコンテナーと BLOB]** を選択し、エクスポートするコンテナーと BLOB を指定します。 複数の選択方法を使用できます。 **[追加]** オプションを選択すると右側のパネルが開き、選択文字列を追加できます。

        |オプション|説明|
        |------|-----------|      
        |**コンテナーの追加**|コンテナー内のすべての BLOB をエクスポートします。<br>**[コンテナーの追加]** を選択し、各コンテナーの名前を入力します。|
        |**BLOB の追加**|エクスポートする個々の BLOB を指定します。<br>**[BLOB の追加]** を選択します。 その後、コンテナー名から始まる BLOB への相対パスを指定します。 ルート コンテナーを指定するには、" *$root* " を使用します。<br>次のスクリーンショットで示すように、処理中にエラーが発生しないように、有効な形式で BLOB のパスを指定する必要があります。 詳細については、「[有効な BLOB パスの例](#examples-of-valid-blob-paths)」を参照してください。|
        |**プレフィックスの追加**|似た名前のコンテナーやコンテナー内の似た名前の BLOB のセットを選択するには、プレフィックスを使用します。 プレフィックスには、コンテナー名のプレフィックス、コンテナー名全体、またはコンテナー名全体の後に BLOB 名のプレフィックスを付けた名前を指定できます。 |

        ![選択したコンテナーと BLOB をエクスポートする](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - **[BLOB リスト ファイルからエクスポートする (XML 形式)]** を選択し、ストレージ アカウントからエクスポートする BLOB のパスとプレフィックスの一覧を含む XML ファイルを選択します。 XML ファイルを作成し、ストレージ アカウントのコンテナーに格納する必要があります。 このファイルは空にできません。

      > [!IMPORTANT]
      > XML ファイルを使用してエクスポートする BLOB を選択する場合は、XML に有効なパスまたはプレフィックスが含まれていることを確認してください。 ファイルが無効な場合、または指定されたパスと一致するデータがない場合は、一部のデータまたはすべてのデータがエクスポートされずに終了します。

       XML ファイルをコンテナーに追加する方法については、「[XML ファイルを使用した注文のエクスポート](../databox/data-box-deploy-export-ordered.md#export-order-using-xml-file)」を参照してください。

      ![BLOB リスト ファイルからエクスポートする](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > データ コピー時にエクスポートする BLOB が使用中の場合、Azure Import/Export サービスによって BLOB のスナップショットが作成され、そのスナップショットがコピーされます。

   **[次へ: 出荷 >]** を選択して続行します。

6. **[出荷]** で、次の手順に従います。

    - ドロップダウン リストから運送業者を選択します。 FedEx または DHL 以外の運送業者を使用する場合は、ドロップダウンから既存のオプションを選びます。 `adbops@microsoft.com` で Azure Data Box Operations チームに使用する予定の運送業者に関する情報を連絡してください。
    - その運送業者で作成した有効な運送業者アカウント番号を入力します。 Microsoft は、エクスポート ジョブの完了後、このアカウントを使ってドライブを返送します。
    - 完全かつ有効な連絡先の名前、電話番号、電子メール、番地、市区町村、郵便番号、都道府県、国/地域を指定します。

        > [!TIP]
        > 1 人のユーザーの電子メール アドレスを指定する代わりに、グループ メール アドレスを提供します。 これにより、管理者が離れる場合でも、通知を受信します。

    **[確認と作成]** を選択して続行します。

7. **[確認と作成]** では、以下を行います。

   1. ジョブの詳細を確認します。
   1. ジョブ名と、Azure にディスクを送付するために使用する Azure データセンターの送付先住所をメモします。

      > [!NOTE]
      > ディスクは常に、Azure Portal に示されているデータ センターに送付します。 誤って別のデータ センターにディスクが発送された場合、ジョブは処理されません。

   1. プライバシーとソース データの削除に関する注文の **使用条件** を確認します。 使用条件に同意する場合は、使用条件の下にあるチェック ボックスをオンにします。 注文の検証が開始されます。

   ![エクスポート注文を確認して作成する](./media/storage-import-export-data-from-blobs/export-from-blob-6-a.png)

 1. 検証に合格した後、 **[作成]** を選択します。

<!--Replaced text: Steps 4 - end of "Create an export job." Wizard design changes required both screen and text updates.

4. In **Basics**:

    - Select **Export from Azure**.
    - Enter a descriptive name for the export job. Use the name you choose to track the progress of your jobs.
        - The name may contain only lowercase letters, numbers, hyphens, and underscores.
        - The name must start with a letter, and may not contain spaces.
    - Select a subscription.
    - Enter or select a resource group.

        ![Basics](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

5. In **Job details**:

    - Select the storage account where the data to be exported resides. Use a storage account close to where you are located.
    - The dropoff location is automatically populated based on the region of the storage account selected.
    - Specify the blob data you wish to export from your storage account to your blank drive or drives.
    - Choose to **Export all** blob data in the storage account.

         ![Export all](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

    - You can specify which containers and blobs to export.
        - **To specify a blob to export**: Use the **Equal To** selector. Specify the relative path to the blob, beginning with the container name. Use *$root* to specify the root container.
        - **To specify all blobs starting with a prefix**: Use the **Starts With** selector. Specify the prefix, beginning with a forward slash '/'. The prefix may be the prefix of the container name, the complete container name, or the complete container name followed by the prefix of the blob name. You must provide the blob paths in valid format to avoid errors during processing, as shown in this screenshot. For more information, see [Examples of valid blob paths](#examples-of-valid-blob-paths).

           ![Export selected containers and blobs](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - You can export from  the blob list file.

        ![Export from blob list file](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > If the blob to be exported is in use during data copy, Azure Import/Export service takes a snapshot of the blob and copies the snapshot.

6. In **Return shipping info**:

    - Select the carrier from the dropdown list. If you want to use a carrier other than FedEx/DHL, choose an existing option from the dropdown. Contact Azure Data Box Operations team at `adbops@microsoft.com`  with the information regarding the carrier you plan to use.
    - Enter a valid carrier account number that you have created with that carrier. Microsoft uses this account to ship the drives back to you once your export job is complete.
    - Provide a complete and valid contact name, phone, email, street address, city, zip, state/province, and country/region.

        > [!TIP]
        > Instead of specifying an email address for a single user, provide a group email. This ensures that you receive notifications even if an admin leaves.

7. In **Summary**:

    - Review the details of the job.
    - Make a note of the job name and provided Azure datacenter shipping address for shipping disks to Azure.

        > [!NOTE]
        > Always send the disks to the datacenter noted in the Azure portal. If the disks are shipped to the wrong datacenter, the job will not be processed.

    - Click **OK** to complete export job creation.
-->

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

以下の手順を使用して、Azure portal でエクスポート ジョブを作成します。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>ジョブの作成

1. [az extension add](/cli/azure/extension#az_extension_add) コマンドを使用して、[az import-export](/cli/azure/ext/import-export/import-export) 拡張機能を追加します。

    ```azurecli
    az extension add --name import-export
    ```

1. ディスクを受け取ることができる場所の一覧を取得するには、[az import-export location list](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) コマンドを使用します。

    ```azurecli
    az import-export location list
    ```

1. 次の [az import-export create](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) コマンドを実行して、既存のストレージ アカウントを使用するエクスポート ジョブを作成します。

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > 1 人のユーザーの電子メール アドレスを指定する代わりに、グループ メール アドレスを提供します。 これにより、管理者が離れる場合でも、通知を受信します。

   このジョブは、ストレージ アカウント内のすべての BLOB をエクスポートします。 エクスポートする BLOB を指定するには、この **--export** の値を次のように置き換えます。

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   このパラメーター値では、ルート コンテナー内の *logo.bmp* という名前の BLOB がエクスポートされます。

   また、プレフィックスを使用して、コンテナー内のすべての BLOB を選択するオプションもあります。 この **--export** の値を次のように置き換えます。

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   詳細については、「[有効な BLOB パスの例](#examples-of-valid-blob-paths)」を参照してください。

   > [!NOTE]
   > データ コピー時にエクスポートする BLOB が使用中の場合、Azure Import/Export サービスは BLOB のスナップショットを作成し、スナップショットをコピーします。

1. [az import-export list](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) コマンドを使用して、リソース グループ myierg のすべてのジョブを表示します。

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. ジョブを更新するかジョブをキャンセルするには、[az import-export update](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update) コマンドを実行します。

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

以下の手順を使用して、Azure PowerShell でエクスポート ジョブを作成します。

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> **Az.ImportExport** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、これを別途インストールする必要があります。 この PowerShell モジュールは、一般提供されると、将来の Az PowerShell モジュール リリースに含まれ、既定で Azure Cloud Shell 内から使用できるようになります。

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>ジョブの作成

1. ディスクを受け取ることができる場所の一覧を取得するには、[Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) コマンドレットを使用します。

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. 次の [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) の例を実行して、既存のストレージ アカウントを使用するエクスポート ジョブを作成します。

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
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
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > 1 人のユーザーの電子メール アドレスを指定する代わりに、グループ メール アドレスを提供します。 これにより、管理者が離れる場合でも、通知を受信します。

   このジョブは、ストレージ アカウント内のすべての BLOB をエクスポートします。 エクスポートする BLOB を指定するには、この **-ExportBlobListblobPath** の値を次のように置き換えます。

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   このパラメーター値では、ルート コンテナー内の *logo.bmp* という名前の BLOB がエクスポートされます。

   また、プレフィックスを使用して、コンテナー内のすべての BLOB を選択するオプションもあります。 この **-ExportBlobListblobPath** の値を次のように置き換えます。

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   詳細については、「[有効な BLOB パスの例](#examples-of-valid-blob-paths)」を参照してください。

   > [!NOTE]
   > データ コピー時にエクスポートする BLOB が使用中の場合、Azure Import/Export サービスは BLOB のスナップショットを作成し、スナップショットをコピーします。

1. [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) コマンドレットを使用して、リソース グループ myierg のすべてのジョブを表示します。

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. ジョブを更新するかジョブをキャンセルするには、[Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) コマンドレットを実行します。

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>手順 2:ドライブを送付する

必要なドライブ数がわからない場合は、「[ドライブ数を確認する](#check-the-number-of-drives)」を参照してください。 ドライブの数がわかっている場合は、ドライブの送付に進みます。

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>手順 3:追跡情報を使用してジョブを更新する

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>手順 4:ディスクを受け取る

ダッシュボードでジョブの完了が報告されると、ディスクが返送され、ポータルで配送の追跡番号を利用できるようになります。

1. エクスポートされたデータが保存されたドライブを受け取ったら、BitLocker キーを取得してドライブのロックを解除する必要があります。 Azure portal でエクスポート ジョブに移動します。 **[インポート/エクスポート]** タブをクリックします。
2. リストからエクスポート ジョブを選択してクリックします。 **[暗号化]** に移動し、キーをコピーします。

   ![エクスポート ジョブの BitLocker キーの表示](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. BitLocker キーを使用してディスクのロックを解除します。

これでエクスポートは完了です。

## <a name="step-5-unlock-the-disks"></a>手順 5:ディスクのロックを解除する

ドライブのロックを解除するには、次のコマンドを使用します。

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

サンプル入力の例を次に示します。

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

この時点で、ジョブを削除するか、そのままにしておくことができます。 ジョブは、90 日後に自動的に削除されます。

## <a name="check-the-number-of-drives"></a>ドライブ数を確認する

この "*省略可能*" な手順は、エクスポート ジョブに必要なドライブ数を決定するために役立ちます。 [サポートされている OS バージョン](storage-import-export-requirements.md#supported-operating-systems)を実行している Windows システム上でこの手順を実行します。

1. Windows システムに [WAImportExport バージョン 1 をダウンロード](https://www.microsoft.com/download/details.aspx?id=42659)します。
2. 既定のフォルダー `waimportexportv1` に解凍します。 たとえば、「 `C:\WaImportExportV1` 」のように入力します。
3. 管理特権を使用して PowerShell またはコマンド ライン ウィンドウを開きます。 解凍されたフォルダーにディレクトリを変更するには、次のコマンドを実行します。

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

```powershell
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

```powershell
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

## <a name="next-steps"></a>次のステップ

- [ジョブとドライブの状態の表示](storage-import-export-view-drive-status.md)
- [Import/Export の要件の確認](storage-import-export-requirements.md)
