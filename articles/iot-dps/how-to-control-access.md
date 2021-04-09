---
title: IoT Device Provisioning Service におけるセキュリティ エンドポイント | Microsoft Docs
description: 概念 - バックエンド アプリの IoT Device Provisioning Service (DPS) へのアクセスを制御する方法。 セキュリティ トークンについての情報が含まれています。
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 024dbf6518748a4048873de4eb54a53f9d9a6362
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954326"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service のアクセスを制御する

この記事では、IoT Device Provisioning Service をセキュリティで保護するためのオプションについて説明します。 プロビジョニング サービスでは、*アクセス許可* を使用して、各エンドポイントへのアクセス権を付与します。 アクセス許可により、機能に応じてサービス インスタンスへのアクセスを制限します。

この記事では、次の内容について説明します。

* プロビジョニング サービスへのアクセス用にバックエンド アプリに付与できるアクセス許可の種類。
* アクセス許可の確認に使用する認証プロセスとトークン。

### <a name="when-to-use"></a>使用する場合

プロビジョニング サービスのエンドポイントにアクセスするには、適切なアクセス許可が必要です。 たとえば、サービスに送信するすべてのメッセージと共に、バックエンド アプリにはセキュリティ資格情報が格納されているトークンを含める必要があります。

## <a name="access-control-and-permissions"></a>アクセス制御とアクセス許可

