---
title: Azure Media Services のアセット
description: アセットとは何か、および Azure Media Services によってどのように使用されるかについて説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 5159432107e60f6c21bcf70e0bbc9a9e2123a728
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897698"
---
# <a name="assets-in-azure-media-services-v3"></a>Azure Media Services v3 のアセット

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services では、[アセット](/rest/api/media/assets)は中核的な概念です。 アセットでは、メディアの入力 (たとえば、アップロードまたはライブ取り込みを通して)、メディアの出力 (ジョブ出力から)、およびメディアの公開 (ストリーム配信の場合) を行います。 

アセットは [Azure Storage アカウント](storage-account-concept.md)内の BLOB コンテナーにマップされ、アセット内のファイルはブロック BLOB としてそのコンテナーに格納されます。 アセットには、Azure Storage 内に格納されているデジタル ファイル (ビデオ、オーディオ、画像、サムネイルのコレクション、テキスト トラック、クローズド キャプション ファイルなど) に関する情報が含まれています。

Media Services は、アカウントが汎用 v2 (GPv2) ストレージを使用している場合に、BLOB 層をサポートします。 GPv2 を使用して、[クール ストレージまたはアーカイブ ストレージ](../../storage/blobs/storage-blob-storage-tiers.md)にファイルを移動できます。 **アーカイブ** ストレージは、(エンコード後などに) 不要になったソース ファイルをアーカイブするのに適しています。

**アーカイブ** ストレージ層は、既にエンコードされ、エンコード ジョブの出力が出力 BLOB コンテナーに配置されている非常に大きなソース ファイルの場合のみ推奨されます。 アセットに関連付け、コンテンツのストリーム配信や分析に使用する出力コンテナー内の BLOB は、**ホット** または **クール** ストレージ層に存在する必要があります。

## <a name="naming"></a>名前を付ける 

### <a name="assets"></a>アセット

アセット名は一意である必要があります。 Media Services v3 のリソース名 (アセット、ジョブ、変換など) には、Azure Resource Manager の名前付け規則が適用されます。 詳細については、「[名前付け規則](media-services-apis-overview.md#naming-conventions)」を参照してください。

### <a name="blobs"></a>BLOB

アセット内のファイルまたは BLOB の名前は、[BLOB 名の要件](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)と[NTFS 名の要件](/windows/win32/fileio/naming-a-file)の両方に従っている必要があります。 これらの要件は、ファイルが BLOB ストレージからローカルの NTFS ディスクにコピーされて処理できるようにするためのものです。

## <a name="next-steps"></a>次のステップ

[Media Services の概要](media-services-overview.md)

## <a name="see-also"></a>関連項目

[Media Services v2 と v3 の違い](migrate-v-2-v-3-migration-introduction.md)
