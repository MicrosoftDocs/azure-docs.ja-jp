---
title: "Azure Machine Learning ワークベンチを使用した Derive Column by Example (例による列の派生) 変換"
description: "\"Derive Column by Example\" (例による列の派生) 変換の参照ドキュメント"
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
ms.openlocfilehash: a02f5e827345a1d28f01d691e1b6fbccfc03ae8a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="derive-column-by-example-transformation"></a>Derive column by example (例による列の派生) 変換

**Derive Column by Example (例による列の派生)** 変換により、ユーザーは、ユーザー指定の派生結果例を使用して、1 つ以上の既存列の派生物を作成できます。 派生は、サポートされている文字列変換、日付変換、数値変換のいかなる組み合わせでも構いません。 

次の文字列変換、日付変換、および数値変換がサポートされています。

**文字列変換:** 

数値と日付のインテリジェント抽出、連結、大文字小文字操作、定数値マッピングを含むサブ文字列

**日付変換:** 

日付形式変更、日付部分の抽出、時間から時間ビンへのマッピング。

日付変換はかなり汎用的ですが、いくつかの注目すべき制限があります。
* タイム ゾーンはサポートされていません。
* いくつかの一般的な形式がサポートされていません。
    * ISO 8601 週/年形式 (例: “2009-W53-7”) 
    * Unix エポック時間。
* すべての形式は大文字と小文字を区別します (特に “4am” は時刻として認識されませんが “4AM” は認識されます)。

**数値変換:** 

丸め、床関数、天井関数、ビニング、ゼロまたはスペースでのパディング、1000 の累乗による除算または乗算。

**複合変換:** 

文字列変換、数値変換、または日付変換の任意の組み合わせ。

## <a name="how-to-use-this-transformation"></a>この変換の使用方法

この変換を使用するには、次の手順を実行します。
1. 値の派生元にする 1 つ以上の列を選択します。 
2. **[Transforms] (変換)** メニューから **[Derive Column by Example (例による列の派生)]** を選択します。 または、選択した列のいずれかのヘッダーを右クリックして、コンテキスト メニューから **[Derive Column by Example (例による列の派生)]** を選択します。 変換エディターが開き、新しい列が、右端の選択した列の横に追加されます。 選択した列は、列ヘッダーのチェック ボックスによって識別できます。 選択された列の追加や削除は、列のヘッダーのチェックボックスを使用して実行できます。
3. 行に対して *[output] (出力)* の例を入力し、Enter キーを押します。 この時点では、Workbench は、提供された出力に加えて入力列を分析し、特定の入力を出力に変換できるプログラムを合成します。 合成されたプログラムは、データ グリッド内のすべての行に対して実行されます。 あいまいなケースや複雑なケースについては、複数の例が必要な場合があります。 基本モードか詳細モードかによって、複数の例を異なる方法で提供できます。
4. 出力をレビューし、**[OK]** をクリックして変換を承諾します。

### <a name="transform-editor-basic-mode"></a>変換エディター: 基本モード

基本モードでは、データ グリッドでのインライン編集ができます。 目的のセルに移動して値を入力することにより、出力の例を提供できます。 

Workbench はデータを分析し、ユーザーによるレビューが必要なエッジ ケースの特定を試みます。 データの分析中、変換エディターのヘッダーに **[Analyzing Data (データ分析中)]** と表示されます。 分析が完了すると、ヘッダーに **[No Suggestions (提案なし)]** または **[Review next suggested row (次の提案行をレビュー)]** と表示されます。 **[Review next suggested row (次の提案行をレビュー)]** をクリックすると、エッジ ケース間を移動できます。 値が行に対して適切でない場合、追加例として正しい値を入力する必要があります。 

### <a name="transform-editor-advanced-mode"></a>変換エディター: 詳細モード

詳細モードでは、例による列の派生に関して、より詳細な操作を実行できます。 すべての例は 1 つの場所に表示されます。 **[Show suggested examples (提案例を表示)]** をクリックして、すべてのエッジ ケースを 1 つの場所でレビューすることもできます。 

詳細モードでは、グリッド内の行をダブルクリックすることにより、任意の行を例の行として追加できます。 例の行として行がコピーされたら、ソース列のデータを編集して合成例を作成することもできます。 これにより、サンプル データに現存しないケースを追加できます。

