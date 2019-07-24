---
title: Azure Storage のサンプル コード | Microsoft Docs
description: Azure Storage のサンプル コードとアプリケーションを表示、ダウンロード、実行します。 .NET、Java、Node.js、C++ のストレージ クライアント ライブラリを使用して、BLOB、キュー、テーブル、ファイルのサンプルの概要について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/03/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 411b43ec631c77246da1ee55f2532486f8b55ffc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006631"
---
# <a name="azure-storage-samples"></a>Azure Storage のサンプル

Azure Storage のサンプル コードとアプリケーションを閲覧したりダウンロードしたりするには、以下のリンクをご利用ください。

## <a name="azure-code-samples-library"></a>Azure のコード サンプル ライブラリ
[Azure のコード サンプル ライブラリ](https://azure.microsoft.com/documentation/samples/?service=storage) には、ダウンロードしてローカルで実行できる Azure Storage のサンプルが用意されています。 コード サンプル ライブラリでは、サンプル コードが .zip 形式で提供されます。 また、各サンプルの GitHub リポジトリを参照して複製することもできます。

<!--## Getting started samples-->
<!-- after our quick starts are available, replace this link with a link to one of those. 
Had to remove this article, it refers to the VS quickstarts, and they've stopped publishing them. Robin --> 
<!--* [Get started with Azure Storage in five minutes](storage-getting-started-guide.md)
* [Visual Studio Quick Starts for Azure Storage](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/VisualStudioQuickStarts)
-->

## <a name="net-samples"></a>.NET のサンプル
.NET のサンプルを調べるには、NuGet から [.NET ストレージ クライアント ライブラリ](https://www.nuget.org/packages/WindowsAzure.Storage/) をダウンロードします。 .NET ストレージ クライアント ライブラリは、 [Azure SDK for .NET のページ](https://azure.microsoft.com/downloads/)にも用意されています。

* [.NET を使用した Azure Storage サンプル](storage-samples-dotnet.md)

## <a name="java-samples"></a>Java のサンプル
Java のサンプルを調べるには、 [Java ストレージ クライアント ライブラリ](https://github.com/azure/azure-storage-java)をダウンロードします。

* [Java を使用した Azure Storage サンプル](storage-samples-java.md)

## <a name="nodejs-samples"></a>Node.js のサンプル
Node.js のサンプルを調べるには、 [Node.js ストレージ クライアント ライブラリ](https://github.com/Azure/azure-storage-node)をダウンロードします。

* [BLOB アップローダー](https://github.com/Azure/azure-storage-node/tree/master/examples/blobuploader)
* [BLOB のアップロードとダウンロード](https://github.com/Azure/azure-storage-node/blob/master/examples/samples/blobuploaddownloadsample.js)
* [継続トークン](https://github.com/Azure/azure-storage-node/blob/master/examples/samples/continuationsample.js)
* [再試行ポリシー](https://github.com/Azure/azure-storage-node/blob/master/examples/samples/retrypolicysample.js)
* [共有アクセス署名](https://github.com/Azure/azure-storage-node/blob/master/examples/samples/sassample.js)
* [スナップショット](https://github.com/Azure/azure-storage-node/blob/master/examples/samples/snapshotsample.js)
* [テーブル クエリ](https://github.com/Azure/azure-storage-node/blob/master/examples/samples/tablequerysample.js)

## <a name="c-samples"></a>C++ のサンプル
C++ のサンプルを調べるには、NuGet から [C++ ストレージ クライアント ライブラリ](https://www.nuget.org/packages/wastorage/) をダウンロードします。

* [BLOB の使用](https://github.com/Azure/azure-storage-cpp/tree/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted)
* [テーブルの使用](https://github.com/Azure/azure-storage-cpp/tree/master/Microsoft.WindowsAzure.Storage/samples/TablesGettingStarted)
* [キューの使用](https://github.com/Azure/azure-storage-cpp/tree/master/Microsoft.WindowsAzure.Storage/samples/QueuesGettingStarted)

## <a name="api-reference-and-source-code"></a>API リファレンスとソース コード

| 言語 | API リファレンス | ソース コード |
|----------|---------------|-------------|
| .NET | [.NET クライアント ライブラリ リファレンス](https://msdn.microsoft.com/library/azure/mt347887.aspx) | [.NET ストレージ クライアント ライブラリのソース コード](https://github.com/Azure/azure-storage-net) |
| Java | [Java クライアント ライブラリ リファレンス](https://docs.microsoft.com/java/api/overview/azure/storage) | [Java ストレージ クライアント ライブラリのソース コード](https://github.com/azure/azure-storage-java) |
| Node.js | [Node.js クライアント ライブラリ リファレンス](https://azure.github.io/azure-storage-node) | [Node.js ストレージ クライアント ライブラリのソース コード](https://github.com/Azure/azure-storage-node) |
| C++ | [C++ クライアント ライブラリ リファレンス](https://azure.github.io/azure-storage-cpp/) | [C++ ストレージ クライアント ライブラリのソース コード](https://github.com/Azure/azure-storage-cpp)|

## <a name="next-steps"></a>次の手順

以下の記事は、サービス (BLOB、ファイル、キュー、テーブル) ごとにサンプルをまとめた索引としてご利用ください。

* [.NET を使用した Azure Storage サンプル](storage-samples-dotnet.md)
* [Java を使用した Azure Storage サンプル](storage-samples-java.md)
