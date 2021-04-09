---
title: Azure PowerShell のサンプル スクリプト - Blob コンテナーの合計課金サイズを計算する | Microsoft Docs
description: 課金の目的で Azure Blob Storage 内のコンテナーの合計サイズを計算します。
services: storage
author: fhryo-msft
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/29/2020
ms.author: fryu
ms.openlocfilehash: dfc338844e310102447e2498ee9cce8f28a79b9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97809566"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Blob コンテナーの合計課金サイズを計算する

このスクリプトは、請求額を見積もるために Azure Blob Storage 内のコンテナーのサイズを計算します。 スクリプトは、コンテナー内の Blob のサイズを合計します。

> [!IMPORTANT]
> この記事に記載されているサンプル スクリプトでは、BLOB スナップショットの課金サイズが正確に計算されない場合があります。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> この PowerShell スクリプトは、課金のためのコンテナーのサイズを計算します。 他の目的でコンテナーのサイズを計算する場合は、見積もりを提供するより簡単なスクリプトが記載されている「[Blob ストレージ コンテナーの合計サイズを計算する](../scripts/storage-blobs-container-calculate-size-powershell.md)」を参照してください。

## <a name="determine-the-size-of-the-blob-container"></a>BLOB コンテナーのサイズを決定する

BLOB コンテナーの合計サイズには、コンテナー自体のサイズとコンテナーの下のすべての BLOB のサイズが含まれます。

次のセクションでは、BLOB コンテナーと BLOB のストレージ容量を計算する方法について説明します。 次のセクションでは Len(X) は文字列の文字数を表します。

### <a name="blob-containers"></a>BLOB コンテナー

次の計算では BLOB コンテナーごとに使用されるストレージの量を推定します。

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

内訳は次のようになります。

* コンテナーごとの 48 バイトのオーバーヘッドには、最終更新時刻、アクセス許可、パブリック設定、およびシステムのメタデータが含まれています。

* コンテナー名は Unicode で格納されているため、文字数を取得して 2 を掛けます。

* 格納されている BLOB コンテナーのメタデータのブロックごとに、メタデータの名前 (ASCII) の長さと文字列値の長さを合計します。

* Signed Identifier ごとの 512 バイトには、Signed Identifier の名前、開始時刻、有効期限、アクセス許可が含まれています。

### <a name="blobs"></a>BLOB

次の計算では BLOB ごとに使用されるストレージの量を推定します。

* ブロック BLOB (ベース BLOB またはスナップショット):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* ページ BLOB (ベース BLOB またはスナップショット):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

内訳は次のようになります。

* BLOB 毎に 124 のバイトのオーバーヘッドがありますが、これには次のものが含まれます。
    - 最終変更時刻
    - サイズ
    - Cache-Control
    - Content-Type
    - Content-Language
    - Content-Encoding
    - Content-MD5
    - アクセス許可
    - スナップショットの情報
    - 占有
    - 一部のシステム メタデータ

* BLOB 名は Unicode で格納されるため、文字数を取得して 2 を掛けます。

* 格納されているメタデータのブロックごとに、名前 (ASCII 形式) の長さを加え、文字列値の長さを足します。

* ブロック BLOB の場合:
  * ブロック一覧の 8 バイト。
  * ブロックの数にブロック ID のサイズ (バイト単位) を掛けたもの。
  * コミットされたブロックとコミットされていないブロックすべてのデータのサイズ。

    >[!NOTE]
    >スナップショットが使用されている場合、このサイズは、ベース BLOB またはスナップショット BLOB の一意のデータだけを含みます。 コミットされていないブロックが 1 週間後に使用されていなければ、ガベージ コレクションされます。 その後は、課金対象ではありません。

* ページ BLOB の場合:
  * 連続しないデータのあるページ範囲の数に 12 バイトを掛けます。 これは **GetPageRanges** API を呼び出すときに表示される一意のページ範囲の数です。

  * 保存されているすべてのページのデータのサイズ (バイト単位)。

    >[!NOTE]
    >スナップショットを使用すると、計算中のベース BLOB またはスナップショット BLOB の一意のページだけがこのサイズに含まれます。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>次のステップ

- コンテナーのサイズの見積もりを提供する簡単なスクリプトについては、「[Blob ストレージ コンテナーの合計サイズを計算する](../scripts/storage-blobs-container-calculate-size-powershell.md)」を参照してください。

- Azure ストレージの課金の詳細については、「[Understanding Windows Azure Storage Billing (Windows Azure Storage の課金について)](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)」をご覧ください。

- Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)をご覧ください。

- その他のストレージ PowerShell サンプル スクリプトは、[Azure Storage 用 PowerShell サンプル](../blobs/storage-samples-blobs-powershell.md)に関するページにあります。