---
title: データセット マッピングの構成
description: データセット マッピングの構成
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 581e1eef5f1d64e68a6501f56ce60218281c605d
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788566"
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



