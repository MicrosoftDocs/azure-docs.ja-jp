---
title: Azure Deployment Manager を使用してテンプレートをデプロイする
description: Resource Manager テンプレートで Azure Deployment Manager を使用して、Azure リソースをデプロイする方法を説明します。
author: mumian
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 95d5067eccff5c847588834061db8454f75e55d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627585"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>チュートリアル:Resource Manager テンプレートで Azure Deployment Manager を使用する (パブリック プレビュー)

[Azure Deployment Manager](./deployment-manager-overview.md) を使用して、アプリケーションを複数の地域に配備する方法を学習します。 高速化のアプローチを採用する場合は、[Azure Deployment Manager のクイック スタート](https://github.com/Azure-Samples/adm-quickstart)に従って、サブスクリプションに必要な構成を作成し、複数のリージョン全体に 1 つのアプリケーションをデプロイするように成果物をカスタマイズします。 このクイック スタートでは、このチュートリアルで実行するのと同じタスクを実行します。

Deployment Manager を使用するには、次の 2 つのテンプレートを作成する必要があります。

* **トポロジ テンプレート**: アプリケーションを構成する Azure リソースとその配備先を記述します。
* **ロールアウト テンプレート**: アプリケーションを配備する際に行う手順を記述します。

> [!IMPORTANT]
> お客様のサブスクリプションが Azure の新機能をテストするようにカナリアに対してマークされている場合は、カナリア リージョンへのデプロイには Azure Deployment Manager しか使用できません。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * シナリオの理解
> * チュートリアル ファイルのダウンロード
> * 成果物の準備
> * ユーザー定義マネージド ID の作成
> * サービス トポロジ テンプレートの作成
> * ロールアウト テンプレートの作成
> * テンプレートの配備
> * デプロイを検証する
> * 新規バージョンの配備
> * リソースをクリーンアップする

その他のリソース:

* [Azure Deployment Manager REST API リファレンス](/rest/api/deploymentmanager/)。
* [チュートリアル:Use health check in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)」 (チュートリアル: Azure Deployment Manager で正常性チェックを使用する) を参照してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* Azure のサブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
* [Azure Resource Manager テンプレート](overview.md)を開発した経験がある。
* Azure PowerShell。 詳細については、[Azure PowerShell の概要](/powershell/azure/get-started-azureps)に関するページを参照してください。
* Deployment Manager コマンドレット。 これらのプレリリース版のコマンドレットをインストールするには、最新バージョンの PowerShellGet が必要です。 最新バージョンを入手するには、「[PowerShellGet のインストール](/powershell/scripting/gallery/installing-psget)」を参照してください。 PowerShellGet をインストールし終えたら、PowerShell ウィンドウを閉じます。 管理者特権の PowerShell ウィンドウを新たに開き、次のコマンドを使用します。

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>シナリオの理解

サービス トポロジ テンプレートでは、サービスを構成する Azure リソースとそのデプロイ先を記述します。 サービス トポロジの定義には次の階層があります。

* サービス トポロジ
  * サービス
    * サービス ユニット

次の図は、このチュートリアルで使用されるサービス トポロジを示したものです。

![Azure Deployment Manager チュートリアルのシナリオ図](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

米国西部と米国東部の場所に割り当てられた 2 つのサービスがあります。 各サービスには、フロントエンド Web アプリケーションとバックエンド ストレージ アカウントの 2 つのサービス ユニットがあります。 サービス ユニットの定義には、Web アプリケーションとストレージ アカウントを作成するテンプレートおよびパラメーター ファイルへのリンクが含まれています。

## <a name="download-the-tutorial-files"></a>チュートリアル ファイルのダウンロード

1. このチュートリアルで使用される[テンプレートと成果物](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip)をダウンロードします。
1. 地域のコンピューターにファイルを解凍します。

ルート フォルダーには次の 2 つのフォルダーがあります。

* _ADMTemplates_: 次を含む Deployment Manager テンプレートが収納されます。
  * _CreateADMServiceTopology.json_
  * _CreateADMServiceTopology.Parameters.json_
  * _CreateADMRollout.json_
  * _CreateADMRollout.Parameters.json_
* _ArtifactStore_: テンプレート成果物とバイナリ成果物の両方が収納されます。 「[成果物の準備](#prepare-the-artifacts)」を参照してください。

2 つのテンプレートのセットがあります。 1 つのセットは、サービス トポロジとロールアウトをデプロイするために使用される Deployment Manager テンプレートです。 もう 1 つのセットは、Web サービスとストレージ アカウントを作成するためにサービス ユニットから呼び出されます。

## <a name="prepare-the-artifacts"></a>成果物の準備

ダウンロードした ArtifactStore フォルダーには、次の 2 つのフォルダーがあります。

![Azure Deployment Manager チュートリアルの成果物ソース図](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* _テンプレート_ フォルダー: テンプレート成果物が収納されます。 _1.0.0.0_ と _1.0.0.1_ のフォルダーは、バイナリ成果物の 2 つのバージョンを表します。 各バージョン内には、各サービス用のフォルダーがあります。_ServiceEUS_ (サービス米国東部) と _ServiceWUS_ (サービス米国西部) です。 各サービスには、ストレージ アカウントを作成するためのテンプレート ファイルとパラメータ ファイルのペアと、Web アプリケーションを作成するための別のペアがあります。 Web アプリケーション テンプレートは、Web アプリケーション ファイルを収納する圧縮パッケージを呼び出します。 圧縮ファイルは、バイナリ フォルダーに格納されるバイナリ成果物です。
* _バイナリ_ フォルダー: バイナリ成果物が収納されます。 _1.0.0.0_ と _1.0.0.1_ のフォルダーは、バイナリ成果物の 2 つのバージョンを表します。 各バージョン内には、米国西部の場所で Web アプリケーションを作成するための 1 つの ZIP ファイルと、米国東部の場所で Web アプリケーションを作成するための別の ZIP ファイルがあります。

2 つのバージョン (1.0.0.0 と 1.0.0.1) は、[リビジョン配備](#deploy-the-revision)用です。 テンプレート成果物とバイナリ成果物の両方に 2 つのバージョンがある場合でも、2 つのバージョン間で違いがあるのはバイナリ成果物のみです。 実際に、バイナリ成果物はテンプレート成果物よりも頻繁に更新されます。

1. テキスト エディターで、 _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json_ を開きます。 これはストレージ アカウントを作成するための基本的なテンプレートです。
1. _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json_ を開きます。

    ![Azure Deployment Manager チュートリアルでは、Web アプリケーション テンプレートを作成します。](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    そのテンプレートは、Web アプリケーションのファイルを収納する配備パッケージを呼び出します。 このチュートリアルでは、圧縮パッケージには _index.html_ ファイルのみが収納されています。
1. _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json_ を開きます。

    ![Azure Deployment Manager チュートリアルでは、Web アプリケーション テンプレート パラメーターの containerRoot を作成します。](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    `deployPackageUri` の値は展開パッケージへのパスです。 パラメーターには `$containerRoot` 変数があります。 `$containerRoot` の値は、成果物ソース SAS の場所、成果物ルート、および `deployPackageUri` を連結することにより、[ロールアウト テンプレート](#create-the-rollout-template)で提供されます。
1. _\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html_ を開きます。

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    HTML では場所とバージョン情報が示されます。 _1.0.0.1_ フォルダー内のバイナリ ファイルは、_Version 1.0.0.1_ であることを示します。 サービスを配備し終えたら、これらのページを参照できます。
1. その他の成果物ファイルをチェックアウトします。 これは、シナリオをさらに良く理解するのに役立ちます。

テンプレート成果物はサービス トポロジ テンプレートで使用され、バイナリ成果物はロールアウト テンプレートで使用されます。 トポロジー テンプレートとロールアウト テンプレートは両方とも、成果物ソースの Azure リソースを定義します。これは、配備で使用されるテンプレート成果物とバイナリー成果物に、Resource Manager をポイントするために使用されるリソースです。 チュートリアルを簡略化するために、1 つのストレージ アカウントを使用して、テンプレート成果物とバイナリ成果物を両方とも格納します。 どちらの成果物ソースも、同じストレージ アカウントをポイントします。

以下の PowerShell スクリプトを実行して、リソース グループとストレージ コンテナーと BLOB コンテナーを作成し、ダウンロードしたファイルをアップロードした後、SAS トークンを作成します。

> [!IMPORTANT]
> PowerShell スクリプトの `projectName` は、このチュートリアルでデプロイされる Azure サービスの名前を生成するために使用します。 Azure サービスが異なると、名前に関する要件も異なります。 デプロイを確実に成功させるには、小文字と数字のみが含まれた 12 文字未満の名前を選びます。
> プロジェクト名のコピーを保存します。 このチュートリアルを通じて、同じ `projectName` を使用します。

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$filePath = Read-Host -Prompt "Enter the folder that contains the downloaded files"


$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$containerName = "admfiles"
$filePathArtifacts = "${filePath}\ArtifactStore"

New-AzResourceGroup -Name $resourceGroupName -Location $location

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$storageContext = $storageAccount.Context

$storageContainer = New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off


$filesToUpload = Get-ChildItem $filePathArtifacts -Recurse -File

foreach ($x in $filesToUpload) {
    $targetPath = ($x.fullname.Substring($filePathArtifacts.Length + 1)).Replace("\", "/")

    Write-Verbose "Uploading $("\" + $x.fullname.Substring($filePathArtifacts.Length + 1)) to $($storageContainer.CloudBlobContainer.Uri.AbsoluteUri + "/" + $targetPath)"
    Set-AzStorageBlobContent -File $x.fullname -Container $storageContainer.Name -Blob $targetPath -Context $storageContext | Out-Null
}

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

SAS トークンを含んだ URL をコピーしてください。 この URL は、トポロジ パラメーター ファイルとロールアウト パラメーター ファイルの 2 つのパラメーター ファイルにフィールドを設定するために必要です。

Azure portal からコンテナーを開き、_binaries_ と _templates_ の両方のフォルダーとファイルがアップロードされていることを確認します。

## <a name="create-the-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID の作成

チュートリアルの後半では、ロールアウトを配備します。 (Web アプリケーションやストレージ アカウントの配備などの) 配備操作を実行するには、ユーザー割り当てマネージド ID が必要です。 この ID には、サービスを配備している Azure サブスクリプションへのアクセス権が与えられている必要があり、成果物の配備を完結するには十分な権限が必要です。

ユーザー割り当てマネージド ID を作成し、サブスクリプション用にアクセス制御を構成する必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. [ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) を作成します。
1. ポータルから、左側のメニューで **[サブスクリプション]** を選択したら、お使いのサブスクリプションを選択します。
1. **[アクセス制御 (IAM)]** を選択したら、 **[ロール割り当ての追加]** を選択します。
1. 次の値を入力または選択します。

    ![Azure Deployment Manager チュートリアルでのユーザー割り当てマネージド ID のアクセス制御](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **ロール**: 成果物の配備 (Web アプリケーションとストレージ アカウント) を完結するための十分な権限を与えます。 このチュートリアルでは、 **[共同作成者]** を選択します。 実際には、アクセス許可を最小限に抑えます。
    * **[アクセスの割り当て先]** : **[ユーザー割り当てマネージド ID]** を選択します。
    * チュートリアルの前半で作成したユーザー割り当てマネージド ID を選択します。
1. **[保存]** を選択します。

## <a name="create-the-service-topology-template"></a>サービス トポロジ テンプレートの作成

_\ADMTemplates\CreateADMServiceTopology.json_ を開きます。

### <a name="the-parameters"></a>パラメーター

テンプレートには、次のパラメーターが収納されています。

* `projectName`: この名前は、Deployment Manager リソース用の名前を作成するために使用されます。 たとえば、**demo** を使用すると、サービス トポロジ名は **demo** ServiceTopology になります。 リソース名は、テンプレートの `variables` セクションで定義されます。
* `azureResourcelocation`:チュートリアルを簡略化するため、特に指定しない限り、すべてのリソースはこの場所を共有します。
* `artifactSourceSASLocation`:配備用にサービス ユニットのテンプレートとパラメーターのファイルが格納されている BLOB コンテナーへのSAS URI です。 「[成果物の準備](#prepare-the-artifacts)」を参照してください。
* `templateArtifactRoot`:テンプレートとパラメーターが格納されている BLOB コンテナーからのオフセット パスです。 既定値は _templates/1.0.0.0_ です。 「[成果物の準備](#prepare-the-artifacts)」で説明されているフォルダー構造を変更しない限り、この値を変更しないでください。 このチュートリアルでは、相対パスを使用します。 フルパスは、`artifactSourceSASLocation`、`templateArtifactRoot`、`templateArtifactSourceRelativePath` (または `parametersArtifactSourceRelativePath`) を連結することで構成されます。
* `targetSubscriptionID`:Deployment Manager のリソースが配備され、請求される予定のサブスクリプションID です。 このチュートリアルでは、サブスクリプション ID を使用します。

### <a name="the-variables"></a>変数

変数セクションでは、リソースの名前、2 つのサービス(`ServiceWUS` と `ServiceEUS`) に対する Azure の場所、および次の成果物のパスを定義します。

![Azure Deployment Manager チュートリアルのトポロジ テンプレート変数](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

成果物パスを、ストレージ アカウントにアップロードしたフォルダー構造と比較します。 成果物パスは相対パスであることに注意してください。 フルパスは、`artifactSourceSASLocation`、`templateArtifactRoot`、`templateArtifactSourceRelativePath` (または `parametersArtifactSourceRelativePath`) を連結することで構成されます。

### <a name="the-resources"></a>リソース

ルート レベルでは、"*成果物ソース*" と "*サービス トポロジ*" の 2 つのリソースが定義されています。

成果物ソースの定義は次のとおりです。

![Azure Deployment Manager チュートリアルのトポロジ テンプレート リソース成果物ソース](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

次のスクリーンショットでは、サービス トポロジ、サービス、サービス ユニットの定義の一部のみを示しています。

![Azure Deployment Manager チュートリアルのトポロジ テンプレート リソース サービス トポロジ](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* `artifactSourceId`:成果物ソース リソースをサービス トポロジ リソースに関連付けるために使用されます。
* `dependsOn`:すべてのサービス トポロジ リソースは、成果物ソース リソースに依存します。
* `artifacts`:テンプレート成果物をポイントします。 ここでは相対パスが使用されます。 完全なパスは、(成果物ソースで定義される) `artifactSourceSASLocation`、(成果物ソースで定義される) `artifactRoot`、および `templateArtifactSourceRelativePath` (または `parametersArtifactSourceRelativePath`) を連結することで作成されます。

### <a name="topology-parameters-file"></a>トポロジ パラメーター ファイル

トポロジ テンプレートで使用されるパラメーター ファイルを作成します。

1. Visual Studio Code またはいずれかのテキスト エディターで、 _\ADMTemplates\CreateADMServiceTopology.Parameters.json_ を開きます。
1. パラメーター値を入力します。

    * `projectName`:4-5 文字の文字列を入力します。 この名前は、一意の Azure リソース名を作成するために使用されます。
    * `azureResourceLocation`:Azure の場所が不明な場合、このチュートリアルでは **centralus** を使用してください。
    * `artifactSourceSASLocation`:配備用にサービス ユニットのテンプレートとパラメーターのファイルが格納されているルート ディレクトリ (BLOB コンテナー) への SAS URI を入力します。  「[成果物の準備](#prepare-the-artifacts)」を参照してください。
    * `templateArtifactRoot`:成果物のフォルダー構造を変更しない限り、このチュートリアルでは _templates/1.0.0.0_ を使用してください。

> [!IMPORTANT]
> トポロジ テンプレートとロールアウト テンプレートでは、いくつかの共通パラメーターを共有します。 これらのパラメーターは、同じ値でなければなりません。 これらのパラメーターとは、`projectName`、`azureResourceLocation`、`artifactSourceSASLocation` のことです (このチュートリアルでは、どちらの成果物ソースも同じストレージ アカウントを共有します)。

## <a name="create-the-rollout-template"></a>ロールアウト テンプレートの作成

_\ADMTemplates\CreateADMRollout.json_ を開きます。

### <a name="the-parameters"></a>パラメーター

テンプレートには、次のパラメーターが収納されています。

![Azure Deployment Manager チュートリアルのロールアウト テンプレート パラメーター](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* `projectName`: この名前は、Deployment Manager リソース用の名前を作成するために使用されます。 たとえば、**demo** を使用すると、ロールアウト名は **demo** Rollout になります。 名前は、テンプレートの `variables` セクションで定義されます。
* `azureResourcelocation`:チュートリアルを簡略化するため、特に指定しない限り、すべての Deployment Manager リソースはこの場所を共有します。
* `artifactSourceSASLocation`:配備用にサービス ユニットのテンプレートとパラメーターのファイルが格納されているルート ディレクトリ (BLOB コンテナー) への SAS URI です。 「[成果物の準備](#prepare-the-artifacts)」を参照してください。
* `binaryArtifactRoot`:既定値は _binaries/1.0.0.0_ です。 「[成果物の準備](#prepare-the-artifacts)」で説明されているフォルダー構造を変更しない限り、この値を変更しないでください。 このチュートリアルでは、相対パスを使用します。 完全なパスは、`artifactSourceSASLocation`、`binaryArtifactRoot`、および _CreateWebApplicationParameters.json_ で指定された `deployPackageUri` を連結することで作成されます。 「[成果物の準備](#prepare-the-artifacts)」を参照してください。
* `managedIdentityID`:配備操作を実行するユーザー割り当てマネージド ID です。 「[ユーザー割り当てマネージド ID の作成](#create-the-user-assigned-managed-identity)」を参照してください。

### <a name="the-variables"></a>変数

`variables` セクションでは、リソースの名前を定義します。 サービス トポロジ名、サービス名、サービス ユニット名が、[トポロジ テンプレート](#create-the-service-topology-template)で定義されている名前と一致していることを確認してください。

![Azure Deployment Manager チュートリアルのロールアウト テンプレート変数](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>リソース

ルート レベルでは、成果物ソース、手順、ロールアウトの 3 つのリソースが定義されています。

成果物ソースの定義は、トポロジ テンプレートで定義されているものと同じです。 詳細については、「[サービス トポロジ テンプレートの作成](#create-the-service-topology-template)」を参照してください。

次のスクリーンショットでは、`wait` 手順の定義を示しています。

![Azure Deployment Manager チュートリアルのロールアウト テンプレート リソース待機手順](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

期間では [ISO 8601 標準](https://en.wikipedia.org/wiki/ISO_8601#Durations)を使用しています。 **PT1M** (大文字必須) は 1 分間の待機の例です。

次のスクリーンショットでは、ロールアウトの定義の一部のみを示しています。

![Azure Deployment Manager チュートリアルのロールアウト テンプレート リソース ロールアウト](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* `dependsOn`:ロールアウト リソースは、成果物ソース リソースに、そして定義されているいずれかの手順に依存します。
* `artifactSourceId`:成果物ソース リソースをロールアウト リソースに関連付けるために使用されます。
* `targetServiceTopologyId`:サービス トポロジ リソースをロールアウト リソースに関連付けるために使用されます。
* `deploymentTargetId`:サービス トポロジ リソースのサービス ユニット リソース ID です。
* `preDeploymentSteps` および `postDeploymentSteps`:ロールアウト手順が含まれています。 テンプレートでは、`wait` 手順が呼び出されます。
* `dependsOnStepGroups`:手順グループ間での依存関係を構成します。

### <a name="rollout-parameters-file"></a>ロールアウト パラメーター ファイル

ロールアウト テンプレートで使用されるパラメーター ファイルを作成します。

1. Visual Studio Code またはいずれかのテキスト エディターで、 _\ADMTemplates\CreateADMRollout.Parameters.json_ を開きます。
1. パラメーター値を入力します。

    * `projectName`:4-5 文字の文字列を入力します。 この名前は、一意の Azure リソース名を作成するために使用されます。
    * `azureResourceLocation` :Azure の場所を指定します。
    * `artifactSourceSASLocation`:配備用にサービス ユニットのテンプレートとパラメーターのファイルが格納されているルート ディレクトリ (BLOB コンテナー) への SAS URI を入力します。  「[成果物の準備](#prepare-the-artifacts)」を参照してください。
    * `binaryArtifactRoot`:成果物のフォルダー構造を変更しない限り、このチュートリアルでは _binaries/1.0.0.0_ を使用してください。
    * `managedIdentityID`:ユーザー割り当てマネージド ID を入力します。 「[ユーザー割り当てマネージド ID の作成](#create-the-user-assigned-managed-identity)」を参照してください。 の構文は次のとおりです。

        ```json
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> トポロジ テンプレートとロールアウト テンプレートでは、いくつかの共通パラメーターを共有します。 これらのパラメーターは、同じ値でなければなりません。 これらのパラメーターとは、`projectName`、`azureResourceLocation`、`artifactSourceSASLocation` のことです (このチュートリアルでは、どちらの成果物ソースも同じストレージ アカウントを共有します)。

## <a name="deploy-the-templates"></a>テンプレートの配備

Azure PowerShell を使用すればテンプレートを配備できます。

1. サービス トポロジを配備するには、スクリプトを実行します。

    ```azurepowershell
    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    「[成果物の準備](#prepare-the-artifacts)」のスクリプトを実行したときとは異なる PowerShell セッションからこのスクリプトを実行する場合は、最初に変数 ( `$resourceGroupName` や `$filePath` など) の値を再設定する必要があります。

    > [!NOTE]
    > `New-AzResourceGroupDeployment` は非同期呼び出しです。 成功メッセージは、デプロイが正常に開始されたことだけを意味します。 デプロイを確認するには、この手順の手順 2. と手順 4. を参照してください。

1. サービス トポロジと下線付きのリソースが、Azure portal を使用することで正常に作成されていることを確認します。

    ![Azure Deployment Manager チュートリアルの配備済みサービス トポロジ リソース](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    リソースを参照するには、 **[非表示型の表示]** を選択する必要があります。

1. <a id="deploy-the-rollout-template"></a>次のロールアウト テンプレートをデプロイします。

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

1. 次の PowerShell スクリプトを使用して、ロールアウトの進行状況を確認します。

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    このコマンドレットを実行する前に、Deployment Manager PowerShell コマンドレットをインストールする必要があります。 「[前提条件](#prerequisites)」を参照してください。 `-Verbose` パラメーターを使用すると、出力全体を表示することができます。

    次のサンプルでは、実行状況を示しています。

    ```Output
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    ロールアウトが正常にデプロイされたら、サービスごとに 1 つずつの、2 つのリソース グループがさらに作成されていることがわかります。

## <a name="verify-the-deployment"></a>デプロイを検証する

1. [Azure Portal](https://portal.azure.com)を開きます。
1. ロールアウトのデプロイによって作成された新しいリソース グループの下で、新たに作成された Web アプリケーションを参照します。
1. Web ブラウザーで、その Web アプリケーションを開きます。 _index.html_ ファイルで、場所とバージョンを確認します。

## <a name="deploy-the-revision"></a>リビジョンの配備

Web アプリケーションに新しいバージョン (1.0.0.1) がある場合。 次の手順を使用すれば、Web アプリケーションを配備し直すことができます。

1. _CreateADMRollout.Parameters.json_ を開きます。
1. `binaryArtifactRoot` を _binaries/1.0.0.1_ に更新します。
1. 「[テンプレートの配備](#deploy-the-rollout-template)」の指示に従って、ロールアウトを再配備します。
1. 「[配備の検証](#verify-the-deployment)」の指示に従って、配備を検証します。 Web ページでは、1.0.0.1 のバージョンであることを示すはずです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
1. **[名前でフィルタリング]** フィールドを使用して、このチュートリアルで作成したリソース グループを絞り込みます。

    * **&lt;projectName>rg**: Deployment Manager リソースが収納されます。
    * **&lt;projectName>ServiceWUSrg**: ServiceWUS によって定義されたリソースが収納されます。
    * **&lt;projectName>ServiceEUSrg**: ServiceEUS によって定義されたリソースが収納されます。
    * ユーザー定義マネージド ID 用のリソース グループです。
1. リソース グループ名を選択します。
1. トップ メニューから **[リソース グループの削除]** を選択します。
1. このチュートリアルで作成した他のリソース グループを削除するには、最後の 2 つの手順を繰り返します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Deployment Manager を使用する方法について学習しました。 Azure Deployment Manager に正常性の監視を統合する場合は、「[Tutorial:Use health check in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)」 (チュートリアル: Azure Deployment Manager で正常性チェックを使用する) を参照してください。