ユーザーは、変換エディターでリンクをクリックして、**基本モード**と**詳細モード**を切り替えられます。

### <a name="editing-existing-transformation"></a>既存の変換の編集

ユーザーは変換ステップの **[編集]** オプションを選択することによって、既存の **Derive Column By Example (例による列の派生)** 変換を編集できます。 **[編集]** をクリックすると、変換エディターが**詳細モード**で開き、変換の作成時に提供されたすべての例が表示されます。

## <a name="examples-of-string-transformations-by-example"></a>例による文字列変換の例


>[!NOTE] 
>**太字**の値は、示されたデータセットで変換を完了するために提供された例を表します。


### <a name="s1-extracting-file-names-from-file-paths"></a>S1。 ファイル パスからファイル名を抽出

このケースに必要だった例の数: 2

|入力|出力|
|:-----|:-----|
|C:\Python35\Tools\pynche\TypeinViewer.py|**TypeinViewer.py**|
|C:\Python35\Tools\pynche\webcolors.txt|webcolors.txt|
|C:\Python35\Tools\pynche\websafe.txt|websafe.txt|
|C:\Python35\Tools\pynche\X\rgb.txt|rgb.txt|
|C:\Python35\Tools\pynche\X\xlicense.txt|xlicense.txt|
|C:\Python35\Tools\Scripts\2to3.py|2to3.py|
|C:\Python35\Tools\Scripts\analyze_dxp.py|**analyze_dxp.py**|
|C:\Python35\Tools\Scripts\byext.py|byext.py|
|C:\Python35\Tools\Scripts\byteyears.py|byteyears.py|
|C:\Python35\Tools\Scripts\checkappend.py|checkappend.py|

### <a name="s2-case-manipulation-during-string-extraction"></a>S2. 文字列抽出中の大文字小文字操作

このケースに必要だった例の数: 3

|入力|出力|
|:-----|:-----|
|REINDEER CT & DEAD END;  NEW HANOVER; Station 332; 2015-12-10 @ 17:10:52;|**New Hanover**|
|BRIAR PATH & WHITEMARSH LN;  HATFIELD TOWNSHIP; Station 345; 2015-12-10 @ 17:29:21;|Hatfield Township|
|HAWS AVE; NORRISTOWN; 2015-12-10 @ 14:39:21-Station:STA27;|**Norristown**|
|AIRY ST & SWEDE ST;  NORRISTOWN; Station 308A; 2015-12-10 @ 16:47:36;|**Norristown**|
|CHERRYWOOD CT & DEAD END;  LOWER POTTSGROVE; Station 329; 2015-12-10 @ 16:56:52;|Lower Pottsgrove|
|CANNON AVE & W 9TH ST;  LANSDALE; Station 345; 2015-12-10 @ 15:39:04;|Lansdale|
|LAUREL AVE & OAKDALE AVE;  HORSHAM; Station 352; 2015-12-10 @ 16:46:48;|Horsham|
|COLLEGEVILLE RD & LYWISKI RD;  SKIPPACK; Station 336; 2015-12-10 @ 16:17:05;|Skippack|
|MAIN ST & OLD SUMNEYTOWN PIKE;  LOWER SALFORD; Station 344; 2015-12-10 @ 16:51:42;|Lower Salford|
|BLUEROUTE  & RAMP I476 NB TO CHEMICAL RD; PLYMOUTH; 2015-12-10 @ 17:35:41;|Plymouth|
|RT202 PKWY & KNAPP RD; MONTGOMERY; 2015-12-10 @ 17:33:50;|Montgomery|
|BROOK RD & COLWELL LN; PLYMOUTH; 2015-12-10 @ 16:32:10;|Plymouth|

### <a name="s3-date-format-manipulation-during-string-extraction"></a>S3. 文字列抽出中の日付形式操作

このケースに必要だった例の数: 1

