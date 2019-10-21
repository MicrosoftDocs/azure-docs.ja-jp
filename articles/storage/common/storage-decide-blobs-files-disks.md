---
title: Azure BLOB、Azure Files、Azure ディスクの使い分け
description: Azure にデータを格納したりそのデータにアクセスしたりするための各種の方法とテクノロジの使い分けのヒントについて説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671041"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Azure BLOB、Azure Files、Azure ディスクの使い分け

Microsoft Azure の Azure Storage には、クラウドにデータを格納したりそのデータにアクセスしたりするためのいくつかの機能が用意されています。 この記事では、Azure Files、Azure BLOB、Azure ディスクについて取り上げると共に、これらの機能をうまく使い分けるためのヒントを紹介しています。

## <a name="scenarios"></a>シナリオ

次の表は、Azure Files、Azure BLOB、Azure ディスクの比較と、それぞれの機能に適したシナリオの例です。

| 機能 | 説明 | いつ使用するか |
|--------------|-------------|-------------|
| **Azure Files** | 格納されているファイルにどこからでもアクセスできる [REST インターフェイス](/rest/api/storageservices/file-service-rest-api)、SMB インターフェイス、クライアント ライブラリを備えています。 | 既にネイティブ ファイル システム API を使用し、Azure で稼働している他のアプリケーションとの間でデータを共有しているアプリケーションをクラウドに "リフト アンド シフト" する。<br/><br/>多くの仮想マシンからアクセスする必要のある開発ツールとデバッグ ツールを格納する。 |
| **Azure BLOB** | 大規模な非構造化データをブロック BLOB に格納してアクセスできる [REST インターフェイス](/rest/api/storageservices/blob-service-rest-api)とクライアント ライブラリを備えています。<br/><br/>エンタープライズ ビッグ データ分析ソリューション用の [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) もサポートされています。 | アプリケーションでストリーミングとランダム アクセスのシナリオに対応する。<br/><br/>アプリケーションのデータにどこからでもアクセスできるようにする。<br/><br/>Azure 上にエンタープライズ Data Lake を構築し、ビッグ データ分析を実行する。 |
| **Azure ディスク** | アタッチされた仮想ハード ディスクにデータを永続的に格納し、アクセスできる [REST インターフェイス](/rest/api/compute/manageddisks/disks/disks-rest-api)とクライアント ライブラリを備えています。 | 永続ディスクに対しネイティブ ファイル システム API を使用してデータの読み取りと書き込みを行うアプリケーションをリフト アンド シフトする。<br/><br/>外部 (ディスクがアタッチされている仮想マシンの外) からのアクセスが不要なデータを格納する。 |


## <a name="next-steps"></a>次の手順

データの格納方法とアクセス方法を決めるときには、それに伴うコストも考慮する必要があります。 詳細については、[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/)に関するページを参照してください。
  
一部の SMB 機能はクラウドでは利用できません。 詳細については、「[Features not supported by the Azure File service (Azure File Service でサポートされていない機能)](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)」を参照してください。
 
Azure BLOB の詳細については、[「Azure Blob Storage とは](../blobs/storage-blobs-overview.md)」という記事を参照してください。

Disk Storage について詳しくは、「[マネージド ディスクの概要](../../virtual-machines/windows/managed-disks-overview.md)」をご覧ください。

Azure Files の詳細については、「[Azure Files のデプロイの計画](../files/storage-files-planning.md)」という記事を参照してください。