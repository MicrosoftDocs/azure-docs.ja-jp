---
title: SAML ベースのシングル サインオンで構成されたアプリへのサインインに関する問題
description: Azure Active Directory での SAML ベースのフェデレーション シングル サインオン用に構成したアプリケーションにサインインするときに発生する特定のエラーに関するガイダンス
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/18/2019
ms.author: kenwith
ms.reviewer: luleon, asteen
ms.custom: contperf-fy21q2
ms.openlocfilehash: d5a90909345599b2389d1752e00a7bc917d06822
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430413"
---
# <a name="problems-signing-in-to-saml-based-single-sign-on-configured-apps"></a>SAML ベースのシングル サインオンで構成されたアプリへのサインインに関する問題
以下のサインインに関する問題を解決する場合は、より優れた診断、および解決手順の自動化に向けて、次を行うことをお勧めします。

- [My Apps Secure Browser Extension](my-apps-deployment-plan.md) をインストールします。これをインストールすると、Azure portal でテスト体験を使用するとき、Azure Active Directory (Azure AD) からより良い診断と解決策が得られます。
- Azure portal のアプリ構成ページにあるテスト体験を使用し、エラーを再現します。 [SAML ベースのシングル サインオン アプリケーションをデバッグする](./debug-saml-sso-issues.md)方法に関するページを参照してください。

My Apps Secure Browser Extension を使用して、Azure portal で[テスト体験](./debug-saml-sso-issues.md)を使用する場合は、[SAML ベースのシングル サインオンの構成] ページを開くための次の手順を、手動で行う必要はありません。

