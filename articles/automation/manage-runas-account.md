---
title: Azure Automation の実行アカウントを管理する
description: この記事では、PowerShell または Azure portal を使用して、実行アカウントを管理する方法について説明します。
services: automation
ms.subservice: shared-capabilities
ms.date: 06/26/2020
ms.topic: conceptual
ms.openlocfilehash: c2d6e026f87211260a2cf45c0623806cc024b44e
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87530668"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Azure Automation の実行アカウントを管理する

Azure Automation の実行アカウントでは、Azure コマンドレットを使用して Azure のリソースを管理するための認証が提供されます。 実行アカウントを作成すると、新しいサービス プリンシパル ユーザーが Azure Active Directory (AD) に作成され、サブスクリプション レベルでこのユーザーに共同作成者ロールが割り当てられます。

## <a name="types-of-run-as-accounts"></a>実行アカウントの種類

Azure Automation では、次の 2 種類の実行アカウントが使用されます。

* Azure 実行アカウント
* Azure クラシック実行アカウント

>[!NOTE]
>Azure Cloud Solution Provider (CSP) サブスクリプションでは、Azure Resource Manager モデルのみがサポートされます。 Azure Resource Manager 以外のサービスは、プログラムでは使用できません。 CSP サブスクリプションを使用する場合、Azure クラシック実行アカウントは作成されませんが、Azure 実行アカウントは作成されます。 CSP サブスクリプションの詳細については、[CSP サブスクリプションで利用可能なサービス](/azure/cloud-solution-provider/overview/azure-csp-available-services)に関するページを参照してください。

実行アカウントのサービス プリンシパルには、既定では、Azure AD を読み取るためのアクセス許可はありません。 Azure AD の読み取り、または管理を行うためのアクセス許可を追加する場合は、 **[API のアクセス許可]** の下でサービス プリンシパルにそのアクセス許可を付与する必要があります。 詳細については、「[Web API にアクセスするためのアクセス許可を追加する](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)」を参照してください。

### <a name="run-as-account"></a>実行アカウント

実行アカウントでは、[Resource Manager デプロイ モデル](../azure-resource-manager/management/deployment-models.md) リソースを管理します。 次のタスクが行われます。

* 自己署名証明書を含む Azure AD アプリケーションを作成し、このアプリケーションの Azure AD におけるサービス プリンシパル アカウントを作成します。また、現在のサブスクリプションにおける、このアカウントの共同作成者ロールが割り当てられます。 証明書の設定は、所有者や他の任意のロールに変更できます。 詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。
  
* 指定された Automation アカウントに `AzureRunAsCertificate` という名前の Automation 証明書資産を作成します。 証明書資産には、Azure AD アプリケーションで使用される証明書の秘密キーが保持されます。
  
* 指定された Automation アカウントに `AzureRunAsConnection` という名前の Automation 接続資産を作成します。 この接続資産には、アプリケーション ID、テナント ID、サブスクリプション ID、証明書の拇印が格納されます。

### <a name="azure-classic-run-as-account"></a>Azure クラシック実行アカウント

Azure クラシック実行アカウントでは、[クラシック デプロイ モデル](../azure-resource-manager/management/deployment-models.md) リソースを管理します。 この種類のアカウントを作成または更新するには、サブスクリプションの共同管理者である必要があります。

Azure クラシック実行アカウントでは、次のタスクが行われます。

  * サブスクリプションに管理証明書を作成します。

  * 指定された Automation アカウントに `AzureClassicRunAsCertificate` という名前の Automation 証明書資産を作成します。 この証明書資産には、管理証明書によって使用される証明書の秘密キーが格納されます。

  * 指定された Automation アカウントに `AzureClassicRunAsConnection` という名前の Automation 接続資産を作成します。 この接続資産には、サブスクリプション名、サブスクリプション ID、および証明書資産名が保持されます。

>[!NOTE]
>Automation アカウントを作成するとき、Azure クラシック実行アカウントは既定では同時に作成されません。 このアカウントは、この記事の後半で説明する手順に従って個別に作成します。

