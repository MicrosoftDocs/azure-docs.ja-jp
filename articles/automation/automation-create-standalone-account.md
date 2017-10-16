---
title: "スタンドアロン Azure Automation アカウントを作成する | Microsoft Docs"
description: "Azure Automation のセキュリティ プリンシパル認証の作成、テスト、使用例をわかりやすく説明しています。"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/18/2017
ms.author: magoedte
ms.openlocfilehash: e3c18c7886c8338efc6168464b63a9557909a769
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-standalone-azure-automation-account"></a>スタンドアロン Azure Automation アカウントを作成する
このトピックでは、Azure Automation の評価および学習を目的として、Azure Portal から Automation アカウントを作成する方法について説明します。Runbook ジョブの高度な監視を実現する管理ソリューションの追加や OMS Log Analytics との統合は含まれていません。  そうした管理ソリューションの追加や Log Analytics との統合は、後からいつでも行うことができます。  Automation アカウントを使うと、Runbook の認証を行い、Azure Resource Manager または Azure クラシック デプロイメントのリソースを管理できます。

Azure Portal で Automation アカウントを作成すると、次のものが自動的に作成されます。

* Azure Active Directory の新しいサービス プリンシパルと証明書を作成し、ロールベースのアクセス制御 (RBAC) の共同作成者ロールを割り当てる実行アカウント。この共同作成者ロールは、Runbook を使用した Resource Manager のリソースの管理に使用されます。   
* クラシック実行アカウント。クラシック リソースを Runbook で管理する際に使用する管理証明書をアップロードすることで作成されます。  

これによって必要な作業が単純化され、オートメーションのニーズを満たす Runbook をすぐに作成し、デプロイすることができます。  

## <a name="permissions-required-to-create-automation-account"></a>Automation アカウントを作成するために必要なアクセス許可
Automation アカウントを作成したり更新したりするには、このトピックの作業で要求される以下に記載した特権とアクセス許可が必要となります。   
 
* Automation アカウントを作成するためには、ご利用の AD ユーザー アカウントが、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」の記事に記載されている Microsoft.Automation リソースの所有者ロールに相当するアクセス許可を備えたロールに追加されている必要があります。  
* [アプリの登録] が **[はい]** に設定されている場合、Azure AD テナントの非管理者ユーザーが [AD アプリケーションを登録](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions)できます。  [アプリの登録] が **[いいえ]** に設定されている場合、この操作を行うユーザーは、Azure AD の全体管理者であることが必要です。 

サブスクリプションの Active Directory インスタンスのメンバーになっていない状態で、サブスクリプションの全体管理者/共同管理者ロールに追加された場合、Active Directory にゲストとして追加されることになります。 この場合、"…を作成するためのアクセス許可がありません" という 警告が **[Automation アカウントの追加]** ブレードに表示されます。 先に全体管理者/共同管理者ロールに追加されていたユーザーは、サブスクリプションの Active Directory インスタンスから削除した後、Active Directory の完全なユーザーとして再度追加できます。 このような状況を検証するには、Azure Portal の **[Azure Active Directory]** ウィンドウで、**[ユーザーとグループ]**、**[すべてのユーザー]**、特定のユーザー、**[プロファイル]** の順に選択します。 ユーザーのプロファイルの下部にある **[ユーザー タイプ]** 属性の値は、**[ゲスト]** と一致しないようにする必要があります。

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Azure Portal から新しい Automation アカウントを作成する
このセクションでは、以下の手順に従って、Azure Portal で Azure Automation アカウントを作成します。    

1. サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用して、Azure Portal にサインインします。
2. **[新規]**をクリックします。<br><br> ![Azure Portal での [新規] オプションの選択](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. **Automation** を検索し、検索結果から **[Automation & Control]** を選択します。<br><br> ![Marketplace からの Automation の検索と選択](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)<br> 
3. [Automation アカウント] ブレードで **[追加]** をクリックします。<br><br>![[Automation アカウントの追加]](media/automation-create-standalone-account/automation-create-automationacct-properties.png)


   > [!NOTE]
   > お使いのアカウントが、サブスクリプション管理ロールのメンバーではなく、サブスクリプションの共同管理者でもない場合、**[Automation アカウントの追加]** ブレードに次の警告が表示されます。<br><br>![Add Automation Account Warning](media/automation-create-standalone-account/create-account-without-perms.png)
   > 
   > 
4. **[Automation アカウントの追加]** ブレードの **[名前]** ボックスに、新しい Automation アカウントの名前を入力します。
5. 複数のサブスクリプションがある場合は、新しいアカウントに対して 1 つのサブスクリプションを指定し、新規または既存の**リソース グループ**と、Azure データ センターの**場所**を指定します。
6. **[Azure 実行アカウントの作成]** オプションで **[はい]** が選択されていることを確認し、**[作成]** ボタンをクリックします。  
   
   > [!NOTE]
   > 実行アカウントを作成しなかった場合 (先ほどのオプションで **[いいえ]** を選択した場合)、**[Automation アカウントの追加]** ブレードに警告メッセージが表示されます。  Azure Portal でアカウントを作成している間は、クラシック サブスクリプションまたは Resource Manager サブスクリプションのディレクトリ サービスには対応する認証 ID が割り当てられず、サブスクリプション内のリソースにアクセスすることはできません。  そのため、このアカウントを参照する Runbook は認証を通過できず、これらのデプロイメント モデルのリソースに対するタスクを実行することができません。
   > 
   > ![Add Automation Account Warning](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)<br>
   > サービス プリンシパルが作成されていない場合、共同作成者ロールは割り当てられません。
   > 

7. Azure によって Automation アカウントが作成されている間、メニューの **[通知]** で進行状況を追跡できます。

### <a name="resources-included"></a>含まれるリソース
Automation アカウントが正常に作成されると、いくつかのリソースが自動的に作成されます。  実行アカウントのリソースを次の表に示します。<br>

| リソース | Description |
| --- | --- |
| AzureAutomationTutorial Runbook |実行アカウントを使用した認証の方法と、すべての Resource Manager リソースの取得方法を示す、サンプルのグラフィカルな Runbook。 |
| AzureAutomationTutorialScript Runbook |実行アカウントを使用した認証の方法と、すべての Resource Manager リソースの取得方法を示す、サンプルの PowerShell Runbook。 |
| AzureAutomationTutorialPython2 Runbook |実行アカウントを使って認証を行い、指定したサブスクリプション内のリソース グループを一覧表示するサンプル Python Runbook。 |
| AzureRunAsCertificate |Automation アカウントの作成時または既存のアカウント用に下の PowerShell スクリプトを使用した場合に自動的に作成される、証明書資産。  これにより、Azure を使用して認証を行い、Runbook から Azure Resource Manager リソースを管理できるようになります。  この証明書には、1 年の有効期間があります。 |
| AzureRunAsConnection |Automation アカウントの作成時または既存のアカウント用に下の PowerShell スクリプトを使用した場合に自動的に作成される、接続資産。 |

クラシック実行アカウントのリソースを次の表に示します。<br>

| リソース | Description |
| --- | --- |
| AzureClassicAutomationTutorial Runbook |クラシック実行アカウント (証明書) を使用してサブスクリプション内のすべてのクラシック VM を取得し、VM の名前と状態を出力するサンプルのグラフィカルな Runbook。 |
| AzureClassicAutomationTutorial Script Runbook |クラシック実行アカウント (証明書) を使用してサブスクリプション内のすべてのクラシック VM を取得し、VM の名前と状態を出力するサンプルの PowerShell Runbook。 |
| AzureClassicRunAsCertificate |Runbook から Azure のクラシック リソースを管理できるように、Azure を使用した認証に使用される、自動的に作成される証明書資産。  この証明書には、1 年の有効期間があります。 |
| AzureClassicRunAsConnection |Runbook から Azure のクラシック リソースを管理できるように、Azure を使用した認証に使用される、自動的に作成される接続資産。 |


## <a name="next-steps"></a>次のステップ
* グラフィカル作成の詳細については、「[Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)」を参照してください。
* PowerShell Runbook の使用を開始するには、「[初めての PowerShell Runbook](automation-first-runbook-textual-powershell.md)」を参照してください。
* PowerShell Workflow Runbook の使用を開始するには、「 [最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)」を参照してください。
* Python2 Runbook の使用を開始するには、[初めての Python2 Runbook](automation-first-runbook-textual-python2.md) に関するページを参照してください。
