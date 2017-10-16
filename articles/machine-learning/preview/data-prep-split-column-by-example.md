---
title: "Azure Machine Learning ワークベンチを使用した [Split Column by Example] (例による列の分割) 変換"
description: "[Split Column by Example] (例による列の分割) 変換の参照ドキュメント"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 013c99045621e4651a44ab99c9f695fff6004654
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="split-column-by-example-transformation"></a>[Split Column by Example] (例による列の分割) 変換
この変換は、ユーザーが入力することなく、予測して、意味のある境界上で列のコンテンツを分割します。 分割アルゴリズムが、列のコンテンツを分析した後で境界を選択します。 これらの境界は以下の項目によって定義できます
* 固定の区切り記号、
* 特定のコンテキストで現れる複数の不定の区切り記号、
* データ パターンまたは特定のエンティティ型

ユーザーは、詳細モードで、区切り文字を指定したり、目的の分割の例を示したりすることにより、分割動作を制御することもできます。

理論上は、分割操作は、ワークベンチでも一連の *[Derive Column by Example] (例による列の派生)* 変換を使用して実行できます。 ただし、複数の列がある場合、例によるアプローチを使用したとしても、各列を個別に派生していると非常に時間がかかります。 予測分割では、ユーザーが列ごとに例を与える必要がなく、簡単に分割できます。 

## <a name="how-to-perform-this-transformation"></a>この変換を実行する方法

1. 分割する列を選択します。 
2. **Transforms (変換)** メニューから **Split Column by Example (例による列の分割)** を選択します。 または、選択した列のヘッダーを右クリックして、コンテキスト メニューから **[Split Column by Example] (例による列の分割)** を選択します。 変換エディターが開き、新しい列が選択した列の横に追加されます。 この時点では、ワークベンチは、入力列を分析し、分割の境界を決定し、グリッドに表示されたときに列を分割するプログラムを合成します。 合成されたプログラムは、列内のすべての行に対して実行されます。 区切り記号は、存在する場合、最終結果から除外されます。
3. **[Advanced mode] (詳細モード)** をクリックすると、分割変換に対してよりきめ細かく制御できます。 
4. 出力をレビューし、**[OK]** をクリックして変換を承諾します。 

変換では、すべての行に対して同じ数の結果列を生成することを目的としています。 いずれかの行が、決定された境界上で分割できない場合、既定では、すべての列に対して *null* 値を生成します。 この動作は**詳細モード**で変更できます。

### <a name="transform-editor-advanced-mode"></a>変換エディター: 詳細モード
**詳細モード**では、列の分割に関するより豊富な操作が用意されています。 

**[Keep Delimiter Columns] (区切り記号の列を保持)** を選択した場合、最終結果に区切り記号が含まれます。 既定では、区切り記号は除外されます。

**[区切り記号]** を指定した場合、自動区切り記号選択ロジックを上書きします。 行ごとに 1 つずつ、複数の区切り記号を **[区切り記号]** として指定できます。 列を分割するための区切り文字としてこれらのすべての文字が使用されます。

決定された境界上で値を分割すると、他の大多数とは異なる数の列が生成されることがあります。 このような場合、列を入力する順序を決定するために **[Fill Direction] (入力方向)** が使用されます。

**[Show suggested examples] (提示された例を表示)** をクリックすると、分割の例を提供する必要のあるユーザーの代表的な行が表示されます。 ユーザーは、提示された行の右側の**上向き**矢印をクリックして、例としての行のレベルを上げることができます。 

ユーザーは、**[Examples Table] (例テーブル)** のヘッダーを右クリックして、**列を削除**したり**新しい列を挿入**したりできます。

ユーザーは、分割の例を提供するために、あるセルから別のセルに値をコピーして貼り付けることができます。

ユーザーは、変換エディターでリンクをクリックして、**基本モード**と**詳細モード**を切り替えられます。

### <a name="editing-an-existing-transformation"></a>既存の変換の編集

ユーザーは、変換ステップの **[編集]** オプションを選択することによって、既存の **[Split Column By Example] (例による列の分割)** 変換を編集できます。 **[編集]** をクリックすると、変換エディターが**詳細モード**で開き、変換の作成時に提供されたすべての例が表示されます。

## <a name="examples-of-splitting-on-a-fixed-single-character-delimiter"></a>固定の単一文字の区切り記号での分割例

一般にデータ フィールドは、CSV 形式でのコンマなどの単一の固定区切り記号で区切られます。 分割変換は、これらの区切り記号を自動的に推定しようと試みます。 たとえば、次のシナリオでは、"." を区切り記号と自動的に推定しています。

### <a name="splitting-ip-addresses"></a>IP アドレスの分割

最初の列の値は、予測して、4 つの列に分割されます。

