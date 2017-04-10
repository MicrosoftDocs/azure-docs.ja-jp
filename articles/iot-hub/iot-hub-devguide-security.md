---
title: "Azure IoT Hub セキュリティについて | Microsoft Docs"
description: "開発者ガイド - デバイス アプリとバックエンド アプリの IoT Hub へのアクセスを制御する方法。 X.509 証明書のセキュリティ トークンおよびサポートに関する情報が含まれています。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 45631e70-865b-4e06-bb1d-aae1175a52ba
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 444b01ffc7cccf0951ecb4d032d42667a99f639c
ms.lasthandoff: 03/24/2017


---
# <a name="control-access-to-iot-hub"></a>IoT Hub へのアクセスの制御

## <a name="overview"></a>概要

この記事では、Azure IoT Hub をセキュリティで保護するためのオプションについて説明します。 IoT Hub では、"*アクセス許可*" を使用して、IoT Hub の各エンドポイントへのアクセス権を付与します。 次のアクセス許可により、機能に応じて IoT Hub へのアクセスを制限します。

この記事では、次の内容について説明します。

* IoT Hub へのアクセス用にデバイスやバックエンド アプリに付与できるアクセス許可の種類。
* アクセス許可の確認に使用する認証プロセスとトークン。
* 資格情報のスコープを指定して特定のリソースへのアクセスを制限する方法。
* X.509 証明書の IoT Hub サポート。
* 既存のデバイス ID のレジストリまたは認証スキームを使用した、カスタムのデバイス認証メカニズム。

### <a name="when-to-use"></a>使用時の注意

IoT Hub のエンドポイントにアクセスするには、適切なアクセス許可が必要です。 たとえば、IoT Hub に送信するすべてのメッセージと共に、デバイスにはセキュリティ資格情報が格納されているトークンを含める必要があります。

## <a name="access-control-and-permissions"></a>アクセス制御とアクセス許可

