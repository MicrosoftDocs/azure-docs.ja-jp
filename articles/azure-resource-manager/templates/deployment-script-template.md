---
title: テンプレートでデプロイ スクリプトを使用する | Microsoft Docs
description: Azure Resource Manager テンプレートでデプロイ スクリプトを使用する。
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: jgao
ms.openlocfilehash: 4094e610bb290fc11656dc192f3d0a495f679dc5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291794"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>テンプレートでデプロイ スクリプトを使用する (プレビュー)

Azure Resource テンプレートでデプロイ スクリプトを使用する方法を説明します。 `Microsoft.Resources/deploymentScripts` という名前の新しいリソースの種類を使用すると、ユーザーはテンプレートのデプロイでデプロイ スクリプトを実行し、実行結果を確認できます。 これらのスクリプトは、次のようなカスタム手順を実行するために使用できます。

- ユーザーをディレクトリに追加する
- データ プレーン操作 (BLOB のコピー、シード データベースなど) の実行
- ライセンス キーを検索して検証する
- 自己署名証明書を作成する
- Azure AD にオブジェクトを作成する
- カスタム システムから IP アドレス ブロックを検索する

デプロイ スクリプトの利点は次のとおりです。

- 簡単にコーディング、使用、デバッグができます。 デプロイ スクリプトは、好みの開発環境で開発できます。 スクリプトは、テンプレートまたは外部スクリプト ファイルに埋め込むことができます。
- スクリプト言語とプラットフォームを指定できます。 現時点では、Linux 環境の Azure PowerShell および Azure CLI のデプロイ スクリプトがサポートされています。
- スクリプトの実行に使用する ID を指定できるようにします。 現時点では、[Azure ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) のみがサポートされています。
- スクリプトにコマンド ライン引数を渡すことを許可します。
- スクリプトの出力を指定して、デプロイに渡すことができます。

デプロイ スクリプト リソースは、Azure Container Instance が使用可能なリージョンでのみ使用できます。  「[Azure リージョンの Azure Container Instances のリソースの可用性](../../container-instances/container-instances-region-availability.md)」を参照してください。

