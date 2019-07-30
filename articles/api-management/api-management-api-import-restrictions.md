---
title: Azure API Management での API のインポートの制限事項と既知の問題 | Microsoft Docs
description: Open API、WSDL、または WADL 形式での Azure API Management へのインポートに関する既知の問題と制限事項の詳細です。
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: af550d3cdf359fc79b3cc2c799e531e5ec491c4e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613630"
---
# <a name="api-import-restrictions-and-known-issues"></a>API のインポートに関する制限事項と既知の問題

## <a name="about-this-list"></a>このリストについて

API をインポートするときに、制限や ID の問題が発生し、修正しないと正常にインポートできない場合があります。 この記事ではこうした内容について、API のインポート形式別に説明します。

## <a name="open-api"> </a>API を開く/Swagger

Open API ドキュメントのインポートでエラーが発生した場合は、事前に検証を行っていることを確認します。 これは、Azure portal でデザイナーを使用する ([デザイン] - [フロント エンド] - [OpenAPI 仕様エディター]) か、<a href="https://editor.swagger.io">Swagger Editor</a> などのサードパーティ ツールを使用して実行できます。

### <a name="open-api-general"> </a>全般

-   パスとクエリの両方の間で必要なパラメーターは、一意の名前が必要です。 (OpenAPI では、パラメーター名は、パス、クエリ、ヘッダーなどの場所でのみ一意である必要があります。 ただし、API Management では、パスのパラメーターとクエリのパラメーターの両方によって操作を区別できます (これは OpenAPI ではサポートされていません)。 このため、URL テンプレート　全体の中で、パラメーターの名前を一意にする必要があります。)
-   **\$ref** ポインターは、外部ファイルを参照できません。
-   サポートされている拡張子は、**x-ms-paths** と **x-servers** のみです。
-   カスタム拡張子は、インポート時に無視され、エクスポートでは保存されず、保持もされません。
-   **再帰** - API Management では、再帰的に定義される定義はサポートされていません (たとえば、それ自体を参照するスキーマ)。
-   ソース ファイルの URL (使用できる場合) が、相対的なサーバーの URL に適用されます。

### <a name="open-api-v2"> </a>OpenAPI バージョン 2

-   JSON 形式のみがサポートされています。

### <a name="open-api-v3"> </a>OpenAPI バージョン 3

-   多数の**サーバー**が指定された場合、API Management では、最初の HTTPS URL の選択が試行されます。 HTTPS URL がない場合は、最初の HTTP URL が使用されます。 HTTP URL がない場合、サーバーの URL は空になります。
-   **Examples** はサポートされていませんが、**example** はサポートされています。
-   **Multipart/form-data** はサポートされていません。

> [!IMPORTANT]
> OpenAPI のインポートに関する重要な情報とヒントについては、この[ドキュメント](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/)を参照してください。

## <a name="wsdl"> </a>WSDL

WSDL ファイルは、SOAP パススルー API と SOAP-to-REST API の作成に使用されます。

-   **SOAP バインド** - "document" および "literal" エンコード スタイルの SOAP バインドのみがサポートされています。 "rpc" スタイルまたは SOAP エンコードはサポートされていません。
-   **WSDL:Import** - この属性はサポートされていません。 インポートを 1 つのドキュメントに結合する必要があります。
-   **複数の部分で構成されたメッセージ** - この種のメッセージはサポートされていません。
-   **WCF wsHttpBinding** - Windows Communication Foundation を使用して作成した SOAP サービスでは、basicHttpBinding を使用する必要があります。wsHttpBinding はサポートされていません。
-   **MTOM** - MTOM を使用するサービスは、動作する "<em>可能性があります</em>"。 現時点では、正式なサポートは提供されていません。
-   **再帰** - APIM では、再帰的に定義される型 (自身の配列を参照する型など) はサポートされていません。
-   **複数の名前空間** - 1 つのスキーマで複数の名前空間を使用できますが、メッセージ部分を定義するために使用できるのはターゲット名前空間だけです。 ターゲット以外の、他の入力または出力要素の定義に使用される名前空間は保持されません。 そのような WSDL ドキュメントをインポートできますが、エクスポート時に、すべてのメッセージ部分は WSDL のターゲット名前空間になります。
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

## <a name="wadl"> </a>WADL

現時点では、WADL のインポートに関する既知の問題はありません。
