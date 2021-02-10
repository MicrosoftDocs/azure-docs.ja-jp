---
title: Azure Active Directory からアプリにユーザーをプロビジョニングするための SCIM エンドポイントを構築する
description: クロスドメイン ID 管理システム (SCIM) では、自動ユーザー プロビジョニングが標準化されます。 SCIM エンドポイントを開発し、SCIM API を Azure Active Directory と統合し、Azure Active Directory を使用してクラウド アプリケーションへのユーザーとグループのプロビジョニングの自動化を開始する方法について学習します。
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
ms.openlocfilehash: 6b7451b0d664995a6b647f7926d856b0db6090d8
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256104"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>チュートリアル:サンプルの SCIM エンドポイントを開発する

新しいエンドポイントを最初から構築しなくても済むよう、[SCIM](https://aka.ms/scimoverview) を使い始めるための[リファレンス コード](https://aka.ms/scimreferencecode)を用意しています。 このチュートリアルでは、SCIM リファレンス コードを Azure にデプロイし、Postman を使用するか Azure AD SCIM クライアントと統合してコードをテストする方法について説明します。 コードなしで、わずか 5 分で、SCIM エンドポイントを立ち上げて稼働させることができます。 このチュートリアルは、SCIM の使用を検討している開発者、または SICM エンドポイントのテストに関心があるその他の人々を対象としています。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * SCIM エンドポイントを Azure にデプロイする
> * SCIM エンドポイントをテストする

## <a name="deploy-your-scim-endpoint-in-azure"></a>SCIM エンドポイントを Azure にデプロイする

ここで説明する手順では、[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) と [Azure App Services](https://docs.microsoft.com/azure/app-service/) を使用して SCIM エンドポイントをサービスにデプロイします。 SCIM リファレンス コードは、ローカルで実行したり、オンプレミス サーバーでホストしたり、別の外部サービスにデプロイしたりもできます。 

### <a name="open-solution-and-deploy-to-azure-app-service"></a>ソリューションを開いて Azure App Service にデプロイする

1. GitHub から [リファレンス コード](https://github.com/AzureAD/SCIMReferenceCode)にアクセスし、 **[クローンまたはダウンロード]** を選択します。

1. **[デスクトップで開く]** を選択します。または、リンクをコピーし、**Visual Studio** を開き、 **[Clone or check out code]\(コードをクローンまたはチェックアウトする\)** を選択し、コピーしたリンクを入力してローカル コピーを作成します。

1. **Visual Studio** では、必ず、ホスティング リソースにアクセスできるアカウントにサインインしてください。

1. **ソリューション エクスプローラー** で、*Microsoft.SCIM.sln* を開き、*Microsoft.SCIM.WebHostSample* ファイルを右クリックします。 **[発行]** を選択します。

    ![クラウド発行](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > このソリューションをローカルで実行するには、プロジェクトをダブルクリックし、 **[IIS Express]** を選択し、ローカル ホストの URL を使用する Web ページとしてプロジェクトを起動します。

1. **[プロファイルの作成]** を選択し、 **[App Service]** と **[新規作成]** が選択されていることを確認します。

    ![クラウド発行 2](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. ダイアログ オプションをステップ実行し、アプリの名前を任意の名前に変更します。 この名前は、アプリと SCIM エンドポイント URL の両方で使用されます。

    ![クラウド発行 3](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. 使用するリソース グループを選択して **[発行]** を選択します。

1. **[Azure App Services]**  >  **[構成]** でアプリケーションに移動し、 **[新しいアプリケーション設定]** を選択して、*Token__TokenIssuer* 設定を `https://sts.windows.net/<tenant_id>/` の値で追加します。 `<tenant_id>` を Azure AD の tenant_id に置き換えます。[Postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) を使用して SCIM エンドポイントをテストする場合は、さらに *ASPNETCORE_ENVIRONMENT* 設定を `Development` の値で追加します。 

   ![appservice の設定](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Azure portal のエンタープライズ アプリケーションを使用してエンドポイントをテストする場合は、[Azure portal](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client) のエンタープライズ アプリケーションで、環境を `Development` のままにしてトークンは `/scim/token` エンドポイントからテスト用に生成したものを指定するか、環境を `Production` に変更してトークン フィールドは空のままにするかを選択します。 

これで完了です。 SCIM エンドポイントが発行され、Azure App Service の URL を使用して SCIM エンドポイントをテストできるようになりました。

## <a name="test-your-scim-endpoint"></a>SCIM エンドポイントをテストする

SCIM エンドポイントへの要求には認可が必要であり、SCIM 標準では、Cookie、基本認証、TLS クライアント認証、[RFC 7644](https://tools.ietf.org/html/rfc7644#section-2) で規定されている任意の方法など、認証と認可のための複数のオプションが用意されています。

ユーザー名とパスワードなどの安全でない方法は避けて、OAuth などのより安全な方法を優先してください。 Azure AD では、有効期間が長いベアラー トークン (ギャラリーおよび非ギャラリー アプリケーション用) と、OAuth 認可付与 (アプリ ギャラリーで発行されたアプリケーション用) がサポートされています。

> [!NOTE]
> リポジトリで提供される認可方法はテスト専用です。 Azure AD と統合する場合は、[SCIM エンドポイント用のプロビジョニングの計画](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#authorization-for-provisioning-connectors-in-the-application-gallery)に関するページで認可のガイダンスを確認してください。 

開発環境では、セキュリティ トークン検証の動作を制御するためのリファレンス コードなど、運用環境では安全でない機能が有効になります。 トークン検証コードは自己署名セキュリティ トークンを使用するように構成され、署名キーは構成ファイルに保存されます。*appsettings.Development.json* ファイルの **Token:IssuerSigningKey** パラメーターを参照してください。

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> `/scim/token` エンドポイントに **GET** 要求を送信すると、構成されたキーを使用してトークンが発行され、後続の認可用のベアラー トークンとして使用できます。

既定のトークン検証コードは、Azure Active Directory によって発行されたトークンを使用するように構成されており、発行元のテナントは *appsettings.json* ファイルの **Token:TokenIssuer** パラメーターを使用して構成する必要があります。

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Postman を使用してエンドポイントをテストする

SCIM エンドポイントがデプロイされたら、テストを実行して、SCIM RFC に準拠していることを確認できます。 この例では、**Postman** でのテスト一式を提供し、ユーザーとグループに対する CRUD 操作、フィルター処理、グループ メンバーシップの更新、ユーザーの無効化を検証します。

エンドポイントは `{host}/scim/` ディレクトリに配置され、標準の HTTP 要求を使用して対話できます。 `/scim/` ルートを変更するには、**AzureADProvisioningSCIMreference** > **ScimReferenceApi** > **Controllers** 内の *ControllerConstant.cs* を参照してください。

> [!NOTE]
> エンドポイントで HTTPS がサポートされていることが Azure AD プロビジョニング サービスの要件であるため、HTTP エンドポイントはローカル テストにしか使用できません。

#### <a name="open-postman-and-run-tests"></a>Postman を開いてテストを実行する

1. [Postman](https://www.getpostman.com/downloads/) をダウンロードしてアプリケーションを起動します。
1. リンク [https://aka.ms/ProvisioningPostman](https://aka.ms/ProvisioningPostman) をコピーし、Postman に貼り付けて、テスト コレクションをインポートします。

    ![Postman コレクション](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. 以下の変数を使用してテスト環境を作成します。

   |環境|変数|値|
   |-|-|-|
   |IIS Express を使用してローカルでプロジェクトを実行する|||
   ||**サーバー**|`localhost`|
   ||**[ポート]**|`:44359` *( **:** を忘れないこと)*|
   ||**Api**|`scim`|
   |Kestrel を使用してローカルでプロジェクトを実行する|||
   ||**サーバー**|`localhost`|
   ||**[ポート]**|`:5001` *( **:** を忘れないこと)*|
   ||**Api**|`scim`|
   |Azure でエンドポイントをホストする|||
   ||**サーバー**|*(SCIM URL を入力)*|
   ||**[ポート]**|*(空白のまま)*|
   ||**Api**|`scim`|

1. Postman コレクションから **Get Key** を使用して、**GET** 要求をトークン エンドポイントに送信し、後続の要求のために **token** 変数に保存されるセキュリティ トークンを取得します。 

   ![Postman Get Key](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > SCIM エンドポイントを安全にするには、接続する前にセキュリティ トークンが必要です。チュートリアルでは、`{host}/scim/token` エンドポイントを使用して自己署名トークンを生成します。

これで完了です。 **Postman** コレクションを実行して SCIM エンドポイント機能をテストできるようになりました。

## <a name="next-steps"></a>次の手順

クライアント向けの相互運用性を備えた SCIM 準拠のユーザーおよびグループ エンドポイントを開発するには、[SCIM クライアントの実装](http://www.simplecloud.info/#Implementations2)に関するページを参照してください。

> [!div class="nextstepaction"]
> [チュートリアル: SCIM エンドポイントのプロビジョニングを開発および計画する](use-scim-to-provision-users-and-groups.md)
> [チュートリアル: ギャラリー アプリのプロビジョニングを構成する](configure-automatic-user-provisioning-portal.md)