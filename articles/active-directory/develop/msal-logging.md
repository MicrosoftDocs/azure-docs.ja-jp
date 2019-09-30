---
title: MSAL のアプリケーションでのログ記録 |Azure
description: Microsoft Authentication Library (MSAL) アプリケーションのログ記録について説明します。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/22/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02c093375ba2dc5c851a2deb35bdea28338ee982
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135783"
---
# <a name="logging"></a>ログの記録
問題の診断や詳細の提示に役立つログ メッセージを生成する Microsoft Authentication Library (MSAL) アプリ。 アプリは数行のコードでログ記録を構成でき、詳細レベルと、個人データと組織のデータをログ記録するかどうかを制御するカスタム コントロールを備えています。 MSAL ログ コールバックを設定し、認証の問題が生じたときにユーザーがログを送信するための方法を提供することをお勧めします。

## <a name="logging-levels"></a>ログ レベル

MSAL のロガーでは、キャプチャする複数レベルの詳細を指定できます。

- エラー:問題の発生し、エラーが生成されたことを示します。 問題のデバッグと特定に使用します。
- 警告:問題になっているイベントであり、アプリが詳細を必要としています。 必ずしもエラーや障害があったわけではありませんが、診断や問題の特定の対象となっています。
- 情報:MSAL は、情報目的でイベントをログ記録し、必ずしもデバッグ目的ではありません。
- 詳細:[既定]。 MSAL は、大量の情報をログ記録し、ライブラリ動作に対する完全な詳細を提示します。

## <a name="personal-and-organizational-data"></a>個人と組織のデータ
既定では、MSAL ロガーは、機密性の高い個人または組織のデータをキャプチャしません。 ライブラリには、個人と組織のデータをログ記録することにした場合に、そのログ記録を有効にするオプションが用意されています。

## <a name="logging-in-msalnet"></a>MSAL.NET でのログ

 > [!NOTE]
 > MSAL.NET の詳細については、[MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) をチェックしてください。 MSAL.NET のログ記録のサンプルなど多くを入手できます。 
 
MSAL 3.x では、ログ記録は、`.WithLogging` ビルダー修飾子を使用してアプリの作成時にアプリケーションごとに設定されます。 このメソッドは、次のオプション パラメーターを取ります。

- *Level* では、目的のログ記録のレベルを指定できます。 エラーに設定すると、エラーだけを記録します
- *PiiLoggingEnabled* では、true に設定した場合、個人および組織のデータをログ記録できます。 既定では、false に設定されており、そのためアプリケーションは個人データをログ記録しません。
- *LogCallback* は、ログ記録を行う委任に設定されます。 *PiiLoggingEnabled* が true の場合、このメソッドはメッセージを 2 回受信します。1 回目は *containsPii* パラメーターが false になっており、メッセージには個人データが含まれず、2 回目は *containsPii* パラメーターが true になっており、メッセージには個人データが含まれることがあります。 場合によっては (メッセージに個人データが含まれない場合)、メッセージは同じになります。
- *DefaultLoggingEnabled* は、プラットフォームの既定のログ記録を有効にします。 既定では、false になっています。 これを true に設定した場合、デスクトップ/UWP アプリケーションのイベント トレーシング、iOS の NSLog、および Android の logcat を使用します。

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>MSAL.js でのログ記録

 `UserAgentApplication` インスタンスの作成用の構成中にロガー オブジェクトを渡すことによって、MSAL.js でのログ記録を有効にできます。 このロガー オブジェクトには、次のプロパティがあります。

- *localCallback*: ユーザー設定の方法でログを使用および公開するために開発者が指定できるコールバック インスタンス。 ログをリダイレクトする方法に応じて localCallback メソッドを実装します。

- *level* (省略可能): 構成可能なログ レベル。 サポートされるレベルは、エラー、警告、情報、詳細です。 既定値は情報です。

- *piiLoggingEnabled* (省略可能): true に設定した場合、個人および組織のデータをログ記録できます。 既定では、false に設定されており、そのためアプリケーションは個人データをログ記録しません。 個人データのログは、Console、Logcat、NSLog などの既定の出力には決して書き込まれません。 既定値は false に設定されます。

- *correlationId* (省略可能): デバッグ目的で要求を応答にマップするために使用される一意の識別子。 既定値は RFC4122 バージョン 4 guid (128 ビット) です。

```javascript

function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
     system: {
             logger: new Msal.Logger(
                                loggerCallback ,{
                                     level: Msal.LogLevel.Verbose,
                                     piiLoggingEnabled: false,
                                     correlationId: '1234'
                                }
                        )
     }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```
