---
title: "Azure Log Analytics におけるログ検索でのデータ収集 | Microsoft Docs"
description: "ユーザーはログの検索を通じて、環境内のさまざまなソースから収集したコンピューター データを組み合わせて相互の関係を比較することができます。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 0d7b6712-1722-423b-a60f-05389cde3625
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a0c8af30fbed064001c3fd393bf0440aa1cb2835
ms.openlocfilehash: d4935af0647f0629cca327a7e87c29f1252af382
ms.lasthandoff: 02/28/2017


---
# <a name="find-data-using-log-searches"></a>ログ検索を使用してデータを探す

Log Analytics の中心にあるのは、環境内のさまざまなソースから収集したコンピューター データを組み合わせて相互の関係を比較できるログ検索機能です。 各種のソリューションも、特定の問題領域に関するj測定基準を得るためのログ検索機能を備えています。

検索クエリは [検索] ページで作成できます。検索結果は、ファセット コントロールを使用してフィルタリングすることができます。 検索結果に対して変換やフィルター処理、レポート作成などを実行する高度なクエリを作成することもできます。

ほとんどのソリューション ページに、一般的な検索クエリが表示されます。 OMS コンソールで、タイルをクリックするか他の項目にドリルインし、ログ検索を使用して項目の詳細を表示できます。

このチュートリアルでは、ログ検索を使用するときの基礎について、そのすべてを例を通じて説明します。

単純で実用的な例から始めて、実際的な使用事例をあげながら、データから洞察を引き出すために構文をどのように使用するかを理解できるように説明を進めていきます。

検索テクニックが十分身に付いたら、「 [Log Analytics log search reference (Log Analytics のログ検索リファレンス)](log-analytics-search-reference.md)」を参照してください。

## <a name="use-basic-filters"></a>基本的なフィルターを使用する
最初に知るべきことは検索クエリの先頭部分、つまり "|" 縦棒パイプ文字の前は常に **"フィルター" であることです。 それは TSQL での WHERE 句と考えることができ、OMS データ ストアから引き出すデータのサブセットは ** "何か" を決定します。 データ ストアでの検索は、ほとんどが抽出するデータの特性を指定することに関係するため、クエリが WHERE 句から始まるのは自然なことです。

使用できる最も基本的なフィルターは、'error'、'timeout'、コンピューター名などの **"キーワード" です。 通常、この種の単純なクエリでは、同じ結果セットの中にさまざまな形のデータが返ります。 Log Analytics がシステム内に異なる ** "種類" のデータを持っているのはこのためです。

### <a name="to-conduct-a-simple-search"></a>単純な検索を実行するには
1. OMS ポータルで **[Log Search]**(ログの検索) をクリックします。  
    ![検索タイル](./media/log-analytics-log-searches/oms-overview-log-search.png)
2. クエリ フィールドに 「 `error` 」と入力し、 **[検索]**をクリックします。  
    ![error の検索](./media/log-analytics-log-searches/oms-search-error.png)  
    たとえば、次の図に示す `error` のクエリでは、100,000 件の **Event** レコード (Log Management が収集)、18 件の **ConfigurationAlert** レコード (Configuration Assessment が生成)、12 件の **ConfigurationChange** レコード (Change Tracking がキャプチャ) が返っています。   
    ![検索結果](./media/log-analytics-log-searches/oms-search-results01.png)  

これらのフィルターは実際はオブジェクト型でもクラスでもありません。 *Type* は、データに付けられた、単なるタグ、プロパティ、または文字列/名前/カテゴリです。 システム内のドキュメントには、**Type:ConfigurationAlert** とタグ付けされたものや **Type:Perf**、**Type:Event** などとタグ付けされたものがあります。 それぞれの検索結果、ドキュメント、レコード、またはエントリには、各データの生のプロパティとその値がすべて表示されます。特定のフィールドに特定の値があるレコードのみを取得する場合は、フィルター内にフィールド名を指定できます。

** "Type" は、実際はすべてのレコードが持っているフィールドであり、他のフィールドと異なっている点はありません。 これは Type フィールドの値に基づいて設定されました。 そのレコードは、さまざまな形になるでしょう。 ちなみに、**Type=Perf** や **Type=Event** も、パフォーマンス データやイベントをクエリするために学習する必要がある構文です。

フィールド名の後と値の前に、コロン (:) または等号 (=) のいずれかを使用できます。 **Type:Event** と **Type=Event** は意味が同じであり、好みのスタイルを選択できます。

したがって、Type=Perf レコードに 'CounterName' というフィールドがある場合は、 `Type=Perf CounterName="% Processor Time"`のようなクエリを記述できます。

