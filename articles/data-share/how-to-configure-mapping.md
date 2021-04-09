---
title: Azure Data Share でデータセットのマッピングを構成する
description: Azure Data Share を使用して、受信した共有のデータセットのマッピングを構成する方法について説明します。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: eed3e8275400a3e677df53b9d62cf0e0bc70271c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88257841"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Azure Data Share で受信した共有のデータセットのマッピングを構成する方法

この記事では、Azure Data Share で受信した共有のデータセットのマッピングを構成する方法について説明します。 データセットのマッピングは、データを受信するターゲット データ ストアを指定する場合や、ターゲット データ ストアを変更する場合に構成する必要があります。

## <a name="navigate-to-a-received-data-share"></a>受信したデータ共有に移動する

Azure Data Share サービスで、自分が受信した共有に移動し、 **[データセット]** タブを選択します。 

![データセットのマッピング](./media/dataset-mapping.png "データセットのマッピング") 

ターゲット先を割り当てるデータセットの横にあるチェックボックスをオンにします。 **[+ ターゲットへのマップ]** を選択して、新しいターゲット先ストアを選択します。 データセットが既にマップ済みで、ターゲット データ ストアを変更したい場合は、まず **[マップ解除]** を選択します。

![ターゲットへのマップ](./media/dataset-map-target.png "ターゲットへのマップ") 

## <a name="select-a-target-store"></a>ターゲット ストアを選択する

データを配置するターゲット データ ストアの種類を選択します。 スナップショット ベースの共有の場合、以前にマップされたストレージ アカウント内に既に存在しているデータが、新しいターゲット ストアに自動的に移動されることはありません。 インプレース共有の場合は、指定された場所にあるデータ ストアを選択します。 この場所は、データ プロバイダーのソース データ ストアが配置されている Azure データ センターです。

![ターゲット ストレージ アカウント](./media/dataset-map-target-sql.png "ターゲット ストレージ") 

## <a name="select-a-file-format-sql-sources-only"></a>ファイル形式の選択 (SQL ソースのみ)

ソース データが SQL ベースのソースのものであり、それをファイルとして受信したい場合は、受信する形式を選択できます。 

![形式の選択](./media/sql-file-formats.png "SQL ファイルの形式")

## <a name="next-steps"></a>次のステップ

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。



