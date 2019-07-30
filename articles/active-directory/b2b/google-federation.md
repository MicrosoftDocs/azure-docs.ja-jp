---
title: Google を B2B の ID プロバイダーとして追加する - Azure Active Directory | Microsoft Docs
description: Google とのフェデレーションを行って、ゲスト ユーザーが独自の Gmail アカウントを使用して Azure AD アプリにサインインできるようにします
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 735c3db14963c1f3cfe700a97dee9fedb70e29f5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441124"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users-preview"></a>Google を B2B ゲスト ユーザーの ID プロバイダーとして追加する (プレビュー)

|     |
| --- |
| Google フェデレーションは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

Google とのフェデレーションを設定することで、招待されたユーザーが Microsoft アカウント (MSA) または Azure AD アカウントを作成することなく、独自の Google アカウントを使用して共有アプリおよびリソースにサインインできるようにできます。  
> [!NOTE]
> Google ゲスト ユーザーは、テナント コンテキストが含まれたリンク (例: `https://myapps.microsoft.com/?tenantid=<tenant id>` または `https://portal.azure.com/<tenant id>`、または検証済みのドメインの場合は `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`) を使用してサインインする必要があります。 アプリケーションとリソースへの直接リンクも、テナント コンテキストが含まれている限り機能します。 ゲスト ユーザーは現在、テナント コンテキストが含まれていないエンドポイントを使用してサインインできません。 たとえば、`https://myapps.microsoft.com`、`https://portal.azure.com`、またはチームの共有エンドポイントを使用すると、エラーが発生します。
 
## <a name="what-is-the-experience-for-the-google-user"></a>Google ユーザーのエクスペリエンスの内容
Google Gmail ユーザーに招待を送信すると、そのゲスト ユーザーは、テナント コンテキストが含まれているリンクを使用して共有アプリまたはリソースにアクセスする必要があります。 Google に既にサインインしているかどうかによって、エクスペリエンスが異なります。
  - ゲスト ユーザーが Google にサインインしていない場合、Google にサインインするよう求められます。
  - ゲスト ユーザーが Google に既にサインインしている場合、使用するアカウントを選択するよう求められます。 招待に使用されたアカウントを選択する必要があります。

"ヘッダーが長すぎる" エラーが発生したら、ゲスト ユーザーは Cookie のクリアを試すことができます。または、プライベートまたは匿名のウィンドウを開いて、もう一度サインインを試すことができます。

