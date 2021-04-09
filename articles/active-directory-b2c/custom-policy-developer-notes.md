---
title: カスタム ポリシーに関する開発者向けのメモ
titleSuffix: Azure AD B2C
description: カスタム ポリシーで Azure AD B2C を構成および管理する開発者向けのメモ。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 76dcb9aec935dd4ea4f57a1362953d9741d8eaf0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095801"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でのカスタム ポリシーに関する開発者向けのメモ

Azure Active Directory B2C でのカスタム ポリシーの構成が一般提供されました。 この構成の方法は、複雑な ID ソリューションを構築する上級 ID 開発者を対象としています。 カスタム ポリシーによって、Identity Experience Framework の機能が Azure AD B2C テナントで利用できるようになります。
カスタム ポリシーを使用する上級 ID 開発者は、チュートリアルを完了させたり参考ドキュメントを読んだりすることに、ある程度の時間をかける必要があります。

カスタム ポリシー オプションの大部分は一般提供されましたが、これに対して技術プロファイルの種類やコンテンツ定義 API など、ソフトウェア ライフサイクルでのさまざまなステージで使用される基本的な機能があります。 他にも多くが予定されています。 次の表では、より細かなレベルで可用性のレベルを指定します。

## <a name="features-that-are-generally-available"></a>一般提供されている機能

- カスタム ポリシーを使用して、カスタム認証のユーザー体験を作成およびアップロードする。
    - 要求プロバイダー間のやり取りとしてユーザー体験を順を追って記述する。
    - ユーザー体験の条件分岐を定義する。
- カスタム認証のユーザー体験で REST API 対応サービスと相互運用する。
- OpenIDConnect プロトコルに準拠している ID プロバイダーとフェデレーションする。
- SAML 2.0 プロトコルに準拠している ID プロバイダーとフェデレーションする。

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>カスタム ポリシー機能セット開発者の責任

手動のポリシー構成で付与される Azure AD B2C の基になるプラットフォームへのアクセス レベルは低くなるため、信頼できる一意のフレームワークが作成されます。 カスタム ID プロバイダー、信頼関係、外部サービスとの統合、ステップバイステップのワークフローの起こり得る多くの配列には、設計および構成に対する系統的なアプローチが必要です。

カスタム ポリシーの機能セットを使用する開発者は、次のガイドラインに従う必要があります。

- カスタム ポリシーとキー/シークレット管理の構成言語について理解を深める。 詳細については、「[TrustFrameworkPolicy](trustframeworkpolicy.md)」を参照してください。
- シナリオおよびカスタム統合の主導権を得る。 ご自分の作業内容を文書化し、ライブ サイト組織に通知します。
- シナリオの体系的なテストを実行する。
- ソフトウェア開発とステージングのベスト プラクティスに従ってください。 少なくとも 1 つの開発およびテスト環境が推奨されます。
- ID プロバイダーによる新しい開発と、統合するサービスに関する情報を常に入手する。 たとえば、シークレットの変更や、サービスの予定された変更と予定外の変更を把握します。
- アクティブな監視を設定し、運用環境の応答性を監視する。 Application Insights との統合の詳細については、[Azure Active Directory B2C: ログの収集](analytics-with-application-insights.md)に関するページを参照してください。
- Azure サブスクリプションの連絡先のメール アドレスを最新に保ち、Microsoft のライブ サイト チームのメールに対応できるようにしておく。
- Microsoft ライブ サイト チームからのアドバイスがあった場合に、すぐに対処する。

## <a name="terms-for-features-in-public-preview"></a>パブリック プレビュー段階の機能に対する使用条件

- パブリック プレビュー機能の使用は、評価目的のみで使用することが推奨されています。
- サービス レベル アグリーメント (SLA) はパブリック プレビュー機能には適用されません。
- パブリック プレビュー機能に対するサポート要求は、通常のサポート チャネルを通じて提出できます。

## <a name="features-by-stage-and-known-issues"></a>段階別の機能と既知の問題

カスタム ポリシー機能は、継続的に開発されています。 次の表は、機能およびコンポーネントの可用性のインデックスです。


### <a name="protocols-and-authorization-flows"></a>プロトコルと承認フロー

