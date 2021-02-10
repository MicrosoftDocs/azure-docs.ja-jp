---
title: 成果物を転送する
description: Azure ストレージ アカウントを使用して転送パイプラインを作成することにより、イメージまたはその他の成果物のコレクションを 1 つのコンテナー レジストリから別のレジストリに転送する
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: ab6657ecd335a6de8c6c93e3c2ff392ac54c487c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935350"
---
# <a name="transfer-artifacts-to-another-registry"></a>成果物を別のレジストリに転送する

この記事では、イメージまたはその他のレジストリ成果物のコレクションを 1 つの Azure コンテナー レジストリから別のレジストリに転送する方法について説明します。 ソース レジストリとターゲット レジストリは、同じサブスクリプションまたは異なるサブスクリプション、Active Directory テナント、Azure クラウド、または物理的に切断されたクラウドに存在する可能性があります。 

成果物を転送するには、[BLOB ストレージ](../storage/blobs/storage-blobs-introduction.md)を使用して、2 つのレジストリ間で成果物をレプリケートする "*転送パイプライン*" を作成します。

* ソース レジストリの成果物が、ソース ストレージ アカウントの BLOB にエクスポートされます。 
* BLOB が、ソース ストレージ アカウントからターゲット ストレージ アカウントにコピーされます。
* ターゲット ストレージ アカウント内の BLOB が、ターゲット レジストリに成果物としてインポートされます。 ターゲット ストレージで成果物 BLOB が更新されるたびにトリガーされるように、インポート パイプラインを設定できます。

転送は、物理的に切断されたクラウド内の 2 つの Azure コンテナー レジストリ間で、各クラウドのストレージ アカウントによって仲介されるコンテンツのコピーに最適です。 そうではなく、Docker Hub などのクラウド ベンダーを含め、接続されたクラウド内のコンテナー レジストリからイメージをコピーする場合は、[イメージ インポート](container-registry-import-images.md)を使用することをお勧めします。

この記事では、Azure Resource Manager テンプレート デプロイを使用して、転送パイプラインを作成して実行します。 Azure CLI は、ストレージ シークレットなど、関連付けられているリソースをプロビジョニングするために使用されます。 Azure CLI バージョン 2.2.0 以降が推奨されます。 CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

この機能は、**Premium** コンテナー レジストリ サービス レベルで使用できます。 レジストリ サービスのレベルと制限については、[Azure Container Registry のレベル](container-registry-skus.md)に関するページを参照してください。

> [!IMPORTANT]
> 現在、この機能はプレビュー段階にあります。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="prerequisites"></a>前提条件

* **コンテナー レジストリ** - 転送する成果物を含む既存のソース レジストリと、ターゲット レジストリが必要です。 ACR 転送は、物理的に切断されたクラウド間での移動を目的としています。 テストの場合、ソース レジストリとターゲット レジストリは、同じまたは異なる Azure サブスクリプション、Active Directory テナント、またはクラウドに存在する可能性があります。 

   レジストリを作成する必要がある場合は、「[クイック スタート: Azure CLI を使用したプライベート コンテナー レジストリの作成](container-registry-get-started-azure-cli.md)」を参照してください。 
* **ストレージ アカウント** - サブスクリプション内および任意の場所にソース ストレージ アカウントとターゲット ストレージ アカウントを作成します。 テスト目的では、ソース レジストリおよびターゲット レジストリと同じ 1 つまたは複数のサブスクリプションを使用できます。 クロスクラウドのシナリオでは、通常、各クラウドに個別のストレージ アカウントを作成します。 

  必要に応じて、[Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli) またはその他のツールでストレージ アカウントを作成します。 

  各アカウントで成果物転送用の BLOB コンテナーを作成します。 たとえば、*transfer* という名前のコンテナーを作成します。 2 つ以上の転送パイプラインで同じストレージ アカウントを共有できますが、異なるストレージ コンテナー スコープを使用する必要があります。
* **キー コンテナー** - ソース ストレージ アカウントとターゲット ストレージ アカウントへのアクセスに使用される SAS トークン シークレットを格納するには、キー コンテナーが必要です。 ソース レジストリおよびターゲット レジストリと同じ 1 つまたは複数の Azure サブスクリプションに、ソース キー コンテナーとターゲット キー コンテナーを作成します。 デモンストレーションの目的で、この記事で使用されているテンプレートとコマンドでも、ソースとターゲットの各キー コンテナーは、それぞれソースとターゲットの各レジストリと同じリソース グループにあることを前提としています。 この共通のリソース グループの使用は必須ではありませんが、この記事で使用するテンプレートとコマンドが簡単になります。

   必要に応じて、[Azure CLI](../key-vault/secrets/quick-create-cli.md) またはその他のツールを使用してキー コンテナーを作成します。

