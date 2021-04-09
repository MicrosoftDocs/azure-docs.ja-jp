---
title: Azure Automation を使用して Office 365 サービスを管理する
description: この記事では、Azure Automation を使用して Office 365 サブスクリプション サービスを管理する方法について説明します。
services: automation
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: 70c8892969a3b13175c60a4e20e0cf9086112abe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93398047"
---
# <a name="manage-office-365-services"></a>Office 365 サービスを管理する

Azure Automation は、Microsoft Word や Microsoft Outlook などの製品に関する、Office 365 サブスクリプション サービスの管理に使用できます。 Office 365 との対話は [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) によって有効になります。 「[Azure Automation で Azure AD を使用して Azure に対して認証する](automation-use-azure-ad.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure Automation で Office 365 サブスクリプション サービスを管理するには、次のものが必要です。

* Azure サブスクリプション。 「[サブスクリプション決定ガイド](/azure/cloud-adoption-framework/decision-guides/subscriptions/)」を参照してください。
* ユーザー アカウントの資格情報と Runbook を保持する、Azure の Automation オブジェクト。 「[Azure Automation の概要](./automation-intro.md)」を参照してください。
* Azure AD。 「[Azure Automation で Azure AD を使用して Azure に対して認証する](automation-use-azure-ad.md)」を参照してください。
* アカウントを持つ Office 365 テナント。 「[Office 365 テナントのセットアップ](/sharepoint/dev/spfx/set-up-your-developer-tenant)」を参照してください。

## <a name="install-the-msonline-and-msonlineext-modules"></a>MSOnline と MSOnlineExt モジュールのインストール

Azure Automation で Office 365 を使用するには、Windows PowerShell 用 Microsoft Azure Active Directory (`MSOnline` モジュール) が必要です。 また、シングルおよびマルチ テナント環境での Azure AD 管理を簡素化するモジュール [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35) も必要です。 「[Azure Automation で Azure AD を使用して Azure に対して認証する](automation-use-azure-ad.md)」の説明に従ってこれらのモジュールをインストールしてください。

>[!NOTE]
>MSOnline PowerShell を使用するには、Azure AD のメンバーである必要があります。 ゲスト ユーザーはモジュールを使用できません。

## <a name="create-an-azure-automation-account"></a>Azure Automation アカウントを作成する

この記事の手順を完了するには、Azure Automation のアカウントが必要です。 「[Azure Automation アカウントを作成する](automation-quickstart-create-account.md)」を参照してください。
 
## <a name="add-msonline-and-msonlineext-as-assets"></a>資産としての MSOnline および MSOnlineExt の追加

次に、インストールされている MSOnline と MSOnlineExt モジュールを追加して、Office 365 の機能を有効にします。 「[Azure Automation でモジュールを管理する](shared-resources/modules.md)」を参照してください。

1. Azure portal で、 **[Automation アカウント]** を選択します。
2. お使いの Automation アカウントを選択します。
3. **[共有リソース]** で **[モジュール ギャラリー]** を選択します。
4. MSOnline を検索します。
5. `MSOnline` PowerShell モジュールを選択し、 **[インポート]** をクリックして、モジュールを資産としてインポートします。
6. 手順 4 と 5 を繰り返して、`MSOnlineExt` モジュールを見つけてインポートします。

## <a name="create-a-credential-asset-optional"></a>資格情報資産の作成 (省略可能)

スクリプトを実行するアクセス許可を持つ Office 365 管理ユーザーの資格情報資産を作成するかどうかは任意です。 ただし、これは、PowerShell スクリプト内でユーザー名とパスワードが公開されないようにするのに役立ちます。 手順については、「[資格情報資産の作成](automation-use-azure-ad.md#create-a-credential-asset)」を参照してください。

## <a name="create-an-office-365-service-account"></a>Office 365 サービス アカウントの作成

Office 365 サブスクリプション サービスを実行するには、必要な操作を実行するためのアクセス許可を持つ Office 365 サービス アカウントが必要です。 1 つの全体管理者アカウントを使用するか、サービスごとに 1 つのアカウントを使用するか、1 つの関数またはスクリプトを実行できます。 いずれの場合も、サービス アカウントには複雑でセキュリティで保護されたパスワードが必要です。 「[一般法人向け Office 365 のセットアップ](/microsoft-365/admin/setup/setup)」を参照してください。

## <a name="connect-to-the-azure-ad-online-service"></a>Azure AD オンライン サービスへの接続

>[!NOTE]
>MSOnline モジュール コマンドレットを使用するには、それらを Windows PowerShell から実行する必要があります。 PowerShell Core では、これらのコマンドレットはサポートされていません。

MSOnline モジュールを使用して、Office 365 サブスクリプションから Azure AD に接続することができます。 この接続では、Office 365 のユーザー名とパスワードを使用するか、多要素認証 (MFA) を使用します。 Azure portal または Windows PowerShell コマンド プロンプトを使用して接続できます (管理者特権は必要ありません)。

PowerShell の例を以下に示します。 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) コマンドレットは、資格情報の入力を求め、それらを `Msolcred` 変数に格納します。 その後、[Connect-MsolService](/powershell/module/msonline/connect-msolservice) コマンドレットは、その資格情報を使用してAzure ディレクトリ オンライン サービスに接続します。 特定の Azure 環境に接続したい場合は、`AzureEnvironment` パラメーターを使用します。

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

エラーが表示されない場合は、正常に接続されています。 簡単なテストとして、Office 365 コマンドレット (`Get-MsolUser` など) を実行して結果を確認します。 エラーが表示された場合、一般的な問題はパスワードが正しくないことです。

>[!NOTE]
>AzureRM モジュールまたは Az モジュールを使用して、Office 365 サブスクリプションから Azure AD に接続することもできます。 メインの接続コマンドレットは [Connect-AzureAD](/powershell/module/azuread/connect-azuread) です。 このコマンドレットは、特定の Office 365 環境の `AzureEnvironmentName` パラメーターをサポートしています。

## <a name="create-a-powershell-runbook-from-an-existing-script"></a>既存のスクリプトから PowerShell Runbook を作成する

PowerShell スクリプトから Office 365 の機能にアクセスします。 ユーザー名 `admin@TenantOne.com` を持つ `Office-Credentials` という名前の資格情報に対するスクリプトの例を次に示します。 これは、`Get-AutomationPSCredential` を使用して Office 365 資格情報をインポートします。

```powershell
$emailFromAddress = "admin@TenantOne.com"
$emailToAddress = "servicedesk@TenantOne.com"
$emailSMTPServer = "outlook.office365.com"
$emailSubject = "Office 365 License Report"

$credObject = Get-AutomationPSCredential -Name "Office-Credentials"
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body $O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="run-the-script-in-a-runbook"></a>Runbook 内でスクリプトを実行する

お使いのスクリプトを Azure Automation Runbook で使用できます。 例として、PowerShell の Runbook の種類を使用します。

1. 新しい PowerShell Runbook を作成します。 「[Azure Automation Runbook を作成する](./automation-quickstart-create-runbook.md)」を参照してください。
2. お使いの Automation アカウントから、 **[プロセスの自動化]** の下にある **[Runbook]** を選択します。
3. 新しい Runbook を選択し、 **[編集]** をクリックします。
4. お使いのスクリプトをコピーし、Runbook のテキストエディターに貼り付けます。
5. **[資産]** を選択し、 **[資格情報]** を展開して、Office 365 資格情報があることを確認します。
6. **[保存]** をクリックします。
7. **[テスト] ペイン** を選択し、 **[開始]** をクリックして Runbook のテストを開始します。 「[Azure Automation で Runbook を管理する](./manage-runbooks.md)」を参照してください。
8. テストが完了したら、[テスト] ペインから終了します。

## <a name="publish-and-schedule-the-runbook"></a>Runbookを発行およびスケジュール設定する

Runbook を発行してスケジュールを設定するには、「[Azure Automation で Runbook を管理する](./manage-runbooks.md)」を参照してください

## <a name="next-steps"></a>次のステップ

* 資格情報の使用について詳しくは、「[Azure Automation で資格情報を管理する](shared-resources/credentials.md)」を参照してください。
* モジュールの詳細については、「[Azure Automation でモジュールを管理する](shared-resources/modules.md)」を参照してください。
* Runbook を開始する必要がある場合は、「[Azure Automation で Runbook を開始する](start-runbooks.md)」を参照してください。
* PowerShell の詳細については、[PowerShell のドキュメント](/powershell/scripting/overview)を参照してください。
