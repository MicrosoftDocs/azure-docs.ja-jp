---
title: "Azure Active Directory B2C: 制限事項および制約事項 | Microsoft Docs"
description: "Azure Active Directory B2C に関する制限事項と制約事項のリスト"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: bryanla
ms.assetid: 04ec3310-98bb-4bb1-856d-ddc66913b390
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/17/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 610b72f9f979f76d2d9c5180cb51ddae82a3e823
ms.lasthandoff: 03/23/2017


---
# <a name="azure-active-directory-b2c-limitations-and-restrictions"></a>Azure Active Directory B2C: 制限事項および制約事項

Azure Active Directory (Azure AD) B2C には、まだサポートされていない機能があります。 これらの既知の問題や制限事項の多くは、今後解決される予定ですが、Azure AD B2C を使用して消費者向けアプリケーションを作成する場合は注意が必要です。

## <a name="issues-during-the-creation-of-azure-ad-b2c-tenants"></a>Azure AD B2C テナントの作成中の問題

[Azure AD B2C テナントの作成](active-directory-b2c-get-started.md)中に問題が発生した場合は、「[Azure AD テナントまたは Azure AD B2C テナントの作成 - 問題と解決策](active-directory-b2c-support-create-directory.md)」を参照してください。

既存の B2C テナントを削除し、同じドメイン名でそれを再作成する場合に、既知の問題があることに注意してください。 B2C テナントは異なるドメイン名で作成する必要があります。

## <a name="branding-issues-on-verification-email"></a>確認メールのブランドの問題

既定の確認メールには、Microsoft ブランドが含まれています。 これは将来削除される予定です。 現時点では、 [会社のブランド化機能](../active-directory/active-directory-add-company-branding.md)を使用して削除できます。

## <a name="branding-issues-on-local-account-sign-in-page-in-a-sign-in-policy"></a>サインイン ポリシーでのローカル アカウント サインイン ページのブランド表示の問題

サインイン ポリシーのローカル アカウント サインイン ページは、[会社のブランド化機能](../active-directory/active-directory-add-company-branding.md)のみを使用してカスタマイズできます。[ここ](active-directory-b2c-reference-ui-customization.md)に記載されたページ UI カスタマイズ機能を使用してカスタマイズすることはできません。 さらに、ユーザー名およびパスワード フィールドで利用できるラベルまたはプレースホルダーはありません。 この問題を回避するには、代替策として完全にカスタマイズ可能な "サインアップまたはサインイン ポリシー" を使用することをお勧めします。 サインイン ポリシーでのローカル アカウント サインイン ページ全体のカスタマイズに興味がある場合は、[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13062033-b2c-fully-customizable-sign-in-page) でその機能に投票してください。

## <a name="restrictions-on-applications"></a>アプリケーションに関する制限事項

Azure AD B2C では現在、次のタイプのアプリケーションはサポートされていません。 サポートされているアプリケーションのタイプについては、「 [Azure Active Directory B2C: アプリケーションの種類](active-directory-b2c-apps.md)」を参照してください。

### <a name="daemons--server-side-applications"></a>デーモン/サーバー側のアプリケーション

長時間実行されるプロセスを含んだアプリケーションや、ユーザーの介入なしで動作するアプリケーションも、セキュリティで保護されたリソース (Web API など) にアクセスする必要があります。 これらのアプリケーションは、 [OAuth 2.0 クライアント資格情報フロー](active-directory-b2c-reference-protocols.md)を使用することで、(お客様の委任 ID ではなく) アプリケーションの ID を使って認証を行い、トークンを取得することができます。 このフローは Azure AD B2C ではまだ使用できないので、現時点では、アプリケーションは対話的なコンシューマー サインイン フローが発生した後にのみ、トークンを取得できます。


### <a name="web-api-chains-on-behalf-of"></a>Web API チェーン (On-Behalf-Of)

Azure AD B2C によって保護された Web API から、同様に保護されたダウンストリームの別の Web API を呼び出す手法は、多くのアーキテクチャで使用されています。 このシナリオは、バックエンドの Web API から Microsoft オンライン サービス (Azure AD Graph API など) を呼び出すネイティブ クライアントでよく見られます。

このように Web API を連鎖的に呼び出すシナリオは、OAuth 2.0 Jwt Bearer Credential Grant (On-Behalf-Of フロー) を使用してサポートできます。 ただし、現時点では、Azure AD B2C に On-Behalf-Of フローは実装されていません。

## <a name="restrictions-on-reply-urls"></a>応答 URL に関する制限事項

