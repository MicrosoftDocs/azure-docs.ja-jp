---
title: 記録の再生 - Azure
description: Live Video Analytics on IoT Edge を使用して、クラウドにビデオを数週間から数か月にわたって録画する継続的なビデオ記録を行うことができます。 また、イベントベースの録画を使用して、記録を必要なクリップに制限することもできます。 この記事では、記録を再生する方法について説明します。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 0351f10d9fac3ad7e3b4fdd5fd549eb7c0023694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99490897"
---
# <a name="playback-of-recordings"></a>記録の再生 

## <a name="pre-read"></a>先にお読みください  

* [ビデオ再生](video-playback-concept.md)
* [継続的なビデオ記録](continuous-video-recording-concept.md)
* [イベントベースのビデオ記録](event-based-video-recording-concept.md)

## <a name="background"></a>バックグラウンド  

Live Video Analytics on IoT Edge を使用して、クラウドにビデオを数週間から数か月にわたって録画できる[継続的なビデオ記録](continuous-video-recording-concept.md) (CVR) を行うことができます。 また、[イベントベースのビデオ記録](event-based-video-recording-concept.md) (EVR) を使用して、記録を必要なクリップに制限することもできます。 

お使いの Media Services アカウントを Azure Storage アカウントにリンクして、ビデオをクラウドに録画すると、コンテンツは [Media Service アセット](terminology.md#asset)に書き込まれます。 Media Services では、録画完了後または録画中に[コンテンツのストリーミング](terminology.md#streaming)ができます。 Media Services には、HLS または MPEG-DASH プロトコルを介して、ストリームを再生デバイス (クライアント) に配信するために必要な機能が用意されています。 詳細については、「[ビデオ再生](video-playback-concept.md)」を参照してください。 Media Service API を使用して、必要なストリーミング URL を生成します。これは、ビデオとオーディオを再生するためにクライアントによって使用されます。 アセット用のストリーミング URL を作成するには、[ストリーミング ロケーターの作成と URL の構築](../latest/create-streaming-locator-build-url.md)に関するページを参照してください。 アセット用のストリーミング URL を作成したら、コンテンツ管理システムのビデオ ソース (つまり、カメラ) と URL の関連付けを保存する必要があります。

たとえば、HLS を介してストリーミングする場合、ストリーミング URL は `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` のようになります。 MPEG-DASH の場合は、`https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=mpd-time-cmaf).mpd` のようになります。

マニフェスト ファイルが返されます。このファイルには、配信されるストリームの全体時間と、事前に録画されたコンテンツなのか、進行中の "ライブ" フィードなのかが記述されています。

### <a name="live-vs-vod"></a>ライブとVOD  

HLS や MPEG-DASH などのストリーミング プロトコルは、ライブ ビデオのストリーミングのほか、テレビ番組や映画などのオンデマンドまたは事前録画のコンテンツのストリーミングなどのシナリオに対応するために作成されました。 ライブ ビデオの場合、HLS クライアントと MPEG-DASH クライアントは、"直近" の時刻から再生を開始するように設計されています。 ただし、動画の場合、視聴者は最初から開始し、必要に応じてジャンプすることを期待します。 HLS マニフェストと MPEG-DASH マニフェストには、ビデオがライブ ストリームを表すのか、事前録画コンテンツであるのかをクライアントに示すフラグがあります。
以上は、Live Video Analytics on IoT Edge を使用して録画されたビデオが含まれるアセットからの HLS ストリームと MPEG-DASH ストリームでも同じです。

## <a name="browsing-and-selective-playback-of-recordings"></a>記録の参照と選択再生  

Live Video Analytics on IoT Edge を使用して、学年度期間中に学校が開いている日の午前 8 時から午前 10 時までの間のみビデオを録画したシナリオを考えてみましょう。 あるいは、祝祭日の午前 7 時から午後 7 時の間のみビデオを録画することもあるかもしれません。 この 2 つのシナリオのいずれかで、ビデオ記録を参照して表示しようとする場合は、以下が必要になります。

* 記録に含まれているビデオの日付と時間を確認する方法。
* 記録の一部 (たとえば、元日の午前 9 時から午前 11 時) を選択して再生する方法。

Media Services には、最初の問題に対処するクエリ API (availableMedia) と 2 番目の問題に対処する時間範囲フィルター (startTime、endTime) が用意されています。

## <a name="query-api"></a>クエリ API 

CVR を使用する場合、再生デバイス (クライアント) から、記録全体の再生をカバーするマニフェストを要求することはできません。  複数年にまたがる記録では、再生するには大きすぎるマニフェスト ファイルが生成されるため、クライアント側で利用可能な部分に解析するのが困難になります。  クライアントでは、あまり推測しないでも有効な要求を行えるように、どの日時範囲のデータが記録に含まれているかを認識できる必要があります。 これを可能にするため、新しいクエリ API が導入され、クライアントで、このサーバー側 API を使用してコンテンツを検出できるようになりました。

精度の値に、年、月、日、または完全 (以下を参照) のいずれかを指定できます。 

|Precision|year|month|day|フル|
|---|---|---|---|---|
|クエリ|`/availableMedia?precision=year&startTime=2018&endTime=2019`|`/availableMedia?precision=month& startTime=2018-01& endTime=2019-02`|`/availableMedia?precision=day& startTime=2018-01-15& endTime=2019-02-02`|`/availableMedia?precision=full& startTime=2018-01-15T10:08:11.123& endTime=2019-01-015T12:00:01.123`|
|Response|`{  "timeRanges":[{ "start":"2018", "end":"2019" }]}`|`{  "timeRanges":[{ "start":"2018-03", "end":"2019-01" }]}`|`{  "timeRanges":[    { "start":"2018-03-01", "end":"2018-03-07" },    { "start":"2018-03-09", "end":"2018-03-31" }  ]}`|完全忠実度の応答。 ギャップがまったくない場合、開始は startTime、終了は endTime になります。|
|制約|&#x2022;startTime <= endTime<br/>&#x2022;どちらも YYYY 形式である必要があります。それ以外の場合は、エラーが返されます。<br/>&#x2022;値は何年離れていてもかまいません。<br/>&#x2022;値は範囲に含まれます。|&#x2022;startTime <= endTime<br/>&#x2022;どちらも YYYY-MM 形式である必要があります。それ以外の場合は、エラーが返されます。<br/>&#x2022;値は最大 12 か月まで離れていてもかまいません。<br/>&#x2022;値は範囲に含まれます。|&#x2022;startTime <= endTime<br/>&#x2022;どちらも YYYY-MM-DD 形式である必要があります。それ以外の場合は、エラーが返されます。<br/>&#x2022;値は最大 31 日まで離れていてもかまいません。<br/>値は範囲に含まれます。|&#x2022;startTime < endTime<br/>&#x2022;値は最大 25 時間まで離れていてもかまいません。<br/>&#x2022;値は範囲に含まれます。|

#### <a name="additional-request-format-considerations"></a>追加の要求形式に関する考慮事項

* 時間はすべて UTC 形式です
* 精度クエリ文字列パラメーターは必須です。  
* 月、日、および完全の精度値では、startTime および endTime クエリ文字列パラメーターは必須です。  
* 年の精度値では、startTime および endTime クエリ文字列パラメーターは省略可能です (なし、どちらか、または両方がサポートされています)。  

    * startTime を省略すると、記録の最初の年であると見なされます。
    * endTime を省略すると、記録の最後の年であると見なされます。
    * たとえば、記録が 2011 年に開始され、2020 年まで続いている場合は、次のようになります。

        * /availableMedia?precision=year は /availableMedia?precision=year&startTime=2011&endTime=2020 と同じ
        * /availableMedia?precision=year&startTime=2015 は /availableMedia?precision=year&startTime=2015&endTime=2020 と同じ
        * /availableMedia?precision=year&endTime=2018 は /availableMedia?precision=year&startTime=2011&endTime=2018 と同じ

その時間の範囲に使用できるメディア データがない場合は、空のリストが返されます。

アセットにメディア グラフの記録が含まれない場合は、HTTP 400 応答が返され、この機能はメディア グラフから録画されたコンテンツに対してのみ使用できることを示すエラー メッセージが表示されます。

パラメーターで指定した時間が、特定の種類のクエリの最大時間範囲で許容される時間より長い場合は、HTTP 400 が返され、要求したクエリの種類で許容される最大時間範囲を示すエラー メッセージが表示されます。

時間の範囲が、指定したパラメーターで有効であるなら、記録内のデータの時間枠以外のクエリに対してエラーは返されません。  つまり、録画が 7 時間前に開始され、{UtcNow –24 時間} から UtcNow までの間の利用可能なメディアを要求した場合、{UtcNow – 7} 時間分のデータが返されます。

### <a name="response-format"></a>応答形式  

availableMedia を呼び出すと、時刻値のセットが返されます。

応答は JSON 本文で、timeRanges 値は、要求した精度に応じて、ISO 8601 UTC 日付の年 (YYYY 形式)、月 (YYYY-MM 形式)、または日 (YYYY-MM-DD) の配列になります。  完全な timeRanges には、ISO 8601 UTC 日時 (YYYY-MM-DDThh:mm:ss.sssZ 形式) としてフォーマットされた開始値と終了値の両方が含まれます。

availableMedia クエリの場合、API により、ビデオのタイムラインからキーオフされます。 タイムライン内に不連続があると、応答内にギャップとして示されます。

#### <a name="response-example-for-availablemedia"></a>availableMedia の応答例

##### <a name="example-1-live-recording-with-no-gaps"></a>例 1:ギャップのないライブ記録

以下は、2019 年 12 月 21 日の利用可能なすべてのメディアを示す応答です。録画は 100% 完了しています。 応答には、開始/終了ペアが 1 つだけ含まれます。

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00.000Z", 
         "end":"2019-12-22T00:00:00.000Z"
    }
   ]
}
```

##### <a name="example-2-live-recording-with-a-2-second-gap"></a>例 2:2 秒のギャップがあるライブ記録

何らかの理由で、カメラが 1 日に 2 秒間有効なビデオを送信できなかったとします。 以下は、2019 年 12 月 21 日の利用可能なすべてのメディアを示す応答です。

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

##### <a name="example-3-live-recording-with-an-8-hour-gap"></a>例 3: 8 時間のギャップがあるライブ記録

カメラまたはオンプレミス施設で、1 日の間に UTC の午前 4 時から正午までの 8 時間電力が失われ、バックアップ電源がなかったとします。 以下は、このような日に対して利用可能なすべてのメディアを示す応答です

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

#### <a name="example-4-live-recording-where-no-video-is-recorded-over-summer-holidays"></a>例 4: 夏休み中にビデオが録画されないライブ記録

学期中のみビデオを録画し、6 月 17 日から 9 月 6 日まで録画が停止したとします。 利用可能な月のクエリは次のようになります。

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=month&startTime=2019-01&endTime=2019-12
{
   "timeRanges":[
    {
         "start":"2019-01", 
         "end":"2019-06"
    },
    {
         "start":"2019-09", 
         "end":"2019-12"
    }
   ]
}
```

