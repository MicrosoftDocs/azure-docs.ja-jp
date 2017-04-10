---
title: "Azure API Management での API のインポートの制限事項と既知の問題 | Microsoft Docs"
description: "Open API、WSDL、または WADL 形式での Azure API Management へのインポートに関する既知の問題と制限事項の詳細です。"
services: api-management
documentationcenter: 
author: mattfarm
manager: vlvinogr
editor: 
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: apipm
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: ac799d66b5038c207413086b0fa71239ff2a332f
ms.lasthandoff: 03/15/2017


---
# <a name="api-import-restrictions-and-known-issues"></a>API のインポートに関する制限事項と既知の問題
## <a name="about-this-list"></a>このリストについて
Azure API Management API にお客様の API をできる限りシームレスかつ問題なくインポートできるようにあらゆる努力をしていますが、場合によっては制限をかけるほか、インポートを正常に行うことができるように修正する必要のある問題が判明することがあります。 この記事ではこうした内容について、API のインポート形式別に説明します。

## <a name="open-api"> </a>Open API/Swagger
通常、Open API ドキュメントのインポートでエラーが発生した場合は、新しい Azure Portal のデザイナー ([Design] \(設計) - [フロントエンド] - [Open API Specification Editor] \(Open API 仕様エディター))、または <a href="http://www.swagger.io">Swagger Editor</a> などのサードパーティ ツールを使用して、そのドキュメントが有効であることを確認してください。

* **ホスト名**: ホスト名属性が必要です。
* **ベース パス**: ベース パス属性が必要です。
* **スキーム**: スキーム配列が必要です。 

## <a name="wsdl"> </a>WSDL
WSDL ファイルは SOAP パススルー API の作成に使用するものであり、SOAP-to-REST API のバックエンドとして機能します。

* **WSDL:Import**: 現時点では、この属性を使用する API はサポートされていません。 インポートした要素を 1 つのドキュメントに結合する必要があります。
* **複数の部分を持つメッセージ**: 現時点ではサポートされていません。
* **WCF wsHttpBinding**: Windows Communication Foundation を使用して作成した SOAP サービスでは、basicHttpBinding を使用する必要があります。wsHttpBinding はサポートされていません。
* **MTOM**: MTOM を使用するサービスは、動作する ”<em>可能性があります</em>”。 現時点では、正式なサポートは提供されていません。
* **再帰**: 再帰的に定義されている (例: 自身の配列を参照している) 型はサポートされていません。

## <a name="wadl"> </a>WADL
現時点では、WADL のインポートに関する既知の問題はありません。


[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md

