---
title:メディアをアップロードする:Azure Media Services の説明:ストリーミングまたはエンコードのためにメディアをアップロードする方法について説明します。
services: media-services documentationcenter: '' author:IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: how-to ms.date:08/31/2020 ms.author: inhenkel
---

# <a name="upload-media-for-streaming-or-encoding"></a>ストリーミングまたはエンコードのためにメディアをアップロードする

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services で、アセットに関連付けられている BLOB コンテナーにデジタル ファイル (メディア) をアップロードします。 [Asset](/rest/api/media/operations/asset) エンティティには、ビデオ、オーディオ、画像、サムネイル コレクション、テキスト トラック、クローズド キャプション ファイル (各ファイルのメタデータを含む) を格納できます。 ファイルをアセットのコンテナーにアップロードすると、コンテンツがクラウドに安全に保存され、処理したりストリーミングしたりできるようになります。

ただし、作業を開始する前に、いくつかの値を収集するか、検討する必要があります。

1. アップロードするファイルへのローカル ファイル パス
1. アセット (コンテナー) のアセット ID
1. 拡張子を含む、アップロードしたファイルに使用する名前
1. 使用しているストレージ アカウントの名前
1. ストレージ アカウントのアクセス キー

## <a name="portal"></a>[ポータル](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="rest"></a>[REST](#tab/rest/)

[Postman やその他の REST 方法を使用してアセットを作成し、そのアセットの SAS URL を取得](how-to-create-asset.md?tabs=rest)したら、Azure Storage API または SDK ([Storage REST API](../../storage/common/storage-rest-api-auth.md) や [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md) など) を使用します。

---
<!-- add these to the tabs when available -->
その他の方法については、[.NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)、[Java](../../storage/blobs/storage-quickstart-blobs-java.md)、[Python](../../storage/blobs/storage-quickstart-blobs-python.md)、[JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md) での BLOB の操作に関する [Azure Storage のドキュメント](../../storage/blobs/index.yml)を参照してください。

## <a name="next-steps"></a>次のステップ

> [Media Services の概要](media-services-overview.md)