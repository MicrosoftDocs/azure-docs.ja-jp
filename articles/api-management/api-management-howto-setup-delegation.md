---
title: "ユーザーの登録と成果物のサブスクリプションを委任する方法"
description: "ユーザーの登録と製品のサブスクリプションを Azure API Management でサード パーティに委任する方法について説明します。"
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 30ec6f45da114b6c7bc081f8a2df46f037de61fd
ms.openlocfilehash: 0be893406c6a20193b10b728fff2cec06f562069
ms.lasthandoff: 12/20/2016


---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>ユーザーの登録と成果物のサブスクリプションを委任する方法
委任を使用すると、開発者のサインイン/サインアップおよび成果物のサブスクリプション処理を、開発者ポータルの組み込みの機能ではなく、お客様の既存の Web サイトを使用して行うことができます。 これにより、お客様の Web サイトでユーザー データを保持し、独自の方法でこれらのステップの検証を実行できます。

## <a name="delegate-signin-up"> </a>開発者のサインインおよびサインアップ処理の委任
開発者のサインインおよびサインアップ処理をお客様の既存の Web サイトに委任するには、API Management 開発者ポータルから開始される該当する要求のエントリ ポイントとして動作する特殊な委任エンドポイントをサイト上に作成する必要があります。

最終的なワークフローは次のようになります。

1. 開発者が、API Management 開発者ポータルのサインインまたはサインアップ リンクをクリックします。
2. ブラウザーが、委任エンドポイントにリダイレクトされます。
3. 委任エンドポイントにより、ユーザーにサインインまたはサインアップを求める UI にリダイレクトされるか、ユーザーにサインインまたはサインアップを求める UI が表示されます。
4. サインインまたはサインアップが成功すると、ユーザーが最初の API Management 開発者ポータル ページにリダイレクトされます。

これを実現するには、最初に委任エンドポイント経由で要求をルーティングするように API Management を設定します。 API Management パブリッシャー ポータルで、**[セキュリティ]** をクリックし、**[委任]** タブをクリックします。 チェックボックスをクリックして、[サインインおよびサインアップの委任] を有効にします。

![[委任] ページ][api-management-delegation-signin-up]

* 特殊な委任エンドポイントの URL を決めて **[委任エンドポイント URL]** フィールドに入力します。 
* **[委任の認証キー]** フィールドに、要求が本当に Azure API Management から送信されたものかをどうかを確かめるための署名のコンピューティングに使用するシークレットを入力します。 **[生成]** ボタンをクリックすると、API Management によってランダムにキーを生成できます。

次に、 **委任エンドポイント**を作成する必要があります。 委任エンドポイントでは、次に示す操作を実行します。

1. 次の形式の要求を受け取ります。
   
   > "*http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={元のページの URL}&salt={文字列}&sig={文字列}*"
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
* **userId**: 管理するアカウントのユーザー ID
* **salt**: セキュリティ ハッシュの計算に使用される特殊な salt 文字列。
* **sig**: 自分で計算したハッシュとの比較に使用される、計算によって求められたセキュリティ ハッシュ。

## <a name="delegate-product-subscription"> </a>成果物のサブスクリプション処理の委任
成果物のサブスクリプション処理を委任するしくみは、ユーザーのサインイン/サインアップ処理の委任と似ています。 最終的なワークフローは次のようになります。

1. 開発者が API Management 開発者ポータルで成果物を選択し、[サブスクライブ] ボタンをクリックします。
2. ブラウザーが、委任エンドポイントにリダイレクトされます。
3. 委任エンドポイントにより、必要な成果物のサブスクリプション操作が実行されます。ここでは任意の操作を実行でき、課金情報を要求する別のページへのリダイレクトしたり、追加の質問をたずねたりできます。また、ユーザーによる操作を求めることなく単に情報を保存することもあります。

この機能を有効にするには、**[委任]** ページで **[成果物のサブスクリプションの委任]** をクリックします。

次に、次の操作を実行するように委任エンドポイントを設定します。

1. 次の形式の要求を受け取ります。
   
   > "*http://www.yourwebsite.com/apimdelegation?operation={操作}&productId={サブスクライブする成果物}&userId={要求元のユーザー}&salt={文字列}&sig={文字列}*"
   > 
   > 
   
    成果物のサブスクリプション処理のためのクエリ パラメーター:
   
   * **operation**: 委任要求の種類を識別します。 成果物のサブスクリプション要求の有効なオプションを次に示します。
     * "Subscribe": 提供された ID (以下を参照) を持つ特定の成果物をユーザーがサブスクライブするための要求。
     * "Unsubscribe": ユーザーの成果物のサブスクリプションを解除するための要求。
     * "Renew": (たとえば、有効期限が近づいている) サブスクリプションを更新するための要求。
   * **productId**: ユーザーから要求されたサブスクライブ対象の成果物の ID。
   * **userId**: 要求の対象のユーザーの ID。
   * **salt**: セキュリティ ハッシュの計算に使用される特殊な salt 文字列。
   * **sig**: 自分で計算したハッシュとの比較に使用される、計算によって求められたセキュリティ ハッシュ。
2. 要求の送信元が Azure API Management であることを確認します (省略できますが、セキュリティ上強く推奨されます)。
   
   * **productId**、**userId**、および **salt** のクエリ パラメーターに基づいて、文字列の HMAC-SHA512 を計算します。
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * 上の計算によって求められたハッシュを **sig** クエリ パラメーターの値と比較します。 2 つのハッシュ値が等しい場合は、次の手順に移動します。それ以外の場合は、要求を拒否します。
3. **operation** で要求された操作の種類 (課金、追加の質問など) に基づいて、成果物のサブスクリプション処理を実行します。
4. ユーザーがお客様の側の成果物を正常にサブスクライブすることができたら、 [成果物のサブスクリプションのための REST API を呼び出して]、ユーザーが API Management の成果物をサブスクライブできるようにします。

## <a name="delegate-example-code"> </a> コード例
これらのコード サンプルでは、パブリッシャー ポータルの [委任] 画面に設定された *委任検証キー*を取得して、署名の検証に使用する HMAC を作成し、渡された returnUrl の有効性を証明する方法を示します。 同じコードは、わずかに変更するだけで、productId と userId に対して機能します。

**returnUrl のハッシュを生成する C# のコード**

```c#
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

## <a name="next-steps"></a>次のステップ
委任に関する詳細については、次のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[シングル サインオン (SSO) トークンを要求]: http://go.microsoft.com/fwlink/?LinkId=507409
[ユーザーを作成]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[成果物のサブスクリプションのための REST API を呼び出して]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[コードの例を次に示します]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 

