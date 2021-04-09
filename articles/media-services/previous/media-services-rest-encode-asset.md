---
title: Media Encoder Standard を使用して Azure 資産をエンコードする方法 | Microsoft Docs
description: Media Encoder Standard を使用して Azure Media Services でメディア コンテンツをエンコードする方法について説明します。 コード サンプルでは REST API を使用しています。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d4b7a188a9509c3510de2843b232f57e3437177b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009829"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Media Encoder Standard を使用して資産をエンコードする方法

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [ポータル](media-services-portal-encode.md)
>
>

## <a name="overview"></a>概要

インターネット経由でデジタル ビデオを配信するには、メディアを圧縮する必要があります。 デジタル ビデオ ファイルはサイズがとても大きく、インターネット経由で配信したり、顧客のデバイスでうまく表示したりできない可能性があります。 エンコードはビデオやオーディオを圧縮するプロセスです。圧縮することでメディア コンテンツが見やすくなります。

エンコード ジョブは Azure Media Services で最も一般的な処理の 1 つです。 エンコード ジョブを作成することで、メディア ファイルをあるエンコードから別のエンコードに変換できるようになります。 エンコードするときは、Media Services の組み込みエンコーダー (Media Encoder Standard) を使用できます。 さらに、Media Services パートナーで提供されるエンコーダーを使用することもできます。 サード パーティのエンコーダーは Azure Marketplace から入手できます。 エンコーディング タスクの詳細を指定するには、エンコーダー用に定義されたプリセット文字列を使用するか、プリセット構成ファイルを使用します。 使用できるプリセットの種類を確認する場合は、「[Task Presets for Media Encoder Standard (Media Encoder Standard のタスク プリセット)](./media-services-mes-presets-overview.md)」を参照してください。

各ジョブは実行する処理の種類に応じて 1 つまたは複数のタスクを持つことができます。 REST API を使って、2 つの方法のいずれかでジョブおよびジョブに関連するタスクを作成できます。

* タスクは、Job エンティティの Tasks ナビゲーション プロパティを使用して、インラインで定義できます。
* OData バッチ処理を使用します。

ソース ファイルは常にアダプティブ ビットレート MP4 セットにエンコードして、その後 [ダイナミック パッケージ](media-services-dynamic-packaging-overview.md)を使用して目的の形式に変換することをお勧めします。

出力資産がストレージで暗号化されている場合は、資産配信のポリシーを構成する必要があります。 詳細については、「[方法: アセットの配信ポリシーを構成する](media-services-rest-configure-asset-delivery-policy.md)」を参照してください。

## <a name="considerations"></a>考慮事項

Media Services でエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。 詳細については、「 [Media Services REST API の概要](media-services-rest-how-to-use.md)」をご覧ください。

メディア プロセッサの参照を開始する前に、正しいメディア プロセッサの ID を使用していることを確認してください。 詳細については、[メディア プロセッサの取得](media-services-rest-get-media-processor.md)に関するページをご覧ください。

## <a name="connect-to-media-services"></a>Media Services への接続

AMS API に接続する方法については、「[Azure AD 認証を使用した Azure Media Services API へのアクセス](media-services-use-aad-auth-to-access-ams-api.md)」を参照してください。 

## <a name="create-a-job-with-a-single-encoding-task"></a>1 つのエンコード タスクを持つジョブの作成

