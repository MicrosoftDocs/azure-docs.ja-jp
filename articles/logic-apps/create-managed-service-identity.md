---
title: マネージド ID を使用して認証する - Azure Logic Apps | Microsoft Docs
description: サインインなしで認証するために、マネージド ID (以前はマネージド サービス ID (MSI) と呼ばれていました) を作成できます。そうすることで、ロジック アプリは資格情報やシークレットなしで他の Azure Active Directory (Azure AD) テナント内のリソースにアクセスできます
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 03/29/2019
ms.openlocfilehash: b157db5032bd62ab443209f201b4ceded6e44cb5
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385557"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Azure Logic Apps でマネージド ID を使用して認証し、リソースにアクセスする

サインインすることなく他の Azure Active Directory (Azure AD) テナント内のリソースへのアクセスと ID の認証を行うために、ロジック アプリは、資格情報やシークレットではなく [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) (以前はマネージド サービス ID (MSI) と呼ばれていました) を使用できます。 この ID は、ユーザーの代わりに Azure で管理されます。ユーザーがシークレットを提供したりローテーションしたりする必要がないため、資格情報の保護に役立ちます。 この記事では、ロジック アプリのためにシステム割り当てのマネージド ID を設定して使用する方法を示します。 マネージド ID の詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

> [!NOTE]
> ロジック アプリでは、マネージド ID をサポートするコネクタでのみマネージド ID を使用できます。 現時点では、HTTP コネクタのみがマネージド ID をサポートしています。
>
> 現在、システム割り当てのマネージド ID は、各 Azure サブスクリプションで最大 10 個までのロジック アプリ ワークフローに割り当てることができます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。サブスクリプションをお持ちでない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

