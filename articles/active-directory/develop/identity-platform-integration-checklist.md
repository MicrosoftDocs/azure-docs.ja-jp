---
title: Microsoft ID プラットフォームに関するベスト プラクティス | Azure
description: Microsoft ID プラットフォームと統合する場合のベスト プラクティス、推奨事項、よくある見落としについて説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: e3ec9a96c15cb1f337a3c750b41b22c3b7a13d20
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917456"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Microsoft ID プラットフォームのベスト プラクティスと推奨事項

この記事では、Microsoft ID プラットフォームと統合する場合のベスト プラクティス、推奨事項、よくある見落としについて説明します。  このチェックリストに従うと、高品質で安全な統合を実現できます。 この一覧を定期的に確認して、アプリと ID プラットフォームとの統合の品質とセキュリティを維持してください。 チェックリストは、アプリケーション全体を確認することを目的としていません。 チェックリストの内容は、プラットフォームの改善に伴って変更される可能性があります。

まだ使い始めたばかりの場合は、[Microsoft ID プラットフォームのドキュメント](index.yml)を確認して、Microsoft ID プラットフォームでの認証の基本、アプリケーション シナリオなどについて学習してください。

次のチェックリストは、アプリケーションが [Microsoft ID プラットフォーム](https://docs.microsoft.com/azure/active-directory/develop/)と効果的に統合されていることを確認するために使用します。

## <a name="basics"></a>基本

|   |   |
|---|---|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | [Microsoft プラットフォーム ポリシー](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409)を読んで理解します。 ユーザーとプラットフォームを保護するために設計時に要点をまとめた条項にアプリケーションが従っていることを確認します。 |

## <a name="ownership"></a>所有権

|   |   |
|---|---|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | アプリの登録と管理に使用したアカウントに関連付けられている情報が最新であることを確認します。 |

## <a name="branding"></a>ブランド

|   |   |
|---|---|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | 「[アプリケーションのブランド化ガイドライン](howto-add-branding-in-azure-ad-apps.md)」に従っています。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | アプリケーションにわかりやすい名前とロゴを用意します。 この情報は、[アプリケーションの同意プロンプト](application-consent-experience.md)に表示されます。 ユーザーが情報に基づいて決定を下せるように、名前とロゴが会社や製品を表現するものにします。 商標に違反していないことを確認します。 |

## <a name="privacy"></a>プライバシー

|   |   |
|---|---|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | アプリのサービス使用条件とプライバシーに関する声明のリンクを用意します。 |

## <a name="security"></a>セキュリティ

|   |   |
|---|---|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | リダイレクト URI を管理します。 <ul><li>すべてのリダイレクト URI の所有権を維持し、それらの DNS レコードを最新の状態に保ちます。</li><li>URI にワイルドカード (*) を使用しないでください。</li><li>Web アプリの場合は、すべての URI がセキュリティで保護され、暗号化されていることを確認します (たとえば、https スキームの使用など)。</li><li>パブリック クライアントの場合、該当する場合 (主に iOS および Android) はプラットフォーム固有のリダイレクト URI を使用します。 それ以外の場合は、アプリにコール バックするときの競合を防ぐために、ランダム性が高いリダイレクト URI を使用します。</li><li>アプリが独立した Web エージェントから使用されている場合は、 https://login.microsoftonline.com/common/oauth2/nativeclient を使用できます。</li><li>未使用または不要なリダイレクト URI を定期的に確認して整理します。</li></ul> |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | アプリがディレクトリに登録されている場合は、アプリ登録所有者の一覧を最小化して手動で監視します。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | 明示的に必要な場合を除き、[OAuth2 の暗黙的な許可フロー](v2-oauth2-implicit-grant-flow.md)のサポートを有効にしないでください。 有効なシナリオについては、[こちら](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)を参照してください。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | ユーザー名/パスワードにうまく対処してください。 ユーザーのパスワードを直接処理する[リソース所有者パスワード資格情報フロー (ROPC)](v2-oauth-ropc.md) を使用しないでください。 このフローには高度な信頼とユーザーの公開が必要であり、他のより安全なフローを使用できない場合にのみ使用します。 このフローは、一部のシナリオ (DevOps など) ではまだ必要ですが、それを使用するとアプリケーションに制約が課せされることに注意してください。  最新の手法については、「[認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)」を参照してください。|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | Web アプリ、Web API、およびデーモン アプリに対応した機密アプリの資格情報を保護および管理します。 パスワードの資格情報 (クライアント シークレット) ではなく、[証明書の資格情報](active-directory-certificate-credentials.md)を使用します。 パスワードの資格情報を使用する必要がある場合は、手動で設定しないでください。 資格情報は、コードまたは構成に格納しないでください。また、資格情報の人間による処理を許可しないでください。 可能であれば、[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) または [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) を使用して資格情報を格納し、定期的にローテーションします。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | アプリケーションが最低限の特権のアクセス許可を要求していることを確認します。 アプリケーションに絶対に必要なアクセス許可のみを必要なときにのみ必須とします。 [各種アクセス許可](v1-permissions-and-consent.md#types-of-permissions)を理解します。 必要に応じて、アプリケーションのアクセス許可のみを使用します。可能であれば、委任されたアクセス許可を使用してください。 Microsoft Graph のアクセス許可の一覧については、こちらの[アクセス許可リファレンス](https://docs.microsoft.com/graph/permissions-reference)を参照してください。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | Microsoft ID プラットフォームを使用して API をセキュリティで保護している場合は、公開する必要があるアクセス許可について慎重に検討します。 ソリューションに適した細分性と、管理者の同意が必要なアクセス許可を考慮します。 どのような承認でも、決定する前に受信トークンで予想されるアクセス許可を確認します。 |

## <a name="implementation"></a>実装

|   |   |
|---|---|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | 最新の認証ソリューション (OAuth 2.0、[OpenID Connect](v2-protocols-oidc.md)) を使用して安全にユーザーのサインインを行います。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) |  OAuth 2.0 や Open ID などのプロトコルに対して、直接プログラミングしないでください。 代わりに、[Microsoft Authentication Library (MSAL)](msal-overview.md) を活用してください。 MSAL ライブラリでは、使いやすいライブラリ内に安全にセキュリティ プロトコルがラップされており、[条件付きアクセス](/azure/active-directory/conditional-access/overview)のシナリオに対する組み込みのサポート、デバイス全体の[シングル サインオン (SSO)](/azure/active-directory/manage-apps/what-is-single-sign-on)、および組み込みのトークン キャッシュ サポートを利用できます。 詳細については、Microsoft がサポートする[クライアント ライブラリ](reference-v2-libraries.md#microsoft-supported-client-libraries)および[ミドルウェア ライブラリ](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)の一覧と、[互換性のあるサードパーティのクライアント ライブラリ](reference-v2-libraries.md#compatible-client-libraries)の一覧を参照してください。<br/><br/>認証プロトコル用に手作業でコーディングする必要がある場合は、[Microsoft SDL](https://www.microsoft.com/sdl/default.aspx) などの手法に従う必要があります。 各プロトコルの標準仕様におけるセキュリティの考慮事項に十分注意してください。|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) |  [Azure Active Directory Authentication Library (ADAL)](active-directory-authentication-libraries.md) から [Microsoft Authentication Library](msal-overview.md) へ既存のアプリを移行します。 MSAL は、Microsoft の最新の ID プラットフォーム ソリューションであり、ADAL に適しています。 .NET、JavaScript、Android、iOS、macOS で利用可能であり、Python と Java についてはパブリック プレビュー段階です。 [ADAL.NET](msal-net-migration.md)、[ADAL.js](msal-compare-msal-js-and-adal-js.md)、および [ADAL.NET と iOS ブローカー](msal-net-migration-ios-broker.md)アプリの移行に関する詳細を確認してください。|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) |  モバイル アプリの場合、アプリケーションの登録エクスペリエンスを使用して、各プラットフォームを構成します。 アプリケーションでのシングル サインインに Microsoft Authenticator または Microsoft ポータル サイトを利用するためには、アプリに "ブローカー リダイレクト URI" が構成されている必要があります。 これにより、認証後に Microsoft からアプリケーションに制御を返すことができます。 各プラットフォームを構成するときに、アプリの登録エクスペリエンスにプロセスが表示されます。 クイックスタートを使用して、実際の例をダウンロードします。 iOS 上では、可能な限りブローカーと System Webview を使用します。|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) |  Web アプリまたは Web API では、アカウントごとに1つのトークン キャッシュを保持します。  Web アプリの場合、トークン キャッシュは、アカウント ID によってキー指定されている必要があります。  Web API の場合、アカウントは、API の呼び出しに使用されるトークンのハッシュによって、キー指定されている必要があります。 MSAL.NET では、.NET Framework および .NET Core サブプラットフォーム上でカスタム トークン キャッシュのシリアル化が提供されます。 セキュリティとパフォーマンス上の理由から、ユーザーごとに1つのキャッシュをシリアル化することをお勧めします。 詳細については、[トークン キャッシュのシリアル化](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)に関するページを参照してください。|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | アプリに必要なデータを [Microsoft Graph](https://developer.microsoft.com/graph) を介して入手できる場合は、個々の API ではなく Microsoft Graph エンドポイントを使用してこのデータに対するアクセス許可を要求します。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) |アクセス トークンの値を表示したり、クライアントとして解析したりしないでください。  値や形式が変化したり、警告なしで暗号化されたりする可能性があります。クライアントでユーザーに関する情報が必要な場合は、常に、id_token を使用するか、または Microsoft Graph を呼び出してください。  アクセス トークンの解析は、Web API でのみ行う必要があります (これは、形式の定義と暗号化キーの設定は、Web API で行われているためです)。 |

## <a name="end-user-experience"></a>エンド ユーザー エクスペリエンス

|   |   |
|---|---|
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | [同意エクスペリエンスを理解し](application-consent-experience.md)、アプリを信頼するかどうかを判断できる十分な情報がエンド ユーザーと管理者に与えられるように、アプリの同意プロンプトを構成します。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | 対話フローの前にサイレント認証 (サイレント トークン取得) を試行することで、アプリの使用中にユーザーがログイン資格情報を入力する必要がある回数を最小限に抑えます。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | 毎回のサインインに "プロンプト = 同意" を使用しないでください。 追加のアクセス許可について同意を求める必要があると判断した場合 (たとえば、アプリに必要なアクセス許可を変更した場合など) に限り、プロンプト = 同意を使用します。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | 該当する場合は、ユーザー データを使用してアプリケーションを強化します。 これを行うには、[Microsoft Graph API](https://developer.microsoft.com/graph) を使用するのが簡単です。 初めて利用するときに役立つ [Graph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer) ツール。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | 管理者がテナントに簡単に同意を許可できるように、アプリに必要なすべてのアクセス許可を登録します。 実行時に[増分同意](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)を使用して、最初の起動時に要求されたときにユーザーが懸念する、または混乱する可能性があるアクセス許可をアプリケーションが要求する理由をわかりやすくします。 |
| ![チェックボックス](./media/active-directory-integration-checklist/checkbox-two.svg) | [クリーン シングル サインアウト エクスペリエンス](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)を実装します。 これはプライバシーとセキュリティの要件であり、優れたユーザー エクスペリエンスのために役立ちます。 |

## <a name="testing"></a>テスト

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