6 月の利用可能な日を要求した場合は、次のように表示されます。

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2019-06-01&endTime=2019-06-30
{
   "timeRanges":[
    {
         "start":"2019-06-01", 
         "end":"2019-06-17"
    }
   ]
}
```

##### <a name="example-5-live-recording-where-no-video-is-recorded-over-weekends-or-holidays"></a>例 5:週末と休日にビデオが録画されないライブ記録

営業時間中にのみビデオを録画したとします。 利用可能な日のクエリは次のようになります

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=day&startTime=2020-02-01&endTime=2020-02-29
{
   "timeRanges":[
    {
         "start":"2020-02-03", 
         "end":"2020-02-07"
    },
    {
         "start":"2020-02-10", 
         "end":"2020-02-14"
    },
    {
         "start":"2020-02-18", // Monday Feb 17th was a holiday
         "end":"2020-02-21"
    },
    {
         "start":"2020-02-24", 
         "end":"2020-02-28"
    }
   ]
}
```

## <a name="time-range-filters"></a>時間の範囲フィルター

前述したように、このフィルターを使用すると、記録の一部 (たとえば、元旦の午前 9 時から午前 11 時) を選択することができます。 HLS を介してストリーミングする場合、ストリーミング URL は `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl).m3u8` のようになります。 記録の一部を選択するには、startTime および endTime パラメーターを追加します (例: `https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00Z,endTime=2019-12-21T10:00:00Z).m3u8`)。 このように、時間の範囲フィルターは URL 修飾子で、ストリーミング マニフェストに含まれる記録のタイムラインの部分を記述するために使用します。

