---
title: Azure Image Builder サービスのトラブルシューティング
description: Azure VM Image Builder サービスを使用するときの一般的な問題とエラーのトラブルシューティングを行います
author: cynthn
ms.author: danis
ms.date: 10/02/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: f76c3e6c739ae4dd13355d350a01b878e4d4f360
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666208"
---
# <a name="troubleshoot-azure-image-builder-service"></a>Azure Image Builder サービスのトラブルシューティング

この記事は、Azure Image Builder サービスの使用時に発生する可能性がある一般的な問題のトラブルシューティングと解決に役立ちます。

AIB の障害は、次の 2 つの領域で発生する可能性があります。
- イメージ テンプレートの送信
- イメージのビルド

## <a name="troubleshoot-image-template-submission-errors"></a>イメージ テンプレート送信エラーのトラブルシューティング

イメージ テンプレート送信エラーは、送信時にのみ返されます。 イメージ テンプレート送信エラーに対しては、エラー ログはありません。 送信中にエラーが発生した場合は、テンプレートの状態を確認することでエラーを返すことができます。具体的には、`ProvisioningState` と `ProvisioningErrorMessage`/`provisioningError` を確認します。

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> PowerShell の場合は、[Azure Image Builder PowerShell モジュール](../windows/image-builder-powershell.md#prerequisites)をインストールする必要があります。

以下のセクションでは、イメージ テンプレートの一般的な送信エラーに関する問題解決のガイダンスについて説明します。

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>イメージ テンプレートの更新とアップグレードは現在サポートされていない

#### <a name="error"></a>エラー

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>原因

テンプレートは既に存在します。

#### <a name="solution"></a>解決策

イメージ構成テンプレートを送信し、送信が失敗した場合、失敗したテンプレート成果物はまだ存在しています。 失敗したテンプレートを削除してください。

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>リソース操作が完了し、ターミナル プロビジョニング状態は 'Failed' である

#### <a name="error"></a>エラー

```text
Microsoft.VirtualMachineImages/imageTemplates 'helloImageTemplateforSIG01' failed with message '{
  "status": "Failed",
  "error": {
    "code": "ResourceDeploymentFailure",
    "message": "The resource operation completed with terminal provisioning state 'Failed'.",
    "details": [
      {
        "code": "InternalOperationError",
        "message": "Internal error occurred."
```
#### <a name="cause"></a>原因

ほとんどの場合、リソースのデプロイ失敗エラーは、アクセス許可がないことが原因で発生します。

#### <a name="solution"></a>解決策

シナリオによっては、Azure Image Builder で次のものに対するアクセス許可が必要になる場合があります。
- ソース イメージまたは Shared Image Gallery リソース グループ
- ディストリビューション イメージまたは Shared Image Gallery リソース
- ファイル カスタマイザーがアクセスしているストレージ アカウント、コンテナー、または BLOB。 

アクセス許可の構成の詳細については、「[Azure CLI を使用して Azure Image Builder サービスのアクセス許可を構成する](image-builder-permissions-cli.md)」または「[PowerShell を使用して Azure Image Builder サービスのアクセス許可を構成する](image-builder-permissions-powershell.md)」を参照してください

### <a name="error-getting-managed-image"></a>マネージド イメージの取得エラー

#### <a name="error"></a>エラー

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>原因

アクセス許可がありません。

#### <a name="solution"></a>解決策

シナリオによっては、Azure Image Builder で次のものに対するアクセス許可が必要になる場合があります。
* ソース イメージまたは Shared Image Gallery リソース グループ
* ディストリビューション イメージまたは Shared Image Gallery リソース
* ファイル カスタマイザーがアクセスしているストレージ アカウント、コンテナー、または BLOB。 

アクセス許可の構成の詳細については、「[Azure CLI を使用して Azure Image Builder サービスのアクセス許可を構成する](image-builder-permissions-cli.md)」または「[PowerShell を使用して Azure Image Builder サービスのアクセス許可を構成する](image-builder-permissions-powershell.md)」を参照してください

### <a name="build--step-failed-for-image-version"></a>イメージ バージョンのビルド ステップが失敗した

#### <a name="error"></a>エラー
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>原因

Azure Image Builder ではソース イメージを見つけることはできません。

#### <a name="solution"></a>解決策

ソース イメージが正しく、Azure Image Builder サービスの場所に存在していることを確認します。

### <a name="downloading-external-file-to-local-file"></a>外部ファイルのローカル ファイルへのダウンロード

#### <a name="error"></a>エラー

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>原因

ファイルの名前または場所が正しくないか、場所にアクセスできません。

#### <a name="solution"></a>解決策

ファイルにアクセスできることを確認します。 名前と場所が正しいことを確認します。

## <a name="troubleshoot-build-failures"></a>ビルド エラーのトラブルシューティング

イメージ ビルド エラーの場合は、`lastrunstatus` からエラーを取得した後、customization.log で詳細を確認できます。


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>カスタマイズ ログ

イメージのビルドが実行されているとき、ログが作成されてストレージ アカウントに格納されます。 イメージ テンプレート成果物を作成すると、Azure Image Builder により、一時リソース グループにストレージ アカウントが作成されます。

ストレージ アカウント名では、次のパターンが使用されます: **IT_\<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\>**

たとえば、*IT_aibmdi_helloImageTemplateLinux01* などです。

リソース グループのストレージ アカウントの customization.log を表示するには、 **[ストレージ アカウント]**  >  **[BLOB]**  > `packerlogs` を選択します。  次に、**directory > customization.log** を選択します。


### <a name="understanding-the-customization-log"></a>カスタマイズ ログの概要

ログは詳細です。 イメージ ビルドの、イメージ ディストリビューション (Shared Image Gallery レプリケーションなど) に関する問題などが含まれます。 これらのエラーは、イメージ テンプレートの状態のエラー メッセージで表示されます。

customization.log には次のステージが含まれます。

1. ARM テンプレートを使用して、ビルド VM と依存関係を、IT_ ステージング リソース グループ ステージにデプロイします。 このステージには、Azure Image Builder リソース プロバイダーへの複数の POST が含まれます。
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. デプロイ ステージの状態。 このステージには、各リソースのデプロイの状態が含まれます。
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. ビルド VM ステージに接続します。

    Windows の場合、Azure Image Builder サービスは WinRM を使用して接続します。
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    Linux の場合、Azure Image Builder サービスは SSH を使用して接続します。
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. カスタマイズ ステージを実行します。 カスタマイズが実行されたら、カスタマイズ ログを確認することでそれらを識別できます。 *(telemetry)* を検索します。
    ```text
    (telemetry) Starting provisioner windows-update
    (telemetry) ending windows-update
    (telemetry) Starting provisioner powershell
    (telemetry) ending powershell
    (telemetry) Starting provisioner file
    (telemetry) ending file
    (telemetry) Starting provisioner windows-restart
    (telemetry) ending windows-restart
    
    (telemetry) Finalizing. - This means the build hasfinished
    ```
5. プロビジョニング解除ステージ。 Azure Image Builder では、非表示のカスタマイザーが追加されます。 このプロビジョニング解除ステップでは、プロビジョニング解除のための VM の準備が行われます。 Windows の Sysprep (c:\DeprovisioningScript.ps1 を使用) または Linux の waagent deprovision (/tmp/DeprovisioningScript.sh を使用) が実行されます。 

    次に例を示します。
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. クリーンアップ ステージ。 ビルドが完了すると、Azure Image Builder のリソースが削除されます。
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>スクリプトとインライン カスタマイズのトラブルシューティングに関するヒント
- Azure VM Image Builder に渡す前にコードをテストします
- Azure Policy とファイアウォールでリモート リソースへの接続が許可されていることを確認します。
- `Write-Host` や `echo` などを使用して、コンソールにコメントを出力します。これにより、customization.log を検索できます。

## <a name="troubleshoot-common-build-errors"></a>一般的なビルド エラーのトラブルシューティング

### <a name="packer-build-command-failure"></a>Packer ビルド コマンドのエラー

#### <a name="error"></a>エラー

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-04-30T23:24:06.756985789Z",
   "endTime": "2020-04-30T23:39:14.268729811Z",
   "runState": "Failed",
   "message": "Failed while waiting for packerizer: Microservice has failed: Failed while processing request: Error when executing packerizer: Packer build command has failed: exit status 1. During the image build, a failure has occurred, please review the build log to identify which build/customization step failed. For more troubleshooting steps go to https://aka.ms/azvmimagebuilderts. Image Build log location: https://xxxxxxxxxx.blob.core.windows.net/packerlogs/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/customization.log. OperationId: xxxxxx-5a8c-4379-xxxx-8d85493bc791. Use this operationId to search packer logs."
```

#### <a name="cause"></a>原因

カスタマイズが失敗しました。

#### <a name="solution"></a>解決策

ログを確認して、カスタマイザーのエラーを見つけます。 *(telemetry)* を検索します。 

次に例を示します。
```text
(telemetry) Starting provisioner windows-update
(telemetry) ending windows-update
(telemetry) Starting provisioner powershell
(telemetry) ending powershell
(telemetry) Starting provisioner file
(telemetry) ending file
(telemetry) Starting provisioner windows-restart
(telemetry) ending windows-restart

(telemetry) Finalizing. - This means the build has finished
```

### <a name="timeout-exceeded"></a>タイムアウトを超えた

#### <a name="error"></a>エラー

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>原因

ビルドがビルドのタイムアウトを超えました。 このエラーは、"lastrunstatus" に表示されます。

#### <a name="solution"></a>解決策

1. customization.log を確認します。 実行する最後のカスタマイザーを特定します。 ログの一番下から `(telemetry)` を検索します。 

2. スクリプトのカスタマイズを確認します。 カスタマイズでは、`quiet` オプションなど、コマンドのユーザー操作が抑制されていない場合があります。 たとえば、`apt-get install -y` では、スクリプトの実行でユーザーの操作が待機されます。

3. `File` カスタマイザーを使用して 20 MB を超える成果物をダウンロードする場合は、回避策セクションを参照してください。

4. スクリプトが待機する原因になっている可能性のあるエラーと依存関係を確認します。

5. カスタマイズにもっと時間が必要であると予想される場合は、[buildTimeoutInMinutes](image-builder-json.md) を増やします。 既定値は 4 時間です。

6. ギガバイト規模のファイルのダウンロードなど、リソースを大量に消費する操作がある場合は、基になるビルド VM のサイズを検討します。 サービスでは、Standard_D1_v2 VM が使用されます。 VM には、1 個の vCPU と 3.5 GB のメモリがあります。 50 GB をダウンロードする場合、これでは VM リソースが枯渇し、Azure Image Builder サービスとビルド VM の間で通信エラーが発生する可能性があります。 [VM_Size](image-builder-json.md#vmprofile) を設定し、より大きなメモリ VM でビルドを再試行してください。

### <a name="long-file-download-time"></a>長いファイル ダウンロード時間

#### <a name="error"></a>エラー
```text
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 826 B / 826000 B  1.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 1652 B / 826000 B  2.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
..
hours later...
..
myBigFile.zip 826000 B / 826000 B  100.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
```
#### <a name="cause"></a>原因 

ファイル カスタマイザーで大きなファイルがダウンロードされています。

#### <a name="solution"></a>解決策

ファイル カスタマイザーは、20 MB 未満の小さいファイルのダウンロードにのみ適しています。 それより大きいファイルのダウンロードには、スクリプトまたはインライン コマンドを使用します。 たとえば、Linux では、`wget` や `curl` を使用できます。 Windows では、`Invoke-WebRequest` を使用できます。

### <a name="error-waiting-on-shared-image-gallery"></a>Shared Image Gallery での待機中のエラー

#### <a name="error"></a>エラー

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to shared image gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to shared image gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on shared image gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>原因

イメージが追加されて、Shared Image Gallery (SIG) にレプリケートされるのを待っている間に、Azure VM Image Builder がタイムアウトしました。 イメージが SIG に挿入されている場合は、イメージのビルドは成功したものと見なすことができます。 ただし、Azure VM Image Builder は共有イメージ ギャラリーでのレプリケーションが完了するのを待っていたため、プロセス全体は失敗しました。 ビルドが失敗した場合でも、レプリケーションは続行されます。 ディストリビューションの *runOutput* を調べることで、イメージ バージョンのプロパティを取得できます。

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2019-05-01-preview
```

#### <a name="solution"></a>解決策

**buildTimeoutInMinutes** を増やします。
 
### <a name="low-windows-resource-information-events"></a>Windows リソース不足の情報イベント

#### <a name="error"></a>エラー

```text
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 1% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 51% cpu; 35% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 36% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 90% cpu; 32% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for the Windows Modules Installer to exit...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'PowerShell -ExecutionPolicy Bypass -OutputFormat Text -File C:/Windows/Temp/packer-windows-update-elevated.ps1' exited with code: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Restarting the machine...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Waiting for machine to become available...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] starting remote command: shutdown.exe -f -r -t 0 -c "packer restart"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'shutdown.exe -f -r -t 0 -c "packer restart"' exited with code: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: A system shutdown is in progress.(1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] starting remote command: shutdown.exe -f -r -t 60 -c "packer restart test"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] command 'shutdown.exe -f -r -t 60 -c "packer restart test"' exited with code: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 Retryable error: Machine not yet available (exit status 1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT Build 'azure-arm' errored: unexpected EOF
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT 
```
#### <a name="cause"></a>原因

リソースが枯渇しています。 この問題は、既定のビルド VM サイズ D1_V2 を使用して実行されている Windows Update でよく見られます。

#### <a name="solution"></a>解決策

ビルド VM のサイズを増やします。

### <a name="builds-finished-but-no-artifacts-were-created"></a>ビルドは完了したが、成果物が作成されなかった

#### <a name="error"></a>エラー

```text
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT Build 'azure-arm' errored: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 machine readable: azure-arm,error []string{"Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded"}
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT --> azure-arm: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 Cancelling builder after context cancellation context canceled
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 [INFO] (telemetry) Finalizing.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:24 waiting for all plugin processes to complete...
Done exporting Packer logs to Azure for Packer prefix: [a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT
```
#### <a name="cause"></a>原因

必要な Azure リソースが作成されるのを待機している間に、タイムアウトが発生しました。

#### <a name="solution"></a>解決策

ビルドを再実行して、もう一度やり直してください。

### <a name="resource-not-found"></a>リソースが見つかりません

#### <a name="error"></a>エラー

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-05-01T00:13:52.599326198Z",
   "endTime": "2020-05-01T00:15:13.62366898Z",
   "runState": "Failed",
   "message": "network.InterfacesClient#UpdateTags: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. Status=404 Code=\"ResourceNotFound\" Message=\"The Resource 'Microsoft.Network/networkInterfaces/aibpls7lz2e.nic.4609d697-be0a-4cb0-86af-49b6fe877fe1' under resource group 'IT_aibImageRG200_window2019VnetTemplate01_9988723b-af56-413a-9006-84130af0e9df' was not found.\""
  },
