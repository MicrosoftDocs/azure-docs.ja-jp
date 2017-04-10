---
title: "Log Analytics のカスタム フィールド | Microsoft Docs"
description: "Log Analytics のカスタム フィールド機能を使用すると、収集済みレコードのプロパティに追加される OMS データから独自の検索可能なフィールドを作成できます。  この記事では、カスタム フィールドを作成するプロセスと、サンプル イベントの詳細なチュートリアルについて説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b98dcbcf219276bb9a7fd24e7f8c8c8eba806cf0


---
# <a name="custom-fields-in-log-analytics"></a>Log Analytics のカスタム フィールド
Log Analytics の **カスタム フィールド** 機能を使用すると、独自の検索可能なフィールドを追加して、OMS リポジトリの既存のレコードを拡張できます。  カスタム フィールドは、同じレコードの他のプロパティから抽出したデータから自動的に設定されます。

![カスタム フィールドの概要](media/log-analytics-custom-fields/overview.png)

たとえば、以下のサンプル レコードには、イベントの説明に埋もれている有益なデータがあります。  このデータを別のプロパティに抽出すると、並べ替えやフィルター処理などに使用できます。

![[ログの検索] ボタン](media/log-analytics-custom-fields/sample-extract.png)

> [!NOTE]
> プレビュー版では、カスタム フィールド数の上限はワークスペース内で 100 個です。  この機能が一般公開されるときには、この上限は上がる予定です。
> 
> 

## <a name="creating-a-custom-field"></a>カスタム フィールドを作成する
カスタム フィールドを作成する場合、その値を設定するために使用するデータを Log Analytics が認識する必要があります。  Log Analytics は、FlashExtract という Microsoft Research のテクノロジを使用して、そのデータをすばやく特定しています。  ユーザーが明示的な指示をしなくても、Log Analytics は指定した例から抽出する目的のデータを認識します。

以下のセクションでは、カスタム フィールドを作成する手順について説明します。  サンプル抽出のチュートリアルについては、この記事の末尾を参照してください。

> [!NOTE]
> カスタム フィールドは、指定した条件に一致するレコードが OMS データ ストアに追加されるときに設定されます。そのため、カスタム フィールドの作成後に収集されたレコードのデータのみが表示されます。  カスタム フィールドの作成時にデータ ストアに既にあったレコードには、カスタム フィールドは追加されません。
> 
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>手順 1 - カスタム フィールドを追加するレコードを指定する
最初の手順は、カスタム フィールドを追加するレコードの指定です。  まず[標準のログ検索](log-analytics-log-searches.md)から始めて、Log Analytics が認識する、モデルとして動作するレコードを選択します。  データをカスタム フィールドに抽出しようとすると、**フィールド抽出ウィザード**が開きます。この画面で、条件を検証し、調整します。

1. **ログ検索** を開き、 [クエリを使用してカスタム フィールドを追加するレコードを取得](log-analytics-log-searches.md) します。
2. Log Analytics でカスタム フィールドを設定するデータを抽出する際に、モデルとして機能するために使用するレコードを選択します。  このレコードから抽出するデータを特定すると、Log Analytics はその情報を使用して、すべての同様のレコードのカスタム フィールドを設定します。
3. レコードのテキスト プロパティの左にあるボタンをクリックし、 **[フィールドの抽出]**を選択します。
4. **フィールドの抽出ウィザード**が開き、選択したレコードが **[メインの例]** 列に表示されます。  選択したプロパティと同じ値を持つ、そのレコードのカスタム フィールドが定義されます。  
5. 目的の選択内容ではない場合、追加のフィールドを選択して条件を絞り込みます。  条件のフィールド値を変更するには、取り消して、目的の条件と一致する別のレコードを選択する必要があります。

### <a name="step-2---perform-initial-extract"></a>手順 2 - 初回の抽出を実行する
カスタム フィールドを追加するレコードを特定したら、抽出するデータを特定します。  Log Analytics は、その情報を使用して、似たレコード内の似たパターンを特定します。  その後に、結果を検証して、Log Analytics で分析に使用する詳細情報を指定します。

1. カスタム フィールドを設定するサンプル レコードのテキストを選択します。  フィールドの名前を指定し、初回の抽出を実行するダイアログ ボックスが表示されます。  **\_CF** という文字が自動的に付加されます。
2. **[抽出]** をクリックして、収集したレコードの分析を実行します。  
3. **[概要]** と **[検索結果]** セクションには抽出結果が表示されるので、正確さを確認できます。  **[概要]** には、レコードの特定に使用される条件と、特定された各データ値の件数が表示されます。  **[検索結果]** には、条件と一致するレコードの詳細な一覧が表示されます。

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>手順 3 - 抽出の正確さを確認し、カスタム フィールドを作成する
初回の抽出を実行すると、既に収集されたデータに基づいて結果が Log Analytics に表示されます。  結果が正確と判断したら、追加作業なしでカスタム フィールドを作成できます。  結果が正確ではない場合、Log Analytics がロジックを改善できるように結果を調整することができます。

