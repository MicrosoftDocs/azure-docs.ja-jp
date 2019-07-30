---
title: Bing Speech の WebSocket プロトコル | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: WebSocket に基づく Bing Speech のプロトコル ドキュメント
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d6601f57d87b518b2061df64174818432b822755
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515320"
---
# <a name="bing-speech-websocket-protocol"></a>Bing Speech の WebSocket プロトコル

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing Speech サービスは、発話された音声をテキストに変換するためのきわめて高度なアルゴリズムを採用した、クラウドベースのプラットフォームです。 Bing Speech プロトコルは、クライアント アプリケーションとサービスの間の[接続設定](#connection-establishment)や、双方の間でやりとりされる音声認識メッセージ ([クライアント発のメッセージ](#client-originated-messages)と[サービス発のメッセージ](#service-originated-messages)) を定義したものです。 なお、この記事では、[テレメトリ メッセージ](#telemetry-schema)と[エラー処理](#error-handling)についても説明しています。

## <a name="connection-establishment"></a>接続の確立

Speech Service プロトコルは、WebSocket 標準仕様 [IETF RFC 6455](https://tools.ietf.org/html/rfc6455) に準拠しています。 WebSocket 接続は、HTTP ヘッダーを含んだ HTTP 要求として開始されます。これらのヘッダーは、クライアントが HTTP セマンティクスを使用せず、接続を WebSocket にアップグレードしようとしていることを示します。 サーバーは、HTTPの `101 Switching Protocols` 応答を返すことで、WebSocket 接続に参加する意思を示します。 このハンドシェイクが交わされた後、クライアントとサービスの双方がソケットを開放し、メッセージ ベースのプロトコルの使用を開始して、情報を送受信します。

WebSocket のハンドシェイクを開始するため、クライアント アプリケーションはサービスに HTTPS GET 要求を送信します。 これには、標準の WebSocket アップグレード ヘッダーと、音声認識に固有の、その他のヘッダーが含まれます。

```HTTP
GET /speech/recognition/interactive/cognitiveservices/v1 HTTP/1.1
Host: speech.platform.bing.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Authorization: t=EwCIAgALBAAUWkziSCJKS1VkhugDegv7L0eAAJqBYKKTzpPZOeGk7RfZmdBhYY28jl&p=
X-ConnectionId: A140CAF92F71469FA41C72C7B5849253
Origin: https://speech.platform.bing.com
```

サービスの応答は次のようになります。

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

音声認識要求では、[TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) 暗号化が必須となります。 暗号化されていない音声認識要求の使用はサポートされていません。 サポートされている TLS バージョンは次のとおりです。

* TLS 1.2

### <a name="connection-identifier"></a>接続識別子

Speech Service では、すべてのクライアントに、接続を識別する一意の ID が必要となります。 クライアントは、WebSocket ハンドシェイクを開始する際、*X ConnectionId* ヘッダーを含める*必要があります*。 *X ConnectionId* ヘッダーの値は、[ユニバーサル一意識別子](https://en.wikipedia.org/wiki/Universally_unique_identifier) (UUID) である必要があります。 WebSocket アップグレード要求に *X ConnectionId* がない場合や、*X ConnectionId* ヘッダーの値が指定されていない場合、または有効な UUID 値が含まれていない場合は、要求がサービスから拒否され、`400 Bad Request` 応答が返されます。

### <a name="authorization"></a>Authorization

音声認識要求では、標準の WebSocket ハンドシェイク ヘッダーに加えて、*Authorization* ヘッダーも必須となります。 このヘッダーがない接続要求はサービスから拒否され、HTTP の `403 Forbidden` 応答が返されます。

*Authorization* ヘッダーには、JSON Web トークン (JWT) アクセス トークンが含まれている必要があります。

有効な JWT アクセス トークンを取得するために使用される API キーのサブスクライブ方法と取得方法については、[Cognitive Services のサブスクリプション](https://azure.microsoft.com/try/cognitive-services/)に関するページをご覧ください。

API キーは、トークン サービスに渡されます。 例:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

トークン アクセスでは、次のヘッダー情報が必要になります。

| Name | 形式 | 説明 |
|----|----|----|
| Ocp-Apim-Subscription-Key | ASCII | Your subscription key (サブスクリプション キー) |

トークン サービスは、JWT アクセス トークンを `text/plain` として返します。 その後 JWT は、文字列 `Bearer` のプレフィックスが付いた *Authorization* ヘッダーで、`Base64 access_token` としてハンドシェイクに渡されます。 例:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Cookie

クライアントは、[RFC 6265](https://tools.ietf.org/html/rfc6265) の規定に沿って Cookie をサポートする*必要があります*。

### <a name="http-redirection"></a>HTTP リダイレクト

クライアントは、[HTTP プロトコル仕様](https://www.w3.org/Protocols/rfc2616/rfc2616.html)で規定された、標準のリダイレクト メカニズムをサポートしている*必要があります*。

### <a name="speech-endpoints"></a>音声認識エンドポイント

クライアントは、Speech Service の適切なエンドポイントを使用する*必要があります*。 エンドポイントは、認識モードと言語に基づいて決まります。 次の表に例を示します。

| Mode | Path | サービス URI |
| -----|-----|-----|
| Interactive | /speech/recognition/interactive/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| 会話 | /speech/recognition/conversation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| ディクテーション | /speech/recognition/dictation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

詳しくは、 「[サービス URI](../GetStarted/GetStartedREST.md#service-uri)」をご覧ください。

### <a name="report-connection-problems"></a>接続問題の報告

クライアントは、接続の確立中に発生したすべての問題を直ちに報告する必要があります。 接続の失敗を報告するためのメッセージ プロトコルについては、「[接続障害テレメトリ](#connection-failure-telemetry)」で説明しています。

### <a name="connection-duration-limitations"></a>接続時間についての制限事項

一般的な Web サービスの HTTP 接続と比べて、WebSocket 接続は*長時間*継続されます。 Speech Service では、サービスへの WebSocket 接続の継続時間について、次の制限が設けられています。

 * アクティブな WebSocket 接続の最大継続時間は 10 分です。 接続がアクティブと見なされるのは、サービスまたはクライアントが、その接続を介して WebSocket メッセージを送信した場合です。 制限値に達した場合、サービスは警告なく接続を終了します。 クライアントは、接続の最大継続時間に達するか、またはそれに迫る時間まで接続をアクティブのままにしなくて済むようなユーザー シナリオを開発する必要があります。

 * 非アクティブな WebSocket 接続の最大継続時間は 180 秒です。 接続が非アクティブと見なされるのは、サービスとクライアントのいずれも、その接続を介して WebSocket メッセージを送信していない場合です。 非アクティブの最大継続時間に達すると、サービスは非アクティブな WebSocket 接続を終了します。

## <a name="message-types"></a>メッセージの種類

クライアントとサービスの間で WebSocket 接続が確立されたら、クライアントとサービスの双方でメッセージを送信できるようになります。 このセクションでは、それらの WebSocket メッセージの形式について説明します。

[IETF RFC 6455](https://tools.ietf.org/html/rfc6455) では、WebSocket メッセージはテキストまたはバイナリ エンコードを使用してデータを送信できると規定されています。 これら 2 つのエンコードでは、それぞれ異なる送信形式が使用されます。 各形式は、メッセージのペイロードを効率的にエンコードし、転送し、デコードできるように最適化されています。

### <a name="text-websocket-messages"></a>テキスト WebSocket メッセージ

テキスト WebSocket メッセージは、テキスト情報のペイロードを伝達します。これは、HTTP メッセージに使用される一般的なダブル キャリッジ リターンの改行ペアで区切られた、ヘッダーと本文のセクションで構成されます。 また、HTTP メッセージと同様、テキスト WebSocket メッセージでは、シングル キャリッジ リターンの改行ペアで区切られた、*名前: 値*の形式でヘッダーが示されます。 テキスト WebSocket メッセージに含めるテキストには、[UTF-8](https://tools.ietf.org/html/rfc3629) エンコードを使用する*必要があります*。

テキスト WebSocket メッセージでは、ヘッダー *Path* でメッセージのパスを指定する必要があります。 このヘッダーの値は、このドキュメントで後述する、音声認識プロトコル メッセージのいずれかの種類である必要があります。

### <a name="binary-websocket-messages"></a>バイナリ WebSocket メッセージ

バイナリ WebSocket メッセージは、バイナリのペイロードを伝達します。 Speech Service プロトコルでは、サービスとの間での音声の送受信は、バイナリ WebSocket メッセージを使用して行われます。 その他のメッセージはすべて、テキスト WebSocket メッセージになります。

テキスト WebSocket メッセージと同様、バイナリ WebSocket メッセージはヘッダーと本文のセクションで構成されます。 バイナリ WebSocket メッセージの最初の 2 バイトは、ヘッダー セクションのサイズを 16 ビット整数で示します ([ビッグ エンディアン](https://en.wikipedia.org/wiki/Endianness)順)。 ヘッダー セクションの最小サイズは 0 バイトです。 最大サイズは、8,192 バイトです。 バイナリ WebSocket メッセージのヘッダーに含めるテキストには、[US-ASCII](https://tools.ietf.org/html/rfc20) エンコードを使用する*必要があります*。

バイナリ WebSocket メッセージのヘッダーは、テキスト WebSocket メッセージと同じ形式でエンコードされます。 *名前:値*形式は、シングル キャリッジ リターンの改行ペアで区切られます。 バイナリ WebSocket メッセージでは、ヘッダー *Path* でメッセージのパスを指定する必要があります。 このヘッダーの値は、このドキュメントで後述する、音声認識プロトコル メッセージのいずれかの種類である必要があります。

Speech Service プロトコルでは、テキストとバイナリの両方の WebSocket メッセージが使用されます。

## <a name="client-originated-messages"></a>クライアント発のメッセージ

接続が確立されると、クライアントとサービスの双方でメッセージを送信できるようになります。 このセクションでは、クライアント アプリケーションが Speech Service に送信するメッセージの形式とペイロードについて説明します。 Speech Service から発信され、クライアント アプリケーションへと送信されるメッセージについては、「[サービス発のメッセージ](#service-originated-messages)」で説明しています。

クライアントからサービスに送信される主なメッセージは、`speech.config`、 `audio`、および `telemetry` メッセージです。 各メッセージについて詳しく説明する前に、すべてのメッセージに共通する必須のメッセージ ヘッダーについて説明します。

### <a name="required-message-headers"></a>必須のメッセージ ヘッダー

次のヘッダーは、すべてのクライアント発メッセージに必須のものです。

| ヘッダー | 値 |
|----|----|
| Path | このドキュメントで説明されているメッセージ パス |
| X-RequestId | "ダッシュのない" 形式の UUID |
| X-Timestamp | クライアントの UTC クロックのタイムスタンプ (ISO 8601 形式) |

#### <a name="x-requestid-header"></a>X-RequestId ヘッダー

クライアント発の要求は、*X RequestId* メッセージ ヘッダーによって一意に識別されます。 このヘッダーは、すべてのクライアント発メッセージに必須のものです。 *X-RequestId* ヘッダーの値は、"ダッシュのない" 形式の UUID である必要があります (たとえば、*123e4567e89b12d3a456426655440000*)。 これを正規形式 (*123e4567-e89b-12d3-a456-426655440000*) にすることは*できません*。 要求に *X-RequestId* ヘッダーがない場合や、ヘッダー値の UUID 形式が間違っている場合は、サービスが WebSocket 接続を終了します。

#### <a name="x-timestamp-header"></a>X-Timestamp ヘッダー

クライアント アプリケーションによって Speech Service に送信された各メッセージには、*X-Timestamp* ヘッダーが含まれている*必要があります*。 このヘッダーの値は、クライアントがメッセージを送信した時刻を示します。 要求に *X-Timestamp* ヘッダーがない場合や、ヘッダー値の形式が間違っている場合は、サービスが WebSocket 接続を終了します。

*X-Timestamp* ヘッダーの値は、'yyyy'-'MM'-'dd'T'HH':'mm':'ss'.'fffffffZ' という形式にする必要があります。'fffffff' は秒数の小数部分を表します。 たとえば、'12.5' は、'12 + 5/10 秒' を意味し、'12.526' は '12 + 526/1000 秒' を意味します。 この形式は、[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) に準拠したもので、標準の HTTP *Date* ヘッダーとは異なり、ミリ秒単位の精度を提供できます。 クライアント アプリケーションでは、ミリ秒単位のタイムスタンプが丸め処理される場合があります。 クライアント アプリケーションでは、[ネットワーク タイム プロトコル (NTP) サーバー](https://en.wikipedia.org/wiki/Network_Time_Protocol)を使用して、デバイス クロックが時刻を正確に追跡されるようにする必要があります。

### <a name="message-speechconfig"></a>メッセージ `speech.config`

Speech Service では、最善の音声認識機能を提供するため、お客様のアプリケーションの特性を把握する必要があります。 必要な特性データとしては、アプリケーションの実行に使用されている OS やデバイスに関する情報があります。 これらの情報は、`speech.config` メッセージを使って提供していただきます。

クライアントは、Speech Service への接続を確立した直後、`audio` メッセージを送信する前に、`speech.config` メッセージを送信する*必要があります*。 `speech.config` メッセージは、接続ごとに 1 回だけ送信する必要があります。

| フィールド | 説明 |
|----|----|
| WebSocket message encoding | Text |
| Body | ペイロード (JSON 構造) |

#### <a name="required-message-headers"></a>必須のメッセージ ヘッダー

| ヘッダー名 | 値 |
|----|----|
| Path | `speech.config` |
| X-Timestamp | クライアントの UTC クロックのタイムスタンプ (ISO 8601 形式) |
| Content-Type | application/json; charset=utf-8 |

Speech Service プロトコル内のすべてのクライアント発メッセージと同様、`speech.config` メッセージには、*X-Timestamp* ヘッダーを含める*必要があります*。 このヘッダーは、メッセージがサービスに送信された、クライアント UTC クロック時刻を記録するものです。 `speech.config` メッセージは特定の音声認識要求には関連付けられて*いない*ので、*X-RequestId* ヘッダーは必要ありません。

#### <a name="message-payload"></a>メッセージ ペイロード
`speech.config` メッセージのペイロードは、アプリケーションに関する情報を含んだ JSON 構造体です。 次の例は、この情報を示したものです。 クライアントとデバイスのコンテキスト情報は、JSON 構造体の *context* 要素に含まれています。

```JSON
{
  "context": {
    "system": {
      "version": "2.0.12341",
    },
    "os": {
      "platform": "Linux",
      "name": "Debian",
      "version": "2.14324324"
    },
    "device": {
      "manufacturer": "Contoso",
      "model": "Fabrikan",
      "version": "7.341"
      }
    },
  }
}
```

##### <a name="system-element"></a>システム要素

`speech.config` メッセージの system.version 要素には、クライアントのアプリケーションやデバイスによって使用される音声認識 SDK ソフトウェアのバージョンが含まれます。 値の形式は、*major.minor.build.branch* となります。 *branch* コンポーネントは、該当しない場合には省略できます。

##### <a name="os-element"></a>OS 要素

| フィールド | 説明 | 使用法 |
|-|-|-|
| os.platform | アプリケーションをホストしている OS プラットフォーム (たとえば、Windows、Android、iOS、Linux) |必須 |
| os.name | OS の製品名 (たとえば、Debian や Windows 10 など) | 必須 |
| os.version | OS のバージョン (形式は *major.minor.build.branch*) | 必須 |

##### <a name="device-element"></a>デバイス要素

| フィールド | 説明 | 使用法 |
|-|-|-|
| device.manufacturer | デバイス ハードウェアの製造元 | 必須 |
| device.model | デバイスのモデル | 必須 |
| device.version | デバイスの製造元によって提供されたデバイス ソフトウェア バージョン。 この値は、製造元によって追跡可能な、デバイスのバージョンを示します。 | 必須 |

### <a name="message-audio"></a>メッセージ `audio`

音声認識対応のクライアント アプリケーションは、音声ストリームを一連のチャンクに変換することで、Speech Service に音声を送信します。 各音声チャンクには、サービスによって文字起こしされる、音声のセグメントが含まれます。 1 つの音声チャンクの最大サイズは、8,192 バイトです。 音声ストリーム メッセージは、*バイナリ WebSocket メッセージ*です。

クライアントは、`audio` メッセージを使用してサービスに音声チャンクを送信します。 クライアントはマイクからの音声をチャンクで読み取り、それらのチャンクを Speech Service に送信して、トランスクリプションを要求します。 最初の `audio` メッセージには、サービスでサポートされているいずれかのエンコード形式に音声が準拠していることを示す、正しい形式のヘッダーが含まれている必要があります。 追加の `audio` メッセージには、マイクから読み取られたバイナリ音声ストリーム データのみが含まれます。

クライアントは必要に応じて、`audio`長さ 0 の本文を含んだメッセージを送信することがあります。 このメッセージは、ユーザーが発話をやめ、発話が完了し、マイクがオフになったことをクライアントが把握しているということを、サービスに知らせるものです。

Speech Service は、一意の要求識別子を含んだ最初の `audio` メッセージを使用して、新しい要求/応答サイクルまたは*ターン*の開始を知らせます。 サービスは、新しい要求識別子を持った `audio` メッセージを受信すると、以前のターンに関連付けられているキュー内のメッセージや未送信のメッセージを破棄します。

| フィールド | 説明 |
|-------------|----------------|
| WebSocket message encoding | Binary |
| Body | 音声チャンクのバイナリ データ。 最大サイズは、8,192 バイトです。 |

#### <a name="required-message-headers"></a>必須のメッセージ ヘッダー

次のヘッダーはすべての `audio` メッセージに必須です。

| ヘッダー         |  値     |
| ------------- | ---------------- |
| Path | `audio` |
| X-RequestId | "ダッシュのない" 形式の UUID |
| X-Timestamp | クライアントの UTC クロックのタイムスタンプ (ISO 8601 形式) |
| Content-Type | 音声コンテンツの種類。 種類は、*audio/x-wav* (PCM) か *audio/silk* (SILK) のいずれかである必要があります。 |

#### <a name="supported-audio-encodings"></a>サポートされている音声エンコード

このセクションでは、Speech Service でサポートされる音声コーデックについて説明します。

##### <a name="pcm"></a>PCM 0

Speech Service では、非圧縮のパルス符号変調 (PCM) 音声が受け付けられます。 音声は [WAV](https://en.wikipedia.org/wiki/WAV) 形式でサービスに送信されるため、最初の音声チャンクには、有効な [Resource Interchange File Format](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) (RIFF) ヘッダー*が含まれている必要があります*。 有効な RIFF ヘッダーを含んで*いない*音声チャンクを使ってクライアントがターンを開始した場合、サービスは要求を拒否し、WebSocket 接続を終了します。

PCM 音声は、16 kHz、16 ビット/サンプル、単一チャネル (*riff-16khz-16bit-mono-pcm*) でサンプリングされる*必要があります*。 Speech Service では、ステレオの音声ストリームはサポートされておらず、指定されたビット レート、サンプル レート、チャネル数を使用していない音声ストリームは拒否されます。

##### <a name="opus"></a>Opus

Opus は、非常に用途の広い、オープンかつ無料の音声コーデックです。 Speech Service では、`32000` または `16000` の固定ビット レートで Opus をサポートしています。 現在 Opus については、`audio/ogg` MIME タイプで指定された、`OGG` コンテナーのみがサポートされています。

Opus を使用するには、[JavaScript サンプル](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101)を変更し、返される `RecognizerSetup` メソッドを変更してください。

```javascript
return SDK.CreateRecognizerWithCustomAudioSource(
            recognizerConfig,
            authentication,
            new SDK.MicAudioSource(
                     new SDK.OpusRecorder(
                     {
                         mimeType: "audio/ogg",
                         bitsPerSecond: 32000
                     }
              )
          ));
```

#### <a name="detect-end-of-speech"></a>発話終了の検知

人間どうしの会話では、自分がいつ話し終えたのかを明示的に知らせることはありませんが、 音声入力を受け付けるアプリケーションでは、音声ストリームの終了を 2 つの方法で処理できます。サービス発話終了検知と、クライアント発話終了検知です。 通常、これら 2 つの方法のうち、より優れたユーザー エクスペリエンスを提供できるのは、サービス発話終了検知です。

##### <a name="service-end-of-speech-detection"></a>サービス発話終了検知

ハンズフリーの最適な音声認識エクスペリエンスを提供するために、ユーザーがいつ話し終えたのかをサービス側で検知します。 クライアントは、サービスが無音状態を検知し、`speech.endDetected` メッセージで応答を返すまで、マイクからの音声を*音声*チャンクとして送信します。

##### <a name="client-end-of-speech-detection"></a>クライアント発話終了検知

クライアント アプリケーションで、発話の終了をユーザーが何らかの方法で通知できる場合は、その通知をサービスに伝達することもできます。 たとえば、クライアント アプリケーションで、ユーザーが "停止" や "ミュート" のボタンを押すことができる場合などです。 発話の終了を通知するには、クライアント アプリケーションから、長さ 0 の本文を含んだ*音声*チャンク メッセージを送信します。 Speech Service は、このメッセージを入力音声ストリームの終了として解釈します。

### <a name="message-telemetry"></a>メッセージ `telemetry`

クライアント アプリケーションは、ターンに関するテレメトリを Speech Service に送信することで、各ターンの終了を確認する*必要があります*。 ターンの終了確認により、Speech Service は、要求の完了に必要なすべてのメッセージとその応答が、クライアントによって適切に受信されたことを確認できます。 また、ターンの終了確認によって、Speech Service では、クライアントアプリケーションが期待どおりに実行されていることも確認できるようになります。 この情報は、音声対応アプリケーションのトラブルシューティングについて支援を要請する際に、非常に重要となります。

クライアントは、`turn.end` メッセージを受信した後すぐに `telemetry` メッセージ送信することで、ターンの終了を確認する必要があります。 クライアントでは、できるだけ早く `turn.end` を確認するようにしてください。 クライアント アプリケーションがターンの終了確認に失敗した場合、Speech Service はエラーを生成して接続を終了する可能性があります。 クライアントは、*X-RequestId* 値によって識別される各要求と応答ごとに、`telemetry` メッセージを 1 つだけ送信する必要があります。

| フィールド | 説明 |
| ------------- | ---------------- |
| WebSocket message encoding | Text |
| Path | `telemetry` |
| X-Timestamp | クライアントの UTC クロックのタイムスタンプ (ISO 8601 形式) |
| Content-Type | `application/json` |
| Body | ターンに関するクライアント情報を含んだ JSON 構造体 |

`telemetry` メッセージの本文のスキーマについては、「[テレメトリ スキーマ](#telemetry-schema)」セクションで説明されていす。

#### <a name="telemetry-for-interrupted-connections"></a>接続が中断された場合のテレメトリ

ターン中に何らかの理由でネットワーク接続が失敗し、クライアントがサービスから `turn.end` メッセージを受信*できない*場合、クライアントは `telemetry` メッセージを送信します。 このメッセージは、クライアントが次回サービスに接続した際に、失敗した要求のことを説明するためのものです。 クライアントは、`telemetry` メッセージを送信するために、直ちに接続を試行する必要はありません。 メッセージはクライアント上にバッファーされ、ユーザー要求による次回の接続時に送信されます。 失敗した要求の `telemetry` メッセージには、失敗した要求からの *X-RequestId* 値を使用する*必要があります*。 なお、このメッセージは、他のメッセージの送受信を待つことなく、接続が確立され次第、サービスに送信される場合もあります。

## <a name="service-originated-messages"></a>サービス発のメッセージ

このセクションでは、Speech Service で生成され、クライアントへと送信されるメッセージについて説明します。 Speech Service では、クライアントの機能のレジストリを保持しており、クライアントごとに必要なメッセージを生成します。そのため、ここで説明されているすべてのメッセージを、すべてのクライアントが受信するわけではありません。 簡略化のため、メッセージは *Path* ヘッダーの値によって参照されます。 たとえば、*Path* 値が `speech.hypothesis` の WebSocket テキスト メッセージは、speech.hypothesis メッセージと呼ばれます。

### <a name="message-speechstartdetected"></a>メッセージ `speech.startDetected`

`speech.startDetected` メッセージは、Speech Service が音声ストリーム内に発話を検知したことを示します。

| フィールド | 説明 |
| ------------- | ---------------- |
| WebSocket message encoding | Text |
| Path | `speech.startDetected` |
| Content-Type | application/json; charset=utf-8 |
| Body | 発話の開始が検出された状況に関する情報を含んだ JSON 構造体。 この構造体の *Offset* フィールドは、音声ストリーム内で発話が検知された箇所のオフセット (100 ナノ秒単位) を示します。このオフセットは、音声ストリームの先頭からの相対位置で示されます。 |

#### <a name="sample-message"></a>サンプル メッセージ

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>メッセージ `speech.hypothesis`

音声認識中、Speech Service は検出された語句に関する仮説を定期的に生成します。 Speech Service は、これらの仮説を約 300 ミリ秒ごとにクライアントへ送信します。 `speech.hypothesis` は、音声認識のユーザー エクスペリエンスを強化する目的に*のみ*適しています。 これらのメッセージのコンテンツやテキストの正確性を当てにすることはしないでください。

 `speech.hypothesis` メッセージは、一定のテキスト レンダリング機能を有しているクライアントで、発話中のユーザーに、進行中の音声認識に関するニア リアルタイムのフィードバックを提供したい場合にのみ使用してください。

| フィールド | 説明 |
| ------------- | ---------------- |
| WebSocket message encoding | Text |
| Path | `speech.hypothesis` |
| X-RequestId | "ダッシュのない" 形式の UUID |
| Content-Type | application/json |
| Body | 音声認識仮説の JSON 構造体 |

#### <a name="sample-message"></a>サンプル メッセージ

```HTML
Path: speech.hypothesis
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Text": "this is a speech hypothesis",
  "Offset": 0,
  "Duration": 23600000
}
```

*Offset* 要素は、語句が認識された箇所のオフセット (100 ナノ秒単位) を示します。このオフセットは、音声ストリームの先頭からの相対位置で示されます。

*Duration* 要素は、その音声フレーズの継続時間 (100 ナノ秒単位) 示します。

クライアントでは、音声仮説に含まれている頻度、タイミング、テキスト、または 2 つの音声仮説内のテキスト間の整合性について、推測を立てることはしないでください。 仮説はあくまでも、サービスでのトランスクリプション プロセスに関するスナップショットにすぎません。 トランスクリプションの累積による安定した情報を表すものではありません。 たとえば、1 つ目の音声仮説に "fine fun" という語句が含まれていても、2 つ目の仮説には "find funny" という語句が含まれる場合もあります。 Speech Service では、音声仮説内のテキストに対して後処理 (たとえば、大文字/小文字や句読点の処理など) を実行することはありません。

### <a name="message-speechphrase"></a>メッセージ `speech.phrase`

Speech Service では、確定された認識結果を生成するための十分な情報が集まったと判断された場合、`speech.phrase` メッセージが生成されます。 Speech Service では、ユーザーの文や語句の完了が検知された後に、これらの結果が生成されます。

| フィールド | 説明 |
| ------------- | ---------------- |
| WebSocket message encoding | Text |
| Path | `speech.phrase` |
| Content-Type | application/json |
| Body | 音声フレーズの JSON 構造体 |

音声フレーズの JSON スキーマには、次のフィールドが含まれます: `RecognitionStatus`、 `DisplayText`、 `Offset`、および`Duration`。 これらのフィールドについて詳しくは、「[トランスクリプション応答](../concepts.md#transcription-responses)」をご覧ください。

#### <a name="sample-message"></a>サンプル メッセージ

```HTML
Path: speech.phrase
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": 0,
  "Duration": 12300000
}
```

### <a name="message-speechenddetected"></a>メッセージ `speech.endDetected`

`speech.endDetected` メッセージは、クライアント アプリケーションからサービスへの音声ストリーミングを停止する必要があることを示します。

| フィールド | 説明 |
| ------------- | ---------------- |
| WebSocket message encoding | Text |
| Path | `speech.endDetected` |
| Body | 発話の終了が検知された箇所のオフセットを含んだ JSON 構造体。 オフセットは、認識に使用された音声の先頭を基点に、100 ナノ秒単位で表されます。 |
| Content-Type | application/json; charset=utf-8 |

#### <a name="sample-message"></a>サンプル メッセージ

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

*Offset* 要素は、語句が認識された箇所のオフセット (100 ナノ秒単位) を示します。このオフセットは、音声ストリームの先頭からの相対位置で示されます。

### <a name="message-turnstart"></a>メッセージ `turn.start`

`turn.start` は、サービスの観点から見たターンの開始を通知します。 `turn.start` メッセージは、すべての要求で常に最初に受信される応答メッセージです。 `turn.start` メッセージが受信されない場合は、サービス接続の状態が無効であると考えられます。

| フィールド | 説明 |
| ------------- | ---------------- |
| WebSocket message encoding | Text |
| Path | `turn.start` |
| Content-Type | application/json; charset=utf-8 |
| Body | JSON 構造体 |

#### <a name="sample-message"></a>サンプル メッセージ

```HTML
Path: turn.start
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "context": {
    "serviceTag": "7B33613B91714B32817815DC89633AFA"
  }
}
```

`turn.start` メッセージの本文は、ターン開始のコンテキストを含んだ JSON 構造体です。 *context* 要素には、*serviceTag* プロパティが含まれています。 このプロパティは、サービスによってターンに関連付けられたタグの値を示します。 この値は、アプリケーション障害のトラブルシューティングについてお客様から支援の要請があった場合に、Microsoft によって使用される場合があります。

### <a name="message-turnend"></a>メッセージ `turn.end`

`turn.end` は、サービスの観点から見たターンの終了を通知します。 `turn.end` メッセージは、すべての要求で常に最後に受信される応答メッセージです。 クライアントはこのメッセージの受信を、クリーンアップ処理とアイドル状態への移行を促すシグナルとして使用できます。 `turn.end` メッセージが受信されない場合は、サービス接続の状態が無効であると考えられます。 その場合は、サービスへの既存の接続を終了し、再接続してください。

| フィールド | 説明 |
| ------------- | ---------------- |
| WebSocket message encoding | Text |
| Path | `turn.end` |
| Body | なし |

#### <a name="sample-message"></a>サンプル メッセージ

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>テレメトリ スキーマ

*telemetry* メッセージの本文は、ターンまたは接続試行に関するクライアント情報を含んだ JSON 構造体です。 この構造体は、クライアント イベントの発生時刻を記録したクライアント タイムスタンプで構成されます。 各タイムスタンプは、「X-Timestamp ヘッダー」というタイトルのセクションで説明したように、ISO 8601 形式である必要があります。 *telemetry* メッセージの JSON 構造体ですべての必須フィールドが指定されていない場合や、正しいタイムスタンプ形式が使用されていない場合は、サービスがクライアントへの接続を終了する可能性があります。 クライアントは、すべての必須フィールドに対して有効な値を指定する*必要があります*。 オプションのフィールドについても、適切な場合は必ず値を指定するように*してください*。 このセクションのサンプルに示す値は、説明のみを目的としたものです。

テレメトリ スキーマは、次の部分に分かれています: 受信したメッセージのタイムスタンプとメトリック。 各部分の形式と使用については、次のセクションで説明しています。

### <a name="received-message-time-stamps"></a>受信したメッセージのタイムスタンプ

クライアントは、サービスへの正常な接続後に受信したすべてのメッセージについて、受信時刻の値を記録する必要があります。 これらの値は、クライアントがネットワークからの各メッセージを*受信*した時刻を記録したものである必要があります。 その他の時刻をこの値に記録することはしないでください。 たとえば、クライアントがメッセージに対して*動作を行った*時刻を記録するようなことはしないでください。 受信したメッセージのタイムスタンプは、*名前:値*ペアの配列で指定されます。 ペアの名前は、メッセージの *Path* 値を示します。 ペアの値は、メッセージが受信されたクライアント時刻を示します。 なお、指定された名前のメッセージが 2 つ以上受信された場合、ペアの値は、それらのメッセージが受信された時刻を示すタイムスタンプの配列になります。

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

クライアントは、メッセージのタイムスタンプを JSON の本文に含めることで、サービスから送信されたすべてのメッセージの受信を確認する*必要があります*。 クライアントがメッセージの受信確認に失敗した場合は、サービスが接続を終了する可能性があります。

### <a name="metrics"></a>メトリック

クライアントでは、要求の有効期間中に発生したイベントに関する情報を記録する必要があります。 サポートされているメトリックは、`Connection`、 `Microphone`、および `ListeningTrigger` です。

### <a name="metric-connection"></a>メトリック `Connection`

`Connection` メトリックは、クライアントによる接続試行の詳細を示します。 このメトリックには、WebSocket 接続の開始時刻と終了時刻のタイムスタンプを含める必要があります。 `Connection` メトリックは、*接続の最初のターンについてのみ*必要です。 以降のターンについては、この情報を含める必要はありません。 接続が確立されるまでに、クライアントが接続を複数回試行した場合は、*すべての*接続試行に関する情報を含める必要があります。 詳しくは、 「[接続障害テレメトリ](#connection-failure-telemetry)」をご覧ください。

| フィールド | 説明 | 使用法 |
| ----- | ----------- | ----- |
| Name | `Connection` | 必須 |
| Id | この接続要求の *X-ConnectionId* ヘッダーに使用された接続識別子の値 | 必須 |
| start | クライアントが接続要求を送信した時刻 | 必須 |
| End | 接続が正常に確立されたという通知をクライアントが受信した時刻 (エラーが発生した場合は、接続が拒否された時刻や失敗した時刻) | 必須 |
| Error | 発生したエラーの説明 (発生した場合)。 接続に成功した場合は、このフィールドは省略してください。 このフィールドの最大長は 50 文字です。 | エラーの場合は必須 (その他の場合は省略) |

エラーの説明は最大 50 文字とし、なるべく、次の表に示す値を使用してください。 エラー状況がこれらの値と一致しない場合は、空白なしの[キャメルケース](https://en.wikipedia.org/wiki/Camel_case)を使用して、エラー状況を簡潔に説明できます。 *telemetry* メッセージをサービスへ送信するには、サービスへの接続が必要となるため、*telemetry* メッセージで報告できるのは、一時的なエラー状況に限られます。 クライアントからサービスへの接続確立が*永続的に*できなくなるエラー状況においては、*telemetry* メッセージを含め、一切のメッセージをサービスに送信できなくなります。

| Error | 使用法 |
| ----- | ----- |
| DNSfailure | ネットワーク スタック内の DNS エラーにより、クライアントがサービスに接続できなかった。 |
| NoNetwork | クライアントが接続を試行したが、ネットワーク スタックから、利用可能な物理ネットワークがないと報告された。 |
| NoAuthorization | 接続のための認証トークンを取得しようとしているときに、クライアント接続が失敗した。 |
| NoResources | 接続の試行中に、クライアントで一部のローカル リソース (メモリなど) が足りなくなった。 |
| 許可されていません | WebSocket アップグレード要求でサービスが HTTP `403 Forbidden` ステータス コードを返したため、クライアントがサービスに接続できなかった。 |
| 権限がありません | WebSocket アップグレード要求でサービスが HTTP `401 Unauthorized` ステータス コードを返したため、クライアントがサービスに接続できなかった。 |
| BadRequest | WebSocket アップグレード要求でサービスが HTTP `400 Bad Request` ステータス コードを返したため、クライアントがサービスに接続できなかった。 |
| ServerUnavailable | WebSocket アップグレード要求でサービスが HTTP `503 Server Unavailable` ステータス コードを返したため、クライアントがサービスに接続できなかった。 |
| ServerError | WebSocket アップグレード要求でサービスが `HTTP 500` 内部エラー ステータス コードを返したため、クライアントがサービスに接続できなかった。 |
| タイムアウト | サービスが応答する前に、クライアントの接続要求がタイムアウトした。 *End* フィールドには、クライアントがタイムアウトし、接続の待機を停止した時刻が記録されます。 |
| ClientError | 何らかの内部クライアント エラーにより、クライアントが接続を終了した。 |

### <a name="metric-microphone"></a>メトリック `Microphone`

`Microphone` メトリックは、すべての音声認識ターンに必須です。 このメトリックは、音声認識要求のために音声入力がアクティブに使用されているクライアント上の時間を計測するものです。

次の例は、クライアント アプリケーションで `Microphone` メトリックの *Start* 時刻値を記録するうえでのガイドラインとして使用できます。

* クライアント アプリケーションで、マイクを起動するための物理ボタンをユーザーが押す必要がある場合: ボタンが押されると、クライアント アプリケーションはマイクからの入力を読み取り、それを Speech Service に送信します。 `Microphone` メトリックの *Start* 値には、ボタンが押された後、マイクが初期化され、入力を提供する準備ができた時刻が記録されます。 `Microphone` メトリックの *End* 値には、サービスから `speech.endDetected` メッセージを受信した後、クライアント アプリケーションがサービスへの音声ストリーミングを停止した時刻が記録されます。

* クライアント アプリケーションが、"常時" リッスンするキーワード スポッターを使用する場合: クライアント アプリケーションは、発話されたトリガー語句をキーワード スポッターが検出した後に、マイクからの入力を収集し、それを Speech Service に送信します。 `Microphone` メトリックの *Start* 値には、キーワード スポッターがマイク入力の使用を開始するようにクライアントに通知した時刻が記録されます。 `Microphone` メトリックの *End* 値には、サービスから `speech.endDetected` メッセージを受信した後、クライアント アプリケーションがサービスへの音声ストリーミングを停止した時刻が記録されます。

* クライアント アプリケーションが持続的な音声ストリームにアクセスし、*音声検出モジュール*で、その音声ストリームでの無音状態/発話の検出を実行する場合: `Microphone` メトリックの *Start* 値には、*音声検出モジュール*が音声ストリームからの入力の使用を開始するようにクライアントに通知した時刻が記録されます。 `Microphone` メトリックの *End* 値には、サービスから `speech.endDetected` メッセージを受信した後、クライアント アプリケーションがサービスへの音声ストリーミングを停止した時刻が記録されます。

* クライアント アプリケーションが、複数ターンの要求の 2 番目のターンを処理しているときに、サービスの応答メッセージから、マイクをオンにして 2 番目のターンの入力を収集するように通知された場合: `Microphone` メトリックの *Start* 値には、アプリケーションがマイクをオンにし、その音声ソースからの入力の使用を開始した時刻が記録されます。 `Microphone` メトリックの *End* 値には、サービスから `speech.endDetected` メッセージを受信した後、クライアント アプリケーションがサービスへの音声ストリーミングを停止した時刻が記録されます。

`Microphone` メトリックの *End* 時刻値には、クライアント アプリケーションが音声入力のストリーミングを停止した時刻が記録されます。 多くの場合、このイベントは、クライアントがサービスからの `speech.endDetected` メッセージを受信した直後に発生します。 クライアント アプリケーションは、自身がプロトコルに適切に準拠していることを確認するために、`Microphone` メトリックの *End* 時刻値が、`speech.endDetected` メッセージの受信時刻値よりも後に発生していることを確認する場合があります。 また、1 つのターンの終了と、もう 1 つのターンの開始の間には、通常遅延が発生するため、クライアントはプロトコルへの準拠を検証するために、マイクを使用してサービスへの音声入力のストリーミングを*開始した*時刻が、後続のターンの `Microphone` メトリックの *Start* 時刻に正しく記録されていることを確認する場合があります。

| フィールド | 説明 | 使用法 |
| ----- | ----------- | ----- |
| Name | Microphone | 必須 |
| start | クライアントが、マイクまたはその他の音声ストリームからの音声入力の使用を開始した時刻。または、キーワード スポッターからトリガーを受信した時刻 | 必須 |
| End | クライアントがマイクや音声ストリームの使用を停止した時刻 | 必須 |
| Error | 発生したエラーの説明 (発生した場合)。 マイクの操作が正常に実行された場合は、このフィールドは省略してください。 このフィールドの最大長は 50 文字です。 | エラーの場合は必須 (その他の場合は省略) |

### <a name="metric-listeningtrigger"></a>メトリック `ListeningTrigger`
`ListeningTrigger` メトリックは、音声入力を開始するアクションをユーザーが実行した時間を測定するものです。 `ListeningTrigger` メトリックはオプションですが、このメトリックを提供できるクライアントでは、これを使用することをお勧めします。

次の例は、クライアント アプリケーションで `ListeningTrigger` メトリックの *Start* および *End* 時刻値を記録するうえでのガイドラインとして使用できます。

* クライアント アプリケーションで、マイクを起動するための物理ボタンをユーザーが押す必要がある場合: このメトリックの *Start* 値には、ボタンが押された時刻が記録されます。 *End* 値には、ボタンの押下が終了した時刻が記録されます。

* クライアント アプリケーションが、"常時" リッスンするキーワード スポッターを使用する場合: クライアント アプリケーションは、発話されたトリガー語句をキーワード スポッターが検出した後に、マイクからの入力を読み取り、それを Speech Service に送信します。 このメトリックの *Start* 値には、後にトリガー語句として検出される音声を、キーワード スポッターが受信した時刻が記録されます。 *End* 値には、トリガー語句の最後の単語をユーザーが発話した時刻が記録されます。

* クライアント アプリケーションが持続的な音声ストリームにアクセスし、*音声検出モジュール*で、その音声ストリームでの無音状態/発話の検出を実行する場合: このメトリックの *Start* 値には、後に発話として検出される音声を、*音声検出モジュール*が受信した時刻が記録されます。 *End* 値には、*音声検出モジュール*が発話を検出した時刻が記録されます。

* クライアント アプリケーションが、複数ターンの要求の 2 番目のターンを処理しているときに、サービスの応答メッセージから、マイクをオンにして 2 番目のターンの入力を収集するように通知された場合: このターンについては、クライアント アプリケーションで `ListeningTrigger` メトリックを*使いません*。

| フィールド | 説明 | 使用法 |
| ----- | ----------- | ----- |
| Name | ListeningTrigger | 省略可能 |
| start | クライアントのリスニング トリガーが開始された時刻 | 必須 |
| End | クライアントのリスニング トリガーが終了した時刻 | 必須 |
| Error | 発生したエラーの説明 (発生した場合)。 トリガー操作に成功した場合は、このフィールドは省略してください。 このフィールドの最大長は 50 文字です。 | エラーの場合は必須 (その他の場合は省略) |

#### <a name="sample-message"></a>サンプル メッセージ

次の例は、ReceivedMessages と Metrics の両方の部分のテレメトリ メッセージを示したものです。

```HTML
Path: telemetry
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000
X-Timestamp: 2016-08-16T15:03:54.183Z

{
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.171Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ],
  "Metrics": [
    {
      "Name": "Connection",
      "Id": "A140CAF92F71469FA41C72C7B5849253",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:48.000Z",
    },
    {
      "Name": "ListeningTrigger",
      "Start": "2016-08-16T15:03:48.776Z",
      "End": "2016-08-16T15:03:48.777Z",
    },
    {
      "Name": "Microphone",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:51.921Z",
    },
  ],
}
```

## <a name="error-handling"></a>エラー処理

このセクションでは、エラー メッセージの種類と、アプリケーションで処理する必要があるエラー状況について説明します。

### <a name="http-status-codes"></a>HTTP 状態コード

WebSocket アップグレード要求の最中に、Speech Service が標準の HTTP ステータス コード (`400 Bad Request` など) を返す場合があります。アプリケーションでは、これらのエラー状況を正しく処理する必要があります。

#### <a name="authorization-errors"></a>承認エラー

WebSocket アップグレード中に不正な認証が指定された場合、Speech Service は HTTP `403 Forbidden` ステータス コードを返します。 このエラー コードをトリガーする可能性があるのは、次のような状況です。

* *承認*ヘッダーが見つからない

* 認証トークンが無効である

* 認証トークンの期限が切れている

`403 Forbidden` エラー メッセージは、Speech Service の問題を示すものではありません。 このエラー メッセージは、クライアント アプリケーションの問題を示すものです。

### <a name="protocol-violation-errors"></a>プロトコル違反エラー

Speech Service がクライアントからプロトコル違反を検出した場合、サービスは接続終了の*ステータス コード*と*理由*を返した後に、WebSocket 接続を終了します。 クライアント アプリケーションは、この情報を使用して、違反のトラブルシューティングと修正を行うことができます。

#### <a name="incorrect-message-format"></a>不正なメッセージ形式

クライアントがサービスに送信したテキストまたはバイナリ メッセージが、この仕様で指定された正しい形式でエンコードされていない場合は、サービスが接続を終了し、*1007 Invalid Payload Data* ステータス コードを返します。

サービスがこのステータス コードを返す理由は、場合によってさまざまです。以下に例を示します。

* "Incorrect message format. Binary message has invalid header size prefix." クライアントが送信したバイナリ メッセージのヘッダー サイズ プレフィックスが無効です。

* "Incorrect message format. Binary message has invalid header size." クライアントが送信したバイナリ メッセージのヘッダー サイズが無効です。

* "Incorrect message format. Binary message headers decoding into UTF-8 failed." クライアントが送信したバイナリ メッセージに、UTF-8 で正しくエンコードされていないヘッダーが含まれています。

* "Incorrect message format. Text message contains no data." クライアントが送信したテキスト メッセージに、本文データが含まれていません。

* "Incorrect message format. Text message decoding into UTF-8 failed." クライアントが送信したテキスト メッセージが、UTF-8 で正しくエンコードされていません。

* "Incorrect message format. Text message contains no header separator." クライアントが送信したテキスト メッセージに、ヘッダー区切り記号が含まれていないか、不正なヘッダー区切り記号が使用されています。

#### <a name="missing-or-empty-headers"></a>ヘッダーが存在しないか空である

クライアントが送信したメッセージに、必須の *X-RequestId* ヘッダーや *Path* ヘッダーが含まれていない場合、サービスは接続を終了し、*1002 Protocol Error* ステータス コードを返します。 メッセージは次のとおりです: "Missing/Empty header. {Header name}."

#### <a name="requestid-values"></a>RequestId 値

クライアントが送信したメッセージで、不正な形式の *X-RequestId* ヘッダーが指定されている場合、サービスは接続を終了し、*1002 Protocol Error* ステータスを返します。 メッセージは次のとおりです: "Invalid request. X-RequestId header value was not specified in no-dash UUID format."

#### <a name="audio-encoding-errors"></a>音声エンコード エラー

クライアントがターンを開始する音声チャンクを送信し、音声形式やエンコーディングが必要な仕様に準拠していない場合、サービスは接続を終了し、*1007 Invalid Payload Data* ステータス コードを返します。 このメッセージは、形式やエンコーディングがエラーの原因であることを示しています。

#### <a name="requestid-reuse"></a>RequestId の再利用

ターンが終了した後、クライアントがそのターンの要求識別子を再利用したメッセージを送信した場合、サービスは接続を終了し、*1002 Protocol Error* ステータス コードを返します。 メッセージは次のとおりです: "Invalid request. Reuse of request identifiers is not allowed."

## <a name="connection-failure-telemetry"></a>接続障害テレメトリ

最善のユーザー エクスペリエンスを確保するため、クライアントは *telemetry* メッセージを使用して、接続内の重要なチェックポイントのタイムスタンプを Speech Service に通知する必要があります。 それと同様に重要なこととして、クライアントは、試行後に失敗した接続について、サービスに通知する必要があります。

クライアントは、失敗した接続試行ごとに、一意の *X-RequestId* ヘッダー値を使用して、*telemetry* メッセージを作成します。 接続を確立できなかった都合上、JSON 本文内の *ReceivedMessages* フィールドは省略してかまいません。 *Metrics*フィールドの `Connection` エントリのみを含めます。 このエントリには、開始時刻と終了時刻のタイムスタンプに加えて、発生したエラー状況が記録されています。

### <a name="connection-retries-in-telemetry"></a>テレメトリ内の接続再試行

クライアントは、接続試行をトリガーするイベントによって、*再試行*と*複数の接続試行*とを区別する必要があります。 ユーザーの入力ではなく、プログラムによって実行される接続試行は、再試行と呼ばれます。 ユーザー入力に応答して実行される複数の接続試行は、複数の接続試行と呼ばれます。 ユーザーによってトリガーされた接続試行の場合は、試行ごとに一意の *X-RequestId* と *telemetry* メッセージが付与されます。 プログラムによる再試行に対しては、*X-RequestId* が再利用されます。 1 つの接続試行に対して複数回の再試行が行われた場合、各再試行は、*telemetry* メッセージ内の `Connection` エントリにまとめられます。

たとえば、あるユーザーが接続を開始するためにキーワード トリガーを発話し、DNS エラーによって最初の接続試行が失敗したとします。 ただし、クライアントがプログラムによって実行した 2 回目の試行は成功したとします。 ユーザーからの追加入力を必要とせず、クライアントが接続を再試行したので、クライアントは、複数の `Connection` エントリを持つ 1 つの *telemetry* メッセージを使用して、接続を記述します。

もう 1 つの例として、あるユーザーが接続を開始するためにキーワード トリガーを発話し、3 回の再試行の後、その接続試行が失敗したとします。 その後、クライアントがサービスへの接続試行をやめ、何らかの問題が発生したことをユーザーに通知したとします。 そしてユーザーが、キーワード トリガーを再度発話し、 その結果、クライアントがサービスに接続できたとします。 接続されると、クライアントは直ちに、接続エラーについて記述した 3 つの `Connection` エントリを含む *telemetry* メッセージを、サービスに送信します。 `turn.end` メッセージを受信した後、クライアントは正常な接続について記述した別の *telemetry* メッセージを送信します。

## <a name="error-message-reference"></a>エラー メッセージ リファレンス

### <a name="http-status-codes"></a>HTTP 状態コード

| HTTP 状態コード | 説明 | トラブルシューティング |
| - | - | - |
| 400 Bad Request | クライアントが、不正な WebSocket 接続要求を送信しました。 | 必須のパラメーターと HTTP ヘッダーがすべて指定され、それらの値が正しいことを確認してください。 |
| 401 権限がありません | クライアントが、必要な認証情報を送信しませんでした。 | WebSocket 接続で *Authorization*ヘッダーを送信していることを確認してください。 |
| 403 許可されていません | クライアントが送信した認証情報が無効でした。 | *Authorization* ヘッダーを確認し、送信する値が期限切れや無効な値になっていないことを確認してください。 |
| 404 見つかりません | クライアントが、サポートされていない URL パスにアクセスしようとしました。 | WebSocket 接続に正しい URL を使用していることを確認してください。 |
| 500 Server Error | サービスで内部エラーが発生し、要求に応えることができません。 | 多くの場合、このエラーは一時的なものです。 要求をやり直してください。 |
| 503 サービス利用不可 | サービスが要求を処理できない状態でした。 | 多くの場合、このエラーは一時的なものです。 要求をやり直してください。 |

### <a name="websocket-error-codes"></a>WebSocket エラー コード

| WebSocketsStatus コード | 説明 | トラブルシューティング |
| - | - | - |
| 1000 Normal Closure | サービスが、エラーを返さずに WebSocket 接続を終了しました。 | WebSocket の終了が予期しないものであった場合は、ドキュメントを読み直して、サービスがどのような状況やタイミングで WebSocket 接続を終了する可能性があるかについて理解していることを確認してください。 |
| 1002 Protocol Error | クライアントがプロトコル要件に準拠していませんでした。 | プロトコル ドキュメントの内容を理解し、要件について把握していることを確認してください。 また、以前のドキュメントを読んでエラーの理由について確認し、プロトコル要件に違反していないかどうかを確認してください。 |
| 1007 Invalid Payload Data | クライアントが、プロトコル メッセージで無効なペイロードを送信しました。 | サービスに送信した最後のメッセージを確認して、エラーがないことを確認してください。 ペイロード エラーについては、前のドキュメントを読んでください。 |
| 1011 Server Error | サービスで内部エラーが発生し、要求に応えることができません。 | 多くの場合、このエラーは一時的なものです。 要求をやり直してください。 |

## <a name="related-topics"></a>関連トピック

WebSocket ベースの Speech Service プロトコルの実装である、[JavaScript SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) を参照してください。
