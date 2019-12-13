---
title: IoT プラグ アンド プレイ プレビュー リポジトリにアクセスするためのセキュリティ トークンを生成する | Microsoft Docs
description: プログラムによって IoT プラグ アンド プレイ プレビュー モデル リポジトリにアクセスするときに使用する Shared Access Signature トークンを生成します。
author: Philmea
ms.author: philmea
ms.date: 08/06/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f6c4f5b9784eeff9d03b6e93953674736fb78c6c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976028"
---
# <a name="generate-sas-token"></a>SAS トークンの生成

この攻略ガイドでは、IoT プラグ アンド プレイ プレビュー モデル リポジトリ API で使用する Shared Access Signature (SAS) トークンをプログラムで生成する方法について説明します。

## <a name="python"></a>Python

次のスニペットは、Python を使用して SAS トークンを生成する方法を示しています。

```python
from base64 import b64decode, b64encode
from hashlib import sha256
from hmac import digest
from time import time
from urllib.parse import quote_plus, urlencode

def calculate_sas_token(hostname, repo_id, key_name, key, expiry_in_second):
    expire = int(time() + expiry_in_second)
    sign_value = "{0}\n{1}\n{2}".format(repo_id, quote_plus(hostname), expire)
    sig = b64encode(digest(b64decode(key), sign_value.encode("utf-8"), sha256))
    token = "SharedAccessSignature " + urlencode({
        "sr": hostname, 
        "sig": sig,
        "se": str(expire),
        "skn": key_name,
        "rid": repo_id
        })
    return token
```

## <a name="c"></a>C\#

次のスニペットは、C\# を使用して SAS トークンを生成する方法を示しています。

```csharp
public static string generateSasToken(string hostName, string repoId, string key, string keyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = repoId + "\n" + WebUtility.UrlEncode(hostName) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(
        CultureInfo.InvariantCulture,
        "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}&rid={4}",
        WebUtility.UrlEncode(hostName),
        WebUtility.UrlEncode(signature),
        expiry,
        keyName,
        repoId);

    return token;
}
```

## <a name="use-the-sas-token"></a>SAS トークンを使用する

SAS トークンを生成したら、それを使用して HTTP POST 要求を行うことができます。 例:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

クライアントに SAS トークンを渡した場合、そのクライアントにはリソースの主キーがないため、ハッシュを逆にしてそれを取得することはできません。 SAS トークンを使用すると、クライアントがアクセスできる対象と、その期間を制御できます。 ポリシーで主キーを変更すると、それから作成された SAS トークンはすべて無効になります。

## <a name="next-steps"></a>次の手順

ここでは、モデル IoT ププラグ アンド プレイ プレビュー モデル リポジトリへのアクセスに使用するセキュリティ トークンの生成について学習しました。次の手順として、「[IoT プラグ アンド プレイ プレビュー モデリング開発者ガイド](concepts-developer-guide.md)」でさらに学習することをお勧めします。