このクエリでは、パフォーマンス カウンターの名前が "% Processor Time" であるパフォーマンス データだけが返ります。

### <a name="to-search-for-processor-time-performance-data"></a>プロセッサ時間のパフォーマンス データを検索するには
* 検索クエリ フィールドに、「 `Type=Perf CounterName="% Processor Time"`

もっと詳しく指定することもできます。クエリで **InstanceName=_'Total'** を使用します。これは Windows パフォーマンス カウンターです。 ファセットや別の **field:value** を選択することもできます。 フィルターは、クエリ バー内のフィルターに自動的に追加されます。 これを次の図で確認できます。 図は、何も入力せずに **InstanceName:’_Total’** をクエリに追加するためにクリックする場所を示しています。

![ファセットの検索](./media/log-analytics-log-searches/oms-search-facet.png)

これで、クエリは `Type=Perf CounterName="% Processor Time" InstanceName="_Total"`

この例では、結果を取得するために **Type=Perf** を指定する必要はありません。 CounterName フィールドと InstanceName フィールドは Type=Perf のレコードにのみ存在するため、このクエリの結果は先ほどと同じになります。

```
CounterName="% Processor Time" InstanceName="_Total"
```

これは、クエリ内のすべてのフィルターは *AND* として評価されるためです。 条件にフィールドを追加すればするほど、結果は少なくなり、より詳しく、より洗練されていきます。

たとえば、`Type=Event EventLog="Windows PowerShell"` というクエリは `Type=Event AND EventLog="Windows PowerShell"` と同じです。 それは、Windows PowerShell イベント ログに記録され、収集されたすべてのイベントを返します。 同じファセットを繰り返し選択することで&1; つのフィルターを複数回追加しても、問題は見た目の問題だけです。検索バーが読み取りにくくなりますが、暗黙的な AND 演算子は常に存在するため、同じ結果が返ります。

暗黙的な AND 演算子は NOT 演算子を明示的に使用することで簡単に逆転させることができます。 次に例を示します。

`Type:Event NOT(EventLog:"Windows PowerShell")` またはそれと同等である `Type=Event EventLog!="Windows PowerShell"` は、Windows PowerShell ログではない (NOT) 他のすべてのログからすべてのイベントを返します。

または、'OR' などの他のブール演算子を使用できます。 次のクエリは、EventLog が Application または (OR) System であるレコードを返します。

```
EventLog=Application OR EventLog=System
```

上のクエリを使用すると、同じ結果セットの中に両方のログのエントリが取得されます。

ただし、暗黙的な AND を残したまま OR を削除した場合、両方のログに属すイベント ログは存在しないため、次のクエリでは何の結果も返りません。 イベント ログのエントリは、2 つのログのどちらかのみに書き込まれていました。

```
EventLog=Application EventLog=System
```


## <a name="use-additional-filters"></a>追加フィルターを使用する
次のクエリは、データを送信しているすべてのコンピューターの 2 つのイベント ログのエントリを返します。

```
EventLog=Application OR EventLog=System
```

![検索結果](./media/log-analytics-log-searches/oms-search-results03.png)

フィールドまたはフィルターのいずれかを選択するとクエリが特定のコンピューターに絞り込まれ、その他のすべてのコンピューターが除外されます。 クエリは次のようになります。

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

これは暗黙的な ANDのため、次と同じです。

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

各クエリは、次の明示的な順序で評価されます。 かっこに注意してください。

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

イベント ログのフィールドと同じように、OR を追加することで、特定のコンピューターのデータだけを取得できます。 次に例を示します。

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

同様に、次のクエリは、選択した&2; 台のコンピューターの **CPU 時間の割合** を返します。

```
CounterName="% Processor Time"  AND InstanceName="_Total" AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```


### <a name="boolean-operators"></a>ブール演算子
日時フィールドと数値フィールドでは、"*より大きい*"、"*未満*"、および "*以上*" を使用して値を検索できます。 クエリ検索バーで、>、<、>=、<=、!= などの単純な演算子を使用できます。

特定の期間の特定のイベント ログをクエリできます。 たとえば、過去 24 時間は、次のニーモニック式で表されます。

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### <a name="to-search-using-a-boolean-operator"></a>ブール演算子を使用して検索するには
* 検索クエリ フィールドに、「 `EventLog=System TimeGenerated>NOW-24HOURS"`  
    ![ブール演算子を使用した検索](./media/log-analytics-log-searches/oms-search-boolean.png)

