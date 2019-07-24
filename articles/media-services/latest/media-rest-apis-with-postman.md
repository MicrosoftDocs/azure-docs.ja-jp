---
title: Azure Media Services REST API 呼び出し用の Postman の構成
description: Media Services REST API 呼び出し用に Postman を構成する方法を説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2019
ms.author: juliako
ms.openlocfilehash: a2171ff8a4354a59ec2f790f9bf38b7a687419ca
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543878"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Media Services REST API 呼び出し用の Postman の構成

この記事では、Azure Media Services (AMS) REST API を呼び出すために使用できるように **Postman** を構成する方法を示します。 この記事では、環境およびコレクション ファイルを **Postman** にインポートする方法を示しています。 コレクションには、Azure Media Services (AMS) REST API を呼び出す HTTP 要求のグループ化された定義が含まれます。 環境ファイルには、コレクションによって使用される変数が含まれています。

開発を開始する前に、[Media Services v3 API を使用した開発](media-services-apis-overview.md)に関するページを確認してください。

## <a name="prerequisites"></a>前提条件

- [Media Services アカウントを作成する](create-account-cli-how-to.md) リソース グループ名と Media Services アカウント名を覚えておいてください。 
- [API へのアクセス](access-api-cli-how-to.md)に必要な情報を取得する
- [Postman](https://www.getpostman.com/) REST クライアントをインストールして、AMS REST チュートリアルの一部で示されている REST API を実行します。 

    ここでは **Postman** を使用しますが、任意の REST ツールを使用できます。 その他の選択肢は、REST プラグインを使用する **Visual Studio Code** や **Telerik Fiddler** です。 

## <a name="download-postman-files"></a>Postman ファイルをダウンロードする

Postman コレクションと環境ファイルを含む GitHub リポジトリを複製します。

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Postman を構成する

このセクションでは、Postman を構成します。

### <a name="configure-the-environment"></a>環境の構成 

1. **Postman** を開きます。
2. 画面の右側で、**[Manage environment]/(環境の管理/)** オプションを選択します。

    ![環境を管理する](./media/develop-with-postman/postman-import-env.png)
4. **[Manage environment]/(環境の管理/)** ダイアログで、**[インポート]** をクリックします。
2. `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` を複製したときにダウンロードされた `Azure Media Service v3 Environment.postman_environment.json` ファイルを参照します。
6. **[Azure Media Service v3 Environment]\(Azure Media Service v3 環境\)** 環境が追加されています。

    > [!Note]
    > 前述の **[Access the Media Services API]\(Media Services API にアクセスする\)** セクションから取得した値で、アクセス変数を更新します。

7. 選択されたファイルをダブルクリックして、API へのアクセスに関する手順に従って取得した値を入力します。
8. ダイアログを閉じます。
9. ドロップ ダウンから **[Azure Media Service v3 Environment]\(Azure Media Service v3 環境\)** 環境を選択します。

    ![環境を選択する](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>コレクションの構成

1. **[インポート]** をクリックしてコレクション ファイルをインポートします。
1. `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` を複製したときにダウンロードされた `Media Services v3.postman_collection.json` ファイルを参照します。
3. **Media Services v3.postman_collection.json** ファイルを選択します。

    ![ファイルをインポートする](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Azure AD トークンを取得する 

AMS v3 リソースの操作を開始する前に、サービス プリンシパル認証用の Azure AD トークンを取得して設定する必要があります。

1. Postman の左側のウィンドウで、[Step 1:Get AAD Auth token]\(手順 1: AAD 認証トークンを取得する\) を選択します。
2. 次に、[Get Azure AD Token for Service Principal Authentication]\(\サービス プリンシパル認証のために Azure AD トークンを取得する) を選択します。
3. **[送信]** をクリックします。

    次の **POST** 操作が送信されます。

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. 応答がトークンと共に返され、"AccessToken" 環境変数がトークン値に設定されます。  

    ![AAD トークンを取得する](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="see-also"></a>関連項目

- [Media Services アカウントにファイルをアップロードする - REST](upload-files-rest-how-to.md)
- [Media Services を使用してフィルターを作成する - REST](filters-dynamic-manifest-rest-howto.md)
- [Azure Resource Manager ベースの REST API](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>次の手順

- [REST を使用してファイルのストリーム配信を行う](stream-files-tutorial-with-rest.md)。  
- [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - REST](stream-files-tutorial-with-rest.md)
