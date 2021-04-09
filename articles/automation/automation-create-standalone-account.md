---
title: スタンドアロン Azure Automation アカウントを作成する
description: この記事では、スタンドアロンの Azure Automation アカウントとクラシック実行アカウントを作成する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 01/07/2021
ms.topic: conceptual
ms.openlocfilehash: e0088fb129e9c6558de7539ba754a45e067dc3d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576839"
---
# <a name="create-a-standalone-azure-automation-account"></a>スタンドアロン Azure Automation アカウントを作成する

この記事では、Azure Automation アカウントを Azure Portal で作成する方法について説明します。 ポータルの Automation アカウントを使用すると、追加の管理機能を使用したり、Azure Monitor ログを統合したりすることなく、Automation について評価し、学ぶことができます。 管理機能の追加や Azure Monitor ログの統合は、Runbook ジョブを詳細に監視するために、後からいつでも行うことができます。

Automation アカウントを使うと、Azure Resource Manager またはクラシック デプロイ モデルでリソースを管理することで、Runbook を認証できます。 1 つの Automation アカウントで、特定のテナントのすべてのリージョンおよびサブスクリプションにわたってリソースを管理できます。

Azure portal で Automation アカウントを作成すると、**実行** アカウントが自動的に作成されます。 このアカウントは、次のタスクを実行します。

* Azure Active Directory (Azure AD) にサービス プリンシパルを作成します。
* 証明書を作成します。
* Runbook を使用して Azure Resource Manager リソースを管理する、共同作成者ロールを割り当てます。

このアカウントが作成されると、自動化のニーズを満たす Runbook の作成とデプロイをすばやく開始することができます。

## <a name="permissions-required-to-create-an-automation-account"></a>Automation アカウントを作成するために必要なアクセス許可

Automation アカウントを作成または更新したり、この記事で説明されているタスクを完了したりするには、次の特権とアクセス許可が必要です。

* Automation アカウントを作成するには、Azure AD ユーザー アカウントが、`Microsoft.Automation` リソースの所有者ロールに相当するアクセス許可を持つロールに追加されている必要があります。 詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。
* Azure portal の **[Azure Active Directory]**  >  **[管理]**  >  **[ユーザー設定]** で、( **[アプリの登録]** が **[はい]** に設定されている場合)、Azure AD テナント内の管理者以外のユーザーは [Active Directory アプリケーションを登録](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions)できます。 **[アプリの登録]** が **[いいえ]** に設定されている場合、このアクションを実行するユーザーに Azure AD 内のアプリケーション開発者ロールがある必要があります。

サブスクリプションの Active Directory インスタンスのメンバーになっていない状態で、サブスクリプションのグローバル管理者または共同管理者ロールに追加された場合、Active Directory にはゲストとして追加されます。 このシナリオでは、[Automation アカウントの追加] ページに次のメッセージが表示されます: `You do not have permissions to create.`

ユーザーが先にグローバル管理者または共同管理者ロールに追加された場合は、そのユーザーをサブスクリプションの Active Directory インスタンスから削除できます。 Active Directory のユーザー ロールにユーザーを追加し直すことができます。 ユーザー ロールを確認するには

1. Azure portal で、[Azure Active Directory] ウィンドウに移動します。
1. **[ユーザーとグループ]** を選択します。
1. **[すべてのユーザー]** を選択します。
1. 特定のユーザーを選択した後、 **[プロファイル]** を選択します。 ユーザーのプロファイルの下にある **[ユーザー タイプ]** 属性の値が **[ゲスト]** であってはいけません。

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Azure Portal で新しい Automation アカウントを作成する

Azure Portal で Azure Automation アカウントを作成するには、以下の手順を実行します。

