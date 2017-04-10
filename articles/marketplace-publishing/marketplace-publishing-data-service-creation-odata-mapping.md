---
title: "Marketplace 用データ サービスの作成ガイド | Microsoft Docs"
description: "Azure Marketplace で購入できるデータ サービスを作成、認定、デプロイする方法について詳しく説明します。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3a632825-db5b-49ec-98bd-887138798bc4
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a853b4dbd1952ba4ea8ee68ea3ca98f588bb71a2


---
# <a name="mapping-an-existing-web-service-to-odata-through-csdl"></a>CSDL を使用した既存の Web サービスの OData へのマッピング
> [!IMPORTANT]
> **現時点では、新しいデータ サービスの発行元はオンボードされなくなりました。一覧への新しいデータ サービスの追加は承認されません。** SaaS ビジネス アプリケーションを AppSource で発行する場合、詳細については[こちら](https://appsource.microsoft.com/partners)をご覧ください。 IaaS アプリケーションまたは開発者サービスを Azure Marketplace で発行する場合、詳細については[こちら](https://azure.microsoft.com/marketplace/programs/certified/)をご覧ください。
> 
> 

この記事では、CSDL を使用して既存のサービスを OData 互換サービスにマッピングする方法について説明します。 また、サービスの呼び出し経由でクライアントからの入力要求を変換し、OData 互換フィード経由で出力 (データ) を返すマッピング ドキュメント (CSDL) を作成する方法についても説明します。 Microsoft Azure Marketplace は、OData プロトコルを使用してエンドユーザーにサービスを公開しています。 コンテンツ プロバイダー (データ所有者) が公開するサービスは、REST、SOAP など多様な形式で公開されます。

## <a name="what-is-a-csdl-and-its-structure"></a>CSDL とその構造
CSDL (Conceptual Schema Definition Language) は、Azure Marketplace に対して共通の XML 表現で Web サービスまたはデータベース サービスを記述する方法を定義した仕様です。

**要求フロー**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

**データ フロー** は反対方向です。

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**図 1** は、Azure Marketplace にアクセスして、クライアントがコンテンツ プロバイダー (発行元のサービス) のデータを取得する方法を示します。  CSDL は、コンテンツ プロバイダーのサービスと要求側クライアント間で渡される要求とデータを処理するマッピング/変換コンポーネントに使用されます。

*図 1: Azure Marketplace 経由の要求側クライアントからコンテンツ プロバイダーへのフローの詳細*

  ![図](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Azure Marketplace 拡張機能を構築する基本となる Atom、Atom Pub、OData プロトコルの背景については、 [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

このリンクからの引用:       *" Open Data プロトコル (以降 OData) の目的は、データ サービスとして公開されているリソースに対する CRUD スタイルの操作 (作成、読み取り、更新、削除) 用に REST ベースのプロトコルを提供することです。"データ サービス" とは、それぞれにゼロ個以上の "エントリ" がある 1 つ以上の "コレクション" から公開されたデータがあるエンドポイントです。エントリは型指定された名前と値のペアで構成されます。OData は、OASIS (Organization for the Advancement of Structured Information Standards) 標準に従い Microsoft から発行されています。そのため、誰でも、ロイヤリティや制限なしでサーバー、クライアント、またはツールを構築できます。"*

### <a name="three-critical-pieces-that-have-to-be-defined-by-the-csdl-are"></a>CSDL で定義する必要がある 3 つの重要な項目があります。
* サービス プロバイダーの**エンドポイント**。サービスの Web アドレス (URI) です。
* サービス プロバイダーへの入力として渡される**データ パラメーター**。データ型など、コンテンツ プロバイダーのサービスに送信されるパラメーターの定義です。
* 要求側サービスに返されるデータの**スキーマ**。コンテンツ プロバイダーのサービスから配信されるデータのスキーマです (コンテナー、collections/tables、variables/columns、データ型など)。

次の図は、クライアントが OData ステートメントを入力して (コンテンツ プロバイダーの Web サービスを呼び出して)、結果/データを取得するフローの概要です。

  ![図](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### <a name="steps"></a>手順:
1. クライアントは、XML に定義されている入力パラメーターを指定した要求を、サービス呼び出しによってAzure Marketplace に送信します。
2. CSDL は、サービス呼び出しの検証に使用されます。
   * 書式設定されたサービス呼び出しは、Azure Marketplace によってコンテンツ プロバイダー サービスに送信されます。
3. Web サービスは、HTTP 動詞 (GET など) の処理を実行します。データは Azure Marketplace に返されます。Azure Marketplace では、CSDL に定義されているマッピングを使用して、要求されたデータ (ある場合) が XML 形式でクライアントに発行されます。
4. データがある場合、クライアントには XML または JSON 形式で送信されます。

## <a name="definitions"></a>定義
### <a name="odata-atom-pub"></a>OData ATOM pub
ATOM pub の拡張機能です。各エントリで 1 行の結果セットが表されます。 エントリのコンテンツ部分は、キーと値のペアとして行の値を含むように強化されます。 詳細については、[https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/) を参照してください。

### <a name="csdl---conceptual-schema-definition-language"></a>CSDL - Conceptual Schema Definition Language
データベースで公開される関数 (SPROC) とエンティティを定義できます。 詳細については、 [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [!TIP]
> 記事が表示されない場合は、 **他のバージョン** のドロップダウンをクリックしてバージョンを選択してください。
> 
> 

### <a name="edm---entry-data-model"></a>EDM - Entry Data Model
* 概要: [http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx][OverviewLink]

[OverviewLink]:http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx
* プレビュー: [http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx][PreviewLink]

[PreviewLink]:http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx
* データ型: [http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx][DataTypesLink]

[DataTypesLink]:http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx

次に、クライアントが OData ステートメントを入力して (コンテンツ プロバイダーの Web サービスを呼び出して)、結果/データを取得するフロー (左から右方向) の詳細を示します。

  ![図](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)

## <a name="csdl-basics"></a>CSDL の基本
CSDL (Conceptual Schema Definition Language) は、Azure Marketplace に対して共通の XML 表現で Web サービスまたはデータベース サービスを記述する方法を定義した仕様です。 CSDL では、**データ ソースを Azure Marketplace にデータを渡す**ために重要な部分を記述します。 その主要部分を次に示します。

* パブリックに使用できる関数について説明したインターフェイス情報 (FunctionImport ノード)
* メッセージ要求 (入力) とメッセージ応答 (出力) すべてのデータ型情報 (EntityContainer/EntitySet/EntityType ノード)
* 使用するトランスポート プロトコルに関するバインディング情報 (Header ノード)
* 指定したサービスを特定するためのアドレス情報 (BaseURI 属性)

簡単に説明すると、CSDL とは、サービス要求側とサービス プロバイダー間のプラットフォームや言語に依存しない規約です。 クライアントは CSDL を使用して、Web サービス/データベース サービスを特定し、パブリックに使用できる関数を呼び出すことができます。

### <a name="relating-a-csdl-to-a-database-or-a-collection"></a>CSDL とデータベースまたはコレクションとの関連付け
**CSDL の仕様**

CSDL は、Web サービスを記述するための XML 文法です。 CSDL の仕様は、主に EntitySet、FunctionImport、NameSpace、EntityType という 4 つの要素に分けられます。

この抽象化をさらにわかりやすくするために、CSDL をテーブルに関連付けてみましょう。

繰り返しになりますが、

  CSDL とは、**サービス要求側**と**サービス プロバイダー**間のプラットフォームや言語に依存しない規約です。 **クライアント**は CSDL を使用して、**Web サービス/データベース サービス**を特定し、パブリックに使用できる**関数**を呼び出すことができます。

データ サービスの場合、4 つの部分の CSDL は、データベース、テーブル、列、ストアド プロシージャの観点から考えることができます。

これらを次のようにデータ サービスと関連付けます。

* EntityContainer ~= データベース
* EntitySet ~= テーブル
* EntityType ~= 列
* FunctionImport ~= ストアド プロシージャ

**使用できる HTTP 動詞**

* GET – データベースから値を取得します (コレクションを取得する)。
* POST – データを渡すときに使用します。必要に応じてデータベースから値が返されます (コレクションに新しいエントリを作成し、ID/URI を取得する)。
* DELETE – データベースからデータを削除します (コレクションを削除する)。
* PUT – データベースにデータを挿入して更新します (コレクションを置換または新規作成する)。

## <a name="metadatamapping-document"></a>メタデータ/マッピング ドキュメント
メタデータ/マッピング ドキュメントは、コンテンツ プロバイダーの既存の Web サービスをマッピングするために使用されます。このマッピングによって、Azure Marketplace システムによる OData Web サービスとして公開できます。 メタデータ/マッピング ドキュメントは CSDL に基づいており、CSDL にいくつかの拡張機能を実装することで、Azure Marketplace 経由の REST ベース Web サービスのニーズに対応しています。 拡張機能については、[http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) 名前空間を参照してください。

次に CSDL の例について説明します (以下の CSDL 例をコピーして XML エディターに貼り付け、実際のサービスに合わせて変更します。  [Azure Marketplace 発行ポータル](https://publish.windowsazure.com)でサービスを作成するときに、変更した内容を [データ サービス] タブの CSDL マッピングに貼り付けます)。

**用語:** CSDL の用語と [発行ポータル](https://publish.windowsazure.com) UI (PPUI) の用語との対応関係について。

* PPUI におけるプランの "タイトル" は、MyWebOffer と対応します。
* PPUI における MyCompany は、 **Microsoft デベロッパー センター** の UI における [Publisher Display Name](http://dev.windows.com/registration?accountprogram=azure) と対応します。
* ご利用の API は、Web サービスまたはデータ サービス (PPUI におけるプラン) に対応します。

**階層:**
 会社 (コンテンツ プロバイダー) は、API に沿って、プラン (つまりサービス) を含むオファーを所有しています。

### <a name="webservice-csdl-example"></a>WebService CSDL の例
(C# アプリケーションと同様に) Web アプリケーション エンドポイントを公開しているサービスへの接続

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID"    d:IsPrimaryKey="True" Type="Int32"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount"    Type="Double"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"    Type="String"    Nullable="false" d:Map="./City"/>
        <Property Name="State"    Type="String"    Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime"    Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [!TIP]
> 他の CSDL Web サービスの例については、 [既存の Web サービスを CSDL で OData にマッピングする例](marketplace-publishing-data-service-creation-odata-mapping-examples.md)
> 
> 

### <a name="dataservice-csdl-example"></a>DataService CSDL の例
エンドポイントとしてデータベース テーブルまたビューを公開するサービスに接続します。次の例では、データベースに基づく API CSDL の 2 つの API を示します (テーブルではなくビューを使用できます)。

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## <a name="see-also"></a>関連項目
* 特定のノードとそのパラメーターについて知りたい場合は、定義と説明、例、ユース ケースのコンテキストなどが記載された、この [データ サービスの OData マッピング ノード](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) に関する記事を参照してください。
* 例を確認したい場合は、 [データ サービスの OData マッピングの例](marketplace-publishing-data-service-creation-odata-mapping-examples.md) に関する記事でサンプル コードを参照し、コード構文とコンテキストを学習してください。
* データ サービスを Azure Marketplace に発行するために指定のパスに戻る場合は、こちらの [データ サービスの発行ガイド](marketplace-publishing-data-service-creation.md)を参照してください。




<!--HONumber=Dec16_HO2-->


