---
title: Microsoft Power BI で Azure Blockchain Workbench データを使用する
description: Microsoft Power BI で Azure Blockchain Workbench SQL DB のデータを読み込んで表示する方法を説明します。
ms.date: 04/22/2020
ms.topic: how-to
ms.reviewer: sunri
ms.openlocfilehash: 2a5e67a8416c57c1a0cb039733a93608a8919be6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85214232"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Microsoft Power BI で Azure Blockchain Workbench データを使用する

Microsoft Power BI は Power BI Desktop を使用して SQL DB データベースから高度なレポートを容易に生成して、[https://www.powerbi.com](https://www.powerbi.com) に公開する機能を提供します。

この記事には、Power BI Desktop から Azure Blockchain Workbench の SQL データベースへ接続し、レポートを作成し、powerbi.com にレポートをデプロイする方法に関するステップ バイ ステップのチュートリアルが含まれています。

## <a name="prerequisites"></a>前提条件

* [Power BI Desktop](https://powerbi.microsoft.com/desktop/) をダウンロードします。

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Power BI を Azure Blockchain Workbench のデータに接続する

1.  Power BI Desktop を開きます。
2.  **[データの取得]** を選択します。

    ![データを取得する](./media/data-powerbi/get-data.png)
3.  データ ソースの種類の一覧から **[SQL Server]** を選択します。

4.  ダイアログ ボックスでサーバーとデータベースの名前を指定します。 データをインポートするか、 **[DirectQuery]** を実行するかを指定します。 **[OK]** を選択します。

    ![SQL Server の選択](./media/data-powerbi/select-sql.png)

5.  Azure Blockchain Workbench にアクセスするデータベースの資格情報を入力します。 **[データベース]** を選択して、資格情報を入力します。

    Azure Blockchain Workbench のデプロイプロセスによって作成された資格情報を使用している場合、ユーザー名は **dbadmin** となり、パスワードはデプロイ時に指定したパスワードです。

    ![SQL DB の設定](./media/data-powerbi/db-settings.png)

6.  データベース接続に成功すると、**ナビゲーター** ダイアログに、テーブルとデータベース内で使用可能なビューが表示されます。 ビューは、レポート向けに設計されており、すべてに **vw** のプレフィックスが付いています。

    ![ナビゲーター](./media/data-powerbi/navigator.png)

7.  含めるビューを選択します。 ここではデモのため、コントラクト上で実行されたアクションの詳細を提供する **vwContractAction** を含めます。

    ![ビューの選択](./media/data-powerbi/select-views.png)

これで Power BI で通常行うように、レポートを作成して公開することができます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench のデータベース ビュー](database-views.md)