![Google のサインイン ページを示すスクリーンショット](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>手順 1:Google 開発者プロジェクトを構成する
最初に、Google Developers Console で新しいプロジェクトを作成して、Azure AD に後で追加するクライアント ID とクライアント シークレットを取得します。 
1. https://console.developers.google.com で Google API に移動し、Google アカウントでサインインします。 共有のチーム Google アカウントを使用することをお勧めします。
2. 新しいプロジェクトを作成します。ダッシュボードで **[プロジェクトを作成]** 、 **[作成]** の順に選択します。 [新しいプロジェクト] ページで、 **[プロジェクト名]** に入力を行ってから **[作成]** を選択します。
   
   ![Google の [New project] ページを示すスクリーンショット](media/google-federation/google-new-project.png)

3. 新しいプロジェクトがプロジェクト メニューで選択されていることを確認します。 次に、左上隅にあるメニューを開いて、 **[API とサービス]**  >  **[認証情報]** の順に選択します。

   ![Google の [API credentials] オプションを示すスクリーンショット](media/google-federation/google-api.png)
 
4. **[OAuth consent screen]\(OAuth 同意画面\)** タブを選択して、 **[Application name]\(アプリケーション名\)** を入力します。 (他の設定はそのままにします)。

   ![Google の [OAuth consent] 画面を示すスクリーンショット](media/google-federation/google-oauth-consent-screen.png)

5. **[Authorized domains]\(承認済みドメイン\)** セクションまでスクロールし、「microsoftonline.com」と入力します。

   ![[Authorized domains] セクションを示すスクリーンショット](media/google-federation/google-oauth-authorized-domains.png)

6. **[保存]** を選択します。

7. **[認証情報]** タブを選択します。 **[認証情報を作成]** メニューで、 **[OAuth クライアント ID]** を選択します。

   ![Google の [APIs create credentials] オプションを示すスクリーンショット](media/google-federation/google-api-credentials.png)

8. **[アプリケーションの種類]** で **[Web アプリケーション]** を選択し、 **[承認済みのリダイレクト URI]** で次の URI を入力します。
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(`<directory id>` は自分のディレクトリ ID に置き換えます)
   
     > [!NOTE]
     > ディレクトリ ID を見つけるには、 https://portal.azure.com に移動します。 **[Azure Active Directory]** で **[プロパティ]** を選択して、**ディレクトリ ID** をコピーします。

   ![[Authorized redirect URIs] セクションを示すスクリーンショット](media/google-federation/google-create-oauth-client-id.png)

9. **作成** を選択します。 クライアント ID とクライアント シークレットをコピーします。これらは、Azure AD ポータルで ID プロバイダーを追加する際に使用します。

   ![OAuth クライアント ID とクライアント シークレットを示すスクリーンショット](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>手順 2:Azure AD で Google フェデレーションを構成する 
次に、Google クライアント ID とクライアント シークレットを設定します。Azure AD ポータルで入力するか、または PowerShell を使用します。 Gmail アドレスを使って自分自身を招待したうえで、招待した Google アカウントを使って招待を利用してみて、Google フェデレーション構成をテストするようにしてください。 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Azure AD ポータルで Google フェデレーションを構成するには 
1. [Azure ポータル](https://portal.azure.com)にアクセスします。 左ウィンドウで、 **[Azure Active Directory]** を選択します。 
2. **[組織の関係]** を選択します。
3. **[ID プロバイダー]** を選択し、 **[Google]** ボタンをクリックします。
4. 名前を入力します。 次に、先ほど取得したクライアント ID とクライアント シークレットを入力します。 **[保存]** を選択します。 

   ![[Add Google identity provider] ページを示すスクリーンショット](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>PowerShell を使用して Google フェデレーションを構成するには
1. 最新バージョンの Azure AD PowerShell for Graph モジュールをインストールします ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. `Connect-AzureAD` というコマンドを実行します。
3. サインイン プロンプトで、マネージド グローバル管理者アカウントを使用してサインインします。  
4. 次のコマンドを実行します。 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > クライアント ID とクライアント シークレットは、「手順 1:Google 開発者プロジェクトを構成する」で作成したアプリのものを使用します。 詳細については、記事「[New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview)」を参照してください。 
 
## <a name="how-do-i-remove-google-federation"></a>Google フェデレーションを削除する方法
Google フェデレーション セットアップは削除できます。 これを行うと、招待を既に利用している Google ゲスト ユーザーはサインインできなくなります。しかし、ディレクトリからこれらのユーザーを削除して招待し直すことで、もう一度リソースへのアクセス権を付与できます。 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Azure AD ポータルで Google フェデレーションを削除するには: 
1. [Azure ポータル](https://portal.azure.com)にアクセスします。 左ウィンドウで、 **[Azure Active Directory]** を選択します。 
2. **[組織の関係]** を選択します。
3. **[Identity Providers]** を選択します。
4. **[Google]** 行で、コンテキスト メニュー ( **...** ) を選択してから **[Delete]** を選択します。 
   
   ![ソーシャル ID プロバイダーの [Delete] オプションを示すスクリーンショット](media/google-federation/google-social-identity-providers.png)

1. **[はい]** を選択して削除を確定します。 

### <a name="to-delete-google-federation-by-using-powershell"></a>PowerShell を使用して Google フェデレーションを削除するには: 
1. 最新バージョンの Azure AD PowerShell for Graph モジュールをインストールします ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. `Connect-AzureAD` を実行します。  
4. ログイン プロンプトで、マネージド グローバル管理者アカウントを使用してサインインします。  
5. 次のコマンドを入力します。

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > 詳細については、「[Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview)」を参照してください。 
