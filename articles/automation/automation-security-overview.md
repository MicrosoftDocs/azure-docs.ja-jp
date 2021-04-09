---
title: Azure Automation アカウントの認証の概要
description: この記事では、Azure Automation アカウントの認証について概要を紹介します。
keywords: Automation のセキュリティ, セキュリティで保護された Automation; Automation の認証
services: automation
ms.subservice: process-automation
ms.date: 02/26/2021
ms.topic: conceptual
ms.openlocfilehash: c559a81b17b92f48b2d51b7c2d26325d6a1b1cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708902"
---
# <a name="automation-account-authentication-overview"></a>Automation アカウントの認証の概要

Azure Automation を使用すると、Azure 内のリソース、オンプレミスのリソース、Amazon Web Services (AWS) などの他のクラウド プロバイダーのリソースに対するタスクを自動化できます。 タスクの自動化には Runbook を使用できるほか、Azure 以外で管理する必要があるビジネスや運用プロセスがあれば Hybrid Runbook Worker を使用することもできます。 どの環境においても、必要な最低限の権限だけで操作対象のリソースに安全にアクセスするためのアクセス許可が必要です。

この記事では、Azure Automation でサポートされる認証シナリオを紹介すると共に、基本的な取り組み方を管理対象の環境ごとに説明します。

## <a name="automation-account"></a>Automation アカウント

Azure Automation を初めて開始するときに、少なくとも 1 つの Automation アカウントを作成する必要があります。 Automation アカウントを使用すると、他のアカウントのリソースから Automation のリソース、Runbook、資産、構成を分離できます。 Automation アカウントを使用して、リソースを個別の論理環境または委任された役割に分けることができます。 たとえば、開発環境用、本番環境用、オンプレミス環境用に、それぞれ異なるアカウントを使用できます。 または、[Update Management](update-management/overview.md) を使用して、すべてのコンピューターでオペレーティング システムの更新プログラムを管理するように 1 つの Automation アカウントを専用にすることもできます。 

Azure Automation アカウントは、Microsoft アカウントや、Azure サブスクリプションで作成されたアカウントとは異なります。 Automation アカウントの作成の概要については、「[Automation アカウントを作成する](automation-quickstart-create-account.md)」を参照してください。

## <a name="automation-resources"></a>Automation リソース

各 Automation アカウントの Automation リソースは単一の Azure リージョンと関連付けられていますが、Azure サブスクリプション内のリソースはすべて Automation アカウントで管理することができます。 異なるリージョンで Automation アカウントを作成する主な理由としては、特定のリージョンに分離しなければならないデータやリソースを必要とするポリシーがある場合が挙げられます。

Azure Automation で Azure Resource Manager と PowerShell コマンドレットを使用してリソースに対して作成するすべてのタスクは、Azure Active Directory (Azure AD) の組織 ID 資格情報に基づく認証を使用して、Azure に対する認証を行う必要があります。

## <a name="run-as-accounts"></a>実行アカウント

Azure Automation の実行アカウントを使用すると、Azure Resource Manager リソースまたはクラシック デプロイ モデルにデプロイされたリソースを管理するための認証を行うことができるようになります。 Azure Automation には、次の 2 種類の実行アカウントがあります。

* Azure 実行アカウント: Azure リソースは、Azure の Azure Resource Manager デプロイと管理サービスに基づいて管理できます。
* Azure クラシック実行アカウント:クラシック デプロイ モデルに基づいて Azure クラシック リソースを管理できます。

Azure Resource Manager とクラシック デプロイ モデルの詳細については、[Azure Resource Manager とクラシック デプロイの比較](../azure-resource-manager/management/deployment-models.md)に関するページを参照してください。

