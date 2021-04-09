---
title: チュートリアル - Azure Resource Manager Bicep ファイルを作成してデプロイする
description: Azure リソースをデプロイするための最初の Bicep ファイルを作成します。 このチュートリアルでは、Bicep ファイルの構文とストレージ アカウントのデプロイ方法について説明します。
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8979585d7ec0fa6eac1866375fe1e80214f2d2e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594276"
---
# <a name="tutorial-create-and-deploy-first-azure-resource-manager-bicep-file"></a>チュートリアル: 最初の Azure Resource Manager Bicep ファイルを作成およびデプロイする

このチュートリアルでは、[Bicep](./bicep-overview.md) について説明します。 スターター Bicep ファイルを作成して Azure にデプロイする方法を紹介します。 Bicep ファイルの構造のほか、Bicep ファイルを扱う際に必要なツールについても説明します。 このチュートリアルの所要時間は約 **12 分** ですが、実際の時間は、インストールする必要のあるツールの数によって変化します。

これは、シリーズの最初のチュートリアルです。 シリーズを進めながら、開始時の Bicep ファイルを段階的に変更して、Bicep ファイルの核となるすべての部分を探索していきます。 それらの要素は、はるかに複雑な Bicep ファイルの構成要素となります。 シリーズの最後には、独自の Bicep ファイルを作成したり、Bicep ファイルを使ってデプロイを自動化したりする自信が持てるようになればさいわいです。

Bicep ファイルを使用する利点と、Bicep ファイルを使用してデプロイを自動化すべき理由については、[Bicep](./bicep-overview.md) に関する記事をご覧ください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-tools"></a>ツールを入手する

まず、Bicep ファイルを作成してデプロイするために必要なツールがあることを確認していきましょう。 これらのツールをローカル コンピューターにインストールする

### <a name="editor"></a>エディター

