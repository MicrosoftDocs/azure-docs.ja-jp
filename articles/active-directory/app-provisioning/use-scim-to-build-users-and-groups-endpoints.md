---
title: Azure Active Directory からアプリにユーザーをプロビジョニングするための SCIM エンドポイントを構築する
description: SCIM エンドポイントを開発し、SCIM API を Azure AD と統合し、Azure Active Directory を使用してクラウド アプリケーションへのユーザーとグループのプロビジョニングを自動的に行う方法を学習します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 4e933000c8e700d8bfd193b542e3855b2fca26f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101689336"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>チュートリアル:サンプルの SCIM エンドポイントを開発する

新しいエンドポイントを最初から構築しなくても済むよう、[クロスドメイン ID 管理システム (SCIM)](https://aka.ms/scimoverview) を使い始めるための[リファレンス コード](https://aka.ms/scimreferencecode)を用意しています。 コードを必要とすることなく、わずか 5 分で、SCIM エンドポイントを立ち上げて稼働させることができます。

このチュートリアルでは、SCIM リファレンス コードを Azure にデプロイし、Postman を使用するか Azure Active Directory (Azure AD) SCIM クライアントと統合してコードをテストする方法について説明します。 このチュートリアルは、SCIM の使用を開始したい開発者、または SCIM エンドポイントのテストに関心のある方を対象としています。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * SCIM エンドポイントを Azure にデプロイする。
> * SCIM エンドポイントをテストする。

## <a name="deploy-your-scim-endpoint-in-azure"></a>SCIM エンドポイントを Azure にデプロイする

ここで説明する手順では、[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) と [Azure App Service](../../app-service/index.yml) を使用して SCIM エンドポイントをサービスにデプロイします。 SCIM リファレンス コードは、ローカルで実行したり、オンプレミス サーバーでホストしたり、別の外部サービスにデプロイしたりもできます。

1. GitHub から [リファレンス コード](https://github.com/AzureAD/SCIMReferenceCode)にアクセスし、 **[クローンまたはダウンロード]** を選択します。

1. **[デスクトップで開く]** を選択します。または、リンクをコピーし、Visual Studio を開き、 **[Clone or check out code]\(コードをクローンまたはチェックアウトする\)** を選択し、コピーしたリンクを入力してローカル コピーを作成します。

1. Visual Studio では、必ず、ホスティング リソースにアクセスできるアカウントにサインインしてください。

1. ソリューション エクスプローラーで、*Microsoft.SCIM.sln* を開き、*Microsoft.SCIM.WebHostSample* ファイルを右クリックします。 **[発行]** を選択します。

    ![サンプル ファイルを示すスクリーンショット。](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > このソリューションをローカルで実行するには、プロジェクトをダブルクリックし、 **[IIS Express]** を選択し、ローカル ホストの URL を使用する Web ページとしてプロジェクトを起動します。

1. **[プロファイルの作成]** を選択し、 **[App Service]** と **[新規作成]** が選択されていることを確認します。

    ![[発行] ウィンドウを示すスクリーンショット。](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. ダイアログ オプションをステップ実行し、アプリの名前を任意の名前に変更します。 この名前は、アプリと SCIM エンドポイント URL の両方で使用されます。

    ![新しいアプリ サービスの作成を示すスクリーンショット。](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. 使用するリソース グループを選択し、 **[発行]** を選択します。

    ![新しいアプリ サービスの発行を示すスクリーンショット。](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-4.png)

1. **[Azure App Service]**  >  **[構成]** でアプリケーションに移動し、 **[新しいアプリケーション設定]** を選択して、*Token__TokenIssuer* 設定を `https://sts.windows.net/<tenant_id>/` の値で追加します。 `<tenant_id>` を Azure AD テナント ID に置き換えます。 [Postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)を使用して SCIM エンドポイントをテストする場合、*ASPNETCORE_ENVIRONMENT* 設定を値 `Development`で追加します。

   ![[アプリケーション設定] ウィンドウを示すスクリーンショット。](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   [Azure portal](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client) でエンタープライズ アプリケーションを使用してエンドポイントをテストする場合、2 つのオプションがあります。 環境を `Development` のままにして、トークンは `/scim/token` エンドポイントからテスト用に生成したものを指定するか、環境を `Production` に変更してトークン フィールドは空のままにすることができます。

これで完了です。 SCIM エンドポイントが発行されたので、Azure App Service の URL を使用して SCIM エンドポイントをテストできるようになりました。

## <a name="test-your-scim-endpoint"></a>SCIM エンドポイントをテストする

SCIM エンドポイントへの要求には認可が必要です。 SCIM 標準では、Cookie、基本認証、TLS クライアント認証、[RFC 7644](https://tools.ietf.org/html/rfc7644#section-2) で規定されている任意の方法など、認証と認可のための複数のオプションが用意されています。

ユーザー名とパスワードなどの安全でない方法は避けて、OAuth などのより安全な方法を優先してください。 Azure AD では、有効期間が長いベアラー トークン (ギャラリーおよび非ギャラリー アプリケーション用) と、OAuth 認可付与 (ギャラリー アプリケーション用) がサポートされています。

> [!NOTE]
> リポジトリで提供される認可方法はテスト専用です。 Azure AD と統合する場合は、認可のガイダンスを参照できます。 [SCIM エンドポイントのプロビジョニングの計画](use-scim-to-provision-users-and-groups.md)に関する記事を参照してください。

開発環境では、セキュリティ トークン検証の動作を制御するためのリファレンス コードなど、運用環境では安全でない機能が有効になります。 トークン検証コードでは自己署名セキュリティ トークンが使用され、署名キーは構成ファイルに保存されます。 *appsettings.Development.json* ファイルの **Token:IssuerSigningKey** パラメーターを参照してください。

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> `/scim/token` エンドポイントに **GET** 要求を送信すると、構成されたキーを使用してトークンが発行されます。 そのトークンを、後続の認可用のベアラー トークンとして使用できます。

既定のトークン検証コードは、Azure AD トークンを使用するように構成されており、発行元のテナントは、*appsettings.json* ファイルの **Token:TokenIssuer** パラメーターを使用して構成する必要があります。

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Postman を使用してエンドポイントをテストする

SCIM エンドポイントがデプロイされたら、SCIM RFC に確実に準拠していることをテストすることができます。 この例では、Postman でのテスト一式を提供し、ユーザーとグループに対する CRUD (作成、読み取り、更新、削除) 操作、フィルター処理、グループ メンバーシップの更新、ユーザーの無効化を検証します。

エンドポイントは `{host}/scim/` ディレクトリに配置され、標準の HTTP 要求を使用して、それらとやりとりすることができます。 `/scim/` ルートを変更するには、**AzureADProvisioningSCIMreference** > **ScimReferenceApi** > **Controllers** 内の *ControllerConstant.cs* を参照してください。

> [!NOTE]
> HTTP エンドポイントはローカル テストにのみ使用できます。 Azure AD プロビジョニング サービスを利用するには、エンドポイントで HTTPS がサポートされている必要があります。

1. [Postman](https://www.getpostman.com/downloads/) をダウンロードしてアプリケーションを起動します。
1. リンク `https://aka.ms/ProvisioningPostman`をコピーし、Postman に貼り付けて、テスト コレクションをインポートします。

    ![テスト コレクションを Postman にインポートする方法を示すスクリーンショット。](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. 次の変数を使用するテスト環境を作成します。

   |環境|変数|値|
   |-|-|-|
   |IIS Express を使用してローカルでプロジェクトを実行する|||
   ||**サーバー**|`localhost`|
   ||**[ポート]**|`:44359` " *( **`:`** を忘れないでください)* "|
   ||**Api**|`scim`|
   |Kestrel を使用してローカルでプロジェクトを実行する|||
   ||**サーバー**|`localhost`|
   ||**[ポート]**|`:5001` " *( **`:`** を忘れないでください)* "|
   ||**Api**|`scim`|
   |Azure でエンドポイントをホストする|||
   ||**サーバー**|*(SCIM URL を入力)*|
   ||**[ポート]**|*(空白のまま)*|
   ||**Api**|`scim`|

1. Postman コレクションから **Get Key** を使用して、**GET** 要求をトークン エンドポイントに送信し、後続の要求のために **token** 変数に保存されるセキュリティ トークンを取得します。

   ![Postman Get Key フォルダーを示すスクリーンショット。](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > SCIM エンドポイントを安全にするには、接続する前にセキュリティ トークンが必要です。 チュートリアルでは、`{host}/scim/token` エンドポイントを使用して自己署名トークンを生成します。

これで完了です。 **Postman** コレクションを実行して SCIM エンドポイント機能をテストできるようになりました。

## <a name="next-steps"></a>次のステップ

クライアント向けの相互運用性を備えた SCIM 準拠のユーザーおよびグループ エンドポイントを開発するには、[SCIM クライアントの実装](http://www.simplecloud.info/#Implementations2)に関するページを参照してください。

> [!div class="nextstepaction"]
> [チュートリアル: SCIM エンドポイントのプロビジョニングを開発および計画する](use-scim-to-provision-users-and-groups.md)
> [チュートリアル: ギャラリー アプリのプロビジョニングを構成する](configure-automatic-user-provisioning-portal.md)