---
title: "Azure Scheduler で複雑なスケジュールと高度な繰り返しを構築する方法"
description: "Azure Scheduler で複雑なスケジュールと高度な繰り返しを構築する方法"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e329d152ea6a95c8cdfa6a507504601d4e0957cd


---
# <a name="how-to-build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Azure Scheduler で複雑なスケジュールと高度な繰り返しを構築する方法
## <a name="overview"></a>Overview
Azure Scheduler ジョブの中核となるのが *スケジュール*です。 スケジュールは、Scheduler でジョブを実行するタイミングと方法を決定します。

Azure Scheduler では、ジョブに対して 1 回限りの別々のスケジュールと、定期的なスケジュールを指定できます。 *1 回限り*のスケジュールは、指定された時間に起動します。実質的には、これらも 1 回だけ実行される*定期的な*スケジュールです。 定期的なスケジュールは、事前に定義された頻度で起動します。

この柔軟性により、Azure Scheduler では、次のようなさまざまなビジネス シナリオをサポートできます。

* 定期的なデータ クリーンアップ: たとえば、3 か月よりも古いツイートをすべて削除する
* アーカイブ: たとえば、毎月、バックアップ サービスに請求書の履歴を送信する
* 外部データの要求: たとえば、15 分ごとに、NOAA から新しいスキー場の気象レポートを取得する
* 画像処理: たとえば、すべての平日のピーク時以外に、クラウド コンピューティングを利用して、その日にアップロードされた画像を圧縮する