現時点では、Azure AD B2C に登録されるアプリは、限られた応答 URL 値に制限されています。 Web アプリと Web サービスの応答 URL は `https` スキームで始まる必要があり、すべての応答 URL 値で 1 つの DNS ドメインを共有する必要があります。 たとえば、次の直接 URL のいずれかを使用する Web アプリを登録することはできません。

`https://login-east.contoso.com`
`https://login-west.contoso.com`

登録システムによって、既存の応答 URL の DNS 名全体と、追加しようとしている応答 URL の DNS 名が比較されます。 次のいずれかの条件に当てはまる場合、DNS 名を追加する要求は失敗します。

* 新しい応答 URL の DNS 名全体が、既存の応答 URL の DNS 名と一致しない場合。
* 新しい応答 URL の DNS 名全体が、既存の応答 URL のサブドメインではない場合。

たとえば、アプリで次の応答 URL を使用しているとします。

`https://login.contoso.com`

次のように、これに追加できます。

`https://login.contoso.com/new`

この場合、DNS は完全に一致します。 または、次を行うことができます。

`https://new.login.contoso.com`

この場合、login.contoso.com の DNS サブドメインを参照しています。 応答 URL として login-east.contoso.com と login-west.contoso.com を使用するアプリが必要な場合は、これらの応答 URL を次の順番で追加する必要があります。

`https://contoso.com`
`https://login-east.contoso.com`
`https://login-west.contoso.com`

後の 2 つの応答 URL を追加できるのは、これらが 1 つ目の contoso.com という応答 URL のサブドメインであるためです。 この制限は、今後のリリースで解消される予定です。

Azure AD B2C にアプリを登録する方法については、[Azure Active Directory B2C にアプリケーションを登録する方法](active-directory-b2c-app-registration.md)に関する記事をご覧ください。

## <a name="restriction-on-libraries-and-sdks"></a>ライブラリと SDK に関する制限事項

Azure AD B2C への対応を Microsoft が表明しているライブラリは、現時点ではごく一部に限られています。 .NET ベースの Web アプリと Web サービス、NodeJS の Web アプリと Web サービスはサポートされています。  また Windows や .NET アプリから、プレビュー版の .NET クライアント ライブラリ (MSAL) を介して Azure AD B2C を使用することもできます。

それ以外の言語やプラットフォーム (iOS、Android を含む) に対応したライブラリは現時点では存在しません。  上記とは異なるプラットフォームを使用する場合は、オープンソース SDK の使用をお勧めします。必要に応じて [OAuth 2.0 と OpenID Connect プロトコル リファレンス](active-directory-b2c-reference-protocols.md)を参照してください。  Azure AD B2C には、OAuth と OpenID Connect が実装されているため、汎用的な OAuth ライブラリや OpenID Connect ライブラリを使った連携が可能となっています。

