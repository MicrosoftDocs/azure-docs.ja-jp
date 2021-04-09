---
title: Azure Enterprise Agreement サービス プリンシパル名にロールを割り当てる
description: この記事は、PowerShell と REST API を使用してサービス プリンシパル名にロールを割り当てる際に役立ちます。
author: bandersmsft
ms.reviewer: ruturajd
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/07/2021
ms.author: banders
ms.openlocfilehash: e7f5370e1e387947d196959fef31043ea8f4d3bd
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508522"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>Azure Enterprise Agreement サービス プリンシパル名にロールを割り当てる

[Azure Enterprise Portal](https://ea.azure.com/) では、Enterprise Agreement (EA) の登録を管理できます。 さまざまなロールを作成して、組織を管理したり、コストを表示したり、サブスクリプションを作成したりすることができます。 この記事は、Azure PowerShell と各種の REST API、さらに Azure のサービス プリンシパル名 (SPN) を使用してこれらのタスクの一部を自動化する際に役立ちます。

始める前に、次の記事の内容を理解しておいてください。

- [Enterprise Agreement の各種ロール](understand-ea-roles.md)
- [Azure PowerShell を使用してサインインする](/powershell/azure/authenticate-azureps)
- [Postman を使用して REST API を呼び出す方法](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>サービス プリンシパルを作成および認証する

SPN を使用して EA に関するアクションを自動化するには、Azure Active Directory (Azure AD) アプリケーションを作成する必要があります。 その認証は自動的に実行できます。 サービス プリンシパルの作成と認証については、次の記事を参照のうえ、その手順に従ってください。

1. [サービス プリンシパルの作成](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [サインインするためのテナントとアプリ ID の値を取得する](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

次のスクリーンショットは、アプリケーションを登録するようすを示した例です。

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="アプリケーションを登録するようすを示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>SPN と Tenant ID を特定する

また、SPN のオブジェクト ID とアプリのテナント ID も必要です。 この情報は、後のセクションでアクセス許可の割り当て操作をする際に必要になります。

Azure AD アプリのテナント ID は、アプリケーションの概要ページにあります。 Azure portal で探すときは、Azure Active Directory に移動し、 **[エンタープライズ アプリケーション]** を選択します。 アプリを検索します。

:::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="サンプルのエンタープライズ アプリケーションを示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

アプリを選びます。 アプリケーション ID とオブジェクト ID の例は次のとおりです。

:::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="エンタープライズ アプリケーションのアプリケーション ID とオブジェクト ID を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

テナント ID は、Microsoft Azure AD の概要ページで確認できます。

:::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="テナント ID を確認できる場所を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

プリンシパルのテナント ID は、場所によってプリンシパル ID、SPN、オブジェクト ID と呼ばれることもあります。 Azure AD のテナント ID の値は GUID とよく似ており、`11111111-1111-1111-1111-111111111111` の形式になっています。

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>SPN に割り当てることができるアクセス許可

次の手順では、Azure AD アプリに対し、EA ロールを使用したアクションを実行するためのアクセス許可を付与します。 SPN には、次のロールのみ割り当てることができます。 ここに示したロール定義 ID は、後の割り当ての段階でそのまま使用します。

| ロール | 許可されるアクション | ロール定義 ID |
| --- | --- | --- |
| EnrollmentReader | あらゆるアカウントとサブスクリプションの使用状況と料金を確認できます。 登録に関連付けられている Azure 前払い (旧称: 年額コミットメント) の残高を確認できます。 | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| DepartmentReader | 管理している部署について、使用状況の詳細をダウンロードできます。 自分の部署に関連付けられている使用状況と料金を確認できます。 | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | 一定範囲のアカウントで、新しいサブスクリプションを作成できます。 | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- SPN に登録リーダーを割り当てることができるのは、登録ライター ロールのユーザーのみです。
- SPN に部署リーダーを割り当てることができるのは、登録ライター ロールまたは部署ライター ロールが割り当てられているユーザーのみです。
- SPN にサブスクリプション作成者ロールを割り当てることができるのは、登録アカウントのアカウント所有者となっているユーザーのみです。

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>SPN に登録アカウントのロールのアクセス許可を割り当てる

REST API に関する記事「[ロールの割り当て - Put](/rest/api/billing/2019-10-01-preview/roleassignments/put)」をお読みください。

記事を読んでいる間に、 **[使ってみる]** を選択し、SPN の使用を開始します。

:::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Put に関する記事の中の [使ってみる] オプションを示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

自分のアカウントを使用して、アクセス権を割り当てたい登録にアクセスできるテナントにサインインします。

API 要求の一環として、次のパラメーターを指定します。

**billingAccountName**

このパラメーターは、課金アカウントの ID です。 これは、Azure portal の Cost Management + Billing の概要ページにあります。

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="課金アカウント ID を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

このパラメーターは一意の GUID であり、指定が必要です。 GUID は、PowerShell コマンド [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) を使って生成できます。

このほか、[Online GUID / UUID Generator](https://guidgenerator.com/) Web サイトを使って一意の GUID を生成することもできます。

**api-version**

**2019-10-01-preview** バージョンを使用してください。

要求本文では、JSON コードを使用する必要があります。

[「ロールの割り当て - Put」の「例」](/rest/api/billing/2019-10-01-preview/roleassignments/put#examples)のところにある要求本文のサンプルを使用します。

JSON の中で使用する必要があるパラメーターは 3 つあります。

| パラメーター | 参照先 |
| --- | --- |
| properties.principalId | 「[SPN と Tenant ID を特定する](#find-your-spn-and-tenant-id)」を参照してください。 |
| properties.principalTenantId | 「[SPN と Tenant ID を特定する](#find-your-spn-and-tenant-id)」を参照してください。 |
| properties.roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e" |

課金アカウント名は、API のパラメーターで使用したパラメーターと同じです。 これは、EA Portal と Azure portal で確認できる登録 ID です。

`24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` は請求先のロール定義 ID で、登録リーダーのものである点にご注意ください。

**[実行]** を選択してコマンドを起動します。

:::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="「ロールの割り当て - Put」の例の [使ってみる] を選択し、サンプルの情報を入力して [実行] の準備が整っている状態を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

`200 OK` という応答は、SPN が正常に追加されたことを示します。

これで、自動的に SPN (オブジェクト ID を備えた Azure AD アプリ) を使用して EA API にアクセスできるようになりました。 SPN には EnrollmentReader ロールが割り当てられています。

## <a name="assign-the-department-reader-role-to-the-spn"></a>SPN に部署リーダー ロールを割り当てる

始める前に、REST API に関する記事「[登録部署のロールの割り当て - Put](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put)」をお読みください。

記事を読んでいる間に、 **[使ってみる]** を選択します。

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="「登録部署のロールの割り当て - Put」の記事の [使ってみる] オプションを示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

自分のアカウントを使用して、アクセス権を割り当てたい登録にアクセスできるテナントにサインインします。

API 要求の一環として、次のパラメーターを指定します。

**billingAccountName**

課金アカウント ID です。 これは、Azure portal の Cost Management + Billing の概要ページにあります。

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="課金アカウント ID を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

このパラメーターは一意の GUID であり、指定が必要です。 GUID は、PowerShell コマンド [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) を使って生成できます。

このほか、[Online GUID / UUID Generator](https://guidgenerator.com/) Web サイトを使って一意の GUID を生成することもできます。

**departmentName**

部署 ID です。 部署 ID は、Azure portal で確認できます。 Cost Management + Billing の **[部署]** に移動します。

この例では、ACE 部署を使用しました。 この例の ID は `84819` です。

:::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="部署 ID の例を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

**api-version**

**2019-10-01-preview** バージョンを使用してください。

要求本文では、JSON コードを使用する必要があります。

「[登録部署のロールの割り当て - Put](/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put)」にあるサンプルを使用します。 JSON の中で使用する必要があるパラメーターは 3 つあります。

| パラメーター | 参照先 |
| --- | --- |
| properties.principalId | 「[SPN と Tenant ID を特定する](#find-your-spn-and-tenant-id)」を参照してください。 |
| properties.principalTenantId | 「[SPN と Tenant ID を特定する](#find-your-spn-and-tenant-id)」を参照してください。 |
| properties.roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a" |

課金アカウント名は、API のパラメーターで使用したパラメーターと同じです。 これは、EA Portal と Azure portal で確認できる登録 ID です。

請求先のロール定義 ID `db609904-a47f-4794-9be8-9bd86fbffd8a` は、部署リーダーのものです。

**[実行]** を選択してコマンドを起動します。

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="「登録部署のロールの割り当て - Put」で REST の例の [使ってみる] を選択し、サンプルの情報を入力して [実行] の準備が整っている状態を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

`200 OK` という応答は、SPN が正常に追加されたことを示します。

これで、自動的に SPN (オブジェクト ID を備えた Azure AD アプリ) を使用して EA API にアクセスできるようになりました。 SPN には DepartmentReader ロールが割り当てられています。

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>SPN にサブスクリプション作成者ロールを割り当てる

記事「[登録アカウントのロールの割り当て - Put](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put)」をお読みください。

読んでいる間に、 **[使ってみる]** を選択して、SPN にサブスクリプション作成者ロールを割り当てます。

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="「登録アカウントのロールの割り当て - Put」の記事の [使ってみる] オプションを示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

自分のアカウントを使用して、アクセス権を割り当てたい登録にアクセスできるテナントにサインインします。

API 要求の一環として、次のパラメーターを指定します。 記事[「登録アカウントのロールの割り当て - Put」の「URI パラメーター」](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put#uri-parameters)をお読みください。

**billingAccountName**

このパラメーターは、課金アカウントの ID です。 これは、Azure portal の Cost Management + Billing の概要ページにあります。

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="課金アカウント ID を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

このパラメーターは一意の GUID であり、指定が必要です。 GUID は、PowerShell コマンド [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid) を使って生成できます。

このほか、[Online GUID / UUID Generator](https://guidgenerator.com/) Web サイトを使って一意の GUID を生成することもできます。
**enrollmentAccountName**

このパラメーターは、アカウント ID です。 Azure portal の Cost Management + Billing のところでスコープを登録と部署にして、アカウント名に対応したアカウント ID を探します。

この例では、GTM テストアカウントを使用しました。 ID は `196987` です。

:::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="アカウント ID を示したスクリーンショット。" lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

**api-version**

**2019-10-01-preview** バージョンを使用してください。

要求本文では、JSON コードを使用する必要があります。

[「登録部署のロールの割り当て - Put」の「例」](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put#putenrollmentdepartmentadministratorroleassignment)のところにあるサンプルを使用します。

JSON の中で使用する必要があるパラメーターは 3 つあります。

| パラメーター | 参照先 |
| --- | --- |
| properties.principalId | 「[SPN と Tenant ID を特定する](#find-your-spn-and-tenant-id)」を参照してください。 |
| properties.principalTenantId | 「[SPN と Tenant ID を特定する](#find-your-spn-and-tenant-id)」を参照してください。 |
| properties.roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71" |

課金アカウント名は、API のパラメーターで使用したパラメーターと同じです。 これは、EA Portal と Azure portal で確認できる登録 ID です。

請求先のロール定義 ID `a0bcee42-bf30-4d1b-926a-48d21664ef71` は、サブスクリプション作成者ロールのものです。

**[実行]** を選択してコマンドを起動します。

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="「登録アカウントのロールの割り当て - Put」の記事の [使ってみる] オプションを示したスクリーンショット" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

`200 OK` という応答は、SPN が正常に追加されたことを示します。

これで、自動的に SPN (オブジェクト ID を備えた Azure AD アプリ) を使用して EA API にアクセスできるようになりました。 SPN には SubscriptionCreator ロールが割り当てられています。

## <a name="next-steps"></a>次の手順

- [Azure EA Portal の管理](ea-portal-administration.md)について学習します。