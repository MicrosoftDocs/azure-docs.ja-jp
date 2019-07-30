---
title: Microsoft ID プラットフォームと統合する | Azure
description: Microsoft ID プラットフォーム (v2.0) と統合する際のベスト プラクティスと一般的な見落としについて説明します。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev
ms.openlocfilehash: 0acef783b4877b5b1787f142aec6cc3588293f83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111470"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Microsoft ID プラットフォーム統合チェックリスト

Microsoft ID プラットフォーム統合チェックリストは、高品質で安全な統合をガイドすることを目的としています。 Microsoft ID プラットフォームと統合する際のベスト プラクティスと一般的な見落としが強調されているので、定期的にこのリストを確認し、アプリと ID プラットフォームの統合の品質とセキュリティを確実に維持してください。 チェックリストは、アプリケーション全体を確認することを目的としていません。 チェックリストの内容は、プラットフォームの改善に伴って変更される可能性があります。

まだ始めたばかりの場合は、[ドキュメント](index.yml)を参照して、認証の基本、Microsoft ID プラットフォームのアプリケーション シナリオなどについて学習してください。

## <a name="testing-your-integration"></a>統合のテスト

次のチェックリストは、アプリケーションが [Microsoft ID プラットフォーム](https://docs.microsoft.com/azure/active-directory/develop/)と効果的に統合されていることを確認するために使用します。

### <a name="basics"></a>基本

|   |   |
|---|---|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | [Microsoft プラットフォーム ポリシー](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409)を読んで理解します。 ユーザーとプラットフォームを保護するために設計時に要点をまとめた条項にアプリケーションが従っていることを確認します。 |

### <a name="ownership"></a>所有権

|   |   |
|---|---|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | アプリの登録と管理に使用したアカウントに関連付けられている情報が最新であることを確認します。 |

### <a name="branding"></a>ブランド

|   |   |
|---|---|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | 「[アプリケーションのブランド化ガイドライン](howto-add-branding-in-azure-ad-apps.md)」に従っています。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | アプリケーションにわかりやすい名前とロゴを用意します。 この情報は、アプリケーションの同意プロンプトに表示されます。 ユーザーが情報に基づいて決定を下せるように、名前とロゴが会社や製品を表現するものにします。 商標に違反していないことを確認します。 |

### <a name="privacy"></a>プライバシー

|   |   |
|---|---|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | アプリのサービス使用条件とプライバシーに関する声明のリンクを用意します。 |

### <a name="security"></a>セキュリティ

|   |   |
|---|---|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | すべてのリダイレクト URI の所有権を維持し、それらの DNS レコードを最新の状態に保ちます。 URI にワイルドカード (*) を使用しないでください。 Web アプリの場合は、すべての URI がセキュリティで保護され、暗号化されていることを確認します (たとえば、https スキームの使用など)。 パブリック クライアントの場合、該当する場合 (主に iOS および Android) はプラットフォーム固有のリダイレクト URI を使用します。 それ以外の場合は、アプリにコール バックするときの競合を防ぐために、ランダム性が高いリダイレクト URI を使用します。 アプリが独立した Web エージェントから使用されている場合は、 https://login.microsoftonline.com/nativeclient を使用できます。 未使用または不要なリダイレクト URI を定期的に確認して整理します。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | アプリがディレクトリに登録されている場合は、アプリ登録所有者の一覧を最小化して手動で監視します。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | 明示的に必要な場合を除き、[OAuth2 の暗黙的な許可フロー](v2-oauth2-implicit-grant-flow.md)のサポートを有効にしないでください。 有効なシナリオについては、[こちら](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)を参照してください。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | ユーザーのパスワードを直接処理する[リソース所有者パスワード資格情報フロー (ROPC)](v2-oauth-ropc.md) を使用しないでください。 このフローには高度な信頼とユーザーの公開が必要であり、他のより安全なフローを使用できない場合にのみ使用します。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | アプリの資格情報を保護および管理します。 パスワードの資格情報 (クライアント シークレット) ではなく、[証明書の資格情報](active-directory-certificate-credentials.md)を使用します。 パスワードの資格情報を使用する必要がある場合は、手動で設定しないでください。 資格情報は、コードまたは構成に格納しないでください。また、資格情報の人間による処理を許可しないでください。 可能であれば、[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) または [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) を使用して資格情報を格納し、定期的にローテーションします。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | アプリケーションが最低限の特権のアクセス許可を要求していることを確認します。 アプリケーションに絶対に必要なアクセス許可のみを必要なときにのみ必須とします。 [各種アクセス許可](v1-permissions-and-consent.md#types-of-permissions)を理解します。 必要に応じてアプリケーションのアクセス許可のみを使用します。可能であれば、委任されたアクセス許可を使用します。 Microsoft Graph のアクセス許可の一覧については、こちらの[アクセス許可リファレンス](https://docs.microsoft.com/graph/permissions-reference)を参照してください。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | Microsoft ID プラットフォームを使用して API をセキュリティで保護している場合は、公開する必要があるアクセス許可について慎重に検討します。 ソリューションに適した細分性と、管理者の同意が必要なアクセス許可を考慮します。 どのような承認でも、決定する前に受信トークンで予想されるアクセス許可を確認します。 |

### <a name="implementation"></a>実装

|   |   |
|---|---|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | 最新の認証ソリューション (OAuth 2.0、[OpenID Connect](v2-protocols-oidc.md)) を使用して安全にユーザーのサインインを行います。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | 自分でプロトコルを実装しないでください。[Microsoft がサポートする認証ライブラリ](reference-v2-libraries.md) (MSAL、サーバー ミドルウェア) を使用します。 統合した最新バージョンの認証ライブラリを使用していることを確認します。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | アプリに必要なデータを [Microsoft Graph](https://developer.microsoft.com/graph) を介して入手できる場合は、個々の API ではなく Microsoft Graph エンドポイントを使用してこのデータに対するアクセス許可を要求します。 |

### <a name="end-user-experience"></a>エンド ユーザー エクスペリエンス

|   |   |
|---|---|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | [同意エクスペリエンスを理解し](application-consent-experience.md)、アプリを信頼するかどうかを判断できる十分な情報がエンド ユーザーと管理者に与えられるように、アプリの同意プロンプトを構成します。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | 対話フローの前にサイレント認証 (サイレント トークン取得) を試行することで、アプリの使用中にユーザーがログイン資格情報を入力する必要がある回数を最小限に抑えます。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | 毎回のサインインに "プロンプト = 同意" を使用しないでください。 追加のアクセス許可について同意を求める必要があると判断した場合 (たとえば、アプリに必要なアクセス許可を変更した場合など) に限り、プロンプト = 同意を使用します。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | 該当する場合は、ユーザー データを使用してアプリケーションを強化します。 これを行うには、[Microsoft Graph API](https://developer.microsoft.com/graph) を使用するのが簡単です。 初めて利用するときに役立つ [Graph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer) ツール。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | 管理者がテナントに簡単に同意を許可できるように、アプリに必要なすべてのアクセス許可を登録します。 実行時に[増分同意](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)を使用して、最初の起動時に要求されたときにユーザーが懸念する、または混乱する可能性があるアクセス許可をアプリケーションが要求する理由をわかりやすくします。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | [クリーン シングル サインアウト エクスペリエンス](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)を実装します。 これはプライバシーとセキュリティの要件であり、優れたユーザー エクスペリエンスのために役立ちます。 |

### <a name="testing"></a>テスト

|   |   |
|---|---|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | ユーザーがアプリケーションを使用する能力に影響を与える可能性がある[条件付きアクセス ポリシー](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)をテストします。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | サポートする予定がある可能性のあるすべてのアカウント (職場または学校のアカウント、個人用の Microsoft アカウント、子供のアカウント、ソブリン アカウントなど) を使用してアプリケーションをテストします。 |

## <a name="additional-resources"></a>その他のリソース

v2.0 についての詳細な情報:

* [Microsoft ID プラットフォーム (v2.0 の概要)](v2-overview.md)
* [Microsoft ID プラットフォーム プロトコル リファレンス](active-directory-v2-protocols.md)
* [アクセス トークン リファレンス](access-tokens.md)
* [ID トークン リファレンス](id-tokens.md)
* [認証ライブラリ リファレンス](reference-v2-libraries.md)
* [Microsoft ID プラットフォームでのアクセス許可と同意](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