この記事では、Azure Scheduler で作成できるジョブの例を、手順に沿って説明します。 説明には、各スケジュールを表す JSON データを使用します。 [Scheduler REST API](https://msdn.microsoft.com/library/mt629143.aspx) を使用する場合は、この同じ JSON を使用して [Azure Scheduler ジョブを作成](https://msdn.microsoft.com/library/mt629145.aspx)できます。

## <a name="supported-scenarios"></a>サポートされるシナリオ
このトピックに示す多くの例では、Azure Scheduler がサポートするさまざまなシナリオについて説明します。 これらの例では、多くの動作パターンに対応するスケジュールの作成方法を幅広く示します。以下に、いくつかの例を示します。

* 特定の日時に 1 回実行する
* 明示した回数だけ実行を繰り返す
* すぐに実行し繰り返す
* 特定の時刻に開始し、 *n* 分、時間、日、週、または月ごとに実行を繰り返す。
* 毎週または毎月の頻度で、特定の日、特定の曜日、または月の特定の日にのみ実行を繰り返す
* ある期間内で複数回実行を繰り返す。たとえば、毎月の最終金曜日と月曜日、毎日の午前 5 時 15 分と午後 5 時 15 分など

## <a name="dates-and-datetimes"></a>日付と日付/時刻
Azure Scheduler ジョブにおける日付は、 [ISO 8601 仕様](http://en.wikipedia.org/wiki/ISO_8601) に従い、日付のみを含みます。

Azure Scheduler ジョブにおける日付/時刻の参照は、 [ISO 8601 仕様](http://en.wikipedia.org/wiki/ISO_8601) に従い、日付の部分と時刻の部分の両方を含みます。 UTC オフセットを指定していない日付/時刻は UTC と見なされます。  

## <a name="how-to-use-json-and-rest-api-for-creating-schedules"></a>方法: JSON や REST API を使用した、スケジュールの作成
[Azure Scheduler REST API](https://msdn.microsoft.com/library/mt629143) を使用して単純なスケジュールを作成するには、まず[サブスクリプションをリソースプロバイダーに登録](https://msdn.microsoft.com/library/azure/dn790548.aspx)します (Scheduler のプロバイダー名は *Microsoft.Scheduler* です)。次に、[ジョブ コレクションを作成](https://msdn.microsoft.com/library/mt629159.aspx)し、最後に[ジョブを作成](https://msdn.microsoft.com/library/mt629145.aspx)します。 ジョブを作成するときに、JSON を使用して、以下に抜粋したようなスケジュール設定と繰り返しを指定できます。

    {
        "startTime": "2012-08-04T00:00Z", // optional
         …
        "recurrence":                     // optional
        {
            "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
            "interval": 1,                // optional, how often to fire (default to 1)
            "schedule":                   // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // optional (default to recur infinitely)
            "endTime": "2012-11-04",      // optional (default to recur infinitely)
        },
        …
    }

## <a name="overview-job-schema-basics"></a>概要: ジョブのスキーマの基礎
次の表に、ジョブの繰り返しとスケジュール設定に関連する主要な要素の概要を示します。

| **JSON での名前** | **説明** |
|:--- |:--- |
| ***startTime*** |*startTime* は、日付/時刻です。 簡単なスケジュールの場合は、*startTime* は最初の実行で、複雑なスケジュールの場合は、ジョブは *startTime* と同時に起動します。 |
| ***recurrence*** |*recurrence* オブジェクトは、ジョブの繰り返しの規則と、ジョブの実行が繰り返される頻度を指定します。 この recurrence オブジェクトは、*frequency、interval、endTime、count*、および *schedule* という要素をサポートします。 *recurrence* を定義する場合は、*frequency* が必要です。*recurrence* の他の要素は省略可能です。 |
| ***frequency*** |*frequency* の文字列は、ジョブが繰り返される頻度の単位を表します。 サポートされる値は、*"minute"、"hour"、"day"、"week"*、または *"month"* です。 |
| ***interval*** |*interval* は正の整数で、ジョブの実行頻度を決定する *frequency* の間隔を示します。 たとえば、*interval* が 3 で *frequency* が "week" の場合は、ジョブは 3 週間おきに繰り返されます。 Azure Scheduler でサポートする最大の *interval* は、月の場合は 18 か月、週の場合は 78 週、日の場合は 548 日です。 時間と分については、1 <= *interval* <= 1000 の範囲をサポートします。 |
| ***endTime*** |*endTime* の文字列に指定した日付/時刻を過ぎると、ジョブは実行されません。 過去の日時を *endTime* に指定するのは無効です。 *endTime* または count が指定されていない場合は、ジョブが無期限で実行されます。 *endTime* と *count* の両方を、同じジョブに対して指定することはできません。 |
| ***count*** |<p>*count* は、このジョブが完了する前に実行する必要のある回数を指定する (0 より大きい) 正の整数です。</p><p>*count* は、完了と判定されるまでにジョブが実行する回数を表します。 たとえば、*count* に 5 を指定したジョブが月曜日から毎日実行される場合は、このジョブは金曜日に実行された後に完了します。 開始日が過去の場合、最初の実行は作成日時から計算されます。</p><p>*endTime* または *count* が指定されていない場合は、ジョブが無期限で実行されます。 *endTime* と *count* の両方を、同じジョブに対して指定することはできません。</p> |
| ***schedule*** |ジョブに頻度を指定すると、繰り返しのスケジュールに基づいて、そのジョブの繰り返しを変更できます。 *schedule* には、分、時間、曜日、月の日および週の数に基づいた変更を指定します。 |

## <a name="overview-job-schema-defaults-limits-and-examples"></a>概要: ジョブのスキーマの既定値、制限、および例
この概要の後で、これらの各要素の詳細について説明します。

| **JSON での名前** | **値の型** | **必須** | **既定値** | **有効な値** | **例** |
|:--- |:--- |:--- |:--- |:--- |:--- |
| ***startTime*** |String |なし |なし |ISO-8601 の日付/時刻 |<code>"startTime" : "2013-01-09T09:30:00-08:00"</code> |
| ***recurrence*** |オブジェクト |なし |なし |recurrence オブジェクト |<code>"recurrence" : { "frequency" : "monthly", "interval" : 1 }</code> |
| ***frequency*** |String |はい |なし |"minute"、"hour"、"day"、"week"、"month" |<code>"frequency" : "hour"</code> |
| ***interval*** |Number |なし |1 |1 ～ 1000。 |<code>"interval":10</code> |
| ***endTime*** |String |なし |なし |将来の時刻を表す日付/時刻の値 |<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
| ***count*** |Number |なし |なし |>= 1 |<code>"count": 5</code> |
| ***schedule*** |オブジェクト |なし |なし |schedule オブジェクト |<code>"schedule" : { "minute" : [30], "hour" : [8,17] }</code> |

## <a name="deep-dive-starttime"></a>詳細: *startTime*
次の表では、*startTime* による、ジョブの実行の制御方法を説明しています。

| **startTime の値** | **繰り返しなし** | **繰り返しあり。スケジュールなし** | **スケジュールありの繰り返し** |
|:--- |:--- |:--- |:--- |
| **開始時刻なし** |すぐに 1 回実行 |すぐに 1 回実行。 2 回目以降のジョブは、最後の実行時間から計算して実行 |<p>すぐに 1 回実行</p><p>2 回目以降のジョブは、繰り返しのスケジュールに基づいて実行</p> |
| **開始時刻が過去に設定されている** |すぐに 1 回実行 |<p>初回は、開始時刻の後に将来の最初の実行時刻を計算し、その時刻に実行</p><p>2 回目以降のジョブは、最後の実行時刻から計算して実行</p><p>詳細については、この表の後にある説明を参照してください</p> |<p>ジョブは、指定した開始時刻と "*同時に*" 開始。 最初は、開始時刻から計算したスケジュールに基づいて実行</p><p>2 回目以降のジョブは、繰り返しのスケジュールに基づいて実行</p> |
| **開始時刻が将来または現在に設定されている** |指定した開始時刻に 1 回実行 |<p>指定した開始時刻に 1 回実行</p><p>2 回目以降のジョブは、最後の実行時間から計算して実行</p> |<p>ジョブは、指定した開始時刻と "*同時に*" 開始。 最初は、開始時刻から計算したスケジュールに基づいて実行</p><p>2 回目以降のジョブは、繰り返しのスケジュールに基づいて実行</p> |

ここでは、*startTime* が過去であり、*recurrence* を設定し、*schedule* を設定していない場合についての動作を説明します。  現在の時刻が 2015 年 4 月 8 日 13 時 00 分、*startTime* が 2015 年 4 月 7 日 14時 00 分で、*recurrence* は 2 日ごと (*frequency* に day と *interval* に 2 を指定) であると仮定します。*startTime* が過去、つまり現在の時刻よりも前であることに注意してください。

この条件では、"*最初の実行*" は、2015 年 4 月 9 日 14 時 00 分になります。\.Scheduler エンジンは、開始時刻から実行を計算します。  過去のインスタンスはすべて破棄されます。 エンジンは、将来発生する次回のインスタンスを使用します。  したがって、この場合は、*startTime* が 2015 年 4 月 7 日午後 2 時 00 分となり、次回のインスタンスは 2 日後、つまり 2015 年 4 月 9 日午後 2 時 00 分となります。

startTime が 2015 年 4 月 5 日 14 時 00 分または 2015 年 4 月 1 日 14 時 00 分である場合でも、最初の実行は同じであることに注意してください。\. 最初の実行後、スケジュールを使用して 2 回目以降の実行が計算されます。よって、2 回目以降の実行は、2015 年 4 月 11 日午後 2 時 00 分、2015 年 4 月 13 日午後 2 時 00 分、2015 年 4 月 15 日午後 2 時 00 分のように続きます。

最後に、ジョブにスケジュールが設定されている場合に、スケジュールに時および/または分が設定されていないと、既定では、最初の実行に指定した時および/または分が、それぞれ使用されます。

## <a name="deep-dive-schedule"></a>詳細: *schedule*
一方、*schedule* は、ジョブの実行回数を "*制限*" できます。  たとえば、frequency が "month" のジョブに、31 日にのみ実行する *schedule* を設定すると、ジョブは 31<sup></sup> 日がある月にのみ実行されます。

一方で、*schedule* によりジョブの実行回数を "*増やす*" こともできます。 たとえば、frequency が "month" のジョブに、月 の 1 日と 2 日に実行する *schedule* を設定すると、ジョブは、月に 1 回ではなく、毎月 1<sup></sup> 日と 2<sup></sup> 日に実行されます。

schedule の要素を複数指定した場合は、評価の順序は大きい要素から小さい要素の順序となります。つまり、週番号、月の日、曜日、時間、および分という順序です。

*schedule* の要素を次の表に示します。

| **JSON での名前** | **説明** | **有効な値** |
|:--- |:--- |:--- |
| **分** |ジョブを実行する時刻 (分) |<ul><li>整数、または</li><li>整数の配列</li></ul> |
| **hours** |ジョブを実行する時刻 (時) |<ul><li>整数、または</li><li>整数の配列</li></ul> |
| **weekDays** |ジョブを実行する曜日。 週単位の頻度だけを指定できます。 |<ul><li>"Monday"、"Tuesday"、"Wednesday"、"Thursday"、"Friday"、"Saturday"、"Sunday"</li><li>上記の任意の値の配列 (最大配列サイズは 7)</li></ul>大文字/小文字は "*区別されません*" |
| **monthlyOccurrences** |ジョブを実行する月の日にちを指定します。 月単位の頻度だけを指定できます。 |<ul><li>monthlyOccurence オブジェクトの配列:</li></ul> <pre>{ "day": *day*,<br />  "occurrence": *occurence*<br />}</pre><p> *day* は、ジョブを実行する曜日です。たとえば、{Sunday} は、月の毎週日曜日という意味です。 必須。</p><p>*occurrence* は、月の第何週目に実行するかを表します。たとえば、{Sunday, -1} は月の最終日曜日という意味です。 省略可能。</p> |
| **monthDays** |ジョブが実行される月の日にち。 月単位の頻度だけを指定できます。 |<ul><li>-1 以下かつ -31 以上の任意の値。</li><li>1 以上かつ 31 以下の任意の値。</li><li>上記の値の配列</li></ul> |

## <a name="examples-recurrence-schedules"></a>例: 繰り返しのスケジュール
次に、schedule オブジェクトとそのサブ要素に注目して、さまざまな繰り返しの例を示します。

次のスケジュールでは、すべて *interval* を 1 \.に設定してとを仮定しています。また、*schedule* の値に合った適切な頻度が指定されていると仮定します。たとえば、スケジュールで frequency に "day" を使用して "monthDays" を変更することはできません。 そのような制限は、上で説明しています。

| **例** | **説明** |
|:--- |:--- |
| <code>{"hours":[5]}</code> |毎日午前 5 時に実行。 Azure Scheduler は "hours" の値を、"minutes" の値と 1 つずつ突き合わせ、ジョブを実行するすべての時刻の一覧を作成します。 |
| <code>{"minutes":[15], "hours":[5]}</code> |毎日午前 5 時 15 分に実行 |
| <code>{"minutes":[15], "hours":[5,17]}</code> |毎日午前 5 時 15 分と 午後 5 時 15 分に実行 |
| <code>{"minutes":[15,45], "hours":[5,17]}</code> |毎日午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、および午後 5 時 45 分に実行 |
| <code>{"minutes":[0,15,30,45]}</code> |15 分ごとに実行 |
| <code>{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}</code> |1 時間ごとに実行。 このジョブは、1 時間ごとに実行されます。 分は、指定されている場合は、*startTime* によって制御されます。指定されていない場合は、作成時間によって制御されます。 たとえば、開始時刻または作成時刻が午後 12 時 25 分である場合 (どちらが適用される場合でも)、ジョブは 0 時 25 分、1 時 25 分、2 時 25 分...23 時 25 分に実行されます。 このスケジュールは、*frequency* に "hour"、*interval* に 1 を指定して、*schedule* を指定しないジョブと同等です。 両者の違いは、このスケジュールは、*frequency* と *interval* を変えることで、他のジョブの作成に使用できる点にあります。 たとえば、*frequency* が "month" の場合には、月 1 回だけ実行されます。*frequency* が "day" の場合には、スケジュールは毎日実行されます。 |
| <code>{minutes:[0]}</code> |毎正時に実行。 このジョブも 1 時間ごとに実行されますが、正時に実行されます。(例、午前 12 時、午前 1 時、午前 2 時)。これは、frequency に "hour"、startTime に 0 分を設定し、さらに頻度が "day" であれば、schedule を設定しなかったジョブと同等です。ただし、頻度が "week" または "month" の場合は、このスケジュールは、それぞれ週 に 1 日だけ、または月に 1 日だけ実行します。 |
| <code>{"minutes":[15]}</code> |正時から 15 分経過後に実行。 1 時間ごとに実行。午前 0 時 15 分から開始し、午前 1 時 15 分、午前 2 時 15 分...午後 10 時 15 分と続き、最後に午後 11 時 15 分に実行します。 |
| <code>{"hours":[17], "weekDays":["saturday"]}</code> |毎週土曜日の午後 5 時に実行 |
| <code>{hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |毎週月曜日、水曜日、および金曜日の午後 5 時に実行 |
| <code>{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |毎週月曜日、水曜日、および金曜日の午後 5 時 15 分と午後 5 時 45 分に実行 |
| <code>{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |毎週月曜日、水曜日、および金曜日の午前 5 時 と午後 5 時に実行 |
| <code>{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |毎週月曜日、水曜日、および金曜日の午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、および午後 5 時 45 分に実行 |
| <code>{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |平日に 15 分ごとに実行 |
| <code>{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |平日の午前 9 時と午後 4 時 45 分の間、15 分ごとに実行 |
| <code>{"weekDays":["sunday"]}</code> |日曜日の開始時刻に実行 |
| <code>{"weekDays":["tuesday", "thursday"]}</code> |火曜日と木曜日の開始時刻に実行 |
| <code>{"minutes":[0], "hours":[6], "monthDays":[28]}</code> |毎月 28 日の午前 6 時に実行 (頻度を月と仮定した場合) |
| <code>{"minutes":[0], "hours":[6], "monthDays":[-1]}</code> |月の最終日の午前 6 時に実行。 月の最終日にジョブを実行する場合は、28、29、30、または 31 という日にちではなく -1 を使用します。 |
| <code>{"minutes":[0], "hours":[6], "monthDays":[1,-1]}</code> |毎月の最初と最後の日の午前 6 時に実行 |
| <code>{monthDays":[1,-1]}</code> |毎月の最初と最後の日の開始時刻に実行 |
| <code>{monthDays":[1,14]}</code> |毎月 1 日と 14 日の開始時刻に実行 |
| <code>{monthDays":[2]}</code> |月の 2 日の開始時刻に実行 |
| <code>{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |毎月の最初の金曜日の午前 5 時に実行 |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |毎月の最初の金曜日の開始時刻に実行 |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}</code> |毎月、月の最後から 3 番目の金曜日の開始時刻に実行 |
| <code>{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |毎月の最初と最後の金曜日の午前 5 時 15 分に実行 |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |毎月の最初と最後の金曜日の開始時刻に実行 |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}</code> |毎月の第 5 金曜日の開始時刻に実行 第 5 金曜日にのみ実行するスケジュールとなっているため、月に第 5 金曜日がない場合は、これは実行されません。 月の最終金曜日にジョブを実行する場合は、occurrence に対して 5 ではなく -1 を使用することを検討してください。 |
| <code>{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}</code> |月の最終金曜日に 15 分ごとに実行 |
| <code>{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}</code> |毎月第 3 水曜日の午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、および午後 5 時 45 分に実行 |

## <a name="see-also"></a>関連項目
 [What is Scheduler? (Scheduler とは)](scheduler-intro.md)

 [Azure Scheduler の概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)

 [Azure ポータル内で Scheduler を使用した作業開始](scheduler-get-started-portal.md)

 [Azure Scheduler のプランと課金](scheduler-plans-billing.md)

 [Azure Scheduler REST API リファレンス](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)

 [Azure Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Azure Scheduler の制限、既定値、エラー コード](scheduler-limits-defaults-errors.md)

 [Azure Scheduler 送信認証](scheduler-outbound-authentication.md)




<!--HONumber=Nov16_HO3-->


