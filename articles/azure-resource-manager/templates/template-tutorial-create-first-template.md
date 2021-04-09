---
title: チュートリアル - テンプレートの作成とデプロイ
description: 初めての Azure Resource Manager テンプレート (ARM テンプレート) を作成します。 このチュートリアルでは、テンプレート ファイルの構文とストレージ アカウントのデプロイ方法について説明します。
author: mumian
ms.date: 12/17/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 001a20c38a82d053ae22a2614a32f0be9d8aabce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656990"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>チュートリアル:初めての ARM テンプレートを作成してデプロイする

このチュートリアルでは、Azure Resource Manager テンプレート (ARM テンプレート) について取り上げます。 スターター テンプレートを作成して Azure にデプロイする方法を紹介します。 テンプレートの構造のほか、テンプレートを扱う際に必要なツールについても説明します。 このチュートリアルの所要時間は約 **12 分** ですが、実際の時間は、インストールする必要のあるツールの数によって変化します。

これは、シリーズの最初のチュートリアルです。 シリーズを進めながら、ARM テンプレートの核となる部分がすべてわかるまで、開始時のテンプレートを段階的に変更していきます。 それらの要素は、はるかに複雑なテンプレートの構成要素となります。 シリーズの最後には、独自のテンプレートを作成したり、テンプレートを使ってデプロイを自動化したりする自信が持てるようになればさいわいです。

テンプレートを使用する利点と、テンプレートを使用してデプロイを自動化すべき理由については、[ARM テンプレートの概要](overview.md)に関する記事をご覧ください。 Microsoft Learn のガイド付きモジュール セットを使用して ARM テンプレートを学習するには、「[ARM テンプレートを使用して Azure にリソースをデプロイして管理する](/learn/paths/deploy-manage-resource-manager-templates/)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="get-tools"></a>ツールを入手する

まず、テンプレートを作成してデプロイするために必要なツールがあることを確認していきましょう。 これらのツールをローカル コンピューターにインストールする

### <a name="editor"></a>エディター

テンプレートは JSON ファイルです。 テンプレートを作成するには、適切な JSON エディターが必要です。 Visual Studio Code と Resource Manager Tools 拡張機能をお勧めします。 これらのツールをインストールする必要がある場合は、「[クイックスタート: Visual Studio Code を使用して ARM テンプレートを作成する](quickstart-create-templates-use-visual-studio-code.md)」を参照してください。

### <a name="command-line-deployment"></a>コマンド ライン デプロイ

テンプレートをデプロイするには、Azure PowerShell または Azure CLI も必要です。 Azure CLI を使用する場合は、最新バージョンが必要です。 インストール手順については、以下を参照してください。

- [Azure PowerShell をインストールするには](/powershell/azure/install-az-ps)
- [Windows での Azure CLI のインストール](/cli/azure/install-azure-cli-windows)
- [Linux での Azure CLI のインストール](/cli/azure/install-azure-cli-linux)
- [macOS での Azure CLI のインストール](/cli/azure/install-azure-cli-macos)