期間は視覚的に制御でき、ほとんどの場合そうしたいと思いますが、時間フィルターを直接クエリに含めることには利点があります。 たとえば、これはダッシュボードで非常に役立ちます。ダッシュボードでは、ダッシュボード ページの "*グローバル*" なタイム セレクターに関係なく、各タイルの時間をオーバーライドできます。 詳細については、[ダッシュボードでの時間の問題](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/)に関するページを参照してください。

時間でフィルター処理するときは、2 つの期間 (OMS ポータルで指定した期間 (S1) とクエリに指定した期間 (S2)) の *共通部分* が結果として取得されることに注意してください。

![共通部分](./media/log-analytics-log-searches/oms-search-intersection.png)

つまり、期間に共通部分がない場合 (たとえば OMS ポータルで **[今週]** を選択し、クエリに **[先週]** を定義した場合)、共通部分がないため、何の結果も取得されません。

TimeGenerated フィールドで使用される比較演算子は、他の状況でも役立ちます。 たとえば、数値フィールドでも使用できます。

例として、Configuration Assessment のアラートに以下の重大度の値があるとします。

* 0 = 情報
* 1 = 警告
* 2 = 重大

次のクエリを使用して、警告アラートと重大アラートの両方をクエリすると同時に、情報アラートを除外できます。

```
Type=ConfigurationAlert  Severity>=1
```


範囲クエリを使用することもできます。 これは、連続する値の先頭と末尾の範囲を指定できることを意味します。 たとえば、Operations Manager イベント ログから、イベント ID が 2100 以上で 2199 未満のイベントを取得する場合は、次のクエリを使用すると、それらが戻ります。

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


> [!NOTE]
> 使用する必要がある範囲構文は、コロン (:) 区切り記号であり (field:value)、等号 (=) は使用 "*しません*"。 範囲の下限と上限を&2; つのピリオド(..) で区切り、全体を角かっこで囲みます。
>
>

## <a name="manipulate-search-results"></a>検索結果を操作する
データを検索しているときに、検索クエリを調整して結果を適切なレベルに制御できます。 結果が取得されたら、コマンドを適用して結果を変換できます。

Log Analytics 検索のコマンドは、縦棒パイプ文字 (|) の後ろに指定する *必要があります* 。 クエリ文字列の最初の部分は、常にフィルターにする必要があります。 それは、操作するデータ セットを定義した後、結果をコマンドに "パイプ" します。 このパイプを使用して、その他のコマンドを追加できます。 これは、大まかに言って Windows PowerShell パイプラインに似ています。

全体として、Log Analytics の検索言語は、IT プロフェッショナルが慣れている PowerShell のスタイルとガイドラインに従うことで、容易に学習できるようになっています。

コマンドには動詞の名前が付いているため、そのコマンドが何をするかを簡単に識別できます。  

### <a name="sort"></a>並べ替え
Sort コマンドは、1 つまたは複数のフィールドによって並べ替え順序を定義できるようにします。 このコマンドを使用しない場合でも、時間による降順が既定によって適用されます。 最新の結果が常に検索結果の最上位に表示されます。 つまり、`Type=Event EventID=1234` を使用して検索を実行すると、実際には次のクエリが実行されます。

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

これは、ログでよく見られる種類の動作であり、 たとえば Windows イベント ビューアーが該当します。

Sort を使用して、結果が返される方法を変更できます。 この操作の例を以下に示します。

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


上記の単純な例は、コマンドの動作方法を示しています。これらのコマンドはフィルターが返した結果の形を変更します。

### <a name="limit-and-top"></a>Limit と Top
あまり知られていないコマンドとして Limit があります。 Limit は PowerShellに似た動詞です。 Limit の機能は Top コマンドと同じです。 以下のクエリは同じ結果を返します。

```
Type=Event EventID=600 | Limit 1
```

```
Type=Event EventID=600 | Top 1
```


#### <a name="to-search-using-top"></a>Top を使用して検索するには
* 検索クエリ フィールドに、「`Type=Event EventID=600 | Top 1` 」と入力します  
    ![Top を使用した検索](./media/log-analytics-log-searches/oms-search-top.png)

上記の図では、EventID が 600 であるレコードが 358,000 個あります。 左側のフィールド、ファセット、およびフィルターには、クエリのパイプ文字の前の部分である "*フィルター部分によって*" 返された結果についての情報が常に表示されます。 **[結果]** パネルには最新の結果が 1 つだけ返されます。これは、コマンドによって結果が成形され、変換されたためです。

### <a name="select"></a>Select
Select コマンドは、PowerShell の Select-Object のように動作します。 それはフィルター処理された結果を返し、結果に元のプロパティがすべて含まれることはありません。 代わりに、指定したプロパティのみが選択されます。

