---
title: デプロイ資格情報の構成
description: Azure App Service のデプロイ資格情報の種類と、それらを構成および使用する方法について説明します。
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: c7d3c7c8b5da40a4e9ccd9085af5a850b9ebc3dd
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052349"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Azure App Service のデプロイ資格情報の構成
ローカル コンピューターからのアプリのデプロイをセキュリティで保護するために、[Azure App Service](./overview.md) では、[ローカル Git デプロイ](deploy-local-git.md)と [FTP/S デプロイ](deploy-ftp.md)に対して 2 種類の資格情報がサポートされています。 これらの資格情報は Azure サブスクリプションの資格情報とは異なります。

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

> [!NOTE]
> Azure portal の **[開発センター (クラシック)]** ページ (以前のデプロイ エクスペリエンス) は、2021 年 3 月に非推奨となる予定です。 この変更はアプリの既存のデプロイ設定には影響せず、 **[デプロイ センター]** ページで引き続きアプリのデプロイを管理できます。

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>ユーザー スコープの資格情報を構成する

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) コマンドを実行します。 \<username> と \<password> を、デプロイ ユーザーのユーザー名とパスワードで置き換えます。 

- ユーザー名は、Azure 内で一意である必要があり、ローカル Git プッシュの場合は "\@" シンボルを含めることはできません。 
- パスワードは長さが 8 文字以上で、文字、数字、記号のうち 2 つを含む必要があります。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 出力には、パスワードが `null` として表示されます。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Azure PowerShell を使用してユーザー スコープの資格情報を構成することはできません。 別の方法を使用するか、[アプリケーション スコープの資格情報の使用](#appscope)を検討してください。 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

ユーザー スコープの資格情報は、任意のアプリの[リソース ページ](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)で構成できます。 どのアプリで構成した場合でも、これらの資格情報は、Azure アカウント内のすべてのサブスクリプションのすべてのアプリに適用されます。 

[Azure portal](https://portal.azure.com) で、[デプロイ資格情報] ページにアクセスするには、少なくとも 1 つのアプリが必要です。 ユーザー スコープの資格情報を構成するには、次の手順を実行します。

1. アプリの左側のメニューで、 **[管理者用センター]**  >  **[FTPS 資格情報]** または **[ローカル Git/FTPS 資格情報]** を選択します。

    ![Azure App Services のデプロイ センターから FTP ダッシュボードを選択する方法を示します。](./media/app-service-deployment-credentials/access-no-git.png)

2. **[ユーザー スコープ]** まで下にスクロールし、 **[ユーザー名]** と **[パスワード]** を構成してから、 **[保存]** を選択します。

デプロイ資格情報を設定すると、*Git* デプロイのユーザー名がアプリの **[概要]** ページに表示されます。

![アプリの [概要] ページで Git デプロイ ユーザー名を見つける方法を示します。](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Git デプロイが構成されている場合、ページに **Git/デプロイ ユーザー名** が表示されます。構成されていない場合、**FTP/デプロイ ユーザー名** が表示されます。

> [!NOTE]
> Azure では、ユーザー スコープのデプロイ パスワードは表示されません。 パスワードを忘れた場合は、このセクションの手順に従って、資格情報をリセットできます。
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>ユーザー スコープの資格情報と FTP/FTPS を使用する

ユーザー スコープの資格情報を使用した FTP または FTPS エンドポイントの認証には、`<app-name>\<user-name>` の形式のユーザー名が必要です

ユーザー スコープの資格情報は、特定のリソースではなくユーザーにリンクされているため、適切なアプリのエンドポイントに対してサインイン アクションを実行するには、ユーザー名はこの形式でなければなりません。

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>アプリケーション スコープの資格情報を取得する

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[az webapp deployment list-publishing-profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) コマンドを使用して、アプリケーション スコープの資格情報を取得します。 次に例を示します。

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

[ローカル Git デプロイ](deploy-local-git.md)の場合は、[az webapp deployment list-publishing-credentials](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) コマンドを使用して、アプリケーション スコープの資格情報が既に埋め込まれているアプリの Git リモート URI を取得することもできます。 次に例を示します。

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) コマンドを使用して、アプリケーション スコープの資格情報を取得します。 次に例を示します。

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. アプリの左側のメニューで、 **[管理者用センター]**  >  **[FTPS 資格情報]** または **[ローカル Git/FTPS 資格情報]** を選択します。

    ![Azure App Services のデプロイ センターから FTP ダッシュボードを選択する方法を示します。](./media/app-service-deployment-credentials/access-no-git.png)

2. **[アプリケーション スコープ]** セクションで、 **[コピー]** リンクを選択してユーザー名またはパスワードをコピーします。

-----

## <a name="reset-application-scope-credentials"></a>アプリケーション スコープの資格情報をリセットする

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

[az resource invoke-action](/cli/azure/resource#az_resource_invoke_action) コマンドを使用して、アプリケーション スコープの資格情報をリセットします。

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) コマンドを使用して、アプリケーション スコープの資格情報をリセットします。

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. アプリの左側のメニューで、 **[管理者用センター]**  >  **[FTPS 資格情報]** または **[ローカル Git/FTPS 資格情報]** を選択します。

    ![Azure App Services のデプロイ センターから FTP ダッシュボードを選択する方法を示します。](./media/app-service-deployment-credentials/access-no-git.png)

2. **[アプリケーション スコープ]** セクションで、 **[リセット]** を選択します。

-----

## <a name="disable-basic-authentication"></a>基本認証を無効にする

組織によっては、セキュリティ要件を満たす必要があるため、FTP または WebDeploy によるアクセスを無効にした方がよい場合があります。 こうすることで、組織のメンバーは Azure Active Directory (Azure AD) で制御されている API を介してのみ App Services にアクセスできます。

### <a name="ftp"></a>FTP

サイトへの FTP アクセスを無効にするには、次の CLI コマンドを実行します。 プレースホルダーを目的のリソース グループとサイト名に置き換えます。 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

FTP アクセスがブロックされていることを確認するには、FileZilla などの FTP クライアントを使用して認証を試みます。 発行資格情報を取得するには、サイトの概要ブレードに移動して [発行プロファイルのダウンロード] をクリックします。 ファイルの FTP ホスト名、ユーザー名、パスワードを使用して認証すると、許可されないことを示す 401 エラー応答が表示されます。

### <a name="webdeploy-and-scm"></a>WebDeploy と SCM

WebDeploy ポートと SCM サイトへの基本認証アクセスを無効にするには、次の CLI コマンドを実行します。 プレースホルダーを目的のリソース グループとサイト名に置き換えます。 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

WebDeploy で発行プロファイルの資格情報がブロックされていることを確認するには、[Visual Studio 2019 を使用して Web アプリを発行](/visualstudio/deployment/quickstart-deploy-to-azure)してみます。

### <a name="disable-access-to-the-api"></a>API へのアクセスを無効にする

前のセクションの API は、Azure ロールベースのアクセス制御 (Azure RBAC) によってサポートされます。つまり、[カスタム ロールを作成](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)し、低い特権を持つユーザーをこのロールに割り当てると、これらのユーザーはどのサイトでも基本認証を有効化できなくなります。 カスタム ロールを構成するには、[こちらの手順を実行します](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role)。

また、[Azure Monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) を使用して成功した認証要求を監査し、[Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) を使用してサブスクリプション内のすべてのサイトにこの構成を適用することもできます。

## <a name="next-steps"></a>次のステップ

これらの資格情報を使用して、[ローカル Git](deploy-local-git.md) から、または [FTP/S](deploy-ftp.md) を使用してアプリをデプロイする方法を確認します。
