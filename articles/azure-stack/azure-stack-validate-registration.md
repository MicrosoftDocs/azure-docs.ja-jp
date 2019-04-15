---
title: Azure Stack の Azure 登録を検証する | Microsoft Docs
description: Azure Stack 適合性チェッカーを使用して、Azure 登録を検証します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: a777fc1d9052eb58bbebd319fe6cc7f42a09cb9a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403614"
---
# <a name="validate-azure-registration"></a>Azure の登録の検証

Azure Stack 適合性チェッカー ツール (**AzsReadinessChecker**) を使用して、対象の Azure サブスクリプションを Azure Stack で使用する準備が整っていることを検証します。 Azure Stack デプロイを開始する前に、登録を検証します。 適合性チェッカーは以下を検証します。

- 使用する Azure サブスクリプションの種類がサポート対象であること。 サブスクリプションは、Cloud Service Provider (CSP) または Enterprise Agreement (EA) である必要があります。
- ご自身のサブスクリプションの登録に使用するアカウントが Azure にサインインでき、サブスクリプション所有者であること。

Azure Stack 登録の詳細については、「[Azure を使用した Azure Stack の登録](azure-stack-registration.md)」を参照してください。

## <a name="get-the-readiness-checker-tool"></a>適合性チェッカー ツールを取得する

最新バージョンの **AzsReadinessChecker** を [PowerShell ギャラリー](https://aka.ms/AzsReadinessChecker)からダウンロードします。  

## <a name="prerequisites"></a>前提条件

以下の前提条件が必要です。

**ツールを実行するコンピューター:**

- インターネットに接続された Windows 10 または Windows Server 2016。
- PowerShell 5.1 以降。 バージョンを確認するには、次の PowerShell コマンドレットを実行し、"**メジャー**" バージョンと "**マイナー**" バージョンを調べます。  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [Azure Stack 用に構成された PowerShell](azure-stack-powershell-install.md)。
- 最新バージョンの [Microsoft Azure Stack 適合性チェッカー](https://aka.ms/AzsReadinessChecker)。  

**Azure Active Directory の環境:**

- Azure Stack で使用する Azure サブスクリプションの所有者であるアカウントのユーザー名とパスワードを特定します。  
- 使用する Azure サブスクリプションのサブスクリプション ID を特定します。
- 使用する **AzureEnvironment** を特定します。 環境名のパラメーターとしてサポートされる値は、**AzureCloud**、**AzureChinaCloud**、または **AzureUSGovernment** です。使用している Azure サブスクリプションに応じて異なります。

## <a name="steps-to-validate-azure-registration"></a>Azure の登録を検証する手順

1. 前提条件を満たしているコンピューターで、管理者特権の PowerShell プロンプトを開き、次のコマンドを実行して、**AzsReadinessChecker** をインストールします。

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. PowerShell プロンプトから次のコマンドを実行して、`$registrationCredential` を、サブスクリプション所有者であるアカウントとして設定します。 `subscriptionowner@contoso.onmicrosoft.com` を、お使いのアカウントとテナント名に置き換えます。

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > CSP として、共有サービスまたは IUR サブスクリプションを使用する場合は、それぞれの AAD のユーザーの資格情報を指定する必要があります。 これは、通常、`subscriptionowner@iurcontoso.onmicrosoft.com` のようになります。 前の手順で説明したように、そのユーザーは適切な資格情報を持っている必要があります。

3. PowerShell プロンプトから次を実行して、`$subscriptionID` を、使用する Azure サブスクリプションとして設定します。 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` をお使いのサブスクリプション ID に置き換えます。

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. PowerShell プロンプトから次のコマンドを実行して、お使いのサブスクリプションの検証を開始します。

   - `AzureEnvironment` の値を **AzureCloud**、**AzureGermanCloud**、または **AzureChinaCloud** として指定します。  
   - Azure Active Directory 管理者と、お使いの Azure Active Directory テナントの名前を指定します。

   ```powershell
   Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
   ```

5. ツールの実行後、出力を確認します。 サインインと登録の両方の要件ついて、状態が適切であることを確認します。 検証が成功した場合の出力は、次の例のように表示されます。

   ```shell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

## <a name="report-and-log-file"></a>レポートとログ ファイル

検証を実行するたびに、結果のログが **AzsReadinessChecker.log** と **AzsReadinessCheckerReport.json** に出力されます。 これらのファイルの場所は、PowerShell に検証結果と共に表示されます。

これらのファイルは、Azure Stack をデプロイする前、または検証に関する問題を調査する前に、検証の状態を共有するときに役立ちます。 両方のファイルに、以降の各検証チェックの結果が保持されます。 デプロイ チームはこのレポートを使用して ID 構成を確認できます。 デプロイ チームやサポート チームは、検証の問題を調査する際に、このログ ファイルを役立たせることができます。

既定では、両方のファイルが **C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json** に書き込まれます。  

- 別のレポートの場所を指定するには、実行コマンド ラインの末尾で **-OutputPath** ***&lt;パス&gt;*** パラメーターを使用します。
- ツールの以前の実行に関する情報を **AzsReadinessCheckerReport.json** からクリアするには、実行コマンドの末尾に **-CleanReport** パラメーターを使用します。

詳細については、「[Azure Stack 検証レポート](azure-stack-validation-report.md)」を参照してください。

## <a name="validation-failures"></a>検証エラー

検証チェックに失敗した場合は、エラーの詳細が PowerShell ウィンドウに表示されます。 また、ツールによって、**AzsReadinessChecker.log** ファイルにログ情報が記録されます。

次の例は、一般的な検証エラーに関するガイダンスです。

### <a name="user-must-be-an-owner-of-the-subscription"></a>ユーザーがサブスクリプションの所有者でなければならない

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**原因** - アカウントが Azure サブスクリプションの管理者ではありません。

**解決策** - Azure サブスクリプション管理者であるアカウントを使用します。これは、Azure Stack デプロイから使用の請求対象となります。

### <a name="expired-or-temporary-password"></a>期限切れまたは一時パスワード

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change Password.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**原因** - パスワードの有効期限が切れているか、一時パスワードであるため、アカウントがサインインできません。

**解決策** - PowerShell で次のコマンドを実行し、プロンプトに従ってパスワードをリセットします。

```powershell
Login-AzureRMAccount
```

または、[Azure portal](https://portal.azure.com) にアカウント オーナーとしてサインインします。この場合、ユーザーはパスワードの変更を強制されます。

### <a name="unknown-user-type"></a>ユーザーの種類が不明  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**原因** - 指定した Azure Active Directory 環境にアカウントがサインインできません。 この例では、**AzureChinaCloud** が **AzureEnvironment** として指定されています。  

**解決策** - 指定した Azure 環境に対してアカウントが有効であることを確認します。 PowerShell で次のコマンドを実行して、特定の環境に対してアカウントが有効であることを確認します。

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>次の手順

- [Azure ID の検証](azure-stack-validate-identity.md)
- [対応状況レポートを表示する](azure-stack-validation-report.md)
- [Azure Stack の統合に関する一般的な考慮事項](azure-stack-datacenter-integration.md)
