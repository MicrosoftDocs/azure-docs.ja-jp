---
title: "Media Services SDK for .NET の再試行ロジック | Microsoft Docs"
description: "このトピックでは、Media Services SDK for .NET の再試行ロジックの概要を説明します。"
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e22a16c0929b28c475aa4caa0465651603713112


---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Media Services SDK for .NET の再試行ロジック
Microsoft Azure サービスの使用中に、一時的な障害が発生することがあります。 一時的な障害が発生しても、多くの場合は、何度か再試行すれば操作に成功します。 Media Services SDK for .NET には、例外とエラーに関連する一時的な障害を処理するための再試行ロジックが実装されています。そのような例外とエラーは、Web 要求、クエリの実行、変更の保存、ストレージ操作によって発生します。  既定では、Media Services SDK for .NET はアプリケーションに例外を再びスローする前に、再試行を 4 回実行します。 アプリケーションのコードは、この例外を正しく処理する必要があります。  

 Web Request、Storage、Query、SaveChanges ポリシーの簡単なガイドラインを次に示します。  

* Storage ポリシーは、Blob Storage の操作 (アセット ファイルのアップロードまたはダウンロード) に使用されます。  
* Web Request ポリシーは、一般的な Web 要求 (たとえば、認証トークンを取得してユーザー クラスター エンドポイントを解決する) に使用されます。  
* Query ポリシーは、REST からのエンティティのクエリ実行に使用されます (たとえば、mediaContext.Assets.Where(…))。  
* SaveChanges ポリシーは、サービス内のデータを変更する操作に使用されます (たとえば、エンティティを更新するエンティティの作成、操作のためのサービス関数の呼び出しなど)。  
  
  このトピックでは、Media Services SDK for .NET の再試行ロジックが処理する例外の種類とエラー コードについて説明します。  

## <a name="exception-types"></a>例外の種類
次の表では、一時的な障害を引き起こす可能性のあるいくつかの操作について、Media Services SDK for .NET が処理する、または処理しない例外について説明します。  

| 例外 | Web Request | Storage | クエリ | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>詳細については、「[WebException 状態コード](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus)」のセクションを参照してください。 |はい |あり |あり |はい |
| DataServiceClientException<br/> 詳細については、「[HTTP エラー状態コード](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)」を参照してください。 |なし |はい |あり |はい |
| DataServiceQueryException<br/> 詳細については、「[HTTP エラー状態コード](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)」を参照してください。 |なし |はい |あり |はい |
| DataServiceRequestException<br/> 詳細については、「[HTTP エラー状態コード](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)」を参照してください。 |なし |はい |あり |はい |
| DataServiceTransportException |いいえ |いいえ |はい |はい |
| TimeoutException |はい |あり |あり |いいえ |
| SocketException |はい |あり |あり |はい |
| StorageException |いいえ |あり |なし |いいえ |
| IOException |いいえ |あり |なし |なし |

### <a name="a-namewebexceptionstatusa-webexception-status-codes"></a><a name="WebExceptionStatus"></a> WebException 状態コード
次の表では、再試行ロジックが実装される WebException エラー コードを示します。 [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) の列挙型は、状態コードを定義します。  

| 状態 | Web Request | Storage | クエリ | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |はい |あり |あり |はい |
| NameResolutionFailure |はい |あり |あり |はい |
| ProxyNameResolutionFailure |はい |あり |あり |はい |
| SendFailure |はい |あり |あり |はい |
| PipelineFailure |はい |あり |あり |いいえ |
| ConnectionClosed |はい |あり |あり |いいえ |
| KeepAliveFailure |はい |あり |あり |なし |
| UnknownError |はい |あり |あり |いいえ |
| ReceiveFailure |はい |あり |あり |なし |
| RequestCanceled |はい |あり |あり |いいえ |
| タイムアウト |はい |あり |あり |なし |
| ProtocolError <br/>ProtocolError の再試行は、HTTP 状態コード処理によって制御されます。 詳細については、「[HTTP エラー状態コード](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)」を参照してください。 |はい |あり |あり |はい |

### <a name="a-namehttpstatuscodea-http-error-status-codes"></a><a name="HTTPStatusCode"></a> HTTP エラー状態コード
Query および SaveChanges 操作が DataServiceClientException、DataServiceQueryException、または DataServiceQueryException をスローすると、StatusCode プロパティに HTTP エラー状態コードが返されます。  次の表では、再試行ロジックが実装されるエラー コードを示します。  

| 状態 | Web Request | Storage | クエリ | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |いいえ |あり |なし |なし |
| 403 |いいえ |はい<br/>長い待機時間で再試行を処理します。 |なし |なし |
| 408 |はい |あり |あり |はい |
| 429 |はい |あり |あり |はい |
| 500 |はい |あり |あり |いいえ |
| 502 |はい |あり |あり |いいえ |
| 503 |はい |あり |あり |はい |
| 504 |はい |あり |あり |いいえ |

Media Services SDK for .NET 再試行ロジックの実際の実装を確認するには、「[azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling)」を参照してください。

## <a name="next-steps"></a>次のステップ
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO3-->


