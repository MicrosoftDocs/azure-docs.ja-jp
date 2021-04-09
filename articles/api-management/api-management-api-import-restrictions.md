---
title: API 形式のサポートの制限事項と詳細
titleSuffix: Azure API Management
description: Azure API Management での Open API、WSDL、および WADL 形式のサポートに関する既知の問題と制限事項の詳細です。
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 6a53cc2b2ec6d46b4bde54af58b4e5542ff6cf79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91932636"
---
# <a name="api-import-restrictions-and-known-issues"></a>API のインポートに関する制限事項と既知の問題

## <a name="about-this-list"></a>このリストについて

API をインポートするときに、制限や ID の問題が発生し、それを修正しないと正常にインポートを実行することができない場合があります。 この記事ではこうした制限について、API のインポート形式別に説明します。 また、OpenAPI のエクスポートの仕組みについても説明します。

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>OpenAPI/Swagger のインポートの制限事項

Open API ドキュメントのインポートでエラーが発生した場合は、事前に検証を行っていることを確認します。 これは、Azure portal でデザイナーを使用する ([デザイン] - [フロント エンド] - [OpenAPI 仕様エディター]) か、<a href="https://editor.swagger.io">Swagger Editor</a> などのサードパーティ ツールを使用して実行できます。

### <a name="general"></a><a name="open-api-general"> </a>全般

-   パスとクエリの両方の間で必要なパラメーターは、一意の名前が必要です。 (OpenAPI では、パラメーター名は、パス、クエリ、ヘッダーなどの場所でのみ一意である必要があります。 ただし、API Management では、パスのパラメーターとクエリのパラメーターの両方によって操作を区別できます (これは OpenAPI ではサポートされていません)。 このため、URL テンプレート　全体の中で、パラメーターの名前を一意にする必要があります。)
-   `\$ref` ポインターは、外部ファイルを参照できません。
-   サポートされている拡張機能は `x-ms-paths` と `x-servers` のみです。
-   カスタム拡張子は、インポート時に無視され、エクスポートでは保存されず、保持もされません。
-   `Recursion` - API Management では、再帰的に定義される定義はサポートされていません (たとえば、それ自体を参照するスキーマ)。
-   ソース ファイルの URL (使用できる場合) が、相対的なサーバーの URL に適用されます。
-   セキュリティ定義は無視されます。
-   API 操作に対するインライン スキーマ定義はサポートされていません。 スキーマ定義は、API スコープで定義され、API 操作の要求または応答のスコープで参照できます。
-   定義された URL パラメーターは URL テンプレートに含まれている必要があります。
-   `server` オブジェクトは API 操作レベルでサポートされていません。
-   API によって返される MIME 型を説明する `Produces` キーワードはサポートされていません。 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI バージョン 2

-   JSON 形式のみがサポートされています。

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI バージョン 3

-   多数の `servers` が指定された場合、API Management では、最初の HTTPS URL の選択が試行されます。 HTTPS URL がない場合は、最初の HTTP URL が使用されます。 HTTP URL がない場合、サーバーの URL は空になります。
-   `Examples` はサポートされていませんが、`example` はサポートされています。

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI のインポート、更新、およびエクスポートのメカニズム

### <a name="general"></a><a name="open-import-export-general"> </a>全般

-   API Management サービスからエクスポートされた API 定義は、API Management サービスでホストされている API を呼び出す必要がある API Management サービス外部のアプリケーションを主に対象としています。 エクスポートされた API 定義は、同じまたは別の API Management サービスに再度インポートするためのものではありません。 さまざまなサービスまたは環境にわたる API 定義の構成管理については、Git での API Management サービスの使用に関するドキュメントを参照してください。 

### <a name="add-new-api-via-openapi-import"></a>OpenAPI のインポートを使用して新しい API を追加する

OpenAPI ドキュメントで見つかった操作ごとに、新しい操作が作成され、Azure リソース名と表示名がそれぞれ `operationId` と `summary` に設定されます。 `operationId` 値は、以下で説明する規則に従って正規化されます。 `summary` 値はそのままインポートされ、その長さは 300 文字に制限されます。

