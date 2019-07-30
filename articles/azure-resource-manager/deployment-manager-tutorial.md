---
title: Resource Manager テンプレートで Azure Deployment Manager を使用する | Microsoft Docs
description: Resource Manager テンプレートで Azure Deployment Manager を使用して、Azure リソースを配備します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a42ccb1c0e60f5bf1568ccea13392186577f2875
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205717"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>チュートリアル:Resource Manager テンプレートで Azure Deployment Manager を使用する (パブリック プレビュー)

[Azure Deployment Manager](./deployment-manager-overview.md) を使用して、アプリケーションを複数の地域に配備する方法を学習します。 高速化のアプローチを採用する場合は、[Azure Deployment Manager のクイック スタート](https://github.com/Azure-Samples/adm-quickstart)に従って、サブスクリプションに必要な構成を作成し、複数のリージョン全体に 1 つのアプリケーションをデプロイするように成果物をカスタマイズします。 このクイック スタートでは、このチュートリアルで実行するのと同じタスクを実行します。

Deployment Manager を使用するには、次の 2 つのテンプレートを作成する必要があります。

* **トポロジ テンプレート**: アプリケーションを構成する Azure リソースとその配備先を記述します。
* **ロールアウト テンプレート**: アプリケーションを配備する際に行う手順を記述します。

> [!IMPORTANT]
> サブスクリプションが Azure の新機能をテストするためのカナリア用としてマークされている場合は、Azure Deployment Manager を使用したカナリア リージョンへのデプロイのみが可能です。 

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
> * リソースのクリーンアップ

その他のリソース:

* [Azure Deployment Manager REST API リファレンス](https://docs.microsoft.com/rest/api/deploymentmanager/)
* [チュートリアル:Use health check in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)」 (チュートリアル: Azure Deployment Manager で正常性チェックを使用する) を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* [Azure Resource Manager テンプレート](./resource-group-overview.md)を開発した経験がある。
* Azure PowerShell。 詳細については、[Azure PowerShell の概要](https://docs.microsoft.com/powershell/azure/get-started-azureps)に関するページを参照してください。
* Deployment Manager コマンドレット。 これらのプレリリース版のコマンドレットをインストールするには、最新バージョンの PowerShellGet が必要です。 最新バージョンを入手するには、「[PowerShellGet のインストール](/powershell/gallery/installing-psget)」を参照してください。 PowerShellGet をインストールし終えたら、PowerShell ウィンドウを閉じます。 管理者特権の PowerShell ウィンドウを新たに開き、次のコマンドを使用します。

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

* [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)。 Azure Storage Explorer は必須ではありませんが、作業しやすくなります。

## <a name="understand-the-scenario"></a>シナリオの理解

サービス トポロジ テンプレートでは、アプリケーションを構成する Azure リソースとその配備先を記述します。 サービス トポロジの定義には次の階層があります。

* サービス トポロジ
  * サービス
    * サービス ユニット

次の図は、このチュートリアルで使用されるサービス トポロジを示したものです。

![Azure Deployment Manager チュートリアルのシナリオ図](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

米国西部と米国東部の場所に割り当てられた 2 つのサービスがあります。  各サービスには、Web アプリケーションのフロントエンドとバックエンド用のストレージ アカウントの 2 つのサービス ユニットがあります。 サービス ユニットの定義には、Web アプリケーションとストレージ アカウントを作成するためのテンプレートとパラメータ ファイルへのリンクがあります。

## <a name="download-the-tutorial-files"></a>チュートリアル ファイルのダウンロード

1. このチュートリアルで使用される[テンプレートと成果物](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip)をダウンロードします。
2. 地域のコンピューターにファイルを解凍します。

ルート フォルダーには次の 2 つのフォルダーがあります。

* **ADMTemplates**: 次を含む Deployment Manager テンプレートが収納されます。
  * CreateADMServiceTopology.json
  * CreateADMServiceTopology.Parameters.json
  * CreateADMRollout.json
  * CreateADMRollout.Parameters.json
* **ArtifactStore**: テンプレート成果物とバイナリ成果物の両方が収納されます。 「[成果物の準備](#prepare-the-artifacts)」を参照してください。

2 種類のテンプレートがあることに注意してください。  1 つは、サービス トポロジとロールアウトを配備するために使用される Deployment Manager テンプレートです。もう 1 つは、Web サービスとストレージ アカウントを作成するために、サービス ユニットから呼び出されます。

## <a name="prepare-the-artifacts"></a>成果物の準備

ダウンロードした ArtifactStore フォルダーには、次の 2 つのフォルダーがあります。

![Azure Deployment Manager チュートリアルの成果物ソース図](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* **テンプレート** フォルダー: テンプレート成果物が収納されます。 **1.0.0.0** と **1.0.0.1** は、バイナリ成果物の 2 つのバージョンを表します。 各バージョン内には、各サービス (サービス米国東部とサービス米国西部) 用のフォルダーがあります。 各サービスには、ストレージ アカウントを作成するためのテンプレート ファイルとパラメータ ファイルのペアと、Web アプリケーションを作成するための別のペアがあります。 Web アプリケーション テンプレートは、Web アプリケーション ファイルを収納する圧縮パッケージを呼び出します。 圧縮ファイルは、バイナリ フォルダーに格納されるバイナリ成果物です。
* **バイナリ** フォルダー: バイナリ成果物が収納されます。 **1.0.0.0** と **1.0.0.1** は、バイナリ成果物の 2 つのバージョンを表します。 各バージョン内には、米国西部の場所で Web アプリケーションを作成するための 1 つの zip ファイルと、米国東部の場所で Web アプリケーションを作成するための別の zip ファイルがあります。

2 つのバージョン (1.0.0.0 と 1.0.0.1) は、[リビジョン配備](#deploy-the-revision)用です。 テンプレート成果物とバイナリ成果物の両方に 2 つのバージョンがある場合でも、2 つのバージョン間で違いがあるのはバイナリ成果物のみです。 実際に、バイナリ成果物はテンプレート成果物よりも頻繁に更新されます。

1. テキスト エディターで、 **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json** を開きます。 これはストレージ アカウントを作成するための基本的なテンプレートです。
2. **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json** を開きます。

    ![Azure Deployment Manager チュートリアルでは、Web アプリケーション テンプレートを作成します。](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    そのテンプレートは、Web アプリケーションのファイルを収納する配備パッケージを呼び出します。 このチュートリアルでは、圧縮パッケージには index.html ファイルのみが収納されています。
3. **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json** を開きます。

    ![Azure Deployment Manager チュートリアルでは、Web アプリケーション テンプレート パラメーターの containerRoot を作成します。](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    deployPackageUri の値は配備パッケージへのパスです。 パラメーターには **$containerRoot** 変数があります。 $containerRoot の値は、成果物ソース SAS の場所、成果物ルート、deployPackageUri を連結することにより、[ロールアウト テンプレート](#create-the-rollout-template)で提供されます。
4. **\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html** を開きます。

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

    html では場所とバージョン情報が示されます。 1\.0.0.1 フォルダー内のバイナリ ファイルは、"Version 1.0.0.1" であることを示します。 サービスを配備し終えたら、これらのページを参照できます。
5. その他の成果物ファイルをチェックアウトします。 これは、シナリオをさらに良く理解するのに役立ちます。

テンプレート成果物はサービス トポロジ テンプレートで使用され、バイナリ成果物はロールアウト テンプレートで使用されます。 トポロジー テンプレートとロールアウト テンプレートは両方とも、成果物ソースの Azure リソースを定義します。これは、配備で使用されるテンプレート成果物とバイナリー成果物に、Resource Manager をポイントするために使用されるリソースです。 チュートリアルを簡略化するために、1 つのストレージ アカウントを使用して、テンプレート成果物とバイナリ成果物を両方とも格納します。 どちらの成果物ソースも、同じストレージ アカウントをポイントします。

1. Azure ストレージ アカウントを作成します。 手順については、「[クイック スタート:Azure portal を使用して BLOB をアップロード、ダウンロード、および一覧表示する](../storage/blobs/storage-quickstart-blobs-portal.md)」を参照してください。
2. ストレージ アカウントに BLOB コンテナーを作成します。
3. 2 つのフォルダー (バイナリとテンプレート) と 2 つのフォルダーの内容を BLOB コンテナーにコピーします。 [Microsoft Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) では、ドラッグ アンド ドロップ機能をサポートしています。
4. 次の手順を使用して、コンテナーの SAS の場所を取得します。

    1. Azure Storage Explorer から BLOB コンテナーに移動します。
    2. 左側のウィンドウで BLOB コンテナーを右クリックしたら、 **[Shared Access Signature の取得]** を選択します。
    3. **[開始時刻]** と **[有効期限]** を構成します。
    4. **作成**を選択します。
    5. URL のコピーを作成します。 この URL は、[トポロジ パラメーター ファイル](#topology-parameters-file)と[ロールアウト パラメーター ファイル](#rollout-parameters-file)の 2 つのパラメーター ファイルにフィールドを設定するために必要です。

## <a name="create-the-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID の作成

チュートリアルの後半では、ロールアウトを配備します。 (Web アプリケーションやストレージ アカウントの配備などの) 配備操作を実行するには、ユーザー割り当てマネージド ID が必要です。 この ID には、サービスを配備している Azure サブスクリプションへのアクセス権が与えられている必要があり、成果物の配備を完結するには十分な権限が必要です。

ユーザー割り当てマネージド ID を作成し、サブスクリプション用にアクセス制御を構成する必要があります。

> [!IMPORTANT]
> ユーザー割り当てマネージド ID は、[ロールアウト](#create-the-rollout-template)と同じ場所になければなりません。 現在、ロールアウトを含む Deployment Manager のリソースは、米国中部または米国東部 2 のいずれかでのみ作成できます。 ただし、これが該当するのは Deployment Manager のリソース (サービス トポロジ、サービス、サービス ユニット、ロールアウト、ステップなど) だけです。 ターゲット リソースは、サポートされている任意の Azure リージョンにデプロイできます。 たとえば、このチュートリアルでは Deployment Manager のリソースを米国中部にデプロイしていますが、サービスのデプロイ先は米国東部と米国西部です。 この制限は将来解除される予定です。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. [ユーザー割り当てマネージド ID](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) を作成します。
3. ポータルから、左側のメニューで **[サブスクリプション]** を選択したら、お使いのサブスクリプションを選択します。
4. **[アクセス制御 (IAM)]** を選択したら、 **[ロール割り当ての追加]** を選択します。
5. 次の値を入力または選択します。

    ![Azure Deployment Manager チュートリアルでのユーザー割り当てマネージド ID のアクセス制御](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **ロール**: 成果物の配備 (Web アプリケーションとストレージ アカウント) を完結するための十分な権限を与えます。 このチュートリアルでは、 **[共同作成者]** を選択します。 実際には、アクセス許可を最小限に抑えます。
    * **割り当て済みのアクセス先**: **[ユーザー割り当てマネージド ID]** を選択します。
    * チュートリアルの前半で作成したユーザー割り当てマネージド ID を選択します。
6. **[保存]** を選択します。

## <a name="create-the-service-topology-template"></a>サービス トポロジ テンプレートの作成

**\ADMTemplates\CreateADMServiceTopology.json** を開きます。

### <a name="the-parameters"></a>パラメーター

テンプレートには、次のパラメーターが収納されています。

![Azure Deployment Manager チュートリアルのトポロジ テンプレート パラメーター](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-parameters.png)

* **namePrefix**:このプレフィックスは、Deployment Manager リソース用の名前を作成するために使用されます。 たとえば、サービス トポロジの名前は、"jdoe" プレフィックスを使用して、**jdoe**ServiceTopology とします。  リソース名は、このテンプレートの変数セクションで定義されます。
* **azureResourcelocation**:チュートリアルを簡略化するため、特に指定しない限り、すべてのリソースはこの場所を共有します。 現在、Deployment Manager のリソースは、**米国中部**または**米国東部 2** のいずれかでのみ作成できます。
* **artifactSourceSASLocation**:配備用にサービス ユニットのテンプレートとパラメーターのファイルが格納されている BLOB コンテナーへのSAS URI です。  「[成果物の準備](#prepare-the-artifacts)」を参照してください。
* **templateArtifactRoot**:テンプレートとパラメーターが格納されている BLOB コンテナーからのオフセット パスです。 既定値は **templates/1.0.0.0** です。 「[成果物の準備](#prepare-the-artifacts)」で説明されているフォルダー構造を変更しない限り、この値を変更しないでください。 このチュートリアルでは、相対パスを使用します。  フルパスは、**artifactSourceSASLocation**、**templateArtifactRoot**、**templateArtifactSourceRelativePath** (または **parametersArtifactSourceRelativePath**) を連結することで構成されます。
* **targetSubscriptionID**:Deployment Manager のリソースが配備され、請求される予定のサブスクリプションID です。 このチュートリアルでは、サブスクリプション ID を使用します。

### <a name="the-variables"></a>変数

変数セクションでは、リソースの名前、2 つのサービス(**サービス WUS** と **サービス EUS**) に対する Azure の場所、および次の成果物のパスを定義します。

![Azure Deployment Manager チュートリアルのトポロジ テンプレート変数](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

成果物パスを、ストレージ アカウントにアップロードしたフォルダー構造と比較します。 成果物パスは相対パスであることに注意してください。 フルパスは、**artifactSourceSASLocation**、**templateArtifactRoot**、**templateArtifactSourceRelativePath** (または **parametersArtifactSourceRelativePath**) を連結することで構成されます。

### <a name="the-resources"></a>リソース

ルート レベルでは、*成果物ソース*と*サービス トポロジー*の 2 つのリソースが定義されています。

成果物ソースの定義は次のとおりです。

![Azure Deployment Manager チュートリアルのトポロジ テンプレート リソース成果物ソース](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

次のスクリーンショットでは、サービス トポロジ、サービス、サービス ユニットの定義の一部のみを示しています。

![Azure Deployment Manager チュートリアルのトポロジ テンプレート リソース サービス トポロジ](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* **artifactSourceId** は、成果物ソース リソースをサービス トポロジ リソースに関連付けるために使用されます。
* **dependsOn**:すべてのサービス トポロジ リソースは、成果物ソース リソースに依存します。
* **artifacts** は、テンプレート成果物をポイントします。  ここでは相対パスが使用されます。 フルパスは、artifactSourceSASLocation (成果物ソースで定義される)、artifactRoot (成果物ソースで定義される)、templateArtifactSourceRelativePath (または parametersArtifactSourceRelativePath) を連結することで構成されます。

### <a name="topology-parameters-file"></a>トポロジ パラメーター ファイル

トポロジ テンプレートで使用されるパラメーター ファイルを作成します。

1. Visual Studio Code またはいずれかのテキスト エディターで、 **\ADMTemplates\CreateADMServiceTopology.Parameters** を開きます。
2. 次のパラメーター値を記入します。

    * **namePrefix**:4-5 文字の文字列を入力します。 このプレフィックスは、固有の azure リソース名を作成するために使用されます。
    * **azureResourceLocation**:Azure の場所が不明な場合、このチュートリアルでは **centralus** を使用してください。
    * **artifactSourceSASLocation**:配備用にサービス ユニットのテンプレートとパラメーターのファイルが格納されているルート ディレクトリ (BLOB コンテナー) への SAS URI を入力します。  「[成果物の準備](#prepare-the-artifacts)」を参照してください。
    * **templateArtifactRoot**:成果物のフォルダー構造を変更しない限り、このチュートリアルでは **templates/1.0.0.0** を使用してください。
    * **targetScriptionID**:Azure サブスクリプション ID を入力します。

> [!IMPORTANT]
> トポロジ テンプレートとロールアウト テンプレートでは、いくつかの共通パラメーターを共有します。 これらのパラメーターは、同じ値でなければなりません。 これらのパラメーターとは、**namePrefix**、**azureResourceLocation**、**artifactSourceSASLocation** のことです (このチュートリアルでは、どちらの成果物ソースも同じストレージ アカウントを共有します)。

## <a name="create-the-rollout-template"></a>ロールアウト テンプレートの作成

**\ADMTemplates\CreateADMRollout.json** を開きます。

### <a name="the-parameters"></a>パラメーター

テンプレートには、次のパラメーターが収納されています。

![Azure Deployment Manager チュートリアルのロールアウト テンプレート パラメーター](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* **namePrefix**:このプレフィックスは、Deployment Manager リソース用の名前を作成するために使用されます。 たとえば、ロールアウト名は、"jdoe" プレフィックスを使用して、**jdoe**Rollout とします。  その名前は、テンプレートの変数セクションで定義されます。
* **azureResourcelocation**:チュートリアルを簡略化するため、特に指定しない限り、すべての Deployment Manager リソースはこの場所を共有します。 現在、Deployment Manager のリソースは、**米国中部**または**米国東部 2** のいずれかでのみ作成できます。
* **artifactSourceSASLocation**:配備用にサービス ユニットのテンプレートとパラメーターのファイルが格納されているルート ディレクトリ (BLOB コンテナー) への SAS URI です。  「[成果物の準備](#prepare-the-artifacts)」を参照してください。
* **binaryArtifactRoot**:既定値は **binaries/1.0.0.0** です。 「[成果物の準備](#prepare-the-artifacts)」で説明されているフォルダー構造を変更しない限り、この値を変更しないでください。 このチュートリアルでは、相対パスを使用します。  フルパスは、CreateWebApplicationParameters.json で指定されている **artifactSourceSASLocation**、**binaryArtifactRoot**、**deployPackageUri** を連結することで構成されます。  「[成果物の準備](#prepare-the-artifacts)」を参照してください。
* **managedIdentityID**:配備操作を実行するユーザー割り当てマネージド ID です。 「[ユーザー割り当てマネージド ID の作成](#create-the-user-assigned-managed-identity)」を参照してください。

### <a name="the-variables"></a>変数

変数セクションでは、リソースの名前を定義します。 サービス トポロジ名、サービス名、サービス ユニット名が、[トポロジ テンプレート](#create-the-service-topology-template)で定義されている名前と一致していることを確認してください。

![Azure Deployment Manager チュートリアルのロールアウト テンプレート変数](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>リソース

ルート レベルでは、成果物ソース、手順、ロールアウトの 3 つのリソースが定義されています。

成果物ソースの定義は、トポロジ テンプレートで定義されているものと同じです。  詳細については、「[サービス トポロジ テンプレートの作成](#create-the-service-topology-template)」を参照してください。

次のスクリーンショットでは、待機手順の定義を示しています。

![Azure Deployment Manager チュートリアルのロールアウト テンプレート リソース待機手順](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

期間には [ISO 8601 標準](https://en.wikipedia.org/wiki/ISO_8601#Durations)を使用しています。 **PT1M** (大文字必須) は 1 分間の待機の例です。

次のスクリーンショットでは、ロールアウトの定義の一部のみを示しています。

![Azure Deployment Manager チュートリアルのロールアウト テンプレート リソース ロールアウト](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* **dependsOn**:ロールアウト リソースは、成果物ソース リソースに、そして定義されているいずれかの手順に依存します。
* **artifactSourceId**: 成果物ソース リソースをロールアウト リソースに関連付けるために使用されます。
* **targetServiceTopologyId**: サービス トポロジ リソースをロールアウト リソースに関連付けるために使用されます。
* **deploymentTargetId**:サービス トポロジ リソースのサービス ユニット リソース ID です。
* **preDeploymentSteps** と **postDeploymentSteps**: ロールアウト手順が記載されます。 テンプレートでは、待機手順が呼び出されます。
* **dependsOnStepGroups**: 手順グループ間での依存関係を構成します。

### <a name="rollout-parameters-file"></a>ロールアウト パラメーター ファイル

ロールアウト テンプレートで使用されるパラメーター ファイルを作成します。

1. Visual Studio Code またはいずれかのテキスト エディターで、 **\ADMTemplates\CreateADMRollout.Parameters** を開きます。
2. 次のパラメーター値を記入します。

    * **namePrefix**:4-5 文字の文字列を入力します。 このプレフィックスは、固有の azure リソース名を作成するために使用されます。
    * **azureResourceLocation**:現在、Deployment Manager のリソースは、**米国中部**または**米国東部 2** のいずれかでのみ作成できます。
    * **artifactSourceSASLocation**:配備用にサービス ユニットのテンプレートとパラメーターのファイルが格納されているルート ディレクトリ (BLOB コンテナー) への SAS URI を入力します。  「[成果物の準備](#prepare-the-artifacts)」を参照してください。
    * **binaryArtifactRoot**:成果物のフォルダー構造を変更しない限り、このチュートリアルでは **binaries/1.0.0.0** を使用してください。
    * **managedIdentityID**:ユーザー割り当てマネージド ID を入力します。 「[ユーザー割り当てマネージド ID の作成](#create-the-user-assigned-managed-identity)」を参照してください。 の構文は次のとおりです。

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> トポロジ テンプレートとロールアウト テンプレートでは、いくつかの共通パラメーターを共有します。 これらのパラメーターは、同じ値でなければなりません。 これらのパラメーターとは、**namePrefix**、**azureResourceLocation**、**artifactSourceSASLocation** のことです (このチュートリアルでは、どちらの成果物ソースも同じストレージ アカウントを共有します)。

## <a name="deploy-the-templates"></a>テンプレートの配備

Azure PowerShell を使用すればテンプレートを配備できます。

1. サービス トポロジを配備するには、スクリプトを実行します。

    ```azurepowershell
    $resourceGroupName = "<Enter a Resource Group Name>"
    $location = "Central US"
    $filePath = "<Enter the File Path to the Downloaded Tutorial Files>"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location "$location"

    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    > [!NOTE]
    > `New-AzResourceGroupDeployment` は非同期呼び出しです。 成功メッセージは、デプロイが正常に開始されたことだけを意味します。 デプロイを確認するには、この手順の手順 2. と手順 4. を参照してください。

2. サービス トポロジと下線付きのリソースが、Azure portal を使用することで正常に作成されていることを確認します。

    ![Azure Deployment Manager チュートリアルの配備済みサービス トポロジ リソース](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    リソースを参照するには、 **[非表示型の表示]** を選択する必要があります。

3. <a id="deploy-the-rollout-template"></a>次のロールアウト テンプレートをデプロイします。

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. 次の PowerShell スクリプトを使用して、ロールアウトの進行状況を確認します。

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "<Enter the Rollout Name>" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    このコマンドレットを実行する前に、Deployment Manager PowerShell コマンドレットをインストールする必要があります。 「前提条件」を参照してください。 -Verbose スイッチを使用すると、出力全体を表示することができます。

    次のサンプルでは、実行状況を示しています。

    ```
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

    ロールアウトが正常に配備されたら、サービスごとに 1 つずつの、2 つのリソース グループがさらに作成されていることがわかります。

## <a name="verify-the-deployment"></a>デプロイを検証する

1. [Azure Portal](https://portal.azure.com)を開きます。
2. ロールアウトの配備によって作成された新しいリソース グループの下で、新たに作成された Web アプリケーションを参照します。
3. Web ブラウザーで、その Web アプリケーションを開きます。 index.html ファイルの場所とバージョンを確認します。

## <a name="deploy-the-revision"></a>リビジョンの配備

Web アプリケーションに新しいバージョン (1.0.0.1) がある場合。 次の手順を使用すれば、Web アプリケーションを配備し直すことができます。

1. CreateADMRollout.Parameters.json を開きます。
2. **binaryArtifactRoot** を **binaries/1.0.0.1** に更新します。
3. 「[テンプレートの配備](#deploy-the-rollout-template)」の指示に従って、ロールアウトを再配備します。
4. 「[配備の検証](#verify-the-deployment)」の指示に従って、配備を検証します。 Web ページでは、1.0.0.1 のバージョンであることを示すはずです。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルタリング]** フィールドを使用して、このチュートリアルで作成したリソース グループを絞り込みます。 次の 3-4 になります。

    * **&lt;namePrefix>rg**: Deployment Manager リソースが収納されます。
    * **&lt;namePrefix>ServiceWUSrg**: ServiceWUS によって定義されたリソースが収納されます。
    * **&lt;namePrefix>ServiceEUSrg**: ServiceEUS によって定義されたリソースが収納されます。
    * ユーザー定義マネージド ID 用のリソース グループです。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。
5. このチュートリアルで作成した他のリソース グループを削除するには、最後の 2 つの手順を繰り返します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Deployment Manager を使用する方法について学習しました。 Azure Deployment Manager に正常性の監視を統合する場合は、「[Tutorial:Use health check in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)」 (チュートリアル: Azure Deployment Manager で正常性チェックを使用する) を参照してください。