* **環境変数** - この記事のコマンド例では、ソース環境とターゲット環境に対して次の環境変数を設定します。 すべての例は、Bash シェル用に書式設定されています。
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>シナリオの概要

レジストリ間のイメージ転送用に、次の 3 つのパイプライン リソースを作成します。 すべて PUT 操作を使用して作成されます。 これらのリソースは、"*ソース*" と "*ターゲット*" のレジストリおよびストレージ アカウントで動作します。 

ストレージ認証では、キー コンテナーのシークレットとして管理される SAS トークンが使用されます。 パイプラインでは、マネージド ID を使用してコンテナー内のシークレットが読み取られます。

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** - "*ソース*" レジストリおよびストレージ アカウントに関する概要情報を含む長期間のリソース。 この情報には、ソース ストレージ BLOB コンテナーの URI と、ソース SAS トークンを管理するキー コンテナーが含まれます。 
* **[ImportPipeline](#create-importpipeline-with-resource-manager)** - "*ターゲット*" レジストリおよびストレージ アカウントに関する概要情報を含む長期間のリソース。 この情報には、ターゲット ストレージ BLOB コンテナーの URI と、ターゲット SAS トークンを管理するキー コンテナーが含まれます。 インポート トリガーは既定で有効になっているため、成果物 BLOB がターゲット ストレージ コンテナーに格納されるときに、パイプラインが自動的に実行されます。 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** - ExportPipeline リソースまたは ImportPipeline リソースを呼び出すために使用されるリソース。  
  * ExportPipeline を手動で実行するには、PipelineRun リソースを作成し、エクスポートする成果物を指定します。  
  * インポート トリガーが有効になっている場合、ImportPipeline は自動的に実行されます。 また、PipelineRun を使用して手動で実行することもできます。 
  * 現時点では、PipelineRun ごとに最大 **50 個の成果物** を転送できます。

### <a name="things-to-know"></a>注意事項
* ExportPipeline と ImportPipeline は、通常、ソース クラウドとターゲット クラウドに関連付けられている異なる Active Directory テナントにあります。 このシナリオでは、リソースのエクスポートとインポート用に個別のマネージド ID とキー コンテナーが必要です。 テスト目的では、これらのリソースを同じクラウドに配置し、ID を共有することができます。
* 既定では、ExportPipeline および ImportPipeline の各テンプレートによって、システム割り当てマネージド ID を使用してキー コンテナーのシークレットにアクセスできるようになります。 ExportPipeline および ImportPipeline テンプレートでは、ユーザー割り当て ID もサポートされています。 

## <a name="create-and-store-sas-keys"></a>SAS キーを作成して格納する

転送では、共有アクセス署名 (SAS) トークンを使用して、ソース環境とターゲット環境のストレージ アカウントにアクセスします。 次のセクションで説明するように、トークンを生成して格納します。  

### <a name="generate-sas-token-for-export"></a>エクスポート用に SAS トークンを生成する

[az storage container generate-sas][az-storage-container-generate-sas] コマンドを実行して、ソース ストレージ アカウントで、成果物のエクスポートに使用されるコンテナーの SAS トークンを生成します。

*推奨されるトークン アクセス許可*:読み取り、書き込み、一覧表示、追加。 

次の例では、コマンド出力は、"?" 文字で始まる EXPORT_SAS 環境変数に割り当てられています。 環境の `--expiry` 値を更新します。

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>エクスポート用に SAS トークンを格納する

[az keyvault secret set][az-keyvault-secret-set] を使用して、ソース Azure キー コンテナーに SAS トークンを格納します。

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>インポート用に SAS トークンを生成する

[az storage container generate-sas][az-storage-container-generate-sas] コマンドを実行して、ターゲット ストレージ アカウントで、成果物のインポートに使用されるコンテナーの SAS トークンを生成します。

*推奨されるトークン アクセス許可*:読み取り、削除、一覧表示

次の例では、コマンド出力は、"?" 文字で始まる IMPORT_SAS 環境変数に割り当てられています。 環境の `--expiry` 値を更新します。

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>インポート用に SAS トークンを格納する

[az keyvault secret set][az-keyvault-secret-set] を使用して、ターゲット Azure キー コンテナーに SAS トークンを格納します。

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>Resource Manager を使用して ExportPipeline を作成する

Azure Resource Manager テンプレート デプロイを使用して、ソース コンテナー レジストリに ExportPipeline リソースを作成します。

ExportPipeline Resource Manager の[テンプレート ファイル](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines)をローカル フォルダーにコピーします。

ファイル `azuredeploy.parameters.json` に次のパラメーター値を入力します。

|パラメーター  |値  |
|---------|---------|
|registryName     | ソース コンテナー レジストリの名前      |
|exportPipelineName     |  エクスポート パイプライン用に選択した名前       |
|targetUri     |  ソース環境内のストレージ コンテナーの URI (エクスポート パイプラインのターゲット)。<br/>例: `https://sourcestorage.blob.core.windows.net/transfer`       |
|KeyVaultName     |  ソース キー コンテナーの名前  |
|sasTokenSecretName  | ソース キー コンテナー内の SAS トークン シークレットの名前 <br/>例: acrexportsas

### <a name="export-options"></a>エクスポート オプション

エクスポート パイプラインの `options` プロパティでは、省略可能なブール値がサポートされています。 次の値が推奨されます。

|パラメーター  |値  |
|---------|---------|
|options | OverwriteBlobs - 既存のターゲット BLOB を上書きします。<br/>ContinueOnErrors - 1 つの成果物のエクスポートが失敗した場合に、ソース レジストリ内の残りの成果物のエクスポートを続行します。

### <a name="create-the-resource"></a>リソースを作成する

次の例に示すように、[az deployment group create][az-deployment-group-create] を実行して *exportPipeline* という名前のリソースを作成します。 既定で、最初のオプションを使用すると、サンプル テンプレートによって ExportPipeline リソースのシステム割り当て ID が有効になります。 

2 つ目のオプションを使用すると、リソースにユーザー割り当て ID を指定できます (ユーザー割り当て ID の作成は示されていません)。

どちらのオプションでも、テンプレートによって、エクスポート キー コンテナー内の SAS トークンにアクセスするための ID が構成されます。 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>オプション 1: リソースを作成し、システム割り当て ID を有効にする

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>オプション 2:リソースを作成し、ユーザー割り当て ID を指定する

このコマンドでは、ユーザー割り当て ID のリソース ID を追加のパラメーターとして指定します。

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

コマンド出力で、パイプラインのリソース ID (`id`) を書き留めます。 この値を後で使用できるように環境変数に格納するには、[az deployment group show][az-deployment-group-show] を実行します。 次に例を示します。

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>Resource Manager を使用して ImportPipeline を作成する 

Azure Resource Manager テンプレート デプロイを使用して、ターゲット コンテナー レジストリに ImportPipeline リソースを作成します。 既定では、ターゲット環境のストレージ アカウントに成果物 BLOB が含まれている場合、パイプラインが有効化されて自動的にインポートが行われます。

ImportPipeline Resource Manager の[テンプレート ファイル](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines)をローカル フォルダーにコピーします。

ファイル `azuredeploy.parameters.json` に次のパラメーター値を入力します。

パラメーター  |値  |
|---------|---------|
|registryName     | ターゲット コンテナー レジストリの名前      |
|importPipelineName     |  インポート パイプライン用に選択した名前       |
|sourceUri     |  ターゲット環境内のストレージ コンテナーの URI (インポート パイプラインのソース)。<br/>例: `https://targetstorage.blob.core.windows.net/transfer/`|
|KeyVaultName     |  ターゲット キー コンテナーの名前 |
|sasTokenSecretName     |  ターゲット キー コンテナー内の SAS トークン シークレットの名前<br/>例: acr importsas |

### <a name="import-options"></a>インポート オプション

インポート パイプラインの `options` プロパティでは、省略可能なブール値がサポートされています。 次の値が推奨されます。

|パラメーター  |値  |
|---------|---------|
|options | OverwriteTags - 既存のターゲット タグを上書きします。<br/>DeleteSourceBlobOnSuccess - ターゲット レジストリへのインポートが正常に完了した後にソース ストレージ BLOB を削除します。<br/>ContinueOnErrors - 1 つの成果物のインポートが失敗した場合に、ターゲット レジストリ内の残りの成果物のインポートを続行します。

### <a name="create-the-resource"></a>リソースを作成する

次の例に示すように、[az deployment group create][az-deployment-group-create] を実行して *importPipeline* という名前のリソースを作成します。 既定で、最初のオプションを使用すると、サンプル テンプレートによって ImportPipeline リソースのシステム割り当て ID が有効になります。 

2 つ目のオプションを使用すると、リソースにユーザー割り当て ID を指定できます (ユーザー割り当て ID の作成は示されていません)。

どちらのオプションでも、テンプレートによって、インポート キー コンテナー内の SAS トークンにアクセスするための ID が構成されます。 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>オプション 1: リソースを作成し、システム割り当て ID を有効にする

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>オプション 2:リソースを作成し、ユーザー割り当て ID を指定する

このコマンドでは、ユーザー割り当て ID のリソース ID を追加のパラメーターとして指定します。

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

インポートを手動で実行する場合は、パイプラインのリソース ID (`id`) を書き留めておきます。 この値を後で使用できるように環境変数に格納するには、[az deployment group show][az-deployment-group-show] コマンドを実行します。 次に例を示します。

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>Resource Manager を使用してエクスポート用に PipelineRun を作成する 

Azure Resource Manager テンプレート デプロイを使用して、ソース コンテナー レジストリに PipelineRun リソースを作成します。 このリソースでは、前に作成した ExportPipeline リソースが実行され、指定された成果物が BLOB としてコンテナー レジストリからソース ストレージ アカウントにエクスポートされます。

PipelineRun Resource Manager の[テンプレート ファイル](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export)をローカル フォルダーにコピーします。

ファイル `azuredeploy.parameters.json` に次のパラメーター値を入力します。

|パラメーター  |値  |
|---------|---------|
|registryName     | ソース コンテナー レジストリの名前      |
|pipelineRunName     |  実行用に選択した名前       |
|pipelineResourceId     |  エクスポート パイプラインのリソース ID<br/>例: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName     |  *myblob* など、ソース ストレージ アカウントにエクスポートされた成果物 BLOB 用に選択した名前
|artifacts | タグまたはマニフェスト ダイジェストとして転送するソース成果物の配列<br/>例: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

同じプロパティを使用して PipelineRun リソースを再デプロイする場合は、[forceUpdateTag](#redeploy-pipelinerun-resource) プロパティも使用する必要があります。

[az deployment group create][az-deployment-group-create] を実行して、PipelineRun リソースを作成します。 次の例では、デプロイ *exportPipelineRun* に名前を付けます。

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

後で使用するために、パイプライン実行のリソース ID を環境変数に格納します。

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

成果物のエクスポートには数分かかることがあります。 デプロイが正常に完了したら、ソース ストレージ アカウントの *transfer* コンテナーでエクスポート済み BLOB を一覧表示することによって、成果物のエクスポートを確認します。 たとえば、[az storage blob list][az-storage-blob-list] コマンドを実行します。

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>BLOB を転送する (オプション) 

AzCopy ツールまたはその他の方法を使用して、ソース ストレージ アカウントからターゲット ストレージ アカウントに [BLOB データを転送](../storage/common/storage-use-azcopy-v10.md#transfer-data)します。

たとえば、次の [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) コマンドでは、ソース アカウントの *transfer* コンテナーからターゲット アカウントの *transfer* コンテナーに myblob がコピーされます。 BLOB がターゲット アカウントに存在する場合は上書きされます。 認証では、ソース コンテナーとターゲット コンテナーに対する適切なアクセス許可を持つ SAS トークンが使用されます (トークンを作成する手順については説明しません)。

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>ImportPipeline リソースをトリガーする

ImportPipeline の `sourceTriggerStatus` パラメーターを有効にした場合 (既定値)、BLOB がターゲット ストレージ アカウントにコピーされた後にパイプラインがトリガーされます。 成果物のインポートには数分かかることがあります。 インポートが正常に完了したら、ターゲット コンテナー レジストリにリポジトリを一覧表示して、成果物のインポートを確認します。 たとえば、[az acr repository list][az-acr-repository-list] を実行します。

```azurecli
az acr repository list --name <target-registry-name>
```

インポート パイプラインの `sourceTriggerStatus` パラメーターを有効にしなかった場合は、次のセクションに示すように、ImportPipeline リソースを手動で実行します。 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>Resource Manager を使用してインポート用に PipelineRun を作成する (省略可能) 
 
PipelineRun リソースを使用して、成果物をターゲット コンテナー レジストリにインポートするための ImportPipeline をトリガーすることもできます。

PipelineRun Resource Manager の[テンプレート ファイル](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import)をローカル フォルダーにコピーします。

ファイル `azuredeploy.parameters.json` に次のパラメーター値を入力します。

|パラメーター  |値  |
|---------|---------|
|registryName     | ターゲット コンテナー レジストリの名前      |
|pipelineRunName     |  実行用に選択した名前       |
|pipelineResourceId     |  インポート パイプラインのリソース ID<br/>例: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName     |  ストレージ アカウント内のエクスポート済み成果物の既存の BLOB の名前 (*myblob など*)

同じプロパティを使用して PipelineRun リソースを再デプロイする場合は、[forceUpdateTag](#redeploy-pipelinerun-resource) プロパティも使用する必要があります。

[az deployment group create][az-deployment-group-create] を実行して、リソースを実行します。

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

後で使用するために、パイプライン実行のリソース ID を環境変数に格納します。

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)

When deployment completes successfully, verify artifact import by listing the repositories in the target container registry. For example, run [az acr repository list][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>PipelineRun リソースを再デプロイする

*同じプロパティ* を使用して PipelineRun リソースを再デプロイする場合は、**forceUpdateTag** プロパティを使用する必要があります。 このプロパティは、構成が変更されていない場合でも、PipelineRun リソースを再作成する必要があることを示します。 PipelineRun リソースを再デプロイするたびに、forceUpdateTag が異なることを確認してください。 次の例では、エクスポートの PipelineRun を再作成しています。 forceUpdateTag を設定するために、現在の日時を使用します。これにより、このプロパティは常に一意になります。

```console
CURRENT_DATETIME=`date +"%Y-%m-%d:%T"`
```

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json \
  --parameters forceUpdateTag=$CURRENT_DATETIME
```

## <a name="delete-pipeline-resources"></a>パイプライン リソースを削除する

次のコマンド例では、[az resource delete][az-resource-delete] を使用して、この記事で作成したパイプライン リソースを削除します。 このリソース ID は、以前に環境変数に格納したものです。

```
# Delete export resources
az resource delete \
--resource-group $SOURCE_RG \
--ids $EXPORT_RES_ID $EXPORT_RUN_RES_ID \
--api-version 2019-12-01-preview

# Delete import resources
az resource delete \
--resource-group $TARGET_RG \
--ids $IMPORT_RES_ID $IMPORT_RUN_RES_ID \
--api-version 2019-12-01-preview
```

## <a name="troubleshooting"></a>トラブルシューティング

* **テンプレート デプロイの失敗またはエラー**
  * パイプライン実行が失敗した場合は、実行リソースの `pipelineRunErrorMessage` プロパティを確認します。
  * テンプレート デプロイに関する一般的なエラーについては、「[ARM テンプレート デプロイのトラブルシューティング](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)」を参照してください。
* **ストレージ BLOB のエクスポートまたはインポートに関する問題**
  * SAS トークンの有効期限が切れているか、指定されたエクスポート実行またはインポート実行に対して十分なアクセス許可がない可能性があります。
  * ソース ストレージ アカウントの既存のストレージ BLOB が、複数のエクスポート実行中に上書きされない可能性があります。 エクスポート実行で OverwriteBlob オプションが設定されており、SAS トークンに十分なアクセス許可があることを確認します。
  * インポート実行が成功した後、ターゲット ストレージ アカウントのストレージ BLOB が削除されない可能性があります。 インポート実行で DeleteBlobOnSuccess オプションが設定されており、SAS トークンに十分なアクセス許可があることを確認します。
  * ストレージ BLOB が作成または削除されません。 エクスポート実行またはインポート実行で指定されたコンテナーが存在するか、または指定されたストレージ BLOB が手動でのインポート実行用に存在することを確認します。 
* **AzCopy の問題**
  * [AzCopy の問題のトラブルシューティング](../storage/common/storage-use-azcopy-configure.md#troubleshoot-issues)に関するセクションを参照してください。  
* **成果物の転送に関する問題**
  * 一部の成果物が転送されないか、または 1 つも転送されません。 エクスポート実行で成果物のスペルを確認し、エクスポート実行とインポート実行で BLOB の名前を確認します。 最大 50 個の成果物を転送していることを確認します。
  * パイプライン実行が完了していない可能性があります。 エクスポート実行またはインポート実行には時間がかかることがあります。 
  * パイプラインに関するその他の問題については、Azure Container Registry チームにエクスポート実行またはインポート実行のデプロイ[関連付け ID](../azure-resource-manager/templates/deployment-history.md) を提示してください。


## <a name="next-steps"></a>次のステップ

1 つのコンテナー イメージをパブリック レジストリまたは別のプライベート レジストリから Azure コンテナー レジストリにインポートするには、[az acr import][az-acr-import] コマンド リファレンスを参照してください。

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-login]: /cli/azure/reference-index#az-login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az-keyvault-secret-show
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az-storage-container-generate-sas
[az-storage-blob-list]: /cli/azure/storage/blob#az-storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-deployment-group-delete]: /cli/azure/deployment/group#az-deployment-group-delete
[az-deployment-group-show]: /cli/azure/deployment/group#az-deployment-group-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-import]: /cli/azure/acr#az-acr-import
[az-resource-delete]: /cli/azure/resource#az-resource-delete