Azure PowerShell または Azure CLI をインストールした後で、初回サインインを行います。 ヘルプ情報については、[PowerShell でのサインイン](/powershell/azure/install-az-ps#sign-in)または [Azure CLI でのサインイン](/cli/azure/get-started-with-azure-cli#sign-in)に関するセクションを参照してください。

> [!IMPORTANT]
> Azure CLI を使用している場合、バージョン 2.6 以降であることを確認してください。 以前のバージョンを使用している場合、このチュートリアルで示されるコマンドが機能しません。 インストールされているバージョンを確認するには、`az --version` を使用します。

テンプレートについて学習を始める準備が整いました。

## <a name="create-your-first-template"></a>最初のテンプレートを作成する

1. Resource Manager Tools 拡張機能がインストールされた Visual Studio Code を開きます。
1. **[ファイル]** メニューから **[新しいファイル]** を選択して新しいファイルを作成します。
1. **[ファイル]** メニューから **[名前を付けて保存]** を選択します。
1. ファイルに _azuredeploy_ という名前を付け、_json_ ファイル拡張子を選択します。 完全なファイル名は _azuredeploy.json_ になります。
1. ご利用のワークステーションにファイルを保存します。 後でテンプレートをデプロイするときにパスを指定するため、覚えやすいパスを選択してください。
1. 次の JSON をコピーして、ファイルに貼り付けます。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Visual Studio Code 環境は次のようになります。

    ![ARM テンプレート、Visual Studio Code、初めてのテンプレート](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    このテンプレートではリソースをデプロイしません。 間違いが生じる可能性をできるだけ抑えつつ、テンプレートをデプロイする手順に慣れることができるよう、最初は空のテンプレートを扱います。

    JSON ファイルには、次の要素が含まれています。

    - `$schema`: JSON スキーマ ファイルの場所を指定します。 スキーマ ファイルには、テンプレート内で使用できるプロパティが記述されます。 たとえば、このスキーマでは、テンプレートの有効なプロパティの 1 つとして `resources` が定義されています。 スキーマの日付が 2019-04-01 になっていますが気にしないでください。 このスキーマ バージョンは最新であり、最新の機能がすべて含まれています。 スキーマの導入以来、破壊的変更が生じていないため、スキーマの日付は変更されていません。
    - `contentVersion`: テンプレートのバージョンを指定します (1.0.0.0 など)。 この要素には任意の値を指定できます。 この値を使用し、テンプレートの大きな変更を記述します。 テンプレートを使用してリソースをデプロイする場合は、この値を使用して、適切なテンプレートが使用されていることを確認できます。
    - `resources`: デプロイまたは更新するリソースが格納されます。 現在は空ですが、後からリソースを追加していくことになります。

1. ファイルを保存します。

お疲れさまでした。初めてのテンプレートが完成しました。

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

テンプレートをデプロイするときは、リソースを格納するリソース グループを指定します。 デプロイ コマンドを実行する前に、Azure CLI または Azure PowerShell を使用してリソース グループを作成します。

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

## <a name="deploy-template"></a>テンプレートのデプロイ

テンプレートをデプロイするには、Azure CLI または Azure PowerShell を使用します。 作成済みのリソース グループを使用します。 デプロイ履歴で識別しやすいよう、デプロイには名前を付けてください。 また、便宜上、テンプレート ファイルのパスを格納する変数も作成します。 この変数を使用すれば、デプロイするたびにパスを再入力しなくても済むため、デプロイ コマンドが実行しやすくなります。 `{provide-the-path-to-the-template-file}` と中かっこ `{}` をテンプレート ファイルのパスに置き換えます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

デプロイ コマンドから結果が返されます。 デプロイが成功したかどうかは、`ProvisioningState` を見て確認します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

![PowerShell によるデプロイのプロビジョニングの状態](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

![Azure CLI によるデプロイのプロビジョニングの状態](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

> [!NOTE]
> デプロイに失敗した場合は、`verbose` スイッチを使用して、作成しているリソースに関する情報を取得します。 デバッグの詳細については、`debug` スイッチを使用してください。

## <a name="verify-deployment"></a>デプロイの確認

Azure portal からリソース グループを探すことでデプロイを確認できます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 左側のメニューから **[リソース グループ]** を選択します。

1. 直前の手順でデプロイしたリソース グループを選択します。 既定の名前は **myResourceGroup** です。 このリソース グループ内には、デプロイされたリソースは表示されないはずです。

1. 概要の右上を見ると、デプロイの状態が表示されます。 **[1 Succeeded]\(1 成功\)** を選択します。

   ![デプロイの状態を確認する](./media/template-tutorial-create-first-template/deployment-status.png)

1. リソース グループのデプロイの履歴が表示されます。 **[blanktemplate]** を選択します。

   ![デプロイの選択](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. デプロイの概要が表示されます。 この場合は、リソースがデプロイされていないので、表示される情報は多くありません。 後でこのシリーズの中で、デプロイ履歴の概要を確認する際に役立ちます。 左側では、デプロイ時に使用された入力、出力、テンプレートを確認できます。

   ![デプロイの概要の表示](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに移動する場合は、リソース グループを削除する必要はありません。

ここで終了する場合は、リソース グループを削除してかまいません。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

Azure にデプロイする簡単なテンプレートを作成しました。 次のチュートリアルでは、テンプレートにストレージ アカウントを追加してリソース グループにデプロイします。

> [!div class="nextstepaction"]
> [リソースを追加する](template-tutorial-add-resource.md)