|入力|出力|
|:-----|:-----|
|MONTGOMERY AVE & WOODSIDE RD;  LOWER MERION; Station 313; 2015-12-11 @ 04:11:35;|**12 Nov 2015 4AM**|
|DREYCOTT LN & W LANCASTER AVE;  LOWER MERION; Station 313; 2015-12-11 @ 01:29:52;|12 Nov 2015 1AM|
|E LEVERING MILL RD & CONSHOHOCKEN STATE RD; LOWER MERION; 2015-12-11 @ 07:29:58;|12 Nov 2015 7AM|
|PENN VALLEY RD & MANOR RD;  LOWER MERION; Station 313; 2015-12-10 @ 20:53:30;|12 Oct 2015 8PM|
|BELMONT AVE & OVERHILL RD; LOWER MERION; 2015-12-10 @ 23:02:27;|12 Oct 2015 11PM|
|W MONTGOMERY AVE & PENNSWOOD RD; LOWER MERION; 2015-12-10 @ 19:25:22;|12 Oct 2015 7PM|
|ROSEMONT AVE & DEAD END;  LOWER MERION; Station 313; 2015-12-10 @ 18:43:07;|12 Oct 2015 6PM|
|AVIGNON DR & DEAD END; LOWER MERION; 2015-12-10 @ 20:01:29-Station:STA24;|12 Oct 2015 8PM|

### <a name="s4-concatenating-strings"></a>S4. 文字列の連結

このケースに必要だった例の数: 1

>[!NOTE] 
>この例では、特殊文字は [Output] (出力) 列に含まれるスペースを表します。

|名|ミドル ネームのイニシャル|姓|出力|
|:-----|:-----|:-----|:-----|
|Laquanda||Lohmann|Laquanda··Lohmann|
|Claudio|A|Chew|**Claudio·A·Chew**|
|Sarah-Jane|S|Smith|Sarah-Jane·S·Smith|
|Brandi||Blumenthal|Brandi··Blumenthal|
|Jesusita|R|Journey|Jesusita·R·Journey|
|Hermina||Hults|Hermina··Hults|
|Anne-Marie|W|Jones|Anne-Marie·W·Jones|
|Rico||Ropp|Rico··Ropp|
|Lauren-May||Fullmer|Lauren-May··Fullmer|
|Marc|T|Maine|Marc·T·Maine|
|Angie||Adelman|Angie··Adelman|
|John-Paul||Smith|John-Paul··Smith|
|Song|W|Staller|Song·W·Staller|
|Jill||Jefferies|Jill··Jefferies|
|Ruby-Grace|M|Simmons|Ruby-Grace·M·Simmons|

### <a name="s5-generating-initials"></a>S5. Generating initials

このケースに必要だった例の数: 2

|フルネーム|出力|
|:-----|:-----|
|Laquanda Lohmann|**L.L.**|
|Claudio Chew|C.C.|
|Sarah-Jane Smith|S.S.|
|Brandi Blumenthal|B.B.|
|Jesusita Journey|J.J.|
|Hermina Hults|H.H.|
|Anne-Marie Jones|A.J.|
|Rico Ropp|R.R.|
|Lauren-May Fullmer|L.F.|
|Marc Maine|M.M.|
|Angie Adelman|A.A.|
|John-Paul Smith|**J.S.**|
|Song Staller|S.S.|
|Jill Jefferies|J.J.|
|Ruby-Grace Simmons|R.S.|


### <a name="s6-mapping-constant-values"></a>S6. 定数値のマッピング

このケースに必要だった例の数: 3

|管理性別|出力|
|:-----|:-----:|
|男性|**0**|
|女性|**1**|
|Unknown|**2**|
|女性|1|
|女性|1|
|男性|0|
|Unknown|2|
|男性|0|
|女性|1|

## <a name="examples-of-number-transformations-by-example"></a>例による数値変換の例

>[!NOTE] 
>**太字**の値は、示されたデータセットで変換を完了するために提供された例を表します。


### <a name="n1-rounding-to-nearest-10"></a>N1. 最も近い 10 への丸め

このケースに必要だった例の数: 1

|入力|出力|
|-----:|-----:|
|112|**110**|
|117|120|
|11112|11110|
|11119|11120|
|548|550|

### <a name="n2-rounding-down-to-nearest-10"></a>N2. 最も近い 10 への切り捨て

このケースに必要だった例の数: 2

|入力|出力|
|-----:|-----:|
|112|**110**|
|117|**110**|
|11112|11110|
|11119|11110|
|548|540|

### <a name="n3-rounding-to-nearest-005"></a>N3. 最も近い 0.05 への丸め

このケースに必要だった例の数: 2

|入力|出力|
|-----:|-----:|
|-75.5812935|**-75.60**|
|-75.2646799|-75.25|
|-75.3519752|-75.35|
|-75.343513|**-75.35**|
|-75.6033497|-75.60|
|-75.283245|-75.30|

