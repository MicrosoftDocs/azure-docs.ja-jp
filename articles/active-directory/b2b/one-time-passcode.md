---
title: B2B ゲスト ユーザーのワンタイム パスコード認証 - Azure Active Directory | Microsoft Docs
description: 電子メール ワンタイム パスコードを使用して、Microsoft アカウントを必要とせずに B2B ゲスト ユーザーを認証する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b817346c37ec43fd66d166684f5d51ecb5a9718
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799441"
---
# <a name="email-one-time-passcode-authentication-preview"></a>電子メール ワンタイム パスコード認証 (プレビュー)

|     |
| --- |
| 電子メール ワンタイム パスコードは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

この記事では、B2B ゲスト ユーザーの電子メールのワンタイム パスコード認証を有効にする方法について説明します。 電子メール ワンタイム パスコード機能では、B2B ゲスト ユーザーが Azure AD、Microsoft アカウント (MSA)、Google フェデレーションなどの他の手段を使用して認証できないときに、ユーザーの認証が行われます。 ワンタイム パスコード認証では、Microsoft アカウントを作成する必要はありません。 ゲスト ユーザーは、招待に応じるか、共有リソースにアクセスするときに、自分のメール アドレスに送信される一時的なコードを要求することができます。 その後は、このコードを入力してサインインを続けます。

