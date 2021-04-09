---
title: Azure IoT Hub の MQTT 5 のサポート (プレビュー)
description: IoT Hub の MQTT 5 のサポートについて説明します
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fb2cc0b81083936a67bcd465e0408b9f4b53996b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603051"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>IoT Hub の MQTT 5 のサポートの概要 (プレビュー)

**バージョン:** 2.0 **API バージョン:** 2020-10-01-preview

このドキュメントでは、MQTT バージョン 5.0 プロトコルに対する IoT Hub のデータ プレーン API について定義します。 この API の完全な定義については、[API リファレンス](iot-hub-mqtt-5-reference.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- MQTT 5 を試すには、新しい IoT Hub で[プレビュー モードを有効](iot-hub-preview-mode.md)にします。
- [MQTT 5 の仕様](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html)に関して事前の知識が必要です。

## <a name="level-of-support-and-limitations"></a>サポートと制限のレベル

IoT Hub による MQTT 5 のサポートはプレビュー段階であり、次のように制限されています (特に明記されていない限り、`CONNACK` のプロパティを使用してクライアントに通知されます)。

- 公式な [Azure IoT Hub デバイス SDK](iot-hub-devguide-sdks.md) のサポートはまだありません。
- サブスクリプション ID はサポートされていません。
- 共有サブスクリプションはサポートされていません。
- `RETAIN` はサポートされていません。
- `Maximum QoS` が `1`です。
- `Maximum Packet Size` は `256 KiB` です (操作ごとにさらに制限されます)。
- 割り当てられたクライアント ID はサポートされていません。
- `Keep Alive` は `19 min` に制限されます (liveness 検査の最大遅延 – `28.5 min`)。
- `Topic Alias Maximum` が `10`です。
- `Response Information` はサポートされていません。`CONNECT` に `Request Response Information` プロパティが含まれている場合でも、`CONNACK` から `Response Information` プロパティは返されません。
- `Receive Maximum` (`QoS: 1` で許可される未処理で未確認の `PUBLISH` パケット (クライアントからサーバーの方向) の最大数) は `16` です。
- 1 つのクライアントが持つことのできるサブスクリプションの数は `50` 以下です。
  上限に達すると、`SUBACK` からサブスクリプションに `0x97` (クォータ超過) 理由コードが返されます。

## <a name="connection-lifecycle"></a>接続のライフサイクル

### <a name="connection"></a>Connection

この API を使用して IoT Hub にクライアントを接続するには、MQTT 5 の仕様に従って接続を確立します。
クライアントは、TLS ハンドシェイクが成功してから 30 秒以内に `CONNECT` パケットを送信する必要があります。そうしないと、サーバーによって接続が閉じられます。
`CONNECT` パケットの例を次に示します。

```yaml
-> CONNECT
    Protocol_Version: 5
    Clean_Start: 0
    Client_Id: D1
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    api-version: 2020-10-10
    host: abc.azure-devices.net
    sas-at: 1600987795320
    sas-expiry: 1600987195320
    client-agent: artisan;Linux
```

- `Authentication Method` プロパティは必須であり、使用される認証方法を示します。 認証方法の詳細については、「[認証](#authentication)」を参照してください。
- `Authentication Data` プロパティの処理は、`Authentication Method` によって異なります。 `Authentication Method` が `SAS` に設定されている場合は、`Authentication Data` が必要であり、それに有効な署名が含まれている必要があります。 認証データの詳細については、「[認証](#authentication)」を参照してください。
- `api-version` プロパティは必須であり、この仕様を適用するには、この仕様のヘッダーで提供される API バージョンの値に設定されている必要があります。
- `host` プロパティは、テナントのホスト名を定義するために使用されます。 TLS ハンドシェイクの間に Client Hello レコードで SNI 拡張機能が提示されていない限り、これは必須です
- `sas-at` は、接続の日時を定義するために使用されます。
- `sas-expiry` は、提供された SAS の有効期限を定義するために使用されます。
- `client-agent` は、必要に応じて、接続を作成するクライアントに関する情報を伝えるために使用されます。

> [!NOTE]
> 仕様全体を通じて、`Authentication Method` および名前が大文字になっている他のプロパティは、MQTT 5 のファースト クラス プロパティです。詳細については、MQTT 5 の仕様で説明されています。 `api-version` およびダッシュが使用されている他のプロパティは、IoT Hub API に固有のユーザー プロパティです。

IoT Hub は、認証と、接続をサポートするためのデータのフェッチが完了すると、`CONNACK` パケットで応答します。 接続が正常に確立された場合、`CONNACK` は次のようになります。

```yaml
<- CONNACK
    Session_Present: 1
    Reason_Code: 0x00
    Session_Expiry_Interval: 0xFFFFFFFF # included only if CONNECT specified value less than 0xFFFFFFFF and more than 0x00
    Receive_Maximum: 16
    Maximum_QoS: 1
    Retain_Available: 0
    Maximum_Packet_Size: 262144
    Topic_Alias_Maximum: 10
    Subscription_Identifiers_Available: 0
    Shared_Subscriptions_Available: 0
    Server_Keep_Alive: 1140 # included only if client did not specify Keep Alive or if it specified a bigger value
```

これらの `CONNACK` パケットのプロパティは、[MQTT 5 の仕様](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901080)に従っています。 それらは、IoT Hub の機能を反映しています。

### <a name="authentication"></a>認証

`CONNECT` クライアントの `Authentication Method` プロパティで、この接続に使用する認証の種類が定義されています。

- `SAS` - Shared Access Signature が `CONNECT` の `Authentication Data` プロパティで提供されます。
- `X509` - クライアントは、クライアント証明書認証を利用します。

 認証方法が IoT Hub で構成されているクライアントの方法と一致しない場合、認証は失敗します。

> [!NOTE]
> この API では、`CONNECT` パケットで `Authentication Method` プロパティを設定する必要があります。 `Authentication Method` プロパティが指定されていない場合、接続は `Bad Request` 応答で失敗します。

以前の API バージョンで使用されていたユーザー名とパスワードによる認証はサポートされていません。

#### <a name="sas"></a>SAS

SAS ベースの認証の場合、クライアントは接続コンテキストの署名を提供する必要があります。 これにより、MQTT 接続の信頼性が証明されます。 署名は、IoT Hub でのクライアント構成の 2 つの認証キーのいずれか、または[共有アクセス ポリシー](iot-hub-devguide-security.md)の 2 つの共有アクセス キーのいずれかに基づいている必要があります。

署名する文字列は、次のように構成されている必要があります。

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name` は、SNI 拡張機能 (TLS ハンドシェイクの間にクライアントによって Client Hello レコードで提示されます) または `CONNECT` パケットの `host` ユーザー プロパティから派生します。
- `Client Id` は、`CONNECT` パケット内のクライアント識別子です。
- `sas-policy` - 存在する場合は、認証に使用される IoT Hub アクセス ポリシーが定義されています。 `CONNECT` パケットではユーザー プロパティとしてエンコードされます。 省略可能: 省略すると、デバイス レジストリの認証設定が代わりに使用されることを意味します。
- `sas-at` - 存在する場合は、接続の日時 (現在の日時) が指定されます。 `CONNECT` パケットでは `time` 型のユーザー プロパティとしてエンコードされます。
- `sas-expiry` は、認証の有効期限を定義するために使用されます。 `CONNECT` パケットでは `time` 型のユーザー プロパティです。 このプロパティは必須です。

省略可能なパラメーターを省略する場合は、署名する文字列の代わりに空の文字列を使用する必要があります。

デバイスの対称キーのいずれかに基づいて文字列をハッシュするには、HMAC-SHA256 が使用されます。 その後、ハッシュ値は `Authentication Data` プロパティの値として設定されます。

#### <a name="x509"></a>X509

`Authentication Method` プロパティが `X509` に設定されている場合、IoT Hub により、指定されたクライアント証明書に基づいて接続の認証が行われます。

#### <a name="reauthentication"></a>再認証

SAS ベースの認証が使用されている場合は、有効期間の短い認証トークンを使用することをお勧めします。 接続の認証を維持し、有効期限切れによる切断を防ぐには、クライアントから `Reason Code: 0x19` (再認証) を指定した `AUTH` パケットを送信することで再認証を行う必要があります。

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

ルール:

- `Authentication Method` は、初期認証に使用されたものと同じである必要があります
- 接続がもともと共有アクセス ポリシーに基づく SAS を使用して認証されていた場合、再認証で使用される署名は、同じポリシーに基づいている必要があります。

再認証が成功した場合、IoT Hub によって `Reason Code: 0x00` (成功) を含む `AUTH` パケットが送信されます。 それ以外の場合、IoT Hub により `Reason Code: 0x87` (非承認) を含む `DISCONNECT` パケットが送信されて、接続は閉じられます。

### <a name="disconnection"></a>切断

サーバーは、いくつかの理由でクライアントを切断する場合があります。

- 否定受信確認 (または応答) を直接使用して応答するのが不可能な方法でクライアントが誤動作している。
- サーバーが接続の状態を最新の状態に保つことができない。
- 同じ ID を持つクライアントが接続している。

サーバーは、MQTT 5.0 の仕様で定義されている任意の理由コードで切断する場合があります。 留意事項:

- `135` (未承認) 再認証が失敗した、現在の SAS トークンの有効期限が切れた、またはデバイスの資格情報が変更されたとき
- `142` (セッション引き継ぎ) 同じクライアント ID を使用して新しい接続が開かれたとき。
- `159` (接続率超過) IoT Hub の接続率がしきい値を超えたとき  
- `131` (実装固有のエラー) は、この API で定義されているカスタム エラーに使用されます。 切断の原因に関する詳細情報を伝えるには、`status` および `reason` プロパティが使用されます (詳細については「[応答](#response)」を参照)。

## <a name="operations"></a>操作

この API のすべての機能は、操作として表されます。 テレメトリ送信操作の例を次に示します。

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 3
    Topic: $iothub/telemetry
    Payload: Hello

<- PUBACK
    Packet_Id: 3
    Reason_Code: 0
```

この API での操作の詳細な仕様については、[API リファレンス](iot-hub-mqtt-5-reference.md)を参照してください。

> [!NOTE]
> この仕様のすべてのサンプルは、クライアントの観点から示されています。 `->` という記号はクライアントがパケットを送信することを意味し、`<-` は受信することを意味します。

### <a name="message-topics-and-subscriptions"></a>メッセージのトピックとサブスクリプション

この API の操作のメッセージで使用されるトピックは、`$iothub/` で始まります。
MQTT ブローカーのセマンティクスは、これらの操作には適用されません (詳細については、「[Topics beginning with \$](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246)」($ で始まるトピック) を参照してください)。
この API で定義されていない `$iothub/` で始まるトピックは、サポートされていません。

- 未定義のトピックにメッセージを送信すると、`Not Found` 応答が返されます (詳細については「[応答](#response)」を参照)。
- 未定義のトピックをサブスクライブすると、`Reason Code: 0x8F` (トピック フィルター無効) を含む `SUBACK` になります。

トピック名とプロパティ名は大文字と小文字が区別され、完全に一致している必要があります。 たとえば、`$iothub/telemetry/` はサポートされませんが、`$iothub/telemetry` はサポートされます。

> [!NOTE]
> `$iothub/..` の下のサブスクリプションでのワイルドカードはサポートされていません。 つまり、クライアントで `$iothub/+` または `$iothub/#` をサブスクライブすることはできません。 それを実行しようとすると、`Reason Code: 0xA2` (ワイルドカード サブスクリプションは非サポート) を含む `SUBACK` になります。 操作に対するトピック名でのパス パラメーターの代わりの単一セグメント ワイルドカード (`+`) のみがサポートされます。

### <a name="interaction-types"></a>やり取りの種類

この API のすべての操作は、次の 2 種類のやり取りのいずれかに基づいています。

- オプションの受信確認を含むメッセージ (MessageAck)
- 要求 - 応答 (ReqRep)

操作は、方向によっても異なります (初期メッセージ交換での方向によって決まります)。

- クライアントからサーバーへ (c2s)
- サーバーからクライアントへ (s2c)

たとえば、テレメトリの送信は "受信確認を含むメッセージ" の種類のクライアントからサーバーへの操作であり、ダイレクト メソッドの処理は要求 - 応答の種類のサーバーからクライアントへの操作です。

#### <a name="message-acknowledgement-interactions"></a>メッセージ受信確認のやり取り

オプションの受信確認を含むメッセージ (MessageAck) のやり取りは、MQTT での `PUBLISH` と `PUBACK` パケットの交換として表されます。 受信確認はオプションであり、送信側は `QoS: 0` を含む `PUBLISH` パケットを送信することによって、それを要求しないことを選択できます。

> [!NOTE]
> クライアントによって宣言された `Maximum Packet Size` のために `PUBACK` パケット内のプロパティを切り捨てる必要がある場合、IoT Hub により、特定の制限内に収まる範囲で可能な限り多くのユーザー プロパティが保持されます。 リストの最初にあるユーザー プロパティの方が、後にあるものより、送信される可能性が高くなります。`Reason String` プロパティは最も低い優先順位です。

##### <a name="example-of-simple-messageack-interaction"></a>MessageAck の簡単なやり取りの例

メッセージ:

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

受信確認 (成功):

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>要求 - 応答のやり取り

要求 - 応答 (ReqRep) のやり取りでは、要求と応答の両方が `QoS: 0` の `PUBLISH` パケットに変換されます。

`Correlation Data` プロパティを両方で設定する必要があり、要求パケットと応答パケットを一致させるためにそれが使用されます。

この API では、すべての ReqRep 操作に対して 1 つの応答トピック `$iothub/responses` が使用されます。 クライアントからサーバーへの操作の場合、このトピックのサブスクライブまたはサブスクライブ解除は必要はありません。すべてのクライアントをサブスクライブすることがサーバーにより想定されます。

##### <a name="example-of-simple-reqrep-interaction"></a>ReqRep の簡単なやり取りの例

要求:

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

応答 (成功):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

ReqRep のやり取りで、要求または応答メッセージとして `QoS: 1` を含む `PUBLISH` パケットはサポートされません。 要求 `PUBLISH` を送信すると、`Bad Request` の応答になります。

`Correlation Data` プロパティでサポートされる最大長は 16 バイトです。 `PUBLISH` パケットの `Correlation Data` プロパティに 16 バイトより長い値が設定されている場合、IoT Hub により結果として `Bad Request` を含む `DISCONNECT` が送信され、接続が閉じられます。 この動作は、この API 内で交換されるパケットにのみ適用されます。

> [!NOTE]
> Correlation Data は任意のバイト シーケンスです。たとえば、UTF-8 文字列であることは保証されません。
>
> ReqRep の場合、応答には定義済みのトピックが使用されます。要求の `PUBLISH` パケットの応答トピック プロパティは無視されます (送信側によって設定されている場合)。

クライアントは、IoT Hub により自動的に、クライアントからサーバーへのすべての ReqRep 操作に対する応答トピックにサブスクライブされます。 クライアントが応答トピックから明示的にサブスクライブを解除した場合でも、IoT Hub により自動的にサブスクリプションに復帰されます。 サーバーからクライアントへの ReqRep のやり取りの場合でも、デバイスをサブスクライブする必要があります。

### <a name="message-properties"></a>メッセージ プロパティ

操作のプロパティ (システムまたはユーザー定義) は、MQTT 5 ではパケット プロパティとして表されます。

ユーザー プロパティ名は大文字と小文字が区別され、定義されているとおりのスペルである必要があります。 たとえば、`Trace-ID` はサポートされませんが、`trace-id` はサポートされます。

仕様外でプレフィックス `@` が付いていないユーザー プロパティが含まれる要求は、エラーになります。

システム プロパティは、ファースト クラス プロパティ (`Content Type` など) またはユーザー プロパティとしてエンコードされます。 仕様には、サポートされているシステム プロパティの完全な一覧が用意されています。
サポートされることが仕様に明記されていない限り、ファースト クラス プロパティはすべて無視されます。

ユーザー定義プロパティが許可される場合、その名前は `@{property name}` の形式に従う必要があります。 ユーザー定義プロパティでは、有効な UTF-8 の文字列値のみがサポートされます。 たとえば、値が `15` である `MyProperty1` プロパティは、名前が `@MyProperty` で値が `15` のユーザー プロパティとしてエンコードされる必要があります。

IoT Hub によりユーザー プロパティが認識されない場合は、エラーと見なされ、IoT Hub の応答は `Reason Code: 0x83` (実装固有エラー) と `status: 0100` (不正要求) が含まれる `PUBACK` になります。 受信確認が要求されなかった場合 (QoS: 0)、同じエラーを含む `DISCONNECT` パケットが返送され、接続は終了されます。

`string` 以外に、次のデータ型がこの API で定義されています。

- `time`: `1970-01-01T00:00:00.000Z` からのミリ秒数。 たとえば、`2020-09-24T22:39:55.320Z` の場合は `1600987195320` になります。
- `u32`: 32 ビットの符号なし整数。
- `u64`: 64 ビットの符号なし整数。
- `i32`: 32 ビットの符号付き整数。

### <a name="response"></a>Response

やり取りは、`Success`、`Bad Request`、`Not Found`、その他のさまざまな結果になる場合があります。
結果は、`status` ユーザー プロパティによって相互に区別されます。 可能な場合、`PUBACK` パケットの `Reason Code` の意味は (MessageAck のやり取りの場合)、`status` と一致します。

> [!NOTE]
> クライアントにより CONNECT パケットで `Request Problem Information: 0` が指定された場合、`status` プロパティも含め、MQTT 5 の仕様に準拠するために、`PUBACK` パケットでユーザー プロパティは送信されません。 この場合でも、クライアントは `Reason Code` を利用して、受信確認が肯定か否定かを判断できます。 

すべてのやり取りには既定値 (または成功) があります。 `Reason Code` は `0` で、`status` プロパティは "非設定" です。 それ以外の場合:

- MessageAck のやり取りの場合、`PUBACK` は 0x0 (成功) 以外の `Reason Code` を取得します。 結果をさらに明確にするため、`status` プロパティが存在する場合があります。
- ReqRep のやり取りの場合、応答 `PUBLISH` は `status` プロパティ セットを取得します。
- `QoS: 0` を使用して直接 MessageAck のやり取りに応答する方法はないため、応答情報を含む `DISCONNECT` パケットが代わりに送信された後、切断されます。

例 :

無効な要求 (MessageAck):

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

未承認 (MessageAck):

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

未承認 (ReqRep):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

必要に応じて、IoT Hub により次のユーザー プロパティが設定されます。

- `status` - 操作の状態に関する IoT Hub の拡張コード。 このコードは、結果を区別するために使用できます。
- `trace-id` – 操作のトレース ID。内部調査に使用できる操作に関する追加の診断情報が、IoT Hub によって保持される場合があります。
- `reason` - `status` プロパティによって示される状態で操作が終了した理由に関する詳細情報を提供する、人間が判読できるメッセージ。

> [!NOTE]
> クライアントにより CONNECT パケットの `Maximum Packet Size` プロパティが非常に小さい値に設定されている場合、すべてのユーザー プロパティがパケットに収まらず、含まれないものが出る可能性があります。
> 
> `reason` は人のみを対象としており、クライアント ロジックでは使用できません。 この API を使用すると、警告やバージョンの変更なしで、いつでもメッセージを変更できます。
>
> クライアントによって CONNECT パケットで `RequestProblemInformation: 0` が送信された場合は、[MQTT 5 の仕様](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053)に従い、ユーザー プロパティは受信確認に含まれません。

#### <a name="status-code"></a>status code

`status` プロパティにより、操作の状態コードが伝達されます。 それは、コンピューターによる読み取りが効率よく行われるように最適化されています。
`0501` のような文字列で 16 進数としてエンコードされる 2 バイトの符号なし整数で構成されます。
コードの構造 (ビット マップ):

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

1 バイト目はフラグに使用されます。

- ビット 0 と 1 は、結果の種類を示します。
  - `00` - 成功
  - `01` - クライアント エラー
  - `10` - サーバー エラー
- ビット 2: `1` は、エラーが再試行可能であることを示します
- ビット 3 から 7 は予約されているため、`0` に設定する必要があります

2 バイト目には、実際の個別の応答コードが含まれています。 フラグが異なるエラー コードで、2 バイト目の値が同じになる場合があります。 たとえば、`0001`、`0101`、`0201`、`0301` の各エラー コードは意味が異なる場合があります。

たとえば、`Too Many Requests` はクライアントの再試行可能なエラーで、独自のコードは `1` です。 その値は、`0000 0101 0000 0001` または `0x0501` です。

クライアントは、種類ビットを使用して、操作が正常に終了したかどうかを識別できます。 また、クライアントは再試行可能ビットを使用して、操作を再試行する必要があるかどうかを判断することもできます。

## <a name="recommendations"></a>Recommendations

### <a name="session-management"></a>セッションの管理

`CONNACK` パケットの `Session Present` プロパティにより、以前に作成されたセッションがサーバーによって復元されたかどうかが示されます。 このプロパティを使用して、トピックをサブスクライブするか、またはサブスクリプションは既に行われているのでサブスクライブをスキップするかを判断します。

`Session Present` を利用するには、クライアントで、行われたサブスクリプションを追跡するか (つまり、送信された `SUBSCRIBE` パケットと、正常の理由コードを含む受信された `SUBACK`)、または 1 つの `SUBSCRIBE`/`SUBACK` の交換ですべてのトピックをサブスクライブする必要があります。 そうしないと、クライアントが 2 つの `SUBSCRIBE` パケットを送信し、そのうちの 1 つがサーバーによって正常に処理された場合、サーバーからの `CONNACK` では `Session Present: 1` が通知されますが、受け入れられるのはクライアントのサブスクリプションの一部だけです。

古いバージョンのクライアントですべてのトピックがサブスクライブされていなかった場合を回避するため、クライアントの動作が変更されたときは (たとえば、ファームウェアの更新の一部として)、無条件でサブスクライブすることをお勧めします。 また、古いサブスクリプションが残されないように (サブスクリプションの許容最大数を消費しないように)、使用されなくなったサブスクリプションは明示的にサブスクライブ解除します。

### <a name="batching"></a>バッチ処理

メッセージのバッチを送信するための特別な形式はありません。 TLS およびネットワークのリソースを集中的に消費する操作のオーバーヘッドを削減するには、基になる TLS/TCP スタックに渡す前に、パケット (`PUBLISH`、`PUBACK`、`SUBSCRIBE` など) をバンドルします。 また、クライアントで "バッチ" 内のトピック エイリアスを簡単に作成できます。

- 接続に対する最初の `PUBLISH` パケットに完全なトピック名を格納し、トピックのエイリアスをそれに関連付けます。
- 同じトピックに対する後続のパケットでは、空のトピック名とトピック エイリアス プロパティを使用します。

## <a name="migration"></a>移行

このセクションでは、[前の MQTT API](iot-hub-mqtt-support.md) と比較して、API の変更点を示します。

- トランスポート プロトコルは MQTT 5 です。 以前は MQTT 3.1.1 です。
- SAS 認証のためのコンテキスト情報は、署名と共にエンコードされるのではなく、`CONNECT` パケットに直接格納されます。
- 使用する認証方法を示すためには、Authentication Method が使用されます。
- Shared Access Signature は、Authentication Data プロパティに格納されます。 以前は、Password フィールドが使用されました。
- 操作に関するトピックが異なります。
  - テレメトリ: `devices/{Client Id}/messages/events` ではなく `$iothub/telemetry`。
  - コマンド: `devices/{Client Id}/messages/devicebound` ではなく `$iothub/commands`。
  - パッチ ツインの報告: `$iothub/twin/PATCH/properties/reported` ではなく `$iothub/twin/patch/reported`。
  - ツインの望ましい状態の変更の通知: `$iothub/twin/PATCH/properties/desired` ではなく `$iothub/twin/patch/desired`。
- クライアント - サーバーの要求 - 応答操作の応答トピックのサブスクリプションは必要ありません。
- トピック名セグメントのエンコード プロパティの代わりに、ユーザー プロパティが使用されます。
- プロパティ名は、特別なプレフィックスを使用する略語ではなく、"ダッシュ" を使用する命名規則で記述されます。 ユーザー定義のプロパティには、代わりにプレフィックスが必要になりました。 たとえば、`$.mid` は `message-id` になり、`myProperty1` は `@myProperty1` になります。
- 要求 - 応答操作の要求と応答メッセージを関連付けるためには、トピックでエンコードされた `$rid` プロパティではなく、Correlation Data プロパティが使用されます。
- テレメトリ イベントには、`iothub-connection-auth-method` プロパティがスタンプされなくなりました。
- デバイスからのサブスクリプションがないと、C2D コマンドは消去されません。 デバイスがサブスクライブするか、期限が切れるまで、キューに登録されたままになります。

## <a name="examples"></a>例

### <a name="send-telemetry"></a>テレメトリを送信する

メッセージ:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 31
    Topic: $iothub/telemetry
    @myProperty1: My String Value # optional
    creation-time: 1600987195320 # optional
    @ No_Rules-ForUser-PROPERTIES: Any UTF-8 string value # optional
    Payload: <data>
```

確認:

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 0
```

代替受信確認 (調整済み):

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>get ツインの状態を送信する

要求:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

応答 (成功):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

応答 (禁止):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    status: 0102
    reason: Operation not allowed for `B2` SKU
    Payload: <empty>
```

---

### <a name="handle-direct-method-call"></a>ダイレクト メソッド呼び出しを処理する

要求:

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/abc
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

応答 (成功):

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status` が設定されていません - 成功応答です。

デバイス使用不可応答:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>QoS 0 使用中のエラー、パート 1

要求:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/gett # misspelled topic name - server won't recognize it as Request-Response interaction
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

応答:

```yaml
<- DISCONNECT
    Reason_Code: 144
    reason: "Unsupported topic: `$iothub/twin/gett`"
```

---

### <a name="error-while-using-qos-0-part-2"></a>QoS 0 使用中のエラー、パート 2

要求:

```yaml
-> PUBLISH # missing Correlation Data
    QoS: 0
    Topic: $iothub/twin/get
    Payload: <data>
```

応答:

```yaml
<- DISCONNECT
    Reason_Code: 131
    status: 0100
    reason: "`Correlation Data` property is missing"
```
## <a name="next-steps"></a>次のステップ

- MQTT 5 プレビュー API リファレンスを確認するには、「[IoT Hub データ プレーン MQTT 5 API リファレンス](iot-hub-mqtt-5-reference.md)」を参照してください。
- C# のサンプルを見るには、[GitHub サンプル リポジトリ](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp)を参照してください。