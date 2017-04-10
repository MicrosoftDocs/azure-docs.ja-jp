---
title: "Azure DocumentDB ポータル ツール: スクリプト エクスプローラー | Microsoft Docs"
description: "DocumentDB のサーバー側プログラミング アーティファクト (JavaScript ストアド プロシージャ、トリガー、ユーザー定義関数など) を管理するための Azure Portal のツール、DocumentDB スクリプト エクスプローラーについて説明します。"
keywords: "javascript エディター"
services: documentdb
author: kirillg
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 9d0620da-2449-4c17-82a4-24aaa46e9b3e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: 429687c6e5a196a3b489dc4dd79ae886b7ad9c38
ms.openlocfilehash: ccff673996d53d2b3b2c177bfb6fff01613b7097
ms.lasthandoff: 02/15/2017


---
# <a name="create-and-run-stored-procedures-triggers-and-user-defined-functions-using-the-documentdb-script-explorer"></a>DocumentDB スクリプト エクスプローラーを使用したストアド プロシージャ、トリガー、ユーザー定義関数の作成と実行
この記事では、DocumentDB のサーバー側プログラミング アーティファクト (ストアド プロシージャ、トリガー、ユーザー定義関数など) の表示と実行を可能にする Azure ポータルの JavaScript エディター、 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) スクリプト エクスプローラーの概要を説明します。 DocumentDB のサーバー側プログラミングの詳細については、「 [ストアド プロシージャ、データベース トリガー、UDF](documentdb-programming.md) 」という記事を参照してください。

