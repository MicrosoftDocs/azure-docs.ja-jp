---
title: Azure Enterprise サブスクリプションを作成する権限を付与する | Microsoft Docs
description: ユーザーまたはサービス プリンシパルがプログラムで Azure Enterprise サブスクリプションを作成できるようにする方法について説明します。
services: azure-resource-manager
author: jureid
manager: jureid
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: a7ed7dffd27b51c1314c4293820dc33be4d7e8e0
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206637"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Azure Enterprise サブスクリプションを作成する権限を付与する (プレビュー)

[Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)を結んでいる Azure の顧客は、自分のアカウントに課金されるサブスクリプションを別のユーザーまたはサービス プリンシパルが作成することを許可できます。 この記事では、[ロール ベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-configure.md) を使用して、サブスクリプションの作成機能を共有する方法と、サブスクリプションの作成を監査する方法について説明します。 共有するアカウントに所有者ロールが必要です。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="grant-access"></a>アクセス権の付与

[登録アカウントでサブスクリプションを作成する](programmatically-create-subscription.md)には、ユーザーがそのアカウントに対して [RBAC 所有者ロール](../role-based-access-control/built-in-roles.md#owner)を持っている必要があります。 次の手順に従うことによって、ユーザーまたはユーザー グループに、登録アカウントに対する RBAC 所有者ロールを付与できます。

1. アクセス権を付与する登録アカウントのオブジェクト ID を取得します

    登録アカウントに対する RBAC 所有者ロールを他のユーザーに付与するには、アカウント オーナーまたはそのアカウントの RBAC 所有者である必要があります。

    # <a name="resttabrest"></a>[REST](#tab/rest)

    自分がアクセスできるすべての登録アカウントを一覧表示するための要求:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    自分がアクセスできるすべての登録アカウントの一覧が Azure から返されます。

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    `principalName` プロパティを使用して、RBAC 所有者のアクセス権を付与するアカウントを指定します。 そのアカウントの `name` をコピーします。 たとえば、SignUpEngineering@contoso.com 登録アカウントに RBAC 所有者のアクセス権を付与する場合、```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` をコピーします。 これは登録アカウントのオブジェクト ID です。 次のステップでこの値を `enrollmentAccountObjectId` として使用できるよう、どこかに貼り付けておきます。

    # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

    [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) コマンドレットを使用して、自分がアクセスできるすべての登録アカウントを一覧表示します。 **[試してみる]** を選択して、[Azure Cloud Shell](https://shell.azure.com/) を開きます。 コードを貼り付けるには、シェル ウィンドウを右クリックして、 **[貼り付け]** を選択します。

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    自分がアクセスできる登録アカウントの一覧が Azure から返されます。

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    `principalName` プロパティを使用して、RBAC 所有者のアクセス権を付与するアカウントを指定します。 そのアカウントの `ObjectId` をコピーします。 たとえば、SignUpEngineering@contoso.com 登録アカウントに RBAC 所有者のアクセス権を付与する場合、```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` をコピーします。 次の手順で `enrollmentAccountObjectId` として使用できるように、このオブジェクト ID をどこかに貼り付けておきます。

    # <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

    [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) コマンドを使用して、自分がアクセスできるすべての登録アカウントを一覧表示します。 **[試してみる]** を選択して、[Azure Cloud Shell](https://shell.azure.com/) を開きます。 コードを貼り付けるには、シェル ウィンドウを右クリックして、 **[貼り付け]** を選択します。

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    自分がアクセスできる登録アカウントの一覧が Azure から返されます。

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    `principalName` プロパティを使用して、RBAC 所有者のアクセス権を付与するアカウントを指定します。 そのアカウントの `name` をコピーします。 たとえば、SignUpEngineering@contoso.com 登録アカウントに RBAC 所有者のアクセス権を付与する場合、```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` をコピーします。 これは登録アカウントのオブジェクト ID です。 次のステップでこの値を `enrollmentAccountObjectId` として使用できるよう、どこかに貼り付けておきます。

1. <a id="userObjectId"></a>RBAC 所有者ロールを付与するユーザーまたはグループのオブジェクト ID を取得します

    1. Azure portal で、**Azure Active Directory** を検索します。
    1. ユーザーにアクセス権を付与する場合は、左側のメニューで **[ユーザー]** をクリックします。 グループにアクセス権を付与する場合は、 **[グループ]** をクリックします。
    1. RBAC 所有者ロールを付与するユーザーまたはグループを選択します。
    1. ユーザーを選択した場合、オブジェクト ID は [プロファイル] ページにあります。 グループを選択した場合、オブジェクト ID は [概要] ページにあります。 テキスト ボックスの右側にあるアイコンをクリックして、**ObjectID** をコピーします。 次の手順で `userObjectId` として使用できるように、これをどこかに貼り付けておきます。

1. ユーザーまたはグループに、登録アカウントに対する RBAC 所有者ロールを付与します

    最初の 2 つの手順で収集した値を使用して、ユーザーまたはグループに、登録アカウントに対する RBAC 所有者ロールを付与します。

    # <a name="resttabrest-2"></a>[REST](#tab/rest-2)

    ```<enrollmentAccountObjectId>``` を最初の手順でコピーした `name` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) で置き換えて、次のコマンドを実行します。 ```<userObjectId>``` は、2 番目の手順でコピーしたオブジェクト ID で置き換えます。

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    登録アカウントのスコープで所有者ロールが付与されると、ロール割り当ての情報が Azure から返されます。

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershelltabazure-powershell-2"></a>[PowerShell](#tab/azure-powershell-2)

    ```<enrollmentAccountObjectId>``` を最初の手順で収集した `ObjectId` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) で置き換えて、次の [New-AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) コマンドを実行します。 ```<userObjectId>``` は、2 番目の手順で収集したオブジェクト ID で置き換えます。

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-clitabazure-cli-2"></a>[Azure CLI](#tab/azure-cli-2)

    ```<enrollmentAccountObjectId>``` を最初の手順でコピーした `name` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) で置き換えて、次の [az role assignment create](../active-directory/role-based-access-control-manage-access-azure-cli.md) コマンドを実行します。 ```<userObjectId>``` は、2 番目の手順で収集したオブジェクト ID で置き換えます。

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    登録アカウントの RBAC 所有者になったユーザーは、そのアカウントで[プログラミングによってサブスクリプションを作成](programmatically-create-subscription.md)できるようになります。 委任されたユーザーによって作成されたサブスクリプションには、元のアカウント所有者がサービス管理者として設定されたままになりますが、既定で委任されたユーザーも RBAC 所有者として設定されています。

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>アクティビティ ログを使用してサブスクリプションを作成した監査