`operationId` が指定されていない場合 (つまり、存在しないか、`null` であるか、空である)、Azure リソース名の値は、HTTP メソッドとパス テンプレートを組み合わせて生成されます (たとえば、`get-foo`)。

`summary` が指定されていない場合 (つまり、存在しないか、`null` であるか、空である)、`display name` 値は `operationId` に設定されます。 `operationId` が指定されていない場合、表示名の値は、HTTP メソッドとパス テンプレートを組み合わせて生成されます (たとえば、`Get - /foo`)。

### <a name="update-an-existing-api-via-openapi-import"></a>OpenAPI のインポートを使用して既存の API を更新する

インポート時に、既存の API は、OpenAPI ドキュメントで説明されている API に一致するように変更されます。 OpenAPI ドキュメントの各操作は、その `operationId` 値を既存の操作の Azure リソース名と比較して、既存の操作と照合されます。

一致が見つかった場合、既存の操作のプロパティは "インプレースで" 更新されます。

一致が見つからない場合は、前のセクションで説明した規則を使用して新しい操作が作成されます。 新しい操作ごとに、インポートにより、同じ HTTP メソッドとパス テンプレートを使用する既存の操作からのポリシーのコピーが試みられます。

一致しない既存の操作はすべて削除されます。

インポートをより予測しやすくするには、次のガイドラインに従ってください。

- 必ず、すべての操作に対して `operationId` プロパティを指定してください。
- 最初のインポート後に `operationId` を変更しないでください。
- `operationId` と HTTP メソッドまたはパス テンプレートを同時に変更しないでください。

### <a name="export-api-as-openapi"></a>API を OpenAPI としてエクスポートする

操作ごとに、その Azure リソース名が `operationId` としてエクスポートされ、表示名が `summary` としてエクスポートされます。
operationId の正規化規則

- 小文字に変換します。
- 英数字以外の文字の各シーケンスを単一のダッシュに置き換えます。たとえば、`GET-/foo/{bar}?buzz={quix}` は `get-foo-bar-buzz-quix-` に変換されます。
- 両側のダッシュをトリムします。たとえば `get-foo-bar-buzz-quix-` は `get-foo-bar-buzz-quix` になります
- リソース名の最大制限より 4 文字少ない 76 文字に収まるように切り捨てます。
- 残りの 4 文字は、必要に応じて `-1, -2, ..., -999` の形式で、重複除去サフィックスに使用します。


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL ファイルは、SOAP パススルー API と SOAP-to-REST API の作成に使用されます。

-   **SOAP バインド** - "document" および "literal" エンコード スタイルの SOAP バインドのみがサポートされています。 "rpc" スタイルまたは SOAP エンコードはサポートされていません。
-   **WSDL:Import** - この属性はサポートされていません。 インポートを 1 つのドキュメントに結合する必要があります。
-   **複数の部分で構成されたメッセージ** - この種のメッセージはサポートされていません。
-   **WCF wsHttpBinding** - Windows Communication Foundation を使用して作成した SOAP サービスでは、basicHttpBinding を使用する必要があります。wsHttpBinding はサポートされていません。
-   **MTOM** - MTOM を使用するサービスは、動作する "<em>可能性があります</em>"。 現時点では、正式なサポートは提供されていません。
-   **再帰** - APIM では、再帰的に定義される型 (自身の配列を参照する型など) はサポートされていません。
-   **複数の名前空間** - 1 つのスキーマで複数の名前空間を使用できますが、メッセージ部分を定義するために使用できるのはターゲット名前空間だけです。 ターゲット以外の、他の入力または出力の要素の定義に使用される名前空間は保持されません。 そのような WSDL ドキュメントをインポートできますが、エクスポート時に、すべてのメッセージ部分は WSDL のターゲット名前空間になります。
-   **配列** - SOAP から REST への変換では、次の例に示すラップされた配列のみがサポートされています。

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

現時点では、WADL のインポートに関する既知の問題はありません。
