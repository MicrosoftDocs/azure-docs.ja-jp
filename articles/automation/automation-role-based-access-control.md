---
title: "Azure Automation におけるロールベースのアクセス制御 | Microsoft Docs"
description: "Azure のリソースに対するアクセスは、ロールベースのアクセス制御 (RBAC) で管理できます。 この記事では、Azure Automation における RBAC の設定方法について説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
keywords: "Automation RBAC, ロールベースのアクセス制御, Azure RBAC"
ms.assetid: 04b5625e-0ee8-4b5b-85cd-7734c1b3d4a3
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: magoedte;sngun
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 17c7e410a9c5b69ab450eb3affd192f1e3cb6e76


---
# <a name="role-based-access-control-in-azure-automation"></a>Azure Automation におけるロールベースのアクセス制御
## <a name="role-based-access-control"></a>ロールベースのアクセス制御
Azure のリソースに対するアクセスは、ロールベースのアクセス制御 (RBAC) で管理できます。 [RBAC](../active-directory/role-based-access-control-configure.md)を使用して、チーム内の職務を分離し、それぞれの職務に必要なアクセス権のみをユーザー、グループ、アプリケーションに付与することができます。 ロールベースのアクセス権をユーザーに付与するには、Azure ポータル、Azure コマンドライン ツール、Azure Management API を使用します。

## <a name="rbac-in-automation-accounts"></a>Automation アカウントの RBAC
Azure Automation でアクセス権を付与するには、Automation アカウント スコープのユーザー、グループ、アプリケーションに適切な RBAC ロールを割り当てます。 Automation アカウントでは次の組み込みロールがサポートされています。  

| **ロール** | **説明** |
|:--- |:--- |
| 所有者 |Automation アカウント内のすべてのリソースおよびアクションへのアクセスは、所有者ロールによって許可されます。Automation アカウントを管理するためのアクセス権を他のユーザー、グループ、アプリケーションに付与することもできます。 |
| 共同作成者 |Automation アカウントに対する他のユーザーのアクセス許可に変更を加えることを除くすべての作業は共同作成者ロールで行うことができます。 |
| 閲覧者 |閲覧者ロールでは、Automation アカウントのすべてのリソースを表示できますが、それらに変更を加えることはできません。 |
| オートメーション オペレーター |ジョブの開始、停止、一時停止、再開、スケジュール設定などの運用タスクは、Automation オペレーター ロールで実行できます。 Automation アカウントのリソース (資格情報アセットや Runbook など) を閲覧したり改変したりできないよう保護したうえで、同じ組織のメンバーにのみ、それらの Runbook の実行を許可する必要がある場合、このロールを活用できます。 |
| ユーザーアクセスの管理者 |Azure Automation アカウントに対するユーザー アクセスは、ユーザー アクセスの管理者ロールで管理できます。 |

> [!NOTE]
> Automation アカウント内のリソースとアクションにのみアクセス権を付与することができ、特定の Runbook には付与できません。  
> 
> 

この記事では、Azure Automation における RBAC の設定方法について説明します。 ただし、まずは共同作成者、閲覧者、Automation オペレーター、ユーザー アクセスの管理者に付与される個々のアクセス許可について詳しく見ていきましょう。そうすることで、実際に Automation アカウントへのアクセス権をユーザーに付与する前に、十分な理解が得られます。  前もって理解しておかないと、意図しない結果や望ましくない結果を招くこともあります。     

## <a name="contributor-role-permissions"></a>共同作成者ロールのアクセス許可
次の表には、Automation の共同作成者ロールで実行できる特定のアクションを示します。

| **リソースの種類** | **読み取り** | **書き込み** | **削除** | **その他のアクション** |
|:--- |:--- |:--- |:--- |:--- |
| Azure Automation アカウント |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation の証明書資産 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation の接続資産 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation 接続の種類の資産 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation の資格情報資産 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation スケジュール資産 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation の変数資産 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation Desired State Configuration | | | |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |
| Hybrid Runbook Worker リソースの種類 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | |
| Azure Automation ジョブ |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |
| Automation ジョブ ストリーム |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation ジョブ スケジュール |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation モジュール |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | |
| Azure Automation Runbook |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |
| Automation Runbook のドラフト |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |
| Automation Runbook のドラフトのテスト ジョブ |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |
| Automation Webhook |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |

