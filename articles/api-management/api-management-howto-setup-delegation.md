---
title: ユーザーの登録と成果物のサブスクリプションを委任する方法
description: ユーザーの登録と製品のサブスクリプションを Azure API Management でサード パーティに委任する方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: 63ff91c6b4db351e5ec72973874466cff74432b5
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073443"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>ユーザーの登録と成果物のサブスクリプションを委任する方法

委任を使用すると、開発者のサインイン/サインアップおよび製品のサブスクリプション処理を、開発者ポータルの組み込みの機能ではなく、お客様の既存の Web サイトを使用して行うことができます。 これにより、お客様の Web サイトでユーザー データを保持し、独自の方法でこれらのステップの検証を実行できます。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>開発者のサインインおよびサインアップ処理の委任

開発者のサインインおよびサインアップ処理を既存の Web サイトに委任するには、サイト上で特別な委任エンドポイントを作成する必要があります。 このエンドポイントは、API Management 開発者ポータルから開始された要求のエントリ ポイントとして機能する必要があります。

最終的なワークフローは次のようになります。

1. 開発者が、API Management 開発者ポータルのサインインまたはサインアップ リンクをクリックします。
2. ブラウザーが、委任エンドポイントにリダイレクトされます。
3. 委任エンドポイントにより、ユーザーにサインインまたはサインアップを求める UI にリダイレクトされるか、ユーザーにサインインまたはサインアップを求める UI が表示されます。
4. サインインまたはサインアップが成功すると、ユーザーが最初の API Management 開発者ポータル ページにリダイレクトされます。

これを実現するには、最初に委任エンドポイント経由で要求をルーティングするように API Management を設定します。 Azure portal で API Management リソースの **[セキュリティ]** を探し、 **[委任]** 項目をクリックします。 チェックボックスをクリックして、[Delegate sign in & sign up]\(サインインおよびサインアップ処理の委任\) を有効にします。

![[委任] ページ][api-management-delegation-signin-up]

* 特殊な委任エンドポイントの URL を決めて **[委任エンドポイント URL]** フィールドに入力します。 
* [委任の認証キー] フィールドに、要求が本当に Azure API Management から送信されたものかをどうかを確かめるための署名のコンピューティングに使用するシークレットを入力します。 **[生成]** ボタンをクリックすると、API Management によってランダムにキーを生成できます。

次に、 **委任エンドポイント**を作成する必要があります。 委任エンドポイントでは、次に示す操作を実行します。

1. 次の形式の要求を受け取ります。
   
   > "*http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={元のページの URL}&salt={文字列}&sig={文字列}* "
   > 
   > 
   
    サインイン/サインアップ処理のためのクエリ パラメーター:
   
   * **operation**: 委任要求の種類を識別します。この場合は **[SignIn]** のみを指定できます。
   * **returnUrl**: ユーザーがサインインまたはサインアップ リンクをクリックしたページの URL。
   * **salt**: セキュリティ ハッシュの計算に使用される特殊な salt 文字列。
   * **sig**: 自分で計算したハッシュとの比較に使用される、計算によって求められたセキュリティ ハッシュ。
2. 要求の送信元が Azure API Management であることを確認します (省略できますが、セキュリティ上強く推奨されます)。
   
   * **returnUrl** および **salt** のクエリ パラメーターに基づいて、文字列の HMAC-SHA512 ハッシュを計算します ([コードの例を次に示します])。
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * 上の計算によって求められたハッシュを **sig** クエリ パラメーターの値と比較します。 2 つのハッシュ値が等しい場合は、次の手順に移動します。それ以外の場合は、要求を拒否します。
3. サインイン/サインアップの要求を受け取っていることを確認します。**operation** クエリ パラメーターが "**SignIn**" に設定されます。
4. サインインまたはサインアップのための UI をユーザーに表示します。
5. サインアップの場合は、API Management に対応するアカウントを作成する必要があります。 [ユーザーを作成] します。 このとき、ユーザー ID をユーザー ストア内のユーザー ID と同じに設定するか、または追跡が可能な ID に設定してください。
6. ユーザーが正常に認証されたら、次の操作を行います。
   
   * [シングル サインオン (SSO) トークンを要求] します。
   * 上記の API 呼び出しによって受け取った SSO URL に returnUrl クエリ パラメーターを付加します。
     
     > 例: https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * 上記の手順で生成された URL にユーザーをリダイレクトします。

