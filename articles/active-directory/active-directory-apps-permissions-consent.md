---
title: "Azure Active Directory のアプリ、アクセス許可、および同意 | Microsoft Docs"
description: "Azure AD Connect は、オンプレミスのディレクトリと Azure Active Directory を統合する機能です。 Office 365、Azure、SaaS など Azure AD と連動するアプリケーションの ID を共通化することができます。"
keywords: "Azure AD の概要, アプリ, Azure AD Connect とは, Active Directory のインストール"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 25897cc4-7687-49b6-b0d5-71f51302b6b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/17/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 095497656a1c6ed1fe5322b6015de750d8fd6f15
ms.openlocfilehash: 3706ed729a525526a415d3a3367e6b8245ead812


---

# <a name="apps-permissions-and-consent-in-azure-active-directory"></a>Azure Active Directory のアプリ、アクセス許可、および同意
Azure Active Directory では、アプリケーションをディレクトリに追加できます。  そのアプリケーションは、アプリケーションの種類に応じて異なります。  クラシック ポータルでアプリケーションを表示するには、ディレクトリを選んで [アプリケーション] を選択します。

![](media/active-directory-apps-permissions-consent/apps1.png)


## <a name="types-of-apps"></a>アプリの種類

1. **シングルテナント アプリ** </br>
    - **シングルテナント アプリ** - 多くの場合、基幹業務 (LOB) アプリと呼ばれます。 このケースでは、組織内のメンバーが独自のアプリを開発しており、そのアプリに組織のユーザーがサインインできるようにしたいと考えています。
    ![](media/active-directory-apps-permissions-consent/apps2.png)
    - **アプリ プロキシ アプリ** - Azure AD アプリ プロキシを使用してオンプレミスのアプリケーションを公開する場合、シングルテナント アプリは、(アプリ プロキシ サービスに加えて) テナントに登録されます。 このアプリは、クラウドとのすべてのやり取り (認証など) 用のオンプレミス アプリケーションを表します  (アプリ プロキシには Azure AD Basic 以上が必要です)。


2. **マルチテナント アプリ**
    - **他のユーザーが同意できるマルチテナント アプリ** - "所属する組織が開発したシングルテナント アプリ" に似ています。 (アプリ自体のロジックのほかに) 主な違いは、他のテナントのユーザーもアプリに同意してサインインできる点です。</br>
    ![](media/active-directory-apps-permissions-consent/apps4.png)
    - **Contoso が同意できる、他の組織が開発したマルチテナント アプリ**  (または "同意されるアプリ")。これは、"所属する組織が開発したマルチテナント アプリ" の反対です。 他の組織がマルチテナント アプリを開発した場合に、自分の組織のユーザーはそのアプリに同意してサインインできます。
    - **ファーストパーティの Microsoft アプリ** - Microsoft サービスを表します。 同意は、サービスへのサインアップに基づきます。 特定のファーストパーティ アプリでは、アプリへのアクセスに関するポリシーを確立する際によく使用される、特別な UX とロジックが存在する場合があります。</br>
    ![](media/active-directory-apps-permissions-consent/apps3.png)
    - **事前統合アプリ** - Azure AD アプリ ギャラリーで入手できるアプリです。ディレクトリに追加して、人気の SaaS アプリへのシングル サインオン (場合によってはプロビジョニング) を有効にできます。
    - **Azure AD シングル サインオン**: サポートされているサインイン プロトコル (SAML 2.0 または OpenID Connect) を使用した、Azure AD に統合できるアプリの "本当の" SSO です。 ウィザードに従って設定します。
    - **パスワード シングル サインオン**: Azure AD では、アプリで使うユーザーの資格情報が安全に保管されます。資格情報は、Azure AD アプリ アクセス ブラウザー拡張機能によってサインイン フォームに "入力" されます。 これは "パスワード保管" とも呼ばれます。

## <a name="permissions"></a>アクセス許可

アプリを登録するとき、アプリの登録を行うユーザー (つまり開発者) は、アプリに必要なアクセス許可とリソースを定義します  (リソース自体は他のアプリとして定義されます)。たとえば、メール リーダー アプリを作成している開発者なら、そのアプリから "Office 365 Exchange Online" リソースに対し、"サインイン済みユーザーとしてメールボックスにアクセスする" アクセス許可が必要だと言うことがあります。
    
![](media/active-directory-apps-permissions-consent/apps6.png)

あるアプリ (クライアント) が別のアプリ (リソース) に対して特定のアクセス許可を要求できるように、リソース アプリの開発者は存在するアクセス許可を定義します。 この例では、"Office 365 Exchange Online" リソース アプリの所有者である Microsoft が、"サインイン済みユーザーとしてメールボックスにアクセスする" という名前のアクセス許可を定義しています。

