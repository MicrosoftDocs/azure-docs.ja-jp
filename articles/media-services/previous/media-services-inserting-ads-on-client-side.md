---
title: クライアント側での広告の挿入 | Microsoft Docs
description: この記事では、クライアント側でメディアに広告を挿入する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 2d942099d0abbdfc4ddfa0276184500166250728
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014963"
---
# <a name="inserting-ads-on-the-client-side"></a>クライアント側での広告の挿入

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

この記事では、クライアント側でさまざまな種類の広告を挿入する方法について説明します。

ライブ ストリーミング ビデオでのクローズド キャプションと広告のサポートの詳細については、「 [サポートされる字幕と広告挿入の標準](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads)」をご覧ください。

> [!NOTE]
> 現在、Azure Media Player は広告をサポートしていません。
> 
> 

## <a name="inserting-ads-into-your-media"></a><a id="insert_ads_into_media"></a>メディアへの広告の挿入
Azure Media Services では、Windows メディア プラットフォームのプレーヤー フレームワークを通じて広告の挿入がサポートされています。 広告がサポートされるプレーヤー フレームワークは、Windows 8、Silverlight、Windows Phone 8、iOS デバイスで使用できます。 各プレーヤー フレームワークには、プレーヤー アプリケーションの実装方法を示すサンプル コードが含まれています。 media:list には、3 種類の広告を挿入できます。

* **線形** – メイン ビデオを一時停止するフル フレーム広告
* **非線形** – メイン ビデオの再生中に表示されるオーバーレイ広告 (通常は、プレーヤー内に配置されるロゴや他の静的イメージ)
* **コンパニオン** – プレーヤーの外部に表示される広告

広告は、メイン ビデオの時系列のどのポイントにも配置できます。 広告をいつ再生し、どの広告を再生するかをプレーヤーに通知する必要があります。 これは、一連の標準 XML ベース ファイルである Video Ad Service Template (VAST)、Digital Video Multiple Ad Playlist (VMAP)、Media Abstract Sequencing Template (MAST)、Digital Video Player Ad Interface Definition (VPAID) を使用して行います。 VAST ファイルは、表示する広告を指定します。 VMAP ファイルは、各広告をいつ再生するかを指定します。このファイルには VAST XML が含まれています。 MAST ファイルを使用して広告の順序を指定することもできます。このファイルにも VAST XML が含まれています。 VPAID ファイルは、ビデオ プレーヤーと広告や広告サーバーの間のインターフェイスを定義します。

各プレーヤー フレームワークの動作は異なるため、それぞれ別の記事で説明します。 この記事では、広告の挿入に使用される基本的なメカニズムについて説明します。 ビデオ プレーヤー アプリケーションは、広告サーバーに広告を要求します。 広告サーバーは、さまざまな方法で対応する可能性があります。

* VAST ファイルを返す
* VMAP ファイルを返す (VAST が埋め込まれています)
* MAST ファイルを返す (VAST が埋め込まれています)
* VPAID 広告を含む VAST ファイルを返す

### <a name="using-a-video-ad-service-template-vast-file"></a>Video Ad Service Template (VAST) ファイルの使用
VAST ファイルは、表示する広告を指定します。 次の XML に、線形広告の VAST ファイルの例を示します。

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
```

線形広告は、<**Linear**> 要素で記述されます。 この要素は、広告の期間、追跡イベント、クリック スルー、クリック追跡、多くの **MediaFile** 要素を指定します。 追跡イベントは、<**TrackingEvents**> 要素内で指定され、広告サーバーが広告の表示中に発生するさまざまなイベントを追跡できるようにします。 この場合、開始、中間、完了、拡大の各イベントが追跡されます。 開始イベントは、広告が表示されたときに発生します。 中間イベントは、広告の時系列が 50% 以上表示されたときに発生します。 完了イベントは、広告が最後まで実行されたときに発生します。 拡大イベントは、ユーザーがビデオ プレーヤーをフル スクリーンに拡大したときに表示されます。 Clickthroughs は、<**VideoClicks**> 要素内の <**ClickThrough**> 要素で指定され、ユーザーが広告をクリックしたときに表示するリソースに対する URI を指定します。 ClickTracking は、<**ClickTracking**> 要素と <**VideoClicks**> 要素内で指定され、ユーザーが広告をクリックしたときに要求する追跡リソースをプレーヤーに対して指定します。 <**MediaFile**> 要素は、広告の特定のエンコードに関する情報を指定します。 複数の <**MediaFile**> 要素がある場合、ビデオ プレーヤーはプラットフォームに最適なエンコードを選択できます。

線形広告は、指定された順序で表示できます。 これを行うには、VAST ファイルに `<Ad>` 要素を追加し、sequence 属性を使用して順序を指定します。 次の例を使って説明します。

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
```

