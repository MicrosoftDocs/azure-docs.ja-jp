---
title: フェデレーション シングル サインオン用に構成されたギャラリー アプリケーションへのサインインに関する問題 | Microsoft Docs
description: Azure AD での SAML ベースのフェデレーション シングル サインオン用に構成されたアプリケーションにサインインするときに発生する特定のエラーのためのガイダンス
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: celested
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 623d684f701df8b1a7c4b84a2bd3840f039ad174
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312701"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>フェデレーション シングル サインオン用に構成されたギャラリー アプリケーションへのサインインに関する問題

以下のサインインに関する問題を解決するとき、次の提案に従ってより良い診断を行い、解決手順を自動化することをお勧めします。

- [My Apps Secure Browser Extension](access-panel-extension-problem-installing.md) をインストールします。これをインストールすると、Azure portal でテスト体験を使用するとき、Azure Active Directory (Azure AD) からより良い診断と解決策が得られます。
- Azure portal のアプリ構成ページにあるテスト体験を使用し、エラーを再現します。 [SAML ベースのシングル サインオン アプリケーションをデバッグする](../develop/howto-v1-debug-saml-sso-issues.md)方法に関するページを参照してください。


## <a name="application-not-found-in-directory"></a>アプリケーションがディレクトリ内に見つからない

*エラー AADSTS70001: 識別子 'https:\//contoso.com' を持つアプリケーションがディレクトリ内に見つかりませんでした*。

**考えられる原因**

SAML 要求でアプリケーションから Azure AD に送信された `Issuer` 属性が、Azure AD でアプリケーションに対して構成された識別子の値に一致していません。

**解決策**

SAML 要求内の `Issuer` 属性が Azure AD で構成された識別子の値に一致していることを確認します。 My Apps Secure Browser Extension を利用し、Azure portal で[テスト体験](../develop/howto-v1-debug-saml-sso-issues.md)を使用する場合、手動で次の手順を行う必要はありません。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

1.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** を選択して **[Azure Active Directory 拡張機能]** を開きます。

1.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

1.  Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** を選択します。

1.  **[すべてのアプリケーション]** を選択して、すべてのアプリケーションの一覧を表示します。

    ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

1.  シングル サインオンを構成するアプリケーションを選択します。

1.  アプリケーションを読み込んだら、**[基本的な SAML 構成]** を開きます。 [識別子] テキスト ボックス内の値が、エラーで表示されている識別子の値に一致していることを確認します。



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>応答アドレスが、アプリケーションに対して構成されている応答アドレスと一致しない。

*エラー AADSTS50011: 応答アドレス 'https:\//contoso.com' は、アプリケーションに対して構成された応答アドレスと一致しません*。

**考えられる原因**

SAML 要求の `AssertionConsumerServiceURL` 値が、応答 URL 値または Azure AD で構成されているパターンと一致しません。 SAML 要求内の `AssertionConsumerServiceURL` 値は、エラー メッセージに表示されている URL です。

**解決策**

SAML 要求の `AssertionConsumerServiceURL` 値が、Azure AD で構成されている応答 URL 値と一致していることを確認してください。 My Apps Secure Browser Extension を利用し、Azure portal で[テスト体験](../develop/howto-v1-debug-saml-sso-issues.md)を使用する場合、手動で次の手順を行う必要はありません。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

1.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** を選択して **[Azure Active Directory 拡張機能]** を開きます。

1.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

1.  Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** を選択します。

1.  **[すべてのアプリケーション]** を選択して、すべてのアプリケーションの一覧を表示します。

    ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

1.  シングル サインオンを構成するアプリケーションを選択します。

1.  アプリケーションを読み込んだら、**[基本的な SAML 構成]** を開きます。 [応答 URL] テキスト ボックスの値が SAML 要求内の `AssertionConsumerServiceURL` 値と一致していることを確認するか、一致するように値を更新します。    
    
Azure AD で応答 URL の値を更新し、その URL 値が、アプリケーションが SAML 要求に含めて送信する値に一致していれば、アプリケーションにサインインできます。

## <a name="user-not-assigned-a-role"></a>ユーザーにロールが割り当てられていない

*エラー AADSTS50105:サインイン ユーザー "brian\@contoso.com" はアプリケーションのロールに割り当てられていません*。

**考えられる原因**

このユーザーは、Azure AD 内のアプリケーションへのアクセスを許可されていません。

