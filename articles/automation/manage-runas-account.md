---
title: Azure Automation の実行アカウントを管理する
description: この記事では、PowerShell または Azure portal を使用して、実行アカウントを管理する方法について説明します。
services: automation
ms.subservice: ''
ms.date: 01/19/2021
ms.topic: conceptual
ms.openlocfilehash: f170fc948f136f4f46634e7ae2645ed2eb357afa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101096461"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Azure Automation の実行アカウントを管理する

Azure Automation の実行アカウントでは、Automation の Runbook やその他の自動化機能を使用し、Azure Resource Manager または Azure クラシック デプロイ モデルのリソースを管理する認証を提供します。 この記事では、実行アカウントまたはクラシック実行アカウントを管理する方法について説明します。

自動化シナリオの処理に関連する Azure Automation アカウントの認証と説明については、「[Automation アカウントの認証の概要](automation-security-overview.md)」を参照してください。

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>自己署名証明書を更新する

実行アカウント用に作成した自己署名証明書は、作成日から 1 年後に有効期限が切れます。 実行アカウントの有効期限が切れる前のある時点で、証明書を更新する必要があります。 有効期限が切れる前にいつでも更新することができます。

自己署名証明書を更新するときに、現在有効な証明書は保持されます。これにより、キューに登録されているかアクティブに実行されていて、実行アカウントで認証される Runbook は確実に悪影響を受けることがなくなります。 証明書は、有効期限日を迎えるまで存在し続けます。

>[!NOTE]
>実行アカウントが侵害されていると思われる場合は、自己署名証明書を削除してから再作成することができます。

>[!NOTE]
>エンタープライズ証明機関によって発行された証明書を使用するように実行アカウントを構成している場合に、自己署名証明書を更新するオプションを使用すると、エンタープライズ証明書は自己署名証明書に置き換えられます。

自己署名証明書を更新するには、次の手順を使用します。

1. [Azure ポータル](https://portal.azure.com)にサインインします。

1. Automation アカウントに移動し、アカウントの設定セクションで **[実行アカウント]** を選択します。

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Automation アカウントのプロパティ ウィンドウ。":::

1. **[実行アカウント]** プロパティ ページで、証明書を更新する必要があるアカウントに応じて、 **[実行アカウント]** または **[クラシック実行アカウント]** を選択します。

1. 選択したアカウントの **[プロパティ]** ページで、 **[証明書の更新]** を選択します。

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="実行アカウントの証明書を更新する。":::

1. 証明書が書き換えられている間、メニューの **[通知]** で進行状況を追跡できます。

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>実行アカウントに他のサブスクリプションのアクセス許可を付与する

Azure Automation では、1 つのサブスクリプションから 1 つの Automation アカウントを使用すること、および複数のサブスクリプション間で Azure Resource Manager リソースに対して Runbook を実行することがサポートされています。 この構成では、Azure クラシック デプロイ モデルはサポートされません。

実行アカウント サービス プリンシパルに、他のサブスクリプションの[共同作成者](../role-based-access-control/built-in-roles.md#contributor)ロール、またはより制限の厳しいアクセス許可を割り当てます。 詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。 実行アカウントを他のサブスクリプションのロールに割り当てるには、このタスクを実行するユーザー アカウントが、そのサブスクリプションの **所有者** ロールのメンバーである必要があります。

> [!NOTE]
> この構成では、共通の Azure AD テナントを使用する組織の複数のサブスクリプションのみがサポートされます。

実行アカウントにアクセス許可を付与する前に、割り当てるサービス プリンシパルの表示名をメモする必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. [Automation アカウント] で、 **[アカウントの設定]** の **[実行アカウント]** を選択します。
1. **[Azure 実行アカウント]** を選択します。
1. **[Azure 実行アカウント]** ページで **[表示名]** の値をコピーまたはメモします。

ロールの割り当てを追加する方法の詳細な手順については、使用する方法に応じて、次に関する記事を参照してください。

* [Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)
* [Azure PowerShell を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-powershell.md)
* [Azure CLI を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-cli.md)
* [REST API を使用して Azure ロールを割り当てる](..//role-based-access-control/role-assignments-rest.md)

実行アカウントをロールに割り当てた後、Runbook で `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` を指定して、使用するサブスクリプションのコンテキストを設定します。 詳細については、「[Set-AzContext](/powershell/module/az.accounts/set-azcontext)」を参照してください。

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

実行アカウントで使用されるサービス プリンシパルに、**共同作成者** ロールとカスタム ロールのどちらが割り当てられているかを確認できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Automation アカウントに移動し、アカウントの設定セクションで **[実行アカウント]** を選択します。
1. **[Azure 実行アカウント]** を選択します。
1. **[ロール]** を選択し、使用されているロールの定義を見つけます。

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="実行アカウント ロールを検証します。" lightbox="media/manage-runas-account/verify-role-expanded.png":::

また、複数のサブスクリプションまたは Automation アカウントの実行アカウントで使用されるロールの定義を確認することができます。 これを行うには、PowerShell ギャラリーの [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) スクリプトを使用します。

### <a name="add-permissions-to-key-vault"></a>Key Vault へのアクセス許可を追加する

Key Vault と実行アカウントのサービス プリンシパルでカスタム ロールの定義が使用されているかどうかを、Azure Automation で確認できるようにすることができます。 次の手順が必要です。

* Key Vault にアクセス許可を付与します。
* アクセス ポリシーを設定します。

PowerShell ギャラリーの [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) スクリプトを使用して、実行アカウントに Key Vault へのアクセス許可を付与できます。 Key Vault へのアクセス許可の設定について詳しくは、「[Key Vault アクセス ポリシーを割り当てる](../key-vault/general/assign-access-policy-powershell.md)」を参照してください。

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>実行アカウントの間違った構成に関する問題を解決する

実行またはクラシック実行アカウントに必要な構成項目の一部が削除されたか、初期セットアップ中に正しく作成されなかった可能性があります。 構成が間違っている可能性のあるインスタンスは、次のとおりです。

* 証明書資産
* 接続資産
* 共同作成者ロールから削除された実行アカウント
* Azure AD のサービス プリンシパルまたはアプリケーション

このような構成が間違っているインスタンスについては、Automation アカウントによって変更が検出され、そのアカウントの実行アカウントのプロパティ ペインに [*Incomplete*]\(不完全\) の状態が表示されます。

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="不完全な実行アカウントの構成。":::

実行アカウントを選択すると、アカウントのプロパティ ペインに次のエラー メッセージが表示されます。

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

こうした実行アカウントの問題は、実行アカウントを[削除](delete-run-as-account.md)してから[再作成](create-run-as-account.md)すればすぐに解決できます。

## <a name="next-steps"></a>次のステップ

* [アプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)。
* [Azure Cloud Services の証明書の概要](../cloud-services/cloud-services-certs-create.md)。
* 実行アカウントを作成または再作成する方法については、「[実行アカウントを作成する](create-run-as-account.md)」を参照してください。
* 実行アカウントを使用する必要がなくなった場合は、「[実行アカウントの削除](delete-run-as-account.md)」を参照してください。