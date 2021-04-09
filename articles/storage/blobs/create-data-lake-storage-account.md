---
title: Azure Data Lake Storage Gen2 のストレージ アカウントを作成する
description: Azure Data Lake Storage Gen2 で使用するストレージ アカウントを作成する方法について説明します。
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 712f1dc0679ee49791831e782fb68c39a757870a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624339"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 で使用するストレージ アカウントを作成する

Data Lake Storage Gen2 の機能を使用するには、階層型名前空間を持つストレージ アカウントを作成します。

## <a name="choose-a-storage-account-type"></a>ストレージ アカウントの種類を選択する

Data Lake Storage 機能は、次の種類のストレージ アカウントでサポートされています。

- 汎用 v2
- BlockBlobStorage

これらのいずれかを選択する方法の詳細については、「[ストレージ アカウントの概要](../common/storage-account-overview.md)」を参照してください。

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>階層型名前空間を持つストレージ アカウントを作成する

[汎用 V2 アカウント](../common/storage-account-create.md)、または **階層型名前空間** の設定が有効になっている [BlockBlobStorage](storage-blob-create-account-block-blob.md) アカウントのいずれかを作成します。

アカウントを作成するときに Data Lake Storage 機能のロックを解除するには、 **[ストレージ アカウントの作成]** ページの **[詳細]** タブにある **階層型名前空間** の設定を有効にします。 この設定は、アカウントを作成するときに有効にする必要があります。 後で有効にすることはできません。

次の図は、 **[ストレージ アカウントの作成]** ページのこの設定を示しています。

> [!div class="mx-imgBorder"]
> ![階層型名前空間の設定](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Data Lake Storage で使用する既存のストレージ アカウントがあり、階層型名前空間の設定が無効になっている場合は、この設定が有効になっている新しいストレージ アカウントにデータを移行する必要があります。

> [!NOTE]
> **データ保護** と **階層型名前空間** を同時に有効にすることはできません。

## <a name="next-steps"></a>次のステップ

- [ストレージ アカウントの概要](../common/storage-account-overview.md)
- [Data Lake Storage Gen2 を使用してビッグ データの要件に対応する](data-lake-storage-data-scenarios.md)
- [Azure Data Lake Storage Gen2 のアクセス制御](data-lake-storage-access-control.md)