* `starttime` は ISO 8601 DateTime 形式のスタンプで、返されたマニフェスト内のビデオ タイムラインの目的の開始時刻を記述します。
* `endtime` は ISO 8601 DateTime 形式のスタンプで、返されたマニフェスト内のビデオ タイムラインの目的の終了時刻を記述ます。

このようなマニフェストの最大長 (時間単位) は、24 時間を超えることはできません。

フィルターの制約を次に示します。

|startTime| endTime |結果|
|---|---|---|
|Absent |Absent |アセット内のビデオの最新部分が返されます。最大長は 4 時間です。<br/><br/>アセットがまだ書き込まれていない (新しいビデオ データが受信されていない) 場合は、オンデマンド (VoD) マニフェストが返されます。 それ以外の場合は、ライブ マニフェストが返されます。|
|存在|Absent|    このようなマニフェストが 24 時間より短い場合は、startTime より新しいビデオがあれば、そのマニフェストが返されます。<br/>マニフェストの長さが 24 時間を超えると、エラーが返されます。<br/>アセットがまだ書き込まれていない (新しいビデオ データが受信されていない) 場合は、オンデマンド (VoD) マニフェストが返されます。 それ以外の場合は、ライブ マニフェストが返されます。
|Absent|存在 |エラー – startTime が存在する場合、endTime は必須です。|
|存在|存在|startTime と endTime の間に利用可能なビデオがあれば、その VoD マニフェストが返されます。<br/>範囲 (startTime、endTime) が 24 時間を超えることはできません。|