## <a name="obtain-run-as-account-permissions"></a><a name="permissions"></a>実行アカウントのアクセス許可を取得する

このセクションでは、通常の実行アカウントとクラシック実行アカウントの両方に対するアクセス許可を定義します。

### <a name="get-permissions-to-configure-run-as-accounts"></a>実行アカウントを構成するためのアクセス許可を取得する

実行アカウントを作成または更新するには、特定の特権およびアクセス許可が必要です。 Azure Active Directory のアプリケーション管理者とサブスクリプションの所有者は、すべてのタスクを完了できます。 職務が分離されている状況について、次の表にタスク、同等のコマンドレット、必要なアクセス許可の一覧を示します。

|タスク|コマンドレット  |最小限のアクセス許可  |アクセス許可を設定する場所|
|---|---------|---------|---|
|Azure AD アプリケーションを作成する|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | アプリケーション開発者ロール<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>[ホーム] > [Azure AD] > [アプリの登録] |
|資格情報をアプリケーションに追加します。|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | アプリケーション管理者または全体管理者<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>[ホーム] > [Azure AD] > [アプリの登録]|
|Azure AD サービス プリンシパルを作成および取得する|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | アプリケーション管理者または全体管理者<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>[ホーム] > [Azure AD] > [アプリの登録]|
|指定されたプリンシパルの Azure ロールを割り当てるまたは取得する|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | ユーザー アクセス管理者または所有者、あるいは次のアクセス許可がある:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [サブスクリプション](../role-based-access-control/role-assignments-portal.md)</br>[ホーム] > [サブスクリプション] > [\<subscription name\> - アクセス制御 (IAM)]|
|Automation の証明書を作成または削除する|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | リソース グループの共同作成者         |Automation アカウント リソース グループ|
|Automation の接続を作成または削除する|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|リソース グループの共同作成者 |Automation アカウント リソース グループ|

<sup>1</sup>[ユーザー設定] ページで Azure AD テナントの **[ユーザーはアプリケーションを登録できる]** オプションが **[はい]** に設定されている場合は、Azure AD テナントの管理者以外のユーザーが [AD アプリケーションを登録する](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)ことができます。 アプリケーション登録設定が **[いいえ]** の場合は、この操作を行うユーザーがこの表で定義されている必要があります。

サブスクリプションの Active Directory インスタンスのメンバーになっていない状態で、サブスクリプションの全体管理者ロールに追加された場合は、ゲストとして追加されます。 このような状況では、`You do not have permissions to create…` 警告が **[Add Automation Account]\(Automation アカウントの追加\)** ページに表示されます。

グローバル管理者ロールが割り当てられたときにサブスクリプションの Active Directory インスタンスのメンバーである場合でも、 **[Automation アカウントの追加]** ページに `You do not have permissions to create…` 警告が表示されることがあります。 この場合は、サブスクリプションの Active Directory インスタンスからの削除を要求してから、再追加するように要求し、Active Directory で完全なユーザーになるようにすることができます。

エラー メッセージが生成される状況が解決されたことを確認するには、次のようにします。

1. Azure portal の Azure Active Directory ペインで、 **[ユーザーとグループ]** を選択します。
2. **[すべてのユーザー]** を選択します。
3. ご自分の名前を選んでから、 **[プロファイル]** を選択します。 
4. ユーザーのプロファイルの下にある **[ユーザーの種類]** 属性の値が、確実に **[ゲスト]** には設定されていないようにします。

### <a name="get-permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>クラシック実行アカウントを構成するためのアクセス許可を取得する