次の方法で[アクセス許可](#device-provisioning-service-permissions)を付与できます。

* **共有アクセス承認ポリシー**。 共有アクセス ポリシーにより、[アクセス許可](#device-provisioning-service-permissions)を自由に組み合わせて付与できます。 ポリシーは、[Azure Portal][lnk-management-portal] で定義することも、[Device Provisioning Service REST API][lnk-resource-provider-apis] を使用してプログラムで定義することもできます。 新しく作成されたプロビジョニング サービスには、次の既定のポリシーがあります。

* **provisioningserviceowner**: すべてのアクセス許可を持つポリシー。

> [!NOTE]
> 詳細については、[権限](#device-provisioning-service-permissions)に関する項目をご覧ください。

## <a name="authentication"></a>認証

Azure IoT Hub Device Provisioning Service では、共有アクセス ポリシーに対してトークンを確認することにより、エンドポイントへのアクセスを許可します。 対称キーなどのセキュリティ資格情報がネットワーク上で送信されることはありません。

> [!NOTE]
> Device Provisioning Service のリソースプロバイダーは、[Azure Resource Manager][lnk-azure-resource-manager] のすべてのプロバイダーと同様、Azure のサブスクリプションによりセキュリティで保護されています。

セキュリティ トークンを作成して使用する方法の詳細については、次のセクションをご覧ください。

HTTP は唯一サポートされているプロトコルであり、**Authorization** 要求ヘッダーに有効なトークンを含めることによって認証を実装します。

#### <a name="example"></a>例
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> [Azure IoT Device Provisioning Service SDK][lnk-sdks] を使用すると、サービスに接続した時点で自動的にトークンが生成されます。

## <a name="security-tokens"></a>セキュリティ トークン

Device Provisioning Service では、サービスの認証にセキュリティ トークンを使用することにより、ネットワーク経由でのキーの送信を回避します。 さらに、セキュリティ トークンには、有効期間とスコープの制限があります。 [Azure IoT Device Provisioning Service SDK][lnk-sdks] を使用すると、特別な構成を行うことなく自動でトークンを生成できます。 一部のシナリオでは、セキュリティ トークンを自分で生成して直接使用する必要があります。 このようなシナリオには、HTTP のサーフェスの直接使用が含まれます。

### <a name="security-token-structure"></a>セキュリティ トークンの構造

セキュリティ トークンは、Device Provisioning Service の特定の機能へのアクセスを期限付きでサービスに許可するために使用します。 プロビジョニング サービスに接続する承認を取得するには、サービスが共有アクセスまたは対称キーのいずれかで署名されたセキュリティ トークンを送信する必要があります。

共有アクセス キーで署名されたトークンでは、その共有アクセス ポリシーのアクセス許可に関連付けられたすべての機能へのアクセスが許可されます。 

セキュリティ トークンには、次の形式があります。

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

考えられる値を次に示します。

| 値 | 説明 |
| --- | --- |
| {signature} |HMAC-SHA256 署名文字列 (形式: `{URL-encoded-resourceURI} + "\n" + expiry`)。 **重要**:キーは base64 からデコードされ、HMAC-SHA256 計算を実行するためのキーとして使用されます。|
| {expiry} |1970 年 1 月 1 日の 00 時 00 分 00 秒 UTC からのエポック秒で表される UTF8 文字列。 |
| {URL-encoded-resourceURI} | 小文字のリソース URI の小文字の URL エンコード。 IoT Device Provisioning Service のホスト名 (プロトコルなし) で始まる、このトークンを使用してアクセスできるエンドポイントの (セグメント単位の) URI プレフィックス。 たとえば、「 `mydps.azure-devices-provisioning.net` 」のように入力します。 |
| {policyName} |このトークンの参照先となる共有アクセス ポリシーの名前。 |

**プレフィックスに関する注意事項**: URI プレフィックスは、文字単位ではなくセグメント単位で計算されます。 たとえば、`/a/b` は `/a/b/c` のプレフィックスであり、`/a/bc` のプレフィックスではありません。

次の Node.js スニペットは、`resourceUri, signingKey, policyName, expiresInMins` の入力からトークンを計算する **generateSasToken** という名前の関数を示しています。 以降のセクションでは、さまざまなトークンの使用例の各種入力を初期化する方法について詳しく説明します。

```javascript
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

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
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
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> トークンの有効期間は IoT Device Provisioning Service のコンピューターで検証されるため、トークンを生成するコンピューターのクロックのずれは最小限である必要があります。

### <a name="use-security-tokens-from-service-components"></a>サービス コンポーネントからセキュリティ トークンを使用する

前述したように、サービス コンポーネントでは、適切なアクセス許可を付与する共有アクセス ポリシーを使用した場合にのみセキュリティ トークンを生成できます。

エンドポイントで公開されるサービス機能を次に示します。

| エンドポイント | 機能 |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Device Provisioning Service を使用したデバイスの登録操作を提供します。 |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |デバイス登録グループを管理する操作を提供します。 |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |デバイスの登録状態を取得し、管理する操作を提供します。 |


たとえば、**enrollmentread** という事前作成済みの共有アクセス ポリシーを使用してトークンを生成するサービスの場合、次のパラメーターを持つトークンが作成されます。

* リソース URI: `{mydps}.azure-devices-provisioning.net`
* 署名キー: `enrollmentread` ポリシーのいずれかのキー
* ポリシー名: `enrollmentread`
* 任意の有効期間

![ポータルで Device Provisioning Service インスタンスの共有アクセス ポリシーを作成する][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

結果は次のようになります (すべての登録レコードの読み取りアクセスが許可されます)。

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>参照トピック:

以下の参照トピックは、IoT Device Provisioning Service へのアクセスの制御に関する詳細情報を提供しています。

### <a name="device-provisioning-service-permissions"></a>Device Provisioning Service のアクセス許可

次の表は、IoT Device Provisioning Service へのアクセス制御に使用できるアクセス許可の一覧です。

| 権限 | Notes |
| --- | --- |
| **ServiceConfig** |サービス構成を変更するためのアクセス権を付与します。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |
| **EnrollmentRead** |デバイスの登録および登録グループの読み取りアクセスを許可します。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |
| **EnrollmentWrite** |デバイスの登録および登録グループの書き込みアクセスを許可します。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |
| **RegistrationStatusRead** |デバイスの登録状態の読み取りアクセスを許可します。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |
| **RegistrationStatusWrite**  |デバイスの登録状態の削除のアクセス権を付与します。 <br/>このアクセス許可はバックエンドのクラウド サービスによって使用されます。 |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: /rest/api/iot-dps/