非線形広告は、`<Creative>` 要素でも指定されます。 次の例は、非線形広告を記述する `<Creative>` 要素を示しています。

```xml
    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>
```

<**NonLinearAds**> 要素には 1 つ以上の <**NonLinear**> 要素が含まれており、それぞれ非線形広告を記述できます。 <**NonLinear**> 要素は、非線形広告のリソースを指定します。 このリソースは、<**StaticResource**>、<**IFrameResource**>、または <**HTMLResource**> です。 \<**StaticResource**> は、HTML 以外のリソースを記述し、次のようなリソースの表示方法を指定する creativeType 属性を定義します。

Image/gif、image/jpeg、image/png – リソースが HTML <**img**> タグで表示されます。

Application/x-javascript – リソースが HTML <**script**> タグで表示されます。

Application/x-shockwave-flash – リソースが Flash プレーヤーで表示されます。

**IFrameResource** は、IFrame で表示可能な HTML リソースを記述します。 **HTMLResource** は、Web ページに挿入可能な HTML コードを記述します。 **TrackingEvents** は、追跡イベントと、イベント発生時に要求する URI を指定します。 このサンプルでは、acceptInvitation イベントと collapse イベントが追跡されます。 **NonLinearAds** 要素とその子の詳細については、「IAB.NET/VAST」を参照してください。 **TrackingEvents** 要素は、**NonLinear** 要素ではなく **NonLinearAds** 要素にある点に注意してください。