* システム割り当てのマネージド ID を使用するロジック アプリ。 ロジック アプリをお持ちでない場合は、[初めてのロジック アプリ ワークフローを作成する](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>マネージド ID の有効化

システム割り当てマネージド ID の場合、その ID を手動で作成する必要はありません。 ロジック アプリのシステム割り当て ID を設定するには、次の方法を使用できます。 

* [Azure Portal](#azure-portal) 
* [Azure リソース マネージャーのテンプレート](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>Azure ポータル

Azure portal から、ロジック アプリ用のシステム割り当てのマネージド ID を有効にするには、目的のロジック アプリの ID 設定で、 **[システム割り当て済み]** 設定をオンにします。

1. [Azure Portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[ID]** を選択します。 

1. **[システム割り当て済み]**  >  **[状態]** で、 **[オン]** を選択します。 次に、 **[保存]**  >  **[はい]** を選択します。

   ![マネージド ID 設定をオンにする](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   これでロジック アプリに、Azure Active Directory に登録された、システム割り当てのマネージド ID が割り当てられました。

   ![オブジェクト ID の GUID](./media/create-managed-service-identity/object-id.png)

   | プロパティ | 値 | 説明 | 
   |----------|-------|-------------| 
   | **オブジェクト ID** | <*identity-resource-ID*> | Azure AD テナント内のロジック アプリのシステム割り当てのマネージド ID を表すグローバル一意識別子 (GUID) | 
   ||| 

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

ロジック アプリなどの Azure リソースの作成とデプロイを自動化するときには、[Azure Resource Manager テンプレート](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)を利用できます。 テンプレートを使ってロジック アプリ用のシステム割り当てマネージド ID を作成するには、デプロイ テンプレートで、ロジック アプリのワークフロー定義に `"identity"` 要素と `"type"` プロパティを追加します。 

```json
"identity": {
   "type": "SystemAssigned"
}
```

例:

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

Azure でロジック アプリを作成すると、そのロジック アプリのワークフロー定義には、これらの追加プロパティが含まれます。

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| プロパティ | 値 | 説明 | 
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | Azure AD テナント内のロジック アプリを表し、"オブジェクト ID" や `objectID` として表示されることもあるグローバル一意識別子 (GUID) | 
| **tenantId** | <*Azure-AD-tenant-ID*> | ロジック アプリが現在メンバーとなっている Azure AD テナントを表すグローバル一意識別子 (GUID)。 Azure AD テナント内では、サービス プリンシパルは、ロジック アプリ インスタンスと同じ名前を持ちます。 | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>マネージド ID を使用してリソースにアクセスする

ロジック アプリ用にシステム割り当てマネージド ID を作成すると、[その ID に他の Azure リソースへのアクセスを与える](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)ことができます。 その後は、他の[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)と同様に、その ID を認証に使用できます。 

> [!NOTE]
> アクセスを割り当てるシステム割り当てマネージド ID とリソースの両方が、同じ Azure サブスクリプションを持っている必要があります。

### <a name="assign-access-to-managed-identity"></a>マネージド ID にアクセスを割り当てる

ロジック アプリのシステム割り当てマネージド ID に別の Azure リソースへのアクセスを付与するには、以下の手順に従います。

1. Azure portal で、マネージド ID にアクセスを割り当てる Azure リソースに移動します。 

1. リソースのメニューから **[アクセス制御 (IAM)]** を選択します。 ツール バーで、 **[追加]**  >  **[ロールの割り当ての追加]** の順に選択します。

   ![ロールの割り当ての追加](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. **[ロールの割り当ての追加]** で、その ID の**ロール**を選択します。 

1. **[アクセスの割り当て先]** プロパティで、まだ選択されていなければ、 **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選択します。

1. **[選択]** ボックスで、ロジック アプリの名前の入力を、ロジック アプリの名前の最初の文字で始めます。 目的のロジック アプリが表示されたら、そのロジック アプリを選択します。

   ![マネージド ID を使用するロジック アプリを選択する](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. 完了したら、 **[保存]** を選択します。

### <a name="authenticate-with-managed-identity-in-logic-app"></a>ロジック アプリでマネージド ID を使用して認証する

システム割り当てのマネージド ID を使用してロジック アプリを設定し、必要なリソースへのアクセスをその ID に割り当てたら、認証のためにその ID を使用できるようになっています。 たとえば、ロジック アプリでそのリソースに HTTP の要求や呼び出しを送信できるようにする HTTP アクションを使用できます。 

1. ロジック アプリで、**HTTP** アクションを追加します。

1. 呼び出すリソースの要求**メソッド**と **URI** の場所など、そのアクションに必要な詳細を提供します。

   たとえば、[Azure AD をサポートする次の Azure サービスのいずれかで](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) Azure Active Directory (Azure AD) 認証を使用しているとします。 
   **[URI]** ボックスに、その Azure サービスのエンドポイント URL を入力します。 
   つまり、Azure Resource Manager を使用している場合、 **[URI]** プロパティにこの値を入力します。

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version=2016-06-01`

1. HTTP アクションで、 **[詳細オプションの表示]** を選択します。

1. **[認証]** 一覧から、 **[マネージド ID]** を選択します。 この認証を選択後、 **[対象ユーザー]** プロパティが既定のリソース ID 値で表示されます。

   ![[マネージド ID] を選択します](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > **[対象ユーザー]** プロパティで、リソース ID 値は、必須の末尾のスラッシュも含めて Azure AD で予想される値に正確に一致している必要があります。 
   > これらのリソース ID 値は、この [Azure AD をサポートする Azure サービスについて説明する表](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)で確認できます。 
   > たとえば、Azure Resoruce Manager のリソース ID を使用している場合、URI の末尾にスラッシュがあることを確認します。

1. ご希望の方法でロジック アプリのビルドを続行します。

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>マネージド ID の削除

Azure portal、Azure Resource Manager デプロイ テンプレート、または Azure PowerShell から ID を設定した方法と同様の手順に従えば、ロジック アプリでシステム割り当てのマネージド ID を無効にできます。

ロジック アプリを削除すると、Azure によってロジック アプリのシステム割り当て ID が Azure AD から自動的に削除されます。

### <a name="azure-portal"></a>Azure ポータル

Azure portal から、ロジック アプリ用のシステム割り当てのマネージド ID を削除するには、目的のロジック アプリの ID 設定で、 **[システム割り当て済み]** 設定をオフにします。

1. [Azure Portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます。

1. ロジック アプリのメニューの **[設定]** で、 **[ID]** を選択します。 

1. **[システム割り当て済み]**  >  **[状態]** で、 **[オフ]** を選択します。 次に、 **[保存]**  >  **[はい]** を選択します。

   ![マネージド ID 設定をオフにする](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>デプロイ テンプレート

Azure Resource Manager デプロイ テンプレートを使用してロジック アプリのシステム割り当てマネージド ID を作成した場合は、`"identity"` 要素の `"type"` プロパティを `"None"` に設定します。 このアクションにより、プリンシパル ID も Azure AD から削除されます。

```json
"identity": {
   "type": "None"
}
```

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。
