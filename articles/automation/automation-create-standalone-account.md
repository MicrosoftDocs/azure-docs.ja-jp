---
title: スタンドアロン Azure Automation アカウントを作成する
description: この記事では、Azure Automation のセキュリティ プリンシパル認証のサンプルを作成、テスト、使用する手順をわかりやすく説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0dcfcfe5bc6e59eeb4ccb7272ed3f68edc9c4172
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850399"
---
# <a name="create-a-standalone-azure-automation-account"></a>スタンドアロン Azure Automation アカウントを作成する

この記事では、Azure Automation アカウントを Azure Portal で作成する方法について説明します。 ポータルの Automation アカウントを使用すると、追加の管理ソリューションを使用したり、Azure Monitor ログと統合したりすることなく、Automation について評価し、学ぶことができます。 このような管理ソリューションの追加や Azure Monitor ログとの統合は、Runbook ジョブを詳細に監視するために、後からいつでも行うことができます。

Automation アカウントを使うと、Azure Resource Manager またはクラシック デプロイ モデルでリソースを管理することで、Runbook を認証できます。 1 つの Automation アカウントで、特定のテナントのすべてのリージョンおよびサブスクリプションにわたってリソースを管理できます。

Azure Portal で Automation アカウントを作成すると、次のアカウントが自動的に作成されます。

* **実行アカウント**。 このアカウントは、次のタスクを実行します。
  * Azure Active Directory (Azure AD) にサービス プリンシパルを作成します。
  * 証明書を作成します。
  * Runbook を使用して Azure Resource Manager リソースを管理する、共同作成者のロールベースのアクセス制御 (RBAC) を割り当てます。

これらのアカウントが作成されると、オートメーションのニーズを満たす Runbook の作成とデプロイをすばやく開始することができます。

## <a name="permissions-required-to-create-an-automation-account"></a>Automation アカウントを作成するために必要なアクセス許可

Automation アカウントを作成または更新したり、この記事で説明されているタスクを完了したりするには、次の特権とアクセス許可が必要です。

* Automation アカウントを作成するには、Azure AD ユーザー アカウントが、**Microsoft.Automation** リソースの所有者ロールに相当するアクセス許可を持つロールに追加されている必要があります。 詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。
* Azure portal の **[Azure Active Directory]**  >  **[管理]**  >  **[ユーザー設定]** で、( **[アプリの登録]** が **[はい]** に設定されている場合)、Azure AD テナント内の管理者以外のユーザーは [Active Directory アプリケーションを登録](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions)できます。 **[アプリの登録]** が **[いいえ]** に設定されている場合、このアクションを実行するユーザーは Azure AD 内のグローバル管理者である必要があります。

サブスクリプションの Active Directory インスタンスのメンバーになっていない状態で、サブスクリプションのグローバル管理者/共同管理者ロールに追加された場合、Active Directory にはゲストとして追加されます。 このシナリオでは、 **[Automation アカウントの追加]** ページに "作成するためのアクセス許可がありません" というメッセージが表示されます。

ユーザーが先にグローバル管理者/共同管理者ロールに追加された場合は、そのユーザーをサブスクリプションの Active Directory インスタンスから削除した後、Active Directory の完全なユーザー ロールに再度追加できます。

ユーザー ロールを確認するには

1. Azure Portal で、 **[Azure Active Directory]** ウィンドウに移動します。
1. **[ユーザーとグループ]** を選択します。
1. **[すべてのユーザー]** を選択します。
1. 特定のユーザーを選択した後、 **[プロファイル]** を選択します。 ユーザーのプロファイルの下にある **[ユーザー タイプ]** 属性の値が **[ゲスト]** であってはいけません。

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Azure Portal で新しい Automation アカウントを作成する

Azure Portal で Azure Automation アカウントを作成するには、以下の手順を実行します。

