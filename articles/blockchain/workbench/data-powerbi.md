---
title: Microsoft Power BI で Azure Blockchain Workbench データを使用する
description: Microsoft Power BI で Azure Blockchain Workbench SQL DB のデータを読み込んで表示する方法を説明します。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 1e12af686a450d39332c37700b9a14b9eb620307
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530894"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Microsoft Power BI で Azure Blockchain Workbench データを使用する

Microsoft Power BI は Power BI Desktop を使用して SQL DB データベースから高度なレポートを容易に生成して、[ https://www.powerbi.com](https://www.powerbi.com) に公開する機能を提供します。

この記事には、Power BI Desktop から Azure Blockchain Workbench の SQL データベースへ接続し、レポートを作成し、powerbi.com にレポートをデプロイする方法に関するステップ バイ ステップのチュートリアルが含まれています。

## <a name="prerequisites"></a>前提条件

* [Power BI Desktop](https://aka.ms/pbidesktopstore) をダウンロードします。

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Power BI を Azure Blockchain Workbench のデータに接続する

1.  Power BI Desktop を開きます。
2.  **[データの取得]** を選択します。

    ![データを取得する](./media/data-powerbi/get-data.png)
3.  データ ソースの種類の一覧から **[SQL Server]** を選択します。

4.  ダイアログ ボックスでサーバーとデータベースの名前を指定します。 データをインポートするか、**[DirectQuery]** を実行するかを指定します。 **[OK]** を選択します。

    ![SQL Server の選択](./media/data-powerbi/select-sql.png)

5.  Azure Blockchain Workbench にアクセスするデータベースの資格情報を入力します。 **[データベース]** を選択して、資格情報を入力します。

    Azure Blockchain Workbench のデプロイプロセスによって作成された資格情報を使用している場合、ユーザー名は **dbadmin** となり、パスワードはデプロイ時に指定したパスワードです。

    ![SQL DB の設定](./media/data-powerbi/db-settings.png)

6.  データベース接続に成功すると、**ナビゲーター** ダイアログに、テーブルとデータベース内で使用可能なビューが表示されます。 ビューは、レポート向けに設計されており、すべてに **vw** のプレフィックスが付いています。

    ![ナビゲーター](./media/data-powerbi/navigator.png)

7.  含めるビューを選択します。 ここではデモのため、コントラクト上で実行されたアクションの詳細を提供する **vwContractAction** を含めます。

    ![ビューの選択](./media/data-powerbi/select-views.png)

これで Power BI で通常行うように、レポートを作成して公開することができます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench のデータベース ビュー](database-views.md)