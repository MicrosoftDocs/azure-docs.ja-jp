---
title: 新しい更新プログラムのインポート方法 | Microsoft Docs
description: 新しい更新プログラムを IoT Hub の Device Update for IoT Hub にインポートするための How-To ガイド。
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: b9d40848abdd85beeca592001b697e3c50b7cd59
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008564"
---
# <a name="import-new-update"></a>新しい更新プログラムのインポート
新しい更新プログラムを Device Update for IoT Hub にインポートする方法について説明します。 基本的な[インポートの概念](import-concepts.md)をまだ理解していない場合は、理解しておいてください。

## <a name="prerequisites"></a>前提条件

* [Device Update for IoT Hub が有効になっている IoT ハブにアクセスできること](create-device-update-account.md)。 IoT ハブには、S1 (Standard) レベル以上を使用することをお勧めします。 
* Device Update 用にプロビジョニングされている、IoT Hub 内の IoT デバイス (またはシミュレーター)。
   * 実際のデバイスを使用する場合は、イメージ更新用の更新イメージ ファイル、またはパッケージの更新用の [APT マニフェスト ファイル](device-update-apt-manifest.md)が必要です。
* [PowerShell 5](https://docs.microsoft.com/powershell/scripting/install/installing-powershell) 以降。
* サポートされているブラウザー:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> このサービスに送信された一部のデータは、このインスタンスが作成されたリージョン外のリージョンで処理される可能性があります。

## <a name="create-device-update-import-manifest"></a>Device Update のインポート マニフェストを作成する

1. 更新イメージ ファイルまたは APT マニフェスト ファイルが、PowerShell からアクセスできるディレクトリに配置されていることを確認します。

2. 更新イメージファイルまたは APT マニフェスト ファイルが配置されているディレクトリに、 **AduUpdate.psm1** という名前のテキスト ファイルを作成します。 次に、 [AduUpdate.psm1](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) PowerShell コマンドレットを開き、内容をテキスト ファイルにコピーして、テキスト ファイルを保存します。

3. PowerShell で、手順 2 で作成した PowerShell コマンドレットを作成したディレクトリに移動します。 次に、次のコマンドを実行します。

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. サンプルのパラメーター値を置き換えて次のコマンドを実行し、インポート マニフェスト (更新内容を記述する JSON ファイル) を生成します。
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    参考として、次に前述のパラメーター値の例を示します。 完成した[インポート マニフェスト スキーマ](import-schema.md)で詳細を確認することもできます。

    | パラメーター | 説明 |
    | --------- | ----------- |
    | deviceManufacturer | 更新プログラムと互換性があるデバイスの製造元 (Contoso など)。 _製造元_ の[デバイス プロパティ](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-plug-and-play#device-properties)と一致する必要があります。
    | deviceModel | 更新プログラムと互換性があるデバイスのモデル (toaster など)。 _モデル_ の[デバイス プロパティ](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-plug-and-play#device-properties)と一致する必要があります。
    | updateProvider | 更新プログラムを作成または直接担当するエンティティ。 多くの場合、会社名になります。
    | updateName | 更新プログラムのクラスの識別子。 クラスには、任意のものを選択できます。 多くの場合、デバイス名またはモデル名になります。
    | updateVersion | 同じプロバイダーと名前を持つ他の更新プログラムとこの更新プログラムを区別するためのバージョン番号。 デバイス上の個々のソフトウェア コンポーネントのバージョンと一致しない場合があります (選択した場合は可能です)。
    | updateType | <ul><li>イメージの更新には `microsoft/swupdate:1` を指定します</li><li>パッケージの更新には `microsoft/apt:1` を指定します</li></ul>
    | installedCriteria | <ul><li>更新の種類 `microsoft/swupdate:1` には SWVersion の値を指定します</li><li>更新の種類 `microsoft/apt:1` には推奨値を指定します
    | updateFilePath(s) | コンピューター上の更新プログラム ファイルへのパス


## <a name="review-generated-import-manifest"></a>生成されたインポート マニフェストを確認する

例:
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-update"></a>更新プログラムのインポート

[!NOTE]
次の手順は、Azure portal ユーザー インターフェイスを使用して更新プログラムをインポートする方法を示しています。 [IoT Hub API のデバイス更新](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) プログラムを使用して、更新プログラムをインポートすることもできます。 

1. [Azure portal](https://portal.azure.com) にログインし、Device Update がある IoT Hub に移動します。

2. ページの左側で、[デバイスの自動管理] の下にある [デバイスの更新プログラム] を選択します。

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="更新プログラムをインポートします" lightbox="media/import-update/import-updates-3.png":::

3. 画面の上部に複数のタブが表示されます。 [更新プログラム] タブを選択します。

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="更新プログラム" lightbox="media/import-update/updates-tab.png":::

4. [デプロイの準備完了] ヘッダーの下にある [+ 新しい更新プログラムのインポート] を選択します。

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="新しい更新プログラムをインポートします" lightbox="media/import-update/import-new-update-2.png":::

5. [インポート マニフェスト ファイルを選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 ファイル ピッカーのダイアログが表示されます。 PowerShell コマンドレットを使用して、さきほど作成したインポート マニフェストを選択します。 次に、[1 つまたは複数の更新プログラム ファイルの選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 ファイル ピッカーのダイアログが表示されます。 更新プログラム ファイルを選択します。

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="更新プログラム ファイルを選択します" lightbox="media/import-update/select-update-files.png":::

6. [ストレージ コンテナーの選択] の下にあるフォルダー アイコンまたはテキスト ボックスを選択します。 次に、適切なストレージ アカウントを選択します。 ストレージ コンテナーは、更新プログラム ファイルを一時的にステージするために使用されます。

   :::image type="content" source="media/import-update/storage-account.png" alt-text="ストレージ アカウント" lightbox="media/import-update/storage-account.png":::

7. コンテナーを既に作成してある場合は、再利用できます (そうでない場合は、[+ コンテナー] を選択して、更新用の新しいストレージ コンテナーを作成します)。  使用するコンテナーを選択し、[選択] をクリックします。

   :::image type="content" source="media/import-update/container.png" alt-text="コンテナーを選択します" lightbox="media/import-update/container.png":::

8. [送信] を選択してインポート プロセスを開始します。

   :::image type="content" source="media/import-update/publish-update.png" alt-text="更新プログラムを発行する" lightbox="media/import-update/publish-update.png":::

9. インポート プロセスが開始され、画面が [インポートの履歴] セクションに切り替わります。 インポート処理が完了するまでの進行状況を表示するには、[更新] を選択します (更新プログラムのサイズによっては、数分で完了することも、長時間かかることもあります)。

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="インポート シーケンスを更新します" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. [状態] 列にインポートが正常に完了したと表示されたら、[デプロイの準備完了] ヘッダーを選択します。 インポートされた更新プログラムが一覧に表示されるはずです。

   :::image type="content" source="media/import-update/update-ready.png" alt-text="ジョブの状態" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>次の手順

[グループを作成する](create-update-group.md)

[インポートの概念について学習する](import-concepts.md)