Bicep ファイルを作成するには、優れたエディターが必要です。 Visual Studio Code と Bicep 拡張機能の組み合わせをお勧めします。 これらのツールをインストールする必要がある場合は、[Bicep 開発環境の構成](./bicep-install.md#development-environment)に関するセクションを参照してください。

### <a name="command-line-deployment"></a>コマンド ライン デプロイ

Azure CLI または Azure PowerShell を使用して、Bicep ファイルをデプロイできます。 Azure CLI には、2.20.0 以降のバージョンが必要です。Azure PowerShell には、5.6.0 以降のバージョンが必要です。 インストール手順については、以下を参照してください。

- [Azure PowerShell をインストールするには](/powershell/azure/install-az-ps)
- [Windows での Azure CLI のインストール](/cli/azure/install-azure-cli-windows)
- [Linux での Azure CLI のインストール](/cli/azure/install-azure-cli-linux)
- [macOS での Azure CLI のインストール](/cli/azure/install-azure-cli-macos)

Azure PowerShell または Azure CLI をインストールした後で、初回サインインを行います。 ヘルプ情報については、[PowerShell でのサインイン](/powershell/azure/install-az-ps#sign-in)または [Azure CLI でのサインイン](/cli/azure/get-started-with-azure-cli#sign-in)に関するセクションを参照してください。

Bicep について学習を始める準備が整いました。

## <a name="create-your-first-bicep-file"></a>最初の Bicep ファイルを作成する

1. Bicep 拡張機能がインストールされた Visual Studio Code を開きます。
1. **[ファイル]** メニューから **[新しいファイル]** を選択して新しいファイルを作成します。
1. **[ファイル]** メニューから **[名前を付けて保存]** を選択します。
1. ファイルに _azuredeploy_ という名前を付け、_bicep_ ファイル拡張子を選択します。 完全なファイル名は _azuredeploy.bicep_ になります。
1. ご利用のワークステーションにファイルを保存します。 後で Bicep ファイルをデプロイするときにパスを指定するため、覚えやすいパスを選択してください。
1. 次の内容をコピーしてファイルに貼り付けます。

    ```bicep
    resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
      name: '{provide-unique-name}'
      location: 'eastus'
      sku: {
        name: 'Standard_LRS'
      }
      kind: 'StorageV2'
      properties: {
        supportsHttpsTrafficOnly: true
      }
    }
    ```

    Visual Studio Code 環境は次のようになります。

    ![Visual Studio Code、最初の Bicep ファイル](./media/Bicep-tutorial-create-first-bicep/resource-manager-visual-studio-code-first-bicep.png)

    リソース宣言には、次の 4 つのコンポーネントがあります。

    - **resource**: キーワード。
    - **シンボリック名** (stg): シンボリック名は、bicep ファイルを通してリソースを参照するための識別子です。 これは、デプロイされるときに付けられるリソースの名前ではありません。 リソースの名前は、**name** プロパティによって定義されます。  この一覧の 4 番目のコンポーネントを参照してください。 このチュートリアル シリーズでは、チュートリアルをわかりやすくするために、ストレージ アカウント リソースのシンボリック名として **stg** を使用しています。 シンボリック名を使用してオブジェクトのプロパティの完全な一覧を取得する方法については、「[出力の追加](./bicep-tutorial-add-outputs.md)」を参照してください。
    - **リソースの種類** (Microsoft.Storage/storageAccounts@2019-06-01): リソース プロバイダー (Microsoft.Storage)、リソースの種類 (storageAccounts)、および apiVersion (2019-06-01) で構成されます。 リソース プロバイダーからは、それぞれ独自の API バージョンが公開されているため、これはその種類に固有の値となります。 [ARM テンプレート リファレンス](/azure/templates/)で、さまざまな Azure リソースの種類と apiVersion を 見つけることができます。
    - **プロパティ** (= {...} 内のすべてのもの): これらは、特定のリソースの種類に指定する特定のプロパティです。 これらは、ARM テンプレートで使用できるプロパティとまったく同じです。 すべてのリソースに、`name` プロパティがあります。 ほとんどのリソースには、リソースのデプロイ先リージョンを設定する `location` プロパティがあります。 その他のプロパティは、リソースの種類と API バージョンにより異なります。 API バージョンと利用可能なプロパティの関係を理解することが大切ですので、もう少し踏み込んでみましょう。

        このストレージ アカウントの場合、[storageAccounts 2019-06-01](/azure/templates/microsoft.storage/2019-06-01/storageaccounts) でその API バージョンを確認できます。 すべてのプロパティを Bicep ファイルに追加したわけではないことに注目してください。 プロパティの多くは省略可能です。 `Microsoft.Storage` リソース プロバイダーから新しい API バージョンがリリースされる可能性もありますが、デプロイするバージョンを変更する必要はありません。 そのバージョンを使い続けることができ、またデプロイの結果に一貫性を確保できます。

        以前の API バージョン ([storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts) など) を表示した場合、用意されているプロパティが少ないことがわかります。

        リソースの API バージョンを変更することにした場合は、そのバージョンのプロパティを評価したうえで、適宜 Bicep ファイルを調整してください。

1. `{provide-unique-name}` (中かっこ `{}` を含む) を一意のストレージ アカウント名に置き換えます。

    > [!IMPORTANT]
    > ストレージ アカウント名は Azure 内で一意である必要があります。 名前に使用できるのは、小文字と数字だけです。 24 文字以内にする必要があります。 名前付けパターンとして、プレフィックスに **store1** を使用し、自分のイニシャルと今日の日付を追加する形が考えられます。 たとえば、**store1abc09092019** のような名前を使用できます。

    ストレージ アカウントの一意の名前を考えるのは容易ではなく、大規模なデプロイの自動化ではうまくいきません。 このチュートリアル シリーズでは、後ほど、一意の名前の作成を容易にする Bicep 機能を使用します。

1. ファイルを保存します。

お疲れさまでした。最初の Bicep ファイルが完成しました。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure PowerShell または Azure CLI を使用して作業を開始するには、自分の Azure の資格情報を使用してサインインします。

以下のコード セクションでは、Azure PowerShell と Azure CLI のどちらかのタブを選択してください。 この記事の CLI の例は、Bash シェルを対象として記述されています。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

複数の Azure サブスクリプションがある場合は、使用するサブスクリプションを選択します。 `[SubscriptionID/SubscriptionName]` と角かっこ `[]` を実際のサブスクリプション情報に置き換えます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>リソース グループの作成

Bicep ファイルをデプロイするときは、リソースを格納するリソース グループを指定します。 デプロイ コマンドを実行する前に、Azure CLI または Azure PowerShell を使用してリソース グループを作成します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-bicep-file"></a>Bicep ファイルをデプロイする

Bicep は Azure Resource Manager テンプレート (ARM テンプレート) に対する透過的な抽象化です。 各 Bicep ファイルは、デプロイされる前に標準の ARM テンプレートにコンパイルされます。 Bicep ファイルは、デプロイする前に ARM テンプレートにコンパイルすることも、直接デプロイすることもできます。 Bicep ファイルをデプロイするには、Azure CLI または Azure PowerShell を使用します。 作成済みのリソース グループを使用します。 デプロイ履歴で識別しやすいよう、デプロイには名前を付けてください。 また、便宜上、Bicep ファイルへのパスを格納する変数も作成します。 この変数により、デプロイのたびにパスを再入力する必要がないため、デプロイ コマンドの実行が簡単になります。 `{provide-the-path-to-the-bicep-file}` (中かっこ `{}` を含む) を、 _.bicep_ ファイル拡張子名を含めて Bicep ファイルへのパスに置き換えます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

このデプロイ コマンドレットを実行するには、Azure PowerShell の[最新バージョン](/powershell/azure/install-az-ps)が必要です。

```azurepowershell
$bicepFile = "{provide-the-path-to-the-bicep-file}"
New-AzResourceGroupDeployment `
  -Name firstbicep `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。

```azurecli
bicepFile="{provide-the-path-to-the-bicep-file}"
az deployment group create \
  --name firstbicep \
  --resource-group myResourceGroup \
  --template-file $bicepFile
```

---

デプロイ コマンドから結果が返されます。 デプロイが成功したかどうかは、`ProvisioningState` を見て確認します。

> [!NOTE]
> デプロイに失敗した場合は、`verbose` スイッチを使用して、作成しているリソースに関する情報を取得します。 デバッグの詳細については、`debug` スイッチを使用してください。

## <a name="verify-deployment"></a>デプロイの確認

Azure portal からリソース グループを探すことでデプロイを確認できます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 左側のメニューから **[リソース グループ]** を選択します。

1. 直前の手順でデプロイしたリソース グループを選択します。 既定の名前は **myResourceGroup** です。 このリソース グループ内には、デプロイされたリソースは表示されないはずです。

1. 概要の右上を見ると、デプロイの状態が表示されます。 **[1 Succeeded]\(1 成功\)** を選択します。

   ![デプロイの状態を確認する](./media/bicep-tutorial-create-first-bicep/deployment-status.png)

1. リソース グループのデプロイの履歴が表示されます。 **[firstbicep]** を選択します。

   ![デプロイの選択](./media/bicep-tutorial-create-first-bicep/select-from-deployment-history.png)

1. デプロイの概要が表示されます。 ストレージ アカウントが 1 つデプロイされています。 左側では、デプロイ時に使用された入力、出力、テンプレートを確認できます。

   ![デプロイの概要の表示](./media/bicep-tutorial-create-first-bicep/view-deployment-summary.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに移動する場合は、リソース グループを削除する必要はありません。

ここで終了する場合は、リソース グループを削除してかまいません。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

Azure にデプロイする簡単な Bicep ファイルを作成しました。  後続のチュートリアルでは、パラメーター、変数、出力、およびモジュールを Bicep ファイルに追加する方法について説明します。 これらの機能は、はるかに複雑な Bicep ファイルの構成要素となります。

> [!div class="nextstepaction"]
> [パラメーターを追加する](bicep-tutorial-add-parameters.md)
