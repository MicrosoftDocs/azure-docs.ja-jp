---
title: 高度なジョブ スケジュールと繰り返しを構築する
description: Azure Scheduler でジョブの高度なスケジュールと繰り返しを作成する方法について説明します
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.suite: infrastructure-services
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: 5a74240e3f116121c0aaddd11c186e6e674ea26a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368181"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Azure Scheduler でジョブの高度なスケジュールと繰り返しを構築する

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、[廃止される予定](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)の Azure Scheduler の後継です。 Scheduler で設定したジョブを使用し続けるには、できるだけ早く [Azure Logic Apps に移行](../scheduler/migrate-from-scheduler-to-logic-apps.md)してください。 
>
> Scheduler は Azure portal で利用できなくなりましたが、現時点では [REST API](/rest/api/scheduler) と [Azure Scheduler PowerShell コマンドレット](scheduler-powershell-reference.md)がまだ使用できるので、お客様はジョブとジョブ コレクションを管理することができます。

[Azure Scheduler](../scheduler/scheduler-intro.md) ジョブでは、スケジュールが核となって、Scheduler サービスでジョブを実行するタイミングと方法が決定されます。 Scheduler を使うと、1 回限りのスケジュールと定期的なスケジュールをジョブに対して複数設定することができます。 1 回限りのスケジュールは、指定された時刻に 1 回だけ実行されます。基本的には、これらは 1 回だけ実行される定期的なスケジュールです。 定期的なスケジュールは、指定された頻度で実行されます。 この柔軟性を活かし、さまざまなビジネス シナリオで Scheduler を使用することができます。その例を次に示します。

* **定期的にデータをクリーンアップする**: 3 か月以上経過したツイートをすべて削除する日単位のジョブを作成します。

* **データをアーカイブする**: バックアップ サービスに請求書の履歴をプッシュする月単位のジョブを作成します。

* **外部データを要求する**: 15 分おきに実行されるジョブを作成し、NOAA から新しい天気予報をプルします。

* **画像を処理する**: ピーク時間外に実行される平日のジョブを作成し、クラウド コンピューティングを利用して、その日にアップロードされた画像を圧縮します。

この記事では、Scheduler と [Azure Scheduler REST API](/rest/api/scheduler) を使用して作成できるサンプル ジョブについて説明すると共に、各スケジュールの JavaScript Object Notation (JSON) 定義を示します。 

## <a name="supported-scenarios"></a>サポートされるシナリオ

これらの例は、Azure Scheduler でサポートされるシナリオの範囲と、さまざまな動作パターンに対応するスケジュールの作成方法を示しています。以下に、その例を示します。

* 特定の日時に 1 回実行する。
* 特定の回数定期実行する。
* すぐに実行し繰り返す。
* 特定の時刻に開始して、*n* 分、時間、日、週、または月ごとに実行を繰り返す。
* 毎週、特定の曜日に実行を繰り返すか、毎月、特定の日に実行を繰り返す。
* 指定した期間に複数回実行を繰り返します。 たとえば、毎月最後の金曜日と月曜日に実行する、毎日午前 5 時 15 分と午後 5 時 15 分に実行するなど。

これらのシナリオについて、この記事で後ほど詳しく説明します。

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>REST API を使用してスケジュールを作成する

[Azure Scheduler REST API](/rest/api/scheduler) を使用して基本スケジュールを作成するには、次の手順に従います。

1. [Resource Manager REST API の登録操作](/rest/api/resources/providers)を使用して、Azure サブスクリプションをリソース プロバイダーに登録します。 Azure Scheduler サービスのプロバイダー名は **Microsoft.Scheduler** です。 

1. Scheduler REST API の[ジョブ コレクションの作成または更新操作](/rest/api/scheduler/jobcollections)を使用して、ジョブ コレクションを作成します。 

1. [ジョブの作成または更新操作](/rest/api/scheduler/jobs/createorupdate)を使用して、ジョブを作成します。 

## <a name="job-schema-elements"></a>ジョブのスキーマの要素

次の表に、ジョブの繰り返しとスケジュールを設定する際に使用できる主要な JSON 要素の概要を示します。 

