---
title: Azure Active Directory REST API - Fiddler を使用してテストする
description: Fiddler を使用して Azure App Configuration REST API をテストする
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 1142aa25212d87c5484963cda4e172df3d1fbafc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932610"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>Fiddler を使用して Azure App Configuration REST API をテストする

[Fiddler](https://www.telerik.com/fiddler) を使用して REST API をテストするには、[認証](./rest-api-authentication-hmac.md)に必要な HTTP ヘッダーを要求に含める必要があります。 REST API をテストし、認証ヘッダーを自動的に生成するように Fiddler を構成する方法を次に示します。

1. TLS 1.2 が許可されているプロトコルであることを確認します。
    1. **[Tools]\(ツール\)**  >  **[Options]\(オプション\)**  >  **[HTTPS]** ) にアクセスします。
    1. **[Decrypt HTTPS traffic]\(HTTPS トラフィックの暗号化を解除する\)** がオンになっていることを確認します。
    1. プロトコルの一覧で、「**tls1.2**」を追加します (存在しない場合)。
1. **[Fiddler Script Editor]\(Fiddler スクリプト エディター\)** を開くか、Fiddler 内で **Ctrl + R** を押します
1. `Handlers` クラス内の `OnBeforeRequest` 関数の前に次のコードを追加します

    ```js
    static function SignRequest(oSession: Session, credential: String, secret: String) {
        var utcNow = DateTimeOffset.UtcNow.ToString("r", System.Globalization.DateTimeFormatInfo.InvariantInfo);
        var contentHash = ComputeSHA256Hash(oSession.RequestBody);
        var stringToSign = oSession.RequestMethod.ToUpperInvariant() + "\n" + oSession.PathAndQuery + "\n" + utcNow +";" + oSession.hostname + ";" + contentHash;
        var signature = ComputeHMACHash(secret, stringToSign);

        oSession.oRequest.headers["x-ms-date"] = utcNow;
        oSession.oRequest.headers["x-ms-content-sha256"] = contentHash;
        oSession.oRequest.headers["Authorization"] = "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=" + signature;
    }

    static function ComputeSHA256Hash(content: Byte[]) {
        var sha256 = System.Security.Cryptography.SHA256.Create();
        try {
            return Convert.ToBase64String(sha256.ComputeHash(content));
        }
        finally {
            sha256.Dispose();
        }
    }

    static function ComputeHMACHash(secret: String, content: String) {
        var hmac = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String(secret));
        try {
            return Convert.ToBase64String(hmac.ComputeHash(System.Text.Encoding.ASCII.GetBytes(content)));
        }
        finally {
            hmac.Dispose();
        }
    }
    ```

1. `OnBeforeRequest` 関数の末尾に次のコードを追加します。 TODO コメントの指示に従ってアクセス キーを更新します。

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. [Fiddler の Composer](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest) を使用して要求を生成して送信します
