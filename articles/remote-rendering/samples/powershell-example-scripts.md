---
title: PowerShell スクリプトの例
description: PowerShell スクリプトを介してフロントエンドを使用する方法を示す例
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 53f09dd042543ebc433e9d9cbbd65db332e8fb6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518213"
---
# <a name="example-powershell-scripts"></a>PowerShell スクリプトの例

Azure Remote Rendering には、次の 2 つの REST API が用意されています。

- [変換 REST API](../how-tos/conversion/conversion-rest-api.md)
- [セッション REST API](../how-tos/session-rest-api.md)

[ARR サンプル リポジトリ](https://github.com/Azure/azure-remote-rendering)には、*Scripts* フォルダーに、サービスの REST API を操作するためのサンプル スクリプトがあります。 この記事では、その使用方法について説明します。

> [!TIP]
> また、サービスと対話するための [ARRT と呼ばれる UI ベースのツール](azure-remote-rendering-asset-tool.md)もあります。これは、スクリプトの使用に代わる便利な方法です。 ![ARRT](./media/azure-remote-rendering-asset-tool.png "ARRT のスクリーンショット")

> [!CAUTION]
> REST API 関数を頻繁に呼び出すと、サーバーが調整され、最終的にエラーが返されます。 この場合の http エラーコード ID は 429 ("要求が多すぎます") です。 経験則として、**次の呼び出しとの間に 5 秒から 10 秒** の間隔が必要です。

## <a name="prerequisites"></a>前提条件

サンプル スクリプトを実行するには、[Azure PowerShell](/powershell/azure/) の機能設定が必要です。

1. Azure PowerShell をインストールします。
    1. 管理者権限で PowerShell ウィンドウを開きます。
    1. `Install-Module -Name Az -AllowClobber` を実行します。

1. スクリプトの実行に関するエラーが発生した場合は、[実行ポリシー](/powershell/module/microsoft.powershell.core/about/about_execution_policies)が適切に設定されていることを確認してください。
    1. 管理者権限で PowerShell ウィンドウを開きます。
    1. `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` を実行します。

1. [Azure Storage アカウントを準備します](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Azure Remote Rendering アカウントを含むサブスクリプションにログインします。
    1. PowerShell ウィンドウを開きます。
    1. `Connect-AzAccount` を実行し、画面の指示に従います。

    > [!NOTE]
    > 組織に複数のサブスクリプションがある場合は、SubscriptionId および Tenant 引数の指定が必要になることがあります。 詳細については、[Connect-AzAccount のドキュメント](/powershell/module/az.accounts/connect-azaccount)を参照してください。

1. [Azure Remote Rendering GitHub リポジトリ](https://github.com/Azure/azure-remote-rendering)から *Scripts* フォルダーをダウンロードします。

## <a name="configuration-file"></a>構成ファイル

`.ps1` ファイルの次に `arrconfig.json` があり、これに入力する必要があります。

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> LocalAssetDirectoryPath パスのバックスラッシュは、二重のバックスラッシュ "\\\\" を使用して正しくエスケープするようにし、inputFolderPath や inputAssetPath などの他のすべてのパスにはスラッシュ "/" を使用するようにしてください。

> [!CAUTION]
> オプションの値を入力するか、キーと値をすべて削除する必要があります。 たとえば、`"outputAssetFileName"` パラメーターを使用しない場合は、`arrconfig.json` 内の行全体を削除する必要があります。

### <a name="accountsettings"></a>accountSettings

`arrAccountId` と `arrAccountKey` については、「[Azure Remote Rendering アカウントを作成する](../how-tos/create-an-account.md)」を参照してください。
`region` については、[利用可能なリージョンの一覧](../reference/regions.md)を参照してください。

### <a name="renderingsessionsettings"></a>renderingSessionSettings

**RenderingSession.ps1** を実行する場合は、この構造を入力する必要があります。

- **vmSize:** 仮想マシンのサイズを選択します。 [*Standard*](../reference/vm-sizes.md) または [*Premium*](../reference/vm-sizes.md) を選択します。 不要になったレンダリング セッションをシャットダウンします。
- **maxLeaseTime:** VM をリースする期間です。 リースの有効期限が切れると、シャットダウンされます。 リース時間は後で延長できます (下記参照)。

### <a name="assetconversionsettings"></a>assetConversionSettings

**Conversion.ps1** を実行する場合は、この構造を入力する必要があります。

詳細については、[Azure Storage アカウントの準備](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)に関する記事を参照してください。

## <a name="script-renderingsessionps1"></a>スクリプト:RenderingSession.ps1

このスクリプトは、レンダリング セッションを作成、クエリ、および停止するために使用されます。

> [!IMPORTANT]
> arrconfig.json の *accountSettings* と *renderingSessionSettings* セクションを入力したことを確認してください。

### <a name="create-a-rendering-session"></a>レンダリング セッションを作成する

完全に入力した arrconfig. json での標準的な使用方法:

```PowerShell
.\RenderingSession.ps1
```

このスクリプトは、[セッション管理 REST API](../how-tos/session-rest-api.md) を呼び出して、指定された設定でレンダリング VM を起動します。 成功すると、*sessionId* が取得されます。 その後、セッションの準備が完了するか、エラーが発生するまで、セッションのプロパティがポーリングされます。

**代替の構成** ファイルを使用するには、次のようにします。

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

構成ファイルから **個々の設定をオーバーライドする** ことができます。

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

**ポーリングせずにセッションの開始のみを行う** には、以下を使用します。

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

スクリプトが取得する *sessionId* は、他のほとんどのセッション コマンドに渡す必要があります。

### <a name="retrieve-session-properties"></a>セッションのプロパティを取得する

セッションのプロパティを取得するには、次のように実行します。

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

`-Poll` を使用して、セッションの "*準備が完了*" するか、エラーが発生するまで待機します。

### <a name="list-active-sessions"></a>アクティブなセッションを一覧表示する

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>セッションを停止する

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>セッションのプロパティを変更する

現時点では、セッションの maxLeaseTime の変更のみがサポートされています。

> [!NOTE]
> リース時間は、セッション VM が最初に作成された時点から常にカウントされます。 そのため、セッション リースをさらに 1 時間延長するには、*maxLeaseTime* を 1 時間増やします。

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>スクリプト:Conversion.ps1

このスクリプトは、入力モデルを Azure Remote Rendering 固有のランタイム形式に変換するために使用されます。

> [!IMPORTANT]
> arrconfig.json の *accountSettings* と *assetConversionSettings* セクションを入力したことを確認してください。

このスクリプトは、サービスでストレージ アカウントを使用する 2 つのオプションを示しています。

- Azure Remote Rendering アカウントにリンクされているストレージ アカウント
- Shared Access Signature (SAS) 経由のストレージへのアクセスを提供する

### <a name="linked-storage-account"></a>リンクされたストレージ アカウント

arrconfig.json を完全に入力し、ストレージ アカウントをリンクすると、次のコマンドを使用できます。 ストレージ アカウントのリンクについては、「[アカウントの作成](../how-tos/create-an-account.md#link-storage-accounts)」に記載されています。

Shared Access Signature を生成する必要がないため、変換サービスの使用には、リンクされたストレージ アカウントを使用することをお勧めします。

```PowerShell
.\Conversion.ps1
```

1. `assetConversionSettings.modelLocation` に格納されているすべてのファイルを、指定した `inputFolderPath` の下の入力 BLOB コンテナーにアップロードします。
1. [モデルの変換 REST API](../how-tos/conversion/conversion-rest-api.md) を呼び出して、[モデルの変換](../how-tos/conversion/model-conversion.md)を開始します
1. 変換が成功または失敗するまで変換ステータスをポーリングします。
1. 変換されたファイルの場所の詳細 (ストレージ アカウント、出力コンテナー、コンテナー内のファイルパス) を出力します。

### <a name="access-to-storage-via-shared-access-signatures"></a>Shared Access Signature 経由のストレージへのアクセス

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

リセットすると、以下のようになります。

1. `assetConversionSettings.localAssetDirectoryPath` から入力 BLOB コンテナーにローカル ファイルをアップロードします。
1. 入力コンテナーの SAS URI を生成します。
1. 出力コンテナーの SAS URI を生成します。
1. [モデルの変換 REST API](../how-tos/conversion/conversion-rest-api.md) を呼び出して、[モデルの変換](../how-tos/conversion/model-conversion.md)を開始します。
1. 変換が成功または失敗するまで変換ステータスをポーリングします。
1. 変換されたファイルの場所の詳細 (ストレージ アカウント、出力コンテナー、コンテナー内のファイルパス) を出力します。
1. 出力 BLOB コンテナー内の変換されたモデルの SAS URI を出力します。

### <a name="additional-command-line-options"></a>追加のコマンド ライン オプション

**代替の構成** ファイルを使用するには、次のようにします。

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

**ポーリングせずにモデルの変換の開始のみを行う** には、以下を使用できます。

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

次のコマンド ライン スイッチを使用して、構成ファイルから **個々の設定をオーバーライドする** ことができます。

* **Id:** GetConversionStatus で使用される ConversionId
* **ArrAccountId:** accountSettings の arrAccountId
* **ArrAccountKey:** accountSettings の arrAccountKey のオーバーライド
* **Region:** accountSettings の region のオーバーライド
* **ResourceGroup:** assetConversionSettings の resourceGroup のオーバーライド
* **StorageAccountName:** assetConversionSettings の storageAccountName のオーバーライド
* **BlobInputContainerName:** assetConversionSettings の blobInputContainer のオーバーライド
* **LocalAssetDirectoryPath:** assetConversionSettings のlocalAssetDirectoryPath のオーバーライド
* **InputAssetPath:** assetConversionSettings の inputAssetPath のオーバーライド
* **BlobOutputContainerName:** assetConversionSettings の BlobOutputContainerName のオーバーライド
* **OutputFolderPath:** assetConversionSettings の outputFolderPath のオーバーライド
* **OutputAssetFileName:** assetConversionSettings の outputAssetFileName のオーバーライド

たとえば、次のように指定のオプションを複数組み合わせることができます。

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>個々の変換ステージを実行する

プロセスの個々のステップを実行する場合は、以下を使用できます。

指定された LocalAssetDirectoryPath からのデータのアップロードのみ行います。

```PowerShell
.\Conversion.ps1 -Upload
```

BLOB ストレージに既にアップロードされているモデルの変換プロセスの開始のみを行います (アップロードは実行せず、変換の状態はポーリングしません)。このスクリプトは、*conversionId* を返します。

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

また、以下を使用して、この変換の変換状態を取得できます。

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

`-Poll` を使用して、変換が完了するか、エラーが発生するまで待機します。

## <a name="next-steps"></a>次のステップ

- [クイック スタート: Unity によるモデルのレンダリング](../quickstarts/render-model.md)
- [クイック スタート: モデルをレンダリング用に変換する](../quickstarts/convert-model.md)
- [モデルの変換](../how-tos/conversion/model-conversion.md)