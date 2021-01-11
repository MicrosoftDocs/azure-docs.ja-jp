---
title: マネージド ID による認証
description: 資格情報やシークレットを使用してサインインすることなく、マネージド ID を使用して Azure Active Directory で保護されたリソースにアクセスする
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 95d892bf7a0c0e395289d4a5535cd9b6b789b055
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565929"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Azure Logic Apps でマネージド ID を使用して Azure リソースへのアクセスを認証する

サインインすることなく他の Azure Active Directory (Azure AD) によって保護される他のリソースに容易にアクセスして ID の認証を行うため、ロジック アプリは、資格情報やシークレットではなく [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) (以前はマネージド サービス ID (MSI) と呼ばれていました) を使用できます。 この ID は、ユーザーの代わりに Azure で管理されます。ユーザーがシークレットを提供したりローテーションしたりする必要がないため、資格情報の保護に役立ちます。

Azure Logic Apps では、"[*システム割り当て*](../active-directory/managed-identities-azure-resources/overview.md)" と "[*ユーザー割り当て*](../active-directory/managed-identities-azure-resources/overview.md)" の両方のマネージド ID がサポートされます。 ロジック アプリでは、システムによって割り当てられた ID または "*単一*" のユーザー割り当て ID のいずれかを使用できます。これは、ロジック アプリのグループ全体で共有できますが、両方を共有することはできません。 現在、[特定の組み込みトリガーおよびアクション](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)でのみマネージド ID がサポートされ、マネージド コネクタや接続はサポートされません。以下に例を示します。

* HTTP
* Azure Functions
* Azure API Management
* Azure App Service

この記事では、ロジック アプリ用に両方の種類のマネージド ID を設定する方法について説明します。 詳細については、以下のトピックを参照してください。

