---
title: REST を使用した Azure Media Services アカウントへのファイルのアップロード | Microsoft Docs
description: REST を使用してアセットを作成し、アップロードすることによって、Media Services にメディア コンテンツを取り込む方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 9f27a427df07302840ce719d35c7876f9dc17dbf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012940"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>REST を使用して Media Services アカウントにファイルをアップロードする

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [ポータル](media-services-portal-upload-files.md)
> 

Media Services で、デジタル ファイルを資産にアップロードします。 [Asset](/rest/api/media/operations/asset) エンティティには、ビデオ、オーディオ、画像、サムネイル コレクション、テキスト トラック、クローズド キャプション ファイル (各ファイルのメタデータを含む) を追加できます。ファイルを資産にアップロードすると、コンテンツがクラウドに安全に保存され、処理したりストリーミングしたりできるようになります。 

このチュートリアルでは、ファイルのアップロードと関連するその他の操作を行う方法を学習します。

> [!div class="checklist"]
> * すべてのアップロード操作向けに Postman を設定する
> * Media Services への接続 
> * 書き込みのアクセス許可を持つアクセス ポリシーを作成する
> * アセットを作成する
> * SAS ロケーターを作成してアップロード URL を作成する
> * アップロード URL を使用して BLOB ストレージにファイルをアップロードする
> * アップロードしたメディア ファイル用の資産にメタデータを作成する

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。
- [Azure Portal を使用して Azure Media Services アカウントを作成](media-services-portal-create-account.md)します。
- [AAD 認証による Azure Media Services API へのアクセスの概要](media-services-use-aad-auth-to-access-ams-api.md)に関する記事を確認してください。
- また、詳細については、「[Azure AD 認証を使用して REST で Media Services API にアクセスする](./media-services-rest-connect-with-aad.md)」の記事を確認してください。
- 「[Configure Postman for Media Services REST API calls](media-rest-apis-with-postman.md)」 (Media Services REST API 呼び出しの Postman の構成) に説明されているように、**Postman** を構成してください。

## <a name="considerations"></a>考慮事項

Media Services REST API を使用する場合は、次の点を考慮してください。
 