| 要素 | 必須 | 説明 | 
|---------|----------|-------------|
| **startTime** | いいえ | ジョブがいつ基本スケジュールで開始されるかを指定する、[ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601)の DateTime 文字列値。 <p>複雑なスケジュールでは、**startTime** になるとすぐにジョブが開始されます。 | 
| **recurrence** | いいえ | ジョブが実行されるときの繰り返しの規則。 **recurrence** オブジェクトでは、**frequency**、**interval**、**schedule**、**count**、**endTime** の各要素がサポートされます。 <p>**recurrence** 要素を使用する場合は、**frequency** 要素も使用する必要があります。**recurrence** の他の要素は省略可能です。 |
| **frequency** | はい (**recurrence** を使用する場合) | 実行間の時間単位。サポートされる値は、"Minute"、"Hour"、"Day"、"Week"、"Month"、および "Year" です | 
| **interval** | いいえ | **frequency** に基づいて実行間の時間単位の数を決定する正の整数。 <p>たとえば、**interval** が 10 で **frequency** が "Week" の場合、ジョブは 10 週間おきに繰り返されます。 <p>各 frequency の最大 interval 数は次のとおりです。 <p>- 18 か月 <br>- 78 週 <br>- 548 日 <br>- 時間と分については、範囲は 1 <= <*interval*> <= 1,000 です。 | 
| **schedule** | いいえ | 指定した分、時間、曜日、および月の日にちに基づいて、recurrence に対する変更を定義します | 
| **count** | いいえ | ジョブが終了するまでの実行回数を指定する正の整数。 <p>たとえば、日単位のジョブの **count** を 7 に設定し、開始日を月曜日に設定した場合、ジョブの実行は日曜日に終了します。 開始日が既に過去の日付である場合、最初の実行は作成時刻から計算されます。 <p>**endTime** または **count** が指定されていない場合、ジョブは無期限で実行されます。 同じジョブで **count** と **endTime** の両方を使用することはできません。この場合、最初に完了する規則が適用されます。 | 
| **endTime** | いいえ | ジョブの実行をいつ終了するかを示す、[ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601)の Date または DateTime 文字列値。 **endTime** には、過去の値を設定することができます。 <p>**endTime** または **count** が指定されていない場合、ジョブは無期限で実行されます。 同じジョブで **count** と **endTime** の両方を使用することはできません。この場合、最初に完了する規則が適用されます。 |
|||| 

たとえば、次の JSON スキーマには、ジョブの基本スケジュールと繰り返しが記述されています。 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

"*日付と DateTime の値*"

