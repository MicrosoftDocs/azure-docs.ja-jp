---
title: Azure AD Connect - AD FS の管理とカスタマイズ | Microsoft Docs
description: Azure AD Connect を使用した AD FS の管理と、Azure AD Connect および PowerShell を使用したユーザー AD FS サインイン エクスペリエンスのカスタマイズ。
keywords: AD FS, ADFS, AD FS 管理, AAD Connect, Connect, サインイン, AD FS カスタマイズ, 信頼の修復, O365, フェデレーション, 証明書利用者
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 021e13dafcc659337d4096a068e224312e69db1b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60353633"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Azure AD Connect を使用した Active Directory フェデレーション サービスの管理とカスタマイズ
この記事では、Azure Active Directory (Azure AD) Connect を使用して、Active Directory フェデレーション サービス (AD FS) を管理およびカスタマイズする方法について説明します。 また、AD FS ファームの完全な構成のために必要となる可能性のある他の一般的な AD FS タスクについても説明します。

| トピック | 内容 |
|:--- |:--- |
| **AD FS の管理** | |
| [信頼を修復する](#repairthetrust) |Office 365 とのフェデレーション信頼を修復する方法 |
| [代替ログイン ID を使用して Azure AD とフェデレーションする](#alternateid) | 代替ログイン ID を使用してフェデレーションを構成する  |
| [AD FS サーバーを追加する](#addadfsserver) |追加の AD FS サーバーで AD FS ファームを拡張する方法 |
| [AD FS Web アプリケーション プロキシ サーバーを追加する](#addwapserver) |追加の Web アプリケーション プロキシ (WAP) サーバーで AD FS ファームを拡張する方法 |
| [フェデレーション ドメインを追加します](#addfeddomain) |フェデレーション ドメインを追加する方法 |
| [SSL 証明書の更新](how-to-connect-fed-ssl-update.md)| AD FS ファームの SSL 証明書を更新する方法 |
| **AD FS のカスタマイズ** | |
| [カスタムの会社のロゴまたはイラストを追加する](#customlogo) |会社のロゴとイラストを使用して AD FS サインイン ページをカスタマイズする方法 |
| [サインインの説明を追加する](#addsignindescription) |サインイン ページの説明を追加する方法 |
| [AD FS の要求規則を変更する](#modclaims) |さまざまなフェデレーション シナリオ向けに AD FS 要求を変更する方法 |

## <a name="manage-ad-fs"></a>AD FS の管理
Azure AD Connect では、Azure AD Connect ウィザードを使用することで、ユーザーの介入を最小限に抑えてさまざまな AD FS 関連のタスクを実行できます。 ウィザードを実行して Azure AD Connect のインストールを完了したら、もう一度ウィザードを実行してその他のタスクを実行できます。

## <a name="repairthetrust"></a>信頼を修復する 
Azure AD Connect を使用して、AD FS と Azure AD の信頼の現在の正常性を確認し、信頼を修復するための適切なアクションを実行できます。 Azure AD と AD FS の信頼を修復するには、次の手順に従います。

1. 追加タスクの一覧から **[AAD と ADFS 信頼を修復します]** を選択します。
   ![[AAD と ADFS 信頼を修復します]](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. **[Azure AD に接続]** ページで、Azure AD の全体管理者の資格情報を入力し、 **[次へ]** をクリックします。
   ![Azure への接続](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. **[リモート アクセスの資格情報]** ページで、ドメイン管理者の資格情報を入力します。

   ![Remote access credentials](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    **[次へ]** をクリックすると、Azure AD Connect によって証明書の正常性が確認され、問題がある場合はその問題が表示されます。

    ![State of certificates](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    **[構成の準備完了]** ページに、信頼を修復するために実行されるアクションの一覧が表示されます。

    ![構成の準備完了](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. **[インストール]** をクリックして信頼を修復します。

> [!NOTE]
> Azure AD Connect では、自己署名されている証明書に対してのみ修復などのアクションを実行できます。 サード パーティの証明書を Azure AD Connect で修復することはできません。

## <a name="alternateid"></a>代替 ID を使用して Azure AD とフェデレーションする 
オンプレミスのユーザー プリンシパル名 (UPN) とクラウドのユーザー プリンシパル名を同じにすることをお勧めします。 オンプレミスの UPN がルーティング不可能なドメイン (Contoso.local など) を使用している場合、 またはローカル アプリケーションの依存関係のために変更できない場合は、代替ログイン ID を設定することをお勧めします。 代替ログイン ID を使用すると、ユーザーがメールなどの UPN 以外の属性でサインイン可能になるサインイン エクスペリエンスを構成できます。 Azure AD Connect でユーザー プリンシパル名として選択した名前が、Active Directory の userPrincipalName 属性の既定値となります。 他の属性をユーザー プリンシパル名として選択し、AD FS を使用してフェデレーションを行っている場合、Azure AD Connect では、代替ログイン ID の AD FS が構成されます。 別の属性をユーザー プリンシパル名として選択した例を、以下に示します。

![代替 ID 属性の選択](./media/how-to-connect-fed-management/attributeselection.png)

AD FS 用の代替ログイン ID の構成は、主に 2 つの手順で構成されています。
1. **適切な発行要求セットの構成**: Azure AD 証明書利用者信頼の発行要求規則は、選択された UserPrincipalName 属性をユーザーの代替 ID として使用するように変更されます。
2. **AD FS の構成での代替ログイン ID の有効化**: AD FS が代替 ID を使用して、適切なフォレスト内でユーザーを検索できるように、AD FS の構成が更新されます。 この構成は、Windows Server 2012 R2 (KB2919355) 以降で動作する AD FS に対応しています。 AD FS サーバーが 2012 R2 である場合は、Azure AD Connect が必要な KB が存在するかどうかを確認します。 KB が検出されない場合、構成の完了後に、次のような警告が表示されます。

    ![2012R2 の KB の欠落を知らせる警告](./media/how-to-connect-fed-management/kbwarning.png)

    KB が欠落している場合、構成を修正するには、必要な [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590)をインストールしたあと、[[AAD と AD FS 信頼を修復します]](#repairthetrust) を使用して信頼を修復します。

> [!NOTE]
> 代替 ID および手動構成の手順の詳細については、「[代替ログイン ID の構成](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)」を参照してください。

## <a name="addadfsserver"></a>AD FS サーバーを追加する 

> [!NOTE]
> Azure AD Connect で AD FS サーバーを追加するには、PFX 証明書が必要です。 そのため、この操作を実行できるのは、Azure AD Connect を使用して AD FS ファームを構成した場合のみです。

1. **[追加のフェデレーション サーバーをデプロイします]** を選択し、 **[次へ]** をクリックします。

   ![Additional federation server](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. **[Azure AD に接続]** ページで、Azure AD の全体管理者の資格情報を入力し、 **[次へ]** をクリックします。

   ![Azure への接続](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. ドメイン管理者の資格情報を入力します。

   ![Domain administrator credentials](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Azure AD Connect で新しい AD FS ファームを構成中に指定した PFX ファイルのパスワードを入力するよう求められます。 **[パスワードの入力]** をクリックし、PFX ファイルのパスワードを入力します。

   ![Certificate password](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![[SSL 証明書の指定]](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. **[AD FS サーバー]** ページで、AD FS ファームに追加するサーバーの名前または IP アドレスを入力します。

   ![AD FS servers](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. **[次へ]** をクリックし、最後の **[構成]** ページに進みます。 Azure AD Connect によって AD FS ファームへのサーバーの追加が完了すると、接続を確認するためのオプションが表示されます。

   ![[構成の準備完了]](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![インストールの完了](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>AD FS WAP サーバーを追加する 

> [!NOTE]
> Azure AD Connect で WAP サーバーを追加するには、PFX 証明書が必要です。 そのため、この操作を実行できるのは、Azure AD Connect を使用して AD FS ファームを構成した場合のみです。

1. 利用可能なタスクの一覧から **[Web アプリケーション プロキシのデプロイ]** を選択します。

   ![[Web アプリケーション プロキシのデプロイ]](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Azure の全体管理者の資格情報を入力します。

   ![Azure への接続](./media/how-to-connect-fed-management/wapserver2.PNG)

3. **[SSL 証明書の指定]** ページで、Azure AD Connect で AD FS ファームを構成したときに指定した PFX ファイルのパスワードを入力します。
   ![Certificate password](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![[SSL 証明書の指定]](./media/how-to-connect-fed-management/WapServer4.PNG)

4. WAP サーバーとして追加するサーバーを追加します。 WAP サーバーがドメインに参加していない場合もあるため、追加するサーバーの管理資格情報を入力するよう求められます。

   ![Administrative server credentials](./media/how-to-connect-fed-management/WapServer5.PNG)

5. **[プロキシ信頼資格情報]** ページで、プロキシ信頼を構成して AD FS ファームのプライマリ サーバーにアクセスするために管理資格情報を入力します。

   ![プロキシ信頼資格情報](./media/how-to-connect-fed-management/WapServer6.PNG)

6. **[構成の準備完了]** ページに、実行されるアクションの一覧が表示されます。

   ![構成の準備完了](./media/how-to-connect-fed-management/WapServer7.PNG)

7. **[インストール]** をクリックして構成を終了します。 構成が完了すると、サーバーへの接続を確認するためのオプションが表示されます。 **[確認]** をクリックして接続を確認します。

   ![インストールの完了](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="addfeddomain"></a>フェデレーション ドメインを追加する 

Azure AD Connect を使用すると、Azure AD とのフェデレーションを設定するドメインを簡単に追加できます。 Azure AD Connect によりフェデレーション用のドメインが追加され、Azure AD とのフェデレーションを設定したドメインが複数ある場合に発行者を正しく反映するように要求規則が変更されます。

1. フェデレーション ドメインを追加するには、 **[Azure AD ドメインを追加します]** タスクを選択します。

   ![Additional Azure AD domain](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. ウィザードの次のページで、Azure AD の全体管理者の資格情報を入力します。

   ![Azure への接続](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. **[リモート アクセスの資格情報]** ページで、ドメイン管理者の資格情報を入力します。

   ![Remote access credentials](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. 次のページに、オンプレミスのディレクトリとのフェデレーションに使用できる Azure AD ドメインの一覧が表示されます。 一覧からドメインを選択します。

   ![Azure AD domain](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    ドメインを選択すると、ウィザードによって今後実行されるアクションと構成の影響に関する適切な情報が表示されます。 Azure AD でまだ確認されていないドメインを選択すると、ドメインの確認に役立つ情報が表示される場合もあります。 詳細については、「 [Azure Active Directory へのカスタム ドメイン名の追加](../active-directory-domains-add-azure-portal.md) 」を参照してください。

5. **[次へ]** をクリックします。 **[構成の準備完了]** ページに、Azure AD Connect によって実行されるアクションの一覧が表示されます。 **[インストール]** をクリックして構成を終了します。

   ![構成の準備完了](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> 追加したフェデレーション ドメインのユーザーは、同期後に Azure AD にログインできるようになります。

## <a name="ad-fs-customization"></a>AD FS のカスタマイズ
以下のセクションでは、AD FS サインイン ページをカスタマイズする際に必要になる可能性のある一般的なタスクについて詳しく説明します。

## <a name="customlogo"></a>カスタムの会社のロゴまたはイラストを追加する 
**サインイン** ページに表示される会社のロゴを変更するには、次の Windows PowerShell コマンドレットと構文を使用します。

> [!NOTE]
> ロゴのサイズについては、260 x 35 \@ 96 dpi、ファイル サイズ 10 KB 以下が推奨されます。

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> *TargetName* パラメーターは必須です。 AD FS と共にリリースされている既定のテーマの名前は Default です。

## <a name="addsignindescription"></a>サインインの説明を追加する 
**サインイン ページ**にサインイン ページの説明を追加するには、次の Windows PowerShell コマンドレットと構文を使用します。

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>AD FS の要求規則を変更する 
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

この規則では、ユーザーの **ms-ds-concistencyguid** が設定されていない場合に **useguid** に設定される、**idflag** という一時フラグを定義します。 この規則は、AD FS では空の要求が許可されないという事実に基づいています。 [http://contoso.com/ws/2016/02/identity/claims/objectguid](http://contoso.com/ws/2016/02/identity/claims/objectguid ) クレームを追加するとき、 http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid ルール 1 では、その値がユーザーに設定される場合にだけ、**msdsconsistencyguid** 要求で終了します。 値が設定されていない場合、AD FS によって空の値を持つことになると判断され、すぐに削除されます。 すべてのオブジェクトは **objectGuid**を持つため、規則 1 が実行された後には、常に要求が存在することになります。

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

[新しいフェデレーション ドメインの追加](how-to-connect-fed-management.md#addfeddomain)に関するセクションで説明しているように、Azure AD Connect を使用して、フェデレーション対象の複数のドメインを追加できます。 Azure AD Connect のバージョン 1.1.553.0 および最新版では、発行者 ID に応じた正しい要求規則が自動的に作成されます。 Azure AD Connect のバージョン 1.1.553.0 または最新版を利用できない場合には、Azure AD の証明書利用者信頼の正しい要求規則の生成および設定に [Azure AD RPT Claim Rules](https://aka.ms/aadrptclaimrules) ツールを使用することをお勧めします。

## <a name="next-steps"></a>次の手順
[ユーザー サインイン オプション](plan-connect-user-signin.md)の詳細を確認します。