**SignIn** 操作に加えて、ここまでの手順に従い、さらに次の操作のいずれかを使用することにより、アカウント管理も実行できます。

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

アカウントの管理操作を実行するには、次のクエリ パラメーターを渡す必要があります。

* **operation**: 委任要求の種類を識別します (ChangePassword、ChangeProfile、または CloseAccount)
* **userId**: 管理するアカウントのユーザー ID。
* **salt**: セキュリティ ハッシュの計算に使用される特殊な salt 文字列。
* **sig**: 自分で計算したハッシュとの比較に使用される、計算によって求められたセキュリティ ハッシュ。

## <a name="delegate-product-subscription"> </a>成果物のサブスクリプション処理の委任
製品のサブスクリプション処理を委任するしくみは、ユーザーのサインイン/サインアップ処理の委任と似ています。 最終的なワークフローは次のようになります。

1. 開発者が API Management 開発者ポータルで製品を選択し、[サブスクライブ] ボタンをクリックします。
2. ブラウザーが、委任エンドポイントにリダイレクトされます。
3. 委任エンドポイントで、必要な製品サブスクリプション手順が実行されます。 この手順の設計は、お客様に任されています。 この手順には、別のページへのリダイレクトを実行して請求先情報の入力を求める処理を含めることもできます。リダイレクト先のページでは、追加の質問をたずねることも、情報を保存するだけでユーザーのアクションを求めないこともできます。

この機能を有効にするには、 **[委任]** ページで **[成果物のサブスクリプションの委任]** をクリックします。

次に、以下の操作を実行するように委任エンドポイントを設定します。

1. 次の形式の要求を受け取ります。
   
   > "*http:\//www.yourwebsite.com/apimdelegation?operation={操作}&productId={サブスクライブする成果物}&userId={要求元のユーザー}&salt={文字列}&sig={文字列}* "
   >
   
    成果物のサブスクリプション処理のためのクエリ パラメーター:
   
   * **operation**: 委任要求の種類を識別します。 成果物のサブスクリプション要求の有効なオプションを次に示します。
     * "Subscribe": 提供された ID (以下を参照) を持つ特定の成果物をユーザーがサブスクライブするための要求。
     * "Unsubscribe": ユーザーの成果物のサブスクリプションを解除するための要求。
     * "Renew": (たとえば、有効期限が近づいている) サブスクリプションを更新するための要求。
   * **productId**: ユーザーから要求されたサブスクライブ対象の成果物の ID。
   * **subscriptionId**: "*登録解除*" および "*更新*" 時 - 製品のサブスクリプション ID。
   * **userId**: 要求の対象のユーザーの ID。
   * **salt**: セキュリティ ハッシュの計算に使用される特殊な salt 文字列。
   * **sig**: 自分で計算したハッシュとの比較に使用される、計算によって求められたセキュリティ ハッシュ。

2. 要求の送信元が Azure API Management であることを確認します (省略できますが、セキュリティ上強く推奨されます)。
   
   * **productId**、**userId**、および **salt** のクエリ パラメーターに基づいて、文字列の HMAC-SHA512 を計算します。
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * 上の計算によって求められたハッシュを **sig** クエリ パラメーターの値と比較します。 2 つのハッシュ値が等しい場合は、次の手順に移動します。それ以外の場合は、要求を拒否します。
3. **operation** で要求された操作の種類 (課金、追加の質問など) に基づいて、製品のサブスクリプションを処理します。
4. お客様の側で製品へのユーザーのサブスクリプションを正常に処理できたら、API Management 製品へのユーザーのサブスクリプション処理を行います。そのためには、[サブスクリプションのための REST API の呼び出し]。

## <a name="delegate-example-code"> </a> コード例

以下のコード サンプルは、次の操作を行う方法を示しています｡

* "*委任検証キー*" を取得する。このキーは、パブリッシャー ポータルの [委任] 画面で設定されます。
* HMAC を作成する。作成した HMAC は、署名の検証のために使用され、これにより、渡された returnUrl の有効性が証明されます。

同じコードは、わずかに変更するだけで、productId と userId に対して機能します。

**returnUrl のハッシュを生成する C# のコード**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**returnUrl のハッシュを生成する NodeJS のコード**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

## <a name="next-steps"></a>次の手順
委任に関する詳細については、次のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[シングル サインオン (SSO) トークンを要求]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[ユーザーを作成]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[サブスクリプションのための REST API の呼び出し]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[コードの例を次に示します]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
