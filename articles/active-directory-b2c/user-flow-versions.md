---
title: Azure Active Directory B2C のユーザー フロー バージョン | Microsoft Docs
description: Azure Active Directory B2C で使用できるユーザー フローのバージョンについて説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c7b3ce6a4e72b90c6fd642b92d4a92eb02f4c92c
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063193"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のユーザー フロー バージョン

>[!IMPORTANT]
> この記事に記載されているすべてのユーザー フローは、**推奨**と指定されていない限り、パブリック プレビューと見なされます。 運用アプリケーションには、推奨されるユーザー フローのみを使用してください。

Azure Active Directory B2C (Azure AD B2C) のユーザー フローは、ユーザー ID エクスペリエンスを完全に記述する共通の[ポリシー](active-directory-b2c-reference-policies.md)を設定するために役立ちます。 このようなエクスペリエンスには、サインアップ、サインイン、パスワード リセット、プロファイル編集が含まれます。 Azure AD B2C では、推奨されるユーザー フローとプレビュー ユーザー フローの両方のコレクションから選択できます。

新しいユーザー フローが新しいバージョンとして追加されます。 ユーザー フローが安定してから使用することをお勧めします。 ユーザー フローは、完全にテストされた場合に**推奨**とマークされます。 ユーザー フローは、推奨とマークされるまでプレビューで検討されます。 運用アプリケーションには推奨されるユーザー フローを使用してください。ただし、新しい機能を使用できるようになったときにテストする場合は、他のバージョンから選択します。 以前のバージョンの推奨されるユーザー フローは使用しないでください。

## <a name="v1"></a>V1

| ユーザー フロー | 推奨 | 説明 |
| --------- | ----------- | ----------- |
| パスワード リセット | はい | ユーザーが電子メールを確認してから新しいパスワードを選択できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](active-directory-b2c-reference-mfa.md)</li><li>トークンの互換性の設定</li><li>[パスワードの複雑さの要件](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| プロファイル編集 | はい | ユーザーがユーザー属性を構成することを許可します。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[トークンの有効期間](active-directory-b2c-reference-tokens.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li></ul> |
| リソース所有者 | いいえ | ローカル アカウントを持つユーザーがネイティブ アプリケーションで直接ログインできるようにします (ブラウザーは必要ありません)。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[トークンの有効期間](active-directory-b2c-reference-tokens.md)</li><li>トークンの互換性の設定</li></ul> |
| サインイン | いいえ | ユーザーが自分のアカウントにサインインできるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](active-directory-b2c-reference-mfa.md)</li><li>[トークンの有効期間](active-directory-b2c-reference-tokens.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>サインインのブロック</li><li>パスワードの強制的なリセット</li><li>サインインしたままにする (KMSI)</ul><br>このユーザー フローではユーザー インターフェイスをカスタマイズできません。 |
| サインアップ | いいえ | ユーザーがアカウントを作成できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](active-directory-b2c-reference-mfa.md)</li><li>[トークンの有効期間](active-directory-b2c-reference-tokens.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[パスワードの複雑さの要件](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| サインアップとサインイン | はい | ユーザーがアカウントを作成したり、アカウントにサインインしたりすることができるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](active-directory-b2c-reference-mfa.md)</li><li>[トークンの有効期間](active-directory-b2c-reference-tokens.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[パスワードの複雑さの要件](active-directory-b2c-reference-password-complexity.md)</li></ul>|

## <a name="v2"></a>V2

| ユーザー フロー | 推奨 | 説明 |
| --------- | ----------- | ----------- |
| パスワードのリセット v2 | いいえ | ユーザーが電子メールを確認してから新しいパスワードを選択できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](active-directory-b2c-reference-mfa.md)</li><li>トークンの互換性の設定</li><li>[年齢制限](basic-age-gating.md)</li><li>[パスワードの複雑さの要件](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| サインイン v2 | いいえ | ユーザーが自分のアカウントにサインインできるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](active-directory-b2c-reference-mfa.md)</li><li>[トークンの有効期間](active-directory-b2c-reference-tokens.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[年齢制限](basic-age-gating.md)</li><li>サインイン ページのカスタマイズ</li></ul> |
| サインアップ v2 | いいえ | ユーザーがアカウントを作成できるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](active-directory-b2c-reference-mfa.md)</li><li>[トークンの有効期間](active-directory-b2c-reference-tokens.md)</li><li>トークンの互換性の設定</li><li>セッションの動作</li><li>[年齢制限](basic-age-gating.md)</li><li>[パスワードの複雑さの要件](active-directory-b2c-reference-password-complexity.md)</li></ul> |
| サインアップとサインイン v2 | いいえ | ユーザーがアカウントを作成したり、アカウントにサインインしたりすることができるようにします。 このユーザー フローを使用すると、以下を構成できます。 <ul><li>[多要素認証](active-directory-b2c-reference-mfa.md)</li><li>[年齢制限](basic-age-gating.md)</li><li>[パスワードの複雑さの要件](active-directory-b2c-reference-password-complexity.md)</li></ul> |
