---
title: クイック スタート:Azure SQL Data Warehouse のコンピューティングをスケールアウトする - Azure Portal | Microsoft Docs
description: Azure Portal で Azure SQL Data Warehouse のコンピューティングをスケーリングします。 コンピューティングをスケールアウトしてパフォーマンスを向上させます。または、コンピューティングをスケールバックしてコストを削減します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: b02259e2eaf497fb1bfefc4c1ed7611a22394d48
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894191"
---
## <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>クイック スタート:Azure portal で Azure SQL Data Warehouse のコンピューティングをスケーリングする

Azure Portal で Azure SQL Data Warehouse のコンピューティングをスケーリングします。 [コンピューティングをスケールアウト](sql-data-warehouse-manage-compute-overview.md)してパフォーマンスを向上させます。または、コンピューティングをスケールバックしてコストを削減します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="before-you-begin"></a>開始する前に

既存のデータ ウェアハウスをスケーリングできます。または、[ポータルでの作成と接続に関するクイック スタート](create-data-warehouse-portal.md)を使用して、**mySampleDataWarehouse** という名前のデータ ウェアハウスを作成できます。  このクイックスタートでは、**mySampleDataWarehouse** をスケーリングします。

>[!Note]
>スケーリングするには、お使いのデータ ウェアハウスがオンラインになっている必要があります。 

## <a name="scale-compute"></a>コンピューティングのスケーリング

データ ウェアハウス ユニットを増減することにより、SQL Data Warehouse のコンピューティング リソースをスケーリングできます。 [ポータルでの作成と接続に関するクイック スタート]\(create-data-warehouse-portal.md)では、**mySampleDataWarehouse** を作成し、それを 400 DWU で初期化しました。 次の手順では、**mySampleDataWarehouse** の DWU を調整します。

Data Warehouse ユニットを変更するには:

1. Azure portal の左側のページで **[SQL データ ウェアハウス]** をクリックします。
2. **[SQL データ ウェアハウス]** ページで **mySampleDataWarehouse** を選びます。 データ ウェアハウスが開きます。
3. **[スケール]** をクリックします。

    ![[スケール] をクリックします。](media/quickstart-scale-compute-portal/click-scale.png)

2. [スケール] パネルで、スライダーを左または右に移動して DWU 設定を変更します。

    ![スライダーの移動](media/quickstart-scale-compute-portal/scale-dwu.png)

3. **[Save]** をクリックします。 確認メッセージが表示されます。 **[はい]** をクリックして確定します。キャンセルするには、**[いいえ]** をクリックします。

    ![[保存] をクリックします。](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>次の手順
ここでは、データ ウェアハウスの計算をスケーリングする方法について学習しました。 Azure SQL Data Warehouse の詳細については、データの読み込みに関するチュートリアルを参照してください。

> [!div class="nextstepaction"]
>[SQL Data Warehouse にデータを読み込む](load-data-from-azure-blob-storage-using-polybase.md)
