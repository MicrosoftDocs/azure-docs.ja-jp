---
title: 入力制約付きデバイスを Azure AD と Azure Maps REST API で保護する方法
titleSuffix: Azure Maps
description: Azure AD へのサインインをサポートし、Azure Maps REST API を呼び出すブラウザーレス アプリケーションを構成する方法。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 3833cbfd0802f334e482203d269984eb0e299797
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895632"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>入力制約付きデバイスを Azure AD と Azure Maps REST API で保護する

このガイドでは、シークレットを安全に格納できない、またはブラウザー入力を受け付けないパブリック アプリケーションまたはデバイスをセキュリティで保護する方法について説明します。 このような種類のアプリケーションは、IoT (モノのインターネット) のカテゴリに分類されます。 このようなアプリケーションの例としては、スマート TV デバイスや、センサー データを生成するアプリケーションなどがあります。 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Azure AD でアプリケーションの登録を作成する

> [!NOTE]
> * **前提条件:** 「[シナリオ:Web API を呼び出すデスクトップ アプリ](../active-directory/develop/scenario-desktop-overview.md)」を読んでおくこと
> * 次のシナリオでは、トークンを取得するために Web ブラウザーを使用しないデバイス コード フローを使用します。

Azure AD でデバイス ベースのアプリケーションを作成して、Azure AD サインインを有効にします。 このアプリケーションには、Azure Maps REST API へのアクセスが許可されます。

1. Azure portal の Azure サービスの一覧で、 **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新規登録]** の順に選択します。  

    > [!div class="mx-imgBorder"]
    > ![アプリの登録](./media/how-to-manage-authentication/app-registration.png)

2. **[名前]** を入力し、 **[サポートされているアカウントの種類]** として **[この組織のディレクトリ内のアカウントのみ]** を選択します。 **[リダイレクト URI]** で、 **[パブリック クライアント/ネイティブ (モバイルとデスクトップ)]** を指定し、値に `https://login.microsoftonline.com/common/oauth2/nativeclient` を追加します。 詳細については、Azure AD の「[Web API を呼び出すデスクトップ アプリ: アプリの登録](../active-directory/develop/scenario-desktop-app-registration.md)」の手順に従う必要があります。 次に、アプリケーションを **[登録]** します。

    > [!div class="mx-imgBorder"]
    > ![アプリケーションの登録の詳細 (名前とリダイレクト URI) を追加する](./media/azure-maps-authentication/devicecode-app-registration.png)

3. **[認証]** に移動して、 **[アプリケーションは、パブリック クライアントとして扱います]** を有効にします。 これにより、Azure AD でのデバイス コード認証が可能になります。
    
    > [!div class="mx-imgBorder"]
    > ![アプリケーションの登録をパブリック クライアントとして有効にする](./media/azure-maps-authentication/devicecode-public-client.png)

4.  委任された API アクセス許可を Azure Maps に割り当てるには、アプリケーションに移動します。 **[API のアクセス許可]**  >  **[アクセス許可の追加]** の順に選択します。 **[所属する組織で使用している API]** で、「**Azure Maps**」を検索して選択します。

    > [!div class="mx-imgBorder"]
    > ![アプリの API アクセス許可を追加する](./media/how-to-manage-authentication/app-permissions.png)

5. **[Access Azure Maps] (Azure Maps へのアクセス)** の横にあるチェック ボックスをオンにしてから、 **[アクセス許可の追加]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![アプリの API アクセス許可を選択する](./media/how-to-manage-authentication/select-app-permissions.png)

6. ユーザーまたはグループの Azure ロールベースのアクセス制御 (Azure RBAC) を構成します。 「[Azure Maps に対するロールベースのアクセスをユーザーに許可する](#grant-role-based-access-for-users-to-azure-maps)」を参照してください。

7. アプリケーションでトークン フローを取得するためのコードを追加します。実装の詳細については、「[デバイス コード フロー](../active-directory/develop/scenario-desktop-acquire-token.md#device-code-flow)」を参照してください。 トークンを取得する場合、前の手順で選択したスコープ `user_impersonation` を参照します。

> [!Tip]
> アクセス トークンを取得するには、Microsoft Authentication Library (MSAL) を使用します。 推奨事項については、「[Web API を呼び出すデスクトップ アプリ:コード構成](../active-directory/develop/scenario-desktop-app-configuration.md)

8. Azure AD から取得したトークンを使用して HTTP 要求を作成し、有効な HTTP クライアントを使用して要求を送信します。

### <a name="sample-request"></a>要求のサンプル
中心点と半径を使用して円ジオメトリとして表される単純なジオフェンスをアップロードするための要求本文のサンプルを次に示します。

```http
POST /mapData/upload?api-version=1.0&dataFormat=geojson
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

 次の要求本文のサンプルは、GeoJSON 内にあります。
```json
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [-122.126986, 47.639754]
        },
        "properties": {
            "geometryId": "001",
            "radius": 500
        }
    }]
}
```

### <a name="sample-response"></a>応答のサンプル:

Headers:
```http
Location: https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0
Access-Control-Expose-Headers: Location
```

本文は次のようになります。
```json
{
  "operationId": "{operationId}",
  "status": "Succeeded",
  "created": "2020-01-02 1:02:03 AM +00:00",
  "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{resourceId}?api-version=1.0"
}
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>次のステップ

Azure Maps アカウントにおける API 使用状況メトリックを確認します。
> [!div class="nextstepaction"]
> [使用状況メトリックを表示する](how-to-view-api-usage.md)