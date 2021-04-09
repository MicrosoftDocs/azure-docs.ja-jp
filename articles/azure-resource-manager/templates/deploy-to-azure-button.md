---
title: '[Azure にデプロイ] ボタン'
description: Azure Resource Manager テンプレートを GitHub リポジトリからデプロイするためのボタンを使用する。
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: e25d49571347bb5ed27dbd52bb60c68cbeb4360d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543782"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>デプロイ ボタンを使用して GitHub リポジトリからテンプレートをデプロイする

この記事では、 **[Azure に配置する]** ボタンを使用して、GitHub リポジトリからテンプレートをデプロイする方法について説明します。 このボタンは、GitHub リポジトリ内の _README.md_ ファイルに直接追加できます。 または、リポジトリを参照する Web ページにボタンを追加することもできます。

デプロイ スコープは、テンプレート スキーマによって決定されます。 詳細については、次を参照してください。

- [resource groups](deploy-to-resource-group.md)
- [subscriptions](deploy-to-subscription.md)
- [管理グループ](deploy-to-management-group.md)
- [tenants](deploy-to-tenant.md)

## <a name="use-common-image"></a>一般的なイメージを使用する

Web ページまたはリポジトリにボタンを追加するには、次の画像を使用します。

```markdown
![Deploy to Azure](https://aka.ms/deploytoazurebutton)
```

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

画像は次のように表示されます。

![[Azure にデプロイ] ボタン](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>テンプレートをデプロイするための URL を作成する

テンプレートの URL を作成するには、リポジトリ内のテンプレートの未加工の URL から開始します。 未加工の URL を表示するには **[Raw]\(未フォーマット\)** を選択します。

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="[Raw]\(未フォーマット\) の選択":::

URL の形式は、次のようになります。

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

次に、URL を URL エンコードされた値に変換します。 オンライン エンコーダーを使用するか、コマンドを実行できます。 次の PowerShell の例は、値を URL エンコードする方法を示しています。

```powershell
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
[uri]::EscapeDataString($url)
```

サンプル URL は、URL エンコードされると次のようになります。

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

各リンクは、同じベース URL で始まります。

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

URL エンコードされたテンプレート リンクをベース URL の末尾に追加します。

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

リンクの完全な URL が表示されます。

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

GitHub リポジトリの代わりに [Azure Repos と共に Git](/azure/devops/repos/git/) を使用する場合でも、 **[Deploy to Azure]\(Azure にデプロイ\)** ボタンを使用できます。 リポジトリがパブリックであることを確認します。 テンプレートを取得するには [Items 操作](/rest/api/azure/devops/git/items/get)を使用します。 要求は次の形式にする必要があります。

```http
https://dev.azure.com/{organization-name}/{project-name}/_apis/git/repositories/{repository-name}/items?scopePath={url-encoded-path}&api-version=6.0
```

この要求 URL をエンコードします。

## <a name="create-deploy-to-azure-button"></a>[Deploy to Azure] (Azure にデプロイ) ボタンを作成する

最後に、リンクと画像を一緒に配置します。

GitHub リポジトリまたは Web ページの _README.md_ ファイルに Markdown を使用してボタンを追加するには、次のように使用します。

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

HTML の場合は、次を使用します。

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

Git で Azure リポジトリを使用する場合、ボタンは次の形式になります。

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fdev.azure.com%2Forgname%2Fprojectname%2F_apis%2Fgit%2Frepositories%2Freponame%2Fitems%3FscopePath%3D%2freponame%2fazuredeploy.json%26api-version%3D6.0)
```

## <a name="deploy-the-template"></a>テンプレートのデプロイ

完全なソリューションをテストするには、次のボタンを選択します。

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

ポータルには、パラメーター値を簡単に指定できるペインが表示されます。 パラメーターには、テンプレートの既定値があらかじめ入力されています。

![デプロイのためのポータルを使用する](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>次のステップ

- テンプレートの詳細については、「[ARM テンプレートの構造と構文について](template-syntax.md)」を参照してください。