1. サブスクリプション管理者ロールのメンバーであり、かつサブスクリプションの共同管理者であるアカウントを使用して Azure Portal にサインインします。
1. **[+ リソースの作成]** を選択します。
1. 「**Automation**」を検索します。 検索結果で、 **[Automation]** を選択します。

   ![Azure Marketplace で [Automation & Control] を検索して選択する](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. 次の画面で **[作成]** を選択します。

   ![Automation アカウントの追加](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > **[Automation アカウントの追加]** ウィンドウに次のメッセージが表示された場合、お使いのアカウントは、サブスクリプションの管理者ロールのメンバーではなく、サブスクリプションの共同管理者でもありません。
   >
   > ![[Automation アカウントの追加] の警告](media/automation-create-standalone-account/create-account-without-perms.png)

1. **[Automation アカウントの追加]** ウィンドウの **[名前]** ボックスに、新しい Automation アカウントの名前を入力します。 この名前は、選択後に変更することはできません。 *Automation アカウント名は、リージョンおよびリソース グループごとに一意です。削除された Automation アカウントの名前はすぐには使用できない場合があります。*
1. 複数のサブスクリプションがある場合は、 **[サブスクリプション]** ボックスで、新しいアカウントで使用するサブスクリプションを指定します。
1. **[リソース グループ]** に、新しいリソース グループを入力するか既存のリソース グループを選択します。
1. **[場所]** で、Azure データセンターの場所を選択します。
1. **[Azure 実行アカウントの作成]** オプションで、 **[はい]** が選択されていることを確認し、 **[作成]** を選択します。

   > [!NOTE]
   > **[Azure 実行アカウントの作成]** で **[いいえ]** を選択して、実行アカウントを作成しなかった場合、 **[Automation アカウントの追加]** ウィンドウにメッセージが表示されます。 Azure Portal でアカウントが作成されますが、このアカウントは、クラシック デプロイ モデル内または Azure Resource Manager サブスクリプションのディレクトリ サービス内に対応する認証 ID を持ちません。 その結果、この Automation アカウントは、ご使用のサブスクリプションのリソースへのアクセス権を持ちません。 そのため、このアカウントを参照する Runbook は認証を通過できず、これらのデプロイメント モデルのリソースに対するタスクを実行することができません。
   >
   > ![[Automation アカウントの追加] の警告](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > サービス プリンシパルが作成されていない場合、共同作成者ロールは割り当てられません。
   >

1. Automation アカウントの作成の進行状況を追跡するには、メニューで **[通知]** を選択します。

### <a name="resources-included"></a>含まれるリソース

Automation アカウントが正常に作成されると、いくつかのリソースが自動的に作成されます。 これらの作成後、Runbook を保持しない場合は安全に削除できます。 実行アカウントを使用して、Runbook でアカウントに対する認証を行うことができます。別の実行アカウントを作成する場合や実行アカウントを必要としない場合を除き、実行アカウントは残しておく必要があります。 実行アカウントのリソースを次の表に示します。

| リソース | 説明 |
| --- | --- |
| AzureAutomationTutorial Runbook |実行アカウントを使用した認証の方法を示す、サンプルのグラフィカルな Runbook。 この Runbook は、すべての Resource Manager リソースを取得します。 |
| AzureAutomationTutorialScript Runbook |実行アカウントを使用した認証の方法を示す、サンプルの PowerShell Runbook。 この Runbook は、すべての Resource Manager リソースを取得します。 |
| AzureAutomationTutorialPython2 Runbook |実行アカウントを使用した認証の方法を示す、サンプルの Python Runbook。 この Runbook には、サブスクリプション内に存在するすべてのリソース グループが一覧表示されます。 |
| AzureRunAsCertificate |Automation アカウントの作成時に自動的に作成される証明書資産、または既存のアカウント用に PowerShell スクリプトを使用して作成される証明書資産。 この証明書は、Runbook から Azure Resource Manager リソースを管理できるよう、Azure に対する認証を行います。 この証明書には、1 年の有効期間があります。 |
| AzureRunAsConnection |Automation アカウントの作成時に自動的に作成される接続資産、または既存のアカウント用に PowerShell スクリプトを使用して作成される接続資産。 |

## <a name="classic-run-as-accounts"></a>クラシック実行アカウント

Azure Automation アカウントを作成するとき、クラシック実行アカウントは既定では作成されなくなりました。 クラシック実行アカウントがまだ必要な場合は、次の手順を実行してください。

1. **[Automation アカウント]** ページで、 **[アカウントの設定]** の **[実行アカウント]** を選択します。
2. **[Azure クラシック実行アカウント]** を選択します。
3. **[作成]** をクリックして、クラシック実行アカウントの作成に進みます。

## <a name="next-steps"></a>次の手順

* グラフィカル作成の詳細については、「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)」を参照してください。
* PowerShell Runbook の使用を開始するには、「[初めての PowerShell Runbook](automation-first-runbook-textual-powershell.md)」を参照してください。
* PowerShell Workflow Runbook の使用を開始するには、「 [最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)」を参照してください。
* Python2 Runbook の使用を開始するには、[初めての Python2 Runbook](automation-first-runbook-textual-python2.md) に関するページを参照してください。