> [!NOTE]
> Media Services REST API を使用する場合は、次のことに考慮します。
>
> Media Services でエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。 詳細については、「[Media Services REST API の概要](media-services-rest-how-to-use.md)」をご覧ください。
>
> JSON を使用しており、(たとえば、リンクされたオブジェクトを参照するために) 要求で **__metadata** キーワードを使用することを指定する場合は、**Accept** ヘッダーを [JSON Verbose 形式](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (Accept: application/json;odata=verbose) に設定する必要があります。
>
>

次の例では、1 つのタスクが設定されたジョブを作成して公開し、特定の解像度と質でビデオをエンコードする方法について説明します。 Media Encoder Standard でエンコードするときは、 [こちら](./media-services-mes-presets-overview.md)で指定されているタスク構成のプリセットを使用できます。

要求:

```console
POST https://media.windows.net/API/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net

{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}
```

応答:

```console
HTTP/1.1 201 Created

. . .
```

### <a name="set-the-output-assets-name"></a>出力アセットの名前を設定します。
次の例では、assetName 属性を設定する方法を示します。

```console
{ "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}`
```

### <a name="considerations"></a>考慮事項
* TaskBody プロパティでは、リテラル XML を使用して、タスクが使用する資産の入力または出力数を定義する必要があります。 タスクの記事には、XML のための XML スキーマ定義が含まれます。
* TaskBody の定義には各 `<inputAsset>` の内部値と `<outputAsset>` を JobInputAsset(value) または JobOutputAsset(value) として設定する必要があります。
* タスクは、複数の出力資産を持つことができます。 1 つの JobOutputAsset(x) はジョブ内のタスクの出力として一度だけ使用できます。
* タスクの入力資産として、JobInputAsset または JobOutputAsset を指定できます。
* タスクは、サイクルを形成することはできません。
* JobInputAsset または JobOutputAsset に渡す値パラメーターは、資産のインデックス値を表します。 実際の資産は、ジョブ エンティティ定義の InputMediaAssets および OutputMediaAssets ナビゲーション プロパティで定義されます。
* Media Services は OData v3 上に構築されるため、InputMediaAssets および OutputMediaAssets ナビゲーション プロパティ コレクション内の個々の資産は、"__metadata: uri" の名前と値のペアによって参照されます。
* InputMediaAssets は、Media Services で作成した1 つまたは複数の資産にマップされます。 OutputMediaAssets はシステムによって作成されます。 既存の資産は参照しません。
* OutputMediaAssets は、assetName 属性を使用して名前を付けることができます。 この属性が存在しない場合、OutputMediaAsset の名前は、ジョブ名の値またはジョブ ID の値 (Name プロパティが定義されていない場合) のいずれかのサフィックスを持つ `<outputAsset>` 要素の内部テキストの値になります。 たとえば、"Sample" に assetName の値を設定する場合は、OutputMediaAsset Name プロパティは "Sample" に指定されます。 ただし、assetName の値を設定せずジョブ名を "NewJob" に設定した場合は、OutputMediaAsset Name は "JobOutputAsset(value)_NewJob" になります。

## <a name="create-a-job-with-chained-tasks"></a>チェーン タスクによるジョブの作成
アプリケーション シナリオの多くで、一連のタスク処理が開発者によって作成されます。 Media Services では、一連のチェーン タスクを作成できます。 各タスクはそれぞれ異なる処理手順を実行し、異なるメディア プロセッサを使うことができます。 チェーン タスクでは、あるタスクから別のタスクに資産を渡しながら一連のタスクを順番に実行できます。 ただし、ジョブで実行されるタスクが必ず順番に実行されなければならないということではありません。 チェーン タスクを作成するときは、一連の **ITask** オブジェクトは 1 つの **IJob** オブジェクト内で作成されます。

> [!NOTE]
> 現時点では、ジョブごとに設定できるタスクは 30 までです。 30 以上のタスクをつなげる必要がある場合、別のジョブを作成してタスクを設定します。
>
>

```console
POST https://media.windows.net/api/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{  
   "Name":"NewTestJob",
   "InputMediaAssets":[  
      {  
         "__metadata":{  
            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
         }
      }
   ],
   "Tasks":[  
      {  
         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
      },
      {  
         "Configuration":"H264 Smooth Streaming 720p",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
      }
   ]
}
```

### <a name="considerations"></a>考慮事項
タスクのチェーンを有効にするには、

* ジョブに少なくとも 2 つのタスクがある必要があります。
* タスクの入力がジョブ内の別のタスクの出力である、1 つ以上のタスクがある必要があります。

## <a name="use-odata-batch-processing"></a>OData バッチ処理の使用
次の例では、OData バッチ処理を使用して、ジョブとタスクを作成する方法を示します。 バッチ処理の詳細については、 [Open Data Protocol (OData) のバッチ処理](https://www.odata.org/documentation/odata-version-3-0/batch-processing/)に関するページを参照してください。

```console
POST https://media.windows.net/api/$batch HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
Accept: multipart/mixed
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
Host: media.windows.net


--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

--changeset_122fb0a4-cd80-4958-820f-346309967e4d
Content-Type: application/http
Content-Transfer-Encoding: binary

POST https://media.windows.net/api/Jobs HTTP/1.1
Content-ID: 1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

--changeset_122fb0a4-cd80-4958-820f-346309967e4d
Content-Type: application/http
Content-Transfer-Encoding: binary

POST https://media.windows.net/api/$1/Tasks HTTP/1.1
Content-ID: 2
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{  
   "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
   "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
   "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
}

--changeset_122fb0a4-cd80-4958-820f-346309967e4d--
--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
```


## <a name="create-a-job-by-using-a-jobtemplate"></a>JobTemplate を使用したジョブの作成
共通する一連のタスクを使って複数のアセットを処理するときは、JobTemplates を使用して、既定のタスク プリセットを指定したり、タスクの順序を設定したりします。

次の例では、TaskTemplate をインラインで定義して JobTemplate を作成しています。 この TaskTemplate では、Media Encoder Standard を MediaProcessor として、アセット ファイルをエンコードしています。 ただし、他の MediaProcessors を使用してもかまいません。

```console
POST https://media.windows.net/API/JobTemplates HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net


{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
```

> [!NOTE]
> GUID 識別子は、他の Media Services エンティティとは異なり、TaskTemplate ごとに新たに定義し、要求本文の taskTemplateId と Id プロパティに設定する必要があります。 コンテンツ ID スキームについては、「Azure Media Services エンティティの識別」で説明されているスキームに従ってください。 また、JobTemplates を更新することはできません。 最新の変更内容を基に新たに作成する必要があります。
>
>

成功した場合、次の応答が返されます。

```console
HTTP/1.1 201 Created

. . .
```

次の例では、JobTemplate ID を参照するジョブを作成する方法を示します。

```console
POST https://media.windows.net/API/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net


{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
```

成功した場合、次の応答が返されます。

```console
HTTP/1.1 201 Created

. . .
```


## <a name="advanced-encoding-features-to-explore"></a>高度なエンコーディング機能の詳細
* [サムネイルを生成する方法](media-services-dotnet-generate-thumbnail-with-mes.md)
* [エンコード中のサムネイルの生成](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [エンコード中にビデオをトリミングする](media-services-crop-video.md)
* [エンコード プリセットをカスタマイズする](media-services-custom-mes-presets-with-dotnet.md)
* [ビデオと画像を重ね合わせる、または透かしを入れる](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>次のステップ
これで、ジョブを作成してアセットをエンコードする方法を学習できました。次は、[Media Services でジョブの進行状況をチェックする方法](media-services-rest-check-job-progress.md)に関するトピックを参照してください。

## <a name="see-also"></a>関連項目
[メディア プロセッサの取得](media-services-rest-get-media-processor.md)