#### <a name="to-run-a-search-using-the-select-command"></a>Select コマンドを使用して検索を実行するには
1. [検索] で「 `Type=Event` 」と入力し、 **[検索]**をクリックします。
2. 結果が持っているすべてのプロパティを表示するには、結果のいずれかで **[+ 詳細]** をクリックします。
3. その中の一部を明示的に選択します。クエリが `Type=Event | Select Computer,EventID,RenderedDescription` に変わります。  
    ![Select を使用した検索](./media/log-analytics-log-searches/oms-search-select.png)

これは、検索の出力を管理し、探索に本当に関係するデータ部分を選択する場合に特に役立つコマンドです。多くの場合、レコード全体が探索に関係することはありません。 このコマンドは、異なる種類のレコードの中に共通するプロパティが "*いくつか*" あるが、"*すべての*" プロパティが共通しているわけではない場合にも便利です。 その後、テーブルなどのもっと自然な形での出力や、CSV ファイルにエクスポートして Excel で表示すると理解しやすい出力を生成できます。

## <a name="use-the-measure-command"></a>Measure コマンドを使用する
Measure は、Log Analytics 検索で最も用途の広いコマンドの&1; つです。 データに統計 *関数* を適用し、結果を指定したフィールドでグループ化して集計できます。 Measure がサポートする統計関数は複数あります。

### <a name="measure-count"></a>Measure count()
操作する最初の統計関数であり、最も簡単な関数の&1; つは *count()* 関数です。

`Type=Event`などの検索クエリの結果では、検索結果の左側にフィルター (ファセットとも呼ばれます) が表示されます。 これらのフィルターは、実行された検索の結果を、特定のフィールドによる値の分布で示します。

![Measure count() を使用した検索](./media/log-analytics-log-searches/oms-search-measure-count01.png)

たとえば、上の図では、**Computer** フィールドがあり、およそ 73 万 9 千件のイベントが結果に含まれ、これらのレコード内の **Computer** フィールドに一意の値が 68 個含まれていることを示しています。 タイルには、上位 5 つの値のみが表示されています。これらは、**Computer** フィールドに書き込まれた最も共通している 5 つの値であり、そのフィールドに特定の値を含むドキュメントの数によって並べ替えられています。 この図から、およそ 369,000 件のイベントのうち、90,000 件が OpsInsights04.contoso.com コンピューター、83,000 件が DB03.contoso.com コンピューターのものであることなどがわかります。

タイルには上位 5 件のみが表示されていますが、すべての値を確認するにはどうすればよいでしょうか?

それが Measure コマンドの count() 関数を使用して実行できることです。 この関数はパラメーターを使用しません。 グループ化するフィールドを指定するだけです。ここでは **Computer** フィールドを指定します。

`Type=Event | Measure count() by Computer`

![Measure count() を使用した検索](./media/log-analytics-log-searches/oms-search-measure-count-computer.png)

ただし、**Computer** は単に各データの "*中で*" 使用されるフィールドであり、関連するリレーショナル データベースはなく、独立した **Computer** オブジェクトも存在しません。 データの "*中の*" 値だけが、値を生成したエンティティと、データの他の特性と側面を記述できます。"*ファセット*" という用語が使用されるのはこのためです。 ただし、その他のフィールドでも同じようにグループ化できます。 Measure コマンドにパイプされるおよそ 739,000 件のイベントという元の結果には、**EventID** という名前のフィールドも含まれているため、同じテクニックを適用してそのフィールドでグループ化することで、EventID 別のイベント数を取得できます。

```
Type=Event | Measure count() by EventID
```

特定の値を含む実際のレコードの数に関心はなく、値そのものの一覧が欲しい場合は、 *Select* コマンドをクエリの末尾に追加し、最初の列を選択できます。

```
Type=Event | Measure count() by EventID | Select EventID
```

次に、複雑なクエリの結果を事前に並べ替えて取得できます。並べ替えはグリッド内の列をクリックすることでも実現できます。

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### <a name="to-search-using-measure-count"></a>Measure count() を使用して検索するには
* 検索クエリ フィールドに、「 `Type=Event | Measure count() by EventID`
* `| Select EventID` をクエリの末尾に追加します。
* 最後に、 `| Sort EventID asc` をクエリの末尾に追加します。

注意する必要がある重要なポイントがいくつかあります。

1 つ目のポイントは、表示される結果は、元の生の結果ではなくなっていることです。 それらは集計された結果であり、本質的に結果のグループです。 これは問題ではありませんが、集計/統計関数の適用によって生成された元の生のデータの形とは非常に異なっていることを理解しておく必要があります。

