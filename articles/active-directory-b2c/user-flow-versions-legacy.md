---
title: Azure Active Directory B2C の ユーザー フローの従来のバージョン | Microsoft Docs
description: Azure Active Directory B2C で使用できるユーザー フローのバージョンについて説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67ce6ab611e8d32c320320976ff2eba932f761a2
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505555"
---
# <a name="legacy-user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のユーザー フローの従来のバージョン

> [!IMPORTANT]
> この記事では、ユーザー フローの従来のバージョン管理方法について説明します。この方法では、V1 (実稼働準備完了) バージョンと、ユーザーフローの V1.1 および V2 (プレビュー) バージョンが提供されます。 Azure パブリック クラウド以外の環境では、この従来のバージョン管理方法が引き続き使用されます。 Azure パブリック クラウドでは、この方法は[新しい**推奨される**バージョンと**プレビュー** バージョン](user-flow-versions.md)に置き換えられています。
> 
Azure Active Directory B2C (Azure AD B2C) のユーザー フローは、ユーザー ID エクスペリエンスを完全に記述する共通の[ポリシー](user-flow-overview.md)を設定するために役立ちます。 このようなエクスペリエンスには、サインアップ、サインイン、パスワード リセット、プロファイル編集が含まれます。

次の表では、ユーザー フローが**推奨**として識別されていない限り、それは*プレビュー*と見なされます。 運用アプリケーションには、推奨されるユーザー フローのみを使用してください。

## <a name="v1"></a>V1

| ユーザー フロー | 推奨 | 説明 |
| --------- | ----------- | ----------- |
| パスワードのリセット | はい | ユーザーが電子メールを確認してから新しいパスワードを選択できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>トークンの互換性の設定</li><li>[パスワードの複雑さの要件](user-flow-password-complexity.md)</li></ul> |
| プロファイル編集 | はい | ユーザーがユーザー属性を構成することを許可します。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li></ul> |
| ROPC を使用したサインイン | いいえ | ローカル アカウントを持つユーザーがネイティブ アプリケーションで直接ログインできるようにします (ブラウザーは必要ありません)。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li></ul> |
| サインイン | いいえ | ユーザーが自分のアカウントにサインインできるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>サインインのブロック</li><li>パスワードの強制的なリセット</li><li>サインインしたままにする (KMSI)</ul><br>このユーザー フローではユーザー インターフェイスをカスタマイズできません。 |
| サインアップ | いいえ | ユーザーがアカウントを作成できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[パスワードの複雑さの要件](user-flow-password-complexity.md)</li></ul> |
| サインアップとサインイン | はい | ユーザーがアカウントを作成したり、アカウントにサインインしたりすることができるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[パスワードの複雑さの要件](user-flow-password-complexity.md)</li></ul>|

## <a name="v11"></a>V1.1

| ユーザー フロー | 推奨 | 説明 |
| --------- | ----------- | ----------- |
| パスワードのリセット v1.1 | いいえ | ユーザーが電子メールを確認した後に新しいパスワードを選択できるようにします (新しいページ レイアウトを使用できます)。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>トークンの互換性の設定</li><li>[パスワードの複雑さの要件](user-flow-password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| ユーザー フロー | 推奨 | 説明 |
| --------- | ----------- | ----------- |
| パスワードのリセット v2 | いいえ | ユーザーが電子メールを確認してから新しいパスワードを選択できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>トークンの互換性の設定</li><li>[年齢制限](basic-age-gating.md)</li><li>[パスワードの複雑さの要件](user-flow-password-complexity.md)</li></ul> |
| プロファイル編集 v2 | はい | ユーザーがユーザー属性を構成することを許可します。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li></ul> |
| サインイン v2 | いいえ | ユーザーが自分のアカウントにサインインできるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[年齢制限](basic-age-gating.md)</li><li>サインイン ページのカスタマイズ</li></ul> |
| サインアップ v2 | いいえ | ユーザーがアカウントを作成できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>[トークンの有効期間](tokens-overview.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[年齢制限](basic-age-gating.md)</li><li>[パスワードの複雑さの要件](user-flow-password-complexity.md)</li></ul> |
| サインアップとサインイン v2 | いいえ | ユーザーがアカウントを作成したり、アカウントにサインインしたりすることができるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](custom-policy-multi-factor-authentication.md)</li><li>[年齢制限](basic-age-gating.md)</li><li>[パスワードの複雑さの要件](user-flow-password-complexity.md)</li></ul> |