|IP|IP_1|IP_2|IP_3|IP_4|
|:-----|:-----|:-----|:-----|:-----|
|192.168.0.102|192|168|0|102|
|192.138.0.101|192|138|0|101|
|192.168.0.102|192|168|0|102|
|192.158.1.202|192|158|1|202|
|192.168.0.102|192|168|0|102|
|192.169.1.102|192|169|1|102|

## <a name="examples-of-splitting-on-multiple-delimiters-within-particular-contexts"></a>特定のコンテキストにおける複数の区切り記号での分割例

ユーザーのデータには、異なるフィールドを分離する別々の区切り記号が多数含まれることがあります。 さらに、出現した区切り文字列のすべてではなく一部だけが区切り文字であることもあります。 たとえば、次の場合では、目的の出力を生成するために必要な区切り文字のセットは、"-"、","、および ":" です。 ただし、時間は分割せず 1 つの列に収めるので、出現したすべての ":" を区切りポイントにするわけにはいきません。 分割変換は、出現した区切り文字すべてを候補とするのではなく、入力データで出現するコンテキスト内での区切り文字を推定します。 変換は、日付や時刻などの一般的なデータ型も認識します。   

### <a name="splitting-store-opening-timings"></a>開店時間の分割

次の*時間*列の値は、予測を使用して、その下の表に表示される 9 つの列に分割されます。

|時間|
|:-----|
|月曜日 - 金曜日: 7:00 am - 6:00 pm、土曜日: 9:00 am - 5:00 pm、日曜日: クローズ|
|月曜日 - 金曜日: 9:00 am - 6:00 pm、土曜日: 4:00 am - 4:00 pm、日曜日: クローズ|
|月曜日 - 金曜日: 8:30 am - 7:00 pm、土曜日: 3:00 am - 2:30 pm、日曜日: クローズ|
|月曜日 - 金曜日: 8:00 am - 6:00 pm、土曜日: 2:00 am - 3:00 pm、日曜日: クローズ|
|月曜日 - 金曜日: 4:00 am - 7:00 pm、土曜日: 9:00 am - 4:00 pm、日曜日: クローズ|
|月曜日 - 金曜日: 8:30 am - 4:30 pm、土曜日: 9:00 am - 5:00 pm、日曜日: クローズ|
|月曜日 - 金曜日: 5:30 am - 6:30 pm、土曜日: 5:00 am - 4:00 pm、日曜日: クローズ|
|月曜日 - 金曜日: 8:30 am - 8:30 pm、土曜日: 6:00 am - 5:00 pm、日曜日: クローズ|
|月曜日 - 金曜日: 8:00 am - 9:00 pm、土曜日: 9:00 am - 8:00 pm、日曜日: クローズ|
|月曜日 - 金曜日: 10:00 am - 9:30 pm、土曜日: 9:30 am - 3:00 pm、日曜日: クローズ|

|Timings_1|Timings_2|Timings_3|Timings_4|Timings_5|Timings_6|Timings_7|Timings_8|Timings_9|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|月曜日|金曜日|7:00 am|6:00 pm|土曜日|9:00 am|5:00 pm|土曜日|クローズ|
|月曜日|金曜日|9:00 am|6:00 pm|土曜日|4:00 am|4:00 pm|土曜日|クローズ|
|月曜日|金曜日|8:30 am|7:00 pm|土曜日|3:00 am|2:30 pm|土曜日|クローズ|
|月曜日|金曜日|8:00 am|6:00 pm|土曜日|2:00 am|3:00 pm|土曜日|クローズ|
|月曜日|金曜日|4:00 am|7:00 pm|土曜日|9:00 am|4:00 pm|土曜日|クローズ|
|月曜日|金曜日|8:30 am|4:30 pm|土曜日|9:00 am|5:00 pm|土曜日|クローズ|
|月曜日|金曜日|5:30 am|6:30 pm|土曜日|5:00 am|4:00 pm|土曜日|クローズ|
|月曜日|金曜日|8:30 am|8:30 pm|土曜日|6:00 am|5:00 pm|土曜日|クローズ|
|月曜日|金曜日|8:00 am|9:00 pm|土曜日|9:00 am|8:00 pm|土曜日|クローズ|
|月曜日|金曜日|10:00 am|9:30 pm|土曜日|9:30 am|3:00 pm|土曜日|クローズ|

### <a name="splitting-iis-log"></a>IIS ログの分割

次に、複数の任意の区切り記号を使用したもう 1 つの例を示します。 この例には、コンテキストによる区切り記号 "/" も含まれます。これは、URL やファイル パスの中では区切ってはならないものです。 多数の *[Derive Column by Example] (例による列の派生)* 変換を使用し、フィールドごとに例を与えてこの分割を実行すると面倒になります。 分割変換を使用すると、例を与えることなく予測して分割を実行できます。

