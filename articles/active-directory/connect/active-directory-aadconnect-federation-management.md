---
title: "Azure AD Connect による Active Directory フェデレーション サービスの管理とカスタマイズ | Microsoft Docs"
description: "Azure AD Connect を使用した AD FS の管理と、Azure AD Connect および PowerShell を使用したユーザー AD FS サインイン エクスペリエンスのカスタマイズ。"
keywords: "AD FS, ADFS, AD FS 管理, AAD Connect, Connect, サインイン, AD FS カスタマイズ, 信頼の修復, O365, フェデレーション, 証明書利用者"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: b4b5e1af6c03e1124de78308cab1dad86d06641e
ms.lasthandoff: 03/28/2017


---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Azure AD Connect を使用した Active Directory フェデレーション サービスの管理とカスタマイズ
この記事では、Azure Active Directory (Azure AD) Connect を使用して、Active Directory フェデレーション サービス (AD FS) を管理およびカスタマイズする方法について説明します。 また、AD FS ファームの完全な構成のために必要となる可能性のある他の一般的な AD FS タスクについても説明します。

| トピック | 内容 |
|:--- |:--- |
| **AD FS の管理** | |
| [信頼を修復する](#repairthetrust) |Office 365 とのフェデレーション信頼を修復する方法 |
| [AD FS サーバーを追加する](#addadfsserver) |追加の AD FS サーバーで AD FS ファームを拡張する方法 |
| [AD FS Web アプリケーション プロキシ サーバーを追加する](#addwapserver) |追加の Web アプリケーション プロキシ (WAP) サーバーで AD FS ファームを拡張する方法 |
| [フェデレーション ドメインを追加します](#addfeddomain) |フェデレーション ドメインを追加する方法 |
| [SSL 証明書の更新](active-directory-aadconnectfed-ssl-update.md)| AD FS ファームの SSL 証明書を更新する方法 |
| **AD FS のカスタマイズ** | |
| [カスタムの会社のロゴまたはイラストを追加する](#customlogo) |会社のロゴとイラストを使用して AD FS サインイン ページをカスタマイズする方法 |
| [サインインの説明を追加する](#addsignindescription) |サインイン ページの説明を追加する方法 |
| [AD FS の要求規則を変更する](#modclaims) |さまざまなフェデレーション シナリオ向けに AD FS 要求を変更する方法 |

## <a name="manage-ad-fs"></a>AD FS の管理
Azure AD Connect では、Azure AD Connect ウィザードを使用することで、ユーザーの介入を最小限に抑えてさまざまな AD FS 関連のタスクを実行できます。 ウィザードを実行して Azure AD Connect のインストールを完了したら、もう一度ウィザードを実行してその他のタスクを実行できます。

## 信頼を修復する <a name=repairthetrust></a>
Azure AD Connect を使用して、AD FS と Azure AD の信頼の現在の正常性を確認し、信頼を修復するための適切なアクションを実行できます。 Azure AD と AD FS の信頼を修復するには、次の手順に従います。

1. 追加タスクの一覧から **[AAD と ADFS 信頼を修復します]** を選択します。
   ![[AAD と ADFS 信頼を修復します]](media/active-directory-aadconnect-federation-management/RepairADTrust1.PNG)

2. **[Azure AD に接続]** ページで、Azure AD の全体管理者の資格情報を入力し、**[次へ]** をクリックします。
   ![Azure への接続](media/active-directory-aadconnect-federation-management/RepairADTrust2.PNG)

3. **[リモート アクセスの資格情報]** ページで、ドメイン管理者の資格情報を入力します。

   ![Remote access credentials](media/active-directory-aadconnect-federation-management/RepairADTrust3.PNG)

    **[次へ]** をクリックすると、Azure AD Connect によって証明書の正常性が確認され、問題がある場合はその問題が表示されます。

    ![State of certificates](media/active-directory-aadconnect-federation-management/RepairADTrust4.PNG)

    **[構成の準備完了]** ページに、信頼を修復するために実行されるアクションの一覧が表示されます。

    ![[構成の準備完了]](media/active-directory-aadconnect-federation-management/RepairADTrust5.PNG)

4. **[インストール]** をクリックして信頼を修復します。

> [!NOTE]
> Azure AD Connect では、自己署名されている証明書に対してのみ修復などのアクションを実行できます。 サード パーティの証明書を Azure AD Connect で修復することはできません。

## AD FS サーバーを追加する <a name=addadfsserver></a>

> [!NOTE]
> Azure AD Connect で AD FS サーバーを追加するには、PFX 証明書が必要です。 そのため、この操作を実行できるのは、Azure AD Connect を使用して AD FS ファームを構成した場合のみです。

1. **[追加のフェデレーション サーバーをデプロイします]** を選択し、**[次へ]** をクリックします。

   ![Additional federation server](media/active-directory-aadconnect-federation-management/AddNewADFSServer1.PNG)

2. **[Azure AD に接続]** ページで、Azure AD の全体管理者の資格情報を入力し、**[次へ]** をクリックします。

   ![Azure への接続](media/active-directory-aadconnect-federation-management/AddNewADFSServer2.PNG)

3. ドメイン管理者の資格情報を入力します。

   ![Domain administrator credentials](media/active-directory-aadconnect-federation-management/AddNewADFSServer3.PNG)

4. Azure AD Connect で新しい AD FS ファームを構成中に指定した PFX ファイルのパスワードを入力するよう求められます。 **[パスワードの入力]** をクリックし、PFX ファイルのパスワードを入力します。

   ![Certificate password](media/active-directory-aadconnect-federation-management/AddNewADFSServer4.PNG)

    ![[SSL 証明書の指定]](media/active-directory-aadconnect-federation-management/AddNewADFSServer5.PNG)

5. **[AD FS サーバー]** ページで、AD FS ファームに追加するサーバーの名前または IP アドレスを入力します。

   ![AD FS servers](media/active-directory-aadconnect-federation-management/AddNewADFSServer6.PNG)

6. **[次へ]** をクリックし、最後の **[構成]** ページに進みます。 Azure AD Connect によって AD FS ファームへのサーバーの追加が完了すると、接続を確認するためのオプションが表示されます。

   ![[構成の準備完了]](media/active-directory-aadconnect-federation-management/AddNewADFSServer7.PNG)

    ![インストールの完了](media/active-directory-aadconnect-federation-management/AddNewADFSServer8.PNG)

## AD FS WAP サーバーを追加する<a name=addwapserver></a>

> [!NOTE]
> Azure AD Connect で WAP サーバーを追加するには、PFX 証明書が必要です。 そのため、この操作を実行できるのは、Azure AD Connect を使用して AD FS ファームを構成した場合のみです。

1. 利用可能なタスクの一覧から **[Web アプリケーション プロキシのデプロイ]** を選択します。

   ![[Web アプリケーション プロキシのデプロイ]](media/active-directory-aadconnect-federation-management/WapServer1.PNG)

2. Azure の全体管理者の資格情報を入力します。

   ![Azure への接続](media/active-directory-aadconnect-federation-management/wapserver2.PNG)

3. **[SSL 証明書の指定]** ページで、Azure AD Connect で AD FS ファームを構成したときに指定した PFX ファイルのパスワードを入力します。
   ![Certificate password](media/active-directory-aadconnect-federation-management/WapServer3.PNG)

    ![[SSL 証明書の指定]](media/active-directory-aadconnect-federation-management/WapServer4.PNG)

4. WAP サーバーとして追加するサーバーを追加します。 WAP サーバーがドメインに参加していない場合もあるため、追加するサーバーの管理資格情報を入力するよう求められます。

   ![Administrative server credentials](media/active-directory-aadconnect-federation-management/WapServer5.PNG)

5. **[プロキシ信頼資格情報]** ページで、プロキシ信頼を構成して AD FS ファームのプライマリ サーバーにアクセスするために管理資格情報を入力します。

   ![プロキシ信頼資格情報](media/active-directory-aadconnect-federation-management/WapServer6.PNG)

6. **[構成の準備完了]** ページに、実行されるアクションの一覧が表示されます。

   ![[構成の準備完了]](media/active-directory-aadconnect-federation-management/WapServer7.PNG)

7. **[インストール]** をクリックして構成を終了します。 構成が完了すると、サーバーへの接続を確認するためのオプションが表示されます。 **[確認]** をクリックして接続を確認します。

   ![インストールの完了](media/active-directory-aadconnect-federation-management/WapServer8.PNG)

## フェデレーション ドメインを追加します <a name=addfeddomain></a>

Azure AD Connect を使用すると、Azure AD とのフェデレーションを設定するドメインを簡単に追加できます。 Azure AD Connect によりフェデレーション用のドメインが追加され、Azure AD とのフェデレーションを設定したドメインが複数ある場合に発行者を正しく反映するように要求規則が変更されます。

1. フェデレーション ドメインを追加するには、 **[Azure AD ドメインを追加します]**タスクを選択します。

   ![Additional Azure AD domain](media/active-directory-aadconnect-federation-management/AdditionalDomain1.PNG)

2. ウィザードの次のページで、Azure AD の全体管理者の資格情報を入力します。

   ![Azure への接続](media/active-directory-aadconnect-federation-management/AdditionalDomain2.PNG)

3. **[リモート アクセスの資格情報]** ページで、ドメイン管理者の資格情報を入力します。

   ![Remote access credentials](media/active-directory-aadconnect-federation-management/additionaldomain3.PNG)

4. 次のページに、オンプレミスのディレクトリとのフェデレーションに使用できる Azure AD ドメインの一覧が表示されます。 一覧からドメインを選択します。

   ![Azure AD domain](media/active-directory-aadconnect-federation-management/AdditionalDomain4.PNG)

    ドメインを選択すると、ウィザードによって今後実行されるアクションと構成の影響に関する適切な情報が表示されます。 Azure AD でまだ確認されていないドメインを選択すると、ドメインの確認に役立つ情報が表示される場合もあります。 詳細については、「 [Azure Active Directory へのカスタム ドメイン名の追加](../active-directory-add-domain.md) 」を参照してください。

5. **[次へ]**をクリックします。 **[構成の準備完了]** ページに、Azure AD Connect によって実行されるアクションの一覧が表示されます。 **[インストール]** をクリックして構成を終了します。

   ![[構成の準備完了]](media/active-directory-aadconnect-federation-management/AdditionalDomain5.PNG)

## <a name="ad-fs-customization"></a>AD FS のカスタマイズ
以下のセクションでは、AD FS サインイン ページをカスタマイズする際に必要になる可能性のある一般的なタスクについて詳しく説明します。

## カスタムの会社のロゴまたはイラストを追加する <a name=customlogo></a>
**サインイン** ページに表示される会社のロゴを変更するには、次の Windows PowerShell コマンドレットと構文を使用します。

> [!NOTE]
> ロゴのサイズについては、260 x 35、96 dpi、ファイル サイズ 10 KB 以下が推奨されます。

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> *TargetName* パラメーターは必須です。 AD FS と共にリリースされている既定のテーマの名前は Default です。

## サインインの説明を追加する <a name=addsignindescription></a>
**サインイン ページ**にサインイン ページの説明を追加するには、次の Windows PowerShell コマンドレットと構文を使用します。

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## AD FS の要求規則を変更する <a name=modclaims></a>
AD FS では、カスタム要求規則の作成に使用できる、機能豊富な要求言語がサポートされています。 詳細については、「 [要求規則言語の役割](https://technet.microsoft.com/library/dd807118.aspx)」を参照してください。

以下のセクションでは、Azure AD と AD FS のフェデレーションに関連するいくつかのシナリオ向けにカスタム規則を作成する方法について説明します。

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>属性内に値が存在することを条件とする不変 ID
Azure AD Connect では、オブジェクトが Azure AD に同期されるときにソース アンカーとして使用される属性を指定できます。 カスタム属性内の値が空でない場合は、不変 ID 要求を発行することができます。

たとえば、**ms-ds-consistencyguid** をソース アンカーの属性として選択し、その属性に対応する値が含まれている場合は、**ImmutableID** を **ms-ds-consistencyguid** として発行するとします。 属性に対応する値が存在しない場合は、**objectGuid** を不変 ID として発行します。 以下のセクションで説明する方法により、カスタム要求規則のセットを作成することができます。

**規則 1: 属性を照会する**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

この規則では、Active Directory のユーザーの **ms-ds-consistencyguid** と **objectGuid** の値を照会します。 ストア名を、AD FS デプロイの適切なストア名に変更します。 また、**objectGuid** と **ms-ds-consistencyguid** の定義に従って、要求の種類をフェデレーションに適した要求の種類に変更します。

さらに、**issue** ではなく **add** を使用することで、エンティティの発行を追加せずに、値を中間値として使用できます。 要求は、不変 ID として使用する値が確定してから、後の規則内で発行します。

**規則 2: ユーザーの ms-ds-consistencyguid が存在するかどうかを確認する**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

この規則では、ユーザーの **ms-ds-concistencyguid** が設定されていない場合に **useguid** に設定される、**idflag** という一時フラグを定義します。 この規則は、AD FS では空の要求が許可されないという事実に基づいています。 したがって、規則 1 に要求 http://contoso.com/ws/2016/02/identity/claims/objectguid と http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid を追加した場合、最終的に **msdsconsistencyguid** 要求が発行されるのは、ユーザーにこの値が設定されている場合のみです。 値が設定されていない場合、AD FS によって空の値を持つことになると判断され、すぐに削除されます。 すべてのオブジェクトは **objectGuid**を持つため、規則 1 が実行された後には、常に要求が存在することになります。

**規則 3: ms-ds-consistencyguid が存在する場合にそれを不変 ID として発行する**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

この規則は、暗黙的な **存在** 確認です。 要求の値が存在する場合は、それを不変 ID として発行します。 前の例では、 **nameidentifier** 要求を使用しています。 この要求は、現在の環境の不変 ID に合わせて適切な種類の要求に変更する必要があります。

**規則 4: ms-ds-consistencyGuid が存在しない場合に objectGuid を不変 ID として発行する**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

この規則では、一時フラグの **idflag** を確認します。 このフラグの値に基づき、要求を発行するかどうかを決定します。

> [!NOTE]
> これらの規則の順序は重要です。

### <a name="sso-with-a-subdomain-upn"></a>サブドメイン UPN を使用した SSO
[新しいフェデレーション ドメインの追加](active-directory-aadconnect-federation-management.md#addfeddomain)に関するセクションで説明しているように、Azure AD Connect を使用して、フェデレーション対象の複数のドメインを追加できます。 フェデレーション ルート ドメインには子ドメインも含まれるため、発行者 ID がサブドメインではなく、ルート ドメインに対応するように、ユーザー プリンシパル名 (UPN) 要求を変更する必要があります。

既定で、発行者の ID の要求規則は次のように設定されます。

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Default issuer ID claim](media/active-directory-aadconnect-federation-management/issuer_id_default.png)

既定の規則では、単に UPN サフィックスを取得し、発行者 ID 要求でそれを使用します。 たとえば、John は sub.contoso.com のユーザーであり、contoso.com には Azure AD とのフェデレーションが設定されています。 John が Azure AD へのサインイン時にユーザー名として「john@sub.contoso.com」と入力すると、 AD FS の既定の発行者 ID 要求規則によって、次のように処理されます。

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**要求の値:** http://sub.contoso.com/adfs/services/trust/

発行者の要求値にルート ドメインのみが含まれるようにするには、次のように要求規則を変更します。

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>次のステップ
[ユーザー サインイン オプション](active-directory-aadconnect-user-signin.md)の詳細を確認します。

