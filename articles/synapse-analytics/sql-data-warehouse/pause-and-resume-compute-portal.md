---
title: 'クイックスタート: Azure portal を使用して専用 SQL プールのコンピューティングを一時停止および再開する'
description: Azure portal を使用して、専用 SQL プールのコンピューティングを一時停止してコストを節約します。 データ ウェアハウスを使用する準備ができたら、コンピューティングを再開します。
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f9cc5083c4b515454b9d5cbc40ed3b48cba80211
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602164"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-via-the-azure-portal"></a>クイックスタート: Azure portal を使用して専用 SQL プールのコンピューティングを一時停止および再開する

Azure portal を使用して、専用 SQL プールのコンピューティング リソースを一時停止および再開できます。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="before-you-begin"></a>開始する前に

[ポータルでの作成と接続](../quickstart-create-sql-pool-portal.md)に関する記事に従って、**mySampleDataWarehouse** という名前の専用 SQL プールを作成してください。 

## <a name="pause-compute"></a>コンピューティングの一時停止

コストを減らすために、オンデマンドでコンピューティング リソースを一時停止および再開できます。 たとえば、夜間と週末にデータベースを使用しない場合、その期間にデータベースを一時停止して、日中に再開することができます。
 
>[!NOTE]
>データベースが一時停止されている間、コンピューティング リソースへの課金は行われません。 ただし、ストレージに対する課金は引き続き行われます。 

専用 SQL プールを一時停止するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[Dedicated SQL pool]\(専用 SQL プール\)** ページに移動して SQL プールを開きます。 
3. **[状態]** が **[オンライン]** であることを確認します。

    ![コンピューティングがオンライン](././media/pause-and-resume-compute-portal/compute-online.png)

4. 専用 SQL プールを一時停止するには、 **[一時停止]** ボタンをクリックします。 
5. 続行してよいかどうかを確認するメッセージが表示されます。 **[はい]** をクリックします。
6. 少し待つと、 **[状態]** が **[一時停止中]** になります。

    ![スクリーンショットは、ステータス値が [一時停止中] になっているサンプル データ ウェアハウスの Azure portal を示しています。](./media/pause-and-resume-compute-portal/pausing.png)

7. 一時停止操作が完了すると、状態が **[一時停止]** になり、オプション ボタンが **[再開]** になります。
8. これで専用 SQL プールのコンピューティング リソースがオフラインになりました。 サービスを再開するまで、コンピューティングの料金は発生しません。

    ![コンピューティングがオフライン](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>コンピューティングの再開

専用 SQL プールを再開するには、次の手順を実行します。

1. **[Dedicated SQL pool]\(専用 SQL プール\)** ページに移動して SQL プールを開きます。
3. **[mySampleDataWarehouse]** ページで **[状態]** が **[一時停止]** になっていることがわかります。

    ![コンピューティングがオフライン](././media/pause-and-resume-compute-portal/compute-offline.png)

1. SQL プールを再開するには、 **[再開]** をクリックします。 
1. 開始してよいかどうかを確認するメッセージが表示されます。 **[はい]** をクリックします。
1. **[状態]** が **[再開中]** になっていることがわかります。

    ![スクリーンショットは、[開始] ボタンが選択され、ステータス値が [再開中] になっているサンプル データ ウェアハウスの Azure portal を示しています。](./media/pause-and-resume-compute-portal/resuming.png)

1. SQL プールがオンライン状態に戻ると、状態が **[オンライン]** になり、オプション ボタンが **[一時停止]** になります。
1. これで SQL プールのコンピューティング リソースがオンラインになりました。サービスを使用することができます。 コンピューティングの課金が再開されます。

    ![コンピューティングがオンライン](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

データ ウェアハウス ユニットと専用 SQL プールに格納されているデータに対して課金されます。 これらのコンピューティングとストレージのリソースは別々に請求されます。 

- データをストレージ内に保持する場合は、コンピューティングを一時停止します。
- それ以上課金されないようにする場合は、専用 SQL プールを削除できます。 

必要に応じて、以下の手順でリソースをクリーンアップします。

1. [Azure portal](https://portal.azure.com) にサインインし、専用 SQL プールを選択します。

    ![リソースをクリーンアップする](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. コンピューティング リソースを一時停止するには、 **[一時停止]** ボタンをクリックします。 

1. コンピューティング リソースやストレージに課金されないように専用 SQL プールを削除するには、 **[削除]** をクリックします。



## <a name="next-steps"></a>次のステップ

専用 SQL プールに対するコンピューティングの一時停止と再開を行いました。 [専用 SQL プールへのデータの読み込み](./load-data-from-azure-blob-storage-using-copy.md)の方法の詳細については、次の記事に進んでください。 コンピューティング機能の管理の詳細については、[コンピューティングの管理の概要](sql-data-warehouse-manage-compute-overview.md)に関する記事を参照してください。