* Scheduler ジョブにおける日付は、日付のみを含み、[ISO 8601 仕様](https://en.wikipedia.org/wiki/ISO_8601)に従います。

* Scheduler ジョブにおける日付/時刻は日付と時刻の両方を含み、[ISO 8601 仕様](https://en.wikipedia.org/wiki/ISO_8601)に従います。UTC オフセットが指定されていない場合は、UTC と見なされます。 

詳細については、[概念、用語、エンティティ](../scheduler/scheduler-concepts-terms.md)に関するページを参照してください。

<a name="start-time"></a>

## <a name="details-starttime"></a>詳細: startTime

次の表で、ジョブの実行が **startTime** によってどのように制御されるかを説明します。

| startTime | 繰り返しなし | スケジュールなしの繰り返し | スケジュールありの繰り返し |
|-----------|---------------|-------------------------|--------------------------|
| **開始時刻なし** | すぐに 1 回実行。 | すぐに 1 回実行。 最後の実行時刻から計算して後で実行。 | すぐに 1 回実行。 繰り返しのスケジュールに基づいて後で実行。 | 
| **開始時刻を過去に設定** | すぐに 1 回実行。 | 開始時刻後における将来の最初の実行時刻を計算し、その時刻に実行。 <p>最後の実行時刻から計算して後で実行。 <p>この表の後の例を参照してください。 | 指定された開始時刻と "*同時に*" ジョブを開始。 最初は、開始時刻から計算したスケジュールに基づいて実行します。 <p>繰り返しのスケジュールに基づいて後で実行。 | 
| **開始時刻が将来または現在に設定されている** | 指定した開始時刻に 1 回実行。 | 指定した開始時刻に 1 回実行。 <p>最後の実行時刻から計算して後で実行。 | 指定された開始時刻と "*同時に*" ジョブを開始。 最初は、開始時刻から計算したスケジュールに基づいて実行されます。 <p>繰り返しのスケジュールに基づいて後で実行。 |
||||| 

ここでは、開始時刻が過去で、recurrence を設定し、schedule を設定していないという条件を例として説明します。

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* 現在の日時は 2015 年 4 月 8 日午後 1 時 00 分です。

* 開始日時は、現在の日時よりも前の 2015 年 4 月 7 日午後 2 時 00 分です。

* 繰り返しは 2 日ごとです。

1. この条件では、最初の実行は 2015 年 4 月 9 日午後 2 時 00 分になります。 

   Scheduler は、開始時刻に基づいて実行の発生を計算し、過去のインスタンスをすべて破棄して、将来の次のインスタンスを使用します。 
   この場合は、**startTime** が 2015 年 4 月 7 日午後 2 時 00 分なので、次回のインスタンスは 2 日後、つまり 2015 年 4 月 9 日午後 2 時 00 分となります。

   **startTime** が 2015 年 4 月 5 日 14 時 00 分であっても 2015 年 4 月 1 日 14 時 00 分であっても、最初の実行時間は同じです。 最初の実行以降の実行は、スケジュールに基づいて計算されます。 
   
1. その後、ジョブは次の順序で実行されます。 
   
   1. 2015 年 4 月 11 日午後 2 時 00 分
   1. 2015 年 4 月 13 日午後 2 時 00 分 
   1. 2015 年 4 月 15 日午後 2 時 00 分
   1. 以降同様

1. 最後に、ジョブにスケジュールが設定されている一方で時と分が設定されていない場合、既定では、最初の実行に指定した時と分がそれぞれ使用されます。

<a name="schedule"></a>

## <a name="details-schedule"></a>詳細: schedule

**schedule** を使用すると、ジョブの実行回数を "*制限*" できます。 たとえば、**frequency** が "month" のジョブに、31 日にのみ実行する schedule を設定すると、ジョブが実行されるのは 31 日がある月のみとなります。

**schedule** によりジョブの実行回数を "*増やす*" こともできます。 たとえば、**frequency** が "month" のジョブに、月の 1 日と 2 日に実行する schedule を設定すると、ジョブは、月に 1 回ではなく、毎月 1 日と 2 日に実行されます。

schedule の要素を複数指定した場合は、評価の順序は大きい要素から小さい要素の順序となります。つまり、週番号、月の日、曜日、時間、分という順序です。

schedule の要素を次の表に詳しく示します。

| JSON での名前 | 説明 | 有効な値 |
|:--- |:--- |:--- |
| **分** |ジョブを実行する時刻 (分)。 |整数の配列。 |
| **hours** |ジョブを実行する時刻 (時)。 |整数の配列。 |
| **weekDays** |ジョブを実行する曜日。 週単位の頻度だけを指定できます。 |次の値の配列 (配列の最大サイズは 7)。<br />- "Monday"<br />- "Tuesday"<br />- "Wednesday"<br />- "Thursday"<br />- "Friday"<br />- "Saturday"<br />- "Sunday"<br /><br />大文字/小文字は区別されません。 |
| **monthlyOccurrences** |ジョブを実行する月の日にちを指定します。 月単位の頻度だけを指定できます。 |**monthlyOccurrences** オブジェクトの配列:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **day** は、ジョブを実行する曜日です。 たとえば、*{Sunday}* は、月の毎週日曜日という意味です。 必須。<br /><br />**occurrence** は、月の第何週目に実行するかを表します。 たとえば、*{Sunday, -1}* は月の最終日曜日という意味です。 省略可能。 |
| **monthDays** |ジョブが実行される月の日にち。 月単位の頻度だけを指定できます。 |次の値の配列。<br />- -1 以下かつ -31 以上の任意の値<br />- 1 以上かつ 31 以下の任意の値|

## <a name="examples-recurrence-schedules"></a>例: 繰り返しのスケジュール

次の例に、さまざまな繰り返しのスケジュールを示します。 この例では、schedule オブジェクトとそのサブ要素に注目します。

スケジュール例では、**interval** が 1\. に設定されていると仮定します。 また、**schedule** の値に合った適切な **frequency** 値が指定されていると仮定します。 たとえば、**frequency** に "day" を使用し、**schedule** に **monthDays** の調整値を指定することはできません。 そのような制限については、この記事の最初の方で説明しています。

| 例 | 説明 |
|:--- |:--- |
| `{"hours":[5]}` |毎日午前 5 時に実行されます。<br /><br />Scheduler は "hours" の値を、"minutes" の値と 1 つずつ突き合わせ、ジョブを実行するすべての時刻の一覧を作成します。 |
| `{"minutes":[15], "hours":[5]}` |毎日午前 5 時 15 分に実行されます。 |
| `{"minutes":[15], "hours":[5,17]}` |毎日午前 5 時 15 分と午後 5 時 15 分に実行されます。 |
| `{"minutes":[15,45], "hours":[5,17]}` |毎日午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、午後 5 時 45 分に実行されます。 |
| `{"minutes":[0,15,30,45]}` |15 分ごとに実行されます。 |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |1 時間ごとに実行されます。<br /><br />このジョブは、1 時間ごとに実行されます。 分は、**startTime** が指定されていれば、その値によって制御されます。 **startTime** の値が指定されていない場合は、作成時間によって制御されます。 たとえば、開始時刻または作成時刻が午後 12 時 25 分である場合 (どちらが適用される場合でも)、ジョブは 0 時 25 分、1 時 25 分、2 時 25 分...23 時 25 分に実行されます。<br /><br />このスケジュールは、**frequency** に "hour"、**interval** に 1 を指定して、**schedule** 値を指定しないジョブと同じです。 両者の違いは、このスケジュールは、**frequency** と **interval** の値を変えることで、他のジョブの作成に使用できる点にあります。 たとえば、**frequency** が "month" の場合には、月 1 回だけ実行されます。**frequency** が "day" の場合には、スケジュールは毎日実行されます。 |
| `{minutes:[0]}` |毎正時に実行されます。<br /><br />このジョブも 1 時間ごとに実行されますが、正時に実行されます。(例、午前 12 時、午前 1 時、午前 2 時など)。 このスケジュールは、**frequency** に "hour"、**startTime** 値に 0 分を指定したジョブ、または **schedule** を設定せずに frequency に "day" を指定したジョブと同じです。 ただし、**frequency** が "week" または "month" の場合、スケジュールはそれぞれ週に 1 日または月に 1 日だけ実行されます。 |
| `{"minutes":[15]}` |毎時、正時から 15 分後に実行されます。<br /><br />午前 0 時 15 分から開始され、午前 1 時 15 分、午前 2 時 15 分のように 1 時間ごとに実行され、 最後に午後 11 時 15 分に実行されます。 |
| `{"hours":[17], "weekDays":["saturday"]}` |毎週土曜日の午後 5 時に実行されます。 |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |毎週月曜日、水曜日、金曜日の午後 5 時に実行されます。 |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |毎週月曜日、水曜日、金曜日の午後 5 時 15 分と午後 5 時 45 分に実行されます。 |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |毎週月曜日、水曜日、金曜日の午後 5 時と午後 5 時に実行されます。 |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |毎週月曜日、水曜日、金曜日の午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、午後 5 時 45 分に実行されます。 |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |平日に 15 分ごとに実行されます。 |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |平日の午前 9 時から午後 4 時 45 分まで 15 分ごとに実行されます。 |
| `{"weekDays":["sunday"]}` |日曜日の開始時刻に実行されます。 |
| `{"weekDays":["tuesday", "thursday"]}` |火曜日と木曜日の開始時刻に実行されます。 |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |毎月 28 日の午前 6 時に実行されます (**frequency** が "month" であることを想定)。 |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |月の最終日の午前 6 時に実行されます。<br /><br />月の最終日にジョブを実行する場合は、28、29、30、または 31 という日にちではなく -1 を使用します。 |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |毎月最初と最後の日の午前 6 時に実行されます。 |
| `{monthDays":[1,-1]}` |毎月の最初と最後の日の開始時刻に実行されます。 |
| `{monthDays":[1,14]}` |毎月の最初の日と 14 日の開始時刻に実行されます。 |
| `{monthDays":[2]}` |月の 2 日の開始時刻に実行されます。 |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |毎月最初の金曜日の午前 5 時に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |毎月最初の金曜日の開始時刻に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |毎月、月の最後から 3 番目の金曜日の開始時刻に実行されます。 |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |毎月最初と最後の金曜日の午前 5 時 15 分に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |毎月最初と最後の金曜日の開始時刻に実行されます。 |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |毎月第 5 金曜日の開始時刻に実行されます。<br /><br />月に第 5 金曜日がない場合、このジョブは実行されません。 月の最終金曜日にジョブを実行する場合は、occurrence に対して 5 ではなく -1 を使用することを検討してください。 |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |月の最後の金曜日に 15 分ごとに実行されます。 |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |毎月第 3 水曜日の午前 5 時 15 分、午前 5 時 45 分、午後 5 時 15 分、午後 5 時 45 分に実行されます。 |

## <a name="next-steps"></a>次のステップ

* [Azure Scheduler の概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)
* [Azure Scheduler REST API リファレンス](/rest/api/scheduler)
* [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)
* [Azure Scheduler の制限、既定値、エラー コード](scheduler-limits-defaults-errors.md)