1. 初回の抽出の値が正しくない場合、不正確なレコードの横にある **[編集]** アイコンをクリックし、**[この選択内容の変更]** を選択して選択内容を変更します。
2. エントリは、**[メインの例]** の **[追加の例]** セクションにコピーされます。  この選択内容を調整することで、Log Analytics が正しい選択内容を理解しやすくなります。
3. **[抽出]** をクリックし、この新しい情報を使用してすべての既存のレコードを評価します。  この新しい情報に基づいて、変更したレコード以外のレコードの結果を変更できます。
4. 抽出のすべてのレコードによって、新しいカスタム フィールドを設定できるデータが正しく特定されるまで、修正を加え続けます。
5. 結果に満足したら、 **[抽出の保存]** をクリックします。  カスタム フィールドは定義されましたが、まだレコードには追加されません。
6. 指定した条件に一致する新しいレコードが収集されるまで待ってから、ログ検索を再実行してください。 新しいレコードにはカスタム フィールドがあります。
7. 他のレコードのプロパティと同様に、カスタム フィールドを使用します。  カスタム フィールドを使用してデータを集計してグループ化できます。また、カスタム フィールドを使用して新しい分析を行うこともできます。

## <a name="viewing-custom-fields"></a>カスタム フィールドを表示する
OMS ダッシュボードの **[設定]** タイルから、管理グループのすべてのカスタム フィールドを一覧表示することができます。  **[データ]** を選択し、ワークスペースのすべてのカスタム フィールドの一覧について **[カスタム フィールド]** を選択します。  

![カスタム フィールド](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>カスタム フィールドを削除する
カスタム フィールドを削除するには、2 つの方法があります。  1 つは、上記のように完全な一覧を表示するときの各フィールドの **[削除]** オプションです。  もう 1 つは、レコードを取得し、フィールドの左側にあるボタンをクリックする方法です。  メニューには、カスタム フィールドを削除するオプションが表示されます。

## <a name="sample-walkthrough"></a>サンプルのチュートリアル
次のセクションでは、カスタム フィールドの作成例を段階的に説明します。  この例では、サービスの変化する状態を示す Windows イベントのサービス名を抽出します。  これは、Service Control Manager によって Windows コンピューターのシステム ログに作成されるイベントに依存します。  この例に従うには、 [システム ログの情報イベントを収集する必要があります](log-analytics-data-sources-windows-events.md)。

Service Control Manager からイベント ID が 7036 のすべてのイベント (サービスの開始または停止を示すイベント) を返すために、ここでは次のクエリを入力します。

![クエリ](media/log-analytics-custom-fields/query.png)

イベント ID が 7036 の任意のレコードを選択します。

![ソース レコード](media/log-analytics-custom-fields/source-record.png)

**[RenderedDescription]** プロパティにサービス名を表示し、そのプロパティの横にあるボタンを選択します。

![フィールドの抽出](media/log-analytics-custom-fields/extract-fields.png)

**フィールド抽出ウィザード**が開き、**[メインの例]** 列の **[EventLog]** フィールドと **[EventID]** フィールドが選択されます。  これは、システム ログからイベント ID が 7036 のイベントに対してカスタム フィールドが定義されることを示します。  これで十分なので、他のフィールドは選択する必要がありません。

![[主な例]](media/log-analytics-custom-fields/main-example.png)

**[RenderedDescription]** プロパティのサービス名を選択し、**[サービス]** を使用してサービス名を指定します。  カスタム フィールド名は **Service_CF** になります。

![フィールドのタイトル](media/log-analytics-custom-fields/field-title.png)

サービス名が正しく指定されたレコードと、指定されていないレコードがあります。   **[検索結果]** は、**[WMI Performance Adapter]** の名前の一部が選択されなかったことを示しています。  **[概要]** には、**DPRMA** サービスを含む 4 件のレコードに余計な単語が不適切に含まれ、**Windows Modules Installer** ではなく **Modules Installer** と指定されたレコードが 2 件あります。  

![[検索結果]](media/log-analytics-custom-fields/search-results-01.png)

まず **WMI Performance Adapter** レコードから始めます。  そのレコードの編集アイコンをクリックし、 **[この選択内容の変更]**をクリックします。  

![選択内容の変更](media/log-analytics-custom-fields/modify-highlight.png)

**WMI** という単語を含むようにこの選択内容を広げて、抽出を再実行します。  

![その他の例](media/log-analytics-custom-fields/additional-example-01.png)

**WMI Performance Adapter** のエントリが修正されたことを確認し、Log Analytics にもその情報が使用され、**Windows Module Installer** のレコードが修正されたことを確認できます。  **[概要]** セクションで、**DPMRA** がまだ正しく特定されていないことを確認できます。

![[検索結果]](media/log-analytics-custom-fields/search-results-02.png)

DPMRA サービスを含むレコードまでスクロールし、同じプロセスでそのレコードを修正します。

![その他の例](media/log-analytics-custom-fields/additional-example-02.png)

 抽出を実行すると、すべての結果が正しいことを確認できます。

![[検索結果]](media/log-analytics-custom-fields/search-results-03.png)

**Service_CF** が作成され、まだレコードに追加されていないことがわかります。

![最初の数](media/log-analytics-custom-fields/initial-count.png)

ある程度時間が経ち、新しいイベントが収集されると、その条件に一致するレコードに **Service_CF** フィールドが追加されたことを確認できます。

![最終結果](media/log-analytics-custom-fields/final-results.png)

他のレコードのプロパティと同様に、カスタム フィールドを使用できるようになります。  それを説明するために、新しい **Service_CF** フィールドでグループ化し、最もアクティブなサービスを調べるクエリを作成します。

![クエリによるグループ化](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>次のステップ
* 基準のカスタム フィールドを使用してクエリを作成するための、 [ログ検索](log-analytics-log-searches.md) について説明します。
* カスタム フィールドを使用して解析対象の[カスタム ログ ファイル](log-analytics-data-sources-custom-logs.md)を監視します。




<!--HONumber=Nov16_HO3-->


