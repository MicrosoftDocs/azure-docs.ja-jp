---
title: Azure Active Directory B2C でリソース所有者のパスワード資格情報フローを構成する | Microsoft Docs
description: Azure AD B2C でリソース所有者のパスワード資格情報フローを構成する方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: ca3fd76cfeb09fe4e18a04bff38e166928ef38b2
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401770"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-ad-b2c"></a>Azure AD B2C でリソース所有者のパスワード資格情報フロー を構成する

リソース所有者のパスワード資格情報 (ROPC) フローは、OAuth の標準認証フローです。アプリケーションは、証明書利用者とも呼ばれ、ID トークン、アクセス トークン、および更新トークンのユーザー ID とパスワードなどの有効な資格情報を交換します。 

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory (Azure AD) B2C では、次のオプションがサポートされています。

- **ネイティブ クライアント**:認証時のユーザー操作は、ユーザー側のデバイスでコードが実行されているときに発生します。 デバイスは、Android などのネイティブ オペレーティング システムで実行されているモバイル アプリケーションや、JavaScript などのブラウザーで実行されているモバイル アプリケーションの可能性があります。
- **パブリック クライアント フロー**:アプリケーションによって収集されたユーザーの資格情報のみが API 呼び出しで送信されます。 アプリケーションの資格情報は送信されません。
- **新しい要求の追加**:新しい要求を追加するために、ID トークンの内容を変更することができます。 

次のフローはサポートされていません。

- **サーバー対サーバー**:ID 保護システムは、対話の一環として、呼び出し元 (ネイティブ クライアント) から収集された信頼できる IP アドレスを必要とします。 サーバー側の API 呼び出しでは、サーバーの IP アドレスのみが使用されます。 失敗した認証の動的しきい値を超えた場合、ID 保護システムによって、繰り返される IP アドレスが攻撃者として特定される可能性があります。
- **機密性の高いクライアント フロー**:アプリケーション クライアント ID は検証されますが、アプリケーション シークレットは検証されません。

##  <a name="create-a-resource-owner-user-flow"></a>リソース所有者のユーザー フローを作成する

1. Azure AD B2C テナントの全体管理者として、Azure portal にサインインします。
2. Azure AD B2C テナントに切り替えるには、ポータルの右上隅で B2C ディレクトリを選択します。
3. **[User Flows]** をクリックし、**[新しいユーザー フロー]** を選択します。
4. **[すべて]** タブをクリックして、**[ROPC を使用してサインイン]** を選択します。
5. *ROPC_Auth* など、ユーザー フローの名前を入力します。
6. **[アプリケーション要求]** の **[さらに表示する]** をクリックします。
7. [表示名]、[メール アドレス]、[ID プロバイダー] など、アプリケーションに必要なアプリケーション要求を選択します。
8. **[OK]** を選択し、**[作成]** を選択します。
9. **[ユーザー フローを実行します]** をクリックします。

   次の例のようなエンドポイントが表示されます。

   `https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_ROPC_Auth`


## <a name="register-an-application"></a>アプリケーションを登録する

1. B2C の設定で、**[アプリケーション]** を選択し、**[追加]** を選択します。
2. *ROPC_Auth_app* などのアプリケーションの名前を入力します。
3. **[Web App/Web API]\(Web アプリ/Web API\)** に対して **[いいえ]** を選択し、次に **[Native client]\(ネイティブ クライアント\)** に対して **[はい]** を選択します。
4. その他のすべての値はそのままにして、**[作成]** を選択します。
5. 新しいアプリケーションを選択し、後で使用するためにアプリケーション ID をメモします。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

お気に入りの API 開発アプリケーションを使用して API 呼び出しを生成し、応答を確認して、ユーザー フローをデバッグします。 次の表の情報を使用して、POST 要求の本文として、このような呼び出しを作成します。
- *\<yourtenant.onmicrosoft.com>* は実際の B2C テナント名に置き換えます。
- *\<B2C_1A_ROPC_Auth>* は実際のリソース所有者のパスワード資格情報ポリシーのフル ネームに置き換えます。
- *\<bef2222d56-552f-4a5b-b90a-1988a7d634c3>* は実際の登録のアプリケーション ID に置き換えます。

