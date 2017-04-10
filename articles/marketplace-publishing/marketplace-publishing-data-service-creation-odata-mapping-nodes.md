---
title: "Marketplace 用データ サービスの作成ガイド | Microsoft Docs"
description: "Azure Marketplace で購入できるデータ サービスを作成、認定、デプロイする方法について詳しく説明します。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 107baab2-5828-4158-abdf-59a580c80d37
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
translationtype: Human Translation
ms.sourcegitcommit: f8b0917b6eb0295641360c4e0a80e81100809f6e
ms.openlocfilehash: e3ce01d20f6b47c6fe68fdbfe31679cc2c92f2e7


---
# <a name="understanding-the-nodes-schema-for-mapping-an-existing-web-service-to-odata-through-csdl"></a>CSDL を使用して既存の Web サービスを OData にマップするためのノード スキーマについて
> [!IMPORTANT]
> **現時点では、新しいデータ サービスの発行元はオンボードされなくなりました。一覧への新しいデータ サービスの追加は承認されません。** SaaS ビジネス アプリケーションを AppSource で発行する場合、詳細については[こちら](https://appsource.microsoft.com/partners)をご覧ください。 IaaS アプリケーションまたは開発者サービスを Azure Marketplace で発行する場合、詳細については[こちら](https://azure.microsoft.com/marketplace/programs/certified/)をご覧ください。
>
>

このドキュメントでは、OData プロトコルを CSDL にマップするためのノード構造について説明します。 ノード構造は整形式 XML である点に注意してください。 このため、OData マッピングを設計する際は、ルート、親、子のスキーマが適用されます。

## <a name="ignored-elements"></a>無視される要素
以下に示すのは、Web サービスのメタデータのインポート時に Azure Marketplace バックエンドで使用されない上位の CSDL 要素 (XML ノード) です。 これらは表示される場合がありますが、無視されます。

| 要素 | Scope |
| --- | --- |
| Using 要素 |ノード、サブ ノード、およびすべての属性 |
| Documentation 要素 |ノード、サブ ノード、およびすべての属性 |
| ComplexType 要素 |ノード、サブ ノード、およびすべての属性 |
| Association 要素 |ノード、サブ ノード、およびすべての属性 |
| 拡張プロパティ |ノード、サブ ノード、およびすべての属性 |
| EntityContainer 要素 |次の属性のみが無視されます: *extends* および *AssociationSet* |
| スキーマ |次の属性のみが無視されます: *Namespace* |
| FunctionImport 要素 |次の属性のみが無視されます: *Mode* (既定値は ln と見なされます) |
| EntityType |次のサブ ノードのみが無視されます: *Key* および *PropertyRef* |

各 CSDL XML ノードに対する変更 (追加された要素と無視される要素) について、次で詳しく説明します。

## <a name="functionimport-node"></a>FunctionImport ノード
FunctionImport ノードは、エンド ユーザーにサービスを公開する 1 つの URL (エントリ ポイント) を表します。 このノードでは、URL を指定する方法、エンド ユーザーに提供するパラメーターの種類、およびこれらのパラメーターを提供する方法を記述できます。

このノードの詳細については、[こちら][MSDNFunctionImportLink](https://msdn.microsoft.com/library/cc716710.aspx) をご覧ください。

FunctionImport ノードで公開されている追加の属性 (または属性への追加事項) を次に示します。

**d:BaseUri**  -
 Marketplace に公開される REST リソースの URI テンプレートです。 Marketplace では、テンプレートを使用して、REST Web サービスに対するクエリを作成します。 URI テンプレートには、パラメーターのプレースホルダーが {parameterName} の形式で含まれています。parameterName はパラメーターの名前です。 例: apiVersion の場合は {apiVersion} になります。
パラメーターは、URI パラメーターとして、または URI パスの一部として表記できます。 パス内に表記する場合は、常に必須になります (null 許容型としてマークすることはできません)。 *例:* `d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Name** - インポートされる関数の名前。  CSDL で定義されている他の名前と同じ名前にすることはできません。  例: Name="GetModelUsageFile"

**EntitySet** *(省略可能)* - 関数がエンティティ型のコレクションを返す場合に、**EntitySet** の値にコレクションが所属するエンティティ セットを指定する必要があります。 そうでない場合は、 **EntitySet** 属性を使用しないでください。 *例:* `EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType** *(省略可能)* - URI によって返される要素の型を指定します。  関数が値を返さない場合は、この属性を使用しないでください。 次の型がサポートされています。

* **Collection (<Entity type name>)**: 定義されたエンティティ型のコレクションを指定します。 名前は、EntityType ノードの Name 属性内にあります。 たとえば、Collection(WXC.HourlyResult) のようになります。
* **Raw (<mime type>)**: ユーザーに返される未加工のドキュメント/BLOB を指定します。 たとえば、Raw(image/jpeg) のようになります。その他の例:

  * ReturnType="Raw(text/plain)"
  * ReturnType="Collection(sage.DeleteAllUsageFilesEntity)"*

**d:Paging** - REST リソースでページングが処理される方法を指定します。 パラメーター値は中かっこで囲みます。例: page={$page}&itemsperpage={$size}。使用できるオプションは次のとおりです。

* **None:** ページングは使用できません。
* **Skip:** 論理的な "スキップ" と "取得" (降順) でページングを表します。 m 個の要素をスキップした後、次の n 個の要素を返します。 パラメーター値: $skip
* **Take:** Take は次の n 個の要素を返します。 パラメーター値: $take
* **PageSize:** 論理的なページとサイズ (1 ページあたりの項目数) でページングを表します。 Page は返される現在のページを表します。 パラメーター値: $page
* **Size:** Size は各ページの返される項目の数を表します。 パラメーター値: $size

**d:AllowedHttpMethods** *(省略可能)* - REST リソースによって処理される動詞を指定します。 また、受け入れられる動詞を、指定した値のみに制限します。  既定値 = POST  *例:* `d:AllowedHttpMethods="GET"` 使用できるオプションは次のとおりです。

* **GET:** 通常、値を返すために使用します。
* **POST:** 通常、新しい値を挿入するために使用します。
* **PUT:** 通常、値を更新するために使用します。
* **DELETE:** データを削除するために使用します。

FunctionImport ノード内の追加の (CSDL ドキュメントに記載されていない) 子ノードは次のとおりです。

**d:RequestBody** *(省略可能)* - 要求本文を使用して、この要求では本文の送信が想定されていることを示します。 パラメーターは、要求本文内で指定できます。 パラメーターは中かっこで囲みます。たとえば、{parameterName} のようになります。 これらのパラメーターは、コンテンツ プロバイダーのサービスに転送される本文へのユーザー入力からマップされます。 requestBody 要素には、httpMethod という名前の属性があります。 この属性では、2 つの値を使用できます。

* **POST:** 要求が HTTP POST である場合に使用します。
* **GET:** 要求が HTTP GET である場合に使用します。

    例:

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** および **d:Namespace** - このノードには、関数インポート (URI エンドポイント) によって返される XML で定義された名前空間が記述されます。 バックエンド サービスによって返される XML には、返すコンテンツを区別するために、任意の数の名前空間が含まれる場合があります。 **d:Map または d:Match の XPath クエリで使用されている場合は、これらの名前空間がすべて一覧で記述される必要があります。**  d:Namespaces ノードには、d:Namespace ノードのセット/一覧が含まれています。 ノードごとに、バックエンド サービスの応答で使用される名前空間が 1 つ記述されます。 d:Namespace ノードの属性を次に示します。

* **d:Prefix:** 名前空間のプレフィックス。サービスによって返される XML 結果に記述されている形式。例: f:FirstName、f:LastName (f はプレフィックス)
* **d:Uri:** 結果ドキュメントで使用されている名前空間の完全 URI。 プレフィックスが実行時に解決される値を表します。

**d:ErrorHandling** *(省略可能)* - このノードには、エラー処理のための条件が含まれています。 各条件が、コンテンツ プロバイダーのサービスによって返される結果に対して検証されます。 提示された HTTP エラー コードに条件が一致する場合、エンド ユーザーにエラー メッセージが返されます。

**d:ErrorHandling** *(省略可能)* および **d:Condition** *(省略可能)* - Condition ノードには、コンテンツ プロバイダーのサービスによって返される結果で検証される条件が 1 つ保持されています。 次に示すのは、 **必須** の属性です。

* **d:Match:** コンテンツ プロバイダーの出力 XML に特定のノード/値が存在するかどうかを検証する XPath 式。 XPath は出力に対して実行され、条件が一致する場合は true を返し、一致しない場合は false を返します。
* **d:HttpStatusCode:** 条件が一致した場合に Marketplace によって返される HTTP 状態コード。 Marketplace は、HTTP 状態コードを通じてユーザーにエラーを伝えます。 HTTP 状態コードの一覧については、http://en.wikipedia.org/wiki/HTTP_status_code をご覧ください
* **d:ErrorMessage:** HTTP 状態コードと共にエンド ユーザーに返されるエラー メッセージ。 これは、機密情報が含まれていない、わかりやすいエラー メッセージです。

**d:Title** *(省略可能)* - 関数のタイトルを記述できます。 タイトルの値は次のものから取得されます。

* サービス要求から返される応答内のタイトルを検索する場所を指定する省略可能なマップ属性 (XPath)。
* ノードの値として指定されたタイトル。

**d:Rights** *(省略可能)* - 関数に関連付けられている権限 (例: 著作権)。 権限の値は次のものから取得されます。

* サービス要求から返される応答内の権限を検索する場所を指定する省略可能なマップ属性 (XPath)。
* ノードの値として指定された権限。

**d:Description** *(省略可能)* - 関数の簡単な説明。 説明の値は次のものから取得されます。

* サービス要求から返される応答内の説明を検索する場所を指定する省略可能なマップ属性 (XPath)。
* ノードの値として指定された説明。

**d:EmitSelfLink** - *「例: 返されたデータの "ページング" のための FunctionImport」を参照してください。*

**d:EncodeParameterValue** - OData のオプションの拡張

**d:QueryResourceCost** - OData のオプションの拡張

**d:Map** - OData のオプションの拡張

**d:Headers** - OData のオプションの拡張

**d:Headers** - OData のオプションの拡張

**d:Value** - OData のオプションの拡張

**d:HttpStatusCode** - OData のオプションの拡張

**d:ErrorMessage** - OData のオプションの拡張

## <a name="parameter-node"></a>Parameter ノード
このノードは、FunctionImport ノードで指定された URI テンプレート/要求本文の一部として公開されている 1 つのパラメーターを表します。

"Parameter 要素" ノードに関する詳細情報が記載された役立つページが[こちら](http://msdn.microsoft.com/library/ee473431.aspx)にあります (必要に応じて、**[その他のバージョン]** ボックスの一覧から別のバージョンのドキュメントを選択して表示できます)。 *例:* `<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| パラメーター属性 | 必須 | 値 |
| --- | --- | --- |
| 名前 |はい |パラメーターの名前。 大文字と小文字が区別されます。  BaseUri の大文字小文字と照合されます。 **例:** `<Property Name="IsDormant" Type="Byte" />` |
| 型 |はい |パラメーターの型。 この値には、 **EDMSimpleType** か、モデルのスコープ内にある複合型を指定する必要があります。 詳細については、6 つ目のトピック「サポートされているパラメーター/プロパティの型」を参照してください。  (大文字と小文字が区別されます。 先頭の文字は大文字で、残りは小文字です。)[概念モデルの型 (CSDL)][MSDNParameterLink](http://msdn.microsoft.com/library/bb399548.aspx) に関するページもご覧ください。 **例:** `<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Mode |なし |パラメーターが入力、出力、入力/出力のいずれであるかに応じて、**In**、Out、または InOut  (Azure Marketplace で使用できるのは "In" のみです)。**例:** `<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength |いいえ |パラメーターの許容される最大長 **例:** `<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Precision |いいえ |パラメーターの説明 **例:** `<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| スケール |いいえ |パラメーターの小数点以下桁数 **例:** `<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

CSDL の仕様に追加された属性を次に示します。

| パラメーター属性 | Description |
| --- | --- |
| **d:Regex** *(省略可能)* |パラメーターの入力値を検証するために使用する正規表現の式。 入力値が式と一致しない場合、値は拒否されます。 これを利用して、入力可能な値のセットとして、たとえば ^[0-9]+?$ を指定して数字のみに制限することもできます。 **例:** `<Parameter Name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters" d:SampleValues="George |
| **d:Enum** *(省略可能)* |パラメーターに有効な値のパイプ区切り一覧。 値の型は、パラメーターの定義済みの型に一致する必要があります。 例: `english |
| **d:Nullable** *(省略可能)* |パラメーターを null に指定できるかどうかを定義できます。 既定値は true です。 ただし、URI テンプレートでパスの一部として公開されているパラメーターを null に指定することはできません。 これらのパラメーターでこの属性が false に設定されている場合、ユーザーの入力は上書きされます。 **例:** `<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(省略可能)* |UI でクライアントにメモとして表示するサンプル値。  パイプで区切られたリストを使用して、複数の値を追加できます (つまり、`a) |

## <a name="entitytype-node"></a>EntityType ノード
このノードは、Marketplace からエンド ユーザーに返される型の 1 つを表します。 また、コンテンツ プロバイダーのサービスによって返される出力とエンド ユーザーに返される値のマッピングも含まれています。

このノードの詳細については、 [こちら](http://msdn.microsoft.com/library/bb399206.aspx) をご覧ください (必要に応じて、 **[その他のバージョン]** ボックスの一覧から別のバージョンのドキュメントを選択して表示できます)。

| 属性名 | 必須 | 値 |
| --- | --- | --- |
| 名前 |はい |エンティティ型の名前。 **例:** `<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType |いいえ |定義するエンティティ型の基本の型である別のエンティティ型の名前。 **例:** `<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

CSDL の仕様に追加された属性を次に示します。

**d:Map** - サービスの出力に対して実行される XPath 式。 ここでは、繰り返し処理されるエントリ ノードのセットが存在する ATOM フィードのように、サービスの出力に、繰り返し処理される要素のセットが含まれていることを前提にしています。 各繰り返しノードには 1 つのレコードが格納されます。 XPath は、各レコードの値を保持しているコンテンツ プロバイダーのサービス結果内の繰り返しノードをポイントするように指定されます。 サービスの出力例:

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

各 bar ノードが出力内で繰り返しノードになっており、エンド ユーザーに返される実際のコンテンツが含まれているため、XPath 式は /foo/bar のようになります。

**Key** - Marketplace では、この属性は無視されます。 一般に、REST ベースの Web サービスでは主キーは公開されません。

## <a name="property-node"></a>Property ノード
このノードには、レコードのプロパティの 1 つが含まれています。

このノードの詳細については、[http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) を参照してください (必要に応じて **[その他のバージョン]** ボックスの一覧から別のバージョンのドキュメントを選択して表示できます)。*例:*
        `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"     Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| AttributeName | 必須 | 値 |
| --- | --- | --- |
| 名前 |はい |プロパティの名前。 |
| 型 |はい |プロパティ値の型。 プロパティ値の型は、 **EDMSimpleType** か、モデルのスコープ内にある (完全修飾名で表された) 複合型を指定する必要があります。 詳細については、「概念モデルの型 (CSDL)」を参照してください。 |
| Nullable |なし |プロパティに null 値を指定できるかどうかに応じて、**True** (既定値) または **False**。 注: [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) 名前空間で示された CSDL のバージョンでは、複合型プロパティは Nullable="False" である必要があります。 |
| DefaultValue |いいえ |プロパティの既定値。 |
| MaxLength |いいえ |プロパティ値の最大長。 |
| FixedLength |いいえ |プロパティの値が固定長文字列として格納されているかどうかに応じて、**True** または **False**。 |
| Precision |いいえ |数値に保持する最大桁数を表します。 |
| スケール |いいえ |数値に保持する小数点以下の最大桁数を表します。 |
| Unicode |なし |プロパティの値が Unicode 文字列として格納されているかどうかに応じて、**True** または **False**。 |
| Collation |いいえ |データ ソースで使用される照合順序を指定する文字列。 |
| ConcurrencyMode |なし |**None** (既定値) または **Fixed**。 値が **Fixed**に設定されている場合、プロパティ値はオプティミスティック同時実行制御チェックで使用されます。 |

CSDL の仕様に追加されたその他の属性を次に示します。

**d:Map** - サービスに対して実行され、出力の特定のプロパティを抽出する XPath 式。 指定された XPath は、EntityType ノードの XPath で選択された繰り返しノードへの相対パスになります。 また、絶対 XPath を指定して、各出力ノードに静的リソースを含めることもできます。たとえば、元のサービス出力に 1 回だけ表記されている著作権情報を、OData 出力の各行に表示することができます。 サービスの例:

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

この例でコンテンツ プロバイダーのサービスから baz0 ノードを取得する XPath 式は ./bar/baz0 になります。

**d:CharMaxLength** - 文字列型の最大長を指定できます。 「DataService CSDL の例」を参照してください。

**d:IsPrimaryKey** - 列がテーブル/ビューの主キーであるかどうかを示します。 「DataService CSDL の例」を参照してください。

**d:isExposed** - テーブルのスキーマが公開されているかどうかを指定します (通常は true)。 「DataService CSDL の例」を参照してください。

**d:IsView** *(省略可能)* - これがテーブルではなくビューに基づいている場合に true になります。  「DataService CSDL の例」を参照してください。

**d:Tableschema** - 「DataService CSDL の例」を参照してください。

**d:ColumnName** - テーブル/ビューの列の名前を指定します。  「DataService CSDL の例」を参照してください。

**d:IsReturned** - サービスがこの値をクライアントに公開しているかどうかを指定するブール値です。  「DataService CSDL の例」を参照してください。

**d:IsQueryable** - 列をデータベース クエリで使用できるかどうかを指定するブール値です。   「DataService CSDL の例」を参照してください。

**d:OrdinalPosition** - テーブルまたはビューにおける列の表示位置を表す x。x は 1 からテーブルの列数の間の数値です。  「DataService CSDL の例」を参照してください。

**d:DatabaseDataType** - SQL データ型など、データベース内の列のデータ型です。 「DataService CSDL の例」を参照してください。

## <a name="supported-parametersproperty-types"></a>サポートされているパラメーター/プロパティの型
パラメーターおよびプロパティのサポートされている型を次に示します  (大文字と小文字が区別されます)。

| プリミティブ型 | Description |
| --- | --- |
| Null |値がないことを表します。 |
| Boolean |2 値論理という数学的概念を表します。 |
| Byte |8 ビットの符号なし整数値。 |
| DateTime |日付と時刻を、西暦 1753 年 1 月 1 日深夜 12:00:00  から西暦 9999 年 12 月 31 日午後 11:59:59 までの値で表します。 |
| Decimal |固定の有効桁数および小数点以下桁数を指定して数値を表します。 この型は、負の 10^255 + 1 から正の 10^255 -1 の間の数値を記述できます。 |
| Double |およそ ± 2.23e -308 から ± 1.79e +308 の範囲の値を表すことができる有効桁数 15 桁の浮動小数点数を表します。 **Exel のエクスポートに関する問題が発生するため、10 進数を使用します。** |
| Single |およそ ± 1.18e -38 から ± 3.40e +38 の範囲の値を表すことができる有効桁数 7 桁の浮動小数点数を表します。 |
| Guid |16 バイト (128 ビット) の一意の識別子値を表します。 |
| Int16 |16 ビットの符号付き整数値を表します。 |
| Int32 |32 ビットの符号付き整数値を表します。 |
| Int64 |64 ビットの符号付き整数値を表します。 |
| String |固長定または可変長の文字データを表します。 |

## <a name="see-also"></a>関連項目
* 全体的な OData マッピングのプロセスと目的を理解したい場合は、こちらの [データ サービスの OData マッピング](marketplace-publishing-data-service-creation-odata-mapping.md) に関する記事を読んで、定義、構造、手順を確認してください。
* 例を確認したい場合は、 [データ サービスの OData マッピングの例](marketplace-publishing-data-service-creation-odata-mapping-examples.md) に関するページでサンプル コードを参照し、コード構文とコンテキストを学習してください。
* データ サービスを Azure Marketplace に発行するために指定のパスに戻る場合は、こちらの [データ サービスの発行ガイド](marketplace-publishing-data-service-creation.md)を参照してください。



<!--HONumber=Nov16_HO3-->


