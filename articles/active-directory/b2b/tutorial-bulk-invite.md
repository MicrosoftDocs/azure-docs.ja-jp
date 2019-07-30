---
title: B2B コラボレーション ユーザーを一括で招待するためのチュートリアル - Azure Active Directory | Microsoft Docs
description: このチュートリアルでは、PowerShell と CSV ファイルを使用して、外部の Azure AD B2B コラボレーション ユーザーに招待状を一括送信する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3bd02afa1fe1aaba6602201f839468a58673c29
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277996"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>チュートリアル:Azure AD B2B コラボレーション ユーザーを一括で招待する

Azure Active Directory (Azure AD) B2B コラボレーションを使用して外部パートナーと協力する場合は、複数のゲスト ユーザーを組織に同時に招待できます。 このチュートリアルでは、PowerShell を使用して、外部ユーザーに招待状を一括送信する方法について説明します。 具体的には、以下を実行します。

> [!div class="checklist"]
> * ユーザー情報を含むコンマ区切り値 (.csv) ファイルを準備する
> * PowerShell スクリプトを実行して招待状を送信する
> * ユーザーがディレクトリに追加されたことを確認する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。 

## <a name="prerequisites"></a>前提条件

### <a name="install-the-latest-azureadpreview-module"></a>最新の AzureADPreview モジュールをインストールする
最新バージョンの Azure AD PowerShell for Graph モジュール (AzureADPreview) をインストールしていることを確認します。 

最初に、どのモジュールをインストールしているかをチェックします。 管理者特権で Windows PowerShell を開き (管理者として実行)、次のコマンドを実行します。
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

出力に基づいて次のいずれかを行います。

- 結果が返らない場合は、次のコマンドを実行して AzureADPreview モジュールをインストールします。
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- 結果に AzureAD モジュールだけが表示される場合は、次のコマンドを実行して AzureADPreview モジュールをインストールします。 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- 結果に AzureADPreview モジュールだけが表示されるが、最新のバージョンがあることを示すメッセージが表示される場合は、次のコマンドを実行してそのモジュールをインストールします。 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

信頼されていないリポジトリからモジュールをインストールしていることを示すメッセージが表示される場合があります。 これは、PSGallery リポジトリを信頼されたリポジトリとして事前に設定していない場合に発生します。 **Y** キーを押してモジュールをインストールします。

### <a name="get-test-email-accounts"></a>テスト用の電子メール アカウントを取得する

招待状の送信先となる、複数のテスト用の電子メール アカウントが必要です。 このアカウントは、組織外にある必要があります。 gmail.com や outlook.com のアドレスなどのソーシャル アカウントを含む任意の種類のアカウントを使用できます。

## <a name="prepare-the-csv-file"></a>CSV ファイルを準備する

Microsoft Excel で、被招待者のユーザー名と電子メール アドレスの一覧を含む CSV ファイルを作成します。 列見出しとして **Name** と **InvitedUserEmailAddress** を必ず含めてください。 

たとえば、次の形式のワークシートを作成します。


![保留中のユーザーの同意を示す PowerShell の出力](media/tutorial-bulk-invite/AddUsersExcel.png)

ファイルを **C:\BulkInvite\Invitations.csv** として保存します。 

Excel をお持ちでない場合は、メモ帳などの任意のテキスト エディターで CSV ファイルを作成できます。 行ごとに値をコンマで区切って入力します。 

## <a name="sign-in-to-your-tenant"></a>テナントにサインインする

次のコマンドを実行してテナント ドメインに接続します。

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
たとえば、「 `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"` 」のように入力します。

メッセージが表示されたら、資格情報を入力します。

## <a name="send-bulk-invitations"></a>招待状を一括送信する

招待メールを送信するには、次の PowerShell スクリプトを実行します (**c:\bulkinvite\invitations.csv** は CSV ファイルのパスです)。 

```powershell
$invitations = import-csv c:\bulkinvite\invitations.csv
   
$messageInfo = New-Object Microsoft.Open.MSGraph.Model.InvitedUserMessageInfo
   
$messageInfo.customizedMessageBody = "Hello. You are invited to the Contoso organization."
   
foreach ($email in $invitations) 
   {New-AzureADMSInvitation `
      -InvitedUserEmailAddress $email.InvitedUserEmailAddress `
      -InvitedUserDisplayName $email.Name `
      -InviteRedirectUrl https://myapps.microsoft.com `
      -InvitedUserMessageInfo $messageInfo `
      -SendInvitationMessage $true
   }
```
このスクリプトは、invitations.csv ファイル内の電子メール アドレスに招待状を送信します。 次の例のようなユーザーごとの出力が表示されます。

![保留中のユーザーの同意を示す PowerShell の出力](media/tutorial-bulk-invite/B2BBulkImport.png)

## <a name="verify-users-exist-in-the-directory"></a>ユーザーがディレクトリに存在することを確認する

招待されたユーザーが Azure AD に追加されたことを確認するには、次のコマンドを実行します。
```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```
招待したユーザーが表示されていることを確認します。*emailaddress*#EXT#\@*domain* 形式のユーザー プリンシパル名 (UPN) になっています。 たとえば、*lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com* では、contoso.onmicrosoft.com が招待状を送信した組織になります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、ディレクトリ内のテスト用のユーザー アカウントを削除できます。 ユーザー アカウントを削除するには、次のコマンドを使用します。

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
次に例を示します。`Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>次の手順
このチュートリアルでは、組織の外部のゲスト ユーザーに招待状を一括送信しました。 次に、招待の受諾プロセスを理解します。

> [!div class="nextstepaction"]
> [Azure AD B2B コラボレーションの招待の受諾プロセスを確認する](redemption-experience.md)