> [!IMPORTANT]
> スクリプト実行とトラブルシューティングには、ストレージ アカウントとコンテナー インスタンスが必要です。 既存のストレージ アカウントを指定するオプションがあります。指定しない場合は、コンテナー インスタンスと共にストレージ アカウントがスクリプト サービスによって自動的に作成されます。 自動的に作成された 2 つのリソースは、通常、デプロイ スクリプトの実行が終了状態になったときにスクリプト サービスによって削除されます。 リソースが削除されるまでは、リソースに対して請求が行われます。 詳細については、「[デプロイ スクリプト リソースのクリーンアップ](#clean-up-deployment-script-resources)」を参照してください。

## <a name="prerequisites"></a>前提条件

- **ターゲットの resource-group への共同作成者のロールが付与されたユーザー割り当てマネージド ID**。 この ID は、デプロイ スクリプトを実行するために使用されます。 リソース グループの外部で操作を実行するには、追加のアクセス許可を付与する必要があります。 たとえば、新しいリソース グループを作成する場合は、サブスクリプション レベルに ID を割り当てます。

  > [!NOTE]
  > スクリプト サービスは、ストレージ アカウント(既存のストレージ アカウントが指定されていない場合) とコンテナー インスタンスをバックグラウンドで作成します。  サブスクリプションで Azure ストレージ アカウント (Microsoft.Storage) と Azure コンテナー インスタンス (Microsoft.ContainerInstance) リソース プロバイダーを登録していない場合は、サブスクリプション レベルで共同作成者のロールを持つユーザー割り当てマネージド ID が必要です。

  ID を作成するには、[Azure portal を使用してユーザー割り当てマネージド ID を作成する方法](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)、[Azure CLI を使用する方法](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)、または [Azure PowerShell を使用する方法](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)に関する各記事を参照してください。 この識別 ID は、テンプレートをデプロイするときに必要です。 ID の形式は次のとおりです。

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  次の CLI または PowerShell スクリプトを使用して、リソース グループ名と ID 名を指定することで ID を取得します。

  # <a name="cli"></a>[CLI](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g $resourceGroupName -n $idName --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** または **Azure CLI**。 [サポートされている Azure PowerShell バージョン](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)の一覧を参照してください。 [サポートされている Azure CLI バージョン](https://mcr.microsoft.com/v2/azure-cli/tags/list)の一覧を参照してください。

    >[!IMPORTANT]
    > デプロイ スクリプトでは、Microsoft Container Registry (MCR) から入手可能な CLI イメージが使用されます。 デプロイ スクリプトに対する CLI イメージの認定には、1 か月ほどかかります。 30 日以内にリリースされた CLI バージョンは使用しないでください。 イメージのリリース日を確認するには、「[Azure CLI リリース ノート](/cli/azure/release-notes-azure-cli?view=azure-cli-latest)」を参照してください。 サポートされていないバージョンが使用されている場合、サポートされているバージョンがエラー メッセージに一覧表示されます。

    これらのバージョンは、テンプレートのデプロイには必要ありません。 ただし、これらのバージョンは、デプロイ スクリプトをローカルでテストするために必要です。 [Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 事前構成済みの Docker イメージを使用できます。  「[開発環境の設定](#configure-development-environment)」を参照してください。

## <a name="sample-templates"></a>サンプル テンプレート

次の JSON はサンプルです。  最新のテンプレートのスキーマについては、[こちら](/azure/templates/microsoft.resources/deploymentscripts)をご覧ください。

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "someSecret",
        "secureValue": "if this is really a secret, don't put it here... in plain text..."
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> この例は、デモンストレーション用です。  **scriptContent** と **primaryScriptUri** をテンプレート内で共存させることはできません。

プロパティ値の詳細:

- **identity**: デプロイ スクリプト サービスは、ユーザー割り当てのマネージド ID を使用してスクリプトを実行します。 現時点では、ユーザー割り当てマネージド ID のみがサポートされています。
- **kind**: スクリプトの種類を指定します。 現在、Azure PowerShell および Azure CLI のスクリプトがサポートされています。 値は、**AzurePowerShell** と **AzureCLI** です。
- **forceUpdateTag**:テンプレートのデプロイ間でこの値を変更すると、デプロイ スクリプトが強制的に再実行されます。 パラメーターの defaultValue として設定する必要がある newGuid() 関数または utcNow() 関数を使用します。 詳細については、「[スクリプトを複数回実行する](#run-script-more-than-once)」を参照してください。
- **containerSettings**:Azure Container Instance をカスタマイズするための設定を指定します。  **containerGroupName** は、コンテナー グループ名を指定するためのものです。  指定しない場合、グループ名は自動的に生成されます。
- **storageAccountSettings**:既存のストレージ アカウントを使用するための設定を指定します。 指定しない場合、ストレージ アカウントは自動的に作成されます。 「[既存のストレージ アカウントの使用](#use-existing-storage-account)」を参照してください。
- **azPowerShellVersion**/**azCliVersion**:使用するモジュールのバージョンを指定します。 サポートされている PowerShell と CLI のバージョンの一覧については、「[前提条件](#prerequisites)」を参照してください。
- **arguments**: パラメーター値を指定します。 値はスペースで区切ります。

    デプロイ スクリプトは、[CommandLineToArgvW](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) システム呼び出しを起動して、引数を文字列の配列に分割します。 これが必要なのは、引数が [command プロパティ](/rest/api/container-instances/containergroups/createorupdate#containerexec)として Azure コンテナー インスタンスに渡され、その command プロパティは文字列の配列であるためです。

    引数にエスケープ文字が含まれている場合は [JsonEscaper](https://www.jsonescaper.com/) を使用して、文字をダブル エスケープします。 元のエスケープされた文字列をそのツールに貼り付け、 **[エスケープ]** を選択します。  ツールにより、ダブル エスケープされた文字列が出力されます。 たとえば、前のサンプル テンプレートの引数は **-name \\"John Dole\\"** です。  エスケープされた文字列は、 **-name \\\\\\"John dole\\\\\\"** です。

    オブジェクト型の ARM テンプレート パラメーターを引数として渡すには、[string ()](./template-functions-string.md#string) 関数を使用してオブジェクトを文字列に変換してから、[replace ()](./template-functions-string.md#replace) 関数を使用して **\\"** を **\\\\\\"** にすべて置き換えます。 次に例を示します。

    ```json
    replace(string(parameters('tables')), '\"', '\\\"')
    ```

    サンプル テンプレートを表示するには、[ここ](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json)を選択します。

- **environmentVariables**:スクリプトに渡す環境変数を指定します。 詳細については、「[デプロイ スクリプトを開発する](#develop-deployment-scripts)」を参照してください。
- **scriptContent**:スクリプトの内容を指定します。 外部スクリプトを実行するには、代わりに `primaryScriptUri` を使用します。 例については、「[インライン スクリプトを使用する](#use-inline-scripts)」および「[外部スクリプトを使用する](#use-external-scripts)」を参照してください。
- **primaryScriptUri**:サポートされているファイル拡張子を含むプライマリ デプロイ スクリプトへのパブリックにアクセス可能な URL を指定します。
- **supportingScriptUris**:`ScriptContent` または `PrimaryScriptUri` で呼び出されるサポート ファイルへのパブリックにアクセス可能な URL の配列を指定します。
- **timeout**: [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601)で指定される、スクリプトの許容最長実行時間を指定します。 既定値は **P1D** です。
- **cleanupPreference**。 スクリプトの実行がターミナル状態になった際の、デプロイ リソースのクリーンアップ設定を指定します。 既定の設定は、**Always** です。これは、ターミナル状態 (Succeeded、Failed、Canceled) に関係なくリソースを削除することを意味します。 詳細については、「[デプロイ スクリプト リソースのクリーンアップ](#clean-up-deployment-script-resources)」を参照してください。
- **retentionInterval**:デプロイ スクリプトの実行が終了状態に達した後、サービスがデプロイ スクリプト リソースを保持する間隔を指定します。 この期間が経過すると、デプロイ スクリプト リソースは削除されます。 期間は [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) のパターンに基づきます。 既定値は 1 日を意味する **P1D** です。 このプロパティは、cleanupPreference が *OnExpiration* に設定されている場合に使用されます。 *OnExpiration* プロパティは、現在有効になっていません。 詳細については、「[デプロイ スクリプト リソースのクリーンアップ](#clean-up-deployment-script-resources)」を参照してください。

### <a name="additional-samples"></a>その他のサンプル

- [サンプル 1](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json): キー コンテナーを作成し、デプロイ スクリプトを使用してキー コンテナーに証明書を割り当てます。
- [サンプル 2](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json): サブスクリプション レベルでリソース グループを作成し、リソース グループにキー コンテナーを作成した後、デプロイ スクリプトを使用して、キー コンテナーに証明書を割り当てます。
- [サンプル 3](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json): ユーザー割り当てマネージド ID を作成し、リソース グループ レベルで ID に共同作成者ロールを割り当てて、キー コンテナーを作成した後、デプロイ スクリプトを使用してキー コンテナーに証明書を割り当てます。

> [!NOTE]
> 事前にユーザー割り当て ID を作成し、アクセス許可を付与しておくことをお勧めします。 デプロイ スクリプトを実行するものと同じテンプレートで、ID を作成してアクセス許可を付与すると、サインインおよびアクセス許可に関連するエラーが発生する場合があります。 アクセス許可が有効になるまでには少し時間がかかります。

## <a name="use-inline-scripts"></a>インライン スクリプトを使用する

次のテンプレートには、`Microsoft.Resources/deploymentScripts` の種類で定義されたリソースが 1 つあります。 強調表示されている部分は、インライン スクリプトです。

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> インライン デプロイ スクリプトは二重引用符で囲まれているため、デプロイ スクリプト内の文字列は、 **&#92;** を使用してエスケープするか、単一引用符で囲む必要があります。 前の JSON サンプルに示されているように、文字列の置換を使用することを検討することもできます。

このスクリプトは、1 つのパラメーターを受け取り、パラメーター値を出力します。 **DeploymentScriptOutputs** は、出力を格納するために使用されます。  出力セクションの**値**の線は、格納されている値にアクセスする方法を示しています。 `Write-Output` はデバッグ目的で使用されます。 出力ファイルにアクセスする方法については、「[デプロイ スクリプトの監視とトラブルシューティング](#monitor-and-troubleshoot-deployment-scripts)」を参照してください。  プロパティの説明については、「[サンプル テンプレート](#sample-templates)」を参照してください。

スクリプトを実行するには、 **[試してみる]** を選択して Cloud Shell を開き、次のコードをシェル ウィンドウに貼り付けます。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

出力は次のようになります。

![Resource Manager テンプレート デプロイ スクリプトの hello world 出力](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>外部スクリプトを使用する

インライン スクリプトに加えて、外部スクリプト ファイルを使用することもできます。 **ps1** ファイル拡張子を持つプライマリ PowerShell スクリプトのみがサポートされています。 CLI スクリプトの場合、スクリプトが有効な bash スクリプトである限り、プライマリ スクリプトには任意の拡張子を設定できます (または拡張子なしにできます)。 外部スクリプト ファイルを使用するには、`scriptContent` を `primaryScriptUri` に置き換えます。 次に例を示します。

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

例を確認するには、[[こちら]](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json) を選択します。

外部スクリプト ファイルにアクセスできる必要があります。  Azure ストレージ アカウントに格納されているスクリプト ファイルをセキュリティで保護するには、「[SAS トークンを使用してプライベート ARM テンプレートをデプロイする](./secure-template-with-sas-token.md)」を参照してください。

デプロイ スクリプトによって参照されるスクリプトの整合性を確保する必要があります (**PrimaryScriptUri** または **SupportingScriptUris** のいずれか)。  信頼できるスクリプトのみを参照します。

## <a name="use-supporting-scripts"></a>サポート スクリプトを使用する

複雑なロジックを、1 以上のサポート スクリプト ファイルに分けることができます。 `supportingScriptURI` プロパティを使用すると、必要に応じて、サポート スクリプト ファイルに URI の配列を指定できます。

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

サポート スクリプト ファイルは、インライン スクリプトとプライマリ スクリプト ファイルの両方から呼び出すことができます。 サポート スクリプト ファイルには、ファイル拡張子に関する制限はありません。

サポート ファイルは、ランタイムで azscripts/azscriptinput にコピーされます。 インライン スクリプトおよびプライマリ スクリプト ファイルからサポート ファイルを参照するには、相対パスを使用します。

## <a name="work-with-outputs-from-powershell-script"></a>PowerShell スクリプトからの出力を操作する

次のテンプレートは、2 つの deploymentScripts リソース間で値を渡す方法を示しています。

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

最初のリソースでは、 **$DeploymentScriptOutputs** という名前の変数を定義し、それを使用して出力値を格納します。 テンプレート内の別のリソースから出力値にアクセスするには、次のように指定します。

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>CLI スクリプトからの出力を操作する

PowerShell デプロイ スクリプトとは異なり、CLI/bash のサポートでは、スクリプトの出力を格納するための共通変数は公開されません。代わりに、スクリプトの出力ファイルが存在する場所を格納する **AZ_SCRIPTS_OUTPUT_PATH** という環境変数があります。 デプロイ スクリプトが Resource Manager テンプレートから実行される場合、この環境変数は Bash シェルによって自動的に設定されます。

デプロイ スクリプトの出力は AZ_SCRIPTS_OUTPUT_PATH の場所に保存される必要があり、その出力は有効な JSON 文字列オブジェクトでなければなりません。 ファイルの内容は、キーと値のペアとして保存される必要があります。 たとえば、文字列の配列は、{ "MyResult": [ "foo", "bar"] } として格納されます。  配列の結果のみ ([ "foo", "bar" ] など) の格納は、無効です。

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

前のサンプルでは、[jq](https://stedolan.github.io/jq/) が使用されています。 これには、コンテナー イメージが付属しています。 「[開発環境の設定](#configure-development-environment)」を参照してください。

## <a name="use-existing-storage-account"></a>既存のストレージ アカウントを使用する

スクリプト実行とトラブルシューティングには、ストレージ アカウントとコンテナー インスタンスが必要です。 既存のストレージ アカウントを指定するオプションがあります。指定しない場合は、コンテナー インスタンスと共にストレージ アカウントがスクリプト サービスによって自動的に作成されます。 既存のストレージ アカウントを使用するための要件は次のとおりです。

- サポートされるストレージ アカウントの種類:

    | SKU             | サポートされている種類     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Storage、StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Storage、StorageV2 |
    | Standard_RAGRS  | Storage、StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    これらの組み合わせはファイル共有をサポートしています。  詳細については、「[Azure ファイル共有を作成する](../../storage/files/storage-how-to-create-file-share.md)」および「[ストレージ アカウントの種類](../../storage/common/storage-account-overview.md)」を参照してください。
- ストレージ アカウントのファイアウォール規則はまだサポートされていません。 詳細については、[Azure Storage ファイアウォールおよび仮想ネットワークの構成](../../storage/common/storage-network-security.md)に関する記事を参照してください。
- デプロイ スクリプトのユーザー割り当て済みマネージド ID には、ストレージ アカウントを管理するためのアクセス許可が必要です。これには、ファイル共有の読み取り、作成、削除が含まれます。

既存のストレージ アカウントを指定するには、次の json を `Microsoft.Resources/deploymentScripts` のプロパティ要素に追加します。

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- **storageAccountName**: ストレージ アカウントの名前を指定します。
- **storageAccountKey"** : ストレージ アカウント キーのいずれかを指定します。 [`listKeys()`](./template-functions-resource.md#listkeys) 関数を使用して、キーを取得することができます。 次に例を示します。

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

完全な `Microsoft.Resources/deploymentScripts` 定義のサンプルについては、「[サンプル テンプレート](#sample-templates)」を参照してください。

既存のストレージ アカウントを使用すると、スクリプト サービスによって一意の名前を持つファイル共有が作成されます。 スクリプト サービスがファイル共有をクリーンアップする方法については、「[デプロイ スクリプト リソースのクリーンアップ](#clean-up-deployment-script-resources)」を参照してください。

## <a name="develop-deployment-scripts"></a>デプロイ スクリプトを開発する

### <a name="handle-non-terminating-errors"></a>終了しないエラーを処理する

デプロイ スクリプトで **$ErrorActionPreference** 変数を使用することで、終了しないエラーに PowerShell が対応する方法を制御できます。 この変数がデプロイ スクリプトに設定されていない場合、スクリプト サービスは既定値 **Continue** を使用します。

$ErrorActionPreference の設定に関係なく、デプロイ スクリプトでエラーが発生すると、スクリプト サービスによってリソースのプロビジョニングの状態が **[失敗]** に設定されます。

### <a name="pass-secured-strings-to-deployment-script"></a>セキュリティで保護された文字列をデプロイ スクリプトに渡す

コンテナー インスタンスで環境変数 (EnvironmentVariable) を設定すると、コンテナーによって実行されるアプリケーションまたはスクリプトの動的な構成を提供できます。 デプロイ スクリプトでは、Azure Container Instance と同じ方法で、セキュリティで、セキュリティで保護されていない環境変数と保護されている環境変数が処理されます。 詳細については、「[コンテナー インスタンスで環境変数を設定する](../../container-instances/container-instances-environment-variables.md#secure-values)」を参照してください。

環境変数の最大許容サイズは 64 KB です。

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>デプロイ スクリプトの監視とトラブルシューティング

スクリプト サービスは、[ストレージ アカウント](../../storage/common/storage-account-overview.md) (既存のストレージ アカウントが指定されていない場合) と、スクリプト実行用の[コンテナー インスタンス](../../container-instances/container-instances-overview.md)を作成します。 これらのリソースがスクリプト サービスによって自動的に作成される場合、両方のリソースには、リソース名に **azscripts** サフィックスが付けられます。

![Resource Manager テンプレート デプロイ スクリプトのリソース名](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

ユーザー スクリプト、実行結果、stdout ファイルは、ストレージ アカウントのファイル共有に格納されます。 **Azscripts** という名前のフォルダーがあります。 このフォルダーには、**azscriptinput** と **azscriptinput** という、入力用と出力ファイル用の 2 つのフォルダーがあります。

出力フォルダーには、**executionresult.json** とスクリプトの出力ファイルが含まれています。 **executionresult.json** で、スクリプト実行のエラー メッセージを確認できます。 出力ファイルは、スクリプトが正常に実行された場合にのみ作成されます。 入力フォルダーには、システム用 PowerShell スクリプト ファイルとユーザー用デプロイ スクリプト ファイルが含まれています。 ユーザー用デプロイ スクリプトを変更したものに置き換え、Azure コンテナー インスタンスからデプロイ スクリプトを再実行することができます。

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

デプロイ スクリプト リソースをデプロイすると、Azure portal のリソース グループの下にそのリソースが一覧表示されます。 次のスクリーンショットは、デプロイ スクリプト リソースの [概要] ページを示しています。

![Resource Manager テンプレートのデプロイ スクリプトのポータルの概要](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

概要ページには、リソースの重要な情報が表示されます。たとえば、 **[プロビジョニングの状態]** 、 **[ストレージ アカウント]** 、 **[コンテナー インスタンス]** 、 **[ログ]** などです。

左側のメニューから、デプロイ スクリプトの内容、スクリプトに渡される引数、および出力を表示できます。  デプロイ スクリプトを含む、デプロイ スクリプトのテンプレートをエクスポートすることもできます。

### <a name="use-powershell"></a>PowerShell の使用

Azure PowerShell を使用すると、サブスクリプションまたはリソース グループのスコープでデプロイ スクリプトを管理できます。

- [Get-AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript):デプロイ スクリプトを取得または一覧表示します。
- [Get-AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog):デプロイ スクリプト実行のログを取得します。
- [Remove-AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript):デプロイ スクリプトとそれに関連付けられているリソースを削除します。
- [Save-AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog):デプロイ スクリプト実行のログをディスクに保存します。

Get-AzDeploymentScript の出力は次のようになります。

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Azure CLI の使用

Azure CLI を使用すると、サブスクリプションまたはリソース グループのスコープでデプロイ スクリプトを管理できます。

- [az deployment-scripts delete](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-delete):デプロイ スクリプトを削除します。
- [az deployment-scripts list](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-list):すべてのデプロイ スクリプトを一覧表示します。
- [az deployment-scripts show](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-show):デプロイ スクリプトを取得します。
- [az deployment-scripts show-log](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-show-log):デプロイ スクリプトのログを表示します。

list コマンドの出力は次のようになります。

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>REST API を使用する

デプロイ スクリプト リソースのデプロイ情報は、REST API を使用して、リソース グループ レベルとサブスクリプション レベルで取得できます。

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

[ARMClient](https://github.com/projectkudu/ARMClient) を使用する例を次に示します。

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

次のように出力されます。

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

次の REST API では、ログが返されます。

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

デプロイ スクリプト リソースが削除される前にのみ機能します。

ポータルで deploymentScripts リソースを表示するには、 **[非表示の型の表示]** を選択します。

![Resource Manager テンプレートのデプロイ スクリプト、非表示の型の表示、ポータル](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>デプロイ スクリプト リソースのクリーンアップ

スクリプト実行とトラブルシューティングには、ストレージ アカウントとコンテナー インスタンスが必要です。 既存のストレージ アカウントを指定するオプションがあります。指定しない場合は、コンテナー インスタンスと共にストレージ アカウントがスクリプト サービスによって自動的に作成されます。 自動的に作成された 2 つのリソースは、デプロイ スクリプトの実行が終了状態になったときにスクリプト サービスによって削除されます。 リソースが削除されるまでは、リソースに対して請求が行われます。 価格情報については、「[Container Instances の価格](https://azure.microsoft.com/pricing/details/container-instances/)」と「[Azure Storage の料金](https://azure.microsoft.com/pricing/details/storage/)」を参照してください。

これらのリソースのライフ サイクルは、テンプレートの次のプロパティによって制御されます。

- **cleanupPreference**:スクリプトの実行がターミナル状態になった再のクリーンアップ設定 サポートされる値は

  - **Always**:スクリプトの実行が終了状態になったときに、自動的に作成されたリソースを削除します。 既存のストレージ アカウントが使用されている場合は、スクリプト サービスによって、ストレージ アカウントに作成されたファイル共有が削除されます。 リソースがクリーンアップされた後も deploymentScripts リソースが依然として存在する可能性があるため、リソースが削除される前に、スクリプト サービスがスクリプトの実行結果 (stdout、出力、戻り値など) を保持します。
  - **OnSuccess**:スクリプトの実行が成功した場合にのみ、自動的に作成されたリソースを削除します。 既存のストレージ アカウントが使用されている場合は、スクリプトの実行が成功した場合にのみ、スクリプト サービスによってファイル共有が削除されます。 引き続きリソースにアクセスして、デバッグ情報を見つけることができます。
  - **OnExpiration**:**retentionInterval** 設定の有効期限が切れている場合にのみ、自動的に作成されたリソースを削除します。 既存のストレージ アカウントが使用されている場合は、スクリプト サービスによってファイル共有が削除されますが、ストレージ アカウントは保持されます。

- **retentionInterval**:スクリプト リソースを保持する期間を指定します。この時間が経過すると、期限切れになり削除されます。

> [!NOTE]
> スクリプト サービスによって生成されたストレージ アカウントとコンテナー インスタンスを、他の目的で使用しないことをお勧めします。 この 2 つのリソースは、スクリプト ライフ サイクルに応じて削除される場合があります。

## <a name="run-script-more-than-once"></a>スクリプトを複数回実行する

デプロイ スクリプトの実行はべき等操作です。 deploymentScripts リソースのプロパティ (インライン スクリプトを含む) が変更されていない場合、テンプレートを再デプロイしてもスクリプトは実行されません。 デプロイ スクリプト サービスは、テンプレート内のリソース名と同じリソース グループ内の既存のリソースを比較します。 同じデプロイ スクリプトを複数回実行する場合は、次の 2 つのオプションがあります。

- deploymentScripts リソースの名前を変更します。 たとえば、[utcNow](./template-functions-date.md#utcnow) テンプレート関数をリソース名として使用するか、リソース名の一部として使用します。 リソース名を変更すると、新しい deploymentScripts リソースが作成されます。 これは、スクリプトの実行履歴を保持するのに適しています。

    > [!NOTE]
    > この utcNow 関数は、パラメーターの既定値でのみ使用できます。

- `forceUpdateTag` テンプレート プロパティに別の値を指定してください。  たとえば、値として utcNow を使用します。

> [!NOTE]
> べき等であるデプロイ スクリプトを記述します。 それにより、誤って再び実行されることがあっても、システム変更が引き起こされません。 たとえば、デプロイ スクリプトを使用して Azure リソースを作成する場合は、スクリプトが成功するか、リソースが再度作成されないよう、作成する前にリソースが存在しないことを確認してください。

## <a name="configure-development-environment"></a>開発環境の設定

デプロイ スクリプト開発環境として、事前構成済みのコンテナー イメージを使用できます。 詳細については、「[Configure development environment for deployment scripts in templates](./deployment-script-template-configure-dev.md)」(テンプレートでデプロイ スクリプトの開発環境を構成する) を参照してください。

テストが正常に完了したスクリプトは、テンプレート内のデプロイ スクリプトとして使用できます。

## <a name="deployment-script-error-codes"></a>デプロイ スクリプトのエラー コード

| エラー コード | 説明 |
|------------|-------------|
| DeploymentScriptInvalidOperation | テンプレートのデプロイ スクリプト リソース定義に、無効なプロパティ名が含まれています。 |
| DeploymentScriptResourceConflict | ターミナル以外の状態のデプロイ スクリプト リソースを削除することはできません。また、実行時間が 1 時間を超えていません。 または、同じリソース識別子 (同じサブスクリプション、リソース グループ名、リソース名) を使用して同じデプロイ スクリプトを再実行することはできませんが、別のスクリプト本文のコンテンツは同時に再実行できます。 |
| DeploymentScriptOperationFailed | 内部でデプロイ スクリプトの操作が失敗しました。 Microsoft サポートにお問い合わせください。 |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | 既存のストレージ アカウントにアクセス キーが指定されていません。|
| DeploymentScriptContainerGroupContainsInvalidContainers | デプロイ スクリプト サービスによって作成されたコンテナー グループが外部で変更され、無効なコンテナーが追加されました。 |
| DeploymentScriptContainerGroupInNonterminalState | 2 つ以上のデプロイ スクリプト リソースが、同じリソース グループ内で同じ Azure コンテナー インスタンス名を使用しており、そのうちの 1 つがまだ実行を完了していません。 |
| DeploymentScriptStorageAccountInvalidKind | BlobBlobStorage または BlobStorage 型の既存のストレージ アカウントはファイル共有をサポートしていないため、使用できません。 |
| DeploymentScriptStorageAccountInvalidKindAndSku | 既存のストレージ アカウントはファイル共有をサポートしていません。 サポートされているストレージ アカウントの種類の一覧については、「[既存のストレージ アカウントを使用する](#use-existing-storage-account)」を参照してください。 |
| DeploymentScriptStorageAccountNotFound | ストレージ アカウントが存在しないか、外部のプロセスまたはツールによって削除されています。 |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | 指定されたストレージ アカウントには、サービス エンドポイントがあります。 サービス エンドポイントを持つストレージ アカウントはサポートされていません。 |
| DeploymentScriptStorageAccountInvalidAccessKey | 既存のストレージ アカウントに無効なアクセス キーが指定されています。 |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | ストレージ アカウント キーの形式が無効です。 「[ストレージ アカウント アクセス キーを管理する](../../storage/common/storage-account-keys-manage.md)」をご覧ください。 |
| DeploymentScriptExceededMaxAllowedTime | デプロイ スクリプトの実行時間が、デプロイ スクリプトのリソース定義で指定されているタイムアウト値を超えました。 |
| DeploymentScriptInvalidOutputs | デプロイ スクリプトの出力が有効な JSON オブジェクトではありません。 |
| DeploymentScriptContainerInstancesServiceLoginFailure | ユーザー割り当て済みマネージド ID を使って、1 分間隔で 10 回ログインを試行しましたが、ログインできませんでした。 |
| DeploymentScriptContainerGroupNotFound | デプロイ スクリプト サービスによって作成されたコンテナー グループは、外部ツールまたはプロセスによって削除されました。 |
| DeploymentScriptDownloadFailure | サポート スクリプトをダウンロードできませんでした。 「[サポート スクリプトを使用する](#use-supporting-scripts)」を参照してください。|
| DeploymentScriptError | ユーザー スクリプトがエラーをスローしました。 |
| DeploymentScriptBootstrapScriptExecutionFailed | ブートストラップ スクリプトがエラーをスローしました。 ブートストラップ スクリプトは、デプロイ スクリプトの実行を調整するシステム スクリプトです。 |
| DeploymentScriptExecutionFailed | デプロイ スクリプトの実行中に不明なエラーが発生しました。 |
| DeploymentScriptContainerInstancesServiceUnavailable | Azure Container Instances (ACI) を作成するときに、ACI がサービス利用不可エラーをスローしました。 |
| DeploymentScriptContainerGroupInNonterminalState | Azure Container Instances (ACI) を作成するときに、別のデプロイ スクリプトが同じスコープ (同じサブスクリプション、リソース グループ名、リソース名) で同じ ACI 名を使用しています。 |
| DeploymentScriptContainerGroupNameInvalid | 指定された Azure Container Instances (ACI) 名は、ACI の要件を満たしていません。 「[Azure Container Instances における、トラブルシューティングに関する一般的問題](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment)」を参照してください。|

## <a name="next-steps"></a>次のステップ

この記事では、デプロイ スクリプトの使用方法について学習しました。 デプロイ スクリプトのチュートリアルを詳しく見るには、次を確認してください。

> [!div class="nextstepaction"]
> [チュートリアル:Azure Resource Manager テンプレートでデプロイ スクリプトを使用する](./template-tutorial-deployment-script.md)