2 つ目のポイントは、 **Measure count** は、現時点では上位 100 件の独自の結果のみを返すことです。 この制限は、その他の統計関数には適用されません。 このため、Measure count() は、通常は詳細なフィルターを使用して特定の項目を検索した後で適用する必要があります。

## <a name="use-the-max-and-min-functions-with-the-measure-command"></a>Measure コマンドで Max 関数と Min 関数を使用する
**Measure Max()** と **Measure Min()** は、さまざまな状況で役に立ちます。 ただし、これらの関数は逆の動作をするため、ここでは Max() について説明します。Min() については自分で試すことができます。

セキュリティ イベントのクエリを行う場合、イベントには値が異なる可能性がある **Level** プロパティがあります。 次に例を示します。

```
Type=SecurityEvent
```

![Measure count() を使用した検索の開始](./media/log-analytics-log-searches/oms-search-measure-max01.png)

コンピューターのすべてのセキュリティ イベントの最大値を確認する場合は、次のクエリを使用してフィールド別にグループ化できます。

```
Type=ConfigurationAlert | Measure Max(Level) by Computer
```

![Measure Max を使用したコンピューターの検索](./media/log-analytics-log-searches/oms-search-measure-max02.png)

**Level** レコードを持つコンピューターが表示されます。そのほとんどが少なくともレベル 8 であり、多くはレベル 16 を持っています。

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![Measure Max(TimeGenerated) を使用したコンピューターの検索](./media/log-analytics-log-searches/oms-search-measure-max03.png)

この関数は数値に適していますが、DateTime フィールドでも動作します。 各コンピューターでインデックス作成されたデータの最後または最近のタイムスタンプをチェックするために役立ちます。 たとえば、各コンピューターでレポートされた最新のセキュリティ イベントはいつのものかを調べます。

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## <a name="use-the-avg-function-with-the-measure-command"></a>Measure コマンドで Avg 関数を使用する
Measure で使用される Avg() 統計関数では、あるフィールドの平均値を計算でき、結果を同じフィールドまたは別のフィールド別にグループ化できます。 これは、パフォーマンス データなどのさまざまなケースで役立ちます。

パフォーマンス データから始めます。 現在 OMS では、Windows マシンと Linux マシンの両方のパフォーマンス カウンターが収集されます。

*すべての* パフォーマンス データを検索するための最も基本的なクエリは次のとおりです。

```
Type=Perf
```

![Avg を使用した検索の開始](./media/log-analytics-log-searches/oms-search-avg01.png)

最初に気が付くのは、Log Analytics によって&3; つの観点が示されるということです。1 つ目はリストで、グラフの基になる実際のレコードが示されます。2 つ目はテーブルで、パフォーマンス カウンターのデータが表形式のビューで示されます。3 つ目はメトリックで、パフォーマンス カウンターのグラフが示されます。

上記の図には、以下を示す&2; つのフィールド セットがマークされています。

* 最初のセットは、クエリ フィルター内の Windows パフォーマンス カウンターの名前、オブジェクト名、およびインスタンス名を識別します。 これらは、ファセット/フィルターとして最もよく使用される可能性があるフィールドです。
* **CounterValue** は、カウンターの実際の値です。 この例では、値は *75* です。
* **TimeGenerated** は 12 時 51 分であり、これは 24 時間制の時刻です。

以下に、メトリックのグラフを示します。

![Avg を使用した検索の開始](./media/log-analytics-log-searches/oms-search-avg02.png)

Perf レコードの形と他の検索テクニックに関する情報を読み取った後、Measure Avg() を使用して、この種の数値データを集計できます。

単純な例を次に示します。

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(CounterValue) by Computer
```

![Avg(SampleValue) を使用した検索](./media/log-analytics-log-searches/oms-search-avg03.png)

この例では、CPU の合計時間に関するパフォーマンス カウンターを選択し、コンピューター別に平均を計算します。 検索結果を直近 6 時間に絞り込むには、時間フィルター コントロールを使用するか、またはクエリの中で次のように指定します。

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer
```

### <a name="to-search-using-the-avg-function-with-the-measure-command"></a>Measure コマンドで Avg 関数を使用して検索するには
* 検索クエリ ボックスに、「 `Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer`」と入力します。

*複数の* コンピューターのデータを集計して相関させることができます。 たとえば、ある種のファームの中にホストのセットがあり、各ノードは他のノードと同等であり、同じ種類の作業を実行し、負荷はほぼ均等であるとします。 次のクエリを実行することで、すべてのカウンターを一気に取得し、ファーム全体の平均値を取得できます。 次の例のように、コンピューターを選択することから始めます。

