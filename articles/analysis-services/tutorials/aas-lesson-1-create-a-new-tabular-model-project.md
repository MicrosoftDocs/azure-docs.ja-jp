---
title: "Azure Analysis Services チュートリアル - レッスン 1: 新しい表形式モデル プロジェクトを作成する |Microsoft Docs"
description: "新しい Azure Analysis Services チュートリアル プロジェクトを作成する方法を説明します｡"
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/01/2017
ms.author: owend
ms.openlocfilehash: d523e3e103b4c351d01af6f1eb3c396f9a63016a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="lesson-1-create-a-tabular-model-project"></a>レッスン 1: 表形式モデル プロジェクトを作成する

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

このレッスンでは、SQL Server Data Tools (SSDT) を使用して､1400 互換性レベルの新しい表形式モデルのプロジェクトを作成します｡ 新しいプロジェクトを作成すると､データを追加したり､モデルをオーサリングしたりできます｡ このレッスンではまた､SSDT における表形式モデルのオーサリング環境について簡単に紹介します｡  
  
このレッスンの推定所要時間: **10 分**  
  
## <a name="prerequisites"></a>前提条件  
このトピックは､表形式モデル オーサリング チュートリアルの最初のレッスンです｡ このレッスンを行うには､前提条件となるものをいくつか整えておく必要があります｡ 詳細は､｢[Azure Analysis Services - Adventure Works チュートリアル](../tutorials/aas-adventure-works-tutorial.md)｣を参照してください｡  
  
## <a name="create-a-new-tabular-model-project"></a>新しい表形式モデル プロジェクトを作成する  
  
#### <a name="to-create-a-new-tabular-model-project"></a>新しい表形式モデル プロジェクトを作成する  
  
1.  SSDT の **ファイル** メニューで､**New** > **Project**をクリックします｡  
  
2.  **New Project** ダイアログ ボックスで**Installed** > **Business Intelligence** > **Analysis Services** を展開し､**Analysis Services Tabular Project**をクリックします｡  
  
3.  [**名前**] フィールドで **AW Internet Sales** と入力し､プロジェクトファイルの場所を指定します｡  
  
    既定では､**Solution Name** はプロジェクト名と同じですが､別のソリューション名を指定することができます｡  
  
4.  **[OK]**をクリックします。  
  
5.  **[Tabular model designer]** ダイアログ ボックスで **[Integrated workspace]** を選択します｡  
  
    モデルのオーサリング中､ワークスペースはプロジェクトと同じ名前を使って表形式モデル データベースをホストします｡ 統合ワークスペースとは､SSDT が組み込みインスタンスを使用することを意味します｡このため､モデルのオーサリングのためだけに､Analysis Services サーバー インスタンスを別途インストールする必要はありません｡
      
6.  **[Compatibility level]** で **[SQL Server 2017 / Azure Analysis Services (1400)]** を選択します｡   
 
    ![aas-lesson1-tmd](../tutorials/media/aas-lesson1-tmd.png)
      
    最新バージョンの SQL Server Data Tools が使用されていないと､Compatibility level リストボックスに SQL Server 2017 / Azure Analysis Services (1400) が表示されません｡ 最新バージョンを入手するには､[Install SQL Server Data tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) を参照してください｡  
      
  
## <a name="understanding-the-ssdt-tabular-model-authoring-environment"></a>SSDT の表形式モデル オーサリング環境について  
新しい表形式モデル プロジェクトを作成しましたから､SSDT における表形式モデル オーサリング環境を少し探ってみましょう｡  
  
プロジェクトを作成すると､SSDT にそのプロジェクトが表示されます｡ **表形式モデル エクスプローラー**の右側には､モデル内の全オブジェクトがツリー表示されます｡ データのインポートはまだのため､フォルダーは空です｡ オブジェクト フォルダを右クリックすると､メニュー バーにあるものに似たアクションを実行できます｡ このチュートリアルを順を追って進めていく過程で､表形式モデル エクスプローラーを使用して､モデル プロジェクト内のさまざまなオブジェクトをナビゲートすることになります｡

![aas-lesson1-tme](../tutorials/media/aas-lesson1-tme.png)

**[Solution Explorer]** タブをクリックします｡このタブに **Model.bim** ファイルがあります｡ 左側にデザイナー ウィンドウが表示されない場合は (Model.bim タブのウィンドウが空)､ **AW Internet Sales Project** の下の **Solution Explorer** で **Model.bim** ファイルをクリックします｡ Model.bim ファイルには､モデル プロジェクトのメタデータが含まれます｡ 

![aas-lesson1-se](../tutorials/media/aas-lesson1-se.png)
  
**[Model.bim]** をクリックします｡ **[プロパティ]** ウィンドウにモデルのプロパティが表示されます｡そのうち最も重要なプロパティは､**DirectQuery Mode** プロパティです｡ このプロパティは､モデルを In-Memory (Off) または DirectQuery (On) のどちらのモードでデプロイするかを指定します｡ このチュートリアルでは､In-Memory モードでモデルをオーサリングし､デプロイします｡

![aas-lesson1-properties](../tutorials/media/aas-lesson1-properties.png)
  
モデル プロジェクトの作成では､いくつかのモデル プロパティはデータ モデリング設定に従って自動的に設定されます｡これらの設定は､**[ツール]** メニュー > **[オプション]** ダイアログ ボックスで指定できます｡ Data Backup と Workspace Retention､および Workspace Server は､それぞれワークスペース データベース (モデル オーサリング用データベース) のバックアップとインメモリー保持､構築の方法と場所を指定するプロパティです｡ これらのプロパティ設定は必要に応じて後で変更できるので､ここでは現在の設定のままにしておいてください｡  

**[Solution Explorer]** で **[AW Internet Sales]** (プロジェクト) を右クリックし､**[プロパティ]** をクリックします｡ **[AW Internet Sales Property Pages]** ダイアログ ボックスが表示されます｡ これらプロパティの一部は､後でモデルをデプロイするときに設定します｡  
  
SSDT をインストールすると､Visual Studio 環境にいくつかのメニュー項目が新たに追加されています｡ **[モデル]** メニューをクリックします。 このメニューからは､データのインポート､ワークスペース データの再表示､Excel でのモデルの参照､パースペクティブとロールの作成､モデル ビューの選択､計算オプションの設定を行うことができます｡ **[テーブル]** メニューをクリックします｡ このメニューからは､リレーションシップの作成と管理､日付テーブル設定値の指定､パーティションの作成､テーブルのプロパティの編集を行うことができます｡ **[列]** メニューをクリックすると､テーブル内の列の追加と削除､列の固定､並べ替え順序の指定を行うことができます｡ SSDT または､バーにいくつかボタンを追加します｡ AutoSum 機能で最も有用なのは､選択した列の標準集計メジャーを作成する機能です｡ ツールバーの他のボタンからは､よく使う機能やコマンドに素早くアクセスできます｡  
  
表形式モデルに特有のさまざまな機能に用意されているダイアログ ボックスや場所をいくつか探ってみてください｡ 一部項目がまだアクティブになっていませんが､表形式モデルのオーサリング環境を十分に理解できます｡  
  

## <a name="whats-next"></a>次の手順
[レッスン 2: データを取得する](../tutorials/aas-lesson-2-get-data.md)

  
  
  
