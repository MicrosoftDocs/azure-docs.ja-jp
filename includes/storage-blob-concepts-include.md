---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/19/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0914cf9515930e23e4134181ffe8332e36eacffe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98612956"
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

ユーザーまたはクライアント アプリケーションは、世界のどこからでも、HTTP/HTTPS 経由で Blob Storage 内のオブジェクトにアクセスできます。 [Azure Storage REST API](/rest/api/storageservices/blob-service-rest-api)、[Azure PowerShell](/powershell/module/az.storage)、[Azure CLI](/cli/azure/storage)、または Azure Storage クライアント ライブラリを使用して、Blob Storage 内のオブジェクトにアクセスできます。 各種言語に対応したクライアント ライブラリが用意されています。その例を次に示します。

* [.NET](/dotnet/api/overview/azure/storage)
* [Java](/java/api/overview/azure/storage)
* [Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)
* [Python](../articles/storage/blobs/storage-quickstart-blobs-python.md)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 について

Blob Storage では、Microsoft のクラウド向けのエンタープライズ ビッグ データ分析ソリューションである Azure Data Lake Storage Gen2 がサポートされています。 Azure Data Lake Storage Gen2 では、階層型のファイル システムに加え、Blob Storage の利点が提供されます。その例を次に示します。

* 低コスト、階層型ストレージ
* 高可用性
* "強固" 整合性
* ディザスター リカバリー機能

Data Lake Storage Gen2 について詳しくは、「[Azure Data Lake Storage Gen2 の概要](../articles/storage/blobs/data-lake-storage-introduction.md)」をご覧ください。