iOS と Android に関するクイック スタート チュートリアルでは、Azure AD B2C との互換性が確認されたオープン ソースのライブラリを使用しています。  「 [作業開始](active-directory-b2c-overview.md#get-started) 」セクションで、全クイック スタート チュートリアルをご覧いただけます。

## <a name="restriction-on-protocols"></a>プロトコルに関する制限事項

Azure AD B2C では、OpenID Connect と OAuth 2.0 がサポートされています。 ただし、各プロトコルの一部の機能はまだ実装されていません。 Azure AD B2C でサポートされているプロトコル機能の範囲について理解を深めるには、 [OpenID Connect と OAuth 2.0 のプロトコル リファレンス](active-directory-b2c-reference-protocols.md)をご覧ください。 SAML および WS-Fed プロトコルのサポートは提供されていません。

## <a name="restriction-on-tokens"></a>トークンに関する制限事項

Azure AD B2C によって発行されるトークンの多くは、JSON Web トークン (JWT) として実装されます。 ただし、JWT に含まれているすべての情報 ("要求" と呼ばれる) で十分というわけではなく、欠落しているものもあります。 例として "preferred_username" 要求があります。  要求の値や形式、意味は時間の経過に伴って変化するものであるため、既にあるポリシーのトークンは影響を受けません。運用環境のアプリから、それらの値に依存することができます。  値が変化したときは、ポリシーごとにそれらの変更を構成する機会を設ける予定です。  Azure AD B2C サービスによって現在発行されているトークンについて理解を深めるには、[トークン リファレンス](active-directory-b2c-reference-tokens.md)をご覧ください。

## <a name="restriction-on-nested-groups"></a>グループの入れ子に関する制限

Azure AD B2C テナントでは、グループのメンバーシップを入れ子にすることはできません。 この機能を追加する予定はありません。

## <a name="restriction-on-differential-query-feature-on-azure-ad-graph-api"></a>Azure AD Graph API の差分クエリ機能に関する制限

Azure AD B2C のテナントでは、 [Azure AD Graph API の差分クエリ機能](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query) がサポートされません。 長期的にはサポートされる予定となっています。

## <a name="issues-with-user-management-on-the-azure-classic-portal"></a>Azure クラシック ポータルでのユーザー管理に関する問題

B2C 機能には、Azure Portal からアクセスできます。 ただし、Azure クラシック ポータルを使用して、ユーザー管理などの他のテナント機能にアクセスできます。 現在、Azure クラシック ポータルには、ユーザー管理 ( **[ユーザー]** タブ) に関する既知の問題がいくつかあります。

* ローカル アカウント ユーザー (電子メール アドレスとパスワード、またはユーザー名とパスワードを使用してサインアップするコンシューマー) の場合、 **[ユーザー名]** フィールドはサインアップ中に使用されるサインイン ID (電子メール アドレスまたはユーザー名) に対応していません。 これは、Azure クラシック ポータルに表示されるフィールドが、実際には B2C シナリオでは使用されないユーザー プリンシパル名 (UPN) であるためです。 ローカル アカウントのサインイン ID を確認するには、 [Graph Explorer](https://graphexplorer.cloudapp.net/)でユーザー オブジェクトを見つけます。 ソーシャル アカウント ユーザー (Facebook、Google+ などを使用してサインアップするコンシューマー) についても同じ問題がありますが、その場合、特にサインイン ID はありません。

    ![ローカル アカウントの UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)
* ローカル アカウント ユーザーの場合、 **[プロファイル]** タブでフィールドを編集したり、変更を保存したりすることはできません。

## <a name="issues-with-admin-initiated-password-reset-on-the-azure-classic-portal"></a>Azure クラシック ポータルでの管理者によるパスワードのリセットに関する問題

管理者が Azure クラシック ポータルでローカル アカウント ベースのコンシューマーのパスワードをリセットすると (**[ユーザー]** タブの **[パスワードのリセット]** コマンド)、サインアップ ポリシーまたはサインイン ポリシーを使用している場合に、そのコンシューマーは次回サインイン時に自分のパスワードを変更できなくなり、アプリケーションからロックアウトされます。 この問題を回避するには、 [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) を使用してコンシューマーのパスワードを (パスワードの有効期限なしで) リセットするか、"サインアップまたはサインイン" ポリシーではなく "サインイン" ポリシーを使用します。

## <a name="issues-with-creating-a-custom-attribute"></a>カスタム属性の作成に関する問題

[Azure Portal に追加されたカスタム属性](active-directory-b2c-reference-custom-attr.md) は、すぐには B2C テナントに作成されません。 カスタム属性を B2C テナントに作成して Graph API から利用できる状態にするためには、あらかじめ少なくとも 1 つのポリシーの中でそのカスタム属性を使用する必要があります。

## <a name="issues-with-verifying-a-domain-on-the-azure-classic-portal"></a>Azure クラシック ポータルでのドメインの確認に関する問題

現在、 [Azure クラシック ポータル](https://manage.windowsazure.com/)でドメインを正常に確認することはできません。

## <a name="issues-with-sign-in-with-mfa-policy-on-safari-browsers"></a>Safari ブラウザーでの MFA ポリシーを使用したサインインに関する問題

Safari ブラウザーで、サインイン ポリシーに対する (MFA をオンにした) 要求が、HTTP 400 (正しくない要求) エラーで断続的に失敗します。 これは、Safari の Cookie サイズ制限の値が低いためです。 この問題にはいくつかの回避策があります。

* "サインイン ポリシー" ではなく "サインアップまたはサインイン ポリシー" を使用します。
* ポリシーで要求されている **アプリケーション要求** の数を減らします。

## <a name="issues-with-windows-desktop-wpf-apps-using-azure-ad-b2c"></a>Azure AD B2C を使用する Windows デスクトップ WPF アプリに関する問題

Windows デスクトップ WPF アプリから Azure AD B2C への要求が、"The browser based authentication dialog failed to complete. Reason: The protocol is not known and no pluggable protocols have been entered that match. (ブラウザー ベースの認証ダイアログを完了できませんでした。理由: プロトコルが不明であり、適合するプラグ可能プロトコルが入力されていません。)" というエラー メッセージで失敗する場合があります。

これは、Azure AD B2C で提供される認証コードのサイズに起因します。サイズは、トークンで要求される要求の数と関連しています。 この問題の回避策として、トークンで要求される要求の数を減らし、他の要求では Graph API を個別に照会します。

