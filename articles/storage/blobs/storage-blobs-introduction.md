---
title: Blob (オブジェクト) Storage の概要
titleSuffix: Azure Storage
description: Azure Blob Storage を使用して、テキスト データやバイナリ データなどの大量の非構造化オブジェクト データを格納します。 Azure Blob Storage は、高度にスケーラブルな高可用性のストレージです。
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 06/24/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b55ea814214397e12e50e7bc5047988394623940
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98703591"
---
# <a name="introduction-to-azure-blob-storage"></a>Azure Blob Storage の概要

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Blob Storage のリソース

Blob Storage には、3 種類のリソースがあります。

- **ストレージ アカウント**
- ストレージ アカウント内の **コンテナー**
- コンテナー内の **BLOB**

次の図に、これらのリソースの関係を示します。

![ストレージ アカウント、コンテナー、BLOB の関係を示す図](./media/storage-blobs-introduction/blob1.png)

### <a name="storage-accounts"></a>ストレージ アカウント

ストレージ アカウントは、データ用の一意の名前空間を Azure 内に用意します。 Azure Storage 内に格納されるすべてのオブジェクトには、一意のアカウント名を含むアドレスが割り当てられます。 アカウント名と Azure Storage Blob エンドポイントの組み合わせによって、ストレージ アカウント内のオブジェクト用のベース アドレスが形成されます。

たとえば、ストレージ アカウントの名前が *mystorageaccount* の場合、Blob Storage の既定のエンドポイントは次のようになります。

```
http://mystorageaccount.blob.core.windows.net
```

ストレージ アカウントを作成する場合は、「[ストレージ アカウントの作成](../common/storage-account-create.md)」を参照してください。 ストレージ アカウントの詳細については、「[Azure ストレージ アカウントの概要](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。

### <a name="containers"></a>Containers

ファイル システムのディレクトリと同じように、コンテナーを使用して BLOB のセットを整理できます。 ストレージ アカウントに含めることができるコンテナーの数には制限がなく、1 つのコンテナーに格納できる BLOB の数にも制限はありません。

> [!NOTE]
> コンテナー名は小文字で入力する必要があります。 コンテナーの詳細については、「[Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)」(コンテナー、BLOB、およびメタデータの名前付けと参照) を参照してください。

### <a name="blobs"></a>BLOB

Azure Storage では、3 種類の BLOB がサポートされています。

- **ブロック BLOB** には、テキストとバイナリ データが格納されます。 ブロック BLOB は、個別に管理できるデータ ブロックで構成されます。 ブロック BLOB には、最大約 4.75 TiB のデータが格納されます。 プレビューではもっと大きなブロック BLOB を利用できます。最大約 190.7 TiB です。
- **追加 BLOB** は、ブロック BLOB と同様にブロックで構成されますが、追加操作用に最適化されています。 追加 BLOB は、仮想マシンのデータのログ記録などのシナリオに最適です。
- **ページ BLOB** には、最大 8 TiB のランダム アクセス ファイルを格納できます。 ページ BLOB は、仮想ハード ドライブ (VHD) ファイルを格納し、Azure 仮想マシン用のディスクとして機能します。 ページ BLOB について詳しくは、「[Azure ページ BLOB の概要](storage-blob-pageblob-overview.md)」をご覧ください

異なる種類の BLOB の詳細については、「[Understanding Block Blobs, Append Blobs, and Page Blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)」(ブロック BLOB、追加 BLOB、ページ BLOB について) をご覧ください。

## <a name="move-data-to-blob-storage"></a>Blob Storage にデータを移動する

既存のデータを Blob Storage に移行するためのソリューションは多数存在します。

- **AzCopy** は Windows と Linux 用の使いやすいコマンド ライン ツールであり、Blob Storage 間、コンテナー間、またはストレージ アカウント間でデータをコピーします。 AzCopy の詳細については、「[AzCopy v10 を使用してデータを転送する](../common/storage-use-azcopy-v10.md)」をご覧ください。
- **Azure Storage Data Movement Library** は、Azure Storage サービス間でデータを移動するための .NET ライブラリです。 AzCopy ユーティリティは、Data Movement Library を使用して構築されています。 詳細については、Data Movement Library の[参照ドキュメント](/dotnet/api/microsoft.azure.storage.datamovement)をご覧ください。
- **Azure Data Factory** では、Azure リソースのアカウント キー、Shared Access Signature、サービス プリンシパル、またはマネージド ID を使用した BLOB Storage 間のデータのコピーをサポートしています。 詳しくは、「[Azure Data Factory を使用した Azure Blob Storage との間でのデータのコピー](../../data-factory/connector-azure-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」をご覧ください。
- **blobfuse** は Azure Blob Storage 用の仮想ファイル システム ドライバーです。 Linux ファイル システムでは、blobfuse を使用して、お客様のストレージ アカウント内の既存のブロック BLOB データにアクセスできます。 詳しくは、「[blobfuse を使用して Blob Storage をファイル システムとしてマウントする方法](storage-how-to-mount-container-linux.md)」をご覧ください。
- **Azure Data Box** サービスは、大規模なデータセットやネットワーク上の制約によって有線でのデータのアップロードが非現実的になったときに、オンプレミスのデータを Blob Storage に転送するために使用できるサービスです。 データのサイズに応じて、[Azure Data Box Disk](../../databox/data-box-disk-overview.md)、[Azure Data Box](../../databox/data-box-overview.md)、または [Azure Data Box Heavy](../../databox/data-box-heavy-overview.md) デバイスを Microsoft に要求できます。 その後にデータをそれらのデバイスにコピーし、Microsoft に送り返すことで、Blob Storage にアップロードすることができます。
- **Azure Import/Export サービス** を使用して、お客様が用意したハード ディスクを使用して、大量のデータをお客様のストレージ アカウントにインポートまたはお客様のストレージ アカウントからエクスポートすることができます。 詳しくは、[Microsoft Azure Import/Export サービスを使用した Blob Storage へのデータの転送](../../import-export/storage-import-export-service.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

- [ストレージ アカウントの作成](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [BLOB ストレージのスケーラビリティとパフォーマンスのターゲット](scalability-targets.md)
