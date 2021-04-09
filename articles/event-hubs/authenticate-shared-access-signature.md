---
title: Shared Access Signature を使用して Azure Event Hubs へのアクセスを認証する
description: この記事では、Shared Access Signature を使用して Event Hubs リソースへのアクセスを認証する方法を示します。
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: ff141fc1bb681e2356a4471dfdc808d622fd76b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98986497"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Shared Access Signature (SAS) を使用して Event Hubs リソースへのアクセスを認証する
Shared Access Signature (SAS) を使用すると、共有アクセス署名を持つクライアントに付与するアクセス許可の種類をきめ細かく制御することができます。 SAS で設定できる制御をいくつかを以下に示します。 

- SAS が有効な間隔 (開始時刻と有効期限を含む)。
- SAS によって付与されるアクセス許可。 たとえば、Event Hubs 名前空間の SAS では、リッスン アクセス許可は付与できますが、送信アクセス許可は付与できません。
- 有効な資格情報を提示するクライアントだけが Event Hub にデータを送信できる。
- クライアントが別のクライアントを偽装できないようにする。
- 悪意のあるクライアントをブロックして Event Hub にデータを送信できないようにする。

この記事では、SAS を使用する Event Hubs リソースへのアクセスの認証について説明します。 SAS を使用する Event Hubs リソースへのアクセスの **承認** については、[こちらの記事](authorize-access-shared-access-signature.md)を参照してください。 

> [!NOTE]
> Microsoft では、セキュリティのベスト プラクティスとして、より簡単に侵害される可能性のある Shared Access Signature を使用するのではなく、可能な限り Azure AD 資格情報を使用することをお勧めします。 引き続き Shared Access Signature (SAS) を使用して Event Hubs リソースへのきめ細かいアクセス許可を付与することはできますが、Azure AD では同様の機能が提供され、SAS トークンを管理したり、侵害された SAS の取り消しを心配したりする必要がありません。
> 
> Azure Event Hubs での Azure AD 統合の詳細については、「[Authorize access to Event Hubs using Azure AD](authorize-access-azure-active-directory.md)」 (Azure AD を使用した Event Hubs リソースへのアクセスを承認する) を参照してください。 


## <a name="configuring-for-sas-authentication"></a>SAS 認証用の構成
Event Hubs 名前空間、またはエンティティ (イベント ハブのイベント ハブ インスタンスまたは Kafka トピック) に、EventHubs 共有アクセス承認規則を構成することができます。 コンシューマー グループでの共有アクセス承認規則の構成は現在、サポートされていませんが、名前空間またはエンティティに構成された規則を使用して、コンシューマー グループへのアクセスをセキュリティで保護することができます。 

次の図は、サンプル エンティティで承認規則がどのように適用されるかを示しています。 

