---
title: Microsoft Authentication Library (MSAL) アプリケーションでのログ記録 | Azure
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
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3235037d2b60322ab3e5c393c0a19b1a42bdc6c
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678031"
---
# <a name="logging-in-msal-applications"></a>MSAL アプリケーションでのログ記録

問題の診断に役立つログ メッセージを生成する Microsoft Authentication Library (MSAL) アプリ。 アプリは数行のコードでログ記録を構成でき、詳細レベルと、個人データと組織のデータをログ記録するかどうかを制御するカスタム コントロールを備えています。 ユーザーが認証の問題を抱えている場合は、MSAL ログ コールバックを作成して、ユーザーがログを送信できるようにすることをお勧めします。

## <a name="logging-levels"></a>ログ レベル

MSAL では、いくつかのレベルのログ記録の詳細が提供されます。

- エラー:問題の発生し、エラーが生成されたことを示します。 問題のデバッグと特定に使用します。
- 警告:必ずしもエラーや障害があったわけではありませんが、診断や問題の特定の対象となっています。
- 情報:MSAL は、情報目的でイベントをログ記録し、必ずしもデバッグ目的ではありません。
- 詳細:[既定]。 MSAL では、ライブラリの動作の詳細がログに記録されます。

## <a name="personal-and-organizational-data"></a>個人と組織のデータ

既定では、MSAL ロガーによって、機密性の高い個人または組織のデータはキャプチャされません。 ライブラリには、個人と組織のデータをログ記録することにした場合に、そのログ記録を有効にするオプションが用意されています。

## <a name="logging-in-msalnet"></a>MSAL.NET でのログ

 > [!NOTE]
 > MSAL.NET のログ記録のサンプルなどについては、[MSAL.NET の Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) を参照してください。

MSAL 3.x では、ログ記録は、`.WithLogging` ビルダー修飾子を使用してアプリの作成時にアプリケーションごとに設定されます。 このメソッドは、次のオプション パラメーターを取ります。

- `Level` では、目的のログ記録のレベルを指定できます。 エラーに設定すると、エラーだけを記録します
- `PiiLoggingEnabled` を true に設定した場合、個人および組織のデータをログ記録できます。 既定では、false に設定されており、そのためアプリケーションは個人データをログ記録しません。
- `LogCallback` は、ログ記録を行う委任に設定されます。 `PiiLoggingEnabled` が true の場合、このメソッドはメッセージを 2 回受信します。1 回目は `containsPii` パラメーターが false になっており、メッセージには個人データが含まれず、2 回目は `containsPii` パラメーターが true になっており、メッセージには個人データが含まれることがあります。 場合によっては (メッセージに個人データが含まれない場合)、メッセージは同じになります。
- `DefaultLoggingEnabled` は、プラットフォームの既定のログ記録を有効にします。 既定では、false になっています。 これを true に設定した場合、デスクトップ/UWP アプリケーションのイベント トレーシング、iOS の NSLog、および Android の logcat を使用します。

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

## <a name="logging-in-msal-for-android-using-java"></a>Java を使用した MSAL for Android でのログ記録

ログ記録コールバックを作成することにより、アプリの作成時にログ記録を有効にします。 コールバックでは、次のパラメーターを受け取ります。

- `tag` は、ライブラリによってコールバックに渡される文字列です。 ログ エントリに関連付けられており、ログ メッセージを並べ替えるために使用できます。
- `logLevel` では、目的のログ記録のレベルを指定できます。 サポートされているログ レベルは、`Error`、`Warning`、`Info`、`Verbose` です。
- `message` はログ エントリの内容です。
- `containsPII` では、個人データまたは組織データを含むメッセージをログに記録するかどうかを指定します。 既定では、これは false に設定されています。そのため、ご利用のアプリケーションでは個人データはログ記録されません。 `containsPII` が `true` の場合、このメソッドはメッセージを 2 回受信します。1 回目は `containsPII` パラメーターが `false` になっており、`message` には個人データが含まれず、2 回目は `containsPii` パラメーターが `true` に設定され、メッセージには個人データが含まれることがあります。 場合によっては (メッセージに個人データが含まれない場合)、メッセージは同じになります。

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

既定では、MSAL logger では個人を特定できる情報や組織を特定できる情報はキャプチャされません。
個人を特定できる情報や組織を特定できる情報のログ記録を有効にするには:

```java
Logger.getInstance().setEnablePII(true);
```

個人データと組織データのログ記録を無効にするには:

```java
Logger.getInstance().setEnablePII(false);
```

既定では、logcat へのログ記録は無効になっています。 有効にするには: 
```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="logging-in-msaljs"></a>MSAL.js でのログ記録

 `UserAgentApplication` インスタンスの作成用の構成中にロガー オブジェクトを渡すことによって、MSAL.js でのログ記録を有効にします。 このロガー オブジェクトには、次のプロパティがあります。

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
        clientId: “<Enter your client id>”,
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

## <a name="logging-in-msal-for-ios-and-macos"></a>iOS および macOS 用の MSAL でのログ記録

MSAL ログをキャプチャして独自のアプリケーションのログに組み込むようにコールバックを設定します。 コールバックのシグネチャは次のようになります。

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

例:

Objective-C
```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

Swift
```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-identifiable-information-pii"></a>個人を特定できる情報 (PII)

既定では、MSAL では PII はキャプチャまたはログ記録されません。 このライブラリにより、アプリ開発者は MSALLogger クラスのプロパティを介して、この設定をオンにできます。 PII の設定をオンにすると、アプリは高度な機密データを安全に処理し、規制要件に従う責任を負います。

Objective-C
```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

Swift
```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>ログ レベル

iOS および macOS 用の MSAL を使用してログを記録するときにログ レベルを設定するには、次のいずれかの値を使用します。

|Level  |説明 |
|---------|---------|
| `MSALLogLevelNothing`| すべてのログ記録を無効にします |
| `MSALLogLevelError` | 既定のレベルでは、エラーが発生した場合にのみ情報を出力します |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  ライブラリ エントリ ポイント、パラメーターおよびさまざまなキーチェーン操作を含む |
|`MSALLogLevelVerbose`     |  API のトレース       |

例:

Objective-C
```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```
 
 Swift
```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>ログ メッセージの形式

MSAL ログ メッセージのメッセージ部分は、次の形式となります: `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

例:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

関連付け ID とタイム スタンプを指定することは、問題を追跡する場合に役立ちます。 タイムスタンプと関連付け ID に関する情報は、ログ メッセージで確認できます。 それらの取得元として唯一信頼できるのは MSAL ログ メッセ ージです。
