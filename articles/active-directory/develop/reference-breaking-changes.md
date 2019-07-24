---
title: Azure Active Directory 重大な変更リファレンス | Microsoft Docs
description: アプリケーションに影響を与える可能のある Azure AD プロトコルの変更について説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fcc400f952cc89f5fb4bf6e8d6f0f331483868e
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401307"
---
# <a name="whats-new-for-authentication"></a>認証の新機能 

>このページの更新に関する通知を受け取るには、 [この URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) を RSS フィード リーダーに追加してください。

認証システムは、セキュリティの向上と規格への準拠を確保するために、継続的に機能の変更と追加を行います。 常に最新の開発情報を把握していただけるよう、この記事では以下の事項に関する情報を提供します。

- 最新の機能
- 既知の問題
- プロトコルの変更
- 非推奨の機能

> [!TIP] 
> このページは頻繁に更新されるため、定期的にアクセスしてご確認ください。 特に明記されていない限り、これらの変更は新規に登録されたアプリケーションに対してのみ適用されます。  

## <a name="upcoming-changes"></a>今後の変更

現時点ではスケジュールされていません。 

## <a name="march-2019"></a>2019 年 3 月

### <a name="looping-clients-will-be-interrupted"></a>クライアントのループ処理が中断されます

**発効日**:2019 年 3 月 25 日

**影響を受けるエンドポイント**:v1.0 と v2.0 の両方

**影響を受けるプロトコル**:すべてのフロー

クライアント アプリケーションが誤動作し、短期間に数百の同じログイン要求を発行する場合があります。  これらの要求は成功する場合もしない場合もありますが、そのすべてがユーザー エクスペリエンスの低下や IDP のワークロードの増加につながるため、すべてのユーザーの待ち時間が長くなり、IDP の可用性が低下します。  これらのアプリケーションは通常の使用の範囲外で動作しており、正しく動作するように更新する必要があります。  

重複した要求を複数回発行するクライアントには `invalid_grant` エラー: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` が送信されます。 

ほとんどのクライアントは、動作を変更してこのエラーを回避する必要はありません。  正しく構成されていない (トークンのキャッシュを使用していない、または既にプロンプト ループを示している) クライアントのみがこのエラーの影響を受けます。  クライアントは、次の要因に対して (Cookie 経由で) ローカルにインスタンスごとに追跡されます。

* ユーザー ヒント (存在する場合)

* 要求されているスコープまたはリソース

* クライアント ID

* リダイレクト URI

* 応答の種類とモード

短期間 (5 分) に複数の (15 を超える) 要求を発行しているアプリは、ループ処理していることを示す `invalid_grant` エラーを受信します。  要求されているトークンには十分に長い有効期間 (最小 10 分、既定では 60 分) があるため、この期間にわたって繰り返される要求は必要ありません。  

すべてのアプリが、確認なしでトークンを要求するのではなく、対話型プロンプトを示すことによって `invalid_grant` を処理する必要があります。  このエラーを回避するために、クライアントは、受信したトークンを正しくキャッシュしていることを確認する必要があります。


## <a name="october-2018"></a>2018 年 10 月

### <a name="authorization-codes-can-no-longer-be-reused"></a>認証コードを再利用できなくなりました

**発効日**:2018 年 11 月 15 日

**影響を受けるエンドポイント**:v1.0 と v2.0 の両方

**影響を受けるプロトコル**:[コード フロー](v2-oauth2-auth-code-flow.md)

2018 年 11 月 15 日以降、Azure AD では、以前使用されていた、アプリの認証コードの受け入れが停止されます。 このセキュリティの変更により、Azure AD と OAuth の仕様が一致するようになります。この変更は、v1 と v2 両方のエンドポイントに適用されます。

お使いのアプリで承認コードを再利用して複数のリソースに対するトークンを取得している場合は、コードを使用して更新トークンを取得した後、その更新トークンを使用して他のリソース用のトークンを追加取得することお勧めします。 承認コードは 1 回しか使用できませんが、更新トークンは複数のリソースで複数回使用できます。 OAuth コード フローの使用時に新しいアプリで認証コードを再利用しようとすると、invalid_grant エラーが発生します。

更新トークンについて詳しくは、「[アクセス トークンの更新](v1-protocols-oauth-code.md#refreshing-the-access-tokens)」をご覧ください。  ADAL または MSAL を使用する場合、これはライブラリによって処理され、'AcquireTokenByAuthorizationCodeAsync' の 2 つ目のインスタンスを 'AcquireTokenSilentAsync' に置き換えます。 

## <a name="may-2018"></a>2018 年 5 月

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID トークンは OBO フローに使用できません

**日付**:2018 年 5 月 1 日

**影響を受けるエンドポイント**:v1.0 と v2.0 の両方

**影響を受けるプロトコル**:暗黙的フローと [OBO フロー](v1-oauth2-on-behalf-of-flow.md)

2018 年 5 月 1 日以降、id_tokens は新しいアプリケーションの OBO フローでアサーションとして使用できません。 代わりに、アクセス トークンを使用して、同じアプリケーションのクライアントと中間層の間でも、API のセキュリティを確保する必要があります。 2018 年 5 月 1 日より前に登録されたアプリは、引き続き動作し、id_tokens をアクセス トークンに交換することができますが、このパターンはベスト プラクティスとは見なされません。

この変更を回避するには、次の操作を行います。

1. 1 つ以上のスコープを使用して、アプリケーション用の Web API を作成します。 この明示的なエントリ ポイントにより、きめ細かな制御とセキュリティが可能になります。
1. アプリのマニフェスト、[Azure portal](https://portal.azure.com)、または[アプリ登録ポータル](https://apps.dev.microsoft.com)で、アプリが暗黙のフローを介してアクセス トークンを発行できることを確認します。 これは `oauth2AllowImplicitFlow` キーによって制御されます。
1. クライアント アプリケーションで `response_type=id_token` を使用して id_token を要求した場合、上記で作成した Web API に対してもアクセス トークン (`response_type=token`) を要求します。 したがって、v2.0 エンドポイントを使用する場合、`scope` パラメータは `api://GUID/SCOPE` と同様のものになります。 v1.0 エンドポイントでは、`resource` パラメータを Web API のアプリ URI にする必要があります。
1. このアクセストークンを、id_token の代わりに、中間層に渡します。  