**解決策**

1 人以上のユーザーをアプリケーションに直接割り当てるには、次の手順に従います。 My Apps Secure Browser Extension を利用し、Azure portal で[テスト体験](../develop/howto-v1-debug-saml-sso-issues.md)を使用する場合、手動で次の手順を行う必要はありません。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

1.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** を選択して **[Azure Active Directory 拡張機能]** を開きます。

1.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

1.  Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** を選択します。

1.  **[すべてのアプリケーション]** を選択して、すべてのアプリケーションの一覧を表示します。

    ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

1.  アプリケーションの一覧から、ユーザーを割り当てるアプリケーションを選択します。

1.  アプリケーションが読み込まれたら、アプリケーションの左側のナビゲーション メニューから **[ユーザーとグループ]** を選択します。

1.  **[ユーザーとグループ]** 一覧の上部にある **[追加]** ボタンをクリックして、**[割り当ての追加]** ウィンドウを開きます。

1.  **[割り当ての追加]** ウィンドウから **[ユーザーとグループ]** セレクターを選択します。

1. **[名前または電子メール アドレスで検索]** 検索ボックスに、追加するユーザーのフル ネームまたは電子メール アドレスを入力します。

1. リスト内で**ユーザー**にポインターを合わせ、**チェック ボックス**を表示します。 ユーザーのプロファイル写真またはロゴの横にあるチェック ボックスをオンにして、ユーザーを **[選択済み]** リストに追加します。

1. **省略可能:** **複数のユーザーを追加する**場合は、別のフル ネームまたは電子メール アドレスを **[名前または電子メール アドレスで検索]** 検索ボックスに入力し、チェック ボックスをオンにして **[選択済み]** リストにユーザーを追加します。

1. ユーザーの選択が完了したら、**[選択]** ボタンをクリックして、アプリケーションに割り当てるユーザーとグループの一覧に追加します。

1. **省略可能:** **[割り当ての追加]** ウィンドウで **[ロールの選択]** セレクターをクリックして、選択したユーザーに割り当てるロールを選択します。

1. **[割り当て]** ボタンをクリックして、選択したユーザーにアプリケーションを割り当てます。

少し待つと、選択したユーザーは、ソリューションの説明セクションで説明されている方法を使用してこれらのアプリケーションを起動できるようになります。

## <a name="not-a-valid-saml-request"></a>有効な SAML 要求ではない

*エラー AADSTS75005:この要求は有効な Saml2 プロトコル メッセージではありません。*

**考えられる原因**

Azure AD は、シングル サインオン用のアプリケーションによって送信された SAML 要求をサポートしていません。 いくつかの一般的な問題は次のとおりです。

-   SAML 要求に必要なフィールドがない
-   SAML 要求のエンコード方法

**解決策**

1. SAML 要求をキャプチャします。 [Azure AD でのアプリケーションへの SAML ベースのシングル サインオンをデバッグする方法](../develop/howto-v1-debug-saml-sso-issues.md)に関するチュートリアルに従って、SAML 要求をキャプチャする方法を確認します。

1. アプリケーション ベンダーに連絡し、次の情報を伝えます。

   -   SAML 要求

   -   [Azure AD でのシングル サインオンの SAML プロトコルの要件](../develop/single-sign-on-saml-protocol.md)

アプリケーション ベンダーが、シングル サインオン用の Azure AD SAML 実装をサポートしていることを確認します。

## <a name="misconfigured-application"></a>アプリケーションが正しく構成されていない

*エラー AADSTS650056:アプリケーションが正しく構成されていないこの場合は、次のいずれかの原因が考えられます。クライアントは、クライアントのアプリケーション登録の要求されたアクセス許可に "AAD グラフ" のアクセス許可を記載していません。あるいは、管理者がテナントで同意していません。あるいは、要求のアプリケーション識別子を見て、構成したクライアント アプリケーション識別子に一致することを確認してください。構成を修正するか、テナントの代わりに同意するように管理者に連絡してください*。

**考えられる原因**

SAML 要求でアプリケーションから Azure AD に送信された `Issuer` 属性が、Azure AD でアプリケーションに対して構成された識別子の値に一致していません。

**解決策**

SAML 要求内の `Issuer` 属性が Azure AD で構成された識別子の値に一致していることを確認します。 My Apps Secure Browser Extension を利用し、Azure portal で[テスト体験](../develop/howto-v1-debug-saml-sso-issues.md)を使用する場合、手動で次の手順を行う必要はありません。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

