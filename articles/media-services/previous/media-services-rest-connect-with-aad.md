---
title: REST で Azure AD 認証を使用して Azure Media Services API にアクセスする | Microsoft Docs
description: REST で Azure Active Directory 認証を使用して Azure Media Services API にアクセスする方法を説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: 66c69552157df957e572a3af092131a3b7e560d5
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871690"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Azure AD 認証を使用して REST で Media Services API にアクセスする

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-from-v2-to-v3.md)を参照してください。

Azure Media Services で Azure AD Authentication を使用する場合は、次の 2 つの方法のいずれかで認証できます。

- **ユーザー認証**: Azure Media Services リソースを操作するアプリを使用しているユーザーが認証を受けます。 ユーザーは最初にその操作アプリケーションから資格情報の入力を求められます。 たとえば、承認済みユーザーがエンコード ジョブまたはライブ ストリーミングを監視するために使用する管理コンソール アプリなどです。 
- **サービス プリンシパルの認証**: サービスを認証します。 この認証方法がよく使用されるアプリケーションは、デーモン サービス、中間層サービス、またはスケジュールされたジョブを実行するアプリ (例: Web アプリ、関数アプリ、ロジック アプリ、API、マイクロサービス) です。

    このチュートリアルでは、Azure AD サービス **プリンシパル認証**を使用して、REST で AMS API にアクセスする方法を説明します。 

    > [!NOTE]
    > Azure Media Services に接続するほとんどのアプリケーションでは、**サービス プリンシパル**が推奨されるベスト プラクティスです。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Portal から認証情報を取得する
> * Postman を使用してアクセス トークンを取得する
> * アクセス トークンを使用して **Assets** API をテストする


> [!IMPORTANT]
> 現在 Media Services では、Azure Access Control Service 認証モデルがサポートされています。 ただし、Access Control 認証は 2018 年 6 月 1 日に非推奨となる予定です。 できるだけ早く Azure AD 認証モデルに移行することをお勧めします。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。
- [Azure Portal を使用して Azure Media Services アカウントを作成](media-services-portal-create-account.md)します。
- [Azure AD 認証による Azure Media Services API へのアクセスの概要](media-services-use-aad-auth-to-access-ams-api.md)に関する記事を確認してください。
- [Postman](https://www.getpostman.com/) REST クライアントをインストールして、この記事に記載されている REST API を実行します。 

    このチュートリアルでは **Postman** を使用しますが、任意の REST ツールを使用できます。 その他の選択肢は、REST プラグインを使用する **Visual Studio Code** や **Telerik Fiddler** です。 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Azure Portal から認証情報を取得する

### <a name="overview"></a>概要

Media Services API にアクセスするには、以下のデータ ポイントを収集する必要があります。

|Setting|例|説明|
|---|-------|-----|
|Azure Active Directory テナント ドメイン|microsoft.onmicrosoft.com|セキュリティ トークン サービス (STS) エンドポイントとしての Azure AD は、<https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token> という形式で作成されます。 Azure AD は、リソース (アクセス トークン) にアクセスするために JWT を発行します。|
|REST API エンドポイント|<https://amshelloworld.restv2.westus.media.azure.net/api/>|これは、アプリケーションのすべての Media Services REST API 呼び出しの呼び出し先エンドポイントです。|
|クライアント ID (アプリケーション ID)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Azure AD アプリケーション (クライアント) ID。 アクセス トークンを取得するには、クライアント ID が必要です。 |
|クライアント シークレット|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Azure AD アプリケーション キー (クライアント シークレット)。 アクセス トークンを取得するには、クライアント シークレットが必要です。|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Azure Portal から AAD 認証情報を取得する

情報を取得するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) にログインします。
2. AMS インスタンスに移動します。
3. **[API アクセス]** を選択します。
4. **[サービス プリンシパルを使って Azure Media Services API に接続する]** をクリックします。

    ![API アクセス](./media/connect-with-rest/connect-with-rest01.png)