クラシック実行アカウントを構成または更新するには、サブスクリプション レベルの共同管理者ロールが必要です。 クラシック サブスクリプション アクセス許可の詳細については、「[Azure の従来のサブスクリプション管理者](../role-based-access-control/classic-administrators.md#add-a-co-administrator)」を参照してください。

## <a name="create-a-run-as-account-in-azure-portal"></a>Azure portal で実行アカウントを作成する

以下の手順を行って、Azure portal で Azure Automation アカウントを更新します。 実行およびクラシック実行アカウントを個別に作成します。 クラシック リソースを管理する必要がない場合は、Azure 実行アカウントのみを作成できます。

1. サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用して、Azure ポータルにログインします。

2. **Automation アカウント**を検索して選択します。

3. [Automation アカウント] ページで、一覧から Automation アカウントを選択します。

4. 左ペインで、アカウントの設定セクションの **[実行アカウント]** を選択します。

5. 必要なアカウントに応じて、 **[Azure 実行アカウント]** または **[Azure クラシック実行アカウント]** を選択します。 

6. 関心のあるアカウントに応じて、 **[Azure 実行アカウントを追加する]** または **[Azure クラシック実行アカウントを追加する]** ペインを使用します。 概要情報を確認した後、 **[作成]** クリックします。

7. Azure によって実行アカウントが作成されている間、メニューの **[通知]** で進行状況を追跡できます。 アカウントが作成されていることを示すバナーも表示されます。 プロセスが完了するまでに数分かかることがあります。

## <a name="delete-a-run-as-or-classic-run-as-account"></a>実行アカウントまたはクラシック実行アカウントの削除

このセクションでは、実行またはクラシック実行アカウントを削除する方法について説明します。 この操作を実行するとき、Automation アカウントが保持されます。 アカウントを削除した後、Azure portal でそれを再作成することができます。

1. Azure Portal で Automation アカウントを開きます。

2. 左ペインで、アカウントの設定セクションの **[実行アカウント]** を選択します。

3. 実行アカウントのプロパティ ページで、削除する実行アカウントまたはクラシック実行アカウントを選択します。 

4. 選択したアカウントの [プロパティ] ペインで、 **[削除]** をクリックします。

   ![Azure 実行アカウントを削除する](media/manage-runas-account/automation-account-delete-runas.png)

5. アカウントが削除されている間、メニューの **[通知]** で進行状況を追跡できます。

6. アカウントが削除された後、実行アカウントのプロパティ ページでそれを再作成することができます。その場合、作成オプションの **[Azure 実行アカウント]** を選択します。

   ![Automation 実行アカウントの再作成](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>自己署名証明書を更新する

実行アカウント用に作成した自己署名証明書は、作成日から 1 年後に有効期限が切れます。 実行アカウントの有効期限が切れる前のある時点で、証明書を更新する必要があります。 有効期限が切れる前にいつでも更新することができます。 

自己署名証明書を更新するときに、現在有効な証明書は保持されます。これにより、キューに登録されているかアクティブに実行されていて、実行アカウントで認証される Runbook は確実に悪影響を受けることがなくなります。 証明書は、有効期限日を迎えるまで存在し続けます。

>[!NOTE]
>実行アカウントが侵害されていると思われる場合は、自己署名証明書を削除してから再作成することができます。

>[!NOTE]
>エンタープライズ証明機関によって発行された証明書を使用するように実行アカウントを構成している場合に、自己署名証明書を更新するオプションを使用すると、エンタープライズ証明書は自己署名証明書に置き換えられます。

自己署名証明書を更新するには、次の手順を使用します。

1. Azure Portal で Automation アカウントを開きます。

1. アカウントの設定セクションで、 **[実行アカウント]** を選択します。

    ![Automation アカウントのプロパティ ウィンドウ](media/manage-runas-account/automation-account-properties-pane.png)

1. 実行アカウントのプロパティ ページで、証明書を更新する実行アカウントまたはクラシック実行アカウントを選択します。

1. 選択したアカウントのプロパティ ペインで、 **[証明書の更新]** をクリックします。

    ![実行アカウントの証明書を書き換える](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. 証明書が書き換えられている間、メニューの **[通知]** で進行状況を追跡できます。

## <a name="limit-run-as-account-permissions"></a>実行アカウントのアクセス許可を制限する

Azure のリソースに対する Automation のターゲット設定を制御するために、[Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) スクリプトを実行できます。 このスクリプトでは、カスタム ロールの定義を作成して使用するために、既存の実行アカウント サービス プリンシパルを変更します。 このロールには、[Key Vault](../key-vault/index.yml) を除くすべてのリソースへのアクセス許可があります。

>[!IMPORTANT]
>**Update-AutomationRunAsAccountRoleAssignments.ps1** スクリプトを実行した後、実行アカウントを使用して Key Vault にアクセスする Runbook が動作しなくなります。 スクリプトを実行する前に、アカウント内の Runbook で Azure Key Vault の呼び出しを確認する必要があります。 Azure Automation Runbook から Key Vault へのアクセスを有効にするには、[実行アカウントを Key Vault のアクセス許可に追加する](#add-permissions-to-key-vault)必要があります。

実行サービス プリンシパルで実行できる内容をさらに制限する必要がある場合は、カスタム ロール定義の `NotActions` 要素に他のリソースの種類を追加できます。 次の例では、`Microsoft.Compute/*` へのアクセスが制限されます。 このリソースの種類をロール定義の `NotActions` に追加すると、そのロールではどのコンピューティング リソースにもアクセスできなくなります。 ロール定義の詳細については、「[Azure リソースのロール定義の概要](../role-based-access-control/role-definitions.md)」を参照してください。

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

実行アカウントで使用されるサービス プリンシパルが、共同作成者とカスタムのどちらのロールの定義にあるかを確認できます。 

1. Automation アカウントに移動し、アカウントの設定セクションで **[実行アカウント]** を選択します。
2. **[Azure 実行アカウント]** を選択します。 
3. **[ロール]** を選択し、使用されているロールの定義を見つけます。

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="実行アカウント ロールを検証します。" lightbox="media/manage-runas-account/verify-role-expanded.png":::


また、複数のサブスクリプションまたは Automation アカウントの実行アカウントで使用されるロールの定義を確認することができます。 これを行うには、PowerShell ギャラリーの [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) スクリプトを使用します。

### <a name="add-permissions-to-key-vault"></a>Key Vault へのアクセス許可を追加する

Key Vault と実行アカウントのサービス プリンシパルでカスタム ロールの定義が使用されているかどうかを、Azure Automation で確認できるようにすることができます。 次の手順が必要です。

* Key Vault にアクセス許可を付与します。
* アクセス ポリシーを設定します。

PowerShell ギャラリーの [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) スクリプトを使用して、実行アカウントに Key Vault へのアクセス許可を付与することができます。 Key Vault へのアクセス許可の設定について詳しくは、[アプリケーションに対する Key Vault へのアクセス許可の付与](../key-vault/general/group-permissions-for-apps.md)に関するページを参照してください。

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>実行アカウントの間違った構成に関する問題を解決する

実行またはクラシック実行アカウントに必要な構成項目の一部が削除されたか、初期セットアップ中に正しく作成されなかった可能性があります。 構成が間違っている可能性のあるインスタンスは、次のとおりです。

* 証明書資産
* 接続資産
* 共同作成者ロールから削除された実行アカウント
* Azure AD のサービス プリンシパルまたはアプリケーション

このような構成が間違っているインスタンスについては、Automation アカウントによって変更が検出され、そのアカウントの実行アカウントのプロパティ ペインに [*Incomplete*]\(不完全\) の状態が表示されます。

![実行アカウントの構成が不完全な状態](media/manage-runas-account/automation-account-runas-incomplete-config.png)

実行アカウントを選択すると、アカウントのプロパティ ペインに次のエラー メッセージが表示されます。

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

こうした実行アカウントの問題は、アカウントを削除してから再作成すればすぐに解決できます。

## <a name="next-steps"></a>次のステップ

* [アプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)。
* [Azure Cloud Services の証明書の概要](../cloud-services/cloud-services-certs-create.md)。