この API を使用して作成されたサブスクリプションを追跡するには、[Tenant Activity Log API](/rest/api/monitor/tenantactivitylogs) を使用します。 現時点では、PowerShell、CLI、または Azure Portal を使用してサブスクリプションの作成を追跡することはできません。

1. Azure AD テナントのテナント管理者は、[アクセス権限を昇格](../active-directory/role-based-access-control-tenant-admin-access.md)してから、スコープ内の監査ユーザーに閲覧者ロールを割り当てます`/providers/microsoft.insights/eventtypes/management`。
1. 監査ユーザーは、[Tenant Activity Log API](/rest/api/monitor/tenantactivitylogs) を呼び出して、サブスクリプションの作成活動を確認します。 例:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

コマンドラインからこの API を簡単に呼び出すには、[ARMClient](https://github.com/projectkudu/ARMClient) を使用します。

## <a name="next-steps"></a>次の手順

* これで、ユーザーまたはサービス プリンシパルにサブスクリプションを作成する権限が与えられたので、その ID を使って [Azure Enterprise サブスクリプションをプログラムで作成](programmatically-create-subscription.md)できます。
* .NET を使用してサブスクリプションを作成する例については、[GitHub のサンプル コード](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)を参照してください。
* Azure Resource Manager とその API については、「[Azure Resource Manager の概要](resource-group-overview.md)」を参照してください。
* 管理グループを使用して大量のサブスクリプションを管理する方法については、「[Azure 管理グループのリソースを整理する](management-groups-overview.md)」を参照してください。
* 大規模組織でのサブスクリプション管理に関する包括的なベスト プラクティス ガイダンスについては、「[Azure エンタープライズ スキャフォールディング - 規範的なサブスクリプション ガバナンス](/azure/architecture/cloud-adoption-guide/subscription-governance)」を参照してください。
