---
title: "Media Services Operations REST API の概要 | Microsoft Docs"
description: "Media Services REST API の概要"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/29/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 8b6e19e3c495eec4e16187524df0b13e4ffce9de


---
# <a name="media-services-operations-rest-api-overview"></a>Media Services Operations REST API の概要
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

**Media Services Operations REST** API は、Media Services アカウントのオブジェクトでジョブ、アセット、アクセス ポリシー、およびその他の操作を作成するときに使用されます。 詳細については、[Media Services Operations REST API リファレンス](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)を参照してください。

Microsoft Azure Media Services は OData ベースの HTTP 要求を受け付けるサービスであり、詳細 JSON または atom+pub で応答が可能です。 Media Services は、Azure 設計ガイドラインに準拠しているため、Media Services に接続するときに各クライアントが使用する必要がある必須 HTTP ヘッダーのセットと、使用できる省略可能なヘッダーのセットがあります。 次のセクションでは、要求を作成したり Media Services から応答を受信したりするときに使用できるヘッダーと HTTP 動詞について説明します。


## <a name="considerations"></a>考慮事項

REST を使用するときには、次の考慮事項が適用されます。

* パブリック REST v2 では、クエリ結果が 1000 件に制限されているため、エンティティを照会するときには、一度に返されるエンティティが 1000 個に制限されます。 [この .NET の例](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities)と[この REST API の例](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities)に示すように、**Skip** および **Take** (.NET)/**top** (REST) を使用する必要があります。 
* JSON を使用し、要求で **__metadata** キーワードの使用を指定した場合 (リンクされたオブジェクトを参照する場合など)、**Accept** ヘッダーを [JSON Verbose 形式](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)に設定する必要があります (次の例を参照)。 verbose に設定していない場合、Odata は要求内の **__metadata** プロパティを認識しません。  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.11
        Authorization: Bearer <token> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Media Services でサポートされている標準の HTTP 要求ヘッダー
Media Services に対して行うそれぞれの呼び出しについて、要求に含める必要がある必須のヘッダーのセットと、含める可能性がある省略可能なヘッダーのセットがあります。 次の表に、それらの必須のヘッダーを示します。

| ヘッダー | 型 | 値 |
| --- | --- | --- |
| 承認 |ベアラ |ベアラは、唯一許容される承認のメカニズムです。 値には、ACS によって提供されるアクセス トークンを含める必要もあります。 |
| x-ms-version |小数点 |2.11 |
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Media Services は、OData を使用して REST　APIを介して管理している資産メタデータ レポジトリを公開するため、DataServiceVersion および MaxDataServiceVersion ヘッダーをどの要求にも含める必要があります。含めない場合、Media Services は使用中の DataServiceVersion の値を3.0 と見なします。
> 
> 

省略可能なヘッダーのセットを次に示します。

| ヘッダー | 型 | 値 |
| --- | --- | --- |
| Date |RFC 1123 の日付 |要求のタイムスタンプ |
| Accept |コンテンツの種類 |次のような応答に対する要求のコンテンツの種類:<p> - application/json;odata=verbose<p> - application/atom+xml<p> 応答には、blob フェッチのように、さまざまなコンテンツの種類があります。正常な応答にはペイロードなどの blob ストリームが含まれます。 |
| Accept-Encoding |Gzip、deflate |GZIP Encoding および DEFLATE Encoding　(該当する場合)。 注: 大きなリソースでは、Media Services はこのヘッダーを無視し、圧縮されていないデータを返す場合があります。 |
| Accept-Language |"en"、"es" など。 |応答の優先言語を指定します。 |
| Accept-Charset |"UTF-8"　などの文字の種類 |既定値は UTF-8 です。 |
| X-HTTP-Method |HTTP メソッド |PUT や DELETE などの HTTP メソッドをサポートしないクライアントやファイアウォールが、GET 呼び出しを通じてトンネリングされたこれらのメソッドを使用できます。 |
| Content-Type |コンテンツの種類 |PUT または POST 要求の本文のコンテンツの種類。 |
| client-request-id |String |指定した要求を識別する、呼び出し元で定義された値。 指定されている場合、この値は、要求をマッピングする方法として、応答メッセージに含まれます。 <p><p>**重要**<p>値は 2096b (2k) に制限する必要があります。 |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Media Services でサポートされている標準の HTTP 応答ヘッダー
要求したリソースと実行を意図した操作によって返されるヘッダーのセットを次に示します。

| ヘッダー | 型 | 値 |
| --- | --- | --- |
| request-id |String |現在の操作、生成されたサービスのための一意の識別子。 |
| client-request-id |String |元の要求の呼び出し元によって指定された識別子 (存在する場合)。 |
| Date |RFC 1123 の日付 |要求が処理された日付。 |
| Content-Type |多様 |応答本文のコンテンツの種類。 |
| Content-Encoding |多様 |Gzip またはデフレート (必要に応じて)。 |

## <a name="standard-http-verbs-supported-by-media-services"></a>Media Services でサポートされている標準の HTTP 動詞
HTTP 要求を行うときに使用できる HTTP 動詞の完全な一覧を次に示します。

| 動詞 | Description |
| --- | --- |
| GET |オブジェクトの現在の値を返します。 |
| POST |提供されるデータに基づくオブジェクトを作成、またはコマンドを送信します。 |
| PUT |オブジェクトの置き換え、または名前付きのオブジェクトの作成を行います (使用している場合)。 |
| 削除 |オブジェクトを削除します。 |
| MERGE |名前付きプロパティを変更して、既存のオブジェクトを更新します。 |
| HEAD |GET 応答に対するオブジェクトのメタデータを返します。 |

## <a name="limitation"></a>制限事項
パブリック REST v2 では、クエリ結果が 1000 件に制限されているため、エンティティを照会するときには、一度に返されるエンティティが 1000 個に制限されます。 [この .NET の例](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities)と[この REST API の例](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities)に示すように、**Skip** および **Take** (.NET)/**top** (REST) を使用する必要があります。 

## <a name="discovering-media-services-model"></a>Media Services のモデルの検出
Media Services のエンティティを見つけやすくするには、$metadata 操作を使用できます。 これによって、すべての有効なエンティティの種類、エンティティのプロパティ、関連付け、関数、アクションなどを取得できます。 次の例は、URI を作成する方法を示しています。 https://media.windows.net/API/$metadata

ブラウザーにメタデータを表示する場合は　URI の末尾に "?api-version=2.x" を追加してください。または、要求に　x-ms-version　を含めないでください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Dec16_HO2-->