1. サブスクリプション管理者ロールのメンバーであり、かつサブスクリプションの共同管理者であるアカウントを使用して Azure Portal にサインインします。
1. **[+ リソースの作成]** を選択します。
1. 「**Automation**」を検索します。 検索結果で、 **[Automation]** を選択します。

   ![Azure Marketplace で [Automation & Control] を検索して選択する](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. 次の画面で **[新規作成]** を選択します。

   ![Automation アカウントの追加](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > [Automation アカウントの追加] ウィンドウに次のメッセージが表示された場合、お使いのアカウントは、サブスクリプションの管理者ロールのメンバーではなく、サブスクリプションの共同管理者でもありません。
   >
   > :::image type="content" source="media/automation-create-standalone-account/create-account-without-perms.png" alt-text="&quot;Azure Active Directory に実行アカウントを作成するためのアクセス許可がありません&quot; というプロンプトのスクリーンショット":::

1. [Automation アカウントの追加] ウィンドウの **[名前]** フィールドに、新しい Automation アカウントの名前を入力します。 選択した後は、この名前を変更することはできません。 

    > [!NOTE]
    > Automation アカウント名は、リージョンおよびリソース グループごとに一意です。 削除された Automation アカウントの名前は、すぐには使用できない場合があります。

1. 複数のサブスクリプションがある場合は、 **[サブスクリプション]** フィールドを使用して、新しいアカウントで使用するサブスクリプションを指定します。
1. **[リソース グループ]** に、新しいリソース グループを入力するか既存のリソース グループを選択します。
1. **[場所]** で、Azure データセンターの場所を選択します。
1. **[Azure 実行アカウントの作成]** オプションで、 **[はい]** を確実に選択し、 **[作成]** をクリックします。

   > [!NOTE]
   > **[Azure 実行アカウントの作成]** で **[いいえ]** を選択して、実行アカウントを作成しなかった場合、[Automation アカウントの追加] ウィンドウにメッセージが表示されます。 Azure Portal でアカウントが作成されますが、このアカウントは、クラシック デプロイ モデル内または Azure Resource Manager サブスクリプションのディレクトリ サービス内に対応する認証 ID を持ちません。 その結果、この Automation アカウントは、ご使用のサブスクリプションのリソースへのアクセス権を持ちません。 そのため、このアカウントを参照する Runbook は認証を通過できず、これらのデプロイメント モデルのリソースに対するタスクを実行することができません。
   >
   > :::image type="content" source="media/automation-create-standalone-account/create-account-decline-create-runas-msg.png" alt-text="&quot;実行アカウントを作成しないことを選択しました&quot; というメッセージを含むプロンプトのスクリーンショット":::
   >
   > サービス プリンシパルが作成されていない場合、共同作成者ロールは割り当てられません。
   >

1. Automation アカウントの作成の進行状況を追跡するには、メニューで **[通知]** を選択します。

Automation アカウントが正常に作成されると、いくつかのリソースが自動的に作成されます。 これらの作成後、Runbook を保持しない場合は安全に削除できます。 実行アカウントを使用して、Runbook でアカウントに対する認証を行うことができます。別の実行アカウントを作成する場合や実行アカウントを必要としない場合を除き、実行アカウントは残しておく必要があります。 実行アカウントのリソースを次の表に示します。

| リソース | 説明 |
| --- | --- |
| AzureAutomationTutorial Runbook |実行アカウントを使用した認証の方法を示す、サンプルのグラフィカルな Runbook。 この Runbook は、すべての Resource Manager リソースを取得します。 |
| AzureAutomationTutorialScript Runbook |実行アカウントを使用した認証の方法を示す、サンプルの PowerShell Runbook。 この Runbook は、すべての Resource Manager リソースを取得します。 |
| AzureAutomationTutorialPython2 Runbook |実行アカウントを使用した認証の方法を示す、サンプルの Python Runbook。 この Runbook には、サブスクリプション内に存在するすべてのリソース グループが一覧表示されます。 |
| AzureRunAsCertificate |Automation アカウントの作成時に自動的に作成される証明書資産、または既存のアカウント用に PowerShell スクリプトを使用して作成される証明書資産。 この証明書は、Runbook から Azure Resource Manager リソースを管理できるよう、Azure に対する認証を行います。 この証明書には、1 年の有効期間があります。 |
| AzureRunAsConnection |Automation アカウントの作成時に自動的に作成される接続資産、または既存のアカウント用に PowerShell スクリプトを使用して作成される接続資産。 |

## <a name="create-a-classic-run-as-account"></a>クラシック実行アカウントを作成する

Azure Automation アカウントを作成するとき、クラシック実行アカウントは既定では作成されません。 Azure クラシック リソースを管理するためにクラシック実行アカウントが必要な場合は、次の手順を実行します。

1. [Automation アカウント] で、 **[アカウントの設定]** の **[実行アカウント]** を選択します。
2. **[Azure クラシック実行アカウント]** を選択します。
3. **[作成]** をクリックして、クラシック実行アカウントの作成に進みます。

## <a name="next-steps"></a>次のステップ

* グラフィック作成の詳細については、[Azure Automation でのグラフィカル Runbook の作成](automation-graphical-authoring-intro.md)に関する記事を参照してください。
* PowerShell Runbook の使用を開始するには、「[チュートリアル:PowerShell Runbook を作成する](learn/automation-tutorial-runbook-textual-powershell.md)」を参照してください。
* PowerShell Workflow Runbook の使用を開始するには、「[チュートリアル:PowerShell Workflow Runbook を作成する](learn/automation-tutorial-runbook-textual.md)」を参照してください。
* Python 3 Runbook の使用を開始するには、「[チュートリアル: Python 3 Runbook を作成する](learn/automation-tutorial-runbook-textual-python-3.md)」を参照してください。
* PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation)」をご覧ください。
