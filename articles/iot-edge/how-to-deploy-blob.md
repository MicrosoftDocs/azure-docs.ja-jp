---
title: Azure Blob Storage モジュールをデバイスにデプロイする - Azure IoT Edge | Microsoft Docs
description: Azure Blob Storage モジュールをご利用の IoT Edge デバイスにデプロイして、そのエッジにデータを格納します。
author: arduppal
ms.author: arduppal
ms.date: 06/19/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: mchad
ms.openlocfilehash: 468e4fca5e67850949e7d5826e4bc88fa504b9d6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295184"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>IoT Edge モジュール上の Azure Blob Storage を自分のデバイスにデプロイする

IoT Edge デバイスにモジュールをデプロイするにはいくつかの方法があり、これらすべてが IoT Edge モジュールの Azure Blob Storage で機能します。 2 つの最も簡単な方法では、Azure portal または Visual Studio Code テンプレートを使用します。

> [!NOTE]
> IoT Edge の Azure Blob Storage は、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)中です。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。
- IoT Edge ランタイムがインストールされた [IoT Edge デバイス](how-to-register-device-portal.md)。
- Visual Studio Code からデプロイする場合は、[Visual Studio Code](https://code.visualstudio.com/) と [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。

## <a name="deploy-from-the-azure-portal"></a>Azure portal からデプロイする

Azure portal では、配置マニフェストの作成から、IoT Edge デバイスへのデプロイのプッシュまでをガイドします。

### <a name="select-your-device"></a>デバイスを選択する

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。
1. メニューから **[IoT Edge]** を選択します。
1. デバイスの一覧でターゲット デバイスの ID をクリックします。
1. **[Set Modules] \(モジュールの設定)** を選択します。

### <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 Azure portal には、JSON ドキュメントを手動で作成する代わりに配置マニフェストを作成する手順を示すウィザードがあります。 3 つのステップがあります。**モジュールの追加**、**ルートの指定**、および**デプロイの確認**。

#### <a name="add-modules"></a>モジュールを追加する

1. ページの **[Deployment modules]\(デプロイ モジュール\)** セクションで、 **[追加]** を選択します。

1. モジュールの種類のドロップダウン リストから、 **[IoT Edge モジュール]** を選択します。

1. モジュールの名前を入力し、コンテナー イメージを指定します。

   - **名前**: azureblobstorageoniotedge
   - **イメージの URI**: mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > Azure IoT Edge は、モジュールを呼び出すときに大文字と小文字を区別し、Storage SDK も既定で小文字になります。 [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) でのモジュールの名前は **AzureBlobStorageonIoTEdge** ですが、名前を小文字に変更すると、IoT Edge モジュールの Azure Blob Storage への接続が中断されないことが保証されます。

1. 既定の **[コンテナーの作成オプション]** の値により、コンテナーで必要なポート バインドが定義されますが、ストレージ アカウント情報と、デバイス上のストレージ ディレクトリのバインドも追加する必要があります。 ポータル内の JSON を以下の JSON で置き換えます。

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage directory bind>"
       ],
     "PortBindings":{
       "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. コピーした JSON を次の情報で更新します。

   - `<your storage account name>` を覚えやすい名前に置き換えます。 アカウント名は、小文字と数字の 3 文字から 24 文字の長さにする必要があります。 スペースは含めません。

   - `<your storage account key>` を 64 バイトの Base64 キーに置き換えます。 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) などのツールを使ってキーを生成できます。 他のモジュールから BLOB ストレージにアクセスするには、これらの資格情報を使用します。

   - コンテナーのオペレーティング システムに応じて `<storage directory bind>` を置き換えます。 そのデータを格納する BLOB モジュールが必要な[ボリューム](https://docs.docker.com/storage/volumes/)の名前またはご利用の IoT Edge デバイス上のディレクトリへの絶対パスを指定します。 ストレージ ディレクトリのバインドは、提供したデバイス上の位置をモジュール内の設定された位置にマップします。

     - Linux コンテナーの場合、形式は *\<ストレージのパス>:/blobroot* です。 例: **/srv/containerdata:/blobroot** または **my-volume:/blobroot**。
     - Windows コンテナーの場合、形式は *\<ストレージのパス>:C:/BlobRoot* です。 例: **C:/ContainerData:C:/BlobRoot** または **my-volume:C:/blobroot**。

     > [!IMPORTANT]
     > モジュールの特定の位置を指す、ストレージ ディレクトリのバインド値の後半を変更しないでください。 ストレージ ディレクトリ バインドは常に、Linux コンテナーの場合は **:/blobroot** で、Windows コンテナーの場合は **:C:/BlobRoot** 終わる必要があります。

1. 次の JSON をコピーして、 **[モジュール ツインの必要なプロパティの設定]** ボックスに貼り付けて、モジュールの [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) プロパティと [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) プロパティを設定します。 各プロパティを適切な値で構成して保存し、デプロイを続行します。

   ```json
   {
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading":<true,false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload":<true,false>
       }
     }
   }

      ```

   ![コンテナー作成オプション、deviceAutoDeleteProperties およびdeviceToCloudUploadProperties プロパティを設定する](./media/how-to-deploy-blob/iotedge-custom-module.png)

   モジュールがデプロイされた後に deviceToCloudUploadProperties と deviceAutoDeleteProperties を構成する方法については、[モジュール ツインの編集](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)に関する記事を参照してください。 目的のプロパティに関する詳細情報は、「[必要なプロパティの定義または更新](module-composition.md#define-or-update-desired-properties)」をご覧ください。

1. **[保存]** を選択します。

1. **[次へ]** を選択して、ルートのセクションに進みます。

#### <a name="specify-routes"></a>ルートを指定する

既定のルートを保持し、 **[次へ]** を選択してレビューのセクションに進みます。

#### <a name="review-deployment"></a>デプロイを確認する

確認のセクションには、前述の 2 つのセクションの選択項目に基づいて作成された JSON 配置マニフェストが表示されます。 追加しなかった 2 つのモジュール ( **$edgeAgent** および **$edgeHub**) もあります。 これらの 2 つのモジュールは、[IoT Edge ランタイム](iot-edge-runtime.md)を構成し、すべてのデプロイの既定値として使用されます。

デプロイ情報を確認し、 **[送信]** を選択します。

### <a name="verify-your-deployment"></a>デプロイを確認する

デプロイを送信した後、IoT ハブの **IoT Edge** のページに戻ります。

1. デプロイで対象にした Azure IoT Edge デバイスを選択して、その詳細を開きます。
1. デバイスの詳細で、BLOB ストレージ モジュールが **[デプロイで指定]** および **[デバイス別に報告]** の両方として一覧表示されていることを確認します。

モジュールがデバイス上で開始してから IoT Hub にレポートされるまでしばらく時間がかかる場合があります。 ページを更新して、最新の状態を表示します。

## <a name="deploy-from-visual-studio-code"></a>Visual Studio Code からのデプロイ

Azure IoT Edge では、エッジ ソリューションの開発に役立つ、Visual Studio Code のテンプレートが提供されます。 次の手順を使って、BLOB ストレージ モジュールで新しい IoT Edge ソリューションを作成し、配置マニフェストを構成します。

1. **[ビュー]**  >  **[コマンド パレット]** の順に選択します。

1. コマンド パレットで、**Azure IoT Edge:New IoT Edge solution** コマンドを入力して実行します。

   ![新しい IoT Edge ソリューションを実行する](./media/how-to-develop-csharp-module/new-solution.png)

   コマンド パレットに表示されるメッセージに従って、ソリューションを作成します。

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | Visual Studio Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | ソリューションのためにわかりやすい名前を入力するか、既定値の **EdgeSolution** をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **既存のモジュール (完全なイメージの URL を入力)** を選択します。 |
   | Provide a module name (モジュール名の指定) | **azureblobstorageoniotedge** のようにすべて小文字でモジュールの名前を入力します。<br /><br />IoT Edge モジュール上の Azure Blob Storage に小文字の名前を使用することが重要です。 IoT Edge は、モジュールを参照するときに大文字と小文字を区別し、Storage SDK は既定で小文字になります。 |
   | Provide Docker image for the module (モジュールの Docker イメージの指定) | イメージの URI: **mcr.microsoft.com/azure-blob-storage:latest** を指定します。 |

   Visual Studio Code は、指定された情報を取得し、IoT Edge ソリューションを作成して、それを新しいウィンドウに読み込みます。 ソリューション テンプレートによって、ご自分の BLOB ストレージ モジュール イメージを含む配置マニフェストのテンプレートが作成されますが、モジュールの作成オプションを構成する必要があります。

1. 新しいソリューション ワークスペースで *deployment.template.json* を開いて、**modules** セクションを見つけます。 次の構成の変更を行います。

   1. このデプロイに必要ないときは、**tempSensor** モジュールを削除します。

   1. 次のコードをコピーして、`createOptions` フィールドに貼り付けます。

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![モジュール createOptions - Visual Studio Code の更新](./media/how-to-deploy-blob/create-options.png)

1. `<your storage account name>` を覚えやすい名前に置き換えます。 アカウント名は、小文字と数字の 3 文字から 24 文字の長さにする必要があります。 スペースは含めません。

1. `<your storage account key>` を 64 バイトの Base64 キーに置き換えます。 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) などのツールを使ってキーを生成できます。 他のモジュールから BLOB ストレージにアクセスするには、これらの資格情報を使用します。

1. コンテナーのオペレーティング システムに応じて `<storage directory bind>` を置き換えます。 そのデータを格納する BLOB モジュールが必要な[ボリューム](https://docs.docker.com/storage/volumes/)の名前またはご利用の IoT Edge デバイス上のディレクトリへの絶対パスを指定します。 ストレージ ディレクトリのバインドは、提供したデバイス上の位置をモジュール内の設定された位置にマップします。  

      - Linux コンテナーの場合、形式は *\<ストレージのパス>:/blobroot* です。 例: **/srv/containerdata:/blobroot** または **my-volume:/blobroot**。
      - Windows コンテナーの場合、形式は *\<ストレージのパス>:C:/BlobRoot* です。 例: **C:/ContainerData:C:/BlobRoot** または **my-volume:C:/blobroot**。

      > [!IMPORTANT]
      > モジュールの特定の位置を指す、ストレージ ディレクトリのバインド値の後半を変更しないでください。 ストレージ ディレクトリ バインドは常に、Linux コンテナーの場合は **:/blobroot** で、Windows コンテナーの場合は **:C:/BlobRoot** 終わる必要があります。

1. 次の JSON を *deployment.template.json* ファイルに追加して、モジュールの [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) と [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) を構成します。 各プロパティを適切な値で構成して、ファイルを保存します。

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![azureblobstorageoniotedge - Visual Studio Code に必要なプロパティを設定する](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   モジュールがデプロイされた後に deviceToCloudUploadProperties と deviceAutoDeleteProperties を構成する方法については、[モジュール ツインの編集](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)に関する記事を参照してください。 コンテナー作成オプション、再起動ポリシー、および必要な状態について詳しくは、「[edgeAgent の必要なプロパティ](module-edgeagent-edgehub.md#edgeagent-desired-properties)」をご覧ください。

1. *deployment.template.json* ファイルを保存します。

1. **[deployment.template.json]** を右クリックして、 **[Generate IoT Edge deployment manifest]\(IoT Edge 配置マニフェストの生成\)** を選択します。

1. Visual Studio Code では、*deployment.template.json* で指定した情報が取得され、それを使用して新しい配置マニフェスト ファイルが作成されます。 配置マニフェストは、ご利用のソリューション ワークスペース内の新しい **config** フォルダーに作成されます。 そのファイルが用意されたら、「[Visual Studio Code から Azure IoT Edge モジュールをデプロイする](how-to-deploy-modules-vscode.md)」または [Azure CLI 2.0 を使用した Azure IoT Edge モジュールのデプロイ](how-to-deploy-modules-cli.md)に関するページの手順に従うことができます。

## <a name="deploy-multiple-module-instances"></a>複数のモジュール インスタンスのデプロイ

IoT Edge モジュールで Azure Blob Storage の複数のインスタンスをデプロイする場合は、別のストレージ パスを指定し、モジュールでバインドする `HostPort` の値を変更する必要があります。 BLOB ストレージ モジュールでは常に、コンテナーでポート 11002 を公開しますが、ホスト上でバインドされるポートを宣言できます。

(Azure portal で) **[コンテナーの作成オプション]** または (Visual Studio Code の *deployment.template.json* ファイルで) **createOptions** フィールドを編集して、`HostPort` の値を変更します。

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

追加の BLOB ストレージ モジュールに接続すると、更新されたホスト ポートをポイントするエンドポイントを変更します。

## <a name="next-steps"></a>次の手順

配置マニフェストのしくみとその作成方法について詳しくは、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](module-composition.md)」をご覧ください。
