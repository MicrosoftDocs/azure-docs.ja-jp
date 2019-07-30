---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/18/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8d1f9ca5a964787f0ee68e6d99fe870cd7b5994b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673411"
---
Azure Blob Storage は、Microsoft のクラウド用オブジェクト ストレージ ソリューションです。 Blob Storage は、テキスト データやバイナリ データなどの大量の非構造化データを格納するために最適化されています。 非構造化データとは、特定のデータ モデルや定義に従っていないデータであり、テキスト データやバイナリ データなどがあります。

## <a name="about-blob-storage"></a>Blob Storage について

Blob Storage は、次の用途に適しています。

* 画像またはドキュメントをブラウザーに直接配信する。
* 分散アクセス用にファイルを格納する。
* ビデオおよびオーディオをストリーミング配信する。
* ログ ファイルに書き込む。
* バックアップと復元、ディザスター リカバリー、アーカイブのためのデータを格納する。
* オンプレミス サービスまたは Azure ホステッド サービスで分析するデータを格納する。

ユーザーまたはクライアント アプリケーションは、世界のどこからでも、HTTP/HTTPS 経由で Blob Storage 内のオブジェクトにアクセスできます。 [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage)、[Azure CLI](https://docs.microsoft.com/cli/azure/storage)、または Azure Storage クライアント ライブラリを使用して、Blob Storage 内のオブジェクトにアクセスできます。 [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client)、[Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)、[Node.js](https://azure.github.io/azure-storage-node)、[Python](https://docs.microsoft.com/python/azure/)、[Go](https://github.com/azure/azure-storage-blob-go/)、[PHP](https://azure.github.io/azure-storage-php/)、[Ruby](https://azure.github.io/azure-storage-ruby) などのさまざまな言語のクライアント ライブラリを利用できます。

## <a name="about-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 について

Blob Storage では、Microsoft のクラウド向けのエンタープライズ ビッグ データ分析ソリューションである Azure Data Lake Storage Gen2 がサポートされています。 Azure Data Lake Storage Gen2 では、階層型のファイル システムに加え、低コスト、階層型ストレージ、高可用性、強力な一貫性、ディザスター リカバリー機能を含む Blob Storage の利点が提供されます。

Data Lake Storage Gen2 について詳しくは、「[Azure Data Lake Storage Gen2 の概要](../articles/storage/data-lake-storage/introduction.md)」をご覧ください。
