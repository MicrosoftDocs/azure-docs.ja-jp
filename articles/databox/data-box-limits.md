---
title: Azure Data Box の制限 | Microsoft Docs
description: Microsoft Azure Data Box のコンポーネントと接続に関するシステム制限および推奨サイズについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 97d8da86565db73aa9a3866f39f793aaf0905470
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97900163"
---
# <a name="azure-data-box-limits"></a>Azure Data Box の制限

Microsoft Azure Data Box をデプロイおよび運用する際には、以下の制限事項を考慮してください。 次の表で、Data Box の制限について説明します。

## <a name="data-box-service-limits"></a>Data Box サービスの制限

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Data Box の制限

- Data Box には、インポートとエクスポートの両方で、最大で 5 億個のファイルを格納できます。
- Data Box は、クラウドで最大 512 個のコンテナーまたは共有をサポートしています。 ユーザー共有内の最上位レベルのディレクトリは、クラウド内のコンテナーまたは Azure ファイル共有になります。 
- Data Box の使用容量は、ReFS のメタデータによりスペースが消費されるため、80 TB 以下になる場合があります。
- Data Box では、NFS 共有上で最大 10 のクライアント接続が同時にサポートされます。

## <a name="azure-storage-limits"></a>Azure Storage の制限

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>データのアップロードに関する注意事項


### <a name="for-import-order"></a>インポート注文の場合

インポート注文に関する Data Box の注意事項は次のとおりです。

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

### <a name="for-export-order"></a>エクスポート注文の場合

エクスポート注文に関する Data Box の注意事項は次のとおりです。

- Data Box は Windows ベースのデバイスであり、大文字と小文字が区別されるファイル名はサポートされていません。 たとえば、大文字と小文字の違いのみが異なる 2 つの異なるファイルが Azure にあるとします。 ファイルがデバイス上で上書きされるため、Data Box を使用してこのようなファイルをエクスポートしないでください。
- 入力ファイルに重複するタグがある場合、または同じデータを参照しているタグがある場合、Data Box エクスポートによってこのファイルがスキップまたは上書きされることがあります。 Azure portal に表示されるファイルの数とデータのサイズが、デバイス上の実際のデータのサイズと異なる場合があります。 
- Data Box では、SMB 経由で Windows ベースのシステムにデータがエクスポートされ、ファイルとフォルダーの SMB 制限によって制限されています。 サポートされていない名前のファイルやフォルダーはエクスポートされません。
- プレフィックスからコンテナーは、1 対 1 のマッピングがあります。
- ファイル名の最大サイズは 1024 文字です。 この長さを超えるファイル名はエクスポートされません。
- *XML* ファイル (注文の作成時にアップロードされます) で重複するプレフィックスはエクスポートされます。 重複するプレフィックスは無視されません。
- ページ BLOB とコンテナー名では、大文字と小文字が区別されます。 大文字と小文字の区別が一致しない場合、BLOB またはコンテナーは見つかりません。
 

## <a name="azure-storage-account-size-limits"></a>Azure ストレージ アカウントのサイズ制限

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Azure オブジェクトのサイズ制限

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure ブロック BLOB、ページ BLOB およびファイルの名前付け規則

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