現在、この機能はプレビューで使用できます (後述する「[プレビューにオプトインする](#opting-in-to-the-preview)」をご覧ください)。 プレビュー後、この機能はすべてのテナントに対して既定で有効になります。

> [!NOTE]
> ワンタイム パスコードのユーザーは、テナントのコンテキストを含むリンクを使用してサインインする必要があります (たとえば、`https://myapps.microsoft.com/?tenantid=<tenant id>` や `https://portal.azure.com/<tenant id>`、または検証済みのドメインの場合は `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`)。 アプリケーションとリソースへの直接リンクも、テナント コンテキストが含まれている限り機能します。 ゲスト ユーザーは現在、テナント コンテキストが含まれていないエンドポイントを使用してサインインできません。 たとえば、`https://myapps.microsoft.com`、`https://portal.azure.com`、またはチームの共有エンドポイントを使用すると、エラーが発生します。 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>ワンタイム パスコードのゲスト ユーザーに対するユーザー エクスペリエンス
ワンタイム パスコード認証では、ゲスト ユーザーは、直接リンクをクリックするか、招待メールを使用して、招待に応じることができます。 どちらの場合も、ブラウザーのメッセージで、ゲスト ユーザーのメール アドレスにコードが送信されることが示されます。 ゲスト ユーザーは、**[コードの送信]** を選択します。
 
   ![[コードの送信] ボタンを示すスクリーンショット](media/one-time-passcode/otp-send-code.png)
 
パスコードがユーザーのメール アドレスに送信されます。 ユーザーは、メールからパスコードを取得し、ブラウザー ウィンドウに入力します。
 
   ![[コードの入力] ページを示すスクリーンショット](media/one-time-passcode/otp-enter-code.png)
 
ゲスト ユーザーは認証されて、共有リソースを表示したり、サインインを続行したりできるようになります。 

> [!NOTE]
> ワンタイム パスコードの有効期間は 30 分です。 30 分が経過すると、その特定のワンタイム パスコードは無効になり、ユーザーは新しいパスコードを要求する必要があります。 ユーザー セッションは 24 時間後に期限が切れます。 それを過ぎると、ゲスト ユーザーはリソースにアクセスするときに新しいパスコードを受け取ります。 セッションの有効期限により、ゲスト ユーザーが自分の会社を退職したりアクセスを必要としなくなったときに特に、セキュリティが強化されます。

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>ゲスト ユーザーがワンタイム パスコードを入手するとき

次の場合、ゲスト ユーザーは、招待に応じたとき、または共有されているリソースへのリンクを使用したときに、ワンタイム パスコードを受け取ります。
- Azure AD アカウントを持っていない 
- Microsoft アカウントを持っていない 
- 招待側テナントが @gmail.com および @googlemail.com ユーザーに Google フェデレーションを設定していない 

招待するとき、招待されるユーザーにワンタイム パスコード認証が使用されることは示されません。 ただし、ゲスト ユーザーがサインインするとき、他の認証方法を使用できない場合は、ワンタイム パスコード認証がフォールバック メソッドになります。 

Azure portal の **[Azure Active Directory]** > **[組織の関係]** > **[他の組織からのユーザー]** で、ワンタイム パスコードを使用して認証しているゲスト ユーザーを確認できます。

![[Source]\(ソース\) の値が OTP のワンタイム パスワード ユーザーを示すスクリーンショット](media/one-time-passcode/otp-users.png)

> [!NOTE]
> ユーザーがワンタイム パスコードを使用した後、MSA、Azure AD アカウント、または他のフェデレーション アカウントを取得した場合、引き続きワンタイム パスコードによる認証が使用されます。 認証方法を更新する場合は、ゲスト ユーザー アカウントを削除して、招待し直すことができます。

### <a name="example"></a>例
ゲスト ユーザー alexdoe@gmail.com は、Google フェデレーションが設定されていない Fabrikam に招待されます。 Alex は Microsoft アカウントを持っていません。 彼は認証用のワンタイム パスコードを受け取ります。

## <a name="opting-in-to-the-preview"></a>プレビューにオプトインする 
オプトイン アクションが有効になるまで、数分かかる場合があります。 その後は、上記の条件を満たす新しく招待されたユーザーだけが、ワンタイム パスコード認証を使用します。 以前に招待に応じたゲスト ユーザーは引き続き、同じ認証方法を使用します。

### <a name="to-opt-in-using-the-azure-ad-portal"></a>Azure AD ポータルを使用してオプトインするには
1.  Azure AD の全体管理者として [Azure portal](https://portal.azure.com/) にサインインします。
2.  ナビゲーション ペインで、**[Azure Active Directory]** を選択します。
3.  **[管理]** で **[組織の関係]** を選択します。
4.  **[設定]** を選択します。
5.  **[ゲストの電子メール ワンタイム パスコードを有効にする (プレビュー)]** で、**[はい]** を選択します。
 
### <a name="to-opt-in-using-powershell"></a>PowerShell を使用してオプトインするには

最初に、最新バージョンの Azure AD PowerShell for Graph モジュール (AzureADPreview) をインストールする必要があります。 その後、B2B ポリシーが既に存在するかどうかを確認して、適切なコマンドを実行します。

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>前提条件:最新の AzureADPreview モジュールをインストールする
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

#### <a name="check-for-existing-policies-and-opt-in"></a>既存のポリシーを確認してオプトインする

次に、以下を実行して、B2BManagementPolicy が現在存在するかどうかを確認します。

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- 出力が False の場合、ポリシーは現在存在しません。 新しい B2BManagementPolicy を作成し、以下を実行してプレビューにオプトインします。

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- 出力が True の場合は、現在 B2BManagementPolicy ポリシーは存在します。 ポリシーを更新してプレビューにオプトインするには、以下を実行します。
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>オプトインの後でプレビューからオプトアウトする
オプトアウト アクションが有効になるまで、数分かかる場合があります。 プレビューを無効にした場合、ワンタイム パスコードを使用したゲスト ユーザーはサインインできなくなります。 ゲスト ユーザーを削除して招待し直すことで、ゲスト ユーザーが別の認証方法を使用して再びサインインできるようにすることができます。

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>Azure AD ポータルを使用してプレビューをオフにするには
1.  Azure AD の全体管理者として [Azure portal](https://portal.azure.com/) にサインインします。
2.  ナビゲーション ペインで、**[Azure Active Directory]** を選択します。
3.  **[管理]** で **[組織の関係]** を選択します。
4.  **[設定]** を選択します。
5.  **[ゲストの電子メール ワンタイム パスコードを有効にする (プレビュー)]** で、**[いいえ]** を選択します。

### <a name="to-turn-off-the-preview-using-powershell"></a>PowerShell を使用してプレビューをオフにするには
まだ行っていない場合は、最新の AzureADPreview モジュールをインストールします (前の「[前提条件:最新の AzureADPreview モジュールをインストールする](#prerequisite-install-the-latest-azureadpreview-module)」を参照してください)。 次に、以下を実行して、ワンタイム パスコード プレビュー ポリシーが現在存在するかどうかを確認します。

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

出力が True の場合は、以下を実行してプレビューをオプトアウトします。

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

