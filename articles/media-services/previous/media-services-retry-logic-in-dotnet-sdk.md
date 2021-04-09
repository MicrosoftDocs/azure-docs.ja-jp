---
title: Media Services SDK for .NET の再試行ロジック | Microsoft Docs
description: このトピックでは、Media Services SDK for .NET の再試行ロジックの概要を説明します。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: feda0ccfa1dc6d02153b98ad084bd775a055e9e3
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012906"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Media Services SDK for .NET の再試行ロジック

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Microsoft Azure サービスの使用中に、一時的な障害が発生することがあります。 一時的な障害が発生しても、多くの場合は、何度か再試行すれば操作に成功します。 Media Services SDK for .NET には、例外とエラーに関連する一時的な障害を処理するための再試行ロジックが実装されています。そのような例外とエラーは、Web 要求、クエリの実行、変更の保存、ストレージ操作によって発生します。  既定では、Media Services SDK for .NET はアプリケーションに例外を再びスローする前に、再試行を 4 回実行します。 アプリケーションのコードは、この例外を正しく処理する必要があります。  

 Web Request、Storage、Query、SaveChanges ポリシーの簡単なガイドラインを次に示します。  

* Storage ポリシーは、Blob Storage の操作 (アセット ファイルのアップロードまたはダウンロード) に使用されます。  
* Web Request ポリシーは、一般的な Web 要求 (たとえば、認証トークンを取得してユーザー クラスター エンドポイントを解決する) に使用されます。  
* Query ポリシーは、REST からのエンティティのクエリ実行に使用されます (たとえば、mediaContext.Assets.Where(…))。  
* SaveChanges ポリシーは、サービス内のデータを変更する操作に使用されます (たとえば、エンティティを更新するエンティティの作成、操作のためのサービス関数の呼び出しなど)。  
  
  このトピックでは、Media Services SDK for .NET の再試行ロジックが処理する例外の種類とエラー コードについて説明します。  

## <a name="exception-types"></a>例外の種類
次の表では、一時的な障害を引き起こす可能性のあるいくつかの操作について、Media Services SDK for .NET が処理する、または処理しない例外について説明します。  

| 例外 | Web Request | ストレージ | クエリ | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>詳細については、「[WebException 状態コード](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus)」のセクションを参照してください。 |はい |Yes |Yes |はい |
| DataServiceClientException<br/> 詳細については、「[HTTP エラー状態コード](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)」を参照してください。 |いいえ |はい |Yes |はい |
| DataServiceQueryException<br/> 詳細については、「[HTTP エラー状態コード](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)」を参照してください。 |いいえ |はい |Yes |はい |
| DataServiceRequestException<br/> 詳細については、「[HTTP エラー状態コード](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)」を参照してください。 |いいえ |はい |Yes |はい |
| DataServiceTransportException |いいえ |いいえ |はい |はい |
| TimeoutException |はい |Yes |はい |いいえ |
| SocketException |はい |Yes |Yes |はい |
| StorageException |いいえ |はい |いいえ |いいえ |
| IOException |いいえ |はい |いいえ |いいえ |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a> WebException 状態コード
次の表では、再試行ロジックが実装される WebException エラー コードを示します。 [WebExceptionStatus](/dotnet/api/system.net.webexceptionstatus?view=netcore-3.1) の列挙型は、状態コードを定義します。  

| Status | Web Request | ストレージ | クエリ | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |はい |Yes |Yes |はい |
| NameResolutionFailure |はい |Yes |Yes |はい |
| ProxyNameResolutionFailure |はい |Yes |Yes |はい |
| SendFailure |はい |Yes |Yes |はい |
| PipelineFailure |はい |Yes |はい |いいえ |
| ConnectionClosed |はい |Yes |はい |いいえ |
| KeepAliveFailure |はい |Yes |はい |いいえ |
| UnknownError |はい |Yes |はい |いいえ |
| ReceiveFailure |はい |Yes |はい |いいえ |
| RequestCanceled |はい |Yes |はい |いいえ |
| タイムアウト |はい |Yes |はい |いいえ |
| ProtocolError <br/>ProtocolError の再試行は、HTTP 状態コード処理によって制御されます。 詳細については、「[HTTP エラー状態コード](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)」を参照してください。 |はい |Yes |Yes |はい |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a> HTTP エラー状態コード
Query および SaveChanges 操作が DataServiceClientException、DataServiceQueryException、または DataServiceQueryException をスローすると、StatusCode プロパティに HTTP エラー状態コードが返されます。  次の表では、再試行ロジックが実装されるエラー コードを示します。  

| Status | Web Request | ストレージ | クエリ | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |いいえ |はい |いいえ |いいえ |
| 403 |いいえ |はい<br/>長い待機時間で再試行を処理します。 |いいえ |いいえ |
| 408 |はい |Yes |Yes |はい |
| 429 |はい |Yes |Yes |はい |
| 500 |はい |Yes |はい |いいえ |
| 502 |はい |Yes |はい |いいえ |
| 503 |はい |Yes |Yes |はい |
| 504 |はい |Yes |はい |いいえ |

Media Services SDK for .NET 再試行ロジックの実際の実装を確認するには、「[azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling)」を参照してください。

## <a name="next-steps"></a>次のステップ
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