```
Type=Perf AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

コンピューターを選択したので、次に&2; つの主要業績評価指標 (KPI) である CPU 使用率と空きディスク領域の割合のみを選択します。 したがって、クエリのその部分は次のようになります。

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

これで、次の例のように、コンピューターとカウンターを追加できます。

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

非常に具体的な選択を行っているため、 **Measure Avg()** コマンドは、コンピューター別ではなく、ファーム全体の平均を、CounterName でグループ化して返すことができます。 次に例を示します。

```
Type=Perf  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03") | Measure Avg(CounterValue) by CounterName
```

これにより、環境のいくつかの KPI に関するコンパクトで有用なビューを取得できます。

![Avg を使用した検索のグループ化](./media/log-analytics-log-searches/oms-search-avg04.png)

検索クエリは、ダッシュボードで簡単に使用できます。 たとえば、検索クエリを保存して、それを基にして *Web Farm KPIs* という名前のダッシュボードを作成できます。 ダッシュボードの使用の詳細については、「 [Create a custom dashboard in Log Analytics (Log Analytics でのカスタム ダッシュボードの作成)](log-analytics-dashboards.md)」を参照してください。

![Avg を使用した検索結果を示すダッシュボード](./media/log-analytics-log-searches/oms-search-avg05.png)

### <a name="use-the-sum-function-with-the-measure-command"></a>Measure コマンドで Sum 関数を使用する
Sum 関数は、Measure コマンドの他の関数に似ています。 Sum 関数の使用方法の例については、「 [Microsoft Azure Operational Insights での W3C IIS ログ検索](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx)」で確認できます。

Max() と Min() は、数値、日時、テキスト文字列で使用できます。 テキスト文字列はアルファベット順に並べ替えられ、最初と最後の文字列が取得されます。

ただし、Sum() は、数値フィールド以外では使用できません。 これは Avg() にも当てはまります。

### <a name="use-the-percentile-function-with-the-measure-command"></a>Measure コマンドで百分位関数を使用する
百分位関数は、数値フィールドに使用が限られるという点で Avg() や Sum() と似ています。 数値フィールドに対し、1 ～ 99 の範囲の任意の百分位数を使用できます。 **percentile** コマンドと **pct** コマンドを使用することもできます。 次に例をいくつか示します。  

```
Type:Perf CounterName:"DiskTransers/sec" |measure percentile95(CurrentValue) by Computer
```
```
Type:Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" | measure pct65(CurrentValue) by InstanceName
```

## <a name="use-the-where-command"></a>Where コマンドを使用する
Where コマンドはフィルターのように機能しますが、パイプライン内で適用して、クエリの先頭でフィルター処理される生データではなく、Measure コマンドによって生成された集計結果をさらにフィルター処理できます。

次に例を示します。

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer
```

次の例に示すように、別のパイプ文字 "|" と Where コマンドを追加することで、平均 CPU 使用率が 80% を超えるコンピューターのみを取得できます。

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer | Where AVGCPU>80
```

Microsoft System Center の Operations Manager に慣れている場合、Where コマンドは管理パック用語と考えることができます。 上記の例をルールであるとみなすと、クエリの最初の部分はデータ ソースになり、Where コマンドは条件の検出にあたります。

クエリは、 **[マイ ダッシュボード]**のタイルとして、コンピューターの CPU が過剰に使用されたときに確認するためのある種のモニターとして使用できます。 ダッシュボードの詳細については、「 [Create a custom dashboard in Log Analytics (Log Analytics でのカスタム ダッシュボードの作成)](log-analytics-dashboards.md)」を参照してください。 ダッシュボードは、モバイル アプリで作成して使用することもできます。 詳細については、[OMS モバイル アプリ](http://www.windowsphone.com/en-us/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)のページを参照してください。 次の図の下部にある&2; つのタイルに、モニターが一覧と数値で表示されていることがわかります。 基本的には、数値はゼロであり、一覧は空であることが望まれます。 そうでない場合は、アラート状態であることを示しています。 必要に応じて、どのコンピューターに負荷がかかっているかを調べることができます。

![モバイル ダッシュボード](./media/log-analytics-log-searches/oms-search-mobile.png)

## <a name="use-the-in-operator"></a>IN 演算子の使用
*IN* 演算子と *NOT IN* 演算子ではサブ検索を使用できます。つまり、引数として別の検索を追加することができます。 サブ検索は、別の "*プライマリ*" ("*外側の*") 検索の内側に、中かっこ {} で囲んで指定します。 サブ検索からは通常、重複しない値のリストが返され、その結果が、プライマリ検索の引数として使用されます。

サブ検索を使用すると、データのサブセット (検索式の中で直接記述することはできないものの、検索結果として生成することはできるデータの集合) に対して検索を実行することができます。 たとえば、"*セキュリティ更新プログラムが適用されていないコンピューター*" から生成されたすべてのイベントを&1; 回の検索で探すには、まず "*セキュリティ更新プログラムが適用されていないコンピューター*" を特定したうえで、それらのホストに属しているイベントを探すようにサブ検索を設計する必要があります。

たとえば、 *現時点で必要なセキュリティ更新プログラムが適用されていないコンピューター* は、次のクエリで表現することができます。

```
Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer
```    

![IN search example](./media/log-analytics-log-searches/oms-search-in01-revised.png)

必要な一連の検索結果が得られたら、その検索を内側で使用し、外側の (プライマリ) 検索に一連のコンピューターを渡すことで、それらのコンピューターを対象にイベントを検索することができます。 そのためには内側の検索を中かっこで囲み、外側の検索のフィルターまたはフィールドの候補値としてその結果を IN 演算子で渡します。 このクエリは、次のように記述します。

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer}
```
![IN search example](./media/log-analytics-log-searches/oms-search-in02-revised.png)