## <a name="launch-script-explorer"></a>スクリプト エクスプローラーを起動する
1. [Azure Portal](https://portal.azure.com) の左側のナビゲーションで、![Azure DocumentDB アイコン](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **[NoSQL (DocumentDB)]** をクリックします。 

    **[NoSQL (DocumentDB)]** が表示されない場合は、下にある **[その他のサービス]** をクリックしてから、![Azure DocumentDB アイコン](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **[NoSQL (DocumentDB)]** をクリックします。
2. リソース メニューの **[スクリプト エクスプローラー]**をクリックします。
   
    ![スクリプト エクスプローラー コマンドのスクリーンショット](./media/documentdb-view-scripts/scriptexplorercommand.png)
   
    **[データベース]** と **[コレクション]** の各ドロップダウン リスト ボックスには、スクリプト エクスプローラーを起動したコンテキストに応じて値が設定されています。  たとえば、データベース ブレードから起動した場合には、現在のデータベースが設定されます。  コレクション ブレードから起動した場合には、現在のコレクションが設定されます。
3. **[データベース]** ボックスと **[コレクション]** ボックスを使用すると、スクリプト エクスプローラーを閉じて再度起動することなく、現在表示されているドキュメントが含まれるコレクションを簡単に変更できます。  
4. スクリプト エクスプローラーでは、現在読み込まれているドキュメントのセットを ID プロパティでフィルター処理することもできます。  フィルター ボックスに入力します。スクリプト エクスプローラーの一覧の結果が指定された条件に基づいてフィルター処理されます。
   
    ![フィルターの結果が表示されたスクリプト エクスプローラーのスクリーンショット](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)

    > [!IMPORTANT] 
    > スクリプト エクスプローラーのフィルター機能では、"***現在***" 読み込まれているドキュメントのみがフィルター処理されます。現在選択されているコレクションに対してスクリプトが自動的に更新されることはありません。

1. スクリプト エクスプローラーに読み込まれたスクリプトトの一覧を更新するには、ブレードの上部にある **[更新]** をクリックするだけです。
   
    ![スクリプト エクスプローラーの [最新の情報に更新] コマンドのスクリーンショット](./media/documentdb-view-scripts/scriptexplorerrefresh.png)

## <a name="create-view-and-edit-stored-procedures-triggers-and-user-defined-functions"></a>ストアド プロシージャ、トリガー、ユーザー定義関数の作成、表示、編集
スクリプト エクスプローラーを使用すると、DocumentDB サーバー側プログラミング アーティファクトに対する CRUD 操作を簡単に実行できます。  

* スクリプトを作成するには、スクリプト エクスプローラーで適切な作成コマンドをクリックし、ID を指定し、スクリプトの内容を入力し、 **[保存]**をクリックします。
  
    ![スクリプト エクスプローラーの作成オプションと JavaScript エディターのスクリーンショット](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)
* トリガーを作成する場合は、トリガーの種類とトリガー操作を指定する必要があります。
  
    ![スクリプト エクスプローラーのトリガー作成オプションのスクリーンショット](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)
* スクリプトを表示するには、関心のあるスクリプトをクリックします。
  
    ![スクリプト エクスプローラーのスクリプト表示エクスペリエンスのスクリーンショット](./media/documentdb-view-scripts/scriptexplorerviewscript.png)
* スクリプトを編集するには、JavaScript エディターで目的の変更を行い、 **[保存]**をクリックします。
  
    ![スクリプト エクスプローラーのスクリプト表示エクスペリエンスのスクリーンショット](./media/documentdb-view-scripts/scriptexplorereditscript.png)
* スクリプトの保留中の変更を破棄するには、 **[破棄]** コマンドをクリックします。
  
    ![スクリプト エクスプローラーの変更破棄エクスペリエンスのスクリーンショット](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)
* スクリプト エクスプローラーで **[プロパティ]** をクリックすると、現在読み込まれているスクリプトのシステム プロパティを簡単に表示することもできます。
  
    ![スクリプト エクスプローラーのスクリプトのプロパティ表示のスクリーンショット](./media/documentdb-view-scripts/scriptproperties.png)
  
  > [!NOTE]
  > タイムスタンプ (_ts) プロパティは内部ではエポック時間として表現されますが、スクリプト エクスプローラーでは、人間が読むことができる GMT 形式で値が表示されます。
  > 
  > 
* スクリプトを削除するには、スクリプト エクスプ ローラーで削除するスクリプトを選択し、 **[削除]** コマンドをクリックします。
  
    ![スクリプト エクスプローラーの削除コマンドのスクリーンショット](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)
* 削除アクションを **[はい]** をクリックして確認するか、**[いいえ]** をクリックして取り消します。
  
    ![スクリプト エクスプローラーの削除コマンドのスクリーンショット](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## <a name="execute-a-stored-procedure"></a>ストアド プロシージャの実行
> [!WARNING]
> スクリプト エクスプローラーでのストアド プロシージャの実行は、サーバー側のパーティション分割コレクションではまだサポートされていません。 詳細については、 [DocumentDB でのパーティション分割とスケーリング](documentdb-partition-data.md)に関するページをご覧ください。
> 
> 

スクリプト エクスプローラーでは、Azure ポータルからサーバー側のストアド プロシージャを実行できます。

* ストアド プロシージャの新規作成ブレードを開くと、既定のスクリプト (*prefix*) が提供されます。 *prefix* スクリプトまたは独自のスクリプトを実行するには、*ID* と "*入力*" を追加します。 複数のパラメーターを受け取るストアド プロシージャの場合、すべて配列内に入力する必要があります (例 *["foo", "bar"]*)。
  
    ![スクリプト エクスプローラーの [ストアド プロシージャ] ブレードで入力を追加し、ストアド プロシージャを実行するスクリーンショット](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-input.png)
* ストアド プロシージャを実行するには、スクリプト エディター ウィンドウ内で **[保存して実行]** をクリックします。
  
  > [!NOTE]
  > **[保存して実行]** コマンドでは、実行前にストアド プロシージャが保存されます。つまり、ストアド プロシージャの前回保存されたバージョンが上書きされます。
  > 
  > 
* ストアド プロシージャが正常に実行されると、状態が "*ストアド プロシージャを正常に保存し、実行しました*" になり、返された結果が *[結果]* ウィンドウに入力されます。
  
    ![スクリプト エクスプローラーの [ストアド プロシージャ] ブレードでストアド プロシージャを実行するスクリーンショット](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure.png)
* 実行中にエラーが発生した場合、エラーは *[結果]* ウィンドウに表示されます。
  
    ![スクリプト エクスプローラーのスクリプト プロパティのスクリーンショット ストアド プロシージャの実行とエラー](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-error.png)

## <a name="work-with-scripts-outside-the-portal"></a>ポータルの外部のスクリプトを使用する
Azure ポータルのスクリプト エクスプローラーは、DocumentDB のストアド プロシージャ、トリガー、ユーザー定義関数を使用する方法の&1; つです。 REST API と [クライアント SDK](documentdb-sdk-dotnet.md)を使用してスクリプトを操作することもできます。 REST API ドキュメントには、[REST を使用したストアド プロシージャ](https://msdn.microsoft.com/library/azure/mt489092.aspx)、[REST を使用したユーザー定義関数](https://msdn.microsoft.com/library/azure/dn781481.aspx)、[REST を使用したトリガー](https://msdn.microsoft.com/library/azure/mt489116.aspx)のサンプルが含まれています。 [C# を使用したスクリプト](documentdb-dotnet-samples.md#server-side-programming-examples)と [Node.js を使用したスクリプト](documentdb-nodejs-samples.md#server-side-programming-examples)の使用方法を示すサンプルもあります。

## <a name="next-steps"></a>次のステップ
DocumentDB のサーバー側プログラミングの詳細については、 [ストアド プロシージャ、データベース トリガー、UDF](documentdb-programming.md) に関する記事をご覧ください。

[ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/documentdb/) も、DocumentDB の詳細を理解する際に便利なリソースです。  


