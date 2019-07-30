---
title: Azure IoT Hub Device Provisioning Service - 対称キーの構成証明
description: この記事では、IoT Device Provisioning Service を使用する対称キーの構成証明の概念について概説します。
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: b1a849732539dbc9e066bee7cc20141f56ffe10c
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348359"
---
# <a name="symmetric-key-attestation"></a>対称キーの構成証明

この記事では、Device Provisioning Service で対称キーを使用する場合の ID 構成証明プロセスについて説明します。 

対称キーの構成証明は、Device Provisioning Service インスタンスを使用してデバイスを認証する簡単なアプローチです。 この構成証明の方法では、初めてデバイスのプロビジョニングを行う開発者または厳密なセキュリティ要件がない開発者に対して、"Hello world" エクスペリエンスを提示します。 [TPM](concepts-tpm-attestation.md) または [X.509 証明書](concepts-security.md#x509-certificates)を使用するデバイス構成証明は、より安全であり、さらに厳格なセキュリティ要件に対して使用する必要があります。

対称キーの登録も、セキュリティ機能が限られているレガシ デバイスに対して、Azure IoT を通してクラウドに接続して起動する優れた方法を提供します。 レガシ デバイスでの対称キー構成証明について詳しくは、[レガシ デバイスでの対称キーの使用方法](how-to-legacy-device-symm-key.md)に関するページをご覧ください。


## <a name="symmetric-key-creation"></a>対称キーの作成

**[キーの自動生成]** オプションを有効にして新しい登録を保存すると、既定では、Device Provisioning Service により既定の 32 バイトの長さで新しい対称キーが作成されます。

![対称キーを自動生成する](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

このオプションを無効にすることで、独自の対称キーを登録に提供することもできます。 独自の対称キーを指定するときは、キーの長さを 16 ～ 64 バイトの範囲にする必要があります。 また、対称キーは有効な Base64 形式で提供する必要があります。



## <a name="detailed-attestation-process"></a>詳細な構成証明プロセス

Device Provisioning Service による対称キー構成証明は、デバイスを識別するために IoT ハブによってサポートされているものと同じ[セキュリティ トークン](../iot-hub/iot-hub-devguide-security.md#security-token-structure)を使用して実行されます。 これらのセキュリティ トークンは、[Shared Access Signature (SAS) トークン](../service-bus-messaging/service-bus-sas.md)です。 

SAS トークンは、対称キーを使用して作成されるハッシュされた "*署名*" を持っています。 署名は、構成証明中に提示されたセキュリティ トークンが本物かどうかを確認するために、Device Provisioning Service によって再作成されます。

SAS トークンの形式は次のとおりです。

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

各トークンのコンポーネントを次に示します。

| 値 | 説明 |
| --- | --- |
| {signature} |HMAC-SHA256 署名文字列。 個別の登録の場合は、対称キー (プライマリまたはセカンダリ) を使用してハッシュを実行することにより、この署名が生成されます。 登録グループの場合は、登録グループ キーから派生したキーを使用して、ハッシュを実行します。 ハッシュは、`URL-encoded-resourceURI + "\n" + expiry` の形式のメッセージに対して実行されます。 **重要**:キーは、HMAC-SHA256 計算の実行に使用する前に、base64 からデコードする必要があります。 また、署名の結果は、URL でエンコードする必要があります。 |
| {resourceURI} |このトークンを使用してアクセスできる登録エンドポイントの URI。Device Provisioning Service インスタンスのスコープ ID で始まっています。 たとえば、`{Scope ID}/registrations/{Registration ID}` のように指定します。 |
| {expiry} |1970 年 1 月 1 日の 00 時 00 分 00 秒 UTC からのエポック秒で表される UTF8 文字列。 |
| {URL-encoded-resourceURI} |小文字のリソース URI の小文字の URL エンコード |
| {policyName} |このトークンの参照先となる共有アクセス ポリシーの名前。 対称キー構成証明でプロビジョニングするときに使用されるポリシー名は、**registration** です。 |

デバイスが個々の登録で構成証明を行うときは、デバイスは個別登録エントリで定義されている対称キーを使用して、SAS トークン用のハッシュされた署名を作成します。

SAS トークンを作成するコードの例については、[セキュリティ トークン](../iot-hub/iot-hub-devguide-security.md#security-token-structure)に関するページをご覧ください。

対称キー構成証明のためのセキュリティ トークンの作成は、Azure IoT C SDK でサポートされています。 Azure IoT C SDK を使用して個々の登録で構成証明を行う例については、「[クイック スタート: 対称キーを使用してシミュレートされたデバイスをプロビジョニングする](quick-create-simulated-device-symm-key.md)」をご覧ください。


## <a name="group-enrollments"></a>グループ登録

グループ登録用の対称キーは、プロビジョニング時にデバイスによって直接使用されません。 代わりに、登録グループに属しているデバイスでは、派生されたデバイス キーを使用してプロビジョニングが行われます。 

最初に、登録グループで構成証明を行う各デバイスに対し、一意の登録 ID を定義します。 登録 ID の有効な文字は、小文字の英字、数字、ダッシュ ('-') です。 この登録 ID は、デバイスを識別する一意のものである必要があります。 たとえば、レガシ デバイスは多くのセキュリティ機能をサポートしていない可能性があります。 レガシ デバイスでは、そのデバイスを一意に識別するものが、MAC アドレスまたはシリアル番号しかない場合があります。 その場合は、次のように MAC アドレスとシリアル番号で登録 ID を構成できます。

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

「[対称キーを使用してレガシ デバイスをプロビジョニングする方法](how-to-legacy-device-symm-key.md)」では、この正確な例が使用されています。

デバイスの登録 ID を定義した後は、登録グループの対称キーを使用して、登録 ID の [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) ハッシュを計算し、派生デバイス キーを生成します。 登録 ID のハッシュは、次の C# コードを使用して実行できます。

```csharp
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

その後、結果のデバイス キーを使用して、構成証明に使用する SAS トークンを生成します。 登録グループ内の各デバイスが、一意の派生キーから生成されたセキュリティ トークンを使用して構成証明を行う必要があります。 登録グループの対称キーを構成証明書に直接使用することはできません。

#### <a name="installation-of-the-derived-device-key"></a>派生デバイス キーのインストール

理想的には、工場でデバイス キーを派生してインストールします。 このようにすると、デバイスに展開されるソフトウェアにグループ キーが含まれないことが保証されます。 デバイスに MAC アドレスまたはシリアル番号が割り当てられているときは、製造元がキーを格納することを選んだ場合でも、キーを派生してデバイスに挿入できます。

グループ登録キー (**K**) を使用して各デバイス登録 ID をハッシュすることにより工場で生成されるデバイス キーのテーブルを示す次の図を検討してください。 

![工場で割り当てられるデバイス キー](./media/concepts-symmetric-key-attestation/key-diversification.png)

各デバイスの ID は、登録 ID と、工場でインストールされる派生デバイス キーで表されます。 デバイス キーが別の場所にコピーされることはなく、グループ キーがデバイスに格納されることはありません。

デバイス キーが工場でインストールされない場合は、[ハードウェア セキュリティ モジュール HSM](concepts-security.md#hardware-security-module) を使用してデバイス ID を安全に保管する必要があります。

## <a name="next-steps"></a>次の手順

対称キー構成証明について理解できたので、以下の記事で詳細を確認してください。

* [クイック スタート:対称キーを使用してシミュレートされたデバイスをプロビジョニングする](quick-create-simulated-device-symm-key.md)
* [自動プロビジョニングの概念を確認する](./concepts-auto-provisioning.md)
* [自動プロビジョニングの使用を始める](./quick-setup-auto-provision.md) 