## <a name="reader-role-permissions"></a>閲覧者ロールのアクセス許可
次の表には、Automation の閲覧者ロールで実行できる特定のアクションを示します。

| **リソースの種類** | **読み取り** | **書き込み** | **削除** | **その他のアクション** |
|:--- |:--- |:--- |:--- |:--- |
| 従来のサブスクリプション管理者 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 管理ロック |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| アクセス許可 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| プロバイダー操作 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| ロール割り当て |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| ロール定義 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |

## <a name="automation-operator-role-permissions"></a>Automation オペレーター ロールのアクセス許可
次の表には、Automation の Automation オペレーター ロールで実行できる特定のアクションを示します。

| **リソースの種類** | **読み取り** | **書き込み** | **削除** | **その他のアクション** |
|:--- |:--- |:--- |:--- |:--- |
| Azure Automation アカウント |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation の証明書資産 | | | | |
| Automation の接続資産 | | | | |
| Automation 接続の種類の資産 | | | | |
| Automation の資格情報資産 | | | | |
| Automation スケジュール資産 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | |
| Automation の変数資産 | | | | |
| Automation Desired State Configuration | | | | |
| Hybrid Runbook Worker リソースの種類 | | | | |
| Azure Automation ジョブ |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |
| Automation ジョブ ストリーム |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation ジョブ スケジュール |![Green Status](media/automation-role-based-access-control/green-checkmark.png) |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | |
| Automation モジュール | | | | |
| Azure Automation Runbook |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation Runbook のドラフト | | | | |
| Automation Runbook のドラフトのテスト ジョブ | | | | |
| Automation Webhook | | | | |

