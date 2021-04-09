---
title: MSAL.js でのエラーと例外のログ記録
titleSuffix: Microsoft identity platform
description: MSAL.js でエラーと例外をログに記録する方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d7463e3376847a219750254548c25ca79f4cdfb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954819"
---
# <a name="logging-in-msaljs"></a>MSAL.js でのログ記録

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msaljs"></a>MSAL.js でのログの構成

`UserAgentApplication` インスタンスを作成するための構成中にロガー オブジェクトを渡すことによって、MSAL.js (JavaScript) でのログ記録を有効にします。 このロガー オブジェクトには、次のプロパティがあります。

- `localCallback`: ユーザー設定の方法でログを使用および公開するために開発者が指定できるコールバック インスタンス。 ログをリダイレクトする方法に応じて localCallback メソッドを実装します。
- `level` (省略可能): 構成可能なログ レベル。 サポートされているログ レベルは、`Error`、`Warning`、`Info`、`Verbose` です。 既定では、 `Info`です。
- `piiLoggingEnabled` (省略可能): true に設定した場合、個人データと組織データをログに記録できます。 既定では、これは false であるため、ご利用のアプリケーションでは個人データはログ記録されません。 個人データのログは、Console、Logcat、NSLog などの既定の出力には決して書き込まれません。
- `correlationId` (省略可能): デバッグ目的で要求を応答にマップするために使用される一意の識別子。 既定値は RFC4122 バージョン 4 guid (128 ビット) です。

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
    system: {
        logger: new Msal.Logger(
            loggerCallback , {
                level: Msal.LogLevel.Verbose,
                piiLoggingEnabled: false,
                correlationId: '1234'
            }
        )
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="next-steps"></a>次のステップ

追加のコード サンプルについては、「[Microsoft ID プラットフォームのコード サンプル](sample-v2-code.md)」を参照してください。