![承認規則を構成する](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

この例では、サンプルの Event Hubs 名前空間 (ExampleNamespace) に eh1 と topic1 の 2 つのエンティティがあります。 承認規則は、エンティティ レベルと名前空間レベルの両方で定義されます。  

manageRuleNS、sendRuleNS、および listenRuleNS の承認規則は、イベント ハブ インスタンス eh1 とトピック t1 の両方に適用されます。 listenRule-eh および sendRule-eh の承認規則はイベント ハブ インスタンス eh1 にのみ適用され、sendRuleT 承認規則はトピック topic1 にのみ適用されます。 

sendRuleNS 承認規則を使用する場合は、クライアント アプリケーションで eh1 と topic1 の両方に送信できます。 sendRuleT 承認規則が使用されている場合、topic1 のみに詳細なアクセスが適用されるため、アクセスにこの規則を使用するクライアント アプリケーションでは、eh1 に送信できなくなり、topic1 にのみ送信できます。

## <a name="generate-a-shared-access-signature-token"></a>Shared Access Signature トークンの生成 
承認規則の名前およびその署名キーの 1 つにアクセスできるすべてのクライアントは、SAS トークンを生成できます。 次の形式の文字列を作成することで、トークンが生成されます。

- `se` – トークンの有効期限。 エポック 1970 年 1 月 1 日 00:00:00 UTC (UNIX エポック) からトークンの期限が切れるまでの秒数を示す整数
- `skn` – 承認規則の名前。これは SAS キー名です。
- `sr` – アクセスされているリソースの URI。
- `sig` – 署名。

署名文字列は、リソースの URI (前のセクションで説明したスコープ) とトークンの有効期限の文字列表現を CRLF で区切ったものに対して計算された SHA-256 ハッシュです。

ハッシュ計算は次の擬似コードのようなもので、256 ビット/32 バイトのハッシュ値を返します。 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

受信側が同じパラメーターでハッシュを再計算して、発行者が有効な署名キーを所有していることを確認できるように、トークンにはハッシュされていない値が含まれています。

リソース URI とは、アクセスが要求される Service Bus リソースの完全な URI です。 たとえば、 http://<namespace>.servicebus.windows.net/<entityPath> または `sb://<namespace>.servicebus.windows.net/<entityPath>;` つまり、`http://contoso.servicebus.windows.net/eventhubs/eh1` となります。

URI はパーセント エンコードする必要があります。

署名に使用される共有アクセス承認規則は、この URI、またはその階層の親のいずれかで指定したエンティティに構成する必要があります。 たとえば、前の例では、`http://contoso.servicebus.windows.net/eventhubs/eh1` または `http://contoso.servicebus.windows.net` となります。

SAS トークンは、署名文字列で使われている <resourceURI> がプレフィックスになっているすべてのリソースで有効です。

> [!NOTE]
> 共有アクセス ポリシーを使用して、Event Hubs のアクセス トークンを生成します。 詳細については、「[Shared access authorization policy](authorize-access-shared-access-signature.md#shared-access-authorization-policies)」 (共有アクセス承認ポリシー) を参照してください。

### <a name="generating-a-signaturetoken-from-a-policy"></a>ポリシーからの署名 (トークン) の生成 
次のセクションでは、Shared Access Signature ポリシーを使用して SAS トークンを生成する方法を示します。

#### <a name="nodejs"></a>NodeJS

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>Java

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>SAS を使用する Event Hubs パブリッシャーの認証 
イベント パブリッシャーは Event Hub の仮想エンドポイントを定義します。 パブリッシャーは、イベント ハブへのメッセージ送信にのみ使用でき、メッセージ受信には使用できません。

通常、Event Hub はクライアントごとに 1 つのパブリッシャーを使用します。 Event Hub のパブリッシャーに送信されるすべてのメッセージは、その Event Hub 内でキューに格納されます。 パブリッシャーでは、きめの細かいアクセス制御を行うことができます。

それぞれの Event Hub クライアントに割り当てられる一意のトークンは、クライアントにアップロードされます。 トークンは、一意のトークンでそれぞれ異なる一意のパブリッシャーへのアクセスを許可するように生成されます。 トークンを保持するクライアントでは、1 つのパブリッシャーにのみ送信でき、それ以外のパブリッシャーには送信できません。 複数のクライアントが同じトークンを共有する場合、これらのクライアントそれぞれがパブリッシャーを共有します。

すべてのトークンは、SAS キーで割り当てられます。 通常、すべてのトークンは、同じキーで署名されます。 クライアントではキーが認識されません。そのため、クライアントがトークンを生成することはできません。 クライアントは、有効期限が切れるまで同じトークンで動作します。

たとえば、Event Hubs への送信/発行のみを対象とする承認規則を定義するには、送信承認規則を定義する必要があります。 これは名前空間レベルで行うことも、特定のエンティティ (イベント ハブ インスタンスまたはトピック) に対してより詳細なスコープを指定することもできます。 このような詳細なアクセスでスコープが設定されているクライアントまたはアプリケーションは、Event Hubs パブリッシャーと呼ばれます。 これを行うには、次のステップに従います。

1. 発行するエンティティに SAS キーを作成し、それに対して **送信** スコープを割り当てます。 詳細については、「[Shared access authorization policies](authorize-access-shared-access-signature.md#shared-access-authorization-policies)」 (共有アクセス承認ポリシー) を参照してください。
2. 手順 1 で生成されたキーを使用して、特定のパブリッシャーの有効期限を指定した SAS トークンを生成します。

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. トークンでアクセスを許可するエンティティおよびパブリッシャーにのみ送信できる、パブリッシャー クライアントにトークンを提供します。

    トークンの有効期限が切れると、クライアントはエンティティに送信/発行するためのアクセス権を失います。 


> [!NOTE]
> お勧めはしませんが、イベント ハブまたは名前空間へのアクセスを許可するトークンをデバイスに割り当てることができます。 このトークンを保持するデバイスでは、そのイベント ハブにメッセージを直接送信できます。 さらに、Event Hub への送信を禁止するブロックリストの対象にすることはできません。
> 
> 常に、特定の詳細なスコープを指定することをお勧めします。

> [!IMPORTANT]
> トークンが作成された後、各クライアントは独自のトークンと共にプロビジョニングされます。
>
> クライアントでは、イベント ハブにデータを送信するときに、トークンでその要求にタグを付けます。 攻撃者による送信の傍受とトークンの盗難を防ぐために、クライアントと Event Hub 間の通信は暗号化されたチャネルを介して行う必要があります。
> 
> トークンが攻撃者によって盗まれた場合、攻撃者は、トークンが盗まれたクライアントを偽装できます。 パブリッシャーをブロックリストに登録すると、そのクライアントは、別のパブリッシャーを使用する新しいトークンを受信するまで使用できなくなります。


## <a name="authenticating-event-hubs-consumers-with-sas"></a>SAS を使用する Event Hubs コンシューマーの認証 
Event Hubs プロデューサーによって生成されたデータから消費するバックエンド アプリケーションを認証する場合、Event Hubs トークン認証では、クライアントに **管理** 権限、または Event Hubs 名前空間あるいはイベント ハブ インスタンスまたはトピックに割り当てられる **リッスン** 特権が求められます。 データは、コンシューマー グループを使用して Event Hubs から消費されます。 SAS ポリシーで詳細なスコープが提供されますが、このスコープは、コンシューマー レベルではなく、エンティティ レベルでのみ定義されます。 これは、名前空間レベルあるいはイベント ハブ インスタンス レベルまたはトピック レベルで定義された特権が、そのエンティティのコンシューマー グループに適用されることを意味します。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [SAS を使用して承認する](authenticate-shared-access-signature.md)
- [Azure ロールベースのアクセス制御 (Azure RBAC) を使用して認可する](authenticate-shared-access-signature.md)
- [Event Hubs についてさらに学習する](event-hubs-about.md)

次の関連記事を参照してください。

- [Azure Active Directory を使用してアプリケーションから Azure Event Hubs への要求を認証する](authenticate-application.md)
- [Azure Active Directory を使用して Event Hubs リソースにアクセスするためのマネージド ID を認証する](authenticate-managed-identity.md)
- [Azure Active Directory を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-azure-active-directory.md)
- [Shared Access Signature を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-shared-access-signature.md)