5. 既存の **Azure AD アプリケーション**を選択するか、新しいアプリケーションを作成します (後述します)。

    > [!NOTE]
    > Azure Media REST 要求を成功させるには、呼び出すユーザーに、アクセスしたい Media Services アカウントの**共同作成者**ロールまたは**所有者**ロールが付与されている必要があります。 "リモート サーバーがエラーを返しました: (401) 未承認" という例外を受け取る場合は、「[アクセス制御](media-services-use-aad-auth-to-access-ams-api.md#access-control)」を確認してください。

    新しい AD アプリケーションを作成する場合は、次の手順を実行します。
    
   1. **[新規作成]** をクリックします。
   2. 名前を入力します。
   3. **[新規作成]** をもう一度クリックします。
   4. **[保存]** をクリックします。

      ![API アクセス](./media/connect-with-rest/new-app.png)

      ページに新しいアプリケーションが表示されます。

6. **クライアント ID** (アプリケーション ID) を取得します。
    
   1. アプリケーションを選択します。
   2. 右側のウィンドウから**クライアント ID** を取得します。 

      ![API アクセス](./media/connect-with-rest/existing-client-id.png)

7. アプリケーションの**キー** (クライアント シークレット) を取得します。 

   1. **[アプリケーションの管理]** ボタンをクリックします (クライアント ID の情報は **[アプリケーション ID]** の下に表示されます)。 
   2. **[キー]** をクリックします。
    
       ![API アクセス](./media/connect-with-rest/manage-app.png)
   3. **[説明]** と **[有効期限]** に入力し、 **[保存]** をクリックしてアプリケーション キー (クライアント シークレット) を生成します。
    
       **[保存]** ボタンをクリックすると、キー値が表示されます。 ブレードから離れる前に、キー値をコピーします。

   ![API アクセス](./media/connect-with-rest/connect-with-rest03.png)

AD 接続パラメーターの値を web.config ファイルまたは app.config ファイルに追加して、後でコードに使用することができます。

> [!IMPORTANT]
> **クライアント キー**は重要なシークレットであり、キー コンテナーで適切に保護するか、実稼働環境で暗号化する必要があります。

## <a name="get-the-access-token-using-postman"></a>Postman を使用してアクセス トークンを取得する

このセクションでは、**Postman** を使用して JWT ベアラー トークン (アクセス トークン) を返す REST API を実行する方法を示します。 Media Services REST API を呼び出すには、呼び出しに "Authorization" ヘッダーを追加し、各呼び出しに "Bearer *your_access_token*" という値を追加する必要があります (このチュートリアルの次のセクションを参照してください)。 

1. **Postman** を開きます。
2. **[POST]** を選択します。
3. 次のようにテナント名の末尾に **.onmicrosoft.com** を指定し、URL の末尾に **oauth2/token** を指定して、テナント名を含む URL を入力します。 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. **[Headers]\(ヘッダー\)** タブを選択します。
5. "Key/Value"(キー/値) データ グリッドを使用して **[Headers]\(ヘッダー\)** 情報を入力します。 

    ![データ グリッド](./media/connect-with-rest/headers-data-grid.png)

    または、Postman ウィンドウの右側にある **[Bulk Edit]\(一括編集\)** リンクをクリックし、次のコードを貼り付けます。

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. **[Body]\(本文\)** タブをクリックします。
7. "Key/Value"(キー/値) データ グリッドを使用して本文情報を入力します (クライアント ID とシークレット値を置き換えます)。 

    ![データ グリッド](./media/connect-with-rest/data-grid.png)

    または、Postman ウィンドウの右側にある **[Bulk Edit]\(一括編集\)** リンクをクリックし、次の本文を貼り付けます (クライアント ID とシークレット値を置き換えます)。

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your Azure AD Application}
        client_secret:{Your client secret that you got from your Azure AD Application's Keys}
        resource:https://rest.media.azure.net

8. **[送信]** をクリックします。

    ![トークンを取得する](./media/connect-with-rest/connect-with-rest04.png)

返される応答には、すべての AMS API にアクセスするために必要な**アクセス トークン**が含まれています。

## <a name="test-the-assets-api-using-the-access-token"></a>アクセス トークンを使用して **Assets** API をテストする

このセクションでは、**Postman** を使用して **Assets** API にアクセスする方法について説明します。

1. **Postman** を開きます。
2. **[GET]** を選択します。
3. REST API エンドポイントを貼り付けます (例: https://amshelloworld.restv2.westus.media.azure.net/api/Assets) 。
4. **[Authorization]\(承認\)** タブを選択します。 
5. **[Bearer Token]\(ベアラー トークン\)** を選択します。
6. 前のセクションで作成したトークンを貼り付けます。

    ![トークンを取得する](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Postman UX は Mac と PC で異なる可能性があります。 Mac バージョンの **[Authentication]\(認証\)** セクション ドロップダウンに [Bearer Token]\(ベアラー トークン\) オプションがない場合は、Mac クライアントで **Authorization** ヘッダーを手動で追加する必要があります。

   ![Auth ヘッダー](./media/connect-with-rest/auth-header.png)

7. **[Headers]\(ヘッダー\)** を選択します。
5. Postman ウィンドウの右側にある **[Bulk Edit]\(一括編集\)** リンクをクリックします。
6. 次のヘッダーを貼り付けます。

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. **[送信]** をクリックします。

返される応答には、アカウント内の資産が含まれています。

## <a name="next-steps"></a>次の手順

* [Azure AD Authentication for Azure Media Services Access: Both via REST API (Azure Media Services にアクセスするための Azure AD Authentication: 両方で REST API を使用)](https://github.com/willzhan/WAMSRESTSoln) のサンプル コードを試してください。
* [.NET を使用したファイルのアップロード](media-services-dotnet-upload-files.md)