次の方法で[アクセス許可](#iot-hub-permissions)を付与できます。

* **IoT Hub レベルの共有アクセス ポリシー**:  共有アクセス ポリシーにより、[アクセス許可](#iot-hub-permissions)を自由に組み合わせて付与できます。 ポリシーは、[Azure Portal][lnk-management-portal] で定義することも、[IoT Hub のリソース プロバイダー REST API][lnk-resource-provider-apis] を使用してプログラムによって定義することもできます。 新しく作成された IoT Hub には、次の既定のポリシーがあります。

  * **iothubowner**: すべてのアクセス許可を持つポリシー。
  * **service**: **ServiceConnect** アクセス許可を持つポリシー。
  * **device**: **DeviceConnect** アクセス許可を持つポリシー。
  * **registryRead**: **RegistryRead** アクセス許可を持つポリシー。
  * **registryReadWrite**: **RegistryRead** アクセス許可と RegistryWrite アクセス許可を持つポリシー。
  * **デバイスごとのセキュリティ資格情報**。 各 IoT Hub には、[ID レジストリ][lnk-identity-registry]が含まれています。 この ID レジストリ内の各デバイスでは、対応するデバイスのエンドポイントを対象として **DeviceConnect** アクセス許可を付与する、セキュリティ資格情報を構成できます。

たとえば、標準的な IoT ソリューションの場合は次のようになります。

* デバイス管理コンポーネントでは *registryReadWrite* ポリシーを使用します。
* イベント プロセッサ コンポーネントでは *service* ポリシーを使用します。
* ランタイム デバイス ビジネス ロジック コンポーネントでは *service* ポリシーを使用します。
* 個々のデバイスは、IoT Hub の ID レジストリに格納されている資格情報を使用して接続します。

> [!NOTE]
> 詳細については、[権限](#iot-hub-permissions)に関する項目をご覧ください。

## <a name="authentication"></a>認証

Azure IoT Hub では、共有アクセス ポリシーと ID レジストリのセキュリティ資格情報に対してトークンを確認することにより、エンドポイントへのアクセスを許可します。

対称キーなどのセキュリティの資格情報がネットワーク上で送信されることはありません。

> [!NOTE]
> Azure IoT Hub のリソース プロバイダーは、[Azure Resource Manager][lnk-azure-resource-manager] のすべてのプロバイダーと同様、Azure のサブスクリプションによりセキュリティで保護されています。

セキュリティ トークンを作成して使用する方法の詳細については、[IoT Hub セキュリティ トークン][lnk-sas-tokens]に関するセクションを参照してください。

### <a name="protocol-specifics"></a>プロトコルの詳細

MQTT、AMQP、および HTTP など、サポートされているプロトコルごとに、さまざまな方法でトークンが転送されます。

MQTT を使用する場合、CONNECT パケットでは、ClientId、ユーザー名フィールドの {iothubhostname}/{deviceId}、およびパスワード フィールドの SAS トークンとして、deviceId が使用されます。 {iothubhostname} は IoT Hub の完全な CName とする必要があります (contoso.azure-devices.net など)。

[AMQP][lnk-amqp] を使用する場合、IoT Hub では [SASL PLAIN][lnk-sasl-plain] と [AMQP Claims-Based-Security][lnk-cbs] がサポートされます。

AMQP Claims-Based-Security の場合、標準でこれらのトークンの送信方法が指定されます。

SASL PLAIN では、 **ユーザー名** を以下のように指定できます。

* `{policyName}@sas.root.{iothubName}` (IoT Hub レベルのトークンを使用する場合)。
* `{deviceId}@sas.{iothubname}` (デバイスを対象とするトークンを使用する場合)。

どちらの場合も、[IoT Hub セキュリティ トークン][lnk-sas-tokens]に関するセクションで説明されているように、パスワード フィールドにトークンが含まれています。

HTTP では、 **Authorization** 要求ヘッダーに有効なトークンを含めることによって認証を実装します。

#### <a name="example"></a>例

ユーザー名 (DeviceId では大文字と小文字が区別されます): `iothubname.azure-devices.net/DeviceId`

パスワード ([デバイス エクスプローラー][lnk-device-explorer] ツールで SAS トークンを生成します): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> [Azure IoT SDK][lnk-sdks] を使用すると、サービスに接続した時点で自動的にトークンが生成されます。 場合によっては、Azure IoT SDK でサポートされないプロトコルや認証方法もあります。

### <a name="special-considerations-for-sasl-plain"></a>SASL PLAIN に関する特別な考慮事項

SASL PLAIN を AMQP で使用する場合、IoT Hub に接続するクライアントは、TCP 接続ごとにトークンを 1 つ使用できます。 トークンの有効期限が切れると、サービスから TCP 接続が切断され、再接続がトリガーされます。 この動作は、バックエンド アプリの場合は問題ありませんが、デバイス アプリの場合は、次の理由から有害である可能性があります。

* ゲートウェイは、通常多くのデバイスの代理として接続しています。 SASL PLAIN を使用する場合、IoT Hub に接続するデバイスごとに、個別の TCP 接続を作成する必要があります。 このシナリオにより、電源とネットワーク リソースの消費量が大幅に増大し、各デバイスの接続の待機時間が増加します。
* 各トークンの有効期限が切れた後に、再接続に使用するリソースの量が増加すると、リソースが限られたデバイスには悪影響が出ます。

## <a name="scope-iot-hub-level-credentials"></a>IoT Hub レベルの資格情報のスコープ

制限付きのリソース URI を持つトークンを作成することにより、IoT Hub レベルのセキュリティ ポリシーのスコープを指定できます。 たとえば、デバイスからの D2C メッセージを送信するエンドポイントは **/devices/{deviceId}/messages/events**になります。 また、**DeviceConnect** アクセス許可を持つ IoT Hub レベルの共有アクセス ポリシーを使用して、resourceURI が **/devices/{deviceId}** であるトークンに署名することもできます。 この方法により、デバイスの **deviceId** の代わりにメッセージを送信するためにのみ使用できるトークンが作成されます。

これは [Event Hubs の発行元ポリシー][lnk-event-hubs-publisher-policy]に似たメカニズムであり、カスタムの認証方法の実装を可能にします。

## <a name="security-tokens"></a>セキュリティ トークン

IoT Hub では、デバイスとサービスの認証にセキュリティ トークンを使用することにより、ネットワーク経由でのキーの送信を回避します。 さらに、セキュリティ トークンには、有効期間とスコープの制限があります。 [Azure IoT SDK][lnk-sdks] を使用すると、特別な構成を行うことなく自動でトークンを生成できます。 ただし、一部のシナリオでは、セキュリティ トークンを自分で生成して直接使用する必要があります。 こうしたシナリオには、MQTT、AMQP、HTTP サーフェスを直接的に使用する場合や、「[カスタム デバイスの認証][lnk-custom-auth]」で説明されているようにトークン サービス パターンを実装する場合などがあります。

IoT Hub では、[X.509][lnk-x509] 証明書を使用して IoT Hub でデバイスの認証を行うこともできます。 

### <a name="security-token-structure"></a>セキュリティ トークンの構造

セキュリティ トークンは、IoT Hub の特定の機能へのアクセスを期限付きでデバイスとサービスに許可するために使用します。 承認済みのデバイスとサービスのみが接続できるようにするには、共有アクセス キーか対称キーのどちらかでセキュリティ トークンに署名する必要があります。 これらのキーは、ID レジストリにデバイス ID と共に格納されます。

共有アクセス キーで署名されたトークンでは、その共有アクセス ポリシーのアクセス許可に関連付けられたすべての機能へのアクセスが許可されます。 一方、デバイス ID の対称キーで署名されたトークンは、関連付けられたデバイス ID の **DeviceConnect** アクセス許可のみを付与します。

セキュリティ トークンには、次の形式があります。

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

考えられる値を次に示します。

| 値 | 説明 |
| --- | --- |
| {signature} |HMAC-SHA256 署名文字列 (形式: `{URL-encoded-resourceURI} + "\n" + expiry`)。 **重要**: キーは base64 からデコードされ、HMAC-SHA256 計算を実行するためのキーとして使用されます。 |
| {resourceURI} |IoT Hub のホスト名 (プロトコルなし) で始まる、このトークンを使用してアクセスできるエンドポイントの (セグメント単位の) URI プレフィックス。 たとえば、 `myHub.azure-devices.net/devices/device1` |
| {expiry} |1970 年 1 月 1 日の 00 時 00 分 00 秒 UTC からのエポック秒で表される UTF8 文字列。 |
| {URL-encoded-resourceURI} |小文字のリソース URI の小文字の URL エンコード |
| {policyName} |このトークンの参照先となる共有アクセス ポリシーの名前。 トークンがデバイス レジストリ資格情報を参照する場合は存在しません。 |

**プレフィックスに関する注意事項**: URI プレフィックスは、文字単位ではなくセグメント単位で計算されます。 たとえば、`/a/b` は `/a/b/c` のプレフィックスであり、`/a/bc` のプレフィックスではありません。

次の Node.js スニペットは、`resourceUri, signingKey, policyName, expiresInMins` の入力からトークンを計算する **generateSasToken** という名前の関数を示しています。 以降のセクションでは、さまざまなトークンの使用例の各種入力を初期化する方法について詳しく説明します。

```nodejs
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct autorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

比較として、セキュリティ トークンを生成する同等の Python コードを次に示します。

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> トークンの有効期間は IoT Hub コンピューターで検証されるため、トークンを生成するコンピューターのクロックのずれは最小限である必要があります。

### <a name="use-sas-tokens-in-a-device-app"></a>デバイス アプリで SAS トークンを使用する

セキュリティ トークンを使用して IoT Hub で **DeviceConnect** アクセス許可を取得するには、[ID レジストリの対称デバイス キー](#use-a-symmetric-key-in-the-identity-registry)を使用する方法と、[共有アクセス キー](#use-a-shared-access-policy)を使用する方法の 2 通りがあります。

デバイスからアクセスできるすべての機能は、仕様により、`/devices/{deviceId}`というプレフィックスを持つエンドポイントで公開されることにご注意ください。

> [!IMPORTANT]
> IoT Hub が特定のデバイスを認証する唯一の方法では、デバイス ID 対称キーを使用します。 デバイスの機能へのアクセスに共有アクセス ポリシーを使用する場合、ソリューションでは、セキュリティ トークンを発行するコンポーネントを、信頼できるサブコンポーネントと見なす必要があります。

デバイス向けのエンドポイントを次に示します (プロトコルは関係ありません)。

| エンドポイント | 機能 |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |デバイスからクラウドへのメッセージを送信します。 |
| `{iot hub host name}/devices/{deviceId}/devicebound` |クラウドからデバイスへのメッセージを受信します。 |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>ID レジストリの対称キーを使用する

デバイス ID の対称キーを使用してトークンを生成すると、トークンの policyName (`skn`) 要素は省略されます。

たとえば、すべてのデバイス機能にアクセスするために作成されるトークンには、次のパラメーターが必要です。

* リソース URI: `{IoT hub name}.azure-devices.net/devices/{device id}`
* 署名キー: `{device id}` ID の任意の対称キー
* ポリシー名なし
* 任意の有効期間

上の Node.js 関数を使用した例を次に示します。

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

結果は、次のように、device1 のすべての機能に対するアクセスが許可されます。

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> .NET の[デバイス エクスプローラー][lnk-device-explorer] ツールまたはクロスプラットフォームでノードベースの [iothub-explorer][lnk-iothub-explorer] コマンドライン ユーティリティを使って、SAS トークンを生成できます。

### <a name="use-a-shared-access-policy"></a>共有アクセス ポリシーを使用する

共有アクセス ポリシーからトークンを作成する際は、ポリシー名フィールド `skn` に、使用するポリシーの名前を設定する必要があります。 また、ポリシーで **DeviceConnect** アクセス許可を付与することも必要です。

共有アクセス ポリシーを使用してデバイスの機能にアクセスするための 2 つの主なシナリオは次のとおりです。

* [クラウド プロトコル ゲートウェイ][lnk-endpoints]
* [トークン サービス][lnk-custom-auth] (カスタム認証スキームの実装に使用)

共有アクセス ポリシーは任意のデバイスとして接続するためのアクセス権を付与する可能性があるため、セキュリティ トークンの作成時に適切なリソース URI を使用することが重要です。 これは、トークン サービスに特に重要です。このサービスでは、リソース URI を使用して、トークンのスコープを特定のデバイスに限定する必要があります。 これは、プロトコル ゲートウェイにはあまり関連がありません。このようなゲートウェイは、すべてのデバイスのトラフィックを既に仲介しているためです。

たとえば、 **device** という事前作成済みの共有アクセス ポリシーを使用するトークン サービスの場合、次のパラメーターを持つトークンが作成されます。

* リソース URI: `{IoT hub name}.azure-devices.net/devices/{device id}`
* 署名キー: `device` ポリシーのいずれかのキー
* ポリシー名: `device`
* 任意の有効期間

上の Node.js 関数を使用した例を次に示します。

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

結果は、次のように、device1 のすべての機能に対するアクセスが許可されます。

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

リソース URI を `myhub.azure-devices.net/devices`に設定するだけで、プロトコル ゲートウェイはすべてのデバイスに同じトークンを使用できます。

### <a name="use-security-tokens-from-service-components"></a>サービス コンポーネントからセキュリティ トークンを使用する

前述したように、サービス コンポーネントでは、適切なアクセス許可を付与する共有アクセス ポリシーを使用した場合にのみセキュリティ トークンを生成できます。

エンドポイントで公開されるサービス機能を次に示します。

| エンドポイント | 機能 |
| --- | --- |
| `{iot hub host name}/devices` |デバイス ID を作成、更新、取得、削除します。 |
| `{iot hub host name}/messages/events` |デバイスからクラウドへのメッセージを受信します。 |
| `{iot hub host name}/servicebound/feedback` |クラウドからデバイスへのメッセージのフィードバックを受信します。 |
| `{iot hub host name}/devicebound` |クラウドからデバイスへのメッセージを送信します。 |

たとえば、 **registryRead** という事前作成済みの共有アクセス ポリシーを使用してトークンを生成するサービスの場合、次のパラメーターを持つトークンが作成されます。

* リソース URI: `{IoT hub name}.azure-devices.net/devices`
* 署名キー: `registryRead` ポリシーのいずれかのキー
* ポリシー名: `registryRead`
* 任意の有効期間

```nodejs
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

結果は次のようになります (すべてのデバイス ID の読み取りアクセスが許可されます)。

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>サポートされている X.509 証明書

任意の X.509 証明書を使用して IoT Hub でデバイスを認証することができます。 次の証明書があります。

* **既存の X.509 証明書**。 デバイスには、既に X.509 証明書が関連付けられている場合があります。 デバイスはこの証明書を使用して IoT Hub で認証を受けることができます。
* **自己生成および自己署名の X-509 証明書**。 デバイスの製造業者または社内のデプロイ担当者はこれらの証明書を生成し、対応する秘密キー (および証明書) をデバイスに格納することができます。 [OpenSSL][lnk-openssl] や [Windows SelfSignedCertificate][lnk-selfsigned] ユーティリティなどのツールを使用することができます。
* **証明機関署名入りの X.509 証明書**。 証明機関 (CA) によって生成され署名された X.509 証明書を使用して、デバイスを識別し、IoT Hub でデバイスを認証することもできます。 IoTHub では、提示された拇印が構成されている拇印と一致することのみが検証されます。 証明書チェーンは検証されません。

デバイスは X.509 証明書またはセキュリティ トークンのいずれかを使用できますが、両方一緒に使用することはできません。

### <a name="register-an-x509-certificate-for-a-device"></a>デバイスの X.509 証明書を登録する

[C# 用 Azure IoT サービス SDK][lnk-service-sdk] (バージョン 1.0.8 以上) では、認証に X.509 証明書を使用するデバイスの登録がサポートされています。 デバイスのインポート/エクスポートなどの他の API でも X.509 証明書がサポートされます。

### <a name="c-support"></a>C\# のサポート

**RegistryManager** クラスでは、プログラムでデバイスを登録する方法が用意されています。 具体的には、**AddDeviceAsync** メソッドと **UpdateDeviceAsync** メソッドを使用することで、IoT Hub の ID レジストリにデバイスを登録して更新できます。 これら 2 つのメソッドは、入力として **Device** インスタンスを取ります。 **Device** クラスには **Authentication** プロパティが含まれています。これにより、プライマリとセカンダリの X.509 証明書拇印を指定することができます。 拇印は、X.509 証明書の SHA-1 ハッシュ (DER バイナリ エンコードを使用して格納) を表します。 プライマリ拇印、セカンダリ拇印、またはその両方を指定できます。 証明書のロールオーバー シナリオを処理するために、プライマリ拇印とセカンダリ拇印がサポートされています。

> [!NOTE]
> IoT Hub で必要とされ格納されるのは X.509 証明書全体ではなく、拇印のみとなります。

X.509 証明書を使用してデバイスを登録するための C\# コード スニペットの例を次に示します。

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>ランタイム操作中に X.509 証明書を使用する

[.NET 用 Azure IoT device SDK][lnk-client-sdk] (バージョン 1.0.11+) では、X.509 証明書の使用がサポートされています。

### <a name="c-support"></a>C\# のサポート

**DeviceAuthenticationWithX509Certifcate** クラスでは、X.509 証明書を使用した  **DeviceClient** インスタンスの作成がサポートされます。 X.509 証明書は、秘密キーを含む PFX (PKCS #12) 形式になっている必要があります。

コード スニペットのサンプルを次に示します。

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>カスタム デバイスの認証

IoT Hub の [ID レジストリ][lnk-identity-registry]を使用して、デバイスごとのセキュリティ資格情報とアクセス制御を[トークン][lnk-sas-tokens]により構成できます。 ただし、IoT ソリューションで既にカスタム ID レジストリや認証スキームにかなりの投資を行っている場合は、*トークン サービス*を作成すると、この既存のインフラストラクチャを IoT Hub と統合できます。 この方法により、ソリューションで他の IoT 機能を使用できます。

トークン サービスはカスタム クラウド サービスの 1 つです。 このサービスは、**DeviceConnect** アクセス許可が指定された IoT Hub *共有アクセス ポリシー*を使用して、*デバイスを対象とする*トークンを作成します。 これらのトークンにより、デバイスは IoT Hub に接続できるようになります。

![Steps of the token service pattern][img-tokenservice]

トークン サービス パターンの主な手順を次に示します。

1. IoT Hub 共有アクセス ポリシーを作成し、IoT Hub に対する **DeviceConnect** アクセス許可を指定します。 このポリシーは、[Azure Portal][lnk-management-portal] またはプログラムで作成できます。 トークン サービスは、このポリシーを使用して、作成されるトークンに署名します。
1. IoT Hub にアクセスする必要があるデバイスは、トークン サービスに署名付きトークンを要求します。 デバイスは、カスタム ID レジストリ/認証スキームで認証し、トークン サービスがトークンの作成に使用するデバイス ID を特定できます。
1. トークン サービスは、トークンを返します。 トークンは、`/devices/{deviceId}` を `resourceURI` として使用して作成され、`deviceId` は認証対象のデバイスになります。 トークン サービスは、共有アクセス ポリシーを使用してトークンを作成します。
1. デバイスは、IoT Hub で直接トークンを使用します。

> [!NOTE]
> .NET クラス [SharedAccessSignatureBuilder][lnk-dotnet-sas] または Java クラス [IotHubServiceSasToken][lnk-java-sas] を使用して、トークン サービスでトークンを作成できます。

トークン サービスは、必要に応じて、トークンの有効期限を設定できます。 トークンの期限が切れた時点で、IoT Hub はデバイスの接続を切断します。 その後、デバイスは新しいトークンをトークン サービスに要求する必要があります。 有効期限までの期間が短いと、デバイスとトークン サービスの両方で負荷が増加します。

デバイスをハブに接続するには、デバイスが接続にデバイス キーではなくトークンを使用している場合でも、引き続き IoT Hub の ID レジストリにデバイスを追加する必要があります。 そのため、トークンによりデバイスを認証する場合、[IoT Hub ID レジストリ][lnk-identity-registry]でデバイス ID を有効または無効にすることで、引き続きデバイスごとのアクセス制御を行うことができます。 この方法は、有効期限までの期間が長い場合にトークンを使用するリスクを軽減します。

### <a name="comparison-with-a-custom-gateway"></a>カスタム ゲートウェイとの比較

IoT Hub でカスタム ID レジストリ/認証スキームを実装する場合は、トークン サービス パターンをお勧めします。 これにより、IoT Hub がほとんどのソリューション トラフィックを処理できるためです。 ただし、カスタム認証スキームとプロトコルとの関係が複雑すぎるため、すべてのトラフィックを処理するサービス (*カスタム ゲートウェイ*) が必要になる場合があります。 このようなシナリオの例として、[トランスポート層セキュリティ (TLS) と事前共有キー (PSK)][lnk-tls-psk] の使用があります。 詳細については、[プロトコル ゲートウェイ][lnk-protocols]に関するトピックをご覧ください。

## <a name="reference-topics"></a>参照トピック:

以下の参照トピックは、IoT Hub へのアクセスの制御に関する詳細情報を提供しています。

## <a name="iot-hub-permissions"></a>IoT Hub のアクセス許可

次の表は、IoT hub へのアクセス制御に使用できるアクセス許可の一覧です。

| アクセス許可 | メモ |
| --- | --- |
| **RegistryRead** |ID レジストリへの読み取りアクセスを許可します。 詳細については、「[Identity registry][lnk-identity-registry]」(ID レジストリ) を参照してください。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |
| **RegistryReadWrite** |ID レジストリへの読み取りと書き込みアクセスを許可します。 詳細については、「[Identity registry][lnk-identity-registry]」(ID レジストリ) を参照してください。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |
| **ServiceConnect** |クラウド サービス向けの通信エンドポイントと監視エンドポイントへのアクセスを許可します。 <br/>デバイスからクラウドへのメッセージの受信、クラウドからデバイスへのメッセージの送信、対応する配信確認メッセージの取得のアクセス許可を付与します。 <br/>ファイル アップロードの配信確認メッセージの取得のアクセス許可を付与します。 <br/>タグおよび必要なプロパティを更新するためのデバイス ツインへのアクセス、報告されるプロパティの取得、クエリの実行のアクセス許可を付与します。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |
| **DeviceConnect** |デバイス向けのエンドポイントへのアクセスを許可します。 <br/>デバイスからクラウドへのメッセージの送信、クラウドからデバイスへのメッセージの受信のアクセス許可を付与します。 <br/>デバイスからのファイル アップロードの実行のアクセス許可を付与します。 <br/>デバイス ツインの必要なプロパティ通知の受信と、デバイス ツインの報告されるプロパティの更新のアクセス許可を付与します。 <br/>ファイル アップロードの実行のアクセス許可を付与します。 <br/>このアクセス許可はデバイスによって使用されます。 |

## <a name="additional-reference-material"></a>参考資料

IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント][lnk-endpoints]: 各 IoT Hub でランタイムと管理の操作のために公開される、さまざまなエンドポイントについて説明します。
* [調整とクォータ][lnk-quotas]: IoT Hub サービスに適用されるクォータと、サービスを使用するときに想定される調整の動作について説明します。
* [Azure IoT device SDK とサービス SDK][lnk-sdks]: IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。
* [IoT Hub のツインおよびジョブ向けのクエリ言語][lnk-query]: IoT Hub からデバイス ツインおよびジョブに関する情報を取得する際に使用できる IoT Hub のクエリ言語について説明します。
* [IoT Hub の MQTT サポート][lnk-devguide-mqtt]: IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

## <a name="next-steps"></a>次のステップ

IoT Hub へのアクセス制御の方法を理解できたら、次の IoT Hub 開発者ガイドのトピックも参考にしてください。

* [デバイス ツインを使って状態と構成を同期する][lnk-devguide-device-twins]
* [デバイスでダイレクト メソッドを呼び出す][lnk-devguide-directmethods]
* [複数デバイスで実行されるジョブのスケジュール設定][lnk-devguide-jobs]

この記事で説明した概念を試す場合は、次の IoT Hub のチュートリアルをご利用ください。

* [Azure IoT Hub を使ってみる][lnk-getstarted-tutorial]
* [IoT Hub を使用した C2D メッセージの送信方法][lnk-c2d-tutorial]
* [IoT Hub の D2C メッセージの処理方法][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth._iot_hub_service_sas_token
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

