---
title: 'クイックスタート: PowerShell を使用してゲスト ユーザーを追加する - Azure AD'
description: このクイック スタートでは、PowerShell を使用して、外部の Azure AD B2B コラボレーション ユーザーに招待状を送信する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0faaf0394bddc2e443afc194bbd0ecef72625f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87910599"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>クイック スタート: PowerShell を使用してゲスト ユーザーを追加する

Azure Active Directory B2B コラボレーションで外部パートナーを自社のアプリとサービスに招待できる方法は多数あります。 前のクイック スタートでは、Azure Active Directory 管理ポータルでゲスト ユーザーを直接追加する方法を説明しました。 ゲスト ユーザーは、PowerShell を使用して、一度に 1 人づつ、または一括で追加することもできます。 このクイック スタートでは、New-AzureADMSInvitation コマンドを使用して、1 人のゲスト ユーザーを Azure テナントに追加します。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。 

## <a name="prerequisites"></a>前提条件

### <a name="install-the-latest-azureadpreview-module"></a>最新の AzureADPreview モジュールをインストールする
最新バージョンの Azure AD PowerShell for Graph モジュール (AzureADPreview) をインストールしていることを確認します。 

最初に、どのモジュールをインストールしているかをチェックします。 管理者特権で Windows PowerShell を開き (管理者として実行)、次のコマンドを実行します。
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

最新のバージョンがあることを示すメッセージなしで AzureADPreview モジュールが表示されれば、問題ありません。 それ以外の場合は、出力に基づいて次のいずれかを行います。

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

### <a name="get-a-test-email-account"></a>テスト用の電子メール アカウントを取得する

招待状の送信先となるテスト用の電子メール アカウントが必要です。 このアカウントは、組織外にある必要があります。 gmail.com や outlook.com のアドレスなどのソーシャル アカウントを含む任意の種類のアカウントを使用できます。

## <a name="sign-in-to-your-tenant"></a>テナントにサインインする

次のコマンドを実行してテナント ドメインに接続します。

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
たとえば、「 `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"` 」のように入力します。

メッセージが表示されたら、資格情報を入力します。

## <a name="send-an-invitation"></a>招待状を送信する

1. テスト用の電子メール アカウントに招待状を送信するには、次の PowerShell コマンドを実行します (**"Sanda"** と **sanda\@fabrikam.com** をテスト用の電子メール アカウント名と電子メール アドレスに置き換えます)。 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.microsoft.com -SendInvitationMessage $true
   ```
2. このコマンドは、指定した電子メール アドレスに招待状を送信します。 出力をチェックします。出力は次のようになります。

   ![保留中のユーザーの同意を示す PowerShell の出力](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>ユーザーがディレクトリ内に存在することを確認する

1. 招待されたユーザーが Azure AD に追加されたことを確認するには、次のコマンドを実行します。
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. 出力をチェックして、招待したユーザーが表示されていることを確認します。*emailaddress*#EXT#\@*domain* 形式のユーザー プリンシパル名 (UPN) になっています。 たとえば、*sanda_fabrikam.com#EXT#\@contoso.onmicrosoft.com* では、contoso.onmicrosoft.com が招待状を送信した組織になります。

   ![ゲスト ユーザーが追加されたことを示す PowerShell の出力](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、ディレクトリ内のテスト用のユーザー アカウントを削除できます。 ユーザー アカウントを削除するには、次のコマンドを使用します。

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
例: `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>次のステップ
このクイック スタートでは、PowerShell を使用して、1 人のゲスト ユーザーを招待してディレクトリに追加しました。 次に、PowerShell を使用して、一括でゲスト ユーザーを招待する方法について説明します。

> [!div class="nextstepaction"]
> [チュートリアル:Azure AD B2B コラボレーション ユーザーを一括で招待する](tutorial-bulk-invite.md)