### <a name="n4-binning"></a>N4. ビニング

このケースに必要だった例の数: 1

|入力|出力|
|-----:|:-----:|
|20.16|**20-25**|
|14.32|10-15|
|5.44|5-10|
|3.84|0-5|
|3.73|0-5|
|7.36|5-10|

### <a name="n5-scaling-by-1000"></a>N5. 1000 によるスケーリング

このケースに必要だった例の数: 1

|入力|出力|
|-----:|-----:|
|-243|**-243000**|
|-12.5|-12500|
|-2345.23292|-2345232.92|
|-1202.3433|-1202343.3|
|1202.3433|1202343.3|

### <a name="n6-padding"></a>N6. パディング

このケースに必要だった例の数: 1

|コード|出力|
|-----:|-----:|
|5828|**05828**|
|44130|44130|
|49007|49007|
|29682|29682|
|4759|04759|
|10029|10029|
|7204|07204|

## <a name="examples-of-date-transformations-by-example"></a>例による日付変換の例

>[!NOTE] 
>**太字**の値は、示されたデータセットで変換を完了するために提供された例を表します。


### <a name="d1-extracting-date-parts"></a>D1. 日付部分の抽出

これらの日付部分は、同じデータ セットに対する別の "例による変換" を使用して抽出されました。 太字の文字列は、それぞれの変換で与えられた例を表します。

|DateTime|平日|日付|月|年|Hour|[分]|秒|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**Fri**|**31**|**Jan**|**2031**|**5**|**54**|**18**|
|17-Jan-1990 13:32:01|Wed|17|Jan|1990|13|32|01|
|14-Feb-2034 05:36:07|Tue|14|Feb|2034|5|36|07|
|14-Mar-2002 13:16:16|Thu|14|Mar|2002|13|16|16|
|21-Jan-1985 05:44:43|Mon|21|Jan|1985|5|44|**43**|
|16-Aug-1985 01:11:56|Fri|16|Aug|1985|1|11|56|
|20-Dec-2033 18:36:29|Tue|20|Dec|2033|18|36|29|
|16-Jul-1984 10:21:59|Mon|16|Jul|1984|10|21|59|
|13-Jan-2038 10:59:36|Wed|13|Jan|2038|10|59|36|
|14-Aug-1982 15:13:54|Sat|14|Aug|1982|15|13|54|
|22-Nov-2030 08:18:08|Fri|22|Nov|2030|8|18|08|
|21-Oct-1997 08:42:58|Tue|21|Oct|1997|8|42|58|
|28-Nov-2006 14:19:15|Tue|28|Nov|2006|14|19|15|
|29-Apr-2031 04:59:45|Tue|29|Apr|2031|4|59|45|
|29-Jan-2032 02:38:36|Thu|29|Jan|2032|2|38|36|
|11-May-2028 15:31:52|Thu|11|May|2028|15|31|52|
|15-Jul-1977 12:45:39|Fri|15|Jul|1977|12|45|39|
|27-Jan-2029 05:55:41|Sat|27|Jan|2029|5|55|41|
|03-Mar-2024 10:17:49|Sun|3|Mar|2024|10|17|49|
|14-Apr-2010 00:23:13|Wed|14|Apr|2010|0|23|13|

### <a name="d2-formatting-dates"></a>D2. 日付の書式設定

これらの日付書式設定は、同じデータ セットに対する別の "例による変換" を使用して行われました。 太字の文字列は、それぞれの変換で与えられた例を表します。

