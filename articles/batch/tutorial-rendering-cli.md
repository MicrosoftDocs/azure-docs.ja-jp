---
title: クラウドでのシーンのレンダリング - Azure Batch
description: チュートリアル - Batch Rendering サービスと Azure コマンド ライン インターフェイスを使用して Arnold で Autodesk 3DS Max シーンをレンダリングする方法
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: tutorial
ms.date: 12/11/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: 0c87a6968e5c6fd0e587c240b0a5df0a73f9909b
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321653"
---
# <a name="tutorial-render-a-scene-with-azure-batch"></a>チュートリアル:Azure Batch を使用したシーンのレンダリング 

Azure Batch は、クラウド規模のレンダリング機能を従量課金ベースで提供します。 Azure Batch は、Autodesk Maya、3ds Max、Arnold、V-Ray などのレンダリング アプリをサポートしています。 このチュートリアルでは、Azure コマンド ライン インターフェイスを使用して Batch で小さいシーンをレンダリングする手順を紹介します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure Storage にシーンをアップロードする
> * レンダリング用の Batch プールを作成する
> * 単一フレーム シーンをレンダリングする
> * プールをスケーリングしてマルチフレーム シーンをレンダリングする
> * レンダリングされる出力をダウンロードする

このチュートリアルでは、[Arnold](https://www.autodesk.com/products/arnold/overview) レイ トレース レンダラーを使用して Batch で 3DS Max シーンをレンダリングします。 Batch プールは、従量課金制ライセンスを提供しているプリインストール済のグラフィックス アプリケーションやレンダリング アプリケーションによって Microsoft Azure Marketplace イメージを使用します。

## <a name="prerequisites"></a>前提条件

Batch で従量課金制のレンダリング アプリケーションを使用するには、従量課金制サブスクリプションまたはその他の Azure 購入オプションが必要です。 **金融クレジットを提供する無料の Azure オファーを使用する場合、従量課金制ライセンスはサポートされません。**

このチュートリアル用のサンプル 3DS Max シーンは、サンプルの Bash スクリプトと JSON 構成ファイルと共に [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/batch/render-scene) にあります。 3DS Max シーンは、[Autodesk 3DS Max サンプル ファイル](https://download.autodesk.com/us/support/files/3dsmax_sample_files/2017/Autodesk_3ds_Max_2017_English_Win_Samples_Files.exe)に含まれています (Autodesk 3DS Max のサンプル ファイルは、Creative Commons Attribution-NonCommercial-Share Alike ライセンスで入手可能です。 Copyright © Autodesk, Inc.)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.20 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-batch-account"></a>Batch アカウントを作成する

サブスクリプションにリソース グループ、Batch アカウント、リンクされているストレージ アカウントをまだ作成してない場合は作成します。 

[az group create](/cli/azure/group#az-group-create) コマンドでリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus2* に作成します。

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

[az storage account create](/cli/azure/storage/account#az-storage-account-create) コマンドを使用して、リソース グループ内に Azure ストレージ アカウントを作成します。 このチュートリアルでは、ストレージ アカウントを使用して、入力の 3DS Max シーンとレンダリングされた出力を格納します。

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```
[az batch account create](/cli/azure/batch/account#az-batch-account-create) コマンドを使用して Batch アカウントを作成します。 次の例では、*mybatchaccount* という名前の Batch アカウントを *myResourceGroup* に作成し、作成したアカウントをリンクします。  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

コンピューティング プールとジョブを作成および管理するには、Batch による認証が必要です。 [az batch account login](/cli/azure/batch/account#az-batch-account-login) コマンドを使用してアカウントにログインします。 ログインしたら、`az batch` コマンドでこのアカウントのコンテキストを使用します。 次の例では、Batch アカウントの名前とキーに基づいて、共有キー認証を使用します。 Batch は、個々のユーザーまたは自動アプリケーションを認証するために、[Azure Active Directory](batch-aad-auth.md) による認証もサポートしています。

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```
## <a name="upload-a-scene-to-storage"></a>ストレージへのシーンのアップロード

入力のシーンをストレージにアップロードするには、まず、ストレージ アカウントにアクセスして、BLOB 用にアップロード先コンテナーを作成する必要があります。 Azure ストレージ アカウントにアクセスするには、`AZURE_STORAGE_KEY` 環境変数と `AZURE_STORAGE_ACCOUNT` 環境変数をエクスポートします。 最初の Bash シェル コマンドでは、[az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list) コマンドを使用して、最初のアカウント キーを取得します。 これらの環境変数を設定した後、ストレージ コマンドはこのアカウントのコンテキストを使用します。

```azurecli-interactive
export AZURE_STORAGE_KEY=$(az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup -o tsv --query [0].value)

export AZURE_STORAGE_ACCOUNT=mystorageaccount
```

ここで、シーン ファイル用にストレージ アカウントに BLOB コンテナーを作成します。 次の例では、[az storage container create](/cli/azure/storage/container#az-storage-container-create) コマンドを使用して、パブリック読み取りアクセスを許可する *scenefiles* という名前の BLOB コンテナーを作成します。

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name scenefiles
```

シーン `MotionBlur-Dragon-Flying.max` を [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max) からローカルの作業ディレクトリにダウンロードします。 例:

```azurecli-interactive
wget -O MotionBlur-DragonFlying.max https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max
```

シーン ファイルをローカルの作業ディレクトリから BLOB コンテナーにアップロードします。 次の例では、[az storage blob upload-batch](/cli/azure/storage/blob#az-storage-blob-upload-batch) コマンドを使用します。これにより、複数のファイルをアップロードできます。

```azurecli-interactive
az storage blob upload-batch \
    --destination scenefiles \
    --source ./
```

## <a name="create-a-rendering-pool"></a>レンダリング プールの作成

[az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) コマンドを使用して、レンダリング用の Batch プールを作成します。 この例では、JSON ファイルでプールの設定を指定します。 現在のシェル内で、*mypool.json* という名前のファイルを作成し、次の内容をコピーして貼り付けます。 すべてのテキストが正しくコピーされるようにしてください (このファイルは [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/mypool.json) からダウンロードできます)。


```json
{
  "id": "myrenderpool",
  "vmSize": "standard_d2_v2",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "batch",
      "offer": "rendering-windows2016",
      "sku": "rendering",
      "version": "1.3.2"
    },
    "nodeAgentSKUId": "batch.node.windows amd64"
  },
  "targetDedicatedNodes": 0,
  "targetLowPriorityNodes": 1,
  "enableAutoScale": false,
  "applicationLicenses":[
         "3dsmax",
         "arnold"
      ],
  "enableInterNodeCommunication": false 
}
```
Batch では専用ノードと[低優先度ノード](batch-low-pri-vms.md)がサポートされているため、ご利用のプールではそのいずれかまたは両方を使用できます。 専用ノードは、プール用に予約されています。 低優先度ノードは、Azure の VM の余剰容量から割引価格で提供されます。 低優先度ノードは、Azure に十分な容量がない場合に使用できなくなります。 

指定されたプールには、Batch Rendering サービス用のソフトウェアを搭載した Windows Server イメージを実行している 1 つの低優先度ノードが含まれています。 このプールには、3DS Max および Arnold を使用してレンダリングするためのライセンスが付与されています。 後の手順では、より多くのノードに合わせてプールをスケーリングします。

JSON ファイルを `az batch pool create` コマンドに渡すことで、プールを作成します。

```azurecli-interactive
az batch pool create \
    --json-file mypool.json
``` 
プールのプロビジョニングには数分かかります。 プールの状態を確認するには、[az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) コマンドを実行します。 次のコマンドは、プールの割り当ての状態を取得します。

```azurecli-interactive
az batch pool show \
    --pool-id myrenderpool \
    --query "allocationState"
```

プールの状態が変化している間にジョブとタスクを作成するには、次の手順を続行します。 割り当ての状態が `steady` となり、ノードが実行されていると、プールは完全にプロビジョニングされます。  

## <a name="create-a-blob-container-for-output"></a>出力用 BLOB コンテナーの作成

このチュートリアルの例では、レンダリング ジョブの各タスクで出力ファイルが作成されます。 ジョブのスケジュールを設定する前に、出力ファイルの保存先として BLOB コンテナーをストレージ アカウントに作成します。 次の例では、[az storage container create](/cli/azure/storage/container#az-storage-container-create) コマンドを使用して、パブリック読み取りアクセスを持つ *job-myrenderjob* コンテナーを作成します。 

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name job-myrenderjob
```

コンテナーに出力ファイルを書き込むために、Batch は Shared Access Signature (SAS) トークンを使用する必要があります。 [az storage account generate-sas](/cli/azure/storage/account#az-storage-account-generate-sas) コマンドを使用してトークンを作成します。 この例では、このアカウントの BLOB コンテナーに書き込むトークンを作成します。このトークンの有効期限は 2018 年 11 月 15 日に切れます。

```azurecli-interactive
az storage account generate-sas \
    --permissions w \
    --resource-types co \
    --services b \
    --expiry 2019-11-15
```

このコマンドによって返されたトークンを書き留めておきます。これは次のようになります。 このトークンは、後の手順で使用します。

```
se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## <a name="render-a-single-frame-scene"></a>単一フレーム シーンのレンダリング

### <a name="create-a-job"></a>ジョブを作成する

[az batch job create](/cli/azure/batch/job#az-batch-job-create) コマンドを使用して、プールで実行するレンダリング ジョブを作成します。 最初、ジョブにはタスクがありません。

```azurecli-interactive
az batch job create \
    --id myrenderjob \
    --pool-id myrenderpool
```

### <a name="create-a-task"></a>タスクを作成します。

[az batch task create](/cli/azure/batch/task#az-batch-task-create) コマンドを使用して、ジョブにレンダリング タスクを作成します。 この例では、JSON ファイルでタスクの設定を指定します。 現在のシェル内で、*myrendertask.json* という名前のファイルを作成し、次の内容をコピーして貼り付けます。 すべてのテキストが正しくコピーされるようにしてください (このファイルは [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask.json) からダウンロードできます)。

このタスクでは、3DS Max コマンドを指定して、単一フレームの *MotionBlur-DragonFlying.max* シーンをレンダリングします。

JSON ファイルの `blobSource` 要素と `containerURL` 要素を、ストレージ アカウントと SAS トークンの名前が含まれるように変更します。 

> [!TIP]
> `containerURL` の末尾には SAS トークンが使用され、次のようになります。
> 
> ```
> https://mystorageaccount.blob.core.windows.net/job-myrenderjob/$TaskOutput?se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
> ```

```json
{
  "id": "myrendertask",
  "commandLine": "cmd /c \"%3DSMAX_2018%3dsmaxcmdio.exe -secure off -v:5 -rfw:0 -start:1 -end:1 -outputName:\"dragon.jpg\" -w 400 -h 300 MotionBlur-DragonFlying.max\"",
  "resourceFiles": [
    {
        "blobSource": "https://mystorageaccount.blob.core.windows.net/scenefiles/MotionBlur-DragonFlying.max",
        "filePath": "MotionBlur-DragonFlying.max"
    }
  ],
    "outputFiles": [
        {
            "filePattern": "dragon*.jpg",
            "destination": {
                "container": {
                    "containerUrl": "https://mystorageaccount.blob.core.windows.net/job-myrenderjob/myrendertask/$TaskOutput?Add_Your_SAS_Token_Here"
                }
            },
            "uploadOptions": {
                "uploadCondition": "TaskSuccess"
            }
        }
    ],
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonAdmin"
    }
  }
}
```

次のコマンドを使用してジョブにタスクを追加します。

```azurecli-interactive
az batch task create \
    --job-id myrenderjob \
    --json-file myrendertask.json
```

Batch によってタスクのスケジュールが設定され、プール内のノードが使用可能になるとすぐにタスクが実行されます。


### <a name="view-task-output"></a>タスク出力の表示

タスクの実行には数分かかります。 [az batch task show](/cli/azure/batch/task#az-batch-task-show) コマンドを使用して、タスクの詳細を表示します。

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id myrendertask
```

このタスクによって、コンピューティング ノードに *dragon0001.jpg* が生成され、ストレージ アカウント内の *job-myrenderjob* コンテナーにアップロードされます。 出力を表示するには、[az storage blob download](/cli/azure/storage/blob#az-storage-blob-download) コマンドを使用して、ファイルをストレージからローカル コンピューターにダウンロードします。

```azurecli-interactive
az storage blob download \
    --container-name job-myrenderjob \
    --file dragon.jpg \
    --name dragon0001.jpg

```

コンピューター上の *dragon.jpg* を開きます。 レンダリングされたイメージは次のようになります。

![レンダリングされたドラゴンのフレーム 1](./media/tutorial-rendering-cli/dragon-frame.png) 


## <a name="scale-the-pool"></a>プールのスケーリング

ここで、複数のフレームを使用して、より大きなレンダリング ジョブに備えるようにプールを変更します。 Batch には、タスク要求の変更に応じてノードを追加または削除する[自動スケール](batch-automatic-scaling.md)など、コンピューティング リソースをスケーリングする方法は多数あります。 この基本的な例では、[az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) コマンドを使用して、プール内の低優先度ノードの数を *6* に引き上げます。

```azurecli-interactive
az batch pool resize --pool-id myrenderpool --target-dedicated-nodes 0 --target-low-priority-nodes 6
```

プールのサイズ変更には数分かかります。 その処理の実行中に、既存のレンダリング ジョブで次に実行するタスクを設定します。

## <a name="render-a-multiframe-scene"></a>マルチフレーム シーンのレンダリング

単一フレームの例と同様、[az batch task create](/cli/azure/batch/task#az-batch-task-create) コマンドを使用して、*myrenderjob* という名前のジョブにレンダリング タスクを作成します。 ここでは、*myrendertask_multi.json* という JSON ファイルでタスクの設定を指定します (このファイルは [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask_multi.json) からダウンロードできます)。6 つのタスクそれぞれで、Arnold コマンド ラインを指定して、3DS Max シーン *MotionBlur-DragonFlying.max* の 1 つのフレームをレンダリングします。

現在のシェルに *myrendertask_multi.json* という名前のファイルを作成し、ダウンロードしたファイルから内容をコピーして貼り付けます。 JSON ファイルの `blobSource` 要素と `containerURL` 要素を変更して、ストレージ アカウントと SAS トークンの名前を含めます。 6 つのタスクそれぞれで設定を変更するようにしてください。 ファイルを保存し、次のコマンドを実行してタスクをキューに登録します。

```azurecli-interactive
az batch task create --job-id myrenderjob --json-file myrendertask_multi.json
```

### <a name="view-task-output"></a>タスク出力の表示

タスクの実行には数分かかります。 [az batch task list](/cli/azure/batch/task#az-batch-task-list) コマンドを使用して、タスクの状態を表示します。 例:

```azurecli-interactive
az batch task list \
    --job-id myrenderjob \
    --output table
```

[az batch task show](/cli/azure/batch/task#az-batch-task-show) コマンドを使用して、個々のタスクの詳細を表示します。 例:

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id mymultitask1
```
 
このタスクにより、*dragon0002.jpg* - *dragon0007.jpg* という名前の出力ファイルがコンピューティング ノード上に生成され、ストレージ アカウント内の *job-myrenderjob* コンテナーにアップロードされます。 出力を表示するには、[az storage blob download-batch](/cli/azure/storage/blob) コマンドを使用して、ファイルをローカル コンピューター上のフォルダーにダウンロードします。 例:

```azurecli-interactive
az storage blob download-batch \
    --source job-myrenderjob \
    --destination .
```

コンピューター上のファイルのいずれかを開きます。 レンダリングされたフレーム 6 は次のようになります。

![レンダリングされたドラゴンのフレーム 6](./media/tutorial-rendering-cli/dragon-frame6.png) 


## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループ、Batch アカウント、プール、およびすべての関連リソースを削除できます。 次のように、リソースを削除します。

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure Storage にシーンをアップロードする
> * レンダリング用の Batch プールを作成する
> * Arnold で単一フレーム シーンをレンダリングする
> * プールをスケーリングしてマルチフレーム シーンをレンダリングする
> * レンダリングされる出力をダウンロードする

クラウド規模のレンダリングの詳細については、Batch Rendering サービスのオプションを確認してください。 

> [!div class="nextstepaction"]
> [Batch Rendering サービス](batch-rendering-service.md)
