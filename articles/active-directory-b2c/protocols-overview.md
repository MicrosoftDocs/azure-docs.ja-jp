---
title: Azure Active Directory B2C での認証プロトコル | Microsoft Docs
description: Azure Active Directory B2C によってサポートされるプロトコルを直接使用してアプリを作成する方法。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed393f721d4461ebadea41f8dad707d4881865cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "78183907"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: 認証プロトコル
Azure Active Directory B2C (Azure AD B2C) では、業界標準のプロトコルである OpenID Connect と OAuth 2.0 をサポートすることによって Identity-as-a-Service (サービスとしての ID) 機能がアプリに提供されます。 このサービスは標準に準拠していますが、これらのプロトコルには、実装によって微妙な違いが存在する場合があります。

ここでは、オープン ソース ライブラリを使うのではなく、コードから直接 HTTP 要求を送信して処理する場合に役立つ情報を紹介します。 特定のプロトコルの詳細を学習する前に、このページを読むことをお勧めします。 ただし、Azure AD B2C について既に詳しい場合は、[プロトコル リファレンス ガイド](#protocols)にすぐに進んでもかまいません。

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>基本
Azure AD B2C を使用するすべてのアプリは、 [Azure Portal](https://portal.azure.com) で B2C ディレクトリに登録する必要があります。 アプリの登録プロセスでは、いくつかの値が収集され、対象のアプリに割り当てられます。

* アプリを一意に識別する **アプリケーション ID** 。
* **リダイレクト URI** または **パッケージ識別子** (アプリに戻す応答をリダイレクトする際に使用)。
* その他シナリオに応じた値。 詳細については、[アプリケーションを登録する方法](tutorial-register-applications.md)に関する記事を参照してください。

アプリを登録した後は、エンドポイントに要求を送ることによって、Azure Active Directory (Azure AD) と通信を行います。

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

OAuth と OpenID Connect におけるフローはほぼすべて、情報のやり取りに 4 つの当事者が関係します。

![4 つの OAuth 2.0 ロールを示す図](./media/protocols-overview/protocols_roles.png)

* **承認サーバー** は Azure AD エンドポイントです。 承認サーバーは、ユーザー情報とアクセスに関するすべてのことを安全に処理します。 また、フロー内の当事者間の信頼関係も処理します。 ユーザーの本人性確認、リソースへのアクセス権の付与と取り消し、トークンの発行という役割を担います。 ID プロバイダーとも呼ばれます。

* **リソース所有者** とは通常、エンド ユーザーを指します。 データを所有し、そのデータ (またはリソース) へのアクセスをサード パーティに許可する権限を持つ当事者です。

* **OAuth クライアント** はアプリです。 アプリケーション ID によって識別されます。 通常は、エンド ユーザーと情報をやり取りする当事者です。 また、承認サーバーにトークンを要求します。 リソース所有者は、リソースにアクセスするためのアクセス許可をクライアントに付与する必要があります。

* **リソース サーバー** は、リソースまたはデータが存在する場所です。 承認サーバーを信頼し、OAuth クライアントを安全に認証、承認します。 また、ベアラー アクセス トークンを使用して、リソースへのアクセス許可を確実に付与します。

## <a name="policies-and-user-flows"></a>ポリシーとユーザー フロー
おそらく、Azure AD B2C のポリシーは、サービスの最も重要な機能です。 Azure AD B2C は、ポリシーを導入することによって、標準の OAuth 2.0 や OpenID Connect プロトコルを拡張します。 ポリシーにより、Azure AD B2C は単なる認証および承認以外の多くの機能を実行できます。

最も一般的な ID タスクを設定しやすくするために、Azure AD B2C ポータルには、**ユーザー フロー** という事前定義済みで構成できるポリシーが用意されています。 ユーザー フローには、サインアップ、サインイン、プロファイル編集など、コンシューマーの ID エクスペリエンスが完全に記述されています。 ユーザー フローは、管理 UI で定義できます。 ポリシーは、HTTP 認証要求の特別なクエリ パラメーターを使用して実行できます。

ポリシーとユーザー フローは、OAuth 2.0 および OpenID Connect の標準機能ではないので、理解するために時間をかける必要があります。 詳しくは、[Azure AD B2C のユーザー フロー リファレンス ガイド](user-flow-overview.md)に関する記事をご覧ください。

## <a name="tokens"></a>トークン
Azure AD B2C での OAuth 2.0 および OpenID Connect の実装では、ベアラー トークンが広範囲に使われています (JSON Web トークン (JWT) として表現されたベアラー トークンなど)。 ベアラー トークンは、保護されたリソースへの "ベアラー" アクセスを許可する簡易セキュリティ トークンです。

ベアラーは、トークンを提示できる任意の利用者を表します。 Azure AD は、ベアラー トークンを受信するには、最初に利用者を認証する必要があります。 ただし、転送中や保存時にトークンを保護するために必要な対策を講じていない場合、意図しない利用者によって傍受され、使用されるおそれがあります。

一部のセキュリティ トークンには、許可されていない利用者がトークンを使用できないようにするための組み込みメカニズムがありますが、ベアラー トークンにはこのメカニズムがありません。 トランスポート層セキュリティ (HTTPS) などのセキュリティ保護されたチャネルで転送する必要があります。

ベアラー トークンがセキュリティ保護されたチャネル以外で転送された場合、悪意のある利用者が中間者攻撃によってトークンを取得し、保護されたリソースへの未承認のアクセスに使用する可能性があります。 後で使用するためにベアラー トークンを保存またはキャッシュするときにも、同じセキュリティ原則が適用されます。 アプリケーションでは、常に安全な方法でベアラー トークンを転送および保存してください。

ベアラー トークンのセキュリティに関する考慮事項の詳細については、 [RFC 6750 セクション 5](https://tools.ietf.org/html/rfc6750)を参照してください。

Azure AD B2C で使われている各種トークンの詳細については、[Azure AD のトークン リファレンス](tokens-overview.md)を参照してください。

## <a name="protocols"></a>プロトコル
要求の例を確認できる状態になったら、次のいずれかのチュートリアルを開始できます。 いずれも、特定の認証シナリオに対応しています。 どのフローを見ればよいかわからない場合は、[Azure AD B2C で作成できるアプリの種類](application-types.md)を確認してください。

* [OAuth 2.0 でモバイル アプリケーションおよびネイティブ アプリケーションを作成する](authorization-code-flow.md)
* [OpenID Connect を使用して Web アプリを構築する](openid-connect.md)
* [OAuth 2.0 暗黙的フローを使用してシングルページのアプリを作成する](implicit-flow-single-page-application.md)

