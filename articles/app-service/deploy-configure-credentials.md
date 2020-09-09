---
title: デプロイ資格情報の構成
description: Azure App Service のデプロイ資格情報の種類と、それらを構成および使用する方法について説明します。
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 45d2ec6cf4b2a54b899036d932bc310caede3c29
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223858"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Azure App Service のデプロイ資格情報の構成
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) では、[ローカル Git デプロイ](deploy-local-git.md)と [FTP/S デプロイ](deploy-ftp.md)デプロイ用の 2 種類の資格情報をサポートしています。 これらの資格情報は Azure サブスクリプションの資格情報とは異なります。

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>ユーザーレベルの資格情報を構成する

ユーザー レベルの資格情報は、任意のアプリの[リソース ページ](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)で構成できます。 どのアプリで構成した場合でも、これらの資格情報は、Azure アカウント内のすべてのアプリのすべてのサブスクリプションに適用されます。 

### <a name="in-the-cloud-shell"></a>Cloud Shell で

[Cloud Shell](https://shell.azure.com) でデプロイ ユーザーを構成するには、[az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) コマンドを実行します。 \<username> と \<password> を、デプロイ ユーザーのユーザー名とパスワードで置き換えます。 

- ユーザー名は、Azure 内で一意である必要があり、ローカル Git プッシュの場合は "\@" シンボルを含めることはできません。 
- パスワードは長さが 8 文字以上で、文字、数字、記号のうち 2 つを含む必要があります。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 出力には、パスワードが `null` として表示されます。 `'Conflict'. Details: 409` エラーが発生した場合は、ユーザー名を変更します。 `'Bad Request'. Details: 400` エラーが発生した場合は、より強力なパスワードを使用します。 

### <a name="in-the-portal"></a>ポータルで

Azure portal で、[デプロイ資格情報] ページにアクセスするには、少なくとも 1 つのアプリが必要です。 ユーザー レベルの資格情報を構成するには:

1. [Azure portal](https://portal.azure.com) の左側のメニューで、 **[App Services]**  >  **\<any_app>**  >  **[デプロイ センター]**  >  **[FTP]**  >  **[ダッシュボード]** を選択します。

    ![Azure App Services のデプロイ センターから FTP ダッシュボードを選択する方法を示します。](./media/app-service-deployment-credentials/access-no-git.png)

    あるいは、Git デプロイを既に構成している場合、 **[App Services]** 、 **[&lt;任意のアプリ>]** 、 **[デプロイ センター]** 、 **[FTP/資格情報]** を選択します。

    ![構成済み Git デプロイのために Azure App Services のデプロイ センターから FTP ダッシュボードを選択する方法を示します。](./media/app-service-deployment-credentials/access-with-git.png)

2. **[ユーザーの資格情報]** を選択し、ユーザー名とパスワードを構成してから、 **[資格情報の保存]** を選択します。

デプロイ資格情報を設定すると、*Git* デプロイのユーザー名がアプリの **[概要]** ページに表示されます。

![アプリの [概要] ページで Git デプロイ ユーザー名を見つける方法を示します。](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Git デプロイが構成されている場合、ページに **Git/デプロイ ユーザー名**が表示されます。構成されていない場合、**FTP/デプロイ ユーザー名**が表示されます。

> [!NOTE]
> Azure では、ユーザー レベルのデプロイ パスワードを表示しません。 パスワードを忘れた場合は、このセクションの手順に従って、資格情報をリセットできます。
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>ユーザーレベルの資格情報と FTP/FTPS を使用する

ユーザーレベルの資格情報を使用した FTP または FTPS エンドポイントの認証には、`<app-name>\<user-name>` という形式のユーザー名が必要です。

ユーザーレベルの資格情報は、特定のリソースではなく、ユーザーにリンクされているため、適切なアプリのエンドポイントに対してサインイン アクションを実行するには、ユーザー名はこの形式でなければなりません。

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>アプリ レベルの資格情報の設定とリセット
アプリ レベルの資格情報を取得するには:

1. [Azure portal](https://portal.azure.com) の左側のメニューで、 **[App Services]** 、 **[&lt;任意のアプリ>]** 、 **[デプロイ センター]** 、 **[FTP/資格情報]** を選択します。

2. **[アプリの資格情報]** を選択し、 **[コピー]** リンクを選択し、ユーザー名またはパスワードをコピーします。

アプリレベルの資格情報をリセットするには、同じダイアログで **[資格情報のリセット]** を選択します。

## <a name="next-steps"></a>次のステップ

これらの資格情報を使用して、[ローカル Git](deploy-local-git.md) から、または [FTP/S](deploy-ftp.md) を使用してアプリをデプロイする方法を確認します。
