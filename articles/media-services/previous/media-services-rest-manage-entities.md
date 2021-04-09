---
title: REST による Media Services エンティティの管理 | Microsoft Docs
description: この記事では、REST API を使用して Media Services エンティティを管理する方法について説明します。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 2821c4c2b13edaf5dfb34664c4b9f78ab56d98a7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015558"
---
# <a name="managing-media-services-entities-with-rest"></a>REST による Media Services エンティティの管理

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services は、OData v3 上に構築された REST ベースのサービスです。 他の OData サービスで行うのとほとんど同じ方法で、エンティティの追加、照会、更新、削除を行うことができます。 該当する場合には例外が呼び出されます。 OData の詳細については、 [Open Data Protocol に関するドキュメント](https://www.odata.org/documentation/)を参照してください。

このトピックでは、REST で Azure Media Services エンティティを管理する方法を説明します。

>[!NOTE]
> 2017 年 4 月 1 日からは、レコードの合計数が最大クォータより小さい場合でも、アカウント内の 90 日前より古いすべてのジョブ レコードが、関連付けられているタスク レコードと共に自動的に削除されます。 たとえば、2017 年 4 月 1 日には、アカウント内の 2016 年 12 月 31 日より古いジョブ レコードはすべて、自動的に削除されます。 ジョブやタスクの情報をアーカイブする必要がある場合は、このトピックで説明するコードを使うことができます。

## <a name="considerations"></a>考慮事項  

Media Services でエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。 詳細については、「 [Media Services REST API の概要](media-services-rest-how-to-use.md)」をご覧ください。

## <a name="connect-to-media-services"></a>Media Services への接続

AMS API に接続する方法については、「[Azure AD 認証を使用した Azure Media Services API へのアクセス](media-services-use-aad-auth-to-access-ams-api.md)」を参照してください。 

## <a name="adding-entities"></a>エンティティの追加
Media Services のすべてのエンティティは、POST HTTP 要求によって Assets などのエンティティ セットに追加されます。

次の例に、AccessPolicy を作成する方法を示します。

```console
POST https://media.windows.net/API/AccessPolicies HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
Content-Length: 74
Expect: 100-continue

{"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}
```

## <a name="querying-entities"></a>エンティティの照会
エンティティの照会と一覧化は単純であり、GET HTTP 要求と、オプションで OData 操作を行うだけですみます。
次の例では、すべての MediaProcessor エンティティの一覧を取得します。

```console
GET https://media.windows.net/API/MediaProcessors HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

次の例に示すように、特定のエンティティや、特定のエンティティに関連付けられているすべてのエンティティ セットを取得することもできます。

```console
GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net

GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

次の例では、すべての Jobs の State プロパティのみを返します。

```console
GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

次の例では、"SampleTemplate" という名前のすべての JobTemplates を返します。

```console
GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

> [!NOTE]
> Media Services では、LINQ に関する考慮事項で説明したサポートされていない LINQ メソッドと同様に、$expand 操作もサポートされていません (WCF Data Services)。
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>大規模なコレクションのエンティティの列挙
パブリック REST v2 では、クエリ結果が 1000 件に制限されているため、エンティティを照会するときには、一度に返されるエンティティが 1000 個に制限されます。 大規模なコレクションのエンティティを列挙するには、**skip** と **top** を使用します。 

次の例は、**skip** と **top** を使用して、最初の 2000 個のジョブをスキップし、次の 1000 個のジョブを取得する方法を示しています。  

```console
GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
```

## <a name="updating-entities"></a>エンティティの更新
エンティティの種類とその状態に応じて、PATCH 要求、PUT 要求、または MERGE HTTP 要求を使用してエンティティのプロパティを更新することができます。 これらの操作の詳細については、「 [PATCH/PUT/MERGE](/openspecs/windows_protocols/ms-odata/59d5abd3-7b12-490a-a0e2-9d9324b91893)」を参照してください。

次のコード例では、Asset エンティティの Name プロパティを更新する方法を示します。

```console
MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
Content-Length: 21
Expect: 100-continue

{"Name" : "NewName" }
```

## <a name="deleting-entities"></a>エンティティの削除
Media Services のエンティティは、DELETE HTTP 要求を使用して削除できます。 エンティティによっては、エンティティを削除する順序が重要になる場合があります。 たとえば、Asset などのエンティティでは、特定の Asset を削除する前に、その Asset を参照するすべてのロケーターを取り消す (または削除する) 必要があります。

次の例では、BLOB ストレージへのファイルのアップロードに使用したロケーターを削除する方法を示します。

```console
DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
Content-Length: 0
```

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
