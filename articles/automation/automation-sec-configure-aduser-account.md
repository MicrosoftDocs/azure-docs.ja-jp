---
title: "Azure AD ユーザー アカウントを構成する | Microsoft Docs"
description: "この記事では、Azure Automation の Runbook が認証を受ける際に使用する Azure AD ユーザー アカウントを構成する方法について説明します。"
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
keywords: "Azure Active Directory ユーザー, Azure サービス管理, Azure AD ユーザー アカウント"
ms.assetid: fcfe266d-b22e-4dfb-8272-adcab09fc0cf
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/14/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 0078d544d5c30e31874d75fece62ca560d2aa2d6
ms.openlocfilehash: 0544d4df8f50db2182375aec8ec2a0a9d47ed644


---
# <a name="authenticate-runbooks-with-azure-service-management-and-resource-manager"></a>Azure サービス管理と Azure リソース マネージャーによる Runbook の認証
この記事では、Azure サービス管理および Azure Resource Manager のリソースに対して実行する Azure Automation Runbook 用に Azure AD ユーザー アカウントを構成するための手順を説明します。  Azure Resource Manager ベースの Runbook では、このアカウントを引き続き認証 ID として使用できますが、新しい Azure 実行アカウントを使用することが推奨されます。       

## <a name="create-a-new-azure-active-directory-user"></a>新しい Azure Active Directory ユーザーを作成する
1. 管理する Azure サブスクリプションのサービス管理者として Azure クラシック ポータルにログインします。
2. **[Active Directory]**を選択し、組織のディレクトリの名前を選択します。
3. **[ユーザー]** タブをクリックし、コマンド領域の **[ユーザーの追加]** を選択します。
4. **[このユーザーに関する情報の入力]** ページの **[ユーザーの種類]** から **[組織内の新しいユーザー]** を選択します。
5. ユーザー名を入力します。  
6. Active Directory ページの Azure サブスクリプションに関連付けられているディレクトリ名を選択します。
7. **[ユーザー プロファイル]** ページで、姓と名、わかりやすい名前を入力し、**[ロール]** ボックスの一覧からユーザーを選択します。  **Multi-Factor Authentication を有効**にしないでください。
8. ユーザーの完全な名前と一時的なパスワードを書き留めておきます。
9. Select **Settings > Administrators > Add**.
10. 作成したユーザーの完全なユーザー名を入力します。
11. このユーザーで管理するサブスクリプションを選択してください。
12. ログアウトして、先ほど作成したアカウントで再度 Azure にログインする。 ユーザーのパスワードを変更するように促されます。

## <a name="create-an-automation-account-in-azure-classic-portal"></a>Azure クラシック ポータルで Automation アカウントを作成する
このセクションでは、以下の手順を実行して、Azure サービス管理および Azure Resource Manager モードでリソースを管理する Runbook 用の Azure Automation アカウントを Azure Portal で新規作成します。  

> [!NOTE]
> Azure クラシック ポータルで作成した Automation アカウントは、Azure クラシック ポータルおよび Azure ポータルの両方で、どちらのコマンドレット セットでも管理できます。 アカウントを作成してしまえば、そのアカウントのリソースの作り方や管理方法に違いはありません。 引き続き Azure クラシック ポータルを使用する場合、Automation アカウントの作成には Azure ポータルではなく、Azure クラシック ポータルを使用することをお勧めします。
> 
> 