### <a name="response-examples"></a>応答例  

#### <a name="example-1-live-recording-with-no-gaps"></a>例 1:ギャップのないライブ記録

以下は、2019 年 12 月 21 日の利用可能なすべてのメディアを示す応答です。録画は 100% 完了しています。 応答には、開始/終了ペアが 1 つだけ含まれます。

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
   {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

記録が連続している場合は、範囲が 24 時間以下であれば、その開始/終了ペア内の任意の時間部分に対して HLS または DASH マニフェストを要求できます。 上記の 4 時間の HLS マニフェスト要求の例を次に示します。

`GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:00:00.000Z,endTime=2019-12-21T18:00:00.000Z).m3u8`

#### <a name="example-2-live-recording-with-a-2-second-gap"></a>例 2:2 秒のギャップがあるライブ記録

何らかの理由で、カメラが 1 日に 2 秒間有効なビデオを送信できなかったとします。 以下は、2019 年 12 月 21 日の利用可能なメディアを示す応答です。

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:01:08Z"
    },
    {
         "start":"2019-12-21T04:01:10Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

上記のような記録の場合、範囲が 24 時間未満であれば、startTime/endTime ペアを使用して HLS マニフェストと DASH マニフェストを要求できます。 これらの値が 04:01:08AM UTC の時点でギャップをまたぐ場合、返されるマニフェストでは、これらのプロトコルの関連仕様に従って、メディア タイムラインの不連続が通知されます。

#### <a name="example-3-live-recording-with-an-8-hour-gap"></a>例 3: 8 時間のギャップがあるライブ記録

カメラまたはオンプレミス施設で、1 日の間に UTC の午前 4 時から正午までの 8 時間電力が失われ、バックアップ電源がなかったとします。 以下は、このような日に対して利用可能なすべてのメディアを示す応答です。

```
GET https://hostname/locatorId/content.ism/availableMedia?precision=full&startTime=2019-12-21T00:00:00Z&endTime=2019-12-22T00:00:00Z
{
   "timeRanges":[
    {
         "start":"2019-12-21T00:00:00Z", 
         "end":"2019-12-21T04:00:00Z"
    },
    {
         "start":"2019-12-21T12:00:00Z", 
         "end":"2019-12-22T00:00:00Z"
    }
   ]
}
```

次のように記録を要求した場合:

* startTime/endTime 範囲フィルターの両方がタイムラインの "利用可能な" 部分の内側に含まれるマニフェストを要求した場合 (午前 0 時から午前 4 時または正午から午前 0 時まで)、サービスにより、次のように、startTime から endTime までの時間をカバーするマニフェストが返されます。

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T14:01:00.000Z,endTime=2019-12-21T03:00:00.000Z).m3u8`
* startTime と endTime が利用不可時間帯の "隙間時間" の内側に含まれるマニフェストを要求した場合 (たとえば、UTC の午前 8 時から午前 10 時)、サービスは、アセット フィルターが空の結果を返す場合と同じように動作します。

    [この要求では、空の応答を受け取ります] `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T08:00:00.000Z,endTime=2019-12-21T10:00:00.000Z).m3u8`
* startTime または endTime の一方だけが "隙間時間" の内側に含まれるマニフェストを要求した場合、返されるマニフェストにはそのタイムスパンの部分のみが含まれます。 startTime または endTime の値が、最も近い有効な境界にスナップされます。 たとえば、午前 10 時から午後 1 時までの 3 時間のストリームを要求した場合、応答には正午から午後 1 時までの 1 時間の長さのメディアが含まれます

    `GET https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2019-12-21T10:00:00.000Z,endTime=2019-12-21T13:00:00.000Z).m3u8`
    
    返されるマニフェストでは、午前 10 時の開始を要求した場合でも、2019-12-21T12:00:00.000Z から開始されます。 プレーヤー プラグインを使用してビデオ管理システムを構築する場合は、これを視聴者に通知するように注意する必要があります。 通知されないと、視聴者は、再生タイムラインが、要求した午前 10 時ではなく、正午に始まった理由がわからず混乱することになります

## <a name="recording-and-playback-latencies"></a>記録と再生の待機時間

Live Video Analytics on IoT Edge を使用してアセットに録画する場合は、segmentLength プロパティを指定して、クラウドに録画されるまでのビデオの最小時間 (秒単位) を集計するようにモジュールに指示します。 たとえば、segmentLength を 300 に設定すると、モジュールは 5 分間の長さのビデオを累積してから 5 分間の "チャンク" を 1 つアップロードします。その後、次の 5 分間の累積モードに入り、またアップロードします。 segmentLength を長くすると、読み取りと書き込みの回数が segmentLength 秒ごとに 1 回とした場合の回数よりも少なくなるので、Azure Storage のトランザクション コストを削減できるという利点があります。

そのため、Media Services からのビデオのストリーミングは、少なくともその時間分遅くなります。 

再生の待機時間 (カメラの前でイベントが発生した時刻から、再生デバイスで表示できる時刻までの遅延) を決定するもう 1 つの要因が、[GOP](https://en.wikipedia.org/wiki/Group_of_pictures) (Group Of Pictures) 時間です。 [3 つの簡単な方法を使用してライブ ストリームの遅延を減らす](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641)ことで、GOP 時間が長くなるほど、待機時間も長くなります。 一般的に、監視やセキュリティのシナリオで使用される IP カメラは、30 秒よりも長い GOP を使用するように構成されています。 これは、全体の待機時間に大きな影響を与えます。

## <a name="next-steps"></a>次のステップ

[継続的なビデオ記録チュートリアル](continuous-video-recording-tutorial.md)