アクセス許可を定義する際に、アプリ開発者は、アクセス許可への同意が可能かどうかか、アクセス許可に管理者の同意が必要かどうかを定義する必要があります。 これにより開発者は、機密度の低いアクセス許可のみを要求するアプリではユーザーが自分で同意できるように設定できます。一方で、機密度の高いアクセス許可には管理者の同意が必要になるように設定できます。 たとえば、"Azure Active Directory" リソース アプリは、ユーザーがアプリに同意して制限された読み取り専用アクセス許可を要求できるよう、定義されています。  しかし、完全な読み取りアクセス許可とすべての書き込みアクセス許可には、管理者の同意が必要です。

ネイティブ クライアントが認証されていないため、ネイティブ クライアント アプリとして定義されたアプリが要求できるのは、委任されたアクセス許可のみです。 これは、トークンを取得する際に関係する実際のユーザーが常に必要であることを意味します。 Web アプリと Web API (機密性の高いクライアント) は、アクセス トークンを取得する際に必ず Azure AD の認証を受ける必要があります。 つまり、これらのアプリでも、アプリ専用のアクセス許可を要求する可能性があります。 たとえば、ある 1 つのバックエンド サービスが別のバックエンド サービスの認証を受ける必要がある場合などです。 アプリ専用のアクセス許可を要求するアプリケーションでは、常に管理者の同意が必要です。

まとめ:



- アプリ (クライアント) は、他のアプリ (リソース) に対して必要なアクセス許可を宣言します。
- アプリ (リソース) は、他のアプリ (クライアント) に公開するアクセス許可を宣言します。
- アクセス許可は、アプリ専用のアクセス許可であることも、委任されたアクセス許可であることもあります。
- 委任されたアクセス許可は、"ユーザーの同意を許可" または "管理者の同意が必要" としてマークできます。
- アプリは、クライアント (リソースに対するアクセス許可が必要であると宣言する場合)、リソース (公開するアクセス許可を宣言する場合)、またはその両方として動作する場合があります。

## <a name="controls"></a>コントロール

この動作すべてで使用できる、さまざまな管理者コントロールの一覧を次に示します。 クラシック ポータルでは、ディレクトリの下にある [構成] から管理者コントロールにアクセスできます。

![](media/active-directory-apps-permissions-consent/apps7.png)

Azure Portal では、**[管理]** の下にある **[ユーザー設定]** を選択します。

![](media/active-directory-apps-permissions-consent/apps11.png)



- ユーザーがアプリに同意できるかどうかを制御できます。

クラシック ポータルでは、**[Users may give applications permissions to access their data (ユーザーは自分のデータへのアクセス許可をアプリケーションに付与できる)]** を選択します。
![](media/active-directory-apps-permissions-consent/apps8.png)

Azure Portal では、**[ユーザーはアプリが自分のデータにアクセスすることを許可できる]** を選択します。
![](media/active-directory-apps-permissions-consent/apps12.png)



- ユーザーが独自のシングルテナント LOB アプリを登録できるかどうかを制御できます。クラシック ポータルでは、**[ユーザーが統合アプリケーションを追加できるようにする]** を選択します。
![](media/active-directory-apps-permissions-consent/apps9.png)

Azure Portal では、**[ユーザーはアプリが自分のデータにアクセスすることを許可できる]** を選択します。
![](media/active-directory-apps-permissions-consent/apps13.png)

>[!NOTE]
>ユーザーがシングルテナント LOB アプリを登録できるように許可した場合であっても、登録内容には制限があります。  
>たとえば、ディレクトリ管理者でない開発者です。
>
>- ユーザーは、シングルテナント アプリをマルチテナント アプリにすることはできません。
>- シングルテナント LOB アプリを登録する場合、ユーザーは他のアプリに対してアプリ専用のアクセス許可を要求できません。
>- シングルテナント LOB アプリを登録する場合、委任されたアクセス許可に管理者の同意が必要なときに、ユーザーは他のアプリに対してそのアクセス許可を要求できません。
>- ユーザーは、所有していないアプリに変更を加えることはできません。



- パスワード SSO (つまり "パスワード保管") が使用される事前統合アプリをユーザー自身が追加できるかどうかを制御できます。 ![](media/active-directory-apps-permissions-consent/apps10.png)



- アプリケーションにアクセスできる条件を制御できます (条件付きアクセス)。 この条件はクライアント アプリとリソース アプリの両方に適用されることに注意してください。 準拠しているコンピューターからのみ "Office 365 Exchange Online" アプリにアクセスできるという条件付きアクセス ポリシーを設定するとします。  このポリシーは、Exchange Online へのアクセス許可を要求するクライアント アプリをユーザーが使用するときにも機能します。



- 同意済みのアプリと使用中のアプリを確認できます。

1.  ユーザーがアプリに同意すると、テナントに ServicePrincipal オブジェクトが作成されます。 ServicePrincipal の作成は監査レポートに記載されます。
2.  ユーザー サインインのアクティビティ レポートから、ユーザーがサインインしているアプリがわかります。 

## <a name="example"></a>例