`https://yourtenant.b2clogin.com/<yourtenant.onmicrosoft.com>/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

| キー | 値 |
| --- | ----- |
| username | leadiocl@outlook.com |
| password | Passxword1 |
| grant_type | password |
| scope | openid \<bef2222d56-552f-4a5b-b90a-1988a7d634c3> offline_access |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3> |
| response_type | token id_token |

*Client_id* は、以前にアプリケーション ID としてメモした値です。 更新トークンを受信する場合、*Offline_access* は省略可能です。 ユーザー名とパスワードとしては、Azure AD B2C テナントの既存ユーザーの資格情報を使う必要があります。

実際の POST 要求は次のようになります。

```
POST /yourtenant.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth HTTP/1.1
Host: yourtenant.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


オフライン アクセスで成功した応答は、次の例のようになります。

```json
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg" 
} 
```

## <a name="redeem-a-refresh-token"></a>更新トークンを利用する

次の表の情報を要求の本文として使用して、ここに示すような POST 呼び出しを作成します。

`https://yourtenant.b2clogin.com/<yourtenant.onmicrosoft.com>/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

| キー | 値 |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3> |
| resource | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3> |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*Client_id* と *resource* は、以前にアプリケーション ID としてメモした値です。 *Refresh_token* は、前述の認証呼び出しで受け取ったトークンです。

成功した応答は、次の例のようになります。

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhTmsifQ.eyJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNTE2ZmMwNjUtZmYzNi00YjkzLWFhNWEtZDZlZWRhN2NiYWM4L3YyLjAvIiwiZXhwIjoxNTMzNjc2NTkwLCJuYmYiOjE1MzM2NzI5OTAsImF1ZCI6IjljNTA2MThjLWY5NTEtNDlhNS1iZmU1LWQ3ODA4NTEyMWMzYSIsImlkcCI6IkxvY2FsQWNjb3VudCIsInN1YiI6ImJmZDgwODBjLTBjNDAtNDNjYS05ZTI3LTUyZTAyNzIyNWYyMSIsIm5hbWUiOiJEYXZpZE11IiwiZW1haWxzIjpbImRhdmlkd20xMDMwQGhvdG1haWwuY29tIl0sInRmcCI6IkIyQ18xX1JPUENfQXV0aCIsImF6cCI6IjljNTA2MThjLWY5NTEtNDlhNS1iZmU1LWQ3ODA4NTEyMWMzYSIsInZlciI6IjEuMCIsImlhdCI6MTUzMzY3Mjk5MH0.RULWeBR8--s5cCGG6XOi8m-AGyCaASx9W5B3tNUQjbVkHnGdo2_OUrnVoOZ1PTcrc1b0PQM2kVWi7NpYn57ifnqL_feTJPDbj9FJ8BmyxULdoECWxSM6KHsOPWZOIg5y1lNwN_IQ2HNF6UaDyYf1ZIM-jHr-uSfUnQXyWRnGDwNKX7TQbFmFk4oFMbPxTE7ioWAmxSnroiiB4__P9D0rUM1vf_qfzemf2ErIWSF9rGtCNBG-BvJlr3ZMCxIhRiIWNM2bVY0i3Nprzj0V8_FM6q8U19bvg9yDEzUcbe_1PMqzP3IrXW9N1XvQHupsOj8Keb7SmpgY1GG091X6wBCypw",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhTmsifQ.eyJleHAiOjE1MzM2NzY1OTAsIm5iZiI6MTUzMzY3Mjk5MCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzUxNmZjMDY1LWZmMzYtNGI5My1hYTVhLWQ2ZWVkYTdjYmFjOC92Mi4wLyIsInN1YiI6ImJmZDgwODBjLTBjNDAtNDNjYS05ZTI3LTUyZTAyNzIyNWYyMSIsImF1ZCI6IjljNTA2MThjLWY5NTEtNDlhNS1iZmU1LWQ3ODA4NTEyMWMzYSIsImlhdCI6MTUzMzY3Mjk5MCwiYXV0aF90aW1lIjoxNTMzNjcyOTkwLCJpZHAiOiJMb2NhbEFjY291bnQiLCJuYW1lIjoiRGF2aWRNdSIsImVtYWlscyI6WyJkYXZpZHdtMTAzMEBob3RtYWlsLmNvbSJdLCJ0ZnAiOiJCMkNfMV9ST1BDX0F1dGgiLCJhdF9oYXNoIjoiYW5hZ3QtX1NveUtBQV9UNFBLaHN4dyJ9.bPzpUFh94XFHXC_yR6qH_Unf6_hN-9-BjDXOzrdb1AuoU6-owQ3fWDxNBUbYEPALid3sgm4qhJ6BROFKryD8aWfrNyaErnYZwZ6rliHk4foa3JsbDgM3yNGPL0hzOFpC4Y9QhUjNgQOxvnQLtqbHVNonSvBc7VVPAjBDza44GowmvLORfJ1qkTjdrFM75HlLVeQch8cUNf-Ova77JdG5WHgYgqRhAq1OhV68YgEpQkARyz77zbAz9zZEHZZlgsli8UV6C-CPcmoHbwS-85mLzF9nLxhzjgIXJwckB6I7lvTpfuRtaqZIb3pMYeHZJaxaNLDvq9Qe4N-danXABg1B2w",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAifQ..8oC4Q6aKdr35yMWm.p43lns-cfWNFbtmrhvtssQXCItb3E9aSLafZJ6nKnnpXGQ-ZapOOyH7hPK7AN_RT7NMsQwNdy0Fyv_hOMrFbMPZNvHSa91RsQIvBZ73-CVy0HNF0grSezjCATg4NVHfricuQVegEmZKFOoNP6TaMC2kIlEi3rhrrO8VE3ZFQ3Jjo6j91BJaE9ybb02HWOoKqlzHiazwQyUHujw_R0TyXaQCI_gtLARr5QUXm7hlAfHhxR9uewQKlRbeuMH8nCMLSMASCJyzfeSJTjXmA0F0VrXozrqzOJdyy0EETPR7oA48MJ9l6C2sy2ZELkqpOM3xhbhV-Re7nM09b8DeWuCw7VNTcQc9DKnIHDR-H5U2Tc-lMJQadgUNZv7KGSRGTyprWb7wF7FEPnRNID5PCDV_N_yoQpI7VvJO_NotXEgHFo7OHs5Gsgwpl5mrDtymYzIMM7onTflOlu46em_qltji7xcWNOuHq4AeOlcY9ZythZgJH7livljReTwyX8QuUwpomXVEUGDc5pAnvgSozxnUbM7AlwfUeJZRT45P7L7683RSqChdNxiQk0sXUECqxnFxMAz4VUzld2yFe-pzvxFF4_feQjBEmSCAvekpvJUrEticEs4QzByV5UZ2ZCKccijFTg4doACiCo_z13JTm47mxm-5jUhXOQqiL69oxztk.KqI-z2LlC77lvwqmeFtdGQ",
    "refresh_token_expires_in": 1209600
}
```

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>好みのネイティブ SDK を使用して実装するか、App-Auth を使用する

Azure AD B2C の実装は、パブリック クライアント リソース所有者のパスワード認証情報に関する OAuth 2.0 標準を満たしており、ほとんどのクライアント SDK と互換性があります。 このフローは、AppAuth for iOS と AppAuth for Android を使用して、運用環境で幅広くテストしました。 最新情報については、[最新のベスト プラクティスを実装する OAuth 2.0 と OpenID Connect のネイティブ App SDK](https://appauth.io/) に関するページを参照してください。

Azure AD B2C で使用するように構成された作業サンプル ([Android 用](https://aka.ms/aadb2cappauthropc)、[iOS 用](https://aka.ms/aadb2ciosappauthropc)) を GitHub からダウンロードしてください。