[SAML ベースのシングル サインオンの構成] ページを開くには、次の手順を行います。
1.  [**Azure portal**](https://portal.azure.com/) を開き、**グローバル管理者**、または **共同管理者** としてサインインします。
1.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** を選択して **[Azure Active Directory 拡張機能]** を開きます。
1.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、 **[Azure Active Directory]** 項目を選択します。
1.  Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** を選択します。
1.  **[すべてのアプリケーション]** を選択して、すべてのアプリケーションの一覧を表示します。

    ここに表示したいアプリケーションが表示されない場合は、 **[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、 **[表示]** オプションを **[すべてのアプリケーション]** に設定します。

1.  シングル サインオンを構成するアプリケーションを選択します。
1. アプリケーションが読み込まれたら、アプリケーションの左側にあるナビゲーション メニューで **[シングル サインオン]** をクリックします。
1. [SAML ベースの SSO] を選択します。

## <a name="application-not-found-in-directory"></a>アプリケーションがディレクトリ内に見つからない
`Error AADSTS70001: Application with Identifier 'https:\//contoso.com' was not found in the directory.`

**考えられる原因**

SAML 要求でアプリケーションから Azure AD に送信された `Issuer` 属性が、Azure AD でアプリケーションに対して構成された識別子の値に一致していません。

**解決策**

SAML 要求内の `Issuer` 属性が Azure AD で構成された識別子の値に一致していることを確認します。

[SAML ベースのシングル サインオンの構成] ページの **[基本的な SAML 構成]** セクションで、[識別子] テキストボックスの値が、エラーに表示される識別子の値と一致していることを確認します。

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>応答アドレスが、アプリケーションに対して構成されている応答アドレスと一致しない。
`Error AADSTS50011: The reply URL specified in the request does not match the reply URLs configured for the application: '{application identifier}'.`

**考えられる原因**

SAML 要求の `AssertionConsumerServiceURL` 値が、応答 URL 値または Azure AD で構成されているパターンと一致しません。 SAML 要求内の `AssertionConsumerServiceURL` 値は、エラー メッセージに表示されている URL です。

**解決策**

SAML 要求の `AssertionConsumerServiceURL` 値が、Azure AD で構成されている応答 URL 値と一致していることを確認してください。 

[応答 URL] テキスト ボックスの値が SAML 要求内の `AssertionConsumerServiceURL` 値と一致していることを確認するか、一致するように値を更新します。   

Azure AD で応答 URL の値を更新し、その URL 値が、アプリケーションが SAML 要求に含めて送信する値に一致していれば、アプリケーションにサインインできます。

## <a name="user-not-assigned-a-role"></a>ユーザーにロールが割り当てられていない
`Error AADSTS50105: The signed in user 'brian\@contoso.com' is not assigned to a role for the application.`

**考えられる原因**

このユーザーは、Azure AD 内のアプリケーションへのアクセスを許可されていません。

**解決策**

1 人以上のユーザーをアプリケーションに直接割り当てるには、[アプリへのユーザーの割り当てについてのクイックスタート](add-application-portal-assign-users.md)に関するページを参照してください。

## <a name="not-a-valid-saml-request"></a>有効な SAML 要求ではない
`Error AADSTS75005: The request is not a valid Saml2 protocol message.`

**考えられる原因**

Azure AD は、シングル サインオン用のアプリケーションによって送信された SAML 要求をサポートしていません。 いくつかの一般的な問題は次のとおりです。
- SAML 要求に必要なフィールドがない
- SAML 要求のエンコード方法

**解決策**

1. SAML 要求をキャプチャします。 [Azure AD でのアプリケーションへの SAML ベースのシングル サインオンをデバッグする方法](./debug-saml-sso-issues.md)に関するチュートリアルに従って、SAML 要求をキャプチャする方法を確認します。
1. アプリケーション ベンダーに連絡し、次の情報を伝えます。
    - SAML 要求
    - [Azure AD でのシングル サインオンの SAML プロトコルの要件](../develop/single-sign-on-saml-protocol.md)

アプリケーション ベンダーが、シングル サインオン用の Azure AD SAML 実装をサポートしていることを確認します。

## <a name="misconfigured-application"></a>アプリケーションが正しく構成されていない
`Error AADSTS650056: Misconfigured application. This could be due to one of the following: The client has not listed any permissions in the requested permissions in the client's application registration. Or, The admin has not consented in the tenant. Or, Check the application identifier in the request to ensure it matches the configured client application identifier. Please contact your admin to fix the configuration or consent on behalf of the tenant.`

**考えられる原因**

SAML 要求でアプリケーションから Azure AD に送信された `Issuer` 属性が、Azure AD でアプリケーションに対して構成された識別子の値に一致していません。

**解決策**

SAML 要求内の `Issuer` 属性が Azure AD で構成された識別子の値に一致していることを確認します。 

[識別子] テキスト ボックス内の値が、エラーで表示されている識別子の値に一致していることを確認します。

## <a name="certificate-or-key-not-configured"></a>証明書またはキーが構成されていない
`Error AADSTS50003: No signing key configured.`

**考えられる原因**

アプリケーション オブジェクトが壊れており、アプリケーション用に構成された証明書を Azure AD が認識しません。

**解決策**

証明書を削除して新しい証明書を作成するには、次の手順に従ってください。
1. SAML ベースの SSO 構成画面の **[SAML 署名証明書]** セクションで、 **[新しい証明書の作成]** を選択します。
1. 有効期限を選択し、 **[保存]** をクリックします。
1. **[新しい証明書をアクティブにする]** をオンにして、アクティブな証明書をオーバーライドします。 次に、ウィンドウの上部にある **[保存]** をクリックし、ロールオーバー証明書をアクティブにすることを受け入れます。
1. **[SAML 署名証明書]** セクションで、 **[削除]** をクリックして、**未使用** の証明書を削除します。

## <a name="saml-request-not-present-in-the-request"></a>SAML 要求が要求にない
`Error AADSTS750054: SAMLRequest or SAMLResponse must be present as query string parameters in HTTP request for SAML Redirect binding.`

**考えられる原因**

Azure AD によって、HTTP 要求の URL パラメーター内から SAML 要求が識別されませんでした。 これは、Azure AD に SAML 要求を送信するとき、アプリケーションがが HTTP リダイレクト バインディングを使用していない場合に発生することがあります。

**解決策**

アプリケーションが、HTTP リダイレクト バインディングを使用して、エンコードされて location ヘッダーに含められた SAML 要求を送信する必要があります。 これの実装方法の詳細については、[SAML プロトコルの仕様書](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf)の HTTP リダイレクト バインディングに関するセクションを参照してください。

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD が間違ったエンドポイントにトークンを送信する
**考えられる原因**

シングル サインオン中に、サインイン要求に明示的な応答 URL (Assertion Consumer Service URL) が含まれていない場合、Azure AD によって、そのアプリケーションに対して構成されているいずれかの応答 URL が選択されます。 アプリケーションに明示的な応答 URL が構成されている場合でも、ユーザーは https://127.0.0.1:444 にリダイレクトされる可能性があります。 

アプリケーションがギャラリー以外のアプリとして追加されたときに、Azure Active Directory によって、既定値としてこの応答 URL が作成されていました。 この動作が変更され、Azure Active Directory によってこの URL が既定で追加されることはなくなりました。 

**解決策**

アプリケーションに構成されている未使用の応答 URL を削除します。

[SAML ベースのシングル サインオンの構成] ページの **[応答 URL (Assertion Consumer Service URL)]** で、システムによって作成された未使用の URL または既定の応答 URL を削除します。 たとえば、「 `https://127.0.0.1:444/applications/default.aspx` 」のように入力します。


## <a name="authentication-method-by-which-the-user-authenticated-with-the-service-doesnt-match-requested-authentication-method"></a>サービスでのユーザーの認証に使用された認証方法が、要求された認証方法と一致しません
`Error: AADSTS75011 Authentication method by which the user authenticated with the service doesn't match requested authentication method 'AuthnContextClassRef'. `

**考えられる原因**

`RequestedAuthnContext` が SAML 要求に含まれている。 これは、アプリが `AuthnContextClassRef` によって指定された `AuthnContext` を想定していることを意味します。 しかし、ユーザーはアプリケーションにアクセスする前に既に認証されており、その以前の認証で使用された `AuthnContext` (認証方法) が要求されているものと異なります。 たとえば、myapps と WIA へのフェデレーション ユーザーのアクセスが行われたとします。 `AuthnContextClassRef` は `urn:federation:authentication:windows` になります。 AAD では、新しい認証要求が実行されず、IdP (このケースでは ADFS またはその他のフェデレーション サービス) によって、それを経由して渡された認証コンテキストが使用されます。 そのため、アプリで `urn:federation:authentication:windows` 以外が要求されると、不一致が発生します。 もう 1 つのシナリオは、MultiFactor が使用された場合です (`'X509, MultiFactor`)。

**解像度**


`RequestedAuthnContext` は省略可能な値です。 次に、可能であれば、アプリケーションにそれを削除できるかどうかを確認します。

別のオプションとして、`RequestedAuthnContext` が受け入れられるようにします。 これは、新しい認証を要求することによって行われます。 これを行うことにより、SAML 要求が処理されるときに、新しい認証が行われ、`AuthnContext` が受け入れられます。 新しい認証を要求するために、SAML 要求にはほとんど `forceAuthn="true"`値が含まれています。 



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>アプリケーションに送信される SAML 要求をカスタマイズする際の問題
アプリケーションに送信される SAML 属性要求をカスタマイズする方法については、「[Azure Active Directory での要求マッピング](../develop/active-directory-claims-mapping.md)」をご覧ください。

## <a name="errors-related-to-misconfigured-apps"></a>正しく構成されていないアプリに関連するエラー
ポータルの構成がアプリの構成と一致していることを確認します。 具体的には、クライアント/アプリケーション ID、応答 URL、クライアント シークレット/キー、およびアプリ ID URI を比較します。

コード内でアクセスを要求しているリソースを、 **[Required Resources]** (必要なリソース) タブの構成済みアクセス許可と比較して、自分が構成したリソースのみを要求していることを確認します。

## <a name="next-steps"></a>次のステップ
- [アプリケーション管理のクイックスタート シリーズ](add-application-portal-assign-users.md)
- [Azure AD のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](./debug-saml-sso-issues.md)
- [Azure AD でのシングル サインオンの SAML プロトコルの要件](../develop/single-sign-on-saml-protocol.md)
