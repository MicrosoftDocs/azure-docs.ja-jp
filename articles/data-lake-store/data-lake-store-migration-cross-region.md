---
title: Azure Data Lake Storage Gen1 のリージョン間の移行 | Microsoft Docs
description: 新しいリージョンで利用可能になったとき、Azure Data Lake Storage Gen1 への移行を計画し、遂行するときの考慮事項について説明します。
services: data-lake-store
documentationcenter: ''
author: twooley
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: twooley
ms.openlocfilehash: c6520036f3ddb8799025129391330268b0604886
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97723808"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>リージョン間での Azure Data Lake Storage Gen1 の移行

Azure Data Lake Storage Gen1 が新しいリージョンで利用できるようになった時点で、新しいリージョンを利用するために 1 回限りの移行を実行できます。 移行の計画および実行時に考慮すべき事項について説明します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 詳細については、「[無料の Azure アカウントを今すぐ作成しましょう](https://azure.microsoft.com/pricing/free-trial/)」をご覧ください。
* **2 つの異なるリージョンの Data Lake Storage Gen1 アカウント**。 詳細については、[Azure Data Lake Storage Gen1 の使用](data-lake-store-get-started-portal.md)に関するページをご覧ください。
* **Azure Data Factory**。 詳細については、[Azure Data Factory の概要](../data-factory/introduction.md)に関するページをご覧ください。


## <a name="migration-considerations"></a>移行に関する考慮事項

最初に、Data Lake Storage Gen1 でのデータの書き込み、読み取り、または処理を行うアプリケーションに最適な移行戦略を特定します。 戦略を選択する際は、アプリケーションの可用性の要件、および移行中に発生するダウンタイムを考慮します。 たとえば、最も簡単な方法は、"リフトアンドシフト" クラウド移行モデルを使用することです。 この方法では、すべてのデータを新しいリージョンにコピーしている間、既存のリージョンでアプリケーションを一時停止します。 コピー プロセスが完了したら、新しいリージョンでアプリケーションを再開し、古い Data Lake Storage Gen1 アカウントを削除します。 移行中にはダウンタイムが必要になります。

ダウンタイムを短縮するために、すぐに新しいリージョンで新しいデータの取り込みを開始することもできます。 必要な最小限のデータが揃ったら、新しいリージョンでアプリケーションを実行します。 バックグラウンドでは、既存の Data Lake Storage Gen1 アカウントから新しいリージョンの新しい Data Lake Storage Gen1 アカウントへの古いデータのコピーが続行されます。 この方法により、短いダウンタイムで新しいリージョンに切り替えることができます。 古いデータがすべてコピーされたら、古い Data Lake Storage Gen1 アカウントを削除します。

ほかにも、移行を計画する際に考慮すべき重要な事項があります。

* **データの量**。 データの量 (GB、ファイルやフォルダーの数など) は、移行に必要な時間とリソースに影響します。

* **Data Lake Storage Gen1 アカウント名**。 新しいリージョンの新しいアカウント名はグローバルに一意である必要があります。 たとえば、米国東部 2 の古い Data Lake Storage Gen1 アカウントの名前が contosoeastus2.azuredatalakestore.net である場合、 北ヨーロッパの新しい Data Lake Storage Gen1 アカウントに contosonortheu.azuredatalakestore.net という名前を付けることができます。

* **ツール**。 Data Lake Storage Gen1 のファイルをコピーする場合は、[Azure Data Factory のコピー アクティビティ](../data-factory/connector-azure-data-lake-store.md)を使用することをお勧めします。 Data Factory では、パフォーマンスと信頼性に優れたデータ移動がサポートされています。 Data Factory によってコピーされるのは、フォルダー階層とファイルの内容のみであることに注意してください。 古いアカウントで使用しているアクセス制御リスト (ACL) はすべて、新しいアカウントに手動で適用する必要があります。 最良のシナリオのパフォーマンス目標も含めた詳細については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](../data-factory/copy-activity-performance.md)」をご覧ください。 より迅速にデータをコピーしたい場合は、追加のクラウド データ移動単位の使用が必要になることがあります。 他の一部のツール (AdlCopy など) では、リージョン間のデータのコピーがサポートされていません。  

* **帯域幅の料金**。 Azure リージョンからデータが転送されるため、[帯域幅の料金](https://azure.microsoft.com/pricing/details/bandwidth/)が適用されます。

* **データの ACL**。 ファイルとフォルダーに ACL を適用して、新しいリージョンのデータをセキュリティで保護します。 詳細については、「[Securing data stored in Azure Data Lake Storage Gen1 (Azure Data Lake Storage Gen1 に格納されているデータのセキュリティ保護)](data-lake-store-secure-data.md)」をご覧ください。 移行を ACL の更新と調整に使用することをお勧めします。 現在の設定と同様の設定を使用することもできます。 Azure Portal、[PowerShell コマンドレット](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission)、または SDK を使用して、ファイルに適用されている ACL を表示できます。  

* **分析サービスの場所**。 最良のパフォーマンスを得るには、Azure Data Lake Analytics や Azure HDInsight などの分析サービスをデータと同じリージョンに配置する必要があります。  

## <a name="next-steps"></a>次のステップ
* [Azure Data Lake Storage Gen1 の概要](data-lake-store-overview.md)