内側の検索に使用されている時間フィルターにも注目してください。これは、24 時間おきにすべてのコンピューターのスナップショットが System Update Assessment によって作成されるためです。 内側のクエリは、特定の曜日を検索対象にすれば、もっと簡潔化し、きりの良い結果を得ることができます。 ここでは、外側の検索でユーザー インターフェイスによる時間選択を利用し、直近 7 日間のイベントを取得しています。 時間演算子の詳細については、「[ブール演算子](#boolean-operators)」を参照してください。

内側の検索の結果は、実際には外側の検索のフィルター値として使用しているだけなので、外側の検索でさらにコマンドを適用することができます。 たとえば、上記のイベントを別の Measure コマンドでグループ化することも可能です。

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer} | measure count() by Source
```

![IN search example](./media/log-analytics-log-searches/oms-search-in03-revised.png)

Log Analytics はサービス側のタイムアウトが決まっているため、一般に内側のクエリはすばやく実行されること、また、返される結果の件数が少ないことが理想です。 内側のクエリから大量の結果が返されると、検索結果のリストが切り詰められ、外側の検索から間違った結果が返される原因となります。

現時点では、もう&1; つルールがあって、内側の検索から返される結果は、"*集計済み*" であることが必要です。 つまり、*Measure* コマンドを含んでいる必要があります。現時点では、未加工の結果を外側の検索にそのまま渡すことはできません。

また、使用できる IN 演算子は&1; つだけで、クエリの最後のフィルターとして使用されている必要があります。 複数の IN 演算子を OR で連結することはできません。したがって、複数のサブ検索を実行することはできません。サブ (内側の) 検索は、外側の検索ごとに&1; 回しか使用できないので注意してください。

こうした制限事項はあるものの、IN 演算子を利用すると、さまざまな検索を組み合わせて、コンピューター、ユーザー、ファイルなど、データに含まれているあらゆるフィールドから擬似的なグループを定義することができます。 以下、他にもいくつかの例を紹介します。

**自動更新設定が無効になっているコンピューターに不足しているすべての更新プログラム**

```
Type=Update UpdateState=Needed Optional=false Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual | measure count() by Computer} | measure count() by KBID
```

**SQL Server が実行されている (= SQL Assessment が実行されている) コンピューターから生成されたすべてのエラー イベント**

```
Type=Event EventLevelName=error Computer IN {Type=SQLAssessmentRecommendation | measure count() by Computer}
```

**ドメイン コントローラーとして動作している (= AD Assessment が実行されている) コンピューターから生成されたすべてのセキュリティ イベント**

```
Type=SecurityEvent Computer IN { Type=ADAssessmentRecommendation | measure count() by Computer }
```

**アカウント BACONLAND\jochan がログオンしたコンピューターにログオンした他のアカウント**

```
Type=SecurityEvent EventID=4624   Account!="BACONLAND\\jochan" Computer IN { Type=SecurityEvent EventID=4624   Account="BACONLAND\\jochan" | measure count() by Computer } | measure count() by Account
```

## <a name="use-the-distinct-command"></a>Distinct コマンドの使用
この名前が示すように、このコマンドは、特定のフィールドの、重複しない一連の値を返します。 意外なほどにシンプルですが、きわめて実用的です。 同じことは、以下に示した Measure count() コマンドでもできます。

```
Type=Event | Measure count() by Computer
```

![DISTINCT search command example](./media/log-analytics-log-searches/oms-search-distinct01-revised.png)

ただし必要な情報が単に一意の値のリストであって、その値を含んだドキュメントの数ではないという場合は、DISTINCT の方がすっきりしていて出力結果が読みやすく、構文も短くて済みます。以下の例をご覧ください。

```
Type=Event | Distinct Computer
```
![DISTINCT search command example](./media/log-analytics-log-searches/oms-search-distinct02-revised.png)

## <a name="use-the-countdistinct-function-with-the-measure-command"></a>Measure コマンドでの countdistinct 関数の使用
countdistinct は、各グループに含まれる一意の値の数をカウントする関数です。 たとえば、Type ごとにその報告元となった一意のコンピューターの台数をカウントする場合に利用できます。

```
* | measure countdistinct(Computer) by Type
```

![OMS-countdistinct](./media/log-analytics-log-searches/oms-countdistinct.png)

## <a name="use-the-measure-interval-command"></a>Measure interval コマンドの使用
Log Analytics では、パフォーマンス データをほぼリアルタイムで収集し、あらゆるパフォーマンス カウンターからデータを集めて視覚化することができます。 「 **Type:Perf** 」というクエリを入力するだけで、Log Analytics 環境内のカウンター数やサーバー数に基づいて多数のメトリック グラフが返されます。 必要に応じてメトリックの集計を組み合わせれば、環境内の全体的なメトリックをざっと見渡したり、必要に応じて、さらに粒度の細かいデータにまで踏み込んで調べたりすることもできます。

たとえば、すべてのコンピューターを対象に平均 CPU 時間を調べることになったとしましょう。 コンピューターごとの平均 CPU 時間に着目しても、結果の凹凸がならされてしまい、有意義な結果が得られない可能性があります。 さらに踏み込んで調べるためには、もっと小さな時間枠を&1; つのまとまりとして結果を集計し、さまざまな切り口の時系列に注目します。 たとえば、すべてのコンピューターを対象に、1 時間ごとの平均 CPU 使用率を調べるには次のクエリを実行します。

```
Type:Perf CounterName="% Processor Time" InstanceName="_Total" | measure avg(CounterValue) by Computer Interval 1HOUR
```

![measure average interval](./media/log-analytics-log-searches/oms-measure-avg-interval.png)

既定では、インタラクティブな多系列の折れ線グラフでこれらの結果が表示されます。  このグラフは、系列の切り替え (y 軸のスケール変更を含む)、ズーム、ホバーリングに対応しています。  必要に応じて、テーブルの表示オプションを使用して生データを表示することもできます。

他のフィールドでグループ化することも可能です。 この例では、特定の 1 台のコンピューターを対象に、% で始まるすべてのカウンターに着目し、各カウンターの 1 時間ごとの 70 パーセンタイルを調べています。

```
Type:Perf Computer=beefpatty4 CounterName=%* InstanceName=_Total | measure percentile70(CounterValue) by CounterName Interval 1HOUR
```
これらのクエリはパフォーマンス カウンターに限定されないことに注意してください。 あらゆるメトリックに適用することができます。 この例では、W3C IIS ログに着目しています。 ここで調べたいことは、個々の要求の処理にかかる最大所要時間です。5 分間隔で集計しています。

```
Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES
```

### <a name="use-multiple-aggregates-in-one-query"></a>1 つのクエリで複数の集計を使用する
Measure コマンドには複数の集計句を指定できます。  各集計句には個別に別名を付けることができます。  別名を指定しなかった場合は、使用した集計関数が最終的なフィールド名になります。つまり、avg(CounterValue) を使用した場合は "avg(CounterValue)" という名前になります。

 ```
Type=WireData | measure avg(ReceivedBytes), avg(SentBytes) by Direction interval 1hour
```
![OMS-multiaggregates1](./media/log-analytics-log-searches/oms-multiaggregates1.png)

別名を使用した場合の例:

 ```
* | measure countdistinct(Computer) as Computers, count() as TotalRecords by Type
```


## <a name="next-steps"></a>次のステップ
ログの検索の詳細については、次のトピックを参照してください。

* ログの検索を拡張するには、 [Log Analytics でカスタム フィールド](log-analytics-custom-fields.md) を使用します。
* Log Analytics で使用できるすべての検索フィールドとファセットは、 [Log Analytics のログ検索のリファレンス](log-analytics-search-reference.md) でご覧いただけます。