1.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** を選択して **[Azure Active Directory 拡張機能]** を開きます。

1.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

1.  Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** を選択します。

1.  **[すべてのアプリケーション]** を選択して、すべてのアプリケーションの一覧を表示します。

    ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

1.  シングル サインオンを構成するアプリケーションを選択します。

1.  アプリケーションを読み込んだら、**[基本的な SAML 構成]** を開きます。 [識別子] テキスト ボックス内の値が、エラーで表示されている識別子の値に一致していることを確認します。


## <a name="certificate-or-key-not-configured"></a>証明書またはキーが構成されていない

*エラー AADSTS50003:署名キーが構成されていません。*

**考えられる原因**

アプリケーション オブジェクトが壊れており、アプリケーション用に構成された証明書を Azure AD が認識しません。

**解決策**

証明書を削除して新しい証明書を作成するには、次の手順に従ってください。

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

1. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

1. フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

1. Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** を選択します。

1. **[すべてのアプリケーション]** を選択して、すべてのアプリケーションの一覧を表示します。

    ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

1. シングル サインオンを構成するアプリケーションを選択します。

1. アプリケーションが読み込まれたら、アプリケーションの左側のナビゲーション メニューから **[シングル サインオン]** をクリックします。

1. **[SAML 署名証明書]** セクションの下にある **[新しい証明書の作成]** を選択します。

1. 有効期限を選択し、**[保存]** をクリックします。

1. **[新しい証明書をアクティブにする]** をオンにして、アクティブな証明書をオーバーライドします。 次に、ウィンドウの上部にある **[保存]** をクリックし、ロールオーバー証明書をアクティブにすることを受け入れます。

1. **[SAML 署名証明書]** セクションで、**[削除]** をクリックして、**未使用**の証明書を削除します。

## <a name="saml-request-not-present-in-the-request"></a>SAML 要求が要求にない

*エラー AADSTS750054:SAML リダイレクト バインディング用の HTTP 要求に、SAMLRequest または SAMLResponse がクエリ文字列のパラメーターとしてある必要があります。*

**考えられる原因**

Azure AD によって、HTTP 要求の URL パラメーター内から SAML 要求が識別されませんでした。 これは、Azure AD に SAML 要求を送信するとき、アプリケーションがが HTTP リダイレクト バインディングを使用していない場合に発生することがあります。

**解決策**

アプリケーションが、HTTP リダイレクト バインディングを使用して、エンコードされて location ヘッダーに含められた SAML 要求を送信する必要があります。 これの実装方法の詳細については、[SAML プロトコルの仕様書](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf)の HTTP リダイレクト バインディングに関するセクションを参照してください。

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD が間違ったエンドポイントにトークンを送信する

**考えられる原因**

シングル サインオン中に、サインイン要求に明示的な応答 URL (Assertion Consumer Service URL) が含まれていない場合、Azure AD はそのアプリケーションに対して構成されているいずれかの URL を選択します。 アプリケーションに明示的な応答 URL が構成されている場合でも、ユーザーは https://127.0.0.1:444 にリダイレクトされる可能性があります。 

アプリケーションがギャラリー以外のアプリとして追加されたときに、Azure Active Directory によって、既定値としてこの応答 URL が作成されていました。 この動作が変更され、Azure Active Directory によってこの URL が既定で追加されることはなくなりました。 

**解決策**

アプリケーションに構成されている未使用の応答 URL を削除します。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** を選択して **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4.  Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** を選択します。

5.  **[すべてのアプリケーション]** を選択して、すべてのアプリケーションの一覧を表示します。

    ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

6.  シングル サインオンを構成するアプリケーションを選択します。

7.  アプリケーションを読み込んだら、**[基本的な SAML 構成]** を開きます。 **[応答 URL (Assertion Consumer Service URL)]** で、システムによって作成された未使用または既定の応答 URL を削除します。 たとえば、「 `https://127.0.0.1:444/applications/default.aspx` 」のように入力します。

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>アプリケーションに送信される SAML 要求をカスタマイズする際の問題

アプリケーションに送信される SAML 属性要求をカスタマイズする方法については、「[Azure Active Directory での要求マッピング](../develop/active-directory-claims-mapping.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

[Azure AD のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](../develop/howto-v1-debug-saml-sso-issues.md)
