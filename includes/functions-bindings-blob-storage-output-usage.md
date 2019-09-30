---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 414381d42c52e7489fbd6aaacf0f079cdd978ebe
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641863"
---
次の型にバインドして BLOB を書き込むことができます。

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> *function.json* には "in" バインド `direction`、C# クラス ライブラリには `FileAccess.Read` が必要です。 ただし、コンテナーへの BLOB のアップロードなどの書き込み操作をおこなうためにランタイムが提供するコンテナー オブジェクトを使うことができます。

<sup>2</sup> *function.json* には "inout" バインド `direction`、C# クラス ライブラリには `FileAccess.ReadWrite` が必要です。

Storage SDK タイプの 1 つにバインドしようとしてエラー メッセージが表示された場合は、[適切な Storage SDK バージョン](#azure-storage-sdk-version-in-functions-1x)への参照があることをご確認ください。

非同期関数では、`out` パラメーターの代わりに戻り値または `IAsyncCollector` を使用します。

`string` または `Byte[]` へのバインドが推奨されるのは、BLOB のサイズが小さい場合のみです (BLOB 全体のコンテンツがメモリに読み込まれるため)。 通常、`Stream` 型または `CloudBlockBlob` 型の使用が推奨されます。 詳しくは、この記事で前述した「[コンカレンシーとメモリ使用量](#trigger---concurrency-and-memory-usage)」セクションをご覧ください。