---
title: モジュール上の Blob Storage をデバイスにデプロイする - Azure IoT Edge
description: Azure Blob Storage モジュールをご利用の IoT Edge デバイスにデプロイして、そのエッジにデータを格納します。
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: f766a77ee55351e498a379146826ba1d5507bc93
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201182"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>IoT Edge モジュール上の Azure Blob Storage を自分のデバイスにデプロイする

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge デバイスにモジュールをデプロイするにはいくつかの方法があり、これらすべてが IoT Edge モジュールの Azure Blob Storage で機能します。 2 つの最も簡単な方法では、Azure portal または Visual Studio Code テンプレートを使用します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。
- IoT Edge デバイス。

  IoT Edge デバイスを設定していない場合は、Azure 仮想マシンで作成できます。 クイックスタートの記事のいずれかの手順に従って、[仮想 Linux デバイスを作成](quickstart-linux.md)するか、[仮想 Windows デバイスを作成](quickstart.md)します。

- Visual Studio Code からデプロイする場合は、[Visual Studio Code](https://code.visualstudio.com/) と [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。

## <a name="deploy-from-the-azure-portal"></a>Azure portal からデプロイする

Azure portal では、配置マニフェストの作成から、IoT Edge デバイスへのデプロイのプッシュまでをガイドします。

### <a name="select-your-device"></a>デバイスを選択する

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。
1. メニューから **[IoT Edge]** を選択します。
1. デバイスの一覧でターゲット デバイスの ID をクリックします。
1. **[Set Modules] \(モジュールの設定)** を選択します。

### <a name="configure-a-deployment-manifest"></a>配置マニフェストを構成する

配置マニフェストは、デプロイするモジュール、モジュール間でのデータ フロー、およびモジュール ツインの目的のプロパティを記述した JSON ドキュメントです。 Azure portal には、配置マニフェストを作成する手順を示すウィザードがあります。 タブには次の3つの手順が構成されています:**モジュール**、**ルート**、および **レ表示と作成** を行います。

#### <a name="add-modules"></a>モジュールを追加する

1. ページの **[IoT Edge モジュール]** セクションで、 **[追加]** ドロップダウンをクリックし、**IoT Edge モジュール** を選択して、 **[IoT Edge モジュールの追加]** ページを表示します。

2. **モジュールの設定** タブで、モジュール名を入力し、コンテナー イメージ URI を指定します:

   例 :
  
   - **IoT Edge モジュールの名前**: `azureblobstorageoniotedge`
   - **[イメージの URI]** : `mcr.microsoft.com/azure-blob-storage:latest`

   ![スクリーンショットには、[I o T Edge モジュールの追加] ページの [モジュールの設定] タブが示されています。](./media/how-to-deploy-blob/addmodule-tab1.png)

   この手順の説明に従って、 **[モジュール設定]** タブ、 **[コンテナ作成オプション]** タブ、および **[モジュールツイン設定]** タブで値を指定するまで、 **[追加]** を選択しないでください。

   > [!IMPORTANT]
   > Azure IoT Edge は、モジュールを呼び出すときに大文字と小文字を区別し、Storage SDK も既定で小文字になります。 [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) でのモジュールの名前は **AzureBlobStorageonIoTEdge** ですが、名前を小文字に変更すると、IoT Edge モジュールの Azure Blob Storage への接続が中断されないことが保証されます。

3. **[コンテナーの作成オプション]** タブを開きます。

   ![スクリーンショットには、[I o T Edge モジュールの追加] ページの [コンテナーの作成オプション] タブが示されています。](./media/how-to-deploy-blob/addmodule-tab3.png)

   次の JSON をコピーしてボックスに貼り付け、ストレージ アカウント情報とデバイス上のストレージのマウントを提供します。
  
   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

4. **[コンテナーの作成オプション]** にコピーした JSON を、次の情報で更新します。

   - `<your storage account name>` を覚えやすい名前に置き換えます。 アカウント名は、小文字と数字の 3 文字から 24 文字の長さにする必要があります。 スペースは含めません。

   - `<your storage account key>` を 64 バイトの Base64 キーに置き換えます。 [GeneratePlus](https://generate.plus/en/base64) などのツールを使ってキーを生成できます。 他のモジュールから BLOB ストレージにアクセスするには、これらの資格情報を使用します。

   - コンテナーのオペレーティング システムに応じて `<storage mount>` を置き換えます。 BLOB モジュールによってそのデータが格納される[ボリューム](https://docs.docker.com/storage/volumes/)の名前またはご利用の IoT Edge デバイス上の既存のディレクトリへの絶対パスを指定します。 ストレージ マウントによって、提供したデバイス上の位置がモジュール内の設定された位置にマップされます。

     - Linux コンテナーの場合、形式は **\<your storage path or volume>:/blobroot** です。 次に例を示します。
         - [ボリューム マウント](https://docs.docker.com/storage/volumes/)を使用する: `my-volume:/blobroot`
         - [バインド マウント](https://docs.docker.com/storage/bind-mounts/)を使用する:`/srv/containerdata:/blobroot`。 [コンテナー ユーザーにディレクトリへのアクセス権を付与する](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)手順に必ず従ってください
     - Windows コンテナーの場合、形式は **\<your storage path or volume>:C:/BlobRoot** です。 次に例を示します。
         - [ボリューム マウント](https://docs.docker.com/storage/volumes/)を使用する: `my-volume:C:/BlobRoot`。
         - [バインド マウント](https://docs.docker.com/storage/bind-mounts/)を使用する: `C:/ContainerData:C:/BlobRoot`。
         - ローカル ドライブを使用する代わりに、SMB ネットワークの場所をマップすることができます。詳細については、「[ローカル ストレージとして SMB 共有を使用する](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)」を参照してください

     > [!IMPORTANT]
     > IoT Edge モジュールの Blob Storage の特定の位置を指す、ストレージ マウント値の後半を変更しないでください。 ストレージ マウントは常に、Linux コンテナーの場合は **:/blobroot** で、Windows コンテナーの場合は **:C:/BlobRoot** 終わる必要があります。

5. **[モジュール ツインの設定]** タブで、以降の JSON をコピーし、ボックスに貼り付けます。

   ![スクリーンショットには、[I o T Edge モジュールの追加] ページの [モジュール ツインの設定] タブが示されています。](./media/how-to-deploy-blob/addmodule-tab4.png)

   プレースホルダーに示されているように、各プロパティを適切な値で構成します。 IoT Edge シミュレーターを使用している場合は、値を、これらのプロパティの関連する環境変数 ([deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) と [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) の説明セクションに記載) に設定します。

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
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
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   モジュールがデプロイされた後に deviceToCloudUploadProperties と deviceAutoDeleteProperties を構成する方法については、[モジュール ツインの編集](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)に関する記事を参照してください。 目的のプロパティに関する詳細情報は、「[必要なプロパティの定義または更新](module-composition.md#define-or-update-desired-properties)」をご覧ください。

6. **[追加]** を選択します。

7. **次へ:ルート** 　でルート のセクションに進みます。

#### <a name="specify-routes"></a>ルートを指定する

既定のルートをそのまま保持して、[**次へ] を選択します:[確認と作成]** で、[レ表示] セクションに進みます。

#### <a name="review-deployment"></a>デプロイを確認する

確認のセクションには、前述の 2 つのセクションの選択項目に基づいて作成された JSON 配置マニフェストが表示されます。 追加しなかった 2 つのモジュール ( **$edgeAgent** および **$edgeHub**) もあります。 これらの 2 つのモジュールは、[IoT Edge ランタイム](iot-edge-runtime.md)を構成し、すべてのデプロイの既定値として使用されます。

デプロイ情報を確認し、 **[作成]** を選択します。

### <a name="verify-your-deployment"></a>デプロイを確認する

デプロイを作成すると、お使いの IoT ハブの **IoT Edge** ページに戻ります。

1. デプロイで対象にした Azure IoT Edge デバイスを選択して、その詳細を開きます。
1. デバイスの詳細で、BLOB ストレージ モジュールが **[デプロイで指定]** および **[デバイス別に報告]** の両方として一覧表示されていることを確認します。

モジュールがデバイス上で開始してから IoT Hub にレポートされるまでしばらく時間がかかる場合があります。 ページを更新して、最新の状態を表示します。

## <a name="deploy-from-visual-studio-code"></a>Visual Studio Code からのデプロイ

Azure IoT Edge では、エッジ ソリューションの開発に役立つ、Visual Studio Code のテンプレートが提供されます。 次の手順を使って、BLOB ストレージ モジュールで新しい IoT Edge ソリューションを作成し、配置マニフェストを構成します。

1. **[ビュー]**  >  **[コマンド パレット]** を選択します。

1. コマンド パレットで、**Azure IoT Edge:New IoT Edge solution** コマンドを入力して実行します。

   ![新しい IoT Edge ソリューションを実行する](./media/how-to-develop-csharp-module/new-solution.png)

   コマンド パレットに表示されるメッセージに従って、ソリューションを作成します。

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | Visual Studio Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | ソリューションのためにわかりやすい名前を入力するか、既定値の **EdgeSolution** をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **既存のモジュール (完全なイメージの URL を入力)** を選択します。 |
   | Provide a module name (モジュール名の指定) | **azureblobstorageoniotedge** のようにすべて小文字でモジュールの名前を入力します。<br/><br/>IoT Edge モジュール上の Azure Blob Storage に小文字の名前を使用することが重要です。 IoT Edge は、モジュールを参照するときに大文字と小文字を区別し、Storage SDK は既定で小文字になります。 |
   | Provide Docker image for the module (モジュールの Docker イメージの指定) | イメージの URI: **mcr.microsoft.com/azure-blob-storage:latest** を指定します。 |

   Visual Studio Code は、指定された情報を取得し、IoT Edge ソリューションを作成して、それを新しいウィンドウに読み込みます。 ソリューション テンプレートによって、ご自分の BLOB ストレージ モジュール イメージを含む配置マニフェストのテンプレートが作成されますが、モジュールの作成オプションを構成する必要があります。

1. 新しいソリューション ワークスペースで *deployment.template.json* を開いて、**modules** セクションを見つけます。 次の構成の変更を行います。

   1. このデプロイに必要ないときは、**SimulatedTemperatureSensor** モジュールを削除します。

   1. 次のコードをコピーして、`createOptions` フィールドに貼り付けます。

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![モジュール createOptions - Visual Studio Code の更新](./media/how-to-deploy-blob/create-options.png)

1. `<your storage account name>` を覚えやすい名前に置き換えます。 アカウント名は、小文字と数字の 3 文字から 24 文字の長さにする必要があります。 スペースは含めません。

1. `<your storage account key>` を 64 バイトの Base64 キーに置き換えます。 [GeneratePlus](https://generate.plus/en/base64) などのツールを使ってキーを生成できます。 他のモジュールから BLOB ストレージにアクセスするには、これらの資格情報を使用します。

1. コンテナーのオペレーティング システムに応じて `<storage mount>` を置き換えます。 そのデータを格納する BLOB モジュールが必要な[ボリューム](https://docs.docker.com/storage/volumes/)の名前またはご利用の IoT Edge デバイス上のディレクトリへの絶対パスを指定します。 ストレージ マウントによって、提供したデバイス上の位置がモジュール内の設定された位置にマップされます。  

     - Linux コンテナーの場合、形式は **\<your storage path or volume>:/blobroot** です。 次に例を示します。
         - [ボリューム マウント](https://docs.docker.com/storage/volumes/)を使用する: `my-volume:/blobroot`
         - [バインド マウント](https://docs.docker.com/storage/bind-mounts/)を使用する:`/srv/containerdata:/blobroot`。 [コンテナー ユーザーにディレクトリへのアクセス権を付与する](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)手順に必ず従ってください
     - Windows コンテナーの場合、形式は **\<your storage path or volume>:C:/BlobRoot** です。 次に例を示します。
         - [ボリューム マウント](https://docs.docker.com/storage/volumes/)を使用する: `my-volume:C:/BlobRoot`。
         - [バインド マウント](https://docs.docker.com/storage/bind-mounts/)を使用する: `C:/ContainerData:C:/BlobRoot`。
         - ローカル ドライブを使用する代わりに、SMB ネットワークの場所をマップすることができます。 詳細については、「[ローカル ストレージとして SMB 共有を使用する](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)」を参照してください。

     > [!IMPORTANT]
     > IoT Edge モジュールの Blob Storage の特定の位置を指す、ストレージ マウント値の後半を変更しないでください。 ストレージ マウントは常に、Linux コンテナーの場合は **:/blobroot** で、Windows コンテナーの場合は **:C:/BlobRoot** 終わる必要があります。

1. 次の JSON を *deployment.template.json* ファイルに追加して、モジュールの [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) と [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) を構成します。 各プロパティを適切な値で構成して、ファイルを保存します。 IoT Edge シミュレーターを使用している場合は、値を、これらのプロパティの関連する環境変数 ([deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) と [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) の説明セクションに記載) に設定します。

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

## <a name="configure-proxy-support"></a>プロキシ サポートの構成

組織でプロキシ サーバーを使用している場合は、edgeAgent および edgeHub のランタイム モジュールのプロキシ サポートを構成する必要があります。 このプロセスには、次の 2 つのタスクが含まれます。

- デバイスでランタイム デーモンと IoT Edge エージェントを構成します。
- 配置マニフェスト JSON ファイルでモジュールの HTTPS_PROXY 環境変数を設定します。

このプロセスについては、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。

さらに、Blob Storage モジュールでは、マニフェスト配置ファイルに HTTPS_PROXY 設定も必要です。 配置マニフェスト ファイルを直接編集することも、Azure portal を使用することもできます。

1. Azure portal で IoT Hub に移動し、左側のウィンドウ メニューから **[IoT Edge]** を選択します。

1. モジュールを構成するデバイスを選択します。

1. **[Set Modules] \(モジュールの設定)** を選択します。

1. ページの **[IoT Edge モジュール]** セクションで、BLOB Storage モジュールを選択します。

1. **[IoT Edge モジュールの更新]** ページで、 **[環境変数]** タブを選択します。

1. **[名前]** に `HTTPS_PROXY` を、 **[値]** にプロキシ URL を追加します。

      ![スクリーンショットには、指定した値を入力できる [I o T Edge モジュールの更新] ウィンドウが示されています。](./media/how-to-deploy-blob/https-proxy-config.png)

1. **[更新]** 、 **[確認および作成]** の順にクリックします。

1. 配置マニフェストでプロキシがモジュールに追加されていることに注意し、 **[作成]** を選択します。

1. デバイス詳細ページからモジュールを選択して設定を確認し、 **[IoT Edge Modules Details]/(IoT Edge モジュールの詳細/)** ページの下部にある **[環境変数]** タブを選択します。

      ![[環境変数] タブが示されているスクリーンショット。](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>次のステップ

[IoT Edge 上の Azure Blob Storage](how-to-store-data-blob.md) の詳細を確認する。

配置マニフェストのしくみとその作成方法について詳しくは、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](module-composition.md)」をご覧ください。