詳細については、 [Automation オペレーターのアクション](../active-directory/role-based-access-built-in-roles.md#automation-operator) に関するセクションで、Automation アカウントとそのリソースに対して Automation オペレーター ロールでサポートされる操作の一覧を参照してください。

## <a name="user-access-administrator-role-permissions"></a>ユーザー アクセスの管理者ロールのアクセス許可
次の表には、Automation のユーザー アクセスの管理者ロールで実行できる特定のアクションを示します。

| **リソースの種類** | **読み取り** | **書き込み** | **削除** | **その他のアクション** |
|:--- |:--- |:--- |:--- |:--- |
| Azure Automation アカウント |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation の証明書資産 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation の接続資産 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation 接続の種類の資産 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation の資格情報資産 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation スケジュール資産 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation の変数資産 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation Desired State Configuration | | | | |
| Hybrid Runbook Worker リソースの種類 |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Azure Automation ジョブ |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation ジョブ ストリーム |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation ジョブ スケジュール |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation モジュール |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Azure Automation Runbook |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation Runbook のドラフト |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation Runbook のドラフトのテスト ジョブ |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation Webhook |![Green Status](media/automation-role-based-access-control/green-checkmark.png) | | | |

## <a name="configure-rbac-for-your-automation-account-using-azure-portal"></a>Azure ポータルを使用して Automation アカウントの RBAC を構成する
1. [Azure ポータル](https://portal.azure.com/) にログインし、[Automation アカウント] ブレードから、ご利用の Automation アカウントを開きます。  
2. 右上隅にある **[アクセス]** コントロールをクリックします。 表示された **[ユーザー]** ブレードで、ご利用の Automation アカウントを管理するための新しいユーザー、グループ、アプリケーションを追加できるほか、その Automation アカウント用に構成できる既存のロールを確認できます。  
   
   ![Access button](media/automation-role-based-access-control/automation-01-access-button.png)  

> [!NOTE]
> **サブスクリプション管理者** は、既定のユーザーとして最初から存在します。 Active Directory のサブスクリプション管理グループには、Azure サブスクリプションのサービス管理者と共同管理者が含まれます。 サービス管理者は、Azure サブスクリプションとそのリソースの所有者であり、Automation アカウントに継承される所有者ロールも持ちます。 つまり、アクセスはサブスクリプションの**サービス管理者と共同管理者**に**継承**され、それが他のすべてのユーザーに**割り当て**られます。 **[サブスクリプション管理]** をクリックすると、その権限の詳細が表示されます。  
> 
> 

### <a name="add-a-new-user-and-assign-a-role"></a>新しいユーザーの追加とロールの割り当て
1. [ユーザー] ブレードの **[追加]** をクリックして **[アクセスを追加]** ブレードを開きます。このブレードでは、ユーザー、グループ、アプリケーションを追加し、それらにロールを割り当てることができます。  
   
   ![ユーザーの追加](media/automation-role-based-access-control/automation-02-add-user.png)  
2. 利用可能なロールの一覧からロールを選択します。 ここでは**閲覧者**ロールを選択しますが、Automation アカウントでサポートされている任意の組み込みロールを選択してもかまいません。また、自分で定義したカスタム ロールを選択することもできます。  
   
   ![Select role](media/automation-role-based-access-control/automation-03-select-role.png)  
3. **[ユーザーの追加]** をクリックして **[ユーザーの追加]** ブレードを開きます。 サブスクリプションを管理するためのユーザー、グループ、またはアプリケーションを既に追加してある場合は、それらのユーザーが一覧に表示されるので、それらを選択してアクセス権を追加することができます。 ユーザーがまったく表示されない場合、または追加しようとするユーザーが表示されない場合は、**[招待する]** をクリックして **[ゲストを招待する]** ブレードを開きます。有効な Microsoft アカウントの電子メール アドレス (Outlook.com、OneDrive、Xbox Live の ID など) を持つユーザーを招待できます。 ユーザーの電子メール アドレスを入力したら、**[選択]** をクリックしてユーザーを追加し、**[OK]** をクリックします。 
   
   ![Add users](media/automation-role-based-access-control/automation-04-add-users.png)  
   
   **[ユーザー]** ブレードに目的のユーザーが追加され、**閲覧者**ロールが割り当てられていることを確認できます。  
   
   ![List users](media/automation-role-based-access-control/automation-05-list-users.png)  
   
   **[ロール]** ブレードから、ユーザーにロールを割り当てることもできます。 
4. [ユーザー] ブレードから **[ロール]** をクリックして **[ロール]** ブレードを開きます。 このブレードでは、ロールの名前と、そのロールに割り当てられているユーザー数およびグループ数を確認できます。
   
    ![Assign role from users blade](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
   > [!NOTE]
   > ロールベースのアクセス制御は、Automation アカウント レベルでのみ設定できます。Automation アカウントより下のリソース レベルで設定することはできません。
   > 
   > 
   
    ロールは、単一のユーザー、グループ、またはアプリケーションに複数割り当てることができます。 たとえば、**Automation オペレーター** ロールと**閲覧者**ロールをユーザーに追加した場合、そのユーザーは、すべての Automation リソースを表示することに加え、Runbook ジョブを実行することができます。 ドロップダウンを展開すると、ユーザーに割り当てられているロールの一覧を表示できます。  
   
    ![View multiple roles](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)  

### <a name="remove-a-user"></a>ユーザーの削除
Automation アカウントの管理に関与しないユーザーや既に退社したユーザーについては、アクセス権を削除することができます。 ユーザーを削除する手順を次に示します。 

1. **[ユーザー]** ブレードで、削除するロールの割り当てを選択します。
2. 割り当ての詳細ブレードで、 **[削除]** ボタンをクリックします。
3. **[はい]** をクリックして削除を確定します。 
   
   ![Remove users](media/automation-role-based-access-control/automation-08-remove-users.png)  

## <a name="role-assigned-user"></a>ロールが割り当てられているユーザー
ロールに割り当てられているユーザーがその Automation アカウントにログインすると、 **[既定のディレクトリ]**の一覧に所有者のアカウントが表示されていることが確認できます。 自分が追加されている Automation アカウントを確認するには、既定のディレクトリを所有者の既定のディレクトリに切り替える必要があります。  

![既定のディレクトリ](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)  

### <a name="user-experience-for-automation-operator-role"></a>Automation オペレーター ロールのユーザー エクスペリエンス
Automation オペレーター ロールに割り当てられているユーザーが、その割り当て先となっている Automation アカウントを表示した場合、実際に表示されるのはその Automation アカウントで作成された Runbook、Runbook ジョブ、およびスケジュールの一覧のみで、それらの定義は表示されません。 これらのユーザーは、Runbook ジョブの開始、停止、一時停止、再開、スケジュール設定を実行することができます。 Automation の構成、ハイブリッド worker グループ、DSC ノードなど、他のリソースにアクセスすることはできません。  

![No access to resourcres](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)  

Automation オペレーター ロールのユーザーが Runbook をクリックしても、ソースを表示したり Runbook を編集したりするためのコマンドは利用できません。Automation オペレーター ロールでは、それらにアクセスすることが許可されていないためです。  

![No access to edit runbook](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)  

スケジュールを表示および作成することはできますが、その他の種類のアセットは利用できません。  

![No access to assets](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)  

Runbook に関連付けられている Webhook を表示するアクセス権もこのユーザーにはありません。

![No access to webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)  

## <a name="configure-rbac-for-your-automation-account-using-azure-powershell"></a>Azure PowerShell を使用して Automation アカウントの RBAC を構成する
Automation アカウントに対するロールベースのアクセス制御は、次の [Azure PowerShell コマンドレット](../active-directory/role-based-access-control-manage-access-powershell.md)を使用して構成することもできます。

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) : Azure Active Directory で利用できるすべての RBAC ロールを一覧表示します。 このコマンドに **Name** プロパティを組み合わせることで、特定のロールで実行できるすべてのアクションを一覧表示できます。  
    **例:**  
    ![Get role definition](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)  

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) : 特定のスコープにおける Azure AD RBAC ロールの割り当てを一覧表示します。 このコマンドにパラメーターを指定しなかった場合、対象サブスクリプションで行われたすべてのロールの割り当てが返されます。 指定したユーザーと、そのユーザーが属するグループへのアクセス権の割り当てを一覧表示するには、 **ExpandPrincipalGroups** パラメーターを使用します。  
    **例:** Automation アカウント内のすべてのユーザーとそのロールを一覧表示するには、次のコマンドを使用します。

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Get role assignment](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) : 特定のスコープのユーザー、グループ、アプリケーションにアクセス権を割り当てます。  
    **例:** Automation アカウント スコープのユーザーに対して "Automation オペレーター" ロールを割り当てるには、次のコマンドを使用します。

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![New role assignment](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) : 特定のスコープの指定したユーザー、グループ、またはアプリケーションのアクセス権を削除します。  
    **例:** Automation アカウント スコープの "Automation オペレーター" ロールからユーザーを削除するには、次のコマンドを使用します。

    Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

上の例での**サインイン ID**、**サブスクリプション ID**、**リソース グループ名**、**Automation アカウント名**は、実際のアカウント情報に置き換えてください。 ユーザー ロールの割り当ての削除を続行する前に確認を求められた場合は、 **[はい]** を選択します。   

## <a name="next-steps"></a>次のステップ
* Azure Automation の RBAC を構成する各種の方法については、 [Azure PowerShell を使用した RBAC の管理](../active-directory/role-based-access-control-manage-access-powershell.md)に関する記事を参照してください。
* Runbook を開始するさまざまな方法については、 [Runbook の開始](automation-starting-a-runbook.md)
* Runbook のさまざまな種類については、「 [Azure Automation の Runbook の種類](automation-runbook-types.md)




<!--HONumber=Dec16_HO2-->


