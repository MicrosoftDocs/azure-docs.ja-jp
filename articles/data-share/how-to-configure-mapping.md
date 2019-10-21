---
title: Azure Data Share プレビューでデータセット マッピングを構成する
description: Azure Data Share プレビューを使用して、受信した共有のデータセット マッピングを構成する方法について説明します。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 035235116240bdc6de3bc689c2430fee018b202d
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169146"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>Azure Data Share プレビューで受信した共有のデータセット マッピングを構成する方法

この記事では、Azure Data Share プレビューで受信した共有のデータセット マッピングを構成する方法について説明します。 データ共有への招待を承諾し、[Accept and Configure later] (承諾して後で構成する) を選択した場合、これを行うことができます。 それ以外の場合は、受信データのコピー先ストレージ アカウントを変更することもできます。 

## <a name="navigate-to-a-received-data-share"></a>受信したデータ共有に移動する

Azure Data Share サービスで、受信した共有に移動し、 **[詳細]** タブを選択します。 

![データセット マッピング](./media/dataset-mapping.png "データセット マッピング") 

コピー先を割り当てるデータセットの横にあるチェックボックスをオンにし、 **[+ Map to target] (+ ターゲットへのマッピング)** をクリックします。 既にコピー先ストレージ アカウントが構成されており、マッピングを別のストレージ アカウントに変更したい場合は、最初にマッピングを解除しなければならない場合があります。 

![ターゲットへのマッピング](./media/dataset-map-target.png "ターゲットへのマッピング") 

## <a name="select-a-new-storage-account"></a>新しいストレージ アカウントを選択する 

データを配置したいストレージ アカウントを選択します。 既にマッピングされているストレージ アカウントに存在するデータが、新しいストレージ アカウントに自動的に移動されることはありません。

![ターゲット ストレージ アカウント](./media/map-target.png "ターゲット ストレージ") 

## <a name="next-steps"></a>次の手順

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。



