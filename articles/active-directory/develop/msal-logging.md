---
title: MSAL アプリでのログ記録 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) アプリケーションのログ記録について説明します。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a71093ee05e032a5df89bb0560b6daa82e2fbf2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916147"
---
# <a name="logging-in-msal-applications"></a>MSAL アプリケーションでのログ記録

問題の診断に役立つログ メッセージを生成する Microsoft Authentication Library (MSAL) アプリ。 アプリは数行のコードでログ記録を構成でき、詳細レベルと、個人データと組織のデータをログ記録するかどうかを制御するカスタム コントロールを備えています。 ユーザーが認証の問題を抱えている場合は、MSAL ログ コールバックを作成して、ユーザーがログを送信できるようにすることをお勧めします。

## <a name="logging-levels"></a>ログ レベル

MSAL では、いくつかのレベルのログ記録の詳細が提供されます。

- エラー:問題の発生し、エラーが生成されたことを示します。 問題のデバッグと特定に使用します。
- 警告:必ずしもエラーや障害があったわけではありませんが、診断や問題の特定の対象となっています。
- 情報:MSAL は、情報目的でイベントをログ記録し、必ずしもデバッグ目的ではありません。
- 詳細:既定値。 MSAL では、ライブラリの動作の詳細がログに記録されます。

## <a name="personal-and-organizational-data"></a>個人と組織のデータ

既定では、MSAL ロガーによって、機密性の高い個人または組織のデータはキャプチャされません。 ライブラリには、個人と組織のデータをログ記録することにした場合に、そのログ記録を有効にするオプションが用意されています。

特定の言語での MSAL ログの詳細については、使用する言語に一致するタブを選択してください。

## <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

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

## <a name="androidtabandroid"></a>[Android](#tab/android)

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

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

## <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>iOS および macOS 用の MSAL でのログ記録 - ObjC

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

### <a name="personal-data"></a>個人データ

既定では、MSAL は個人データ (PII) をキャプチャまたはログに記録しません。 このライブラリにより、アプリ開発者は MSALLogger クラスのプロパティを介して、この設定をオンにできます。 `pii.Enabled` をオンにすることで、アプリは機密性の高いデータを安全に処理し、規制要件に従う責任を負います。

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>ログ レベル

iOS および macOS 用の MSAL を使用してログを記録するときにログ レベルを設定するには、次のいずれかの値を使用します。

|Level  |説明 |
|---------|---------|
| `MSALLogLevelNothing`| すべてのログ記録を無効にします |
| `MSALLogLevelError` | 既定のレベルでは、エラーが発生した場合にのみ情報を出力します |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  ライブラリ エントリ ポイント、パラメーターおよびさまざまなキーチェーン操作を含む |
|`MSALLogLevelVerbose`     |  API のトレース |

例:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>ログ メッセージの形式

MSAL ログ メッセージのメッセージ部分は、次の形式となります: `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

例:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

関連付け ID とタイム スタンプを指定することは、問題を追跡する場合に役立ちます。 タイムスタンプと関連付け ID に関する情報は、ログ メッセージで確認できます。 それらの取得元として唯一信頼できるのは MSAL ログ メッセ ージです。

## <a name="swifttabswift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>iOS および macOS 用の MSAL でのログ記録 - Swift

MSAL ログをキャプチャして独自のアプリケーションのログに組み込むようにコールバックを設定します。 コールバックのシグネチャ (Objective-C で表されます) は次のようになります。

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

### <a name="personal-data"></a>個人データ

既定では、MSAL は個人データ (PII) をキャプチャまたはログに記録しません。 このライブラリにより、アプリ開発者は MSALLogger クラスのプロパティを介して、この設定をオンにできます。 `pii.Enabled` をオンにすることで、アプリは機密性の高いデータを安全に処理し、規制要件に従う責任を負います。

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
|`MSALLogLevelVerbose`     |  API のトレース |

例:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>ログ メッセージの形式

MSAL ログ メッセージのメッセージ部分は、次の形式となります: `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

例:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

関連付け ID とタイム スタンプを指定することは、問題を追跡する場合に役立ちます。 タイムスタンプと関連付け ID に関する情報は、ログ メッセージで確認できます。 それらの取得元として唯一信頼できるのは MSAL ログ メッセ ージです。

## <a name="javatabjava"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL for Java でのログ記録

MSAL for Java では、SLF4J と互換性がある限り、アプリで既に使用しているログ記録ライブラリを使用できます。 MSAL for Java は、[Simple Logging Facade for Java](http://www.slf4j.org/) (SLF4J) を、さまざまなログ記録フレームワーク ([java.util.logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html)、[Logback](http://logback.qos.ch/)、および [Log4j](https://logging.apache.org/log4j/2.x/) など) の単純なファサードまたは抽象化として使用します。 SLF4J を使用すると、ユーザーはデプロイ時に目的のログ記録フレームワークをプラグインできます。

たとえば、アプリケーションでログ記録フレームワークとして Logback を使用するには、アプリケーションの Maven pom ファイルに Logback の依存関係を追加します。

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

次に、Logback 構成ファイルを追加します。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J は、デプロイ時に自動的に Logback にバインドされます。 MSAL のログはコンソールに書き込まれます。

他のログ記録フレームワークにバインドする方法については、[SLF4J のマニュアル](http://www.slf4j.org/manual.html)を参照してください。

### <a name="personal-and-organization-information"></a>個人情報と組織情報

既定では、MSAL のログ記録によって、個人データや組織データがキャプチャされたりログに記録されたりすることはありません。 次の例では、個人または組織のデータのログ記録は既定で無効になっています。

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

クライアント アプリケーション ビルダーで `logPii()` を設定することにより、個人データと組織データのログ記録を有効にします。 個人データや組織データのログ記録を有効にする場合、機密性の高いデータの安全な処理と規制上の要件の準拠については、アプリで責任を負う必要があります。

次の例では、個人または組織のデータのログ記録が有効になっています。

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="pythontabpython"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL for Python でのログ記録

MSAL Python でのログ記録には、`logging.info("msg")` などの標準的な Python ログ記録メカニズムが使用されます。次のように MSAL ログを構成できます ([username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32) で動作を確認できます)。

### <a name="enable-debug-logging-for-all-modules"></a>すべてのモジュールのデバッグ ログを有効にする

既定では、すべての Python スクリプトでログ記録は無効になっています。 Python スクリプト全体のすべてのモジュールに対してデバッグ ログを有効にする場合は、次のようにします。

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>MSAL ログ記録のみをサイレント状態にする

MSAL ライブラリのログ記録のみをサイレント状態にするには、Python スクリプト内の他のすべてのモジュールでデバッグ ログを有効にするときに、MSAL Python で使用されるロガーを無効にします。

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Python での個人と組織のデータ

MSAL for Python では、個人データおよび組織データはログに記録されません。 個人または組織のデータのログ記録を有効または無効にするプロパティはありません。

標準的な Python ログ記録を使用して任意のログを記録することができますが、機密データを安全に処理し、規制要件に従う責任があります。

Python でのログ記録の詳細については、Python の「[Logging HOWTO](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)」を参照してください。

---
