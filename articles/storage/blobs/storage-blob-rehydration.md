---
title: アーカイブ層から BLOB データをリハイドレートする
description: データにアクセスできるように、アーカイブ ストレージから BLOB をリハイドレートします。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/07/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 2e7d56a1461dfd89a7309288aadb0ba245d0f885
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2019
ms.locfileid: "68957508"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>アーカイブ層から BLOB データをリハイドレートする

BLOB はアーカイブ アクセス層に含まれていますが、オフラインと見なされ、読み取りや変更はできません。 BLOB のメタデータはオンラインのままで使用可能であり、BLOB とそのプロパティの一覧を表示することができます。 BLOB データの読み取りと変更が可能なのは、ホットやクールなどのオンライン層のみになります。 アーカイブ アクセス層に格納されているデータを取得してアクセスするには、2 つのオプションがあります。

1. [アーカイブ済み BLOB をオンライン層にリハイドレートする](#rehydrate-an-archived-blob-to-an-online-tier) - [[BLOB 層の設定]](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) 操作を使用して階層を変更することにより、アーカイブ済み BLOB をホットまたはクールにリハイドレートします。
2. [アーカイブ済み BLOB をオンライン層にコピーする](#copy-an-archived-blob-to-an-online-tier) - [[BLOB のコピー]](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 操作を使用して、アーカイブ済み BLOB の新しいコピーを作成します。 別の BLOB 名と、移動先の層としてホットまたはクールを指定します。

 アクセス層の詳細については、「[Azure Blob Storage: ホット、クール、アーカイブ ストレージ層](storage-blob-storage-tiers.md)」を参照してください。

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>アーカイブ済み BLOB をオンライン層にリハイドレートする

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>アーカイブ済み BLOB をオンライン層にコピーする

BLOB をリハイドレートしたくない場合は、[[BLOB のコピー]](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 操作を選択できます。 ホット層またはクール層で新しい BLOB を操作している間、元の BLOB はアーカイブで未変更のままになります。 コピー プロセスを使用するときに、オプションの *x-ms-rehydrate-priority* プロパティを Standard または High (プレビュー) に設定できます。

アーカイブ BLOB は、オンラインの移動先の層にのみコピーできます。 アーカイブ BLOB を別のアーカイブ BLOB にコピーすることはサポートされていません。

アーカイブから BLOB をコピーするには時間がかかります。 **[BLOB のコピー]** 操作では、バックグラウンドでアーカイブ ソース BLOB が一時的にリハイドレートされて、移動先の層に新しいオンライン BLOB が作成されます。 この新しい BLOB は、アーカイブからの一時的なリハイドレートが完了して、データが新しい BLOB に書き込まれるまで使用できません。

## <a name="pricing-and-billing"></a>価格と課金

アーカイブからホット層またはクール層への BLOB のリハイドレートは、読み取り操作およびデータ取得として課金されます。 High 優先度 (プレビュー) を使用すると、Standard 優先度と比較して、操作とデータ取得のコストが高くなります。 High 優先度のリハイドレートは、請求書に別の行の項目として表示されます。 数ギガバイトのアーカイブ BLOB を返す High 優先度の要求で 5 時間以上かかった場合、High 優先度の取得料金は課金されません。 ただし、Standard の取得料金は適用されます。

アーカイブからホット層またはクール層への BLOB のコピーは、読み取り操作およびデータ取得として課金されます。 書き込み操作は、新しいコピーの作成に対して課金されます。 ソース BLOB はアーカイブ層で変更されていないため、オンライン BLOB にコピーする場合、早期削除料金は適用されません。 High 優先度の料金は適用されます。

アーカイブ層の BLOB は、少なくとも 180 日間格納する必要があります。 180 日間より前にアーカイブ済み BLOB を削除またはリハイドレートすると、早期削除料金が発生します。

> [!NOTE]
> ブロック BLOB とデータ ハイドレートの価格の詳細については、[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)に関するページを参照してください。 送信データ転送の価格の詳細については、[データ転送料金の詳細](https://azure.microsoft.com/pricing/details/data-transfers/)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* [Blob Storage 層について学習する](storage-blob-storage-tiers.md)
* [BLOB ストレージ アカウントと GPv2 アカウントのホット、クール、アーカイブのリージョンごとの料金を確認する](https://azure.microsoft.com/pricing/details/storage/)
* [Azure Blob Storage のライフサイクルの管理](storage-lifecycle-management-concepts.md)
* [データ転送の価格を確認する](https://azure.microsoft.com/pricing/details/data-transfers/)
