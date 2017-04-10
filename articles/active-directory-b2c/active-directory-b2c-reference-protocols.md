---
title: Azure Active Directory B2C | Microsoft Docs
description: "Azure Active Directory B2C によってサポートされるプロトコルを直接使用してアプリを作成する方法。"
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5e407d0a-73a2-4d74-ac81-3aa6c31ddcee
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 24e86b2a0c47576eba67f74c88bf9b9a42394653
ms.openlocfilehash: 556da1513345b3cadf4fda622bb351835c114845


---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: 認証プロトコル
Azure Active Directory (Azure AD) B2C では、業界標準のプロトコルである OpenID Connect と OAuth 2.0 をサポートすることによって Identity-as-a-Service (サービスとしての ID) 機能がアプリに提供されます。 このサービスは標準に準拠していますが、これらのプロトコルには、実装によって微妙な違いが存在する場合があります。  ここでは、オープン ソース ライブラリを使うのではなく、コードから直接 HTTP 要求を送信して処理する場合に役立つ情報を紹介します。 特定のプロトコルの詳細を学習する前に、このページを読むことをお勧めします。 ただし、Azure AD B2C について既に詳しい場合は、 [プロトコル リファレンス ガイド](#protocols)にすぐに進んでもかまいません。

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>基本
Azure AD B2C を使用するすべてのアプリは、 [Azure Portal](https://portal.azure.com) で B2C ディレクトリに登録する必要があります。 アプリの登録プロセスでは、いくつかの値が収集され、対象のアプリに割り当てられます。

* アプリを一意に識別する **アプリケーション ID** 。
* **リダイレクト URI** または**パッケージ識別子** (アプリに戻す応答をリダイレクトする際に使用)。
* その他シナリオに応じた値。 詳細については、 [アプリケーションを登録する方法](active-directory-b2c-app-registration.md)に関する記事を参照してください。

アプリを登録した後は、v2.0 エンドポイントに要求を送ることによって、Azure AD と通信を行います。

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

OAuth と OpenID Connect におけるフローはほぼすべて、情報のやり取りに&4; つの当事者が関係します。

![OAuth 2.0 Roles](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* **承認サーバー** は Azure AD v2.0 エンドポイントです。 承認サーバーは、ユーザー情報とアクセスに関するすべてのことを安全に処理します。 また、フロー内の当事者間の信頼関係も処理します。 ユーザーの本人性確認、リソースへのアクセス権の付与と取り消し、トークンの発行という役割を担います。 ID プロバイダーとも呼ばれます。
* **リソース所有者** とは通常、エンド ユーザーを指します。 データを所有し、そのデータ (またはリソース) へのアクセスをサード パーティに許可する権限を持つ当事者です。
* **OAuth クライアント** はアプリです。 アプリケーション ID によって識別されます。 通常は、エンド ユーザーと情報をやり取りする当事者です。 また、承認サーバーにトークンを要求します。 リソース所有者は、リソースにアクセスするためのアクセス許可をクライアントに付与する必要があります。
* **リソース サーバー** は、リソースまたはデータが存在する場所です。 承認サーバーを信頼し、OAuth クライアントを安全に認証、承認します。 また、ベアラー アクセス トークンを使用して、リソースへのアクセス許可を確実に付与します。

## <a name="policies"></a>ポリシー
おそらく、Azure AD B2C のポリシーは、サービスの最も重要な機能です。 Azure AD B2C は、ポリシーを導入することによって、標準の OAuth 2.0 や OpenID Connect プロトコルを拡張します。 ポリシーにより、Azure AD B2C は単なる認証および承認以外の多くの機能を実行できます。 ポリシーには、サインアップ、サインイン、プロファイル編集など、コンシューマーの ID エクスペリエンスが完全に記述されています。 ポリシーは、管理 UI で定義できます。 ポリシーは、HTTP 認証要求の特別なクエリ パラメーターを使用して実行できます。 ポリシーは、OAuth 2.0 および OpenID Connect の標準機能ではないので、理解するために時間をかける必要があります。 詳細については、 [Azure AD B2C ポリシー リファレンス ガイド](active-directory-b2c-reference-policies.md)を参照してください。

## <a name="tokens"></a>トークン
Azure AD B2C での OAuth 2.0 および OpenID Connect の実装では、ベアラー トークンが広範囲に使われています (JSON Web トークン (JWT) として表現されたベアラー トークンなど)。 ベアラー トークンは、保護されたリソースへの "ベアラー" アクセスを許可する簡易セキュリティ トークンです。 ベアラーは、トークンを提示できる任意の利用者を表します。 Azure AD は、ベアラー トークンを受信するには、最初に利用者を認証する必要があります。 ただし、転送中や保存時にトークンを保護するために必要な対策を講じていない場合、意図しない利用者によって傍受され、使用されるおそれがあります。

一部のセキュリティ トークンには、許可されていない利用者がトークンを使用できないようにするための組み込みメカニズムがありますが、ベアラー トークンにはこのメカニズムがありません。 トランスポート層セキュリティ (HTTPS) などのセキュリティ保護されたチャネルで転送する必要があります。 ベアラー トークンがセキュリティ保護されたチャネル以外で転送された場合、悪意のある利用者が中間者攻撃によってトークンを取得し、保護されたリソースへの未承認のアクセスに使用する可能性があります。 後で使用するためにベアラー トークンを保存またはキャッシュするときにも、同じセキュリティ原則が適用されます。 アプリケーションでは、常に安全な方法でベアラー トークンを転送および保存してください。

ベアラー トークンのセキュリティに関する考慮事項の詳細については、 [RFC 6750 セクション 5](http://tools.ietf.org/html/rfc6750)を参照してください。

Azure AD B2C で使われている各種トークンの詳細については、 [Azure AD のトークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。

## <a name="protocols"></a>プロトコル
要求の例を確認できる状態になったら、次のいずれかのチュートリアルを開始できます。 いずれも、特定の認証シナリオに対応しています。 どのフローを見ればよいかわからない場合は、 [Azure AD B2C で作成できるアプリの種類](active-directory-b2c-apps.md)を確認してください。

* [OAuth 2.0 でモバイル アプリケーションおよびネイティブ アプリケーションを作成する](active-directory-b2c-reference-oauth-code.md)
* [OpenID Connect を使用して Web アプリを構築する](active-directory-b2c-reference-oidc.md)
* [OAuth 2.0 暗黙的フローを使用してシングルページのアプリを作成する](active-directory-b2c-reference-spa.md)




<!--HONumber=Feb17_HO1-->


