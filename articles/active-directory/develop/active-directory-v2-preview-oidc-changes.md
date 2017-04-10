---
title: "Azure AD v2.0 エンドポイントへの変更 | Microsoft Docs"
description: "アプリ モデル v2.0 パブリック プレビュー プロトコルに対して行われた変更について説明します。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e6c5b891-0b5d-47dc-8184-5d0ef6a1a006
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 3e0bb32a6c60011d71606c896cc506f430bc3c27
ms.openlocfilehash: ce60eb0586b4756aabab0bad830e7fc3ca506097
ms.lasthandoff: 03/29/2017


---
# <a name="important-updates-to-the-v20-authentication-protocols"></a>v2.0 の認証プロトコルに対する重要な更新
開発者を対象としています。 これからの 2 週間、プレビュー期間中に作成したすべてのアプリを大きく変える可能性があるいくつかの変更を v2.0 認証プロトコルに対して行います。  

## <a name="who-does-this-affect"></a>何に影響がありますか?
次の v2.0 集約型認証エンドポイントを使用するよう記述されたすべてのアプリです。

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

v2.0 エンドポイントの詳細については、 [ここ](active-directory-appmodel-v2-overview.md)を参照してください。

OpenID Connect または OAuth Web ミドルウェアのいずれかを使用したり、その他のサード パーティ製のライブラリを使用したりしてまたは認証を行い、v2.0 プロトコルを直接コーディングし、v2.0 のエンドポイントを使用するアプリを構築した場合、プロジェクトをテストする準備をし、必要に応じてそれを変更する必要があります。

## <a name="who-doesnt-this-affect"></a>何が影響を受けませんか?
次の実稼働の Azure AD 認証エンドポイントに対して記述されたすべてのアプリです。

```
https://login.microsoftonline.com/common/oauth2/authorize
```

このプロトコルには変更はなく、変更はありません。

さらに、アプリで ADAL ライブラリ **のみ** を使用して認証を行っている場合、何も変更する必要はありません。  ADAL によりアプリは変更の影響を受けません。  

## <a name="what-are-the-changes"></a>どのような変更があったのですか?
### <a name="removing-the-x5t-value-from-jwt-headers"></a>JWT ヘッダーから x5t 値を削除する
v2.0 エンドポイントは、トークンに関連するメタデータを含むヘッダー パラメーター セクションを含む JWT トークンを広範に使用します。  現在の JWT のいずれかのヘッダーをデコードすると、以下のようなものがあるはずです。