```
#### <a name="cause"></a>原因

アクセス許可がありません。

#### <a name="solution"></a>解決策

Azure Image Builder に必要なアクセス許可がすべてあることを再確認します。 

アクセス許可の構成の詳細については、「[Azure CLI を使用して Azure Image Builder サービスのアクセス許可を構成する](image-builder-permissions-cli.md)」または「[PowerShell を使用して Azure Image Builder サービスのアクセス許可を構成する](image-builder-permissions-powershell.md)」を参照してください

### <a name="sysprep-timing"></a>Sysprep のタイミング

#### <a name="error"></a>エラー

```text
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service RdAgent) -and ((Get-Service RdAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprepping VM ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: & $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while($true) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Write-Output $imageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Start-Sleep -s 5
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprep complete ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (RdAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Sysprepping VM ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_COMPLETE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: Get-Service : Cannot find any service with service name 'WindowsAzureGuestAgent'.
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: At C:\DeprovisioningScript.ps1:6 char:9
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: + while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service Window ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: +         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + CategoryInfo          : ObjectNotFound: (WindowsAzureGuestAgent:String) [Get-Service], ServiceCommandException
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + FullyQualifiedErrorId : NoServiceFoundForGivenName,Microsoft.PowerShell.Commands.GetServiceCommand
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 Cancelling builder after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT Cancelling build after receiving terminated
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling provisioning due to context cancellation: context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: 
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling hook after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
```
#### <a name="cause"></a>原因

D1_V2 VM のサイズによるタイミングの問題が原因である可能性があります。 カスタマイズが制限され、3 秒未満で実行される場合、プロビジョニング解除のために Azure Image Builder によって sysprep コマンドが実行されます。 Azure Image Builder のプロビジョニング解除時に、sysprep コマンドによって *WindowsAzureGuestAgent* がチェックされますが、完全にインストールされていなくてタイミングの問題の原因になる可能性があります。 

#### <a name="solution"></a>解決策

VM のサイズを増やします。 または、60 秒の PowerShell スリープ カスタマイズを追加して、タイミングの問題を回避することもできます。

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>コンテキストの取り消しコンテキストの取り消し後にビルダーがキャンセルされる

#### <a name="error"></a>エラー
```text
PACKER ERR 2020/03/26 22:11:23 Cancelling builder after context cancellation context canceled
PACKER OUT Cancelling build after receiving terminated
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling hook after context cancellation context canceled
..
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling provisioning due to context cancellation: context canceled
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [ERROR] Remote command exited without exit status or exit signal.
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [INFO] RPC endpoint: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] 148974 bytes written for 'stdout'
PACKER ERR 2020/03/26 22:11:25 [INFO] 0 bytes written for 'stderr'
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC client: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC endpoint: Communicator ended with: 2300218
```
#### <a name="cause"></a>原因
Image Builder サービスは、ポート 22 (Linux) または 5986 (Windows) を使用してビルド VM に接続します。これはイメージのビルド中にサービスがビルド VM から切断された場合に発生します。 切断の理由はさまざまですが、スクリプトでファイアウォールを有効にしたり構成したりすると、上記のポートがブロックされる可能性があります。

#### <a name="solution"></a>解決策
ファイアウォールの変更または有効化、あるいは SSH または WinRM への変更についてスクリプトを確認し、上記のポートでサービスとビルド VM の間の常時接続を確立できるように変更してください。 Image Builder のネットワークの詳細については、[要件](./image-builder-networking.md)に関する記事を確認してください。

## <a name="devops-task"></a>DevOps タスク 

### <a name="troubleshooting-the-task"></a>タスクのトラブルシューティング
タスクは、カスタマイズ中にエラーが発生した場合にのみ失敗します。この場合、タスクはエラーを報告し、ログを残してステージング リソース グループを抜けるので、問題を特定できます。 

ログを見つけるには、テンプレート名を知る必要があります。パイプライン、失敗したビルドの順に移動し、AIB DevOps タスクにドリルダウンすると、ログとテンプレート名が表示されます。
```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
``` 

ポータルに移動し、リソース グループでテンプレート名を検索し、IT_* を含むリソース グループを探します。
ストレージ アカウント、BLOB、コンテナー、ログの順に移動します。

### <a name="troubleshooting-successful-builds"></a>成功したビルドのトラブルシューティング
場合によっては、成功したビルドを調査し、ログを確認する必要があります。 前述のように、イメージのビルドが成功した場合、ログを含むステージング リソース グループはクリーンアップの一部として削除されます。 ただし、インライン コマンドの後にスリープを挿入し、ビルドが一時停止している間にログを取得することがきます。 そのためには、次の手順のようにします。
 
1. インライン コマンドを更新し、次を追加します: Write-Host / Echo "Sleep" – これにより、ログを検索できます
2. 少なくとも 10 分のスリープを追加します。[Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep) または `Sleep` Linux コマンドを使用できます。
3. 上のメソッドを使用してログの場所を見つけ、スリープ状態になるまでログのダウンロードと確認を続けます。


### <a name="operation-was-canceled"></a>操作が取り消されました

#### <a name="error"></a>エラー

```text
2020-05-05T18:28:24.9280196Z ##[section]Starting: Azure VM Image Builder Task
2020-05-05T18:28:24.9609966Z ==============================================================================
2020-05-05T18:28:24.9610739Z Task         : Azure VM Image Builder Test(Preview)
2020-05-05T18:28:24.9611277Z Description  : Build images using Azure Image Builder resource provider.
2020-05-05T18:28:24.9611608Z Version      : 1.0.18
2020-05-05T18:28:24.9612003Z Author       : Microsoft Corporation
2020-05-05T18:28:24.9612718Z Help         : For documentation, and end to end example, please visit: http://aka.ms/azvmimagebuilderdevops
2020-05-05T18:28:24.9613390Z ==============================================================================
2020-05-05T18:28:26.0651512Z start reading task parameters...
2020-05-05T18:28:26.0673377Z found build at:  d:\a\r1\a\_AppsAndImageBuilder\webApp
2020-05-05T18:28:26.0708785Z end reading parameters
2020-05-05T18:28:26.0745447Z getting storage account details for aibstagstor1565047758
2020-05-05T18:28:29.8812270Z created archive d:\a\_temp\temp_web_package_09737279437949953.zip
2020-05-05T18:28:33.1568013Z Source for image:  { type: 'PlatformImage',
2020-05-05T18:28:33.1584131Z   publisher: 'MicrosoftWindowsServer',
2020-05-05T18:28:33.1585965Z   offer: 'WindowsServer',
2020-05-05T18:28:33.1592768Z   sku: '2016-Datacenter',
2020-05-05T18:28:33.1594191Z   version: '14393.3630.2004101604' }
2020-05-05T18:28:33.1595387Z template name:  t_1588703313152
2020-05-05T18:28:33.1597453Z starting put template...
2020-05-05T18:28:52.9278603Z put template:  Succeeded
2020-05-05T18:28:52.9281282Z starting run template...
2020-05-05T19:33:14.3923479Z ##[error]The operation was canceled.
2020-05-05T19:33:14.3939721Z ##[section]Finishing: Azure VM Image Builder Task
```
#### <a name="cause"></a>原因

ビルドがユーザーによって取り消されなかった場合は、Azure DevOps ユーザー エージェントによって取り消されました。 最も可能性が高いのは、Azure DevOps の機能により、1 時間のタイムアウトが発生した場合です。 プライベート プロジェクトとエージェントを使用している場合は、60 分のビルド時間が得られます。 ビルドがタイムアウトを超えた場合、DevOps によって実行中のタスクが取り消されます。

Azure DevOps の機能と制限事項の詳細については、「[Microsoft によってホストされるエージェント](/azure/devops/pipelines/agents/hosted#capabilities-and-limitations)」を参照してください
 
#### <a name="solution"></a>解決策

独自の DevOps エージェントをホストするか、ビルドの時間を短縮することができます。 たとえば、Shared Image Gallery に配布している場合は、1 つのリージョンにレプリケートします。 非同期的にレプリケートする場合。 

### <a name="slow-windows-logon-please-wait-for-the-windows-modules-installer"></a>Windows ログオンが遅い:'Windows モジュール インストーラーの処理が完了するのをお待ちください'

#### <a name="error"></a>エラー
Image Builder を使用して Windows 10 イメージを作成し、そのイメージから VM を作成した後、RDP を使用すると、最初のログオン時に次のメッセージとブルー スクリーンが表示され、数分待つ必要があります。
```text
Please wait for the Windows Modules Installer
```

#### <a name="solution"></a>解決策
まず、イメージのビルドでは、最後のカスタマイズとして Windows 再起動カスタマイザーを追加することで、必要な未完了が存在しないことと、すべてのソフトウェアのインストールが完了していることを確認します。 最後に、AIB に使用される既定の sysprep に [/mode:vm](/windows-hardware/manufacture/desktop/sysprep-command-line-options) オプションを追加します。後述する「AIB イメージから作成される VM が正常に作成されない」 > 「コマンドのオーバーライド」を参照してください。  

 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>AIB イメージから作成される VM が正常に作成されない

既定の Azure Image Builder では、イメージを "*一般化*" するため、各イメージ カスタマイズ フェーズの最後に "*プロビジョニング解除*" コードが実行されます。 一般化とは、複数の VM の作成に再利用されるようにイメージを設定し、ホスト名やユーザー名などの VM の設定を渡すことができるようにするプロセスです。Azure Image Builder では、Windows の場合は *Sysprep* が実行され、Linux の場合は `waagent -deprovision` が実行されます。 

Windows の場合、Azure Image Builder では汎用の Sysprep コマンドが使用されます。 ただし、これは Windows のすべての一般化を成功させるのに適しているとは限りません。 Azure Image Builder では、Sysprep コマンドをカスタマイズできます。 Azure Image Builder はイメージ自動化ツールであることに注意してください。 Sysprep コマンドを正常に実行させる責任があります。 ただし、イメージを再利用できるようにするには、異なる Sysprep コマンドが必要になる場合があります。 Linux の場合、Azure Image Builder では汎用の `waagent -deprovision+user` コマンドが使用されます。 詳細については、[Microsoft Azure Linux エージェントのドキュメント](https://github.com/Azure/WALinuxAgent#command-line-options)を参照してください。

既存のカスタマイズを移行していて、異なる Sysprep または waagent コマンドを使用している場合は、Azure VM Image Builder の汎用コマンドを使用してみることができます。 VM の作成が失敗する場合は、前の Sysprep または waagent コマンドを使用します。

> [!NOTE]
> AIB によって Windows のカスタム イメージが作成され、それから VM を作成した後、VM が正常に作成されないことを確認した場合は (たとえば、VM 作成コマンドが完了しない、またはタイムアウトする)、Windows Server Sysprep のドキュメントを確認する必要があります。 または、Windows Server Sysprep のカスタマー サービス サポート チームにサポート リクエストを提出し、正しい Sysprep コマンドに関するトラブルシューティングとアドバイスを受けることもできます。

### <a name="command-locations-and-filenames"></a>コマンドの場所とファイル名

Windows:

```
c:\DeprovisioningScript.ps1
```

Linux:
```bash
/tmp/DeprovisioningScript.sh
```

### <a name="sysprep-command-windows"></a>Sysprep コマンド: Windows

```PowerShell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```

### <a name="deprovision-command-linux"></a>プロビジョニング解除コマンド: Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

### <a name="overriding-the-commands"></a>コマンドのオーバーライド

コマンドをオーバーライドするには、PowerShell またはシェル スクリプトのプロビジョナーを使って正確なファイル名のコマンド ファイルを作成し、前に示したディレクトリに置きます。 Azure Image Builder によってこれらのコマンドが読み取られ、出力が *customization.log* に書き込まれます。

## <a name="getting-support"></a>サポートの入手
ガイダンスを参照しても問題を解決できない場合は、サポート ケースを開くことができます。 それを行うときは、適切な製品とサポート トピックを選択してください。これにより、Azure VM Image Builder のサポート チームが手配されます。

ケース製品を選択します。
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>次の手順

詳細については、[Azure Image Builder の概要](../image-builder-overview.md)に関する記事を参照してください。
