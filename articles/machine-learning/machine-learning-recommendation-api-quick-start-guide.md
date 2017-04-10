---
title: "クイック スタート: Azure Machine Learning Recommendations API (バージョン  1) | Microsoft Docs"
description: "Azure Machine Learning Recommendations - クイック スタート ガイド"
services: machine-learning
documentationcenter: 
author: LuisCabrer
manager: jhubbard
editor: cgronlun
ms.assetid: 5bce1a4a-1ad6-473f-812b-84f800fdc09a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: c5d1812a3bd7e1ad3621ca691339f01729882b08
ms.openlocfilehash: 67131bbaccff549817bfe5fdf874d609ea22c2bc
ms.lasthandoff: 01/31/2017


---
# <a name="quick-start-guide-for-the-machine-learning-recommendations-api-version-1"></a>Machine Learning Recommendations API (バージョン 1) のクイック スタート ガイド

> [!NOTE]
> このバージョンの代わりに [Recommendations API Cognitive Service](https://www.microsoft.com/cognitive-services/recommendations-api) の使用を開始することをお勧めします。 このサービスは Recommendations Cognitive Service に置き換えられ、新機能はすべて Cognitive Service で開発されるようになります。 Cognitive Service には、バッチ処理のサポート、API エクスプローラーの改善、API サーフェスの簡素化、より一貫性のあるサインアップおよび課金方法などの新機能が含まれています。
>
> 詳細については、[新しい Cognitive Service への移行](http://aka.ms/recomigrate)に関するページを参照してください。
> 
> 

このドキュメントでは、サービスやアプリケーションで Microsoft Azure Machine Learning の Recommendations を使用する方法について説明します。 Recommendations API の詳細については、[Cortana Intelligence Gallery ギャラリー](https://gallery.cortanaintelligence.com/MachineLearningAPIs/Recommendations-2)を参照してください。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="general-overview"></a>概要
Azure Machine Learning の Recommendations を使用するには、次の手順を実行する必要があります。

* モデルの作成 – モデルは、使用状況データ、カタログ データ、推奨モデルのコンテナーです。
* カタログ データのインポート - カタログには、項目に関するメタデータ情報が含まれます。 
* 使用状況データのインポート – 使用状況データは、2 つの方法のいずれか (または両方) でアップロードできます。
  * 使用状況データを含むファイルをアップロードする。
  * データ取得イベントを送信する。
    通常は、使用状況ファイルをアップロードして初期の推奨モデル (ブートストラップ) を作成し、システムがデータ取得形式を使用して十分なデータを収集するまではそのモデルを使用します。
* 推奨モデルの構築 – これは、推奨システムがすべての使用状況データを受け取って推奨モデルを作成する非同期操作です。 この操作は、データのサイズとビルド構成パラメーターによっては、数分または数時間かかることがあります。 ビルドをトリガーすると、ビルドの ID を取得します。 Recommendations の使用を開始する前に、ビルド ID を使用してビルド プロセスの終了を確認します。
* 推奨の使用 – 特定の項目や項目一覧に対する推奨を取得します。

これらの手順がすべて、Azure Machine Learning の Recommendations API で実行されます。  [ギャラリー](http://1drv.ms/1xeO2F3)

## <a name="limitations"></a>制限事項
* サブスクリプションごとのモデルの最大数は 10 です。
* カタログが保持できる項目の最大数は 100,000 です。
* 保持される使用状況ポイントの最大数は ~5,000,000 です。 新しいデータがアップロードまたは報告されると、最も古いデータが削除されます。
* POST で送信できるデータ (例: カタログ データのインポート、使用データのインポート) の最大サイズは 200 MB です。
* 無効な推奨モデルのビルドの&1; 秒あたりのトランザクションの数は ~2TPS です。 有効な推奨モデルのビルドは、最大で 20TPS を保持できます。

## <a name="integration"></a>統合
### <a name="authentication"></a>認証
Microsoft Azure Marketplace は、Basic または OAuth のいずれかの認証方法をサポートします。 アカウント キーは、 [アカウント設定](https://datamarket.azure.com/account/keys)のマーケットプレース内のキーに移動すると簡単に見つけることができます。 

#### <a name="basic-authentication"></a>基本認証
承認ヘッダーの追加: 

    Authorization: Basic <creds>

    Where <creds> = ConvertToBase64("AccountKey:" + yourAccountKey);  

Base&64; に変換 (c#)

    var bytes = Encoding.UTF8.GetBytes("AccountKey:" + yourAccountKey);
    var creds = Convert.ToBase64String(bytes);

Base&64; に変換 (JavaScript)

    var creds = window.btoa("AccountKey" + ":" + yourAccountKey);




### <a name="service-uri"></a>サービス URI
Azure Machine Learning Recommendations API のサービス ルート URI は、 [ここ](https://api.datamarket.azure.com/amla/recommendations/v2/)

サービス URI はすべて、OData 仕様の要素を使用して表されます。

### <a name="api-version"></a>API バージョン
各 API 呼び出しでは、最後に apiVersion というクエリ パラメーターを「1.0」に設定する必要があります。

### <a name="ids-are-case-sensitive"></a>ID には大文字小文字の区別がある
API のいずれかにより返される ID は大文字と小文字の区別があり、後続の API 呼び出しにパラメーターとして渡されるときにはそのとおりに使用する必要があります。 たとえば、モデル ID およびカタログ ID では、大文字と小文字が区別されます。

### <a name="create-a-model"></a>モデルの作成
"モデルの作成" 要求を作成します。

| HTTP メソッド | URI |
|:--- |:--- |
| POST |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelName |英字 (A～Z、a～z)、数字 (0～9)、ハイフン (-)、アンダースコア (_) のみが許可されます。<br>最大長: 20 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

* `feed/entry/content/properties/id` – モデル ID が含まれます。
  モデル ID では、大文字と小文字が区別されます。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Create A New Model</subtitle>
      <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T06:35:21Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
      </entry>
    </feed>


### <a name="import-catalog-data"></a>カタログ データのインポート
複数の呼び出しで同じモデルに複数のカタログ ファイルがアップロードされた場合は、新しいカタログ項目のみを挿入します。 既存の項目は、元の値でそのまま残ります。

| HTTP メソッド | URI |
|:--- |:--- |
| POST |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 (大文字と小文字が区別されます) |
| filename |カタログを表すテキスト形式の識別子。<br>英字 (A～Z、a～z)、数字 (0～9)、ハイフン (-)、アンダースコア (_) のみが許可されます。<br>最大長: 50 |
| apiVersion |1.0 |
|  | |
| 要求本文 |カタログ データ。 形式:<br>`<Item Id>,<Item Name>,<Item Category>[,<description>]`<br><br><table><tr><th>名前</th><th>必須</th><th>型</th><th>Description</th></tr><tr><td>項目 ID</td><td>はい</td><td>英数字、最大長は 50 文字</td><td>項目の一意識別子</td></tr><tr><td>Item Name</td><td>あり</td><td>英数字、最大長は 255 文字</td><td>項目名</td></tr><tr><td>Item Category</td><td>はい</td><td>英数字、最大長は 255 文字</td><td>この項目が属しているカテゴリ (例: 料理本、ドラマ...)</td></tr><tr><td>Description</td><td>なし</td><td>英数字、最大長は 4000 文字</td><td>この項目の説明</td></tr></table><br>ファイルの最大サイズは、200 MB です。<br><br>例:<br><code>2406e770-769c-4189-89de-1c9283f93a96,Clara Callan,Book<br>21bf8088-b6c0-4509-870c-e1c7ac78304a,The Forgetting Room: A Fiction (Byzantium Book),Book<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23,Spadework,Book<br>552a1940-21e4-4399-82bb-594b46d7ed54,Restraint of Beasts,Book</code> |

**応答**:

HTTP 状態コード: 200

* `Feed\entry\content\properties\LineCount` – 受け入れられる行数。
* `Feed\entry\content\properties\ErrorCount` – エラーのために挿入されなかった行数。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
          <subtitle type="text">Import catalog file</subtitle>
          <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T06:58:04Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
      </entry>
    </feed>


### <a name="import-usage-data"></a>使用状況データのインポート
#### <a name="uploading-a-file"></a>ファイルのアップロード
このセクションでは、ファイルを使用して使用状況データをアップロードする方法を示します。 この API は使用状況データと共に何度も呼び出すことができます。 すべての呼び出しですべての使用状況データが保存されます。

| HTTP メソッド | URI |
|:--- |:--- |
| POST |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 (大文字と小文字が区別されます) |
| filename |カタログを表すテキスト形式の識別子。<br>英字 (A～Z、a～z)、数字 (0～9)、ハイフン (-)、アンダースコア (_) のみが許可されます。<br>最大長: 50 |
| apiVersion |1.0 |
|  | |
| 要求本文 |使用状況データ。 形式:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>名前</th><th>必須</th><th>型</th><th>Description</th></tr><tr><td>ユーザー ID</td><td>はい</td><td>英数字</td><td>ユーザーの一意識別子</td></tr><tr><td>項目 ID</td><td>はい</td><td>英数字、最大長は 50 文字</td><td>項目の一意識別子</td></tr><tr><td>Time</td><td>なし</td><td>YYYY/MM/DDTHH:MM:SS 形式の日付 (例: 2013/06/20T10:00:00)</td><td>データの時間</td></tr><tr><td>イベント</td><td>いいえ、指定した場合は日付も指定すること</td><td>次のいずれか:<br>• Click<br>• RecommendationClick<br>•    AddShopCart<br>• RemoveShopCart<br>• Purchase</td><td></td></tr></table><br>ファイルの最大サイズは、200 MB です。<br><br>例:<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**応答**:

HTTP 状態コード: 200

* `Feed\entry\content\properties\LineCount` – 受け入れられる行数。
* `Feed\entry\content\properties\ErrorCount` – エラーのために挿入されなかった行数。
* `Feed\entry\content\properties\FileId` – ファイル識別子。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Add bulk usage data (usage file)</subtitle>
      <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T07:21:44Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
      </entry>
    </feed>


#### <a name="using-data-acquisition"></a>データ取得の使用
このセクションでは、Web サイトから Azure Machine Learning Recommendations にリアルタイムでイベントを送信する方法を示します。

| HTTP メソッド | URI |
|:--- |:--- |
| POST |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| apiVersion |1.0 |
|  | |
| 要求本文 |送信する各イベントのイベント データ エントリ。 同じユーザーまたはブラウザーのセッションに対して、SessionId フィールドに同じ ID を送信する必要があります。 (以下のイベントの本文のサンプルを参照してください)。 |

* 'Click' のイベントの例:
  
        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>
* 'RecommendationClick' のイベントの例:
  
        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
          <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
          <SessionId>11112222</SessionId>
          <EventData>
        <EventData>
          <Name>RecommendationClick</Name>
          <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
          </EventData>
        </Event>
* 'AddShopCart' のイベントの例:
  
        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
          <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
          <SessionId>11112222</SessionId>
          <EventData>
        <EventData>
          <Name>AddShopCart</Name>
          <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
          </EventData>
        </Event>
* 'RemoveShopCart' のイベントの例:
  
        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
          <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
          <SessionId>11112222</SessionId>
          <EventData>
        <EventData>
          <Name>RemoveShopCart</Name>
          <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
          </EventData>
        </Event>
* 'Purchase' のイベントの例:
  
        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
            <Name>Purchase</Name> 
            <PurchaseItems>
            <PurchaseItems>
                <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
                <Count>3</Count>
            </PurchaseItems>
        </PurchaseItems>
        </EventData>
        </EventData>
        </Event>
* 2 つのイベント "Click" と "AddShopCart" を送信する例:
  
        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
          <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
          <SessionId>11112222</SessionId>
          <EventData>
        <EventData>
          <Name>Click</Name>
          <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
          <ItemName>itemName</ItemName>
          <ItemDescription>item description</ItemDescription>
          <ItemCategory>category</ItemCategory>
        </EventData>
        <EventData>
          <Name>AddShopCart</Name>
          <ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
        </EventData>
          </EventData>
        </Event>

**応答**: HTTP 状態コード: 200

### <a name="build-a-recommendation-model"></a>推奨モデルの構築
| HTTP メソッド | URI |
|:--- |:--- |
| POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 (大文字と小文字が区別されます) |
| userDescription |カタログを表すテキスト形式の識別子。 空白を使用する場合は、%20 にエンコードする必要があることに注意してください上記の例をご覧ください。 (上記の例を参照)。<br>最大長: 50 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

これは、非同期 API です。 応答として、ビルド ID が表示されます。 ビルドがいつ終了したかを知るには、「モデルのビルド状態を取得する」API を呼び出し、応答でこのビルド ID を検索する必要があります。 データのサイズによっては、ビルドが数分から数時間かかる可能性があることに注意してください。

ビルドが終了するまで推奨事項を使用することはできません。

有効なビルド状態:

* Create – モデルが作成されました。
* Queued – モデルのビルドがトリガーされ、キューに登録されています。
* Building – モデルをビルドしています。
* Success - ビルドが正常に終了しました。
* Error – ビルドでエラーが発生して終了しました。
* Canceled – ビルドが取り消されました。
* Canceling – ビルドを取り消しています。

ビルド ID は、次のパスの下にあります： `Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Build a Model with RequestBody</subtitle>
      <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T08:56:34Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
      </entry>
    </feed>

### <a name="get-build-status-of-a-model"></a>モデルのビルド状態の取得
| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 (大文字と小文字が区別されます) |
| onlyLastBuild |モデルのすべてのビルド履歴を返すか、最新のビルドの状態のみを返すかを示します。 |
| apiVersion |1.0 |

**応答**:

HTTP 状態コード: 200

応答には、ビルドごとに&1; つのエントリが含まれています。 各エントリには、次のデータが含まれています。

* `feed/entry/content/properties/UserName` – ユーザーの名前。
* `feed/entry/content/properties/ModelName` – モデルの名前。
* `feed/entry/content/properties/ModelId` – モデルの一意識別子。
* `feed/entry/content/properties/IsDeployed` - ビルドがデプロイされている ( アクティブなビルド) かどうか。
* `feed/entry/content/properties/BuildId` – ビルドの一意識別子。
* `feed/entry/content/properties/BuildType` - ビルドの種類。
* `feed/entry/content/properties/Status` – ビルド状態。 次のいずれか: Error、Building、Queued、Canceling、Canceled、Success
* `feed/entry/content/properties/StatusMessage` – 詳細なステータス メッセージ (特定の状態にのみ適用されます)。
* `feed/entry/content/properties/Progress` – ビルドの進捗状況 (%)。
* `feed/entry/content/properties/StartTime` – ビルドの開始時刻。
* `feed/entry/content/properties/EndTime` – ビルドの終了時刻。
* `feed/entry/content/properties/ExecutionTime` – ビルドの期間。
* `feed/entry/content/properties/ProgressStep` - 進行中のビルドの現在のステージの詳細。

有効なビルド状態:

* Created – ビルド要求エントリが作成されました。
* Queued – ビルド要求がトリガーされ、キューされます。
* Building - ビルドが処理中です。
* Success - ビルドが正常に終了しました。
* Error – ビルドでエラーが発生して終了しました。
* Canceled – ビルドが取り消されました。
* Canceling – ビルドを取り消しています。

ビルドの種類における有効な値:

* Rank - 順番付けのビルド。 (順位付けのビルドの詳細については、「Machine Learning Recommendation API のドキュメント」を参照してください。)
* Recommendation - 推奨事項のビルド。
* Fbt - Frequently Bought Together ビルド。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get builds status of a model</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
        <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
        <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
        <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
        <d:BuildId m:type="Edm.String">1000272</d:BuildId>
        <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
        <d:Status m:type="Edm.String">Success</d:Status>
        <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
        <d:Progress m:type="Edm.String">0</d:Progress>
        <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
        <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
        <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
        <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
        <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
      </m:properties>
     </content>
    </entry>
    </feed>


### <a name="get-recommendations"></a>推奨の取得
| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 (大文字と小文字が区別されます) |
| itemIds |推奨項目のコンマ区切りの一覧。<br>最大長: 1024 |
| numberOfResults |必要な結果の数  |
| includeMetatadata |将来的に利用 (常に false) |
| apiVersion |1.0 |

**応答:**

HTTP 状態コード: 200

応答には、推奨項目ごとに&1; つのエントリが含まれています。 各エントリには、次のデータが含まれています。

* `Feed\entry\content\properties\Id` - 推奨項目の ID。
* `Feed\entry\content\properties\Name` - 項目の名前。
* `Feed\entry\content\properties\Rating` - 推奨項目の評価、高い数値は高い信頼度を意味します。
* `Feed\entry\content\properties\Reasoning` - 推奨の理由 (推奨の説明など)。

OData XML

次の応答例には、10 個の推奨項目が含まれています。

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Get Recommendation</subtitle>
      <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T12:28:48Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
    </feed>

### <a name="update-model"></a>モデルの更新
モデルの説明またはアクティブなビルド ID を更新できます。
*アクティブなビルド ID* - すべてのモデルのすべてのビルドには "ビルド ID" があります。 アクティブな "ビルド ID" は、新しいモデルそれぞれの最初の正常なビルドです。 アクティブなビルド ID があり、同じモデルに対して追加のビルドを実行する場合は、必要に応じて、既定のビルド ID として明示的に設定する必要があります。 推奨を使用する際に、使用するビルド ID を指定しないと、既定の ID が自動的に使用されます。

このメカニズムにより、推奨モデルが運用環境にあるときに、新しいモデルを構築して、それを運用環境に移行する前にテストすることができます。

| HTTP メソッド | URI |
|:--- |:--- |
| PUT |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| id |モデルの一意識別子 (大文字と小文字が区別されます) |
| apiVersion |1.0 |
|  | |
| 要求本文 |`<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`   <Description>New Description</Description>`<br>`          <ActiveBuildId>-1</ActiveBuildId>`<br>`</ModelUpdateParams>`<br><br>XML タグ Description と ActiveBuildId は省略可能です。 Description や ActiveBuildId を設定したくない場合は、タグ全体を削除します。 |

**応答**:

HTTP 状態コード: 200

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Update an Existing Model</subtitle>
      <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T10:27:17Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'" />
    </feed>

## <a name="legal"></a>法的情報
このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 使用している例は、例示のみを目的に提供された、架空のものです。 実際の関連やつながりはなく、推測によるものです。 このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利も提供するものではありません。 お客様の社内での参照目的に限り、このドキュメントをコピーし使用することができます。 © 2014 Microsoft. All rights reserved. 


