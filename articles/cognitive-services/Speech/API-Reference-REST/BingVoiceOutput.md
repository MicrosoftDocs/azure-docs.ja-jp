---
title: Microsoft 音声サービスの Text to Speech API | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Text to Speech API を使用して、さまざまな音声と言語で、テキストから音声への変換をリアルタイムに提供します
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: priyar
ms.openlocfilehash: 61bd1879a4b1bf8281ac03c8254fb3d48c07a139
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215862"
---
# <a name="bing-text-to-speech-api"></a>Bing のテキスト読み上げ API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="Introduction"></a>はじめに

テキストは、クラウド サーバーで人の発する音声へと直ちに合成され、オーディオ ファイルとし返されます。アプリケーションは Bing のテキスト読み上げ API を使用して、このサーバーに HTTP 要求を送信できます。 この API は、多くの異なるコンテキストで使用し、さまざまな異なる声と言語で、リアルタイムのテキスト読み上げ変換を提供できます。

## <a name="VoiceSynReq"></a>音声合成の要求

### <a name="Subscription"></a>承認トークン

音声合成のすべての要求には、アクセス トークンとして JSON Web トークン (JWT) が必要です。 JWT アクセス トークンは、音声要求ヘッダーに入れられ、それを介して渡されます。 トークンの有効期限は 10 分間です。 有効な JWT アクセス トークンを取得するのに使用する API キーのサブスクライブと取得については、[Cognitive Services のサブスクリプション](https://azure.microsoft.com/try/cognitive-services/)についてのページを参照してください。

API キーは、トークン サービスに渡されます。 例: 

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

トークンへのアクセスに必要なヘッダー情報は次のとおりです。

Name| 形式 | 説明
----|----|----
Ocp-Apim-Subscription-Key | ASCII | Your subscription key (サブスクリプション キー)

トークン サービスは、JWT アクセス トークンを `text/plain` として返します。 次に、`Base64 access_token` としての JWT は、文字列 `Bearer` で始まる承認ヘッダーとして音声エンドポイントに渡されます。 例: 

`Authorization: Bearer [Base64 access_token]`

クライアントは、次のエンドポイントを使用して、テキスト読み上げサービスにアクセスする必要があります。

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>前述したようにサブスクリプション キーを含むアクセス トークンを取得するまで、このリンクは `403 Forbidden` 応答エラーを生成します。

### <a name="Http"></a>HTTP ヘッダー

次の表に、音声合成要求で使用される HTTP ヘッダーを示します。

ヘッダー |値 |説明
----|----|----
Content-Type | application/ssml+xml | 入力コンテンツの種類。
X-Microsoft-OutputFormat | **1.** ssml-16khz-16bit-mono-tts <br> **2.** raw-16khz-16bit-mono-pcm <br>**3.** audio-16khz-16kbps-mono-siren <br> **4.** riff-16khz-16kbps-mono-siren <br> **5.** riff-16khz-16bit-mono-pcm <br> **6.** audio-16khz-128kbitrate-mono-mp3 <br> **7.** audio-16khz-64kbitrate-mono-mp3 <br> **8.** audio-16khz-32kbitrate-mono-mp3 | 出力オーディオの形式。
X-Search-AppId | GUID (16 進数のみ、ダッシュなし) | クライアント アプリケーションを一意に識別する ID。 これはアプリのストア ID でもかまいません。 それが使用できない場合は、ユーザーがアプリケーションのために生成した ID でもかまいません。
X-Search-ClientID | GUID (16 進数のみ、ダッシュなし) | それぞれのインストールのアプリケーション インスタンスを一意に識別する ID。
User-Agent | アプリケーション名 | アプリケーション名は必須で、255 文字未満にする必要があります。
Authorization | 承認トークン |  <a href="#Subscription">承認トークン</a>についてのセクションを参照してください。

### <a name="InputParam"></a>入力パラメーター

Bing のテキスト読み上げ API に対する要求は、HTTP POST 呼び出しを使用して行います。 ヘッダーは前のセクションで指定されました。 本文には、合成するテキストを表す音声合成マークアップ言語 (SSML) の入力を含めます。 言語や話者の性別など、音声の各側面を制御するために使用するマークアップの説明については、[SSML の W3C 仕様](http://www.w3.org/TR/speech-synthesis/)を参照してください。

>[!NOTE]
>サポートされている SSML 入力の最大サイズは、すべてのタグを含めて 1,024 文字です。

###  <a name="SampleVoiceOR"></a>例: 音声出力の要求

音声出力の要求の例は、次のとおりです。

```HTTP
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: application/ssml+xml
Host: speech.platform.bing.com
Content-Length: 197
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```

## <a name="VoiceOutResponse"></a>音声出力の応答

Bing のテキスト読み上げ API では、HTTP POST を使用してオーディオをクライアントに返送します。 API 応答には、オーディオ ストリームとコーデックが含まれていて、要求された出力形式と一致しています。 特定の要求に対して返されるオーディオは、15 秒を超えてはいけません。

### <a name="SuccessfulRecResponse"></a>例: 成功した合成の応答

次のコードは、成功した音声合成要求に対する JSON 応答の例です。 コメントやコードの書式設定は、この例の説明用で、実際の応答にはありません。

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>例: 合成の失敗

次のコード例は、音声合成クエリの失敗に対する JSON 応答を示しています。

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>エラー応答

Error | 説明
----|----
HTTP/400 Bad Request | 必須パラメーターがないか、空か、または null です。または、必須またはオプションのパラメーターに渡された値が無効です。 "無効" な応答を取得することになる理由の 1 つは、許容される長さより長い文字列値を渡したことです。 問題のあるパラメーターの簡単な説明が含まれています。
HTTP/401 Unauthorized | 要求が承認されていません。
HTTP/413 RequestEntityTooLarge  | SSML 入力が、サポートされているより大きいです。
HTTP/502 BadGateway | ネットワーク関連の問題またはサーバー側の問題があります。

エラー応答の例は次のとおりです。

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>SSML による音声出力の変更

Microsoft Text-to-Speech API は、W3C の[音声合成マークアップ言語 (SSML) バージョン 1.0](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/) で定義されているとおり SSML 1.0 をサポートしています。 このセクションでは、読み上げ速度や発音など、生成される音声出力の一定の特性を、SSML タグを使用して変更する例を示します。

1. 中断を追加する

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
  ```

2. 読み上げ速度を変更する

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

3. 発音

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
  ```

4. 音量を変更する

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

5. ピッチを変更する

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

6. 韻律の音調曲線を変更する

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
  ```

> [!NOTE]
> オーディオ データは、次の形式で作成された 8 k または 16 k の wav ファイルにする必要があります: **CRC コード** (CRC- 32): 4 バイト (DWORD)、有効範囲は 0x00000000 から 0xFFFFFFF。**オーディオ形式フラグ**: 4 バイト (DWORD)、有効範囲は 0x00000000 から 0xFFFFFFFF。**サンプル数**: 4 バイト (DWORD)、有効範囲は 0x00000000 から 0x7FFFFFFF。**バイナリ本文のサイズ**: 4 バイト (DWORD)、有効範囲は 0x00000000 から 0x7FFFFFFF。**バイナリ本文**: n バイト。

## <a name="SampleApp"></a>サンプル アプリケーション

実装の詳細については、[Visual C#.NET のテキスト読み上げサンプル アプリケーション](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs)を参照してください。

## <a name="SupLocales"></a>サポート対象のロケールと音声フォント

次の表に、サポート対象のロケールと関連する音声フォントの一部を示します。

ロケール | 性別 | サービス名のマッピング
---------|--------|------------
ar-EG* | 女性 | "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)"
ar-SA | 男性 | "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)"
bg-BG | 男性 | "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)"
ca-ES | 女性 | "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)"
cs-CZ | 男性 | "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)"
da-DK | 女性 | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)"
de-AT | 男性 | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)"
de-CH | 男性 | "Microsoft Server Speech Text to Speech Voice (de-CH, Karsten)"
de-DE | 女性 | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda) "
de-DE | 女性 | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)"
de-DE | 男性 | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo) "
el-GR | 男性 | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)"
en-AU | 女性 | "Microsoft Server Speech Text to Speech Voice (en-AU, Catherine) "
en-AU | 女性 | "Microsoft Server Speech Text to Speech Voice (en-AU, HayleyRUS)"
en-CA | 女性 | "Microsoft Server Speech Text to Speech Voice (en-CA, Linda)"
en-CA | 女性 | "Microsoft Server Speech Text to Speech Voice (en-CA, HeatherRUS)"
en-GB | 女性 | "Microsoft Server Speech Text to Speech Voice (en-GB, Susan, Apollo)"
en-GB | 女性 | "Microsoft Server Speech Text to Speech Voice (en-GB, HazelRUS)"
en-GB | 男性 | "Microsoft Server Speech Text to Speech Voice (en-GB, George, Apollo)"
en-IE | 男性 | "Microsoft Server Speech Text to Speech Voice (en-IE, Sean)"
en-IN | 女性 | "Microsoft Server Speech Text to Speech Voice (en-IN, Heera, Apollo)"
en-IN | 女性 | "Microsoft Server Speech Text to Speech Voice (en-IN, PriyaRUS)"
en-IN | 男性 | "Microsoft Server Speech Text to Speech Voice (en-IN, Ravi, Apollo)"
en-US | 女性 | "Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)"
en-US | 女性 | "Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)"
en-US | 男性 | "Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)"
es-ES | 女性 | "Microsoft Server Speech Text to Speech Voice (es-ES, Laura, Apollo)"
es-ES | 女性 | "Microsoft Server Speech Text to Speech Voice (es-ES, HelenaRUS)"
es-ES | 男性 | "Microsoft Server Speech Text to Speech Voice (es-ES, Pablo, Apollo)"
es-MX | 女性 | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)"
es-MX | 男性 | "Microsoft Server Speech Text to Speech Voice (es-MX, Raul, Apollo)"
fi-FI | 女性 | "Microsoft Server Speech Text to Speech Voice (fi-FI, HeidiRUS)"
fr-CA | 女性 | "Microsoft Server Speech Text to Speech Voice (fr-CA, Caroline)"
fr-CA | 女性 | "Microsoft Server Speech Text to Speech Voice (fr-CA, HarmonieRUS)"
fr-CH | 男性 | "Microsoft Server Speech Text to Speech Voice (fr-CH, Guillaume)"
fr-FR | 女性 | "Microsoft Server Speech Text to Speech Voice (fr-FR, Julie, Apollo)"
fr-FR | 女性 | "Microsoft Server Speech Text to Speech Voice (fr-FR, HortenseRUS)"
fr-FR | 男性 | "Microsoft Server Speech Text to Speech Voice (fr-FR, Paul, Apollo)"
he-IL| 男性| "Microsoft Server Speech Text to Speech Voice (he-IL, Asaf)"
hi-IN | 女性 | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana, Apollo)"
hi-IN | 女性 | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana)"
hi-IN | 男性 | "Microsoft Server Speech Text to Speech Voice (hi-IN, Hemant)"
hr-HR | 男性 | "Microsoft Server Speech Text to Speech Voice (hr-HR, Matej)"
hu-HU | 男性 | "Microsoft Server Speech Text to Speech Voice (hu-HU, Szabolcs)"
id-ID | 男性 | "Microsoft Server Speech Text to Speech Voice (id-ID, Andika)"
it-IT | 男性 | "Microsoft Server Speech Text to Speech Voice (it-IT, Cosimo, Apollo)"
it-IT | 女性 | "Microsoft Server Speech Text to Speech Voice (it-IT, LuciaRUS)"
ja-JP | 女性 | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)"
ja-JP | 男性 | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)"
ja-JP | 女性 | "Microsoft Server Speech Text to Speech Voice (ja-JP, HarukaRUS)"
ko-KR | 女性 | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)"
ms-MY | 男性 | "Microsoft Server Speech Text to Speech Voice (ms-MY, Rizwan)"
nb-NO | 女性 | "Microsoft Server Speech Text to Speech Voice (nb-NO, HuldaRUS)"
nl-NL | 女性 | "Microsoft Server Speech Text to Speech Voice (nl-NL, HannaRUS)"
pl-PL | 女性 | "Microsoft Server Speech Text to Speech Voice (pl-PL, PaulinaRUS)"
pt-BR | 女性 | "Microsoft Server Speech Text to Speech Voice (pt-BR, HeloisaRUS)"
pt-BR | 男性 | "Microsoft Server Speech Text to Speech Voice (pt-BR, Daniel, Apollo)"
pt-PT | 女性 | "Microsoft Server Speech Text to Speech Voice (pt-PT, HeliaRUS)"
ro-RO | 男性 | "Microsoft Server Speech Text to Speech Voice (ro-RO, Andrei)"
ru-RU | 女性 | "Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)"
ru-RU | 男性 | "Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)"
ru-RU | 女性 | "Microsoft Server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)"
sk-SK | 男性 | "Microsoft Server Speech Text to Speech Voice (sk-SK, Filip)"
sl-SI | 男性 | "Microsoft Server Speech Text to Speech Voice (sl-SI, Lado)"
sv-SE | 女性 | "Microsoft Server Speech Text to Speech Voice (sv-SE, HedvigRUS)"
ta-IN | 男性 | "Microsoft Server Speech Text to Speech Voice (ta-IN, Valluvar)"
th-TH | 男性 | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)"
tr-TR | 女性 | "Microsoft Server Speech Text to Speech Voice (tr-TR, SedaRUS)"
vi-VN | 男性 | "Microsoft Server Speech Text to Speech Voice (vi-VN, An)"
zh-CN | 女性 | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)"
zh-CN | 女性 | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)"
zh-CN | 男性 | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)"
zh-HK | 女性 | "Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)"
zh-HK | 女性 | "Microsoft Server Speech Text to Speech Voice (zh-HK, TracyRUS)"
zh-HK | 男性 | "Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)"
zh-TW | 女性 | "Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)"
zh-TW | 女性 | "Microsoft Server Speech Text to Speech Voice (zh-TW, HanHanRUS)"
zh-TW | 男性 | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)"
 *ar-EG では現代標準アラビア語 (MSA) をサポートしています。

> [!NOTE]
> 以前のサービス名の **Microsoft Server Speech Text to Speech Voice (cs-CZ, Vit)** と **Microsoft Server Speech Text to Speech Voice (en-IE, Shaun)** は、Bing Speech API の機能を最適化するため、2018 年 3 月 31 日を過ぎると非推奨となる予定であることに注意してください。 更新後の名前でコードを更新してください。

## <a name="TrouNSupport"></a>トラブルシューティングとサポート

質問や問題はすべて、MSDN の [Bing Speech Service](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService) フォーラムに投稿してください。 以下のような完全な詳細情報を含めてください。

* 要求文字列全体の例。
* 該当する場合は、ログ ID を含む、失敗した要求の出力全体。
* 失敗している要求の割合。
