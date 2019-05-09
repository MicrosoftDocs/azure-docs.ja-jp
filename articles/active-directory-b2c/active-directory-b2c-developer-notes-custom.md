---
title: カスタム ポリシーに関する開発者向けのメモ - Azure Active Directory B2C | Microsoft Docs
description: カスタム ポリシーで Azure AD B2C を構成および管理する開発者向けのメモ。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: cf9c6f6a54c38f00e477e2a9d62e72ab5faccdef
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418926"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でのカスタム ポリシーに関する開発者向けのメモ

Azure Active Directory B2C でのカスタム ポリシーの構成が一般提供されました。 この構成の方法は、複雑な ID ソリューションを構築する上級 ID 開発者を対象としています。 カスタム ポリシーによって、Identity Experience Framework の機能が Azure AD B2C テナントで利用できるようになります。 カスタム ポリシーを使用する上級 ID 開発者は、チュートリアルを完了させたり参考ドキュメントを読んだりすることに、ある程度の時間をかける必要があります。

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
- ソフトウェアの開発とステージングのベスト プラクティスに従い、少なくとも 1 つの開発/テスト環境と 1 つの運用環境を用意する。 
- ID プロバイダーによる新しい開発と、統合するサービスに関する情報を常に入手する。 たとえば、シークレットの変更や、サービスの予定された変更と予定外の変更を把握します。 
- アクティブな監視を設定し、運用環境の応答性を監視する。 Application Insights との統合の詳細については、[Azure Active Directory B2C: ログの収集](active-directory-b2c-custom-guide-eventlogger-appins.md)に関するページを参照してください。 
- Azure サブスクリプションの連絡先のメール アドレスを最新に保ち、Microsoft のライブ サイト チームのメールに対応できるようにしておく。 
- Microsoft ライブ サイト チームからのアドバイスがあった場合に、すぐに対処する。

## <a name="terms-for-features-in-public-preview"></a>パブリック プレビュー段階の機能に対する使用条件

- パブリック プレビュー機能の使用は、評価目的のみで使用することが推奨されています。 
- サービス レベル アグリーメント (SLA) はパブリック プレビュー機能には適用されません。
- パブリック プレビュー機能に対するサポート要求は、通常のサポート チャネルを通じて提出できます。 

## <a name="features-by-stage-and-known-issues"></a>段階別の機能と既知の問題

カスタム ポリシー/Identity Experience Framework 機能は、継続的かつ迅速に開発されています。 次の表は、機能およびコンポーネントの可用性のインデックスです。

### <a name="identity-providers-tokens-protocols"></a>ID プロバイダー、トークン、プロトコル

| 機能 | 開発 | プレビュー | 一般公開 | メモ |
|-------- | ----------- | ------- | -- | ----- |
| IDP-OpenIDConnect |  |  | X | 例: Google+。  |
| IDP-OAUTH2 |  |  | X | 例: Facebook。  |
| IDP-OAUTH1 (twitter) |  | X |  | 例: Twitter。 |
| IDP-OAUTH1 (例: twitter) |  |  |  | サポートされていません |
| IDP-SAML |  |   | X | 例: Salesforce、ADFS。 |
| IDP-WSFED | X |  |  |  |
| 証明書利用者 OAUTH1 |  |  |  | サポートされていません。 |
| 証明書利用者 OAUTH2 |  |  | X |  |
| 証明書利用者 OIDC |  |  | X |  |
| 証明書利用者 SAML | X |  |  |  |
| 証明書利用者 WSFED | X |  |  |  |
| 基本認証と証明書認証を使用した REST API |  |  | X | 例: Azure Logic Apps。 |

### <a name="component-support"></a>コンポーネントのサポート

| 機能 | 開発 | プレビュー | 一般公開 | メモ |
| ------- | ----------- | ------- | -- | ----- |
| Azure Multi Factor Authentication |  |  | X |  |
| ローカル ディレクトリとしての Azure Active Directory |  |  | X |  |
| 電子メール検証のための Azure Email サブシステム |  |  | X |  |
| 複数言語のサポート|  |  | X |  |
| 述語の検証 |  |  | X | 例: パスワードの複雑さ。 |
| サード パーティの電子メール サービス プロバイダーの使用 | X |  |  |  |

### <a name="content-definition"></a>コンテンツ定義

| 機能 | 開発 | プレビュー | 一般公開 | メモ |
| ------- | ----------- | ------- | -- | ----- |
| エラー ページ、api.error |  |  | X |  |
| IDP の選択ページ、api.idpselections |  |  | X |  |
| サインアップのための IDP の選択、api.idpselections.signup |  |  | X |  |
| パスワードを忘れた場合、api.localaccountpasswordreset |  |  | X |  |
| ローカル アカウントのサインイン、api.localaccountsignin |  |  | X |  |
| ローカル アカウントのサインアップ、api.localaccountsignup |  |  | X |  |
| MFA ページ、api.phonefactor |  |  | X |  |
| セルフアサートされたソーシャル アカウントのサインアップ、api.selfasserted |  |  | X |  |
| セルフアサートされたプロファイルの更新、api.selfasserted.profileupdate |  |  | X |  |
| 統合されたサインアップまたはサインイン ページ、api.signuporsignin、パラメーター "disableSignup" を使用 |  |  | X |  |
| JavaScript/ページ コントラクト |  | X |  |  |

### <a name="app-ief-integration"></a>アプリケーションと IEF の統合

| 機能 | 開発 | プレビュー | 一般公開 | メモ |
| ------- | ----------- | ------- | -- | ----- |
| クエリ文字列パラメーター domain_hint |  |  | X | 要求として使用可能、IDP に渡すことができる。 |
| クエリ文字列パラメーター login_hint |  |  | X | 要求として使用可能、IDP に渡すことができる。 |
| client_assertion で JSON を UserJourney に挿入する | X |  |  | 非推奨となる予定。 |
| id_token_hint として JSON を UserJourney に挿入する |  | X |  | JSON を渡す順方向の転送方法。 |
| IDP TOKEN をアプリケーションに渡す |  | X |  | 例: Facebook からアプリへ。 |

### <a name="session-management"></a>セッションの管理

| 機能 | 開発 | プレビュー | 一般公開 | メモ |
| ------- | ----------- | ------- | -- | ----- |
| SSO セッション プロバイダー |  |  | X |  |
| 外部ログイン セッション プロバイダー |  |  | X |  |
| SAML SSO セッション プロバイダー |  |  | X |  |
| 既定の SSO セッション プロバイダー |  |  | X |  |

### <a name="security"></a>セキュリティ

| 機能 | 開発 | プレビュー | 一般公開 | メモ |
|-------- | ----------- | ------- | -- | ----- |
| ポリシー キー - 生成、手動、アップロード |  |  | X |  |
| ポリシー キー - RSA/証明書、シークレット |  |  | X |  |
| ポリシーのアップロード |  |  | X |  |

### <a name="developer-interface"></a>開発者向けインターフェイス

| 機能 | 開発 | プレビュー | 一般公開 | メモ |
| ------- | ----------- | ------- | -- | ----- |
| Azure Portal - IEF UX |  |  | X |  |
| Application Insights の UserJourney ログ |  | X |  | 開発中のトラブルシューティングに使用される。  |
| Application Insights イベント ログ (オーケストレーション手順を介して) |  | X |  | 運用環境内でユーザー フローを監視するために使用される。 |

## <a name="next-steps"></a>次の手順
[カスタム ポリシーについて、ユーザー フローとの違いについて詳しく説明します](active-directory-b2c-overview-custom.md)。