* Media Services REST API を使用してエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。 詳細については、「 [Media Services REST API の概要](media-services-rest-how-to-use.md)」をご覧ください。 <br/>このチュートリアルで使用される Postman コレクションでは、必要なすべてのヘッダーの設定に対応しています。
* Media Services は、ストリーミング コンテンツ (たとえば、 http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters) の URL を構築する際に、IAssetFile.Name プロパティの値を使用します。このため、パーセントエンコーディングは利用できません。 **Name** プロパティの値には、[パーセント エンコーディング予約文字](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) (!*'();:@&=+$,/?%#[]") は使用できません。 また、ファイル名拡張子で使用できる "." は 1 つのみです。
* 名前は 260 文字以内で指定する必要があります。
* Media Services での処理についてサポートされている最大ファイル サイズには制限があります。 ファイル サイズの制限の詳細については、[こちら](media-services-quotas-and-limitations.md)の記事を参照してください。

## <a name="set-up-postman"></a>Postman の設定

このチュートリアル用に Postman を設定する方法の手順については、[Postman の構成 ](media-rest-apis-with-postman.md)に関する記事をご覧ください。

## <a name="connect-to-media-services"></a>Media Services への接続

1. 接続の値を環境に追加します。 

    [コレクション](postman-collection.md)に定義された操作の実行を開始する前に、**MediaServices** [環境](postman-environment.md)に属するいくつかの値は、手動で設定する必要があります。

    最初の 5 つの変数の値を取得するには、「[Azure AD Authentication を使用した Azure Media Services API へのアクセス](media-services-use-aad-auth-to-access-ams-api.md)」をご覧ください。 

    ![右上から選択された "歯車" アイコンと、[Management Environments]\(管理環境\) タブで選択された最初の 5 つの変数を示すスクリーンショット。](./media/media-services-rest-upload-files/postman-import-env.png)
2. **MediaFileName** 環境変数の値を指定します。

    アップロードを計画しているメディアのファイル名を指定します。 この例では、BigBuckBunny.mp4 をアップロードします。 
3. **AzureMediaServices.postman_environment.json** ファイルを確認します。 コレクション内のほとんどすべての操作で、"test" スクリプトが実行されていることがわかります。 スクリプトでは、応答によって返されたいくつかの値を利用して、適切な環境変数を設定します。

    たとえば、最初の操作ではアクセス トークンを取得し、他のすべての操作で使用される **AccessToken** 環境変数に設定します。

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. **[Postman]** ウィンドウの左にある **[1 Get AAD Auth token]\(1 AAD Auth トークンの取得\)**  ->  **[Get Azure AD Token for Service Principal]\(サービス プリンシパルの Azure AD トークンの取得\)** をクリックします。

    URL 部分には、**AzureADSTSEndpoint** 環境変数が入力されます (このチュートリアルの初めの方で、コレクションをサポートする環境変数の値を設定しています)。

    ![スクリーンショットで示されているのは、1. Get AAD Auth token - Get Azure AD Token for Service Principal\(1. AAD 認証トークンの取得 - サービス プリンシパルの Azure AD トークンの取得\) が Postman ウィンドウで選択され、Send\(送信\) ボタンが選択されていることです。](./media/media-services-rest-upload-files/postment-get-token.png)

5. **[送信]** をクリックします。

    "Access_token" が含まれている応答を確認できます。 "test" スクリプトはこの値を利用して、(前述した) **AccessToken** 環境変数に設定します。 お使いの環境変数を確認すると、現時点でこの変数には、残りの操作で使用されるアクセス トークン (ベアラー トークン) 値が格納されていることがわかります。 

    トークンの有効期限が切れた場合は、もう一度 [Get Azure AD Token for Service Principal]\(サービス プリンシパルの Azure AD トークンの取得\) の手順を行います。 

## <a name="create-an-access-policy-with-write-permission"></a>書き込みのアクセス許可を持つアクセス ポリシーを作成する

### <a name="overview"></a>概要 

>[!NOTE]
>さまざまな AMS ポリシー (ロケーター ポリシーや ContentKeyAuthorizationPolicy など) に 1,000,000 ポリシーの制限があります。 常に同じ日数、アクセス許可などを使う場合は、同じポリシー ID を使う必要があります (たとえば、長期間存在するように意図されたロケーターのポリシー (非アップロード ポリシー))。 詳細については、[こちらの記事](media-services-dotnet-manage-entities.md#limit-access-policies)を参照してください。

すべてのファイルを blob ストレージにアップロードする前に、資産に書き込むためのアクセス ポリシーの権限を設定します。 そのためには、AccessPolicies エンティティ セットへの HTTP 要求を投稿します。 作成時に DurationInMinutes 値を定義します。定義していないと、500 Internal Server エラー メッセージが返されます。 AccessPolicies について詳しくは、[AccessPolicy](/rest/api/media/operations/accesspolicy) をご覧ください。

### <a name="create-an-access-policy"></a>アクセス ポリシーを作成します。

1. **[AccessPolicy]**  ->  **[Create AccessPolicy for Upload]\(アップロード用の AccessPolicy の作成\)** の順に選択します。
2. **[送信]** をクリックします。

    ![左側のメニューで選択された [AccessPolicy - Create AccessPolicy for Upload]\(AccessPolicy - アップロード用の AccessPolicy の作成\) と、選択された [Send]\(送信\) ボタンを示すスクリーンショット。](./media/media-services-rest-upload-files/postman-access-policy.png)

    "test" スクリプトは AccessPolicy Id を取得し、適切な環境変数を設定します。

## <a name="create-an-asset"></a>アセットを作成する

### <a name="overview"></a>概要

[資産](/rest/api/media/operations/asset)は、ビデオ、オーディオ、イメージ、サムネイル コレクション、テキスト トラック、クローズド キャプション ファイルなど、Media Services 内の多様な種類やセットのオブジェクトのためのコンテナーです。 REST API で資産を作成するには、Media Services に POST 要求を送信し、要求本文に、資産に関するプロパティ情報を配置する必要があります。

資産を作成するときに追加できるプロパティの 1 つは **Options** です。 次の暗号化オプションのいずれかを指定できます。 **[None]** (既定、暗号化は使用されない)、 **[StorageEncrypted]** (クライアント側のストレージ暗号化を使って事前に暗号化されたコンテンツに対応)、 **[CommonEncryptionProtected]** 、または **[EnvelopeEncryptionProtected]** 。 暗号化された資産がある場合は、配信ポリシーを構成する必要があります。 詳細については、「[資産配信ポリシーの構成](media-services-rest-configure-asset-delivery-policy.md)」をご覧ください。

資産が暗号化されたら、**ContentKey** を作成し、次の記事の説明に従ってその資産にリンクする必要があります:[ContentKey の作成方法](media-services-rest-create-contentkey.md)。 ファイルを資産にアップロードした後、**AssetFile** エンティティの暗号化プロパティを **Asset** 暗号化中に取得した値に更新する必要があります。 **MERGE** HTTP 要求を使用して実行します。 

この例では、暗号化されていない資産を作成しています。 

### <a name="create-an-asset"></a>アセットを作成する

1. **[資産]**  ->  **[資産の作成]** の順に選択します。
2. **[送信]** をクリックします。

    ![[Collections]\(コレクション\) メニューから選択された [Assets - Create Asset]\(アセット - アセットの作成\) と、選択された [Send]\(送信\) ボタンを示すスクリーンショット。](./media/media-services-rest-upload-files/postman-create-asset.png)

    "test" スクリプトは Asset Id を取得し、適切な環境変数を設定します。

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>SAS ロケーターを作成してアップロード URL を作成する

### <a name="overview"></a>概要

AccessPolicy と Locator を設定すると、実際のファイルは、Azure Storage REST API を使用して Azure Blob ストレージ コンテナーにアップロードされます。 ファイルはブロック BLOB としてをアップロードする必要があります。 ページ BLOB は Azure Media Services ではサポートされていません。  

Azure ストレージ BLOB の使用の詳細については、 [BLOB サービス REST API](/rest/api/storageservices/blob-service-rest-api)をご覧ください。

実際のアップロード URL を受信するには、SAS ロケーターを作成します (以降を参照)。 Locator は、資産内のファイルにアクセスするクライアントの開始時刻と接続エンドポイントの種類を定義します。 特定の AccessPolicy と Asset ペアに対して複数の　Locator　エンティティを作成して、別のクライアントの要求およびニーズを処理できます。 これらの各 Locator は、AccessPolicy の StartTime 値と DurationInMinutes 値を使用して、URL を使用できる時間の長さを決定します。 詳細については、「 [Locator](/rest/api/media/operations/locator)」をご覧ください。

SAS URL には次の形式があります。

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

### <a name="considerations"></a>考慮事項

いくつかの考慮事項が適用されます。

* 特定の資産に関連付けられている 5 つの一意の Locator を同時に使用することはできません。 詳細については、「Locator」をご覧ください。
* すぐにファイルをアップロードする必要がある場合は、StartTime 値を現在の時刻の 5 分前に設定する必要があります。 これは、クライアント コンピューターと Media Services の間にクロック スキューがある可能性があるためです。 また、StartTime 値は YYYY-MM-DDTHH:mm:ssZ ("2014-05-23T17:53:50Z" など) の DateTime 形式である必要があります。    
* Locator を作成した後に使用可能になるまで 30 ～ 40 秒の遅延が発生する場合があります。

### <a name="create-a-sas-locator"></a>SAS ロケーターを作成する

1. **[ロケーター]**  ->  **[Create SAS Locator]\(SAS ロケーターの作成\)** を選択します。
2. **[送信]** をクリックします。

    "test" スクリプトでは、指定したメディア ファイル名と SAS ロケーター情報に基づいて "Upload URL" を作成し、適切な環境変数を設定します。

    ![[Collections]\(コレクション\) メニューから選択された [Locator - Create SAS Locator]\(ロケーター - SAS ロケーターの作成\) と、選択された [Send]\(送信\) ボタンを示すスクリーンショット。](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>アップロード URL を使用して BLOB ストレージにファイルをアップロードする

### <a name="overview"></a>概要

アップロード URL を作成し終えたら、Azure BLOB API を使用して直接コードを記述し、SAS コンテナーにファイルをアップロードする必要があります。 詳細については、次の記事を参照してください。

- [Azure Storage REST API の使用](../../storage/common/storage-rest-api-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [PUT Blob](/rest/api/storageservices/put-blob)
- [BLOB を Blob Storage にアップロードする](/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Postman を使用してファイルをアップロードする

例として、Postman を使用して小規模な .mp4 ファイルをアップロードします。 Postman 経由でバイナリをアップロードする際は、ファイル サイズに制限がある場合があります。

アップロード要求は、**AzureMedia** コレクションの一部です。 

新しい要求を作成して設定するには、次の手順を実行します。
1. **+** キーを押して、新しい要求タブを作成します。
2. **PUT** 操作を選択し、URL に **{{UploadURL}}** を貼り付けます。
2. **[Authorization]\(承認\)** タブはそのままにしておきます( **[ベアラー トークン]** に設定しないでください)。
3. **[ヘッダー]** タブで、次を指定します:**Key**: "x-ms-blob-type" および **Value**:"BlockBlob"。
2. **[本文]** タブで、 **[バイナリ]** をクリックします。
4. **MediaFileName** 環境変数に指定した名前のファイルを選択します。
5. **[送信]** をクリックします。

    ![選択された [(UploadURL)] タブを示すスクリーンショット。](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>資産にメタデータを作成する

ファイルがアップロードされたら、お使いの資産に関連付けられている BLOB ストレージにアップロードしたメディア ファイル用の資産に、メタデータを作成する必要があります。

1. **[AssetFiles]**  ->  **[CreateFileInfos]** の順に選択します。
2. **[送信]** をクリックします。

    ![ファイルをアップロードする](./media/media-services-rest-upload-files/postman-create-file-info.png)

ファイルをアップロードして、メタデータを設定する必要があります。

## <a name="validate"></a>検証

ファイルが正常にアップロードされたことを検証するために、[AssetFile](/rest/api/media/operations/assetfile) を照会して、**ContentFileSize** (または他の詳細項目) と新しい資産で確認できる項目を比較します。 

たとえば、次の **GET** 操作では、資産ファイル (ここでは、BigBuckBunny.mp4 ファイル) のファイル データを取得します。 クエリでは、以前に設定した[環境変数](postman-environment.md)を使用しています。

`{{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files`

応答には、サイズ、名前などの情報が含まれます。

```console
"Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
"Name": "BigBuckBunny.mp4",
"ContentFileSize": "3186542",
"ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
```
  
## <a name="next-steps"></a>次のステップ

これで、アップロードした資産をエンコードできます。 詳細については、 [資産のエンコード](media-services-portal-encode.md)に関するページをご覧ください。

また、Azure Functions を使用すると、構成したコンテナーに到着するファイルに基づいてエンコード ジョブをトリガーすることもできます。 詳細については、[このサンプル](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ )を参照してください。