* [マネージド ID がサポートされているトリガーとアクション](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [送信呼び出しでサポートされている認証の種類](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [ロジック アプリのマネージド ID に関する制限](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [マネージド ID を使用した Azure AD 認証がサポートされているサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 マネージド ID とアクセスする必要があるターゲット Azure リソースの両方で、同じ Azure サブスクリプションを使用する必要があります。

* マネージド ID に Azure リソースへのアクセス権を付与するには、その ID のターゲット リソースにロールを追加する必要があります。 ロールを追加するには、対応する Azure AD テナント内の ID にロールを割り当てることができる、[Azure AD 管理者のアクセス許可](../active-directory/users-groups-roles/directory-assign-admin-roles.md)が必要です。

* アクセスするターゲット Azure リソース。 このリソースでは、マネージ ID のロールを追加します。これにより、ロジック アプリはターゲット リソースへのアクセスを認証できます。

* [トリガーまたはマネージド ID をサポートするアクション](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)を使用する必要があるロジック アプリ。

## <a name="enable-managed-identity"></a>マネージド ID の有効化

使用するマネージ ID を設定するには、該当する ID のリンクに従ってください。

* [システム割り当て ID](#system-assigned)
* [ユーザー割り当て ID](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>システム割り当て ID を有効にする

ユーザー割り当て ID とは異なり、システム割り当て ID を手動で作成する必要はありません。 ロジック アプリ用のシステム割り当て ID を設定する際は、次のオプションを使用できます。

* [Azure Portal](#azure-portal-system-logic-app)
* [Azure リソース マネージャーのテンプレート](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Azure portal でシステム割り当て ID を有効にする

1. [Azure Portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[ID]** を選択します。 **[システム割り当て済み]**  >  **[オン]**  >  **[保存]** を選択します。 確認を求めるメッセージが表示されたら、 **[はい]** を選択します。

   ![システム割り当て ID を有効にする](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > マネージド ID を 1 つだけ持つことができるというエラーが表示される場合、ロジック アプリは既にユーザー割り当て ID に関連付けられています。 システム割り当て ID を追加できるようにするには、まず、ロジック アプリからユーザー割り当て ID を "*削除*" する必要があります。

   これで、ロジック アプリでシステム割り当て ID を使用できるようになりました。この ID は Azure Active Directory に登録され、オブジェクト ID で表されます。

   ![システム割り当て ID のオブジェクト ID](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | プロパティ | 値 | 説明 |
   |----------|-------|-------------|
   | **オブジェクト ID** | <*identity-resource-ID*> | Azure AD テナント内のロジック アプリのシステム割り当て ID を表すグローバル一意識別子 (GUID) |
   ||||

1. 次に、このトピックの後半にある、[ID にリソースへのアクセス権を付与する手順](#access-other-resources)に従います。

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Azure Resource Manager テンプレートでシステム割り当て ID を有効にする

ロジック アプリなどの Azure リソースの作成とデプロイを自動化するために、[Azure Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)を使用できます。 テンプレートでロジック アプリのシステム割り当てマネージド ID を有効にするには、次のように、テンプレートのロジック アプリのリソース定義に `identity` オブジェクトと `type` 子プロパティを追加します。

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Azure によってロジック アプリのリソース定義が作成されると、`identity` オブジェクトで次の追加のプロパティが取得されます。

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| プロパティ (JSON) | 値 | 説明 |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | Azure AD テナント内のロジック アプリを表すマネージド ID のサービス プリンシパル オブジェクトのグローバル一意識別子 (GUID)。 この GUID は、"オブジェクト ID" (`objectID`) として表されることがあります。 |
| `tenantId` | <*Azure-AD-tenant-ID*> | ロジック アプリが現在メンバーとなっている Azure AD テナントを表すグローバル一意識別子 (GUID)。 Azure AD テナント内では、サービス プリンシパルは、ロジック アプリ インスタンスと同じ名前を持ちます。 |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>ユーザー割り当て ID を有効にする

ロジック アプリにユーザー割り当てのマネージド ID を設定するには、最初にその ID を別のスタンドアロン Azure リソースとして作成する必要があります。 次に、使用できるオプションを示します。

* [Azure Portal](#azure-portal-user-identity)
* [Azure リソース マネージャーのテンプレート](#template-user-identity)
* Azure PowerShell
  * [ユーザー割り当て ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [ロールの割り当てを追加する](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [ユーザー割り当て ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [ロールの割り当てを追加する](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST API
  * [ユーザー割り当て ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [ロールの割り当てを追加する](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Azure portal でユーザー割り当て ID を作成する

1. [Azure portal](https://portal.azure.com) で、任意のページの検索ボックスに「`managed identities`」と入力し、 **[マネージド ID]** を選択します。

   !["マネージド ID" を検索して選択する](./media/create-managed-service-identity/find-select-managed-identities.png)

1. **[マネージド ID]** の下の **[追加]** を選択します。

   ![新しいマネージド ID の追加](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. 次の例のようにマネージド ID に関する情報を指定し、 **[作成]** を選択します。

   ![ユーザー割り当てマネージド ID を作成する](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **リソース名** | はい | <*user-assigned-identity-name*> | ユーザー割り当て ID に付与する名前。 この例では、"Fabrikam-user-assigned-identity" を使用します。 |
   | **サブスクリプション** | はい | <*Azure サブスクリプション名*> | 使用する Azure サブスクリプションの名前。 |
   | **リソース グループ** | はい | <*Azure-resource-group-name*> | 使用するリソース グループの名前。 新しいグループを作成するか、既存のグループを選択します。 この例では、"fabrikam-managed-identities-RG" という名前の新しいグループを作成します。 |
   | **場所** | はい | <*Azure-region*> | リソースに関する情報の保存先となる Azure リージョン。 この例では "米国西部" を使用します。 |
   |||||

   これで、ユーザー割り当て ID をロジック アプリに追加できるようになりました。 ロジック アプリに複数のユーザー割り当て ID を追加することはできません。

1. Azure portal のロジック アプリ デザイナーで、ロジック アプリを選択して開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[ID]** を選択してから、 **[ユーザー割り当て済み]**  >  **[追加]** の順に選択します。

   ![ユーザー割り当てマネージド ID の追加](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. **[ユーザー割り当てマネージド ID の追加]** ウィンドウで、 **[サブスクリプション]** の一覧から、まだ選択されていない場合は使用する Azure サブスクリプションを選択します。 そのサブスクリプションの "*すべての*" マネージド ID を示す一覧から、目的のユーザー割り当て ID を検索して選択します。 一覧をフィルター処理するには、 **[ユーザー割り当て済みマネージド ID]** 検索ボックスに、ID またはリソース グループの名前を入力します。 終了したら、 **[追加]** を選択します。

   ![使用するユーザー割り当て ID を選択する](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > マネージド ID を 1 つだけ持つことができるというエラーが表示される場合、ロジック アプリは既にシステム割り当て ID に関連付けられています。 ユーザー割り当て ID を追加できるようにするには、まず、ロジック アプリのシステム割り当て ID を無効にする必要があります。

   これで、ロジック アプリがユーザー割り当てのマネージド ID に関連付けられました。

   ![ユーザー割り当て ID との関連付け](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. 次に、このトピックの後半にある、[ID にリソースへのアクセス権を付与する手順](#access-other-resources)に従います。

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートでシステム割り当て ID を作成する

ロジック アプリなどの Azure リソースの作成とデプロイを自動化するために、[Azure Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)を使用できます。これは[認証のためのユーザー割り当て ID](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) をサポートしています。 テンプレートの `resources` セクションでは、ロジック アプリのリソース定義に次の項目が必要です。

* `type` プロパティが `UserAssigned` に設定されている `identity` オブジェクト

* ユーザー割り当てリソースと名前が指定されている子 `userAssignedIdentities` オブジェクト

この例では、HTTP PUT 要求のロジック アプリのリソース定義を示しています。パラメーター化されていない `identity` オブジェクトが含まれています。 PUT 要求への応答と後続の GET 操作にも、この `identity` オブジェクトが含まれています。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

テンプレートにマネージド ID のリソース定義も含まれている場合は、`identity` オブジェクトをパラメーター化できます。 この例では、子 `userAssignedIdentities` オブジェクトが、テンプレートの `variables` セクションで定義した `userAssignedIdentity` 変数を参照する方法を示しています。 この変数は、ユーザー割り当て ID のリソース ID を参照しています。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>ID にリソースへのアクセス権を付与する

ロジック アプリのマネージド ID を認証に使用するには、ID を使用する予定の Azure リソースに対するその ID のアクセスを設定します。 このタスクを完了するには、ターゲットの Azure リソース上でその ID に適切な役割を割り当てます。 次に、使用できるオプションを示します。

* [Azure Portal](#azure-portal-assign-access)
* [Azure Resource Manager テンプレート](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)) - 詳細については、[Azure RBAC と Azure PowerShell を使用したロールの割り当ての追加](../role-based-access-control/role-assignments-powershell.md)に関する記事を参照してください。
* Azure CLI ([az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)) - 詳細については、[Azure RBAC と Azure CLI を使用したロールの割り当ての追加](../role-based-access-control/role-assignments-cli.md)に関する記事を参照してください。
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Azure portal 上でアクセスを割り当てる

1. [Azure portal](https://portal.azure.com) で、マネージド ID にアクセス権を与える Azure リソースに移動します。

1. リソースのメニューから、 **[アクセス制御 (IAM)]**  >  **[ロールの割り当て]** を選択します。ここで、そのリソースの現在のロールの割り当てを確認できます。 ツール バーで、 **[追加]**  >  **[ロールの割り当ての追加]** の順に選択します。

   ![[追加] > [ロールの割り当ての追加] の順に選択する](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > **[ロールの割り当ての追加]** オプションが無効になっている場合は、ご自分にアクセス許可がない可能性があります。 リソースのロールを管理するためのアクセス許可の詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](../active-directory/users-groups-roles/directory-assign-admin-roles.md)」をご覧ください。

1. **[ロールの割り当ての追加]** で、ID にターゲット リソースへのアクセスに必要なアクセス権を与える**ロール**を選択します。

   このトピックの例では、ID に [Azure Storage コンテナー内の BLOB にアクセスできるロール](../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)が必要です。

   ![[ストレージ BLOB データ共同作成者] ロールを選択する](./media/create-managed-service-identity/select-role-for-identity.png)

1. 使用するマネージド ID ついて、次の手順に従います。

   * **システム割り当て ID**

     1. **[アクセスの割り当て先]** ボックスで、 **[ロジック アプリ]** を選択します。 **[サブスクリプション]** プロパティが表示されたら、ID に関連付けられている Azure サブスクリプションを選択します。

        ![システム割り当て ID のアクセス権を選択する](./media/create-managed-service-identity/assign-access-system.png)

     1. **[選択]** ボックスで、一覧からロジック アプリを選択します。 一覧が長すぎる場合は、 **[選択]** ボックスを使用して一覧をフィルター処理します。

        ![システム割り当て ID を設定するロジック アプリを選択する](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **ユーザー割り当て ID**

     1. **[アクセスの割り当て先]** ボックスで、 **[ユーザー割り当てマネージド ID]** を選択します。 **[サブスクリプション]** プロパティが表示されたら、ID に関連付けられている Azure サブスクリプションを選択します。

        ![ユーザー割り当て ID のアクセス権を選択する](./media/create-managed-service-identity/assign-access-user.png)

     1. **[選択]** ボックスで、一覧から ID を選択します。 一覧が長すぎる場合は、 **[選択]** ボックスを使用して一覧をフィルター処理します。

        ![ユーザー割り当て ID の選択](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. 終了したら、 **[保存]** を選択します。

   ターゲット リソースの [ロールの割り当て] の一覧に、選択したマネージド ID とロールが表示されます。 この例では、1 つのロジック アプリに対してシステム割り当て ID を使用し、他のロジック アプリのグループに対してユーザー割り当て ID を使用する方法を示します。

   ![ターゲット リソースに追加されたマネージド ID とロール](./media/create-managed-service-identity/added-roles-for-identities.png)

   詳細については、「[Azure portal を使用してリソースにマネージド ID アクセスを割り当てる](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)」を参照してください。

1. 次に、マネージド ID がサポートされているトリガーまたはアクションで [ID 使用してアクセスを認証する手順](#authenticate-access-with-identity)に従います。

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>マネージド ID を利用してアクセスを認証する

[ロジック アプリに対してマネージド ID を有効](#azure-portal-system-logic-app)にし、[その ID にターゲット リソースまたはエンティティへのアクセス権を付与](#access-other-resources)したら、その ID を[マネージド ID がサポートされているトリガーとアクション](logic-apps-securing-a-logic-app.md#managed-identity-authentication)で使用できます。

> [!IMPORTANT]
> システム割り当て ID を使用する Azure 関数がある場合は、先に [Azure Functions の認証を有効](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)にします。

次の手順では、Azure portal を通じて、トリガーまたはアクションでマネージド ID を使用する方法を示します。 トリガーまたはアクションの基になる JSON 定義でマネージド ID を指定する方法については、「[マネージド ID 認証](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)」をご覧ください。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. まだ行っていない場合は、[マネージド ID がサポートされているトリガーまたはアクション](logic-apps-securing-a-logic-app.md#managed-identity-authentication)を追加します。

   たとえば、HTTP トリガーまたはアクションでは、ロジック アプリに対して有効にしたシステム割り当て ID を使用できます。 一般に、HTTP トリガーまたはアクションでは、次のプロパティを使用して、アクセスするリソースまたはエンティティを指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **方法** | はい | 実行する操作によって使用される HTTP メソッド |
   | **URI** | はい | ターゲットの Azure リソースまたはエンティティにアクセスするためのエンドポイント URL。 URI 構文には、通常、Azure リソースまたはサービスの[リソース ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) が含まれています。 |
   | **ヘッダー** | いいえ | コンテンツ タイプなど、送信要求に含める必要がある (または含めたい) ヘッダーの値 |
   | **クエリ** | いいえ | 特定の操作のパラメーターや実行する操作の API バージョンなど、要求に含める必要がある (または含めたい) クエリ パラメーター |
   | **認証** | はい | ターゲット リソースまたはエンティティへのアクセスの認証に使用する認証の種類 |
   ||||

   具体的な例として、以前に ID 用のアクセスを設定した Azure Storage アカウントの BLOB に対して [Snapshot Blob 操作](/rest/api/storageservices/snapshot-blob)を実行するとします。 しかし、[Azure Blob Storage コネクタ](/connectors/azureblob/)では現在、この操作が提供されていません。 代わりに、[HTTP アクション](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)または別の [BLOB サービス REST API 操作](/rest/api/storageservices/operations-on-blobs)を使用して、この操作を実行できます。

   > [!IMPORTANT]
   > HTTP 要求とマネージド ID を使用してファイアウォールの背後にある Azure Storage アカウントにアクセスするには、[信頼された Microsoft サービスによるアクセスを許可する例外](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)を使用してストレージ アカウントを設定する必要もあります。

   [Snapshot Blob 操作](/rest/api/storageservices/snapshot-blob)を実行するには、HTTP アクションで次のプロパティを指定します。

   | プロパティ | 必須 | 値の例 | 説明 |
   |----------|----------|---------------|-------------|
   | **方法** | はい | `PUT`| Snapshot Blob 操作で使用する HTTP メソッド |
   | **URI** | はい | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | この構文を使用する Azure Global (パブリック) 環境内の Azure Blob Storage ファイルのリソース ID |
   | **ヘッダー** | はい (Azure Storage の場合) | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Azure Storage 操作に必要な `x-ms-blob-type` ヘッダーと `x-ms-version` ヘッダーの値。 <p><p>**重要**:Azure Storage の発信 HTTP トリガーおよびアクションの要求では、ヘッダーに `x-ms-version` プロパティと実行する操作の API バージョンが必要です。 <p>詳細については、以下のトピックを参照してください。 <p><p>- [要求ヘッダー - Snapshot Blob](/rest/api/storageservices/snapshot-blob#request) <br>- [Azure Storage サービスのバージョン管理](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **クエリ** | はい (この操作の場合) | `comp` = `snapshot` | Snapshot Blob 操作のクエリ パラメーターの名前と値。 |
   |||||

   上記すべてのプロパティ値を示す HTTP アクションの例:

   ![Azure リソースにアクセスするための HTTP アクションを追加する](./media/create-managed-service-identity/http-action-example.png)

1. 次に、HTTP アクションに **Authentication** プロパティを追加します。 **[新しいパラメーターの追加]** 一覧で **[認証]** を選択します。

   ![HTTP アクションに "認証" プロパティを追加する](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > すべてのトリガーとアクションで認証の種類を追加できるわけではありません。 詳しくは、「[送信呼び出しに認証を追加する](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)」をご覧ください。

1. **[認証タイプ]** 一覧から、 **[マネージド ID]** を選択します。

   ![[認証] で [マネージド ID] を選択する](./media/create-managed-service-identity/select-managed-identity.png)

1. マネージド ID の一覧から、シナリオに基づいて使用可能なオプションを選択します。

   * システム割り当て ID を設定した場合は、 **[システム割り当てマネージド ID]** を選択します (まだ選択されていない場合)。

     ![[システム割り当てマネージド ID] を選択](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * ユーザー割り当て ID を設定した場合は、その ID を選択します (まだ選択されていない場合)。

     ![ユーザー割り当て ID の選択](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   この例では、 **[システム割り当てマネージド ID]** を使用して先に進みます。

1. 一部のトリガーとアクションでは、ターゲット リソース ID を設定するために **[対象ユーザー]** プロパティも表示されます。 **[対象ユーザー]** プロパティは[ターゲット リソースまたはサービスのリソース ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) に設定します。 そうしないと、既定では、 **[対象ユーザー]** プロパティに Azure Resource Manager のリソース ID である `https://management.azure.com/` リソース ID が使用されます。

   > [!IMPORTANT]
   > ターゲット リソース ID は、必要な末尾のスラッシュも含めて、Azure Active Directory (AD) で予想される値と*正確に一致*するようにします。 たとえば、すべての Azure Blob Storage アカウントのリソース ID には、末尾のスラッシュが必要です。 ただし、特定のストレージ アカウントのリソース ID については、末尾のスラッシュは必要ありません。 「[Azure AD 認証をサポートしている Azure サービスのリソース ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)」をご覧ください。

   この例では、 **[対象ユーザー]** プロパティを `https://storage.azure.com/` に設定して、認証に使用されるアクセス トークンをすべてのストレージ アカウントに対して有効にしています。 ただし、特定のストレージ アカウントに対してルート サービス URL (`https://fabrikamstorageaccount.blob.core.windows.net`) を指定することもできます。

   ![[対象ユーザー] プロパティをターゲット リソース ID に設定する](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Azure AD を使用した Azure Storage へのアクセスの承認について詳しくは、次のトピックをご覧ください。

   * [Azure Active Directory を使用して Azure BLOB およびキューへのアクセスを承認する](../storage/common/storage-auth-aad.md)
   * [Azure Active Directory を使用して Azure Storage へのアクセスを承認する](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. 必要な方法でロジック アプリの構築を続行します。

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>マネージド ID を無効にする

ロジック アプリのマネージド ID の使用を停止する場合は、次のオプションがあります。

* [Azure Portal](#azure-portal-disable)
* [Azure リソース マネージャーのテンプレート](#template-disable)
* Azure PowerShell
  * [ロールの割り当てを削除する](../role-based-access-control/role-assignments-powershell.md)
  * [ユーザー割り当て ID を削除する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [ロールの割り当てを削除する](../role-based-access-control/role-assignments-cli.md)
  * [ユーザー割り当て ID を削除する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST API
  * [ロールの割り当てを削除する](../role-based-access-control/role-assignments-rest.md)
  * [ユーザー割り当て ID を削除する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

ロジック アプリを削除すると、Azure によってマネージド ID が Azure AD から自動的に削除されます。

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Azure portal でマネージド ID を無効にする

Azure portal で、まず、[ターゲット リソース](#disable-identity-target-resource)に対する ID のアクセス権を削除します。 次に、システム割り当て ID をオフにするか、ユーザー割り当て ID を [ロジック アプリ](#disable-identity-logic-app)から削除します。

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>リソースから ID のアクセス権を削除する

1. [Azure portal](https://portal.azure.com) で、マネージド ID のアクセス権を削除するターゲット Azure リソースに移動します。

1. ターゲット リソースのメニューから **[アクセス制御 (IAM)]** を選択します。 ツール バーで、 **[ロールの割り当て]** を選択します。

1. ロールの一覧で、削除するマネージド ID を選択します。 ツール バーの **[削除]** を選択します。

   > [!TIP]
   > **[削除]** オプションが無効になっている場合は、ご自分にアクセス許可がない可能性があります。 リソースのロールを管理するためのアクセス許可の詳細については、「[Azure Active Directory での管理者ロールのアクセス許可](../active-directory/users-groups-roles/directory-assign-admin-roles.md)」をご覧ください。

マネージド ID が削除され、ターゲット リソースにアクセスできなくなりました。

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>ロジック アプリのマネージド ID を無効にする

1. [Azure Portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[ID]** を選択してから、お使いの ID の手順に従います。

   * **[システム割り当て済み]**  >  **[オン]**  >  **[保存]** を選択します。 確認を求めるメッセージが表示されたら、 **[はい]** を選択します。

     ![システム割り当て ID を無効にする](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * **[ユーザー割り当て済み]** とマネージド ID を選択し、 **[削除]** を選択します。 確認を求めるメッセージが表示されたら、 **[はい]** を選択します。

     ![ユーザー割り当て ID の削除](./media/create-managed-service-identity/remove-user-assigned-identity.png)

これで、マネージド ID がロジック アプリで無効になりました。

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Resource Manager テンプレートでマネージド ID を無効にする

Azure Resource Manager テンプレートを使用してロジック アプリのマネージド ID を作成した場合は、`identity` オブジェクトの `type` 子プロパティを `None` に設定します。

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps におけるアクセスとデータのセキュリティ保護](../logic-apps/logic-apps-securing-a-logic-app.md)