1. 管理する Azure サブスクリプションのサービス管理者として Azure クラシック ポータルにログインします。
2. **[Automation]**を選択します。
3. **[Automation]** ページで、**[Automation アカウントの作成]** を選択します。
4. **[Automation アカウントの作成]** ボックスで、新しい Automation アカウントの名前を入力し、ドロップダウン リストから **[リージョン]** を選択します。  
5. **[OK]** をクリックして設定を受け入れて、アカウントを作成します。
6. アカウントが作成され、 **[Automation]** ページに表示されます。
7. アカウントをクリックします。ダッシュボード ページが表示されます。  
8. Automation のダッシュボード ページで、 **[資産]**を選択します。
9. **[資産]** ページで、ページの下部にある **[設定の追加]** を選択します。
10. **[設定の追加]** ページで、**[資格情報の追加]** を選択します。
11. **[資格情報の定義]** ページで、**[資格情報の種類]** ボックスの一覧の **[Windows PowerShell 資格情報]** を選択し、資格情報の名前を指定します。
12. 次の **[資格情報の定義]** ページで、前に作成した AD ユーザー アカウントのユーザー名を **[ユーザー名]** フィールドに入力し、**[パスワード]** および **[パスワードの確認]** フィールドにパスワードを入力します。 **[OK]** をクリックして変更を保存します。

## <a name="create-an-automation-account-in-the-azure-portal"></a>Azure ポータルで Automation アカウントを作成する
このセクションでは、以下の手順を実行して、Azure Resource Manager モードでリソースを管理する Runbook 用の Azure Automation アカウントを Azure Portal で新規作成します。  

1. 管理する Azure サブスクリプションのサービス管理者として Azure Portal にログインします。
2. **[Automation アカウント]**を選択します。
3. [Automation アカウント] ブレードで **[追加]** をクリックします。<br>![[Automation アカウントの追加]](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. **[Automation アカウントの追加]** ブレードの **[名前]** ボックスに、新しい Automation アカウントの名前を入力します。
5. 複数のサブスクリプションがある場合は、新しいアカウントに対していずれかを指定し、新規または既存の**リソース グループ**と、Azure データ センターの**場所**も指定します。
6. **[Azure 実行アカウントの作成]** オプションで **[いいえ]** を選択し、**[作成]** ボタンをクリックします。  
   
   > [!NOTE]
   > 実行アカウントを作成しなかった場合 (先ほどのオプションで **[いいえ]** を選択した場合)、**[Automation アカウントの追加]** ブレードに警告メッセージが表示されます。  アカウントが作成されてサブスクリプションの **共同作成者** ロールに割り当てられますが、サブスクリプションのディレクトリ サービス内の対応する認証 ID が割り当てられないため、サブスクリプション内のリソースにアクセスすることはできません。  このアカウントを参照する Runbook は認証を通過できず、Azure Resource Manager リソースに対するタスクを実行することができません。
   > 
   > 
   
    ![Add Automation Account Warning](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)
7. Azure によって Automation アカウントが作成されている間、メニューの **[通知]** で進行状況を追跡できます。

資格情報の作成が完了したら、資格情報資産を作成し、作成済みの AD ユーザー アカウントに Automation アカウントを関連付ける必要があります。  Automation アカウントは作成しただけで、認証 ID に関連付けられていません。  「[Azure Automation での資格情報資産](automation-credentials.md#creating-a-new-credential-asset)」に説明されている手順を実行し、**ユーザー名**の値を**ドメイン\ユーザー**の形式で入力します。

## <a name="use-the-credential-in-a-runbook"></a>Runbook での資格情報の使用
[Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) アクティビティを使用して Runbook の資格情報を取得した後、それを [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) で使用して Azure サブスクリプションに接続できます。 資格情報が複数の Azure サブスクリプションの管理者のものである場合は、 [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) も使用して正しいものを指定する必要があります。 これについては、ほとんどの Azure Automation Runbook の先頭に一般に出現する以下のサンプル Windows PowerShell で示されています。

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Runbook のすべての [チェックポイント](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) の後で、これらの行を繰り返す必要があります。 Runbook が中断された後、別のワーカーで再開した場合は、もう一度認証を実行する必要があります。

## <a name="next-steps"></a>次のステップ
* Runbook のさまざまな種類について、また、独自の Runbook を作成する手順について確認します。記事「[Azure Automation の Runbook の種類](automation-runbook-types.md)」を参照してください




<!--HONumber=Nov16_HO3-->


