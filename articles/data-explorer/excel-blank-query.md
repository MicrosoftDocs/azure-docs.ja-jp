---
title: Microsoft Excel にインポートされた Azure Data Explorer Kusto クエリを使用してデータを視覚化する
description: この記事では、Azure Data Explorer Kusto クエリを Microsoft Excel にインポートする方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 55198e0c38c2922d69b68d9ce62e16ea25e9cc44
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70174458"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Microsoft Excel にインポートされた Azure Data Explorer Kusto クエリを使用してデータを視覚化する

Azure Data Explorer には、Excel のデータに接続する方法が 2 つ用意されています。ネイティブ コネクタを使用する方法と、Azure Data Explorer からクエリをインポートする方法です。 この記事では、Azure Data Explorer から Excel にクエリをインポートしてデータを視覚化する方法について説明します。 Kusto クエリを Excel データ ソースとして追加し、そのデータに対して追加の計算や視覚化を行います。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* Azure Active Directory のメンバーである組織の電子メール アカウント。このアカウントによって [Azure Data Explorer ヘルプ クラスター](https://dataexplorer.azure.com/clusters/help/databases/Samples)に接続できます。 
<br>or</br>
* [テスト クラスターとデータベース](create-cluster-database-portal.md)を作成し、[Azure Data Explorer Web UI アプリケーション](https://dataexplorer.azure.com/)にサインインします。

## <a name="define-kusto-query-as-an-excel-data-source"></a>Kusto クエリを Excel データ ソースとして定義する

1. [Azure Data Explorer Web UI](https://dataexplorer.azure.com/clusters/help/databases/Samples) で、クエリを実行して結果を確認します。

1. **[共有]** タブを選択し、 **[Query to Power BI]\(Power BI へのクエリ\)** を選択します。

    ![Power BI への Web UI クエリ](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. 次の通知を含むウィンドウが表示されます。

    ![クエリをクリップボードにエクスポートする](media/excel-blank-query/query-exported-to-clipboard.png)

1. **Microsoft Excel**を開きます。

1. **[データ]** タブで、 **[データの取得]**  >  **[その他のソースから]**  >  **[空のクエリ]** の順に選択します。

    ![データを取得して空のクエリを選択する](media/excel-blank-query/get-data-blank-query.png)

1. **[Power Query エディター]** ウィンドウが開きます。 そのウィンドウで、 **[詳細エディター]** を選択します。

    ![Power Query エディター ウィンドウ](media/excel-blank-query/power-query-editor.png)

1. **[詳細エディター]** ウィンドウで、クリップボードにエクスポートしたクエリを貼り付け、 **[完了]** を選択します。

    ![詳細エディター クエリ](media/excel-blank-query/advanced-editor-query.png)    

1. 認証するには、 **[資格情報の編集]** を選択します。

    ![資格情報を編集](media/excel-blank-query/edit-credentials.png)

1. **[組織アカウント]** 、 **[サインイン]** の順に選択します。 サインイン プロセスを完了し、 **[接続]** を選択します。

    ![サインインを完了する](media/excel-blank-query/complete-sign-in.png)

    前の手順を繰り返して、さらにクエリを追加します。 クエリの名前をよりわかりやすい名前に変更できます。

1. **[閉じて読み込む]** ボタンを選択して、データを Excel に取り込みます。

    ![[閉じて読み込む] を選択する](media/excel-blank-query/close-and-load.png)

1. これで、データが Excel に取り込まれました。 **[最新の情報に更新]** ボタンを選択して、クエリを更新します。

    ![Excel でデータを表示する](media/excel-blank-query/data-in-excel.png)