コンパニオン広告は、`<CompanionAds>` 要素内で定義されます。 `<CompanionAds>` 要素には、1 つ以上の `<Companion>` 要素を含めることができます。 各 `<Companion>` 要素は、コンパニオン広告を記述します。また、非線形広告でも同じように指定される `<StaticResource>`、`<IFrameResource>`、`<HTMLResource>` を含めることができます。 VAST ファイルには、複数のコンパニオン広告を含めることができ、プレーヤー アプリケーションは表示する最適な広告を選択できます。 VAST の詳細については、「[VAST 3.0](https://www.iab.net/media/file/VASTv3.0.pdf)」をご覧ください。

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Digital Video Multiple Ad Playlist (VMAP) ファイルの使用
VMAP ファイルを使用すると、広告の中断がいつ発生するか、各中断の長さ、中断中に表示できる広告の数、中断中に表示できる広告の種類を指定できます。 1 つの広告の中断を定義する VMAP ファイルの例を次に示します。

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[https://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scalable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

VMAP ファイルの先頭は、1 つ以上の `<AdBreak>` 要素 (それぞれ広告の中断を定義) を含む `<VMAP>` 要素になっています。 各広告の中断は、中断の種類、中断 ID、時間オフセットを指定します。 BreakType 属性は、中断中に再生できる広告の種類 (線形、非線形、表示) を指定します。 表示広告は、VAST のコンパニオン広告に対応します。 コンマ (スペースなし) 区切りリストで複数の広告の種類を指定できます。 breakID は、広告のオプションの識別子です。 timeOffset は、広告をいつ表示するかを指定します。 これは、次のいずれかの方法で指定できます。

1. 時間 – hh:mm:ss または hh:mm:ss.mmm 形式。ここで、.mmm はミリ秒です。 この属性の値は、ビデオの時系列の先頭から広告の中断の先頭までの時間を指定します。
2. パーセント – n% 形式。ここで、n は広告再生前に再生するビデオの時系列のパーセントです。
3. 開始/終了 – ビデオを表示する前または後に広告を表示することを指定します。
4. 位置 – 広告の中断のタイミングが不明なとき (ライブ ストリーミングなど) に、広告の中断の順序を指定します。 各広告の中断の順序は、#n 形式で指定されます。n は整数 1 以上です。 1 は、広告を最初のチャンスに再生することを意味し、2 は広告を 2 番目のチャンスに再生することを意味し、それ以降も同様です。

`<AdBreak>` 要素内には、<**AdSource**> 要素を 1 つ配置できます。 <**AdSource**> 要素には、次の属性が含まれています。

1. Id – 広告ソースの識別子を指定します。
2. allowMultipleAds – 広告の中断中に複数の広告を表示できるかどうかを指定するブール値。
3. followRedirects – ビデオ プレーヤーが広告応答内でリダイレクトを扱うかどうかを指定するオプションのブール値。

<**AdSource**> 要素は、インライン広告応答または広告応答への参照をプレーヤーに提供します。 これには、次のいずれかの要素を含めることができます。

* `<VASTAdData>` - VAST 広告応答が VMAP ファイル内に埋め込まれていることを示します。
* `<AdTagURI>` - 別のシステムからの広告応答を参照する URI。
* `<CustomAdData>` - VAST 以外の応答を表す任意の文字列。

この例では、VAST 広告応答が含まれる `<VASTAdData>` 要素を使用してインライン広告応答が指定されます。 他の要素の詳細については、「 [VMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap)」をご覧ください。

<**AdBreak**> 要素には、1 つの <**TrackingEvents**> 要素を含めることもできます。 <**TrackingEvents**> 要素を使用すると、広告の中断の開始と終了を追跡したり、広告の中断中にエラーが発生したかどうかを追跡することができます。 <**TrackingEvents**> 要素には、1 つ以上の <**Tracking**> 要素が含まれており、それぞれ追跡イベントと追跡 URI を指定します。 指定できる追跡イベントは、次のとおりです。

1. breakStart – 広告の中断の開始を追跡します。
2. breakEnd – 広告の中断の完了を追跡します。
3. error – 広告の中断中に発生したエラーを追跡します。

次の例は、追跡イベントを指定する VMAP ファイルを示しています。

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

<**TrackingEvents**> 要素とその子の詳細については、 http://iab.net/VMAP.pdf を参照してください。

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Media Abstract Sequencing Template (MAST) ファイルの使用
MAST ファイルを使用すると、広告がいつ表示されるかを定義するトリガーを指定できます。 プリロール広告、ミッドロール広告、ポストロール広告のトリガーを含む MAST ファイルの例を次に示します。

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>
```


MAST ファイルの先頭は、**triggers** 要素が 1 つ含まれる **MAST** 要素です。 `<triggers>` 要素には、広告をいつ再生するかを定義する 1 つ以上の **trigger** 要素が含まれています。

**trigger** 要素には、広告の再生をいつ開始するかを指定する **startConditions** 要素が含まれています。 **startConditions** 要素には、1 つ以上の `<condition>` 要素が含まれています。 各 `<condition>` が true と評価された場合、`<condition>` が **startConditions** 要素または **endConditions** 要素のどちらに含まれているかに応じて、トリガーが開始されるか取り消されます。 複数の `<condition>` 要素が存在する場合、それらは暗黙的な OR として扱われるため、いずれかの条件が true と評価されるとトリガーが開始されます。 `<condition>` 要素はネストできます。 子の `<condition>` 要素がプリセットされている場合、それらは暗黙的な AND として扱われるため、トリガーが開始されるにはすべての条件が true と評価される必要があります。 `<condition>` 要素には、条件を定義する次の属性が含まれています。

1. **type** – 条件、イベント、またはプロパティの種類を指定します
2. **name** – 評価時に使用するイベントまたはプロパティの名前。
3. **value** – プロパティが評価される値。
4. **operator** – 評価時に使用する演算。EQ (等しい)、NEQ (等しくない)、GTR (より大きい)、GEQ (以上)、LT (より小さい)、LEQ (以下)、MOD (モジュロ) です。

**endConditions** には `<condition>` 要素を含めることもできます。 条件が true と表示された場合、トリガーはリセットされます。 `<trigger>` 要素には、1 つ以上の `<source>` 要素が含まれる `<sources>` 要素を含めることもできます。 `<source>` 要素は、広告応答への URI と広告応答の種類を定義します。 次の例では、VAST 応答への URI が指定されています。

```xml
    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
```

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Video Player Ad Interface Definition (VPAID) の使用
VPAID は、実行可能広告ユニットがビデオ プレーヤーとやり取りできるようにするための API です。 これにより、インタラクティブ性の高い広告の操作性が実現します。 ユーザーは、広告を操作でき、広告はビューアーにより実行されたアクションに応答できます。 たとえば、ユーザーが広告の詳細情報やロング バージョンを参照できるボタンを広告に表示することができます。 ビデオ プレーヤーで VPAID API がサポートされていて、実行可能広告に API が実装されている必要があります。 プレーヤーが広告サーバーから広告を要求すると、サーバーは VPAID 広告を含む VAST 応答で応答できます。

実行可能広告は、Web ブラウザーで実行可能な Adobe Flash™ や JavaScript などのランタイム環境で実行する必要があるコードで作成されます。 広告サーバーが VPAID 広告を含む VAST 応答を返す場合、`<MediaFile>` 要素内の apiFramework 属性の値は "VPAID" にする必要があります。 この属性は、含まれている広告が VPAID 実行可能広告であることを指定します。 type 属性は、実行可能ファイルの MIME タイプ ("application/x-shockwave-flash" や "application/x-javascript" など) に設定する必要があります。 次の XML スニペットは、VPAID 実行可能広告を含む VAST 応答からの `<MediaFile>` 要素を示しています。

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type="application/x-shockwaveflash"
                  width="640" height="480" apiFramework="VPAID">
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

実行可能広告は、VAST 応答で `<Linear>` 要素または `<NonLinear>` 要素内の `<AdParameters>` 要素を使用して初期化できます。 `<AdParameters>` 要素の詳細については、「[VAST 3.0](https://www.iab.net/media/file/VASTv3.0.pdf)」をご覧ください。 Graph API の詳細については、「 [VPAID 2.0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf)」をご覧ください。

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>広告をサポートする Windows または Windows Phone 8 プレーヤーの実装
Microsoft Media Platform: Player Framework for Windows 8 and Windows Phone 8 には、このフレームワークを使用してビデオ プレーヤー アプリケーションを実装する方法を示すサンプル アプリケーションのコレクションが含まれています。 Player Framework とサンプルは、 [Player Framework for Windows 8 and Windows Phone 8](https://playerframework.codeplex.com)サイトからダウンロードできます。

Microsoft.PlayerFramework.Xaml.Samples ソリューションを開くと、多数のフォルダーがプロジェクト内に表示されます。 Advertising フォルダーには、広告をサポートするビデオ プレーヤーの作成に関連するサンプル コードが含まれています。 Advertising フォルダーには、広告を挿入するさまざまな方法を示した XAML/CS ファイルが含まれています。 次の一覧で、これらのファイルについて説明します。

* AdPodPage.xaml は、広告ポッドの表示方法を示します。
* AdSchedulingPage.xaml は、広告のスケジュールを設定する方法を示します。
* FreeWheelPage.xaml は、FreeWheel プラグインを使用して広告のスケジュールを設定する方法を示します。
* MastPage.xaml は、MAST ファイルを使用して広告のスケジュールを設定する方法を示します。
* ProgrammaticAdPage.xaml は、プログラムによりビデオ内への広告挿入のスケジュールを設定する方法を示します。
* ScheduleClipPage.xaml は、VAST ファイルを使用せずに広告のスケジュールを設定する方法を示します。
* VastLinearCompanionPage.xaml は、線形広告とコンパニオン広告を挿入する方法を示します。
* VastNonLinearPage.xaml は、非線形広告を挿入する方法を示します。
* VmapPage.xaml は、VMAP ファイルを使用して広告を指定する方法を示します。

これらの各サンプルでは、プレーヤー フレームワークで定義されている MediaPlayer クラスを使用します。 また、ほとんどのサンプルで、さまざまな広告応答形式のサポートを追加するプラグインを使用します。 ProgrammaticAdPage サンプルでは、MediaPlayer インスタンスをプログラムから操作します。

### <a name="adpodpage-sample"></a>AdPodPage サンプル
以下は、AdSchedulerPlugin を使用して、広告を表示するタイミングを定義するサンプルです。 この例では、ミッドロール広告が 5 秒後に再生されようにスケジュールを設定します。 広告ポッド (順番に表示する広告のグループ) は、広告サーバーから返される VAST ファイルで指定されます。 VAST ファイルの URI は、`<RemoteAdSource>` 要素で指定します。

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>
```

AdSchedulerPlugin の詳細については、「 [Advertising in the Player Framework on Windows 8 and Windows Phone 8 (Windows 8 および Windows Phone 8 のプレーヤー フレームワーク内の広告)](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
このサンプルでも、AdSchedulerPlugin を使用します。 このサンプルでは、プレロール広告、ミッドロール広告、ポストロール広告の 3 つ広告のスケジュールを設定します。 各広告の VAST への URI は、`<RemoteAdSource>` 要素で指定します。

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="freewheelpage"></a>FreeWheelPage
このサンプルでは、FreeWheelPlugin を使用して Source 属性を指定し、この属性で指定する URI で広告のコンテンツと広告のスケジュールを指定する SmartXML ファイルをポイントします。

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
このサンプルでは、MastSchedulerPlugin を使用して、MAST ファイルを使用できるようにします。 Source 属性は、MAST ファイルの場所を指定します。
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
このサンプルでは、MediaPlayer をプログラムから操作します。 ProgrammaticAdPage.xaml ファイルによって、media Player がインスタンス化されます。

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

ProgrammaticAdPage.xaml.cs ファイルは、AdHandlerPlugin を作成し、TimelineMarker を追加して広告を表示するタイミングを指定して、MarkerReached イベントのハンドラーを追加します。このハンドラーによって VAST ファイルの URI を指定する RemoteAdSource が読み込まれ、広告が再生されます。

```csharp
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }
```

### <a name="scheduleclippage"></a>ScheduleClipPage
このサンプルでは、AdSchedulerPlugin を使用して、広告が含まれている .wmv ファイルを指定することでミッドロール広告のスケジュールを設定します。

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage
このサンプルには、AdSchedulerPlugin を使用して、ミッドロールの線形広告とコンパニオン広告のスケジュールを設定する方法を示しています。 `<RemoteAdSource>` 要素で、VAST ファイルの場所を指定します。

```xml
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
このサンプルは、AdSchedulerPlugin を使用して、線形広告と非線形広告のスケジュールを指定します。 VAST ファイルの場所は、`<RemoteAdSource>` 要素で指定します。

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vmappage"></a>VMAPPage
このサンプルでは、VmapSchedulerPlugin を使用して、VMAP ファイルを使用する広告のスケジュールを設定します。 VMAP ファイルへの URI は、`<VmapSchedulerPlugin>` 要素の Source 属性で指定します。

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>広告をサポートする IOS ビデオ プレーヤーの実装
Microsoft Media Platform: Player Framework for iOS には、このフレームワークを使用してビデオ プレーヤー アプリケーションを実装する方法を示すサンプル アプリケーションのコレクションが含まれています。 Player Framework とサンプルは、 [Azure Media Player Framework](https://github.com/CloudMetal/azure-media-player-framework)からダウンロードできます。 GitHub ページには、Player Framework に関する追加情報とプレーヤー サンプルの概要が記載された Wiki へのリンク ([Azure Media Player Wiki](https://github.com/CloudMetal/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework)) があります。

### <a name="scheduling-ads-with-vmap"></a>VMAP を使用した広告のスケジュール設定
次のサンプルは、VMAP ファイルを使用して広告のスケジュールを設定する方法を示しています。

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }
            }
```

### <a name="scheduling-ads-with-vast"></a>VAST を使用した広告のスケジュール設定
次のサンプルは、遅延バインディング VAST 広告のスケジュールを設定する方法を示しています。


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

   次のサンプルは、事前バインディング VAST 広告のスケジュールを設定する方法を示しています。

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
    {
        [self logFrameworkError];
    }
    else
    {
        adLinearTime.startTime = 7;
        adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }
```

次のサンプルは、ラフカット編集 (RCE) を使用した広告の挿入方法を示しています。

```csharp
    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }
```

次のサンプルは、広告ポッドの作成方法を示しています。

```csharp
    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

次のサンプルは、非固定のミッドロール広告のスケジュールを設定する方法を示しています。 非固定の広告は、視聴者のシーク操作にかかわらず、一度のみ再生されます。

```csharp
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

次のサンプルは、固定のミッドロール広告のスケジュールを設定する方法を示しています。 固定の広告は、ビデオのタイムライン内の指定ポイントに達したときに毎回表示されます。

```csharp
    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

次のサンプルは、ポストロール広告のスケジュールを設定する方法を示しています。

```csharp
    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

次のサンプルは、プレロール広告のスケジュールを設定する方法を示しています。

```csharp
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

次のサンプルは、ミッドロールのオーバーレイ広告のスケジュールを設定する方法を示しています。

```csharp
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```


## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
