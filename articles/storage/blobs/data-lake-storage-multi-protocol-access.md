---
title: Azure Data Lake Storage のマルチプロトコル アクセス | Microsoft Docs
description: BLOB API と、BLOB API と Azure Data Lake Storage Gen2 を使用するアプリケーションを使用します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "77914860"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Azure Data Lake Storage のマルチプロトコル アクセス

階層型名前空間があるアカウントで、BLOB API を使用できるようになりました。 これにより、階層型名前空間があるアカウントに対するいくつかの BLOB ストレージ機能だけでなく、ツール、アプリケーション、およびサービスのエコシステムを使用できます。

最近まで、オブジェクト ストレージと分析ストレージ用に別々のストレージ ソリューションを管理する必要がありました。 このため、Azure Data Lake Storage Gen2 によるエコシステムのサポートが制限されていました。 さらに、診断ログなどの BLOB サービスの機能へのアクセスも制限されていました。 さまざまなシナリオを実現するにはアカウント間でデータを移動する必要があるため、寸断されたストレージ ソリューションは管理が容易ではありません。 もう、それを行う必要はありません。

Data Lake Storage のマルチプロトコル アクセスでは、ツール、アプリケーション、およびサービスのエコシステムを使用してデータを操作できます。 これには、サードパーティのツールとアプリケーションも含まれています。 それらを変更することなく、それらで階層型名前空間があるアカウントをポイントすることができます。 BLOB API で階層型名前空間があるアカウントのデータを操作できるようになったため、これらのアプリケーションで BLOB API を呼び出した場合でも、*現状のままで* 動作します。

階層型名前空間があるアカウントで、[診断ログ](../common/storage-analytics-logging.md)、[アクセス レベル](storage-blob-storage-tiers.md)、[BLOB ストレージ ライフサイクル管理ポリシー](storage-lifecycle-management-concepts.md)などの BLOB ストレージの機能が動作するようになりました。 そのため、これらの重要な機能へのアクセスを失うことなく、BLOB ストレージ アカウントで階層型名前空間を有効にできます。 

> [!NOTE]
> Data Lake Storage のマルチプロトコル アクセスは一般提供されており、すべてのリージョンで利用できます。 マルチプロトコル アクセスによって有効にされている一部の Azure サービスまたは BLOB ストレージ機能は、引き続きプレビュー段階です。  これらのアーティクルでは、BLOB ストレージの機能と Azure サービスの統合の現在のサポートについてまとめています。 
>
> [Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能](data-lake-storage-supported-blob-storage-features.md)
>
>[Azure Data Lake Storage Gen2 がサポートされている Azure のサービス](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Azure Data Lake Storage のマルチプロトコル アクセスの実行方法

階層型名前空間があるストレージ アカウントの同じデータに対して、BLOB API と Data Lake Storage Gen2 API を操作できます。 Data Lake Storage Gen2 では階層型名前空間を使用して BLOB API がルーティングされるため、第一級のディレクトリ操作と POSIX 準拠のアクセス制御リスト (ACL) のメリットを活用できます。 

![Azure Data Lake Storage のマルチプロトコル アクセスの概念](./media/data-lake-storage-interop/interop-concept.png) 

BLOB API を使用している既存のツールとアプリケーションでは、これらのメリットが自動的に活用されます。 開発者は、それらを変更する必要はありません。 Data Lake Storage Gen2 では、データにアクセスするためにツールとアプリケーションで使用されるプロトコルに関係なく、ディレクトリとファイルレベルの ACL が一貫して適用されます。 

## <a name="see-also"></a>関連項目

- [Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能](data-lake-storage-supported-blob-storage-features.md)
- [Azure Data Lake Storage Gen2 がサポートされている Azure のサービス](data-lake-storage-supported-azure-services.md)
- [Azure Data Lake Storage Gen2 がサポートされているオープン ソース プラットフォーム](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage Gen2 に関する既知の問題](data-lake-storage-known-issues.md)