例として、"FabrikamMail for Office 365" アプリを取り上げます。テナントのユーザーがこのアプリにサインインしようとしています。 "FabrikamMail" は、"Fabrikam, Inc." が発行した、Android 用のメール リーダー アプリです。 これは、"Contoso が同意できる、他の組織が開発したマルチテナント アプリ" に該当します。

ユーザーの同意が許可されている場合、初めてサインインするときにプロンプトによって同意が求められます。 ![](media/active-directory-apps-permissions-consent/apps14.png)

[Access your mailboxes (メールボックスにアクセスする)] は、"Office 365 Exchange Online" (Exchange) によって公開されている "サインイン済みユーザーとしてメールボックスにアクセスする" アクセス許可に関する、ユーザーに向けられた同意の文字列です。

Exchange (リソース) の ServicePrincipal オブジェクトを検索してアクセス許可を確認できます。このオブジェクトは、Office 365 にサインアップした際に追加されたものです。 ServicePrincipal オブジェクトは、異なるオプションと構成を記録するために使用される、テナントにあるアプリの "インスタンス" と見なすことができます。  これは、PowerShell で `Get-AzureADServicePrincipal` を使用することで表示できます。

    PS C:\> Get-AzureADServicePrincipal -ObjectId 383f7b97-6754-4d3d-9474-3908ebcba1c6 | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : Office 365 Exchange Online
    AppId                     : 00000002-0000-0ff1-ce00-000000000000
    AppOwnerTenantId          : 
    AppRoleAssignmentRequired : False
    AppRoles                  : {...}
    DisplayName               : Microsoft.Exchange
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {...
                                , class OAuth2Permission {
                                  AdminConsentDescription : Allows the app to have the same access to mailboxes as the signed-in user via Exchange Web Services.
                                  AdminConsentDisplayName : Access mailboxes as the signed-in user via Exchange Web Services
                                  Id                      : 3b5f3d61-589b-4a3c-a359-5dd4b5ee5bd5
                                  IsEnabled               : True
                                  Type                    : User
                                  UserConsentDescription  : Allows the app full access to your mailboxes on your behalf.
                                  UserConsentDisplayName  : Access your mailboxes
                                  Value                   : full_access_as_user
                                },
                                ...}
    PasswordCredentials       : {}
    PublisherName             : 
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {00000002-0000-0ff1-ce00-000000000000/outlook.office365.com, 00000002-0000-0ff1-ce00-000000000000/mail.office365.com, 00000002-0000-0ff1-ce00-000000000000/outlook.com, 
                                00000002-0000-0ff1-ce00-000000000000/*.outlook.com...}
    Tags                      : {}

ユーザーが [Accept (承諾)] をクリックすると、同意が開始されます。 最初に、"FabrikamMail for Office 365" の ServicePrincipal オブジェクトがテナントに作成されます。 ServicePrincipal は次のようになります。

    PS C:\> Get-AzureADServicePrincipal -SearchString "FabrikamMail for Office 365" | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : a8b16333-851d-42e8-acd2-eac155849b37
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : FabrikamMail for Office 365
    AppId                     : aba7c072-2267-4031-8960-e7a2db6e0590
    AppOwnerTenantId          : 4a4076e0-a70f-41c6-b819-6f9c4a86df89
    AppRoleAssignmentRequired : False
    AppRoles                  : {}
    DisplayName               : FabrikamMail for Office 365
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {}
    PasswordCredentials       : {}
    PublisherName             : Fabrikam, Inc.
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {aba7c072-2267-4031-8960-e7a2db6e0590}
    Tags                      : {WindowsAzureActiveDirectoryIntegratedApp}

アプリに同意すると、次のものの間で Oauth2PermissionGrant リンクが作成されます。
  
- ユーザー オブジェクト
- クライアント アプリ ServicePrincipalName (SPN)
- リソース アプリ ServicePrincipalName (SPN)
- リソース アプリのアクセス許可  

FabrikamMail の場合、次のようになります。

    PS C:\> Get-AzureADUserOAuth2PermissionGrant -ObjectId ddiggle@aadpremiumlab.onmicrosoft.com | fl *
    
    ClientId    : a8b16333-851d-42e8-acd2-eac155849b37
    ConsentType : Principal
    ExpiryTime  : 05/15/2017 07:02:39 AM
    ObjectId    : M2OxqB2F6EKs0urBVYSbN5d7PzhUZz1NlH25COvLocbJjoxkUFfRQauryBKwBWet
    PrincipalId : 648c8ec9-5750-41d1-abab-c812b00567ad
    ResourceId  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    Scope       : full_access_as_user
    StartTime   : 01/01/0001 12:00:00 AM

(**ClientId** は、(作成されたばかりの) FabrikamMail のサービス プリンシパル オブジェクト ID です。**PrincipalId** は、(同意を行ったユーザーの) ユーザー オブジェクト ID です。**ResourceId** は、Exchange のサービス プリンシパル オブジェクト ID です。Scope は、Exchange の同意されたアクセス許可です)。

ユーザーの同意が許可されていない場合、その許可が必要なことを示す画面が表示されます。




<!--HONumber=Dec16_HO1-->


