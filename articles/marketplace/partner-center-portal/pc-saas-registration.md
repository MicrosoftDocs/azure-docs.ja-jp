---
title: SaaS アプリケーションを登録する - Azure Marketplace
description: Azure portal を使用して SaaS アプリケーションを登録し、Azure Active Directory セキュリティ トークンを受け取る方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 39a0830806d2d9c7358d175cae703e9c81c45b02
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93130019"
---
# <a name="register-a-saas-application"></a>SaaS アプリケーションを登録する

この記事では、Microsoft [Azure portal](https://portal.azure.com/) を使用して SaaS アプリケーションを登録する方法と、公開元のアクセス トークン (Azure Active Directory アクセス トークン) を取得する方法について説明します。 公開元はこのトークンを使用して、SaaS Fulfillment API を呼び出すことによって、SaaS アプリケーションを認証します。  Fulfillment API は、OAuth 2.0 クライアント資格情報を使用して、サービス間のアクセス トークン要求を行うための Azure Active Directory (v1.0) エンドポイントのフローを許可します。

Azure Marketplace では、SaaS サービスがエンド ユーザーに対して使用する認証方法に対して、制約がありません。 次のフローは、SaaS Service を Azure Marketplace で認証する場合にのみ必要です。

Azure AD (Active Directory) の詳細については、[認証の概要](../../active-directory/develop/authentication-vs-authorization.md)に関するページを参照してください。

## <a name="register-an-azure-ad-secured-app"></a>Azure AD で保護されるアプリを登録する

アプリケーションで Azure AD の機能を使用するには、まず Azure AD テナントにそのアプリケーションを登録する必要があります。 この登録プロセスでは、アプリケーションに関する詳細の一部を Azure AD に渡します。 Azure portal を使用して新しいアプリケーションを登録するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. ご利用のアカウントで複数の Azure AD テナントにアクセスできる場合は、右上隅でアカウントをクリックし、ポータルのセッションを目的のテナントに設定します。
3. 左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** サービスをクリックし、 **[アプリの登録]** 、 **[新しいアプリケーションの登録]** の順にクリックします。

    ![SaaS AD のアプリ登録](./media/saas-offer-app-registration-v1.png)

4. 作成\' ページで、アプリケーションの登録情報を入力します。
    -   **Name**:わかりやすいアプリケーション名を入力します
    -   **アプリケーションの種類**:  
        
        セキュリティで保護されたサーバーにインストールされている [クライアント アプリケーション](../../active-directory/develop/developer-glossary.md#client-application)と [リソースおよび API アプリケーション](../../active-directory/develop/developer-glossary.md#resource-server)については、 **[Web アプリ/API]** を選択します。 OAuth のコンフィデンシャル [Web クライアント](../../active-directory/develop/developer-glossary.md#web-client)と、パブリック [ユーザーエージェントベース クライアント](../../active-directory/develop/developer-glossary.md#user-agent-based-client)の場合には、この設定を使用します。
        同じアプリケーションでクライアントとリソース/API を両方とも公開することもできます。

        Web アプリケーションの具体的な例については、[Azure AD 開発者向けガイド](../../active-directory/develop/index.yml)の[開始](../../active-directory/develop/quickstart-create-new-tenant.md)セクションで利用できるクイックスタート ガイド付きセットアップを確認してください。

5. 完了したら、 **[登録]** をクリックします。  Azure AD によって、新しいアプリケーションに一意の "*アプリケーション ID*" が割り当てられます。 API にアクセスする 1 つのアプリだけをシングル テナントとして登録することをお勧めします。

6. クライアント シークレットを作成するには、 **[証明書とシークレット]** ページに移動し、 **[+ 新しいクライアント シークレット]** をクリックします。  シークレット値は、コードで使用するため、必ずコピーしてください。

**Azure AD アプリ ID** は自分の公開元 ID に関連付けられているため、自分のすべてのプランで同じ "*アプリ ID*" が使用されるようにしてください。

>[!Note]
>公開元がパートナー センターに 2 つの異なるアカウントを持っている場合は、2 つの異なる Azure AD アプリ ID を使用する必要があります。  パートナー センターの各パートナー アカウントでは、このアカウントを使用して公開されるすべての SaaS プランに対して、一意の Azure AD アプリ ID を使用する必要があります。

## <a name="how-to-get-the-publishers-authorization-token"></a>公開元の承認トークンを取得する方法

アプリケーションを登録したら、公開元の承認トークン (Azure AD アクセス トークン、Azure AD V1 エンドポイントを使用) をプログラムで要求できます。 公開元は、さまざまな SaaS Fulfillment API を呼び出すときに、このトークンを使用する必要があります。 このトークンは 1 時間だけ有効です。 

これらのトークンの詳細については、「[Azure Active Directory アクセス トークン](../../active-directory/develop/access-tokens.md)」を参照してください。  下のフローでは、V1 エンド ポイント トークンが使用されていることに注意してください。

### <a name="get-the-token-with-an-http-post"></a>HTTP POST を使用してトークンを取得する

#### <a name="http-method"></a>HTTP メソッド

Post<br>

##### <a name="request-url"></a>*要求 URL* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*URI パラメーター*

|  パラメーター名    |  必須         |  説明 |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  True      |  登録された AAD アプリケーションのテナント ID。 |

##### <a name="request-header"></a>*要求ヘッダー*

|  ヘッダー名       |  必須         |  説明 |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  True      |  要求に関連付けられたコンテンツの種類。 既定値は `application/x-www-form-urlencoded` です。 |

##### <a name="request-body"></a>*要求本文*

|  プロパティ名     |  必須         |  説明 |
|  ---------------   |  ---------------  | ------------ |
|  `grant_type`      |  True      |  付与タイプ。 `"client_credentials"`を使用します。 |
|  `client_id`       |  True      |  Azure AD アプリに関連付けられているクライアントまたはアプリの識別子。 |
|  `client_secret`   |  True      |  Azure AD アプリに関連付けられているシークレット。 |
|  `resource`        |  True      |  トークンを要求されたターゲット リソース。 この場合、Marketplace SaaS API は常にターゲット リソースであるため、`20e940b3-4c77-4b0b-9a53-9e16a1b010a7` を使用します。 |

##### <a name="response"></a>*Response*

|  名前     |  Type         |  説明 |
|  ------   |  ---------------  | ------------ |
|  200 OK   |  TokenResponse    |  要求成功。 |

##### <a name="tokenresponse"></a>*TokenResponse*

応答のサンプル:

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

応答の `"access_token"` フィールド値は、すべての SaaS Fulfillment API と Marketplace 計測 API を呼び出すときに承認パラメーターとして渡す `<access_token>` です。

## <a name="next-steps"></a>次のステップ

Azure AD で保護されたアプリで、[SaaS Fulfillment API バージョン 2](./pc-saas-fulfillment-api-v2.md) を使用できるようになりました。