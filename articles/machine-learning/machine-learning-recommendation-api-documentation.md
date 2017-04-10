---
title: "Machine Learning Recommendations API ドキュメント | Microsoft Docs"
description: "推奨エンジンにおける Azure Machine Learning Recommendations API ドキュメントは Microsoft Azure Marketplace で入手できます。"
services: machine-learning
documentationcenter: 
author: LuisCabrer
manager: jhubbard
editor: cgronlun
ms.assetid: 32c3ab2f-fdd7-48cc-b501-ad55c79b87dc
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: LuisCa
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d9c7f3c11e534f20bae157430a00724a2c839e8e


---
# <a name="azure-machine-learning-recommendations-api-documentation"></a>Azure Machine Learning Recommendations API ドキュメント
> [!NOTE]
> このバージョンの代わりに Recommendations API Cognitive Service の使用を開始することをお勧めします。 このサービスは Recommendations Cognitive Service に置き換えられ、新機能はすべて Cognitive Service で開発されるようになります。 Cognitive Service には、バッチ処理のサポート、API エクスプローラーの改善、API サーフェスの簡素化、より一貫性のあるサインアップおよび課金方法などの新機能が含まれています。
> 詳細については、「 [Migrating to the new Cognitive Service](http://aka.ms/recomigrate)
> 
> 

このドキュメントは、Microsoft Azure Machine Learning の Recommendations API を示しています。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="1-general-overview"></a>1.概要
このドキュメントは、API リファレンスです。 最初に、ドキュメント『Azure Machine Learning Recommendations – クイック スタート』をお読みください。

Azure Machine Learning の Recommendations API は、次の論理グループに分割できます。

* <ins>制限事項</ins> の Recommendations API の制限事項。
* <ins>一般的な情報</ins> - 認証、サービス URI、およびバージョン管理に関する情報。
* <ins>基本モデル</ins> - モデルの基本的な操作を可能にする API (例: モデルの作成、更新、削除)。
* <ins>高度なモデル</ins> - モデルの高度なデータ分析が取得できる API。
* <ins>ビジネス ルールのモデル</ins> - モデルの推奨結果に関するビジネス ルールを管理できる API。
* <ins>カタログ</ins> - モデルのカタログに関する基本操作を実行できる API。 カタログには、使用状況データ項目に関するメタ データ情報が含まれています。
* <ins>機能</ins> - 項目のインサイトをカタログに表示でき、この情報を使用してより良い推奨事項を構築する方法を有効にする API。
* <ins>使用状況データ</ins> - モデルの使用状況データに関する基本操作を実行できる API。 基本フォームの使用状況データは、&#60;userId&#62;,&#60;itemId&#62; のペアを含む行で構成されます。
* <ins>ビルド</ins> - モデルのビルドを起動し、このビルドに関連する基本操作を実行できる API。 モデルのビルドは、有効な使用状況データが存在する場合に起動できます。
* <ins>推奨</ins> - モデルのビルドの終了後に推奨を使用できる API。
* <ins>ユーザー データ</ins> - ユーザーの使用状況データの情報が取得できる API。
* <ins>通知</ins> - API の操作に関連する問題の通知を受信するための API。 (たとえば、データ取得により使用状況データを報告していて、イベント処理のほとんどが失敗する場合、 エラー通知が発生します)。

## <a name="2-limitations"></a>手順 2.制限事項
* サブスクリプションごとのモデルの最大数は 10 です。
* モデルごとのビルドの最大数は 20 です。
* カタログが保持できる項目の最大数は 100,000 です。
* 保持される使用状況ポイントの最大数は ~5,000,000 です。 新しいデータがアップロードまたは報告されると、最も古いデータが削除されます。
* POST で送信できるデータ (例: カタログ データのインポート、使用データのインポート) の最大サイズは 200 MB です。
* 推奨事項を取得するときに要求できる項目の最大数は 150 です。

## <a name="3-apis---general-information"></a>3.API の概要
### <a name="31-authentication"></a>3.1. 認証
認証に関する Microsoft Azure Marketplace のガイドラインに従ってください。 Marketplace は、Basic または OAuth のいずれかの認証方法をサポートします。

### <a name="32-service-uri"></a>3.2. サービス URI
Azure Machine Learning Recommendations API のサービス ルート URI は、 [ここ](https://api.datamarket.azure.com/amla/recommendations/v3/)

サービス URI はすべて、OData 仕様の要素を使用して表されます。  

### <a name="33-api-version"></a>3.3 API バージョン
各 API 呼び出しでは、最後に apiVersion というクエリ パラメーターを 1.0 に設定する必要があります。

### <a name="34-ids-are-case-sensitive"></a>3.4. ID には大文字小文字の区別がある
API のいずれかにより返される ID は大文字と小文字の区別があり、後続の API 呼び出しにパラメーターとして渡されるときにはそのとおりに使用する必要があります。 たとえば、モデル ID およびカタログ ID では、大文字と小文字が区別されます。

## <a name="4-recommendations-quality-and-cold-items"></a>4.推奨事項の品質とコールド項目
### <a name="41-recommendation-quality"></a>4.1. 推奨事項の品質
通常、システムで推奨事項を提供するには、推奨モデルを作成すれば十分です。 ただし、推奨事項の品質は、処理される使用状況とカタログの対象範囲に基づいて異なります。 たとえば、多数のコールド項目 (使用頻度の高くない項目) がある場合、システムがそのような項目の推奨事項を提供したり、そのような項目を推奨項目として使用したりすることは困難になります。 コールド項目の問題を防ぐために、システムでは項目のメタデータを使用して推奨事項を強化できます。 このメタデータのことを "特徴" と言います。 一般的な特徴には、本の著者や映画の俳優があります。 特徴はキーと値の文字列の形式のカタログを使用して提供されます。 カタログ ファイルの完全なフォーマットについては、「 [カタログ セクションのインポート](#81-import-catalog-data)」を参照してください。 

### <a name="42-rank-build"></a>4.2. 順位付けのビルド
特徴で推奨モデルを強化できますが、そのためには意味のある特徴を使用する必要があります。 この目的のため、順位付けのビルドという、新しいビルドが導入されました。 このビルドは、特徴の有用性を順位付けします。 意味のある特徴は、順位付けのスコアが 2 以上のものです。
どの特徴が意味があるのか理解したら、意味のある特徴のリスト (またはサブリスト) を使用して推奨事項のビルドをトリガーします。 これらの特徴を使用して、ウォーム項目とコールド項目の両方を強化できます。 ウォーム項目のために特徴を使用するには、 `UseFeatureInModel` ビルド パラメーターを設定する必要があります。 コールド項目のために特徴を使用するには、 `AllowColdItemPlacement` ビルド パラメーターを有効にする必要があります。
注: `UseFeatureInModel` を有効にせずに `AllowColdItemPlacement` を有効にすることはできません。

### <a name="43-recommendation-reasoning"></a>4.3. 推奨の理由
推奨の理由は、特徴の使用のもう 1 つの側面です。 実際、Azure Machine Learning Recommendations エンジンは推奨事項の説明 (理由) を提供するために特徴を使用でき、 これによって推奨項目は推奨事項のコンシューマーにとってより信頼できるものになります。
理由を有効にするには、推奨事項のビルドを要求する前に `AllowFeatureCorrelation` パラメーターと `ReasoningFeatureList` パラメーターをセットアップする必要があります。

## <a name="5-model-basic"></a>5.基本モデル
### <a name="51-create-model"></a>5.1. モデルの作成
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
  **注**: モデル ID は大文字小文字を区別します。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Create A New Model</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T06:35:21Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

### <a name="52-get-model"></a>5.2. モデルの取得
"モデルの取得" 要求を作成します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>例:<br>`<rootURI>/GetModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| id |モデルの一意識別子 (大文字小文字を区別する) |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

モデルのデータは、次の要素の下で見つかります。

* `feed/entry/content/properties/Id` - モデルの一意の ID。
* `feed/entry/content/properties/Name` - モデル名。
* `feed/entry/content/properties/Date` - モデルの作成日。
* `feed/entry/content/properties/Status` - モデルの状態。 次のいずれか:
  * Created - モデルは作成済みですが、カタログと使用状況が含まれていません。
  * ReadyForBuild - モデルは作成済みで、カタログと使用状況が含まれています。
* `feed/entry/content/properties/HasActiveBuild` - モデルが正常にビルドされたかどうかを示します。
* `feed/entry/content/properties/BuildId` - モデルのアクティブなビルド ID。
* `feed/entry/content/properties/Mpr` - モデルの平均パーセンタイル順位付け (MPR - 詳細については、ModelInsight を参照してください)。
* `feed/entry/content/properties/UserName` - モデルの内部ユーザー名。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Get A List Of All Models</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-28T14:35:51Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
        <d:Name m:type="Edm.String">vah-11111</d:Name>
        <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
        <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
        <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
        <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
        <d:Description m:type="Edm.String">short description</d:Description>
        <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
      </m:properties>
    </content>
      </entry>
    </feed>

### <a name="53----get-all-models"></a>5.3.    すべてのモデルの取得
現在のユーザーのすべてのモデルを取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetAllModels?apiVersion=%271.0%27`<br>例:<br>`<rootURI>/GetAllModels?apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

* `feed/entry/content/properties/Id` - モデルの一意の ID。
* `feed/entry/content/properties/Name` - モデル名。
* `feed/entry/content/properties/Date` - モデルの作成日。
* `feed/entry/content/properties/Status` - モデルの状態。 次のいずれか:
  * Created - モデルは作成済みですが、カタログと使用状況が含まれていません。
  * ReadyForBuild - モデルは作成済みで、カタログと使用状況が含まれています。
* `feed/entry/content/properties/HasActiveBuild` - モデルが正常にビルドされたかどうかを示します。
* `feed/entry/content/properties/BuildId` - モデルのアクティブなビルド ID。
* `feed/entry/content/properties/Mpr` - モデルの MPR (詳細については ModelInsight を参照してください)。
* `feed/entry/content/properties/UserName` - モデルの内部ユーザー名。
* `feed/entry/content/properties/UsageFileNames` - コンマで区切られたモデルの使用状況ファイルの一覧。
* `feed/entry/content/properties/CatalogId` - モデル カタログの ID。
* `feed/entry/content/properties/Description` - モデルの説明。
* `feed/entry/content/properties/CatalogFileName` - モデル カタログのファイル名。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get A List Of All Models</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-28T14:35:51Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
                    <d:Name m:type="Edm.String">vah-11111</d:Name>
                    <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
                    <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
                    <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
                    <d:BuildId m:type="Edm.String">-1</d:BuildId>
                    <d:Mpr m:type="Edm.String">0</d:Mpr>
                    <d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
                    <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
                    <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
                    <d:Description m:type="Edm.String">short description</d:Description>
                    <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
                </m:properties>
            </content>
        </entry>
    </feed>

### <a name="54----update-model"></a>5.4.    モデルの更新
モデルの説明またはアクティブなビルド ID を更新できます。<br>
<ins>アクティブなビルド ID</ins> - すべてのモデルのすべてのビルドには "ビルド ID" があります。 アクティブな "ビルド ID" は、新しいモデルそれぞれの最初の正常なビルドです。 アクティブなビルド ID があり、同じモデルに対して追加のビルドを実行する場合は、必要に応じて、既定のビルド ID として明示的に設定する必要があります。 推奨を使用する際に、使用するビルド ID を指定しないと、既定の ID が自動的に使用されます。<br>
このメカニズムにより、推奨モデルが運用環境にあるときに、新しいモデルを構築して、それを運用環境に移行する前にテストすることができます。

| HTTP メソッド | URI |
|:--- |:--- |
| PUT |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br>例:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| id |モデルの一意識別子 (大文字小文字を区別する) |
| apiVersion |1.0 |
|  | |
| 要求本文 |`<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`<Description>New Description</Description>`<br>`<ActiveBuildId>-1</ActiveBuildId>`<br>` </ModelUpdateParams>`<br><br>XML タグ Description と ActiveBuildId は省略可能です。 Description や ActiveBuildId を設定したくない場合は、タグ全体を削除します。 |

**応答**:

HTTP 状態コード: 200

### <a name="55----delete-model"></a>5.5.    モデルの削除
既存のモデルを ID で削除します。

| HTTP メソッド | URI |
|:--- |:--- |
| 削除 |`<rootURI>/DeleteModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>例:<br>`<rootURI>/DeleteModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| id |モデルの一意識別子 (大文字小文字を区別する) |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Delete Model by Id</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-28T10:39:33Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">DeleteModelByIdEntity</title>
    <updated>2014-10-28T10:39:33Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:string m:type="Edm.String"></d:string>
      </m:properties>
    </content>
      </entry>
    </feed>

## <a name="6-model-advanced"></a>6.高度なモデル
### <a name="61----model-data-insight"></a>6.1.    モデル データの分析
このモデルがビルドされた際の使用状況データに関する統計データを返します。

推奨事項のビルドでのみ使用できます。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetDataInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>例:<br>`<rootURI>/GetDataInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

データは、プロパティのコレクションとして返されます。

* `feed/entry/id/content/properties/key` - プロパティ名を保持します。
* `feed/entry/id/content/properties/value` - プロパティの値を保持します。

次の表は、各キーを表す値を示しています。

| キー | Description |
|:--- |:--- |
| AvgItemLength |項目ごとの別個のユーザーの平均数。 |
| AvgUserLength |ユーザーごとの別個の項目の平均数。 |
| DensificationNumberOfItems |モデル化できない項目を排除した後の項目数。 |
| DensificationNumberOfUsers |モデル化できないユーザーと項目を排除した後の使用状況ポイントの数。 |
| DensificationNumberOfRecords |モデル化できないユーザーと項目を排除した後の使用状況ポイントの数。 |
| MaxItemLength |最も人気のある項目の別個のユーザー数。 |
| MaxUserLength |ユーザーごとの別個の項目の最大数。 |
| MinItemLength |項目ごとの別個のユーザーの最大数。 |
| MinUserLength |ユーザーごとの別個の項目の最小数。 |
| RawNumberOfItems |使用状況ファイルのアイテムの数。 |
| RawNumberOfUsers |排除する前の使用状況ポイントの数。 |
| RawNumberOfRecords |排除する前の使用状況ポイントの数。 |
| SamplingNumberOfItems |該当なし |
| SamplingNumberOfRecords |該当なし |
| SamplingNumberOfUsers |該当なし |

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get data insight statistics</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgItemLength</d:Key>
        <d:Value m:type="Edm.String">18.936</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgUserLength</d:Key>
        <d:Value m:type="Edm.String">51.879</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfItems</d:Key>
        <d:Value m:type="Edm.String">2,000</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfRecords</d:Key>
        <d:Value m:type="Edm.String">37,872</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
        <m:properties>
            <d:Key m:type="Edm.String">DensificationNumberOfUsers</d:Key>
            <d:Value m:type="Edm.String">730</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MaxItemLength</d:Key>
                <d:Value m:type="Edm.String">4,704</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MaxUserLength</d:Key>
                <d:Value m:type="Edm.String">190</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MinItemLength</d:Key>
                <d:Value m:type="Edm.String">8</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MinUserLength</d:Key>
                <d:Value m:type="Edm.String">20</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">RawNumberOfItems</d:Key>
                <d:Value m:type="Edm.String">2,000</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">RawNumberOfRecords</d:Key>
                <d:Value m:type="Edm.String">72,022</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">RawNumberOfUsers</d:Key>
                <d:Value m:type="Edm.String">9,044</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">SampelingNumberOfItems</d:Key>
                <d:Value m:type="Edm.String">2,000</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">SampelingNumberOfRecords</d:Key>
                <d:Value m:type="Edm.String">72,022</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">SampelingNumberOfUsers</d:Key>
                <d:Value m:type="Edm.String">9,044</d:Value>
            </m:properties>
        </content>
    </entry>
    </feed>

### <a name="62----model-insight"></a>6.2.    モデルの分析
アクティブなビルドまたは特定のビルド (指定した場合) のモデルの分析を返します。

推奨事項のビルドでのみ使用できます。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |アクティブなビルド ID の場合:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetModelInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>特定のビルド ID の場合:<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| buildId |省略可能 - 成功したビルドを識別する数値。 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

データは、プロパティのコレクションとして返されます。

* `feed/entry/id/content/properties/key`
* `feed/entry/id/content/properties/value`

次の表は、各キーを表す値を示しています。

| キー | Description |
|:--- |:--- |
| CatalogCoverage |使用状況のパターンを使用してモデル化できるカタログの部分。 項目の残りの部分は、内容に基づく特徴でなければなりません。 |
| Mpr |モデルの平均パーセンタイル順位付け。 小さいほど良好です。 |
| NumberOfDimensions |行列因子分解アルゴリズムによって使用されるディメンションの数。 |

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get model insight statistics</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-27T14:27:11Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">GetModelInsightStatisticsEntity</title>
        <updated>2014-10-27T14:27:11Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">CatalogCoverage</d:Key>
                <d:Value m:type="Edm.String">1.000</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetModelInsightStatisticsEntity</title>
        <updated>2014-10-27T14:27:11Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">Mpr</d:Key>
                <d:Value m:type="Edm.String">0.367</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">GetModelInsightStatisticsEntity</title>
        <updated>2014-10-27T14:27:11Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">NumberOfDimensions</d:Key>
                <d:Value m:type="Edm.String">20</d:Value>
            </m:properties>
        </content>
    </entry>
    </feed>

### <a name="63----get-model-sample"></a>6.3.    モデルのサンプルの取得
推奨モデルのサンプルを取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetModelSample?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>例:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>特定のビルド ID の場合:<br>`<rootURI>/GetModelSample?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`<br>例:<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&buildId=%271500068%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

OData XML

応答は未加工のテキスト形式で返されます。

<pre>
Level 1 --------------- 655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel 655fc955-a5a3-4a26-9723-3090859cb27b, Prey: A Novel Rating: 0.5215 3f471802-f84f-44a0-99c8-6d2e7418eec1, Black Hawk Down: A Story of Modern War Rating: 0.5151 07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon Rating: 0.5148 6afc18e4-8c2a-43d1-9021-57543d6b11d8, Imajica Rating: 0.5146 e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List Rating: 0.514 56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai 56b61441-0eed-46cc-a8f6-112775b81892, Life and Death in Shanghai Rating: 0.5218 53156702-cc0c-443d-b718-6fb74b2491d3, Son of \ Rating: 0.5212 fb8cf7a6-8719-46ee-97d4-92f931d77a3a, Smoke and Mirrors: Short Fictions and Illusions Rating: 0.5188 8f5fe006-79e4-4679-816b-950989d1db4b, A Place I've Never Been (Contemporary American Fiction) Rating: 0.5156 d8db4583-cc0f-49ce-bc95-b7fa3491623f, Happiness: A Novel Rating: 0.5156 50471eec-9aeb-4900-84d7-21567ab18546, If the Buddha Dated: A Handbook for Finding Love on a Spiritual Path cfe922a1-7ca0-4f8d-ad9d-b7cc87bfe0ef, Divine Secrets of the Ya-Ya Sisterhood: A Novel Rating: 0.5266 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5252 973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Rating: 0.5244 e2cbf7ad-0636-4117-8b30-298da6df7077, Animal Dreams Rating: 0.5227 6c818fd3-5a09-417d-9ab4-7ffe090f0fef, Confessions of an Ugly Stepsister: A Novel Rating: 0.5222 5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club) 5e97148f-defb-4d74-af2d-80f4763bf531, The Deep End of the Ocean (Oprah's Book Club) Rating: 0.537 5dcbac37-2946-4f2a-a0b3-bbe710f9409a, Up Island: A Novel Rating: 0.5277 bc5b69db-733b-4346-adde-3927544258f7, Downtown Rating: 0.5275 31fe5c63-3e5a-48d0-802b-d3b0f989a634, Have a Nice Day: A Tale of Blood and Sweatsocks Rating: 0.5252 0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea Rating: 0.5238 68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived 68f97068-ae1a-4163-9e94-396b800b743d, Modoc: The True Story of the Greatest Elephant That Ever Lived Rating: 0.5379 6724862e-e4e7-4022-9614-1468d8b902ff, Little House on the Prairie Rating: 0.5345 cdedb837-1620-496d-94c4-6ccfed888320, Little House in the Big Woods Rating: 0.5325 382164ba-406b-4187-b726-d7a54b9d790d, The Tao of Pooh Rating: 0.5309 6a068d6a-bb74-4ba3-b3f2-a956c4f9d1b5, On the Banks of Plum Creek Rating: 0.5285 37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships 37ef8e74-e348-44e5-aabc-1d7f9efcb25b, Men Are from Mars, Women Are from Venus: A Practical Guide for Improving Communication and Getting What You Want in Your Relationships Rating: 0.5397 f2be16d4-5faf-4d32-ab83-7ba74d29261e, Politically Correct Bedtime Stories: Modern Tales for Our Life and Times Rating: 0.5207 ef732c5c-334b-4d6b-ab82-7255eb7286d0, Honor Among Thieves Rating: 0.5195 0b209b8c-7cdd-47fd-b940-05c7ff7c60fc, The Giving Tree Rating: 0.5194 883b360f-8b42-407f-b977-2f44ad840877, Scary Stories to Tell in the Dark: Collected from American Folklore (Scary Stories) Rating: 0.5184 ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball d008dae9-c73a-40a1-9a9b-96d5cf546f36, The Gulag Archipelago 1918-1956: An Experiment in Literary Investigation I-II Rating: 0.5416 ff51b67e-fa8e-4c5e-8f4d-02a928de735d, Men at Work: The Craft of Baseball Rating: 0.5403 49dec30e-0adb-411a-b186-48eaabf6f8bc, Fatherland Rating: 0.5394 cc7964fd-d30f-478e-a425-93ddbdf094ed, Magic the Gathering: Arena Vol. 1 Rating: 0.5379 8a1e9f36-97af-4614-bed9-24e3940a05f3, More Sniglets: Any Word That Doesn't Appear in the Dictionary but Should Rating: 0.5377 12a6d988-be21-4a09-8143-9d5f4261ba16, A Dream of Eagles 07b10e28-9e7c-4032-90b7-10acab7f2460, Cryptonomicon Rating: 0.5417 e4cc5e69-3567-43ab-b00f-f0d8d0506870, Hit List Rating: 0.5416 1f1a34c4-9781-49f5-a3cc-acec3ae3c71d, The Family Rating: 0.5371 56daeffe-7d48-43cd-8ef8-7dffd0c103d3, Kilo Class Rating: 0.5366 b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender Rating: 0.5366 df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish 56d33036-dfda-46b9-8e2a-76cb03921bb0, The X-Files: Ground Zero Rating: 0.5417 0780cde8-6529-4e1d-b6c6-082c1b80e596, Twelve Red Herrings Rating: 0.5416 df87525b-e435-4bd6-8701-4e60ad344e28, Finding Fish Rating: 0.5408 400fe331-2c35-490c-adbc-b28b4b73d56c, Shall We Tell the President? Rating: 0.5383 f86ad7d0-5c03-42b3-aebf-13d44aec8b30, Shades of Grace Rating: 0.5358 de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology de1f62a4-89e6-44d2-aaee-992a4bf093f1, The Map That Changed the World: William Smith and the Birth of Modern Geology Rating: 0.5422 b303538f-e2c6-4a2c-b425-8d21e684fc3e, My Uncle Oswald Rating: 0.5385 34b84627-48af-4a4c-96c4-b26fb3863f56, Midnight In the Garden of Good and Evil Rating: 0.5379 306cbaa7-b1a8-4142-9d55-e11b5018a7a8, The Street Lawyer Rating: 0.5376 e53b4baa-8c09-45c4-95c0-b6a26b98770b, Miss Smillas Feeling for Snow Rating: 0.5367

<a name="level-2"></a>Level 2
---------------
352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony) 352aaea1-6b12-454d-a3d5-46379d9e4eb2, The Sinister Pig (Hillerman Tony) Rating: 0.5425 74c49398-bc10-4af5-a658-a996a1201254, Children of the Storm (Peters Elizabeth) Rating: 0.5387 9ba80080-196e-43fd-8025-391d963f77e7, The Floating Girl Rating: 0.5372 e68f81d5-7745-4cc7-b943-fedb8fcc2ced, Killer Smile (Scottoline Lisa) Rating: 0.5353 b2fe511e-5cb9-4a56-b823-2801e63e6a96, Legal Tender Rating: 0.5332 c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days 0adf981a-b65b-4c11-b36b-78aca2f948a2, The Perfect Storm: A True Story of Men Against the Sea Rating: 0.5433 c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5, Lake Wobegon days Rating: 0.543 a00ae6ad-4a7f-4211-9836-75ce8834eb11, Sniglets (Snig'lit: Any Word That Doesn't Appear in the Dictionary But Should) Rating: 0.5327 6f6e192e-0d64-49ca-9b63-f09413ea1ee6, Politically Correct Holiday Stories: For an Enlightened Yuletide Season Rating: 0.5307 798051a8-147d-4d46-b0dc-e836325029e6, AGE OF INNOCENCE (MOVIE TIE-IN) Rating: 0.5301 73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics) cba8163f-6536-436b-8130-47b4a43c827f, Trust No One (The Official Guide to the X-Files Vol. 2) Rating: 0.5434 5708e4cb-2492-49c0-94a8-cc413eec5d89, Small Gods (Discworld Novels (Paperback)) Rating: 0.5406 73f3e25a-e996-4162-9ed8-ff3d34075650, O Pioneers! (Penguin Twentieth-Century Classics) Rating: 0.5403 d885b0bd-ae4b-452d-bdf2-faa90197dbc9, The Color of Magic Rating: 0.539 b133a9c4-4784-4db3-b100-d0d6dffb94d2, The Truth Is Out There (The Official Guide to the X-Files Vol. 1) Rating: 0.5367 271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings 271700a5-854a-4d5a-8409-6b57a5ee4de4, Fluke: Or I Know Why the Winged Whale Sings Rating: 0.5445 2de1c354-90ff-47c5-a0db-1bad7d88ef94, The Salaryman's Wife (Children of Violence Series) Rating: 0.5329 d279416e-19c0-43f8-9ec9-a585947879ca, Zen Attitude Rating: 0.5316 c8f854d7-3de3-4b23-8217-f4f851670fd4, Revenge of the Cootie Girls: A Robin Hudson Mystery (Robin Hudson Mysteries (Paperback)) Rating: 0.5305 8ef4751c-7074-409e-a3ac-d49b222fc864, Where the Wild Things Are Rating: 0.5289 9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God 9ad1b620-0a7b-4543-8673-66d4c3bcb2f1, Their Eyes Were Watching God Rating: 0.5446 da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees Rating: 0.5389 65ecbdd1-131c-40c3-a3d6-d86ca281377a, The God of Small Things Rating: 0.5387 c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries Rating: 0.5355 973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Rating: 0.5344 5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback)) 5f17d90a-2604-4fe8-8977-1a280b9098b1, One for the Money (Stephanie Plum Novels (Paperback)) Rating: 0.5446 57169b2b-9a8a-486b-9aac-1ed98ce57168, Final Appeal Rating: 0.5332 efcb1bc4-7278-4a8f-b491-befde02070d6, Moment of Truth Rating: 0.5329 1efa91a2-993b-4c43-9f5c-3454fc12612d, Burn Factor Rating: 0.5309 24c59962-458a-4ec8-b95d-d694e861919c, At Home in Mitford (The Mitford Years) Rating: 0.5303 4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl 4fd48c46-1a20-4c57-bc7f-a02ef123dc52, As Nature Made Him: The Boy Who Was Raised As a Girl Rating: 0.5449 cd5f2c03-20cb-43be-a1fb-3b4233e63222, Pigs in Heaven Rating: 0.5329 19985fdb-d07a-4a25-ae4a-97b9cb61e5d1, Love in the Time of Cholera (Penguin Great Books of the 20th Century) Rating: 0.5267 15689d09-c711-4844-84d8-130a90237b26, Bel Canto Rating: 0.5245 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5235 98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories f874b5a3-5d40-4436-94ff-0fa1c090ddf5, The Sun Also Rises (A Scribner classic) Rating: 0.5451 98df28ec-41e7-4fca-b77f-8b0d3109085d, Star Trek Memories Rating: 0.5442 0ce0014a-9a48-4013-a08a-7f2c11877930, H.M.S. Unseen Rating: 0.5421 15316ca6-1e38-425f-893d-691944a47000, More Scary Stories To Tell In The Dark Rating: 0.5409 329d5682-3dc3-4206-8aa2-eef4b1032258, Letters from the Earth Rating: 0.54 5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover)) 5b9445d5-c072-419c-8d49-6f669bb1b0a9, Daughter of Fortune: A Novel (Oprah's Book Club (Hardcover)) Rating: 0.5462 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5372 604eb3bd-6026-4f51-bffd-9fb54f180400, Family Pictures: A Novel Rating: 0.5341 8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) Rating: 0.5334 da45c4d5-aba1-413b-a9bd-50df98b1e1d2, The Bean Trees Rating: 0.5319 d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven d5358189-d70f-4e35-8add-34b83b4942b3, Pigs in Heaven Rating: 0.5491 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745, The Poisonwood Bible: A Novel Rating: 0.5401 c78743bf-7947-4a0c-8db7-8a3bfe69ba70, The Stone Diaries Rating: 0.5393 8d06d01d-31cd-4678-b6b1-140a67987ce9, Songs in Ordinary Time (Oprah's Book Club (Paperback)) Rating: 0.5382 973f8cbd-0846-4f6b-9d28-4dd0d7dc3a19, Pigs in Heaven Rating: 0.5367

</pre>


## <a name="7-model-business-rules"></a>7.ビジネス ルールのモデル
サポートされるルールの種類は次のとおりです。

* <strong>BlockList</strong> – BlockList では、推奨事項の結果を返すときに除外する項目の一覧を指定できます。 
* <strong>FeatureBlockList</strong> – Feature BlockList では、機能の値に基づいて項目をブロックできます。

*1 つの BlockList ルールで 1,000 個以上の項目を送信しないでください。呼び出しがタイムアウトになる場合があります。1000 個を超える項目をブロックする必要がある場合は、複数の BlockList 呼び出しを行うことができます。*

* <strong>Upsale</strong> - Upsale では項目を強制的に推奨事項の結果の一部として返すことができます。
* <strong>WhiteList</strong> – White List では、項目の一覧から推奨事項だけを提案できます。
* <strong>FeatureWhiteList</strong> – Feature White List では、特定の機能値を持つ項目だけを推奨できます。
* <strong>PerSeedBlockList</strong> -PerSeedBlockList では、推奨事項の結果として返すことができない項目の一覧を項目ごとに指定できます。

### <a name="71----get-model-rules"></a>7.1.    モデルのルールの取得
| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetModelRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>例:<br>`<rootURI>/GetModelRules?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

* `feed/entry/content/properties/Id` - このルールの一意識別子。
* `feed/entry/content/properties/Type` - ルールの種類。
* `feed/entry/content/properties/Parameter` - ルールのパラメーター。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get a list of rules for a model</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T12:58:57Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">GetAListOfRulesForAModelEntity</title>
        <updated>2014-11-05T12:58:57Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">1000043</d:Id>
                <d:Type m:type="Edm.String">BlockList</d:Type>
                <d:Parameters m:type="Edm.String">{"ItemsToExclude":["1000"]}</d:Parameters>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetAListOfRulesForAModelEntity</title>
        <updated>2014-11-05T12:58:57Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">1000044</d:Id>
                <d:Type m:type="Edm.String">BlockList</d:Type>
                <d:Parameters m:type="Edm.String">{"ItemsToExclude":["1001"]}</d:Parameters>
            </m:properties>
        </content>
    </entry>
    </feed>

### <a name="72----add-rule"></a>7.2.    ルールの追加
| HTTP メソッド | URI |
|:--- |:--- |
| POST |`<rootURI>/AddRule?apiVersion=%271.0%27` |
| ヘッダー |`"Content-Type", "text/xml"` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| apiVersion |1.0 |
|  | |
| 要求本文 | |

<ins>ビジネス ルールの項目 ID を指定する場合は、必ず項目の外部 ID (カタログ ファイルで使用したのと同じ ID) を使用します</ins><br>
<ins>BlockList ルールを追加するには:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>BlockList</Type><Value>{"ItemsToExclude":["2406E770-769C-4189-89DE-1C9283F93A96","3906E110-769C-4189-89DE-1C9283F98888"]}</Value></ApiFilter>`<br><br><ins>
<ins>FeatureBlockList ルールを追加するには:</ins><br>
<br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>FeatureBlockList</Type><Value>{"Name":"Movie_category","Values":["Adult","Drama"]}</Value></ApiFilter>`<br><br><ins>Upsale ルールを追加するには:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>Upsale</Type><Value>{"ItemsToUpsale":["2406E770-769C-4189-89DE-1C9283F93A96"],"NumberOfItemsToUpsale":5}</Value></ApiFilter>`<br><br>
<ins>WhiteList ルールを追加するには:</ins><br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>WhiteList</Type><Value>{"ItemsToInclude":["2406E770-769C-4189-89DE-1C9283F93A96","1116E770-769C-4189-89DE-1C9283F88888"]}</Value></ApiFilter>`<br><br><ins>
<ins>FeatureWhiteList ルールを追加するには:</ins><br>
<br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>FeatureWhiteList</Type><Value>{"Name":"Movie_rating","Values":["PG13"]}</Value></ApiFilter>`<br><br><ins>PerSeedBlockList ルールを追加するには:</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>PerSeedBlockList</Type><Value>{"SeedItems":["9949"],"ItemsToExclude":["9862","8158","8244"]}</Value></ApiFilter>`|

**応答**:

HTTP 状態コード: 200

API は、新しく作成されたルールとその詳細を返します。 ルールのプロパティは、次のパスから取得できます。

* `feed/entry/content/properties/Id` - このルールの一意識別子。
* `feed/entry/content/properties/Type` - ルールのタイプ。BlockList または Upsale。
* `feed/entry/content/properties/Parameter` - ルールのパラメーター。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Add A Rule</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T11:13:28Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">AddARuleEntity</title>
        <updated>2014-11-05T11:13:28Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">1000041</d:Id>
                <d:Type m:type="Edm.String">BlockList</d:Type>
                <d:Parameters m:type="Edm.String">{"ItemsToExclude":["1002"]}</d:Parameters>
            </m:properties>
        </content>
    </entry>
    </feed>

### <a name="73----delete-rule"></a>7.3.    ルールの削除
| HTTP メソッド | URI |
|:--- |:--- |
| 削除 |`<rootURI>/DeleteRule?modelId=%27<model_id>%27&filterId=%27<filter_Id>%27&apiVersion=%271.0%27`<br><br>例:<br>`DeleteRule?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&filterId=%271000011%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| filterId |フィルターの一意識別子 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

### <a name="74----delete-all-rules"></a>7.4.    すべてのルールの削除
| HTTP メソッド | URI |
|:--- |:--- |
| 削除 |`<rootURI>/DeleteAllRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>例:<br>`DeleteAllRules?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

## <a name="8-catalog"></a>8.カタログ
### <a name="81----import-catalog-data"></a>8.1.    カタログ データのインポート
複数の呼び出しで同じモデルに複数のカタログ ファイルがアップロードされた場合は、新しいカタログ項目のみを挿入します。 既存の項目は、元の値でそのまま残ります。 このメソッドを使用して、カタログ データを更新することはできません。

カタログ データは、次の形式に従う必要があります。

* 特徴なし - `<Item Id>,<Item Name>,<Item Category>[,<Description>]`
* 特徴あり - `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

注: ファイルの最大サイズは、200 MB です。

** 形式の詳細 **

| Name | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| 項目 ID |はい |[A-z]、[a-z]、[0-9]、[_] (アンダースコア)、[-] (ダッシュ) <br> 最大長: 50 |項目の一意識別子 |
| Item Name |はい |任意の英数字<br>  最大長: 255 |項目名。 |
| Item Category |はい |任意の英数字 <br>  最大長: 255 |この項目が属しているカテゴリ (例: 料理本、ドラマ...)。空にすることができます。 |
| Description |いいえ。ただし特徴が存在する場合を除きます (しかし、空にすることはできます) |任意の英数字 <br>  最大長: 4000 |この項目の説明。 |
| Features list |なし |任意の英数字 <br>  最大長: 4000、特徴の最大数: 20 |モデルの推奨事項を強化するために使用できる "特徴名=特徴値" のコンマ区切りの一覧。[高度なトピック](#2-advanced-topics)のセクションをご覧ください。 |

| HTTP メソッド | URI |
|:--- |:--- |
| POST |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27` |
| ヘッダー |`"Content-Type", "text/xml"` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| filename |カタログを表すテキスト形式の識別子。<br>英字 (A～Z、a～z)、数字 (0～9)、ハイフン (-)、アンダースコア (_) のみが許可されます。<br>最大長: 50 |
| apiVersion |1.0 |
|  | |
| 要求本文 |例 (特徴あり):<br/>2406e770-769c-4189-89de-1c9283f93a96,Clara Callan,Book,the book  description,author=Richard Wright,publisher=Harper Flamingo Canada,year=2001<br>21bf8088-b6c0-4509-870c-e1c7ac78304a,The Forgetting Room: A Fiction (Byzantium Book),Book,,author=Nick Bantock,publisher=Harpercollins,year=1997<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23,Spadework,Book,,author=Timothy Findley, publisher=HarperFlamingo Canada, year=2001<br>552a1940-21e4-4399-82bb-594b46d7ed54,Restraint of Beasts,Book,the book description,author=Magnus Mills, publisher=Arcade Publishing, year=1998</pre> |

**応答**:

HTTP 状態コード: 200

この API では、インポートのレポートが返されます。

* `feed\entry\content\properties\LineCount` – 受け入れられる行数。
* `feed\entry\content\properties\ErrorCount` – エラーのために挿入されなかった行数。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Import catalog file</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
    <entry>
       <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">ImportCatalogFileEntity2</title>
        <updated>2014-10-05T06:58:04Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:LineCount m:type="Edm.String">4</d:LineCount>
                <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
            </m:properties>
        </content>
    </entry>
    </feed>

### <a name="82----get-catalog"></a>8.2.    カタログの取得
すべてのカタログ項目を取得します。
カタログは一度に 1 ページずつ取得されます。 特定のインデックスで項目を取得する場合は、$skip odata パラメーターを使用します。 たとえば、位置 100 から開始する項目を取得する場合、要求にパラメーターとして $skip=100 を追加します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetCatalog?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>例:<br>`GetCatalog?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

応答には、カタログ項目ごとに 1 つのエントリが含まれています。 各エントリには、次のデータが含まれています。

* `feed/entry/content/properties/ExternalId` - カタログ項目の外部 ID。顧客が指定したカタログ項目。
* `feed/entry/content/properties/InternalId` - カタログ項目の内部 ID。Azure Machine Learning Recommendations が生成したカタログ項目。
* `feed/entry/content/properties/Name` - カタログ項目の名前。
* `feed/entry/content/properties/Category` - カタログ項目のカテゴリ。
* `feed/entry/content/properties/Description` - カタログ項目の説明。
* `feed/entry/content/properties/Metadata` - カタログ項目のメタデータ。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get All Catalog Items</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">552A1940-21E4-4399-82BB-594B46D7ED54</d:ExternalId>
                <d:InternalId m:type="Edm.String">060db26a-e6a6-464e-bb52-436d2da82a50</d:InternalId>
                <d:Name m:type="Edm.String">Restraint of Beasts</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:ExternalId>
                <d:InternalId m:type="Edm.String">209d7bfe-2eb9-4455-92a3-7c867a41a74a</d:InternalId>
                <d:Name m:type="Edm.String">Clara Callan</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">3BB5CB44-D143-4BDD-A55C-443964BF4B23</d:ExternalId>
                <d:InternalId m:type="Edm.String">913ff79b-059b-4d4d-8042-6fa4cc1391dd</d:InternalId>
                <d:Name m:type="Edm.String">Spadework</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">21BF8088-B6C0-4509-870C-E1C7AC78304A</d:ExternalId>
                <d:InternalId m:type="Edm.String">ea65e4fa-768c-40b4-92c3-69d3e8178691</d:InternalId>
                <d:Name m:type="Edm.String">The Forgetting Room: A Fiction (Byzantium Book)</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    </feed>

### <a name="83----get-catalog-items-by-token"></a>8.3.    トークンによるカタログ項目の取得
| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetCatalogItemsByToken?modelId=%27<modelId>%27&token=%27<token>%27&apiVersion=%271.0%27`<br><br>例:<br>`GetCatalogItemsByToken?modelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&token=%27Cla%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| token |カタログ項目の名前のトークン。 3 文字以上にする必要があります。 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

応答には、カタログ項目ごとに 1 つのエントリが含まれています。 各エントリには、次のデータが含まれています。

* `feed/entry/content/properties/InternalId` - カタログ項目の内部 ID。Azure Machine Learning Recommendations が生成したカタログ項目。
* `feed/entry/content/properties/Name` - カタログ項目の名前。
* `feed/entry/content/properties/Rating` - (将来的に使用)
* `feed/entry/content/properties/Reasoning` - (将来的に使用)
* `feed/entry/content/properties/Metadata` - (将来的に使用)
* `feed/entry/content/properties/FormattedRating` - (将来的に使用)

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get Catalog items that contain a token</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-29T11:48:19Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">CatalogItemsThatContainATokenEntity</title>
            <updated>2014-10-29T11:48:19Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                  <m:properties>
                    <d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
                    <d:Name m:type="Edm.String">Clara Callan</d:Name>
                    <d:Rating m:type="Edm.Double">0</d:Rating>
                    <d:Reasoning m:type="Edm.String"></d:Reasoning>
                    <d:Metadata m:type="Edm.String"></d:Metadata>
                    <d:FormattedRating m:type="Edm.Double" m:null="true"></d:FormattedRating>
                  </m:properties>
            </content>
        </entry>
    </feed>

## <a name="9-usage-data"></a>9.使用状況データ
### <a name="91----import-usage-data"></a>9.1.    使用状況データのインポート
#### <a name="911-uploading-file"></a>9.1.1. ファイルのアップロード
このセクションでは、ファイルを使用して使用状況データをアップロードする方法を示します。 この API は使用状況データと共に何度も呼び出すことができます。 すべての呼び出しですべての使用状況データが保存されます。

| HTTP メソッド | URI |
|:--- |:--- |
| POST |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| filename |カタログを表すテキスト形式の識別子。<br>英字 (A～Z、a～z)、数字 (0～9)、ハイフン (-)、アンダースコア (_) のみが許可されます。<br>最大長: 50 |
| apiVersion |1.0 |
|  | |
| 要求本文 |使用状況データ。 形式:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>名前</th><th>必須</th><th>型</th><th>Description</th></tr><tr><td>ユーザー ID</td><td>はい</td><td>[A-z]、[a-z]、[0-9]、[_] (アンダースコア)、[-] (ダッシュ) <br>  最大長: 255 </td><td>ユーザーの一意識別子。</td></tr><tr><td>項目 ID</td><td>はい</td><td>[A-z]、[a-z]、[0-9]、[_] (アンダースコア)、[-] (ダッシュ) <br> 最大長: 50</td><td>項目の一意識別子</td></tr><tr><td>Time</td><td>いいえ</td><td>YYYY/MM/DDTHH:MM:SS 形式の日付 (例: 2013/06/20T10:00:00)</td><td>データの時間。</td></tr><tr><td>イベント</td><td>いいえ、指定した場合は日付も指定すること</td><td>次のいずれか:<br>• Click<br>• RecommendationClick<br>•    AddShopCart<br>• RemoveShopCart<br>• Purchase</td><td></td></tr></table><br>ファイルの最大サイズ: 200 MB<br><br>例:<br><pre>149452,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>6360,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>50321,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>71285,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>224450,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>236645,1b3d95e2-84e4-414c-bb38-be9cf461c347<br>107951,1b3d95e2-84e4-414c-bb38-be9cf461c347</pre> |

**応答**:

HTTP 状態コード: 200

* `Feed\entry\content\properties\LineCount` – 受け入れられる行数。
* `Feed\entry\content\properties\ErrorCount` – エラーのために挿入されなかった行数。
* `Feed\entry\content\properties\FileId` – ファイル識別子。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Add bulk usage data (usage file)</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T07:21:44Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
      </entry>
    </feed>


#### <a name="912-using-data-acquisition"></a>9.1.2. データ取得の使用
このセクションでは、Web サイトから Azure Machine Learning Recommendations にリアルタイムでイベントを送信する方法を示します。

| HTTP メソッド | URI |
|:--- |:--- |
| POST |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27` |
| ヘッダー |`"Content-Type", "text/xml"` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| apiVersion |1.0 |
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
                    <PurchaseItem>
                        <ItemId>ABBF8081-C5C0-4F09-9701-E1C7AC78304A</ItemId>
                        <Count>1</Count>
                    </PurchaseItem>
                    <PurchaseItem>
                        <ItemId>21BF8088-B6C0-4509-870C-11C0AC7F304B</ItemId>
                        <Count>3</Count>
                    </PurchaseItem>
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

### <a name="92----list-model-usage-files"></a>9.2.    モデルの使用状況ファイルの一覧
すべてのモデルに関する使用状況ファイルのメタデータを取得します。
使用状況ファイルは、一度に 1 ページずつ取得されます。 各ページに 100 個の項目が含まれます。 特定のインデックスで項目を取得する場合は、$skip odata パラメーターを使用します。 たとえば、位置 100 から開始する項目を取得する場合、要求にパラメーターとして $skip=100 を追加します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/ListModelUsageFiles?forModelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/ListModelUsageFiles?forModelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| forModelId |モデルの一意識別子 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

応答には、使用状況ファイルごとに 1 つのエントリが含まれています。 各エントリには、次のデータが含まれています。

* `feed\entry\content\properties\Id` - 使用状況ファイルの ID。
* `feed\entry\content\properties\Length` - 使用状況ファイルの長さ (MB)。
* `feed\entry\content\properties\DateModified` - 使用状況ファイルが作成された日付。
* `feed\entry\content\properties\UseInModel` - 使用状況ファイルがモデルで使用されるかどうか。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get a list of model's usage files info</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-30T09:40:25Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
            <updated>2014-10-30T09:40:25Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Id m:type="Edm.String">4c067b42-e975-4cb2-8c98-a6ab80ed6d63</d:Id>
                    <d:Length m:type="Edm.Double">0</d:Length>
                    <d:DateModified m:type="Edm.String">10/30/2014 9:19:57 AM</d:DateModified>
                    <d:UseInModel m:type="Edm.String">true</d:UseInModel>
                </m:properties>
            </content>
        </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
        <updated>2014-10-30T09:40:25Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">3126d816-4e80-4248-8339-1ebbdb9d544d</d:Id>
                <d:Length m:type="Edm.Double">0.001</d:Length>
                <d:DateModified m:type="Edm.String">10/30/2014 9:21:44 AM</d:DateModified>
                <d:UseInModel m:type="Edm.String">true</d:UseInModel>
              </m:properties>
        </content>
    </entry>
</feed>

### <a name="93----get-usage-statistics"></a>9.3.    使用状況の統計の取得
使用状況の統計を取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetUsageStatistics?modelId=%27<modelId>%27& startDate=%27<date>%27&endDate=%27<date>%27&eventTypes=%27<types>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetUsageStatistics?modelId=%271d20c34f-dca1-4eac-8e5d-f299e4e4ad66%27&startDate=%272014%2F10%2F17T00%3A00%3A00%27&endDate=%272014%2F11%2F16T00%3A00%3A00%27&eventTypes=%271%2C2%2C3%2C4%2C5%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| startDate |開始日。 形式: yyyy/MM/ddTHH:mm:ss |
| endDate |終了日。 形式: yyyy/MM/ddTHH:mm:ss |
| eventTypes |イベント種類のコンマ区切りの文字列。すべてのイベントを取得する場合は null 値。 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

キーと値の要素のコレクション。 各コレクションには、時間別にグループ化された特定のイベントの種類のイベント数の合計が含まれます。

* `feed\entry[i]\content\properties\Key` - 時間 (時間別にグループ化) と、イベントの種類が含まれています。
* `feed\entry[i]\content\properties\Value` - 合計イベント数。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get usage statistics</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-18T11:39:16Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;Click</d:Event>
                <d:Value m:type="Edm.String">5</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;RecommendationClick</d:Event>
                <d:Value m:type="Edm.String">10</d:Value>
              </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/1/2014 8:00:00 AM;RemoveShopCart</d:Event>
                <d:Value m:type="Edm.String">10</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/5/2014 8:00:00 AM;RemoveShopCart</d:Event>
                <d:Value m:type="Edm.String">10</d:Value>
            </m:properties>
        </content>
    </entry>
    </feed>

### <a name="94----get-usage-file-sample"></a>9.4.    使用状況ファイルのサンプルの取得
使用状況ファイルの内容の最初の 2 KB を取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetUsageFileSample?modelId=%27<modelId>%27& fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetUsageFileSample?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fileId=%274c067b42-e975-4cb2-8c98-a6ab80ed6d63%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| フィールド |モデルの使用状況ファイルの一意識別子。 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

応答は未加工のテキスト形式で返されます。

<pre>
85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1
</pre>


### <a name="95----get-model-usage-file"></a>9.5.    モデルの使用状況ファイルの取得
使用状況ファイルのすべての内容を取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetModelUsageFile?mid=%27<modelId>%27& fid=%27<fileId>%27&download=%27<download_value>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetModelUsageFile?mid=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fid=%273126d816-4e80-4248-8339-1ebbdb9d544d%27&download=%271%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| mid |モデルの一意識別子 |
| fid |モデルの使用状況ファイルの一意識別子。 |
| ダウンロード |1 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

応答は未加工のテキスト形式で返されます。

<pre>
85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 244881,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 50547,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 213090,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 260655,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 72214,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 36326,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189336,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260655,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 162100,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 54946,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260965,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 102758,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 112602,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 163925,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 262998,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 144717,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1
</pre>

### <a name="96----delete-usage-file"></a>9.6.    使用状況ファイルの削除
指定されたモデルの使用状況ファイルを削除します。

| HTTP メソッド | URI |
|:--- |:--- |
| 削除 |`<rootURI>/DeleteUsageFile?modelId=%27<modelId>%27&fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/DeleteUsageFile?modelId=%270f86d698-d0f4-4406-a684-d13d22c47a73%27&fileId=%27f2e0b09d-be5c-46b2-9ac2-c7f622e5e1a5%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| フィールド |削除するファイルの一意識別子 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

### <a name="97----delete-all-usage-files"></a>9.7.    すべての使用状況ファイルの削除
すべてのモデルの使用状況ファイルを削除します。

| HTTP メソッド | URI |
|:--- |:--- |
| 削除 |`<rootURI>/DeleteAllUsageFiles?modelId=%27<modelId>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/DeleteAllUsageFiles?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

## <a name="10-features"></a>10.Features (機能)
このセクションでは特徴の情報 (インポートされた特徴とその値、順位付け、この順位付けが割り当てられた時期など) を取得する方法を説明します。 特徴は、カタログ データの一部としてインポートされ、順位付けのビルドが実行されたときに順位付けが関連付けられます。
使用状況データと項目の種類のパターンに従って特徴の順位を変更できます。 しかし、使用状況と項目の一貫性を保つため、順位付けの変動は小さくなければなりません。
特徴の順位は負以外の数値です。 数値 0 は、特徴が順位付けされていないことを意味します (最初の順位付けのビルドが完了する前にこの API を呼び出すと、これが発生します)。 順位付けの日付をスコアの鮮度と言います。

### <a name="101-get-features-info-for-last-rank-build"></a>10.1. 特徴の情報の取得 (最後の順位付けのビルド)
前回成功した順位付けのビルドの特徴の情報 (順位付けを含む) を取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| samplingSize |カタログ内に存在するデータに従い、特徴ごとに含める値の数。 <br/>次のいずれかの値になります。<br>  -1 - すべてのサンプル。 <br>0 - サンプリングなし。 <br>N - 特徴名ごとに、N 個のサンプルを返します。 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

応答には、特徴の情報エントリの一覧が含まれています。 各エントリは、次のとおりです。

* `feed/entry/content/m:properties/d:Name` - 特徴名。
* `feed/entry/content/m:properties/d:RankUpdateDate` - 順位付けがこの特徴に割り当てられた日付。 スコアの鮮度特徴とも呼ばれます。 古代の日付 ('0001-01-01T00:00:00') は、順位付けのビルドが実行されていないことを意味します。
* `feed/entry/content/m:properties/d:Rank` - 特徴の順位 (float)。 2.0 以上の順位は良好な特徴であると見なせます。
* `feed/entry/content/m:properties/d:SampleValues` -要求されたサンプリング サイズまでの値のコンマ区切りの一覧。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get the features of a model</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2015-01-08T13:15:02Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">ModelFeaturesEntity</title>
        <updated>2015-01-08T13:15:02Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Name m:type="Edm.String">Author</d:Name>
                <d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
                <d:Rank m:type="Edm.String">0</d:Rank>
                <d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">ModelFeaturesEntity</title>
        <updated>2015-01-08T13:15:02Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Name m:type="Edm.String">Publisher</d:Name>
                <d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
                <d:Rank m:type="Edm.String">0</d:Rank>
                <d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">ModelFeaturesEntity</title>
        <updated>2015-01-08T13:15:02Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1"/>
        <contenttype="application/xml">
        <m:properties>
            <d:Name m:type="Edm.String">Year</d:Name>
            <d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
            <d:Rank m:type="Edm.String">0</d:Rank>
            <d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
        </m:properties>
        </content>
    </entry>
</feed>

### <a name="102-get-features-info-for-specific-rank-build"></a>10.2. 特徴の情報の取得 (特定の順位付けのビルド)
特定の順位付けのビルドの特徴の情報 (順位付けを含む) を取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&rankBuildId=<rankBuildId>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&rankBuildId=1000551&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| samplingSize |カタログ内に存在するデータに従い、特徴ごとに含める値の数。<br/> 次のいずれかの値になります。<br>  -1 - すべてのサンプル。 <br>0 - サンプリングなし。 <br>N - 特徴名ごとに、N 個のサンプルを返します。 |
| rankBuildId |順位付けのビルドの一意識別子。最後の順位付けのビルドの場合は -1。 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

応答には、特徴の情報エントリの一覧が含まれています。 各エントリは、次のとおりです。

* `feed/entry/content/m:properties/d:Name` - 特徴名。
* `feed/entry/content/m:properties/d:RankUpdateDate` - 順位付けがこの特徴に割り当てられた日付。 スコアの鮮度特徴とも呼ばれます。 古代の日付 ('0001-01-01T00:00:00') は、順位付けのビルドが実行されていないことを意味します。
* `feed/entry/content/m:properties/d:Rank` - 特徴の順位 (float)。 2.0 以上の順位は良好な特徴であると見なせます。
* `feed/entry/content/m:properties/d:SampleValues` -要求されたサンプリング サイズまでの値のコンマ区切りの一覧。

OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get the features of a model</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2015-01-08T13:54:22Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">ModelFeaturesEntity</title>
            <updated>2015-01-08T13:54:22Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Name m:type="Edm.String">Author</d:Name>
                    <d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
                    <d:Rank m:type="Edm.String">3.38867426</d:Rank>
                    <d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
            <title type="text">ModelFeaturesEntity</title>
            <updated>2015-01-08T13:54:22Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Name m:type="Edm.String">Publisher</d:Name>
                    <d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
                    <d:Rank m:type="Edm.String">1.67839336</d:Rank>
                    <d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
            <title type="text">ModelFeaturesEntity</title>
            <updated>2015-01-08T13:54:22Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Name m:type="Edm.String">Year</d:Name>
                    <d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
                    <d:Rank m:type="Edm.String">1.12352145</d:Rank>
                    <d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
                </m:properties>
            </content>
        </entry>
    </feed>


## <a name="11-build"></a>11.構築
  このセクションでは、ビルドに関連する様々な API について説明します。 ビルドには、推奨事項のビルド、順位付けのビルド、FBT (Frequently Bought Together) ビルドの 3 種類があります。

推奨事項のビルドは、予測のために使用される推奨モデルの生成に使用されます。 予測 (この種類のビルド用) には、次の 2 種類があります。

* I2I - 別名 項目から項目の推奨事項 - 項目や項目の一覧を前提に、このオプションでは関心が高いと思われる項目の一覧を予測します。
* U2I - 別名 ユーザーから項目の推奨事項 - ユーザー ID (と任意で項目の一覧) を前提に、このオプションでは、そのユーザー (とその項目におけるその他の選択肢) にとって関心が高いと思われる項目を予測します。 U2I の推奨事項は、モデルが構築された時間までユーザーが関心があった項目の履歴に基づいています。

順位付けのビルドは、特徴の有用性について知るための技術的なビルドです。 通常、特徴に関連する推奨モデルについて最適な結果を得るには、次の手順を実行する必要があります。

* 順位付けのビルドをトリガーして (特徴のスコアが安定している場合を除く)、特徴のスコアが取得されるまで待ちます。
* [特徴の情報の取得](#101-get-features-info-for-last-rank-build) API を呼び出して、特徴の順位付けを取得します。
* 次のパラメーターを指定して推奨事項のビルドを構成します。
  * `useFeatureInModel` - True に設定します。
  * `ModelingFeatureList` - スコアが 2.0 以上 (前の手順で取得した順位に応じて変更します) の特徴のコンマ区切りのリストに設定します。
  * `AllowColdItemPlacement` - True に設定します。
  * オプションで `EnableFeatureCorrelation` を True に設定し、`ReasoningFeatureList` を説明に使用する特徴のリストに設定できます (通常はモデル化またはサブリストで使用される特徴のリストと同じです)。
* 構成したパラメーターを使用して推奨事項のビルドをトリガーします。

注: パラメーターを構成しない場合 (パラメーターなしで推奨事項のビルドを呼び出す場合など) や、特徴の使用状況を明示的に無効にしない場合 ( `UseFeatureInModel` を False に設定する場合など)、順位付けのビルドが存在しているなら、システムは特徴に関連するパラメーターを前述の値に設定します。

順位付けのビルドと推奨事項のビルドを同じモデルに対して同時に実行することに関して制限はありません。 ただし、同じモデルに同じ型の 2 つのビルドを並列で実行することはできません。

FBT (よく一緒に購入されている品目) のビルドは「控えめな」レコメンダーとも呼ばれる別の推奨事項のアルゴリズムであり、性質が同様ではないカタログに適しています (同様であるもの: 書籍、映画、ある種の食品、ファッション。同様でないもの: コンピューターとデバイス、クロス ドメイン、多様であるもの)。

注: アップロードした使用状況ファイルにオプション フィールド "イベントの種類" が含まれている場合、FBT モデル化では "購買" イベントのみが使用されます。 イベントの種類を指定しない場合、すべてのイベントが購買として考慮されます。

#### <a name="111-build-parameters"></a>11.1 ビルド パラメーター
各種のビルドは一連のパラメーター (以下を参照) を使用して構成できます。 パラメーターを構成しない場合、システムは、ビルドをトリガーする時点で存在する情報に応じて、自動的にパラメーターに値を付与します。

##### <a name="1111-usage-condenser"></a>11.1.1. 使用状況コンデンサー
いくつかの使用状況ポイントを持つユーザーまたは項目には、情報よりも多くのノイズが含まれている可能性があります。 システムは 1 つのモデルで使用されるユーザー/項目ごとの使用状況のポイントの最小数を予測しようとします。 この数値は、項目の場合は ItemCutoffLowerBound パラメーターと ItemCutoffUpperBound パラメーターで定義されている範囲内になり、ユーザーの場合は UserCutOffLowerBound パラメーターと UserCutoffUpperBound パラメーターで定義されている範囲内になります。 対応する境界の少なくとも 1 つをゼロに設定することによって、項目またはユーザーに対するコンデンサーの影響を最小化できます。

##### <a name="1112-rank-build-parameters"></a>11.1.2. 順位付けのビルドのパラメーター
次の表は、順位付けのビルドのビルド パラメーターを示しています。

| キー | Description | 型 | 有効な値: |
|:--- |:--- |:--- |:--- |
| NumberOfModelIterations |モデルが実行するイテレーションの数は、全体的なコンピューティング時間とモデルの精度に反映されます。 数値が高いと精度が向上しますが、コンピューティング時間が長くなります。 |Integer |10-50 |
| NumberOfModelDimensions |ディメンションの数は、データ内でモデルが検索しようとする「特徴」の数に関連しています。 ディメンションの数を増やすと、結果をより詳細に微調整して、小さいクラスターにすることができます。 ただし、ディメンションが多すぎると、モデルが項目間の相関関係を検出できなくなります。 |Integer |10-40 |
| ItemCutOffLowerBound |コンデンサーの項目の下限を定義します。 前述の使用状況コンデンサーを参照してください。 |Integer |2 以上 (0 でコンデンサーが無効) |
| ItemCutOffUpperBound |コンデンサーの項目の上限を定義します。 前述の使用状況コンデンサーを参照してください。 |Integer |2 以上 (0 でコンデンサーが無効) |
| UserCutOffLowerBound |コンデンサーのユーザーの下限を定義します。 前述の使用状況コンデンサーを参照してください。 |Integer |2 以上 (0 でコンデンサーが無効) |
| UserCutOffUpperBound |コンデンサーのユーザーの上限を定義します。 前述の使用状況コンデンサーを参照してください。 |Integer |2 以上 (0 でコンデンサーが無効) |

##### <a name="1113-recommendation-build-parameters"></a>11.1.3. 推奨事項のビルドのパラメーター
次の表は、推奨事項のビルドのビルド パラメーターを示しています。

| キー | Description | 型 | 有効な値: |
|:--- |:--- |:--- |:--- |
| NumberOfModelIterations |モデルが実行するイテレーションの数は、全体的なコンピューティング時間とモデルの精度に反映されます。 数値が高いと精度が向上しますが、コンピューティング時間が長くなります。 |Integer |10-50 |
| NumberOfModelDimensions |ディメンションの数は、データ内でモデルが検索しようとする「特徴」の数に関連しています。 ディメンションの数を増やすと、結果をより詳細に微調整して、小さいクラスターにすることができます。 ただし、ディメンションが多すぎると、モデルが項目間の相関関係を検出できなくなります。 |Integer |10-40 |
| ItemCutOffLowerBound |コンデンサーの項目の下限を定義します。 前述の使用状況コンデンサーを参照してください。 |Integer |2 以上 (0 でコンデンサーが無効) |
| ItemCutOffUpperBound |コンデンサーの項目の上限を定義します。 前述の使用状況コンデンサーを参照してください。 |Integer |2 以上 (0 でコンデンサーが無効) |
| UserCutOffLowerBound |コンデンサーのユーザーの下限を定義します。 前述の使用状況コンデンサーを参照してください。 |Integer |2 以上 (0 でコンデンサーが無効) |
| UserCutOffUpperBound |コンデンサーのユーザーの上限を定義します。 前述の使用状況コンデンサーを参照してください。 |Integer |2 以上 (0 でコンデンサーが無効) |
| Description |ビルドの説明。 |String |任意のテキスト (最大 512 文字) |
| EnableModelingInsights |推奨モデルのメトリックを計算することができます。 |Boolean |True または False |
| UseFeaturesInModel |推奨モデルを強化するために特徴を使用するかどうかを示します。 |Boolean |True または False |
| ModelingFeatureList |推奨事項を強化するために推奨事項のビルドに使用される、特徴名のコンマ区切りの一覧。 |String |特徴名 (最大 512 文字） |
| AllowColdItemPlacement |推奨事項が特徴の類似性を使用してコールド項目もプッシュするかどうかを示します。 |Boolean |True または False |
| EnableFeatureCorrelation |理由で特徴を使用するかどうかを示します。 |Boolean |True または False |
| ReasoningFeatureList |理由の文 (推奨事項の説明など) に使用される特徴名のコンマ区切りの一覧。 |String |特徴名 (最大 512 文字） |
| EnableU2I |個人用に設定された推奨事項、別名 U2I (ユーザーから項目の推奨事項) を許可します。 |Boolean |True または False (既定値は True) |

##### <a name="1114-fbt-build-parameters"></a>11.1.4. FBT ビルド パラメーター
次の表は、推奨事項のビルドのビルド パラメーターを示しています。

| キー | Description | 型 | 有効な値 (既定値) |
|:--- |:--- |:--- |:--- |
| FbtSupportThreshold |モデルがどの程度控えめか。 モデル化で考慮すべき項目の同時発生の数。 |Integer |3-50 (6) |
| FbtMaxItemSetSize |頻度のセット内のアイテム数の限度を定めます。 |Integer |2-3 (2) |
| FbtMinimalScore |返される結果に含めるために頻度のセットが持つべきスコアの最小値。 大きいほど良好です。 |Double |0 以上 (0) |
| FbtSimilarityFunction |ビルドで使用する類似関数を定義します。 リフトではセレンディピティが、共起では予測可能性が、2 項間では Jaccard が適しています。 |String |共起、リフト、Jaccard (リフト) |

### <a name="112-trigger-a-recommendation-build"></a>11.2. 推奨事項のビルドのトリガー
  既定では、この API は推奨モデルのビルドをトリガーします。 順位付けのビルドをトリガーする (特徴をスコア付けするために) には、ビルドの種類のパラメーターが指定されたビルド API の変種を使用する必要があります。

| HTTP メソッド | URI |
|:--- |:--- |
| POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27` |
| ヘッダー |`"Content-Type", "text/xml"` (送信の場合、要求本文) |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| userDescription |カタログを表すテキスト形式の識別子。 空白を使用する場合は、%20 にエンコードする必要があることに注意してください上記の例をご覧ください。 (上記の例を参照)。<br>最大長: 50 |
| apiVersion |1.0 |
|  | |
| 要求本文 |空のままにすると、既定のビルド パラメーターを使用してビルドが実行されます。<br><br>ビルド パラメーターを設定する場合は、次の例のように本文にパラメーターを XML として指定して送信します。 (パラメーターの詳細については、「ビルド パラメーター」セクションを参照してください)。`<NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance><EnableModelingInsights>true</EnableModelingInsights><UseFeaturesInModel>false</UseFeaturesInModel><ModelingFeatureList>feature_name_1,feature_name_2,...</ModelingFeatureList><AllowColdItemPlacement>false</AllowColdItemPlacement><EnableFeatureCorrelation>false</EnableFeatureCorrelation><ReasoningFeatureList>feature_name_a,feature_name_b,...</ReasoningFeatureList></BuildParametersList>` |

**応答**:

HTTP 状態コード: 200

これは、非同期 API です。 応答として、ビルド ID が表示されます。 ビルドがいつ終了したかを知るには、「モデルのビルド状態を取得する」API を呼び出し、応答でこのビルド ID を検索する必要があります。 データのサイズによっては、ビルドが数分から数時間かかる可能性があることに注意してください。

ビルドが終了するまで推奨事項を使用することはできません。

有効なビルド状態:

* Create - ビルド要求を作成します。
* Queued - ビルド要求が送信され、キューされます。
* Building - ビルドが実行中です。
* Success - ビルドが正常に終了しました。
* Error - ビルドでエラーが発生して終了しました。
* Cancelled - ビルドが取り消されました。
* Cancelling - ビルドの取り消し要求が送信されました。

ビルド ID は、次のパスの下にあります： `Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Build a Model with RequestBody</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T08:56:34Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

### <a name="113-trigger-build-recommendation-rank-or-fbt"></a>11.3. トリガーのビルド (推奨事項、ランク付け、または FBT)
| HTTP メソッド | URI |
|:--- |:--- |
| POST |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&buildType=%27<buildType>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&buildType=%27Ranking%27&apiVersion=%271.0%27` |
| ヘッダー |`"Content-Type", "text/xml"` (送信の場合、要求本文) |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| userDescription |カタログを表すテキスト形式の識別子。 空白を使用する場合は、%20 にエンコードする必要があることに注意してください上記の例をご覧ください。 (上記の例を参照)。<br>最大長: 50 |
| buildType |呼び出すビルドの種類:  <br/> - 推奨事項のビルドは ’Recommendation’ <br> - 順位付けのビルドは 'Ranking' <br/>  -FBT のビルドは ' Fbt' |
| apiVersion |1.0 |
|  | |
| 要求本文 |空のままにすると、既定のビルド パラメーターを使用してビルドが実行されます。<br><br>ビルド パラメーターを設定する場合は、次の例のように本文に XML として指定して送信します。 (パラメーターの詳細と完全なリストについては、「ビルド パラメーター」セクションを参照してください)。`<BuildParametersList><NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance></BuildParametersList>` |

**応答**:

HTTP 状態コード: 200

これは、非同期 API です。 応答として、ビルド ID が表示されます。 ビルドがいつ終了したかを知るには、「モデルのビルド状態を取得する」API を呼び出し、応答でこのビルド ID を検索する必要があります。 データのサイズによっては、ビルドが数分から数時間かかる可能性があることに注意してください。

ビルドが終了するまで推奨事項を使用することはできません。

有効なビルド状態:

* Create - モデルが作成されました。
* Queued - モデルのビルドがトリガーされ、キューに登録されています。
* Building - モデルをビルドしています。
* Success - ビルドが正常に終了しました。
* Error - ビルドでエラーが発生して終了しました。
* Cancelled - ビルドが取り消されました。
* Cancelling - ビルドが取り消されます。

ビルド ID は、次のパスの下にあります： `Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Build a Model with RequestBody</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T08:56:34Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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




### <a name="114-get-builds-status-of-a-model"></a>11.4. モデルのビルド状態の取得
指定したモデルのビルドとそれらの状態を取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| onlyLastBuild |モデルのすべてのビルド履歴を返すか、最新のビルドの状態のみを返すかを示します |
| apiVersion |1.0 |

**応答**:

HTTP 状態コード: 200

応答には、ビルドごとに 1 つのエントリが含まれています。 各エントリには、次のデータが含まれています。

* `feed/entry/content/properties/UserName` - ユーザーの名前。
* `feed/entry/content/properties/ModelName` - モデルの名前。
* `feed/entry/content/properties/ModelId` - モデルの一意識別子。
* `feed/entry/content/properties/IsDeployed` - ビルドがデプロイされている  (アクティブなビルド) かどうか。
* `feed/entry/content/properties/BuildId` - ビルドの一意識別子。
* `feed/entry/content/properties/BuildType` - ビルドの種類。
* `feed/entry/content/properties/Status` - ビルド状態。 次のいずれか: Error、Building、Queued、Cancelling、Cancelled、Success。
* `feed/entry/content/properties/StatusMessage` - 詳細なステータス メッセージ (特定の状態にのみ適用されます)。
* `feed/entry/content/properties/Progress` - ビルドの進捗状況 (%)。
* `feed/entry/content/properties/StartTime` - ビルドの開始時刻。
* `feed/entry/content/properties/EndTime` - ビルドの終了時刻。
* `feed/entry/content/properties/ExecutionTime` - ビルドの期間。
* `feed/entry/content/properties/ProgressStep` - 進行中のビルドの現在のステージの詳細。

有効なビルド状態:

* Created - ビルド要求エントリが作成されました。
* Queued - ビルド要求がトリガーされ、キューされます。
* Building - ビルドが処理中です。
* Success - ビルドが正常に終了しました。
* Error - ビルドでエラーが発生して終了しました。
* Cancelled - ビルドが取り消されました。
* Cancelling - ビルドが取り消されます。

ビルドの種類における有効な値:

* Rank - 順番付けのビルド。
* Recommendation - 推奨事項のビルド。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get builds status of a model</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-11-05T17:51:10Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetBuildsStatusEntity</title>
            <updated>2014-11-05T17:51:10Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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


### <a name="115-get-builds-status"></a>11.5. ビルド状態の取得
ユーザーのすべてのモデルに関するビルド状態を取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=<bool>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=true&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| onlyLastBuild |モデルのすべてのビルド履歴を返すか、最新のビルドの状態のみを返すかを示します。 |
| apiVersion |1.0 |

**応答**:

HTTP 状態コード: 200

応答には、ビルドごとに 1 つのエントリが含まれています。 各エントリには、次のデータが含まれています。

* `feed/entry/content/properties/UserName` - ユーザーの名前。
* `feed/entry/content/properties/ModelName` - モデルの名前。
* `feed/entry/content/properties/ModelId` - モデルの一意識別子。
* `feed/entry/content/properties/IsDeployed` - ビルドがデプロイされるかどうか。
* `feed/entry/content/properties/BuildId` - ビルドの一意識別子。
* `feed/entry/content/properties/BuildType` - ビルドの種類。
* `feed/entry/content/properties/Status` - ビルド状態。 次のいずれか: Error、Building、Queued、Cancelled、Cancelling、Success。
* `feed/entry/content/properties/StatusMessage` - 詳細なステータス メッセージ (特定の状態にのみ適用されます)。
* `feed/entry/content/properties/Progress` - ビルドの進捗状況 (%)。
* `feed/entry/content/properties/StartTime` - ビルドの開始時刻。
* `feed/entry/content/properties/EndTime` - ビルドの終了時刻。
* `feed/entry/content/properties/ExecutionTime` - ビルドの期間。
* `feed/entry/content/properties/ProgressStep` - 進行中のビルドの現在のステージの詳細。

有効なビルド状態:

* Created - ビルド要求エントリが作成されました。
* Queued - ビルド要求がトリガーされ、キューされます。
* Building - ビルドが処理中です。
* Success - ビルドが正常に終了しました。
* Error - ビルドでエラーが発生して終了しました。
* Cancelled - ビルドが取り消されました。
* Cancelling - ビルドが取り消されます。

ビルドの種類における有効な値:

* Rank - 順番付けのビルド。
* Recommendation - 推奨事項のビルド。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get builds status of a user</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-11-05T18:41:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetBuildsStatusEntity</title>
            <updated>2014-11-05T18:41:21Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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


### <a name="116-delete-build"></a>11.6. ビルドの削除
ビルドを削除します。

注: <br>アクティブなビルドを削除することはできません。 削除する前に、そのモデルを別のアクティブなビルドに変更する必要があります。<br>進行中のビルドを削除することはできません。 まず、「<strong>ビルドのキャンセル</strong>」を呼び出してビルドを取り消す必要があります。

| HTTP メソッド | URI |
|:--- |:--- |
| 削除 |`<rootURI>/DeleteBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/DeleteBuild?buildId=%271500068%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| buildId |ビルドの一意識別子。 |
| apiVersion |1.0 |

**応答:**

HTTP 状態コード: 200

### <a name="117-cancel-build"></a>11.7. ビルドのキャンセル
ビルド中の状態にあるビルドをキャンセルします。

| HTTP メソッド | URI |
|:--- |:--- |
| PUT |`<rootURI>/CancelBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/CancelBuild?buildId=%271500076%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| buildId |ビルドの一意識別子。 |
| apiVersion |1.0 |

**応答:**

HTTP 状態コード: 200

### <a name="118-get-build-parameters"></a>11.8. ビルド パラメーターの取得
ビルド パラメーターを取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetBuildParameters?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/GetBuildParameters?buildId=%271000653%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| buildId |ビルドの一意識別子。 |
| apiVersion |1.0 |

**応答:**

HTTP 状態コード: 200

この API は、キーと値の要素のコレクションを返します。 各要素は、パラメーターとその値を表します:

* `feed/entry/content/properties/Key` - パラメーター名をビルドします。
* `feed/entry/content/properties/Value` - パラメーターの値をビルドします。

次の表は、各キーを表す値を示しています。

| キー | Description | 型 | 有効な値: |
|:--- |:--- |:--- |:--- |
| NumberOfModelIterations |モデルが実行するイテレーションの数は、全体的なコンピューティング時間とモデルの精度に反映されます。 数値が高いと精度が向上しますが、コンピューティング時間が長くなります。 |Integer |10-50 |
| NumberOfModelDimensions |ディメンションの数は、データ内でモデルが検索しようとする「特徴」の数に関連しています。 ディメンションの数を増やすと、結果をより詳細に微調整して、小さいクラスターにすることができます。 ただし、ディメンションが多すぎると、モデルが項目間の相関関係を検出できなくなります。 |Integer |10-40 |
| ItemCutOffLowerBound |コンデンサーの項目の下限を定義します。 前述の使用状況コンデンサーを参照してください。 |Integer |2 以上 (0 でコンデンサーが無効) |
| ItemCutOffUpperBound |コンデンサーの項目の上限を定義します。 前述の使用状況コンデンサーを参照してください。 |Integer |2 以上 (0 でコンデンサーが無効) |
| UserCutOffLowerBound |コンデンサーのユーザーの下限を定義します。 前述の使用状況コンデンサーを参照してください。 |Integer |2 以上 (0 でコンデンサーが無効) |
| UserCutOffUpperBound |コンデンサーのユーザーの上限を定義します。 前述の使用状況コンデンサーを参照してください。 |Integer |2 以上 (0 でコンデンサーが無効) |
| Description |ビルドの説明。 |String |任意のテキスト (最大 512 文字) |
| EnableModelingInsights |推奨モデルのメトリックを計算することができます。 |Boolean |True または False |
| UseFeaturesInModel |推奨モデルを強化するために特徴を使用するかどうかを示します。 |Boolean |True または False |
| ModelingFeatureList |推奨事項を強化するために推奨事項のビルドに使用される、特徴名のコンマ区切りの一覧。 |String |特徴名 (最大 512 文字） |
| AllowColdItemPlacement |推奨事項が特徴の類似性を使用してコールド項目もプッシュするかどうかを示します。 |Boolean |True または False |
| EnableFeatureCorrelation |理由で特徴を使用するかどうかを示します。 |Boolean |True または False |
| ReasoningFeatureList |理由の文 (推奨事項の説明など) に使用される特徴名のコンマ区切りの一覧。 |String |特徴名 (最大 512 文字） |

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get build parameters</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2015-01-08T13:50:57Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">UseFeaturesInModel</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">AllowColdItemPlacement</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">EnableFeatureCorrelation</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">EnableModelingInsights</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">NumberOfModelIterations</d:Key>
                    <d:Value m:type="Edm.String">10</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
            <titletype="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">NumberOfModelDimensions</d:Key>
                    <d:Value m:type="Edm.String">10</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <linkrel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ItemCutOffLowerBound</d:Key>
                    <d:Value m:type="Edm.String">2</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ItemCutOffUpperBound</d:Key>
                    <d:Value m:type="Edm.String">2147483647</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">UserCutOffLowerBound</d:Key>
                    <d:Value m:type="Edm.String">2</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">UserCutOffUpperBound</d:Key>
                    <d:Value m:type="Edm.String">2147483647</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ModelingFeatureList</d:Key>
                    <d:Value m:type="Edm.String"/>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ReasoningFeatureList</d:Key>
                    <d:Value m:type="Edm.String"/>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">Description</d:Key>
                    <d:Value m:type="Edm.String">rankBuild</d:Value>
                </m:properties>
            </content>
        </entry>
    </feed>

## <a name="12-recommendation"></a>12.推奨
### <a name="121-get-item-recommendations-for-active-build"></a>12.1. 項目の推奨事項の取得 (アクティブなビルド)
シード (入力) 項目の一覧に基づき、「推奨事項」や「Fbt」の種類のアクティブなビルドの推奨事項を取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| itemIds |推奨項目のコンマ区切りの一覧。 <br>アクティブなビルドが FBT の種類の場合は、1 つの項目のみを送信できます。 <br>最大長: 1024 |
| numberOfResults |必要な結果の数  <br>  最大: 150 |
| includeMetatadata |将来的に利用 (常に false) |
| apiVersion |1.0 |

**応答:**

HTTP 状態コード: 200

応答には、推奨項目ごとに 1 つのエントリが含まれています。 各エントリには、次のデータが含まれています。

* `Feed\entry\content\properties\Id` - 推奨項目の ID。
* `Feed\entry\content\properties\Name` - 項目の名前。
* `Feed\entry\content\properties\Rating` - 推奨項目の評価、高い数値は高い信頼度を意味します。
* `Feed\entry\content\properties\Reasoning` - 推奨の理由 (推奨の説明など)。

次の応答例には、10 個の推奨項目が含まれています。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Get Recommendation</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T12:28:48Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
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

### <a name="122-get-item-recommendations-of-a-specific-build"></a>12.2. 推奨事項の取得 (特定のビルド)
「推奨事項」または「Fbt」の種類の特定のビルドの推奨事項を取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=1234&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| itemIds |推奨項目のコンマ区切りの一覧。 <br>アクティブなビルドが FBT の種類の場合は、1 つの項目のみを送信できます。 <br>最大長: 1024 |
| numberOfResults |必要な結果の数  <br>  最大: 150 |
| includeMetatadata |将来的に利用 (常に false) |
| buildId |この推奨事項の要求で使用するビルド ID |
| apiVersion |1.0 |

**応答:**

HTTP 状態コード: 200

応答には、推奨項目ごとに 1 つのエントリが含まれています。 各エントリには、次のデータが含まれています。

* `Feed\entry\content\properties\Id` - 推奨項目の ID。
* `Feed\entry\content\properties\Name` - 項目の名前。
* `Feed\entry\content\properties\Rating` - 推奨項目の評価、高い数値は高い信頼度を意味します。
* `Feed\entry\content\properties\Reasoning` - 推奨の理由 (推奨の説明など)。

12.1 の応答の例を参照してください。

### <a name="123-get-fbt-recommendations-for-active-build"></a>12.3. FBT の推奨事項の取得（アクティブなビルド）
シード (入力) 項目に基づいて「Fbt」の種類のアクティブなビルドの推奨事項を取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=<double>&includeMetadata=false&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| itemId |推奨する項目です。 <br>最大長: 1024 |
| numberOfResults |必要な結果の数  <br> 最大: 150 |
| minimalScore |返される結果に含めるために頻度のセットが持つべきスコアの最小値。 |
| includeMetatadata |将来的に利用 (常に false) |
| apiVersion |1.0 |

**応答:**

HTTP 状態コード: 200

応答には、推奨項目セット (通常はシード/入力項目と共に購入した項目のセット) ごとに 1 つのエントリが含まれます。 各エントリには、次のデータが含まれています。

* `Feed\entry\content\properties\Id1` - 推奨項目の ID。
* `Feed\entry\content\properties\Name1` - 項目の名前。
* `Feed\entry\content\properties\Id2` - 2 番目の推奨項目 ID (省略可能)。
* `Feed\entry\content\properties\Name2` - 2 番目の項目の名前 (省略可能)。
* `Feed\entry\content\properties\Rating` - 推奨項目の評価、高い数値は高い信頼度を意味します。
* `Feed\entry\content\properties\Reasoning` - 推奨の理由 (推奨の説明など)。

次の応答例には、3 個の推奨項目セットが含まれています。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Get Recommendation</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemId='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T12:28:48Z</updated>
      <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">159</d:Id1>
        <d:Name1 m:type="Edm.String">159</d:Name1>
        <d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '159'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">52</d:Id1>
        <d:Name1 m:type="Edm.String">52</d:Name1>
        <d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.533343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '52'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">35</d:Id1>
        <d:Name1 m:type="Edm.String">35</d:Name1>
        <d:Id2 m:type="Edm.String">102</d:Id2>
        <d:Name2 m:type="Edm.String">102</d:Name2>
        <d:Rating m:type="Edm.Double">0.523343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '35' and '102'</d:Reasoning>
      </m:properties>
    </content>
      </entry>
    </feed>

### <a name="124-get-fbt-recommendations-of-a-specific-build"></a>12.4. FBT の推奨事項の取得 (特定のビルド)
「Fbt」の種類の特定のビルドの推奨事項を取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=0.1&includeMetadata=false&buildId=1234&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| itemId |推奨する項目です。 <br>最大長: 1024 |
| numberOfResults |必要な結果の数  <br> 最大: 150 |
| minimalScore |返される結果に含めるために頻度のセットが持つべきスコアの最小値。 |
| includeMetatadata |将来的に利用 (常に false) |
| buildId |この推奨事項の要求で使用するビルド ID |
| apiVersion |1.0 |

**応答:**

HTTP 状態コード: 200

応答には、推奨項目セット (通常はシード/入力項目と共に購入した項目のセット) ごとに 1 つのエントリが含まれます。 各エントリには、次のデータが含まれています。

* `Feed\entry\content\properties\Id1` - 推奨項目の ID。
* `Feed\entry\content\properties\Name1` - 項目の名前。
* `Feed\entry\content\properties\Id2` - 2 番目の推奨項目 ID (省略可能)。
* `Feed\entry\content\properties\Name2` - 2 番目の項目の名前 (省略可能)。
* `Feed\entry\content\properties\Rating` - 推奨項目の評価、高い数値は高い信頼度を意味します。
* `Feed\entry\content\properties\Reasoning` - 推奨の理由 (推奨の説明など)。

12.3 の応答の例を参照してください。

### <a name="125-get-user-recommendations-for-active-build"></a>12.5. ユーザーの推奨事項の取得（アクティブなビルド）
アクティブなビルドとしてマークされた「推奨事項」の種類の推奨事項を取得します。

API では、ユーザーの使用率の履歴に基づいて予測される項目の一覧を返します。

注: 

1. FBT ビルドに対するユーザーの推奨事項はありません。
2. アクティブなビルドが FBT の場合、このメソッドはエラーを返します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| userId |ユーザーの一意識別子。 |
| numberOfResults |必要な結果の数  |
| includeMetatadata |将来的に利用 (常に false) |
| apiVersion |1.0 |

**応答:**

HTTP 状態コード: 200

応答には、推奨項目ごとに 1 つのエントリが含まれています。 各エントリには、次のデータが含まれています。

* `Feed\entry\content\properties\Id` - 推奨項目の ID。
* `Feed\entry\content\properties\Name` - 項目の名前。
* `Feed\entry\content\properties\Rating` - 推奨項目の評価、高い数値は高い信頼度を意味します。
* `Feed\entry\content\properties\Reasoning` - 推奨の理由 (推奨の説明など)。

12.1 の応答の例を参照してください。

### <a name="126-get-user-recommendations-with-item-list-for-active-build"></a>12.6. ユーザーの推奨事項と項目リストの取得（アクティブなビルド）
アクティブなビルドとしてマークされた「推奨事項」の種類の推奨事項と項目リストを取得します。

API では、ユーザーとその他に提供された項目の使用率の履歴に基づいて予測される項目の一覧を返します。

注: 

1. FBT ビルドに対するユーザーの推奨事項はありません。
2. アクティブなビルドが FBT の場合、このメソッドはエラーを返します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%2C1000%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| userId |ユーザーの一意識別子。 |
| itemsIds |推奨項目のコンマ区切りの一覧。 最大長: 1024 |
| numberOfResults |必要な結果の数  |
| includeMetatadata |将来的に利用 (常に false) |
| apiVersion |1.0 |

**応答:**

HTTP 状態コード: 200

応答には、推奨項目ごとに 1 つのエントリが含まれています。 各エントリには、次のデータが含まれています。

* `Feed\entry\content\properties\Id` - 推奨項目の ID。
* `Feed\entry\content\properties\Name` - 項目の名前。
* `Feed\entry\content\properties\Rating` - 推奨項目の評価、高い数値は高い信頼度を意味します。
* `Feed\entry\content\properties\Reasoning` - 推奨の理由 (推奨の説明など)。

12.1 の応答の例を参照してください。

### <a name="127-get-user-recommendations--of-a-specific-build"></a>12.7. ユーザーの推奨事項の取得 (特定のビルド)
「推奨事項」の種類の特定のビルドでユーザーの推奨事項を取得します。

API では、ユーザーの使用率の履歴 (特定のビルドで使用) に基づいて予測される項目の一覧を返します。

注: FBT ビルドに対するユーザーの推奨事項はありません。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| userId |ユーザーの一意識別子。 |
| numberOfResults |必要な結果の数  |
| includeMetatadata |将来的に利用 (常に false) |
| buildId |この推奨事項の要求で使用するビルド ID |
| apiVersion |1.0 |

**応答:**

HTTP 状態コード: 200

応答には、推奨項目ごとに 1 つのエントリが含まれています。 各エントリには、次のデータが含まれています。

* `Feed\entry\content\properties\Id` - 推奨項目の ID。
* `Feed\entry\content\properties\Name` - 項目の名前。
* `Feed\entry\content\properties\Rating` - 推奨項目の評価、高い数値は高い信頼度を意味します。
* `Feed\entry\content\properties\Reasoning` - 推奨の理由 (推奨の説明など)。

12.1 の応答の例を参照してください。

### <a name="128-get-user-recommendations-with-item-list-of-a-specific-build"></a>12.8. ユーザーの推奨事項と項目リストの取得（特定のビルド）
「推奨事項」とその他の項目リストの種類の特定のビルドでユーザーの推奨事項を取得します。

API では、ユーザーとその他の項目リストにおける使用率の履歴に基づいて予測される項目の一覧を返します。

注: FBT ビルドに対するユーザーの推奨事項はありません。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| userId |ユーザーの一意識別子。 |
| itemIds |推奨項目のコンマ区切りの一覧。 最大長: 1024 |
| numberOfResults |必要な結果の数  |
| includeMetatadata |将来的に利用 (常に false) |
| buildId |この推奨事項の要求で使用するビルド ID |
| apiVersion |1.0 |

**応答:**

HTTP 状態コード: 200

応答には、推奨項目ごとに 1 つのエントリが含まれています。 各エントリには、次のデータが含まれています。

* `Feed\entry\content\properties\Id` - 推奨項目の ID。
* `Feed\entry\content\properties\Name` - 項目の名前。
* `Feed\entry\content\properties\Rating` - 推奨項目の評価、高い数値は高い信頼度を意味します。
* `Feed\entry\content\properties\Reasoning` - 推奨の理由 (推奨の説明など)。

12.1 の応答の例を参照してください。

## <a name="13-user-usage-history"></a>13.ユーザーの使用状況の履歴
推奨モデルが構築されたら、システムでは構築に使用したユーザーの履歴 (特定のユーザーに関連する項目) を取得できるようになります。
この API により、ユーザーの履歴が取得できます。

注: ユーザーの履歴は現在推奨設定のビルドに対してのみ利用できます。

### <a name="131-retrieve-user-history"></a>13.1 ユーザー履歴の取得
アクティブなビルドや指定されたビルドで使用した特定のユーザー ID の項目リストを取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |アクティブなビルドのユーザー履歴を取得します。<br/>`<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&apiVersion=%271.0%27`<br/><br/>指定したビルドのユーザー履歴の取得 `<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&buildId=<int>&apiVersion=%271.0%27`<br/><br/>例:`<rootURI>/GetUserHistory?modelId=%2727967136e8-f868-4258-9331-10d567f87fae%27&&userId=%27u_1013%27&apiVersion=%271.0%277` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| userId |ユーザーの一意識別子 |
| buildId |省略可能なパラメーター。ユーザーの履歴フェッチをするビルドを指定できます。 |
| apiVersion |1.0 |

**応答:**

HTTP 状態コード: 200

応答には、推奨項目ごとに 1 つのエントリが含まれています。 各エントリには、次のデータが含まれています。

* `Feed\entry\content\properties\Id` - 推奨項目の ID。
* `Feed\entry\content\properties\Name` - 項目の名前。
* `Feed\entry\content\properties\Rating` - 該当なし。
* `Feed\entry\content\properties\Reasoning` - 該当なし。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get User History</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2015-05-26T15:32:47Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">CatalogItemsThatContainATokenEntity</title>
        <updated>2015-05-26T15:32:47Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
                <d:Name m:type="Edm.String">Clara Callan</d:Name>
                <d:Rating m:type="Edm.Double">0</d:Rating>
                <d:Reasoning m:type="Edm.String"/>
                <d:Metadata m:type="Edm.String"/>
                <d:FormattedRating m:type="Edm.Double" m:null="true"/>
            </m:properties>
        </content>
    </entry>
</feed>

## <a name="14-notifications"></a>14.通知
Azure Machine Learning Recommendations は、システムで永続的なエラーが発生した際に、通知を作成します。 通知には次の 3 種類があります。

1. ビルドの失敗 - この通知は、すべてのビルドの失敗により生成されます。
2. データ取得プロセスの失敗 - この通知は、1 つのモデルの使用状況イベント処理で、最後の 5 分間に 100 を超えるエラーが発生した場合に生成されます。
3. 推奨の使用の失敗 - この通知は、1 つのモデルの推奨要求処理で、最後の 5 分間に 100 を超えるエラーが発生した場合に生成されます。

### <a name="141-get-notifications"></a>14.1. 通知の取得
すべてのモデル、または 1 つのモデルのすべての通知を取得します。

| HTTP メソッド | URI |
|:--- |:--- |
| GET |`<rootURI>/GetNotifications?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>すべてのモデルのすべての通知を取得する例:<br>`<rootURI>/GetNotifications?apiVersion=%271.0%27`<br><br>特定のモデルの通知を取得する例:<br>`<rootURI>/GetNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%277` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |省略可能なパラメーター。 省略すると、すべてのモデルのすべての通知が取得されます。 <br>有効な値: モデルの一意識別子。 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答:**

HTTP 状態コード: 200

OData XML

    The response includes one entry per notification. Each entry has the following data:
        * feed\entry\content\properties\UserName - Internal user name identification.
        * feed\entry\content\properties\ModelId - Model ID.
        * feed\entry\content\properties\Message - Notification message.
        * feed\entry\content\properties\DateCreated - Date that this notification was created in UTC format.
        * feed\entry\content\properties\NotificationType - Notification types. Values are BuildFailure, RecommendationFailure, and DataAquisitionFailure.

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get a list of Notifications for a user</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-11-04T13:24:23Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'" />
        <entry>
                <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetAListOfNotificationsForAUserEntity</title>
            <updated>2014-11-04T13:24:23Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:UserName m:type="Edm.String">515506bc-3693-4dce-a5e2-81cb3e8efb56@dm.com</d:UserName>
                    <d:ModelId m:type="Edm.String">967136e8-f868-4258-9331-10d567f87fae</d:ModelId>
                    <d:Message m:type="Edm.String">Build failed for user</d:Message>
                    <d:DateCreated m:type="Edm.String">2014-11-04T13:23:14.383</d:DateCreated>
                    <d:NotificationType m:type="Edm.String">BuildFailure</d:NotificationType>
                </m:properties>
            </content>
        </entry>
    </feed>

### <a name="142-delete-model-notifications"></a>14.2. モデル通知の削除
モデルのすべての読み取り通知を削除します。

| HTTP メソッド | URI |
|:--- |:--- |
| 削除 |`<rootURI>/DeleteModelNotifications?modelId=%<model_id>%27&apiVersion=%271.0%27`<br><br>例:<br>`<rootURI>/DeleteModelNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| modelId |モデルの一意識別子 |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

### <a name="143-delete-user-notifications"></a>14.3. ユーザー通知の削除
すべてのモデルのすべての通知を削除します。

| HTTP メソッド | URI |
|:--- |:--- |
| 削除 |`<rootURI>/DeleteUserNotifications?apiVersion=%271.0%27` |

| パラメーター名 | 有効な値 |
|:--- |:--- |
| apiVersion |1.0 |
|  | |
| 要求本文 |なし |

**応答**:

HTTP 状態コード: 200

## <a name="15-legal"></a>15.法的情報
このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報および見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。<br><br>
使用している例は、例示のみを目的に提供された、架空のものです。 実際の関連やつながりはなく、推測によるものです。<br><br>
このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利も提供するものではありません。 お客様の社内での参照目的に限り、このドキュメントをコピーし使用することができます。<br><br>
© 2015 Microsoft. All rights reserved.




<!--HONumber=Nov16_HO3-->