|logtext|
|:-----|
|192.128.138.20 - - [16/Oct/2016 16:22:33 -0200] "GET /images/picture.gif HTTP/1.1" 234 343 www.yahoo.com "http://www.example.com/" "Mozilla/4.0 (compatible; MSIE 4)" "-"|
|10.128.72.213 - - [17/Oct/2016 12:43:12 +0300] "GET /news/stuff.html HTTP/1.1" 200 6233 www.aol.com "http://www.sample.com/" "Mozilla/5.0 (MSIE)" "-"|
|192.165.71.165 - - [12/Nov/2016 14:22:44 -0500] "GET /sample.ico HTTP/1.1" 342 7342 www.facebook.com "-" "Mozilla/5.0 (Windows; U; Windows NT 5.1; rv:1.7.3)" "-"|
|10.166.64.165 - - [23/Nov/2016 01:52:45 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.167.1.193 - - [16/Jan/2017 22:34:56 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.147.76.193 - - [28/Jan/2017 26:36:16 +0800] "GET /search.php HTTP/1.1" 400 1777 www.bing.com "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)" "-"|
|192.166.64.165 - - [23/Mar/2017 01:55:25 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|11.167.1.193 - - [16/Apr/2017 11:34:36 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|

分割情報の取得:

|logtext_1|logtext_2|logtext_3|logtext_4|logtext_5|logtext_6|logtext_7|logtext_8|logtext_9|logtext_10|logtext_11|logtext_12|logtext_13|logtext_14|logtext_15|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|192.128.138.20|16/Oct/2016|16:22:33|-0200|GET|images/picture.gif|HTTP|1.1|234|343|www.yahoo.com|http://www.example.com/|Mozilla|4.0|compatible; MSIE 4|
|10.128.72.213|17/Oct/2016|12:43:12|+0300|GET|news/stuff.html|HTTP|1.1|200|6233|www.aol.com|http://www.sample.com/|Mozilla|5.0|MSIE|
|192.165.71.165|12/Nov/2016|14:22:44|-0500|GET|sample.ico|HTTP|1.1|342|7342|www.facebook.com|-|Mozilla|5.0|Windows; U; Windows NT 5.1; rv:1.7.3|
|10.166.64.165|23/Nov/2016|01:52:45|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|192.167.1.193|16/Jan/2017|22:34:56|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|
|192.147.76.193|28/Jan/2017|26:36:16|+0800|GET|search.php|HTTP|1.1|400|1777|www.bing.com|-|Mozilla|4.0|compatible; MSIE 6.0; Windows NT 5.1|
|192.166.64.165|23/Mar/2017|01:55:25|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|11.167.1.193|16/Apr/2017|11:34:36|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|

## <a name="examples-of-splitting-without-delimiters"></a>区切り記号を使用しない分割例
実際の区切り記号がなく、データ フィールドが互いに隣接して出現することがあります。 この場合、分割変換は、データのパターンを自動的に検出して、分割ポイントを推定します。 たとえば、次のシナリオでは金額を通貨型から分離します。分割変換は自動的に、数値データと数値以外のデータの間の境界を分割ポイントと推定します。

### <a name="splitting-amount-with-currency-symbol"></a>通貨記号付きの金額の分割

|金額|Amount_1|Amount_2|
|:-----|:-----|:-----|
|\$14|$|14|
|£9|£|9|
|\$34|$|34|
|€ 18|€ |18|
|\$42|$|42|
|\$7|$|7|
|£42|£|42|
|\$16|$|16|
|€ 16|€ |16|
|\$15|$|15|
|\$16|$|16|
|€ 64|€ |64|

次の例では、測定単位から重量値を分離します。 同様に分割の推定は自動的に、意味のある境界を検出し、"." の文字などの可能性のある区切り記号よりもこちらを優先します。 

### <a name="splitting-weights-with-units"></a>単位付きの重量の分割

|重量|Weight_1|Weight_2|
|:-----|:-----|:-----|
|2.27KG|2.27|KG|
|1L|1|L|
|2.5KG|2.5|KG|
|2KG|2|KG|
|1.7KGA|1.7|KGA|
|3KG|3|KG|
|2KG|2|KG|
|125G|125|G|
|500 G|500|G|
|1.5KGA|1.5|KGA|

## <a name="technical-notes"></a>テクニカル ノート

分割変換機能は、**予測プログラム合成**技法に基づいています。 この技法では、データ変換プログラムは、入力データに基づいて自動的に学習します。 プログラムは、ドメイン固有の言語で合成されます。 DSL は、特定の正規表現のコンテキストで現れる区切り記号とフィールドに基づきます。 このテクノロジの詳細については、[このトピックに関する最近の発行物](https://www.microsoft.com/en-us/research/publication/automated-data-extraction-using-predictive-program-synthesis/)に記されています。 