|DateTime|Format1|Format2|Format3|Format4|Format5|
|-----:|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**1/31/2031**|**Friday, January 31, 2031**|**01312031 5:54**|**31/1/2031 5:54 AM**|**Q1 2031**|
|17-Jan-1990 13:32:01|1/17/1990|Wednesday, January 17, 1990|01171990 13:32|17/1/1990 1:32 PM|Q1 1990|
|14-Feb-2034 05:36:07|2/14/2034|Tuesday, February 14, 2034|02142034 5:36|14/2/2034 5:36 AM|Q1 2034
|14-Mar-2002 13:16:16|3/14/2002|Thursday, March 14, 2002|03142002 13:16|14/3/2002 1:16 PM|Q1 2002
|21-Jan-1985 05:44:43|1/21/1985|Monday, January 21, 1985|01211985 5:44|21/1/1985 5:44 AM|Q1 1985
|16-Aug-1985 01:11:56|8/16/1985|Friday, August 16, 1985|08161985 1:11|16/8/1985 1:11 AM|Q3 1985
|20-Dec-2033 18:36:29|12/20/2033|Tuesday, December 20, 2033|12202033 18:36|20/12/2033 6:36 PM|Q4 2033
|16-Jul-1984 10:21:59|7/16/1984|Monday, July 16, 1984|07161984 10:21|16/7/1984 10:21 AM|Q3 1984
|13-Jan-2038 10:59:36|1/13/2038|Wednesday, January 13, 2038|01132038 10:59|13/1/2038 10:59 AM|Q1 2038
|14-Aug-1982 15:13:54|8/14/1982|Saturday, August 14, 1982|08141982 15:13|14/8/1982 3:13 PM|Q3 1982
|22-Nov-2030 08:18:08|11/22/2030|Friday, November 22, 2030|11222030 8:18|22/11/2030 8:18 AM|Q4 2030
|21-Oct-1997 08:42:58|10/21/1997|Tuesday, October 21, 1997|10211997 8:42|21/10/1997 8:42 AM|Q4 1997
|28-Nov-2006 14:19:15|11/28/2006|Tuesday, November 28, 2006|11282006 14:19|28/11/2006 2:19 PM|Q4 2006
|29-Apr-2031 04:59:45|4/29/2031|Tuesday, April 29, 2031|04292031 4:59|29/4/2031 4:59 AM|Q2 2031
|29-Jan-2032 02:38:36|1/29/2032|Thursday, January 29, 2032|01292032 2:38|29/1/2032 2:38 AM|Q1 2032
|11-May-2028 15:31:52|5/11/2028|Thursday, May 11, 2028|05112028 15:31|11/5/2028 3:31 PM|Q2 2028
|15-Jul-1977 12:45:39|7/15/1977|Friday, July 15, 1977|07151977 12:45|15/7/1977 12:45 PM|Q3 1977
|27-Jan-2029 05:55:41|1/27/2029|Saturday, January 27, 2029|01272029 5:55|27/1/2029 5:55 AM|Q1 2029
|03-Mar-2024 10:17:49|3/3/2024|Sunday, March 3, 2024|03032024 10:17|3/3/2024 10:17 AM|Q1 2024
|14-Apr-2010 00:23:13|4/14/2010|Wednesday, April 14, 2010|04142010 0:23|14/4/2010 12:23 AM|Q2 2010


### <a name="d3-mapping-time-to-time-periods"></a>D3. 時間から期間へのマッピング

これらの日付時刻から期間へのマッピングは、同じデータ セットに対する別の "例による変換" を使用して行われました。 太字の文字列は、それぞれの変換で与えられた例を表します。

|DateTime|期間(秒)|期間(分)|期間(2 時間)|期間(30 分)|
|-----:|-----:|-----:|-----:|-----:|
|31-Jan-2031 05:54:18|**0-20**|**45-60**|**5AM-7AM**|**5:30-6:00**|
|17-Jan-1990 13:32:01|**0-20**|30-45|1PM-3PM|13:30-14:00|
|14-Feb-2034 05:36:07|0-20|30-45|5AM-7AM|5:30-6:00|
|14-Mar-2002 13:16:16|0-20|15-30|1PM-3PM|13:00-13:30|
|21-Jan-1985 05:44:43|40-60|30-45|5AM-7AM|5:30-6:00|
|16-Aug-1985 01:11:56|40-60|0-15|1AM-3AM|1:00-1:30|
|20-Dec-2033 18:36:29|20-40|30-45|5PM-7PM|18:30-19:00|
|16-Jul-1984 10:21:59|40-60|15-30|9AM-11AM|10:00-10:30|
|13-Jan-2038 10:59:36|20-40|45-60|9AM-11AM|10:30-11:00|
|14-Aug-1982 15:13:54|40-60|0-15|3PM-5PM|15:00-15:30|
|22-Nov-2030 08:18:08|0-20|15-30|7AM-9AM|8:00-8:30|
|21-Oct-1997 08:42:58|40-60|30-45|7AM-9AM|8:30-9:00|
|28-Nov-2006 14:19:15|0-20|15-30|1PM-3PM|14:00-14:30|
|29-Apr-2031 04:59:45|40-60|45-60|3AM-5AM|4:30-5:00|
|29-Jan-2032 02:38:36|20-40|30-45|1AM-3AM|2:30-3:00|
|11-May-2028 15:31:52|40-60|30-45|3PM-5PM|15:30-16:00|
|15-Jul-1977 12:45:39|20-40|45-60|11AM-1PM|12:30-13:00|
|27-Jan-2029 05:55:41|40-60|45-60|5AM-7AM|5:30-6:00|
|03-Mar-2024 10:17:49|40-60|15-30|9AM-11AM|10:00-10:30|
|14-Apr-2010 00:23:13|0-20|15-30|11PM-1AM|0:00-0:30|

