---
title: Azure Automation の実行アカウントを作成する
description: この記事では、PowerShell または Azure portal を使用して、実行アカウントを作成する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: ef6afff30da48b79b42e5fb4b3c72c3500f22dd1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172305"
---
# <a name="how-to-create-an-azure-automation-run-as-account"></a>Azure Automation の実行アカウントを作成する方法

Azure Automation の実行アカウントでは、Automation の Runbook やその他の自動化機能を使用し、Azure Resource Manager または Azure クラシック デプロイ モデルのリソースを管理する認証を提供します。 この記事では、Azure portal または Azure PowerShell から実行またはクラシック実行アカウントを作成する方法について説明します。

## <a name="create-account-in-azure-portal"></a>Azure portal でアカウントを作成する

以下の手順を行って、Azure portal で Azure Automation アカウントを更新します。 実行アカウントとクラシック実行アカウントは別々に作成されます。 クラシック リソースを管理する必要がない場合は、Azure 実行アカウントのみを作成できます。

1. サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用して、Azure Portal にサインインします。

2. **Automation アカウント** を検索して選択します。

3. **[Automation アカウント]** ページで、一覧からお使いの Automation アカウントを選択します。

4. 左側のウィンドウの **[アカウント設定]** から **[実行アカウント]** を選択します。

    :::image type="content" source="media/create-run-as-account/automation-account-properties-pane.png" alt-text="[実行アカウント] オプションを選択する。":::

5. 必要なアカウントに応じて、 **[+ Azure 実行アカウント]** または **[+ Azure クラシック実行アカウント]** を使用します。 概要情報を確認した後、 **[作成]** クリックします。

    :::image type="content" source="media/create-run-as-account/automation-account-create-run-as.png" alt-text="実行アカウントを作成するオプションを選択する":::

6. Azure によって実行アカウントが作成されている間、メニューの **[通知]** で進行状況を追跡できます。 アカウントが作成されていることを示すバナーも表示されます。 プロセスが完了するまでに数分かかることがあります。

## <a name="create-account-using-powershell"></a>PowerShell を使用してアカウントを作成する

提供されたスクリプトを使用して PowerShell で実行アカウントを作成する場合の要件一覧を次に示します。 これらの要件は、両方の種類の実行アカウントに適用されます。

* Azure Resource Manager モジュール 3.4.1 以降がインストールされた Windows 10 または Windows Server 2016。 PowerShell スクリプトでは、以前のバージョンの Windows はサポートされていません。
* Azure PowerShell 6.2.4 以降。 詳細については、[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-az-ps)に関するページを参照してください。
* Automation アカウント。`AutomationAccountName` および `ApplicationDisplayName` パラメーターの値として参照されます。
* [実行アカウントを構成するために必要なアクセス許可](automation-security-overview.md#permissions)に関する記述で一覧表示されているものに相当するアクセス許可。

PowerShell スクリプトの必須パラメーターである `AutomationAccountName`、`SubscriptionId` および `ResourceGroupName`を取得するには、次の手順を行います。

1. Azure portal にサインインします。

1. **Automation アカウント** を検索して選択します。

1. [Automation アカウント] ページで、一覧から Automation アカウントを選択します。

1. 左側のウィンドウで、**[プロパティ]** を選択します。

1. **[プロパティ]** ページの **[名前]** 、 **[サブスクリプション ID]** 、および **[リソース グループ]** の値をメモします。

   ![Automation アカウントの [プロパティ] ページ](media/create-run-as-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>実行アカウントを作成する PowerShell スクリプト

PowerShell スクリプトでは、いくつかの構成をサポートしています。

* 自己署名証明書を使用して実行アカウントを作成する。
* 自己署名証明書を使用して実行アカウントやクラシック実行アカウントを作成する。
* エンタープライズ証明機関 (CA) によって発行された証明書を使用して実行アカウントやクラシック実行アカウントを作成する。
* Azure Government クラウドで自己署名証明書を使用して実行アカウントやクラシック実行アカウントを作成する。

1. 次のコマンドを使用して、スクリプトをダウンロードし、ローカル フォルダーに保存します。

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. 管理者特権で PowerShell を起動し、スクリプトが格納されているフォルダーに移動します。

3. 次のいずれかのコマンドを実行して、自分の要件に基づいて実行アカウントまたはクラシック実行アカウントを作成します。

    * 自己署名証明書を使用して実行アカウントを作成する。

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * 自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する。

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * エンタープライズ証明書を使用して実行アカウントとクラシック実行アカウントを作成する。

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        エンタープライズ公開証明書 ( .cer ファイル) を使ってクラシック実行アカウントを作成した場合は、この証明書を使用します。 このスクリプトでは、PowerShell セッションの実行に使用したお使いのコンピューターのユーザー プロファイルの一時ファイル フォルダー `%USERPROFILE%\AppData\Local\Temp` にそれを作成して保存します。 [Azure portal への管理 API 証明書のアップロード](../cloud-services/cloud-services-configure-ssl-certificate-portal.md)に関するページを参照してください。

    * Azure Government クラウドで自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. スクリプトの実行後に、Azure での認証が求められます。 サブスクリプション管理者ロールのメンバーであるアカウントを使用して、サインインします。 クラシック実行アカウントを作成する場合、そのサブスクリプションの共同管理者のアカウントを使用する必要があります。

## <a name="next-steps"></a>次のステップ

* グラフィック作成の詳細については、[Azure Automation でのグラフィカル Runbook の作成](automation-graphical-authoring-intro.md)に関する記事を参照してください。
* PowerShell Runbook の使用を開始するには、「[チュートリアル:PowerShell Runbook を作成する](learn/automation-tutorial-runbook-textual-powershell.md)」を参照してください。
* Python 3 runbook の使用を開始するには、「[チュートリアル: Python 3 Runbook を作成する](learn/automation-tutorial-runbook-textual-python-3.md)」を参照してください。