| 機能 | 開発 | プレビュー | GA | Notes |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OAuth2 承認コード](authorization-code-flow.md) |  |  | X |  |
| PKCE を使用した OAuth2 承認コード |  |  | X | [パブリック クライアントとシングルページ アプリケーション](authorization-code-flow.md)  |
| [OAuth2 暗黙的フロー](implicit-flow-single-page-application.md) |  |  | X |  |
| [OAuth2 リソース所有者のパスワード資格情報](ropc-custom.md) |  | X |  |  |
| [OIDC Connect](openid-connect.md) |  |  | X |  |
| [SAML2](saml-service-provider.md)  |  |  |X  | POST とリダイレクトのバインディング。 |
| OAuth1 |  |  |  | サポートされていません。 |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>プロバイダーのフェデレーションを識別する 

| 機能 | 開発 | プレビュー | GA | Notes |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | 例: Google+。  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | 例: Facebook。  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | 例: Twitter。 |
| [SAML2](identity-provider-generic-saml.md) |  |   | X | 例: Salesforce、ADFS。 |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>REST API 統合

| 機能 | 開発 | プレビュー | GA | Notes |
|-------- | :-----------: | :-------: | :--: | ----- |
| [基本認証を使用した REST API](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [クライアント証明書認証を使用した REST API](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [OAuth2 ベアラー認証を使用した REST API](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>コンポーネントのサポート

| 機能 | 開発 | プレビュー | GA | Notes |
| ------- | :-----------: | :-------: | :--: | ----- |
| [電話要素認証](phone-factor-technical-profile.md) |  |  | X |  |
| [Azure AD MFA 認証](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [ワンタイム パスワード](one-time-password-technical-profile.md) |  | X |  |  |
| ローカル ディレクトリとしての [Azure Active Directory](active-directory-technical-profile.md) |  |  | X |  |
| 電子メール検証のための Azure 電子メール サブシステム |  |  | X |  |
| [サード パーティの電子メール サービス プロバイダー](custom-email-mailjet.md) |  |X  |  |  |
| [複数言語のサポート](localization.md)|  |  | X |  |
| [述語の検証](predicates.md) |  |  | X | 例: パスワードの複雑さ。 |
| [表示コントロール](display-controls.md) |  |X  |  |  |


### <a name="app-ief-integration"></a>アプリケーションと IEF の統合

| 機能 | 開発 | プレビュー | GA | Notes |
| ------- | :-----------: | :-------: | :--: | ----- |
| クエリ文字列パラメーター `domain_hint` |  |  | X | 要求として使用可能、IDP に渡すことができる。 |
| クエリ文字列パラメーター `login_hint` |  |  | X | 要求として使用可能、IDP に渡すことができる。 |
| `client_assertion` を使用して JSON をユーザー体験に挿入 | X |  |  | 非推奨となる予定。 |
| `id_token_hint` として JSON をユーザー体験に挿入 |  | X |  | JSON を渡す順方向の転送方法。 |
| [ID プロバイダー トークンをアプリケーションに渡す](idp-pass-through-user-flow.md) |  | X |  | 例: Facebook からアプリへ。 |


### <a name="session-management"></a>セッションの管理

| 機能 | 開発 | プレビュー | GA | Notes |
| ------- | :-----------: | :-------: | :--: | ----- |
| [既定の SSO セッション プロバイダー](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [外部ログイン セッション プロバイダー](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [SAML SSO セッション プロバイダー](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |
| [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider)  |  | X |  |  |
| [シングル サインアウト](session-behavior.md#sign-out)  |  | X |  |  |

### <a name="security"></a>Security

| 機能 | 開発 | プレビュー | GA | Notes |
|-------- | :-----------: | :-------: | :--: | ----- |
| ポリシー キー - 生成、手動、アップロード |  |  | X |  |
| ポリシー キー - RSA/証明書、シークレット |  |  | X |  |


### <a name="developer-interface"></a>開発者向けインターフェイス

| 機能 | 開発 | プレビュー | GA | Notes |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Portal - IEF UX |  |  | X |  |
| ポリシーのアップロード |  |  | X |  |
| [Application Insights のユーザー体験ログ](troubleshoot-with-application-insights.md) |  | X |  | 開発中のトラブルシューティングに使用される。  |
| [Application Insights のイベント ログ](analytics-with-application-insights.md) |  | X |  | 運用環境内でユーザー フローを監視するために使用される。 |


## <a name="next-steps"></a>次のステップ

- [Azure AD B2C に使用可能な Microsoft Graph 操作](microsoft-graph-operations.md)を確認します
- [カスタム ポリシーについて、およびユーザー フローとの違い](custom-policy-overview.md)について学習します。