## <a name="examples-of-composite-transformations-by-example"></a>例による複合変換の例

|tripduration|starttime|start station id|start station latitude|start station longitude|usertype|分割|
|-----:|-----:|-----:|-----:|-----:|-----:|-----:|
|61|2016-01-08 16:09:32|107|42.3625|-71.08822|加入者|**加入者が駅 107、緯度/経度 (42.363,-71.088) から 2016 年 1 月 8 日の午後 4 時頃に自転車に乗りました。走行時間は 61 分でした**|
|61|2016-01-17 09:28:10|74|42.373268|-71.118579|顧客|顧客が駅 74、緯度/経度 (42.373,-71.119) から 2016 年 1 月 17 日の午前 9 時頃に自転車に乗りました。 走行時間は 61 分でした|
|62|2016-01-25 08:10:26|176|42.386748020450561|-71.119018793106079|加入者|加入者が駅 176、緯度/経度 (42.387,-71.119) から 2016 年 1 月 25 日の午前 8 時頃に自転車に乗りました。 走行時間は 62 分でした|
|63|2016-01-08 10:10:29|107|42.3625|-71.08822|加入者|加入者が駅 107、緯度/経度 (42.363,-71.088) から 2016 年 1 月 8 日の午前 10 時頃に自転車に乗りました。 走行時間は 63 分でした|
|64|2016-01-15 19:42:08|68|42.36507|-71.1031|加入者|加入者が駅 68、緯度/経度 (42.365,-71.103) から 2016 年 1 月 15 日の午後 7 時頃に自転車に乗りました。 走行時間は 64 分でした|
|64|2016-01-22 18:16:13|115|42.387995|-71.119084|加入者|加入者が駅 115、緯度/経度 (42.388,-71.119) から 2016 年 1 月 22 日の午後 6 時頃に自転車に乗りました。 走行時間は 64 分でした|
|68|2016-01-18 09:51:52|178|42.359573201090441|-71.101294755935669|加入者|加入者が駅 178、緯度/経度 (42.360,-71.101) から 2016 年 1 月 18 日の午前 9 時頃に自転車に乗りました。 走行時間は 68 分でした|
|69|2016-01-14 08:57:55|176|42.386748020450561|-71.119018793106079|加入者|加入者が駅 176、緯度/経度 (42.387,-71.119) から 2016 年 1 月 14 日の午前 8 時頃に自転車に乗りました。 走行時間は 69 分でした|
|69|2016-01-13 22:12:55|141|42.363560158429884|-71.08216792345047|加入者|加入者が駅 141、緯度/経度 (42.364,-71.082) から 2016 年 1 月 13 日の午後 10 時頃に自転車に乗りました。 走行時間は 69 分でした|
|69|2016-01-15 08:13:09|176|42.386748020450561|-71.119018793106079|加入者|加入者が駅 176、緯度/経度 (42.387,-71.119) から 2016 年 1 月 15 日の午前 8 時頃に自転車に乗りました。 走行時間は 69 分でした|


## <a name="technical-notes"></a>テクニカル ノート

### <a name="conditional-transformations"></a>条件付き変換
特定の例を満たす 1 つの変換を見つけることができない場合があります。 そのような場合、Derive Column by Example (例による列の派生) 変換は、何らかのパターンに基づいて入力をグループ化し、グループごとに別々の変換を学習することを試みます。 これを**条件付き変換**と呼びます。 **条件付き変換**は、入力列が 1 つの変換に対してのみ試行されます。 

### <a name="reference"></a>リファレンス
String Transformation by Example (例による文字列変換) 技術の詳細については、[こちらの出版物](https://www.microsoft.com/en-us/research/publication/automating-string-processing-spreadsheets-using-input-output-examples/)を参照してください。