```
{ 
    "type": "JWT",
    "alg": "RS256",
    "x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
    "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

ここで "x5t" と "kid" の両プロパティは、OpenID Connect メタデータ エンドポイントから取得されたトークンの署名の検証に使用される公開キーを識別します。

ここで行う変更は、"x5t" プロパティの削除です。  トークンの検証には同じメカニズムを使用し続けることができますが、正しい公開キーを取得するには、OpenID Connect プロトコルで指定されている、"kid" プロパティにのみ依存する必要があります。 

> [!IMPORTANT]
> **確認事項: アプリが x5t 値の存在に依存していないことを確認してください。**
> 
> 

### <a name="removing-profileinfo"></a>profile_info を削除する
今まで、v2.0 エンドポイントでは、`profile_info` というトークンの応答で、base64 でエンコードされた JSON オブジェクトを返していました。  次に要求を送信することによって、v2.0 エンドポイントからアクセス トークンを要求すると、

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

応答は、次の JSON オブジェクトのようになります。

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

`profile_info` 値には、表示名、名、姓、電子メール アドレス、ID など、そのアプリにサインインしているユーザーの情報が含まれます。  `profile_info` は主に、トークンのキャッシュと表示目的で使用されていました。

今後 `profile_info` 値は削除されますが、この情報は若干異なる場所で開発者用に提供しますので心配はしないでください。  `profile_info` の代わりに、v2.0 エンドポイントは各トークンの応答に `id_token` を返すようになります。

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

profile_info から受け取ったのと同じ情報を取得するために、id_token をデコードおよび解析することができます。  この id_token は、OpenID Connect で指定された内容の JSON Web Token (JWT) です。  これを行うコードは非常に似ています。id_token の中央部分 (本文) を抽出すると、base64 によってデコードされ、その中の JSON オブジェクトにアクセスできるようになります。

これから 2 週間は、存在している `id_token` または `profile_info` のいずれかから、ユーザー情報を取得するようアプリをコーディングしてください。  このようにすれば、変更があっても、アプリは `profile_info` から `id_token` への切り替えをシームレスに処理できます。

> [!IMPORTANT]
> **確認事項: アプリが `profile_info` 値の存在に依存していないことを確認してください。**
> 
> 

### <a name="removing-idtokenexpiresin"></a>id_token_expires_in を削除する
`profile_info` と同様に、応答から `id_token_expires_in` パラメーターも削除します。  今まで、v2.0 エンドポイントは、各 id_token の応答と共に `id_token_expires_in` 値を返していました。承認応答の例を次に示します。

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

またはトークン応答の例を次に示します。

```
{ 
    "token_type": "Bearer",
    "id_token_expires_in": 3599,
    "scope": "openid",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

`id_token_expires_in` 値は、id_token が有効である秒数を示します。  今後 `id_token_expires_in` 値は完全に削除します。  代わりに、OpenID Connect 標準の `nbf` と `exp` 要求を使用し、id_token の有効性を確認できます。  これらの要求の詳細については、「 [v2.0 トークンのリファレンス](active-directory-v2-tokens.md) 」を参照してください。

> [!IMPORTANT]
> **確認事項: アプリが `id_token_expires_in` 値の存在に依存していないことを確認してください。**
> 
> 

### <a name="changing-the-claims-returned-by-scopeopenid"></a>scope=openid で返される要求を変更する
この変更が最も大きなものになります。実際、v2.0 エンドポイントを使用するほぼすべてのアプリに影響します。  多くのアプリケーションは、次のように `openid` スコープを使用し v2.0 エンドポイントに要求を送信します。

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

現在、ユーザーが `openid` スコープに同意すると、アプリでは、結果の id_token のユーザーに関する情報を多数取得できます。  これらの要求には、ユーザー名、推奨ユーザー名、電子メール アドレス、オブジェクト ID などが含まれます。

この更新では、OpenID Connect の仕様により適合するよう `openid` スコープがアプリにアクセスを許可する情報を変更します。  `openid` スコープでは、id_token の `sub` 要求でアプリに、ユーザーのサインインの許可と、ユーザーのアプリ固有の ID の受信のみを許可します。  `openid` スコープのみが付与された id_token の要求には、個人を特定できる情報は含まれません。  id_token 要求の例を次に示します。

```
{ 
    "aud": "580e250c-8f26-49d0-bee8-1c078add1609",
    "iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
    "iat": 1449520283,
    "nbf": 1449520283,
    "exp": 1449524183,
    "nonce": "12345",
    "sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
    "tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
    "ver": "2.0",
}
```

アプリ内のユーザーの個人を特定できる情報 (PII) を取得するには、アプリからユーザーに追加のアクセス許可を要求する必要があります。  これを実行できる OpenID Connect 仕様の 2 つの新しいスコープ、`email` スコープと `profile` スコープのサポートが導入されました。

* `email` スコープは非常に明解です。id_token の `email` 要求を使用すると、アプリでユーザーのプライマリ電子メール アドレスにアクセスできます。  なお、`email` 要求は id_tokens には必ずしもあるわけではありません。ユーザーのプロファイルにある場合のみ含まれます。
* `profile` スコープでは、アプリに名前、推奨ユーザー名、オブジェクト ID など、ユーザーに関するその他のすべての基本的な情報へのアクセスを許可します。

これにより、最小限の公開でアプリをコーディングできます。アプリがそのジョブを実行するために必要な情報セットのみをユーザーに要求できます。  アプリが現在受け取っているユーザー情報をすべて受け取り続けたい場合、承認要求に次の 3 つのスコープすべてを含める必要があります。

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

即座にアプリで、`email` と `profile` のスコープは送信でき、v2.0 エンドポイントでそれら 2 つのスコープを受け取ると、必要に応じてユーザーからアクセス許可が求められ始めます。  ただし、 `openid` のスコープの解釈の変更は数週間実行されません。

> [!IMPORTANT]
> **確認事項: アプリでユーザーの情報が必要な場合は、`profile` と `email` のスコープを追加します。**  ADAL には、既定で要求にこれらのアクセス許可が含まれることに注意してください。 
> 
> 

### <a name="removing-the-issuer-trailing-slash"></a>issuer の末尾のスラッシュを削除する
今まで、v2.0 エンドポイントからのトークンに示される issuer 値は次の形式になっていました。

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

ここでの guid は、トークンを発行した Azure AD テナントの tenantId でした。  これらの変更により、issuer 値は、両方のトークンおよび OpenID Connect 検出ドキュメントで

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

次のようになります。

> [!IMPORTANT]
> **確認事項: issuer 値の検証時、アプリで末尾のスラッシュの有無にかかわらず、issuer 値が受け入れられることを確認してください。**
> 
> 

## <a name="why-change"></a>なぜ変更するのか?
これらの変更を導入する主な目的は、OpenID Connect 標準の仕様に準拠するためです。  OpenID Connect に準拠することにより、Microsoft ID サービスおよびその他の業界の ID サービスと統合することの違いを最小限に抑えたいと考えています。  開発者が Microsoft での違いのために、ライブラリを変更することがなく自分の好みのオープン ソースの認証ライブラリを使用できるようにすることが目的です。

## <a name="what-can-you-do"></a>何ができますか?
現時点では、上記のすべての変更の適用を開始できます。  即座に次を実行する必要があります。

1. **`x5t` ヘッダー パラメーターの依存関係をすべて削除します。**
2. **トークン応答で、適切に `profile_info` から `id_token` に移行できるようにします。**
3. **`id_token_expires_in` 応答パラメーターの依存関係をすべて削除します。**
4. **アプリにユーザーの基本情報が必要な場合、アプリに `profile` スコープと `email` スコープを追加します。**
5. **トークンで issuer 値が末尾のスラッシュの有無にかかわらず受け入れられるようにします。**

[v2.0 プロトコルに関するドキュメント](active-directory-v2-protocols.md) では、これらの変更を反映するよう既に更新されているので、コードの更新時の参照として使用することができます。

変更の範囲に関して他に質問がある場合、Twitter の @AzureAD までお問い合わせください。

## <a name="how-often-will-protocol-changes-occur"></a>プロトコルの変更はどれくらいの頻度で発生するのですか?
今後、認証プロトコルには大きな変更はないと予測しています。  近いうちにこの種の更新を行わなくて済むよう、意図的にこれらの変更を 1 つのリリースにバンドルしています。  もちろん、集中型の v2.0 認証サービスにユーザーが利用できる機能は追加し続けますが、それらは追加型の変更で、既存のコードは壊さないものにします。

最後に、プレビュー期間中にお試しいただいたことに感謝したいと思います。  これまで、早期に採用していただいた方々の洞察および体験は非常に貴重でした。今後も引き続きご意見やアイディアを共有していただけると幸いです。

コーディングをお楽しみください!

Microsoft ID 部門