>[!NOTE]
>Azure Cloud Solution Provider (CSP) サブスクリプションでは、Azure Resource Manager モデルのみがサポートされます。 Azure Resource Manager 以外のサービスは、プログラムでは使用できません。 CSP サブスクリプションを使用する場合、Azure クラシック実行アカウントは作成されませんが、Azure 実行アカウントは作成されます。 CSP サブスクリプションの詳細については、[CSP サブスクリプションで利用可能なサービス](/azure/cloud-solution-provider/overview/azure-csp-available-services)に関するページを参照してください。

Automation アカウントを作成するとき、Azure 実行アカウントは既定では同時に作成されます。 Automation アカウントと共に作成しないことを選択した場合は、後で個別に作成できます。 Azure クラシック実行アカウントはオプションです。クラシック リソースを管理する必要がある場合は、個別に作成されます。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>実行アカウント

実行アカウントを作成すると、次のタスクが実行されます。

* 自己署名証明書を含む Azure AD アプリケーションを作成し、このアプリケーションの Azure AD におけるサービス プリンシパル アカウントを作成します。また、現在のサブスクリプションにおける、このアカウントの[共同作成者](../role-based-access-control/built-in-roles.md#contributor)ロールが割り当てられます。 証明書の設定は、[閲覧者](../role-based-access-control/built-in-roles.md#reader)や他の任意のロールに変更できます。 詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。

* 指定された Automation アカウントに `AzureRunAsCertificate` という名前の Automation 証明書資産を作成します。 証明書資産には、Azure AD アプリケーションで使用される証明書の秘密キーが保持されます。

* 指定された Automation アカウントに `AzureRunAsConnection` という名前の Automation 接続資産を作成します。 この接続資産には、アプリケーション ID、テナント ID、サブスクリプション ID、証明書の拇印が格納されます。

### <a name="azure-classic-run-as-account"></a>Azure クラシック実行アカウント

Azure クラシック実行アカウントを作成すると、次のタスクが実行されます。

> [!NOTE]
> この種類の実行アカウントを作成または更新するには、サブスクリプションの共同管理者である必要があります。

* サブスクリプションに管理証明書を作成します。

* 指定された Automation アカウントに `AzureClassicRunAsCertificate` という名前の Automation 証明書資産を作成します。 この証明書資産には、管理証明書によって使用される証明書の秘密キーが格納されます。

* 指定された Automation アカウントに `AzureClassicRunAsConnection` という名前の Automation 接続資産を作成します。 この接続資産には、サブスクリプション名、サブスクリプション ID、および証明書資産名が保持されます。

## <a name="service-principal-for-run-as-account"></a>実行アカウント用のサービス プリンシパル

実行アカウントのサービス プリンシパルには、既定では、Azure AD を読み取るためのアクセス許可がありません。 Azure AD の読み取り、または管理を行うためのアクセス許可を追加する場合は、 **[API のアクセス許可]** の下でサービス プリンシパルにそのアクセス許可を付与する必要があります。 詳細については、「[Web API にアクセスするためのアクセス許可を追加する](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)」を参照してください。

## <a name="run-as-account-permissions"></a><a name="permissions"></a>実行アカウントのアクセス許可

このセクションでは、通常の実行アカウントとクラシック実行アカウントの両方に対するアクセス許可を定義します。

* 実行アカウントを作成または更新するには、Azure Active Directory のアプリケーション管理者とサブスクリプションの所有者がすべてのタスクを完了する必要があります。
* クラシック実行アカウントを構成または更新するには、サブスクリプション レベルの共同管理者ロールが必要です。 クラシック サブスクリプション アクセス許可の詳細については、「[Azure の従来のサブスクリプション管理者](../role-based-access-control/classic-administrators.md#add-a-co-administrator)」を参照してください。

職務が分離されている状況について、次の表にタスク、同等のコマンドレット、必要なアクセス許可の一覧を示します。

|タスク|コマンドレット  |最小限のアクセス許可  |アクセス許可を設定する場所|
|---|---------|---------|---|
|Azure AD アプリケーションを作成する|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | アプリケーション開発者ロール<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>[ホーム] > [Azure AD] > [アプリの登録] |
|資格情報をアプリケーションに追加します。|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | アプリケーション管理者または全体管理者<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>[ホーム] > [Azure AD] > [アプリの登録]|
|Azure AD サービス プリンシパルを作成および取得する|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | アプリケーション管理者または全体管理者<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>[ホーム] > [Azure AD] > [アプリの登録]|
|指定されたプリンシパルの Azure ロールを割り当てるまたは取得する|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | ユーザー アクセス管理者または所有者、あるいは次のアクセス許可がある:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [サブスクリプション](../role-based-access-control/role-assignments-portal.md)</br>[ホーム] > [サブスクリプション] > [\<subscription name\> - アクセス制御 (IAM)]|
|Automation の証明書を作成または削除する|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | リソース グループの共同作成者         |Automation アカウント リソース グループ|
|Automation の接続を作成または削除する|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|リソース グループの共同作成者 |Automation アカウント リソース グループ|

<sup>1</sup> **[ユーザー設定]** ページで Azure AD テナントの **[ユーザーはアプリケーションを登録できる]** オプションが **[はい]** に設定されている場合は、Azure AD テナントの管理者以外のユーザーが [AD アプリケーションを登録することができます](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)。 アプリケーション登録設定が **[いいえ]** の場合は、この操作を行うユーザーがこの表で定義されている必要があります。

サブスクリプションの Active Directory インスタンスのメンバーになっていない状態で、サブスクリプションの全体管理者ロールに追加された場合は、ゲストとして追加されます。 このような状況では、`You do not have permissions to create…` 警告が **[Add Automation Account]\(Automation アカウントの追加\)** ページに表示されます。

エラー メッセージが生成される状況が解決されたことを確認するには、次のようにします。

1. Azure portal の Azure Active Directory ペインで、 **[ユーザーとグループ]** を選択します。
2. **[すべてのユーザー]** を選択します。
3. ご自分の名前を選んでから、 **[プロファイル]** を選択します。
4. ユーザーのプロファイルの下にある **[ユーザーの種類]** 属性の値が、確実に **[ゲスト]** には設定されていないようにします。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

ロールベースのアクセス制御は、Azure AD ユーザー アカウントおよび実行アカウントに対して許可されたアクションを付与し、そのサービス プリンシパルを認証するために、Azure Resource Manager で使用できます。 Automation アクセス許可を管理するためのモデルの開発に役立つ詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。

リソース グループのアクセス許可の割り当てに対して厳密なセキュリティ制御がある場合は、実行アカウントのメンバーシップをリソース グループの **共同作成者** ロールに割り当てる必要があります。

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Hybrid Runbook Worker を使用した Runbook の認証

データセンター内の Hybrid Runbook Worker で、または AWS などの他のクラウド環境内にあるコンピューティング サービスに対して実行される Runbook は、Runbook が Azure のリソースを認証するために通常使用される方法と同じものを使用することはできません。 これは、これらのリソースは Azure の外部で実行しており、そのため、ローカルにアクセスするリソースに対する認証を行うには Automation で定義されている個別のセキュリティ資格情報を必要とするためです。 runbook worker を使用した Runbook の認証について詳しくは、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照してください。

Azure VM 上で Hybrid Runbook Worker を使用する Runbook の場合は、実行アカウントの代わりに[マネージド ID による Runbook の認証](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)を使用して Azure リソースに対して認証できます。

## <a name="next-steps"></a>次のステップ

* Azure portal からの Automation アカウントの作成については、「[スタンドアロン Azure Automation アカウントを作成する](automation-create-standalone-account.md)」を参照してください。
* テンプレートを使用してアカウントを作成したい場合は、「[Azure Resource Manager テンプレートを使用して Automation アカウントを作成する](quickstart-create-automation-account-template.md)」を参照してください。
* アマゾン ウェブ サービスを使用した認証については、[アマゾン ウェブ サービスによる Runbook の認証](automation-config-aws-account.md)に関するページを参照してください。