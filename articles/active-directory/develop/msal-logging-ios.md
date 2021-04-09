---
title: iOS および macOS 用の MSAL でのエラーと例外のログ記録
titleSuffix: Microsoft identity platform
description: iOS および macOS 用の MSAL でエラーと例外をログに記録する方法について説明します。
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
ms.openlocfilehash: ee3837b75d586238e7ca6ac85434cc56f592929d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98763270"
---
# <a name="logging-in-msal-for-iosmacos"></a>iOS および macOS 用の MSAL でのログ記録

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="objective-c"></a>[Objective-C](#tab/objc)

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

次に例を示します。

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

既定では、MSAL は個人データのキャプチャおよびログへの記録を行いません。 このライブラリにより、アプリ開発者は MSALLogger クラスのプロパティを介して、この設定をオンにできます。 `pii.Enabled` をオンにすることで、アプリは機密性の高いデータを安全に処理し、規制要件に従う責任を負います。

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>ログ記録のレベル

iOS および macOS 用の MSAL を使用してログを記録するときにログ レベルを設定するには、次のいずれかの値を使用します。

|Level  |説明 |
|---------|---------|
| `MSALLogLevelNothing`| すべてのログ記録を無効にします |
| `MSALLogLevelError` | 既定のレベルでは、エラーが発生した場合にのみ情報を出力します |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  ライブラリ エントリ ポイント、パラメーターおよびさまざまなキーチェーン操作を含む |
|`MSALLogLevelVerbose`     |  API のトレース |

次に例を示します。

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>ログ メッセージの形式

MSAL ログ メッセージのメッセージ部分は、次の形式となります: `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

次に例を示します。

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

関連付け ID とタイム スタンプを指定することは、問題を追跡する場合に役立ちます。 タイムスタンプと関連付け ID に関する情報は、ログ メッセージで確認できます。 それらの取得元として唯一信頼できるのは MSAL ログ メッセ ージです。

## <a name="swift"></a>[Swift](#tab/swift)

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

次に例を示します。

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

既定では、MSAL は個人データのキャプチャおよびログへの記録を行いません。 このライブラリにより、アプリ開発者は MSALLogger クラスのプロパティを介して、この設定をオンにできます。 `pii.Enabled` をオンにすることで、アプリは機密性の高いデータを安全に処理し、規制要件に従う責任を負います。

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>ログ記録のレベル

iOS および macOS 用の MSAL を使用してログを記録するときにログ レベルを設定するには、次のいずれかの値を使用します。

|Level  |説明 |
|---------|---------|
| `MSALLogLevelNothing`| すべてのログ記録を無効にします |
| `MSALLogLevelError` | 既定のレベルでは、エラーが発生した場合にのみ情報を出力します |
| `MSALLogLevelWarning` | 警告 |
| `MSALLogLevelInfo` |  ライブラリ エントリ ポイント、パラメーターおよびさまざまなキーチェーン操作を含む |
|`MSALLogLevelVerbose`     |  API のトレース |

次に例を示します。

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>ログ メッセージの形式

MSAL ログ メッセージのメッセージ部分は、次の形式となります: `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

次に例を示します。

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

関連付け ID とタイム スタンプを指定することは、問題を追跡する場合に役立ちます。 タイムスタンプと関連付け ID に関する情報は、ログ メッセージで確認できます。 それらの取得元として唯一信頼できるのは MSAL ログ メッセ ージです。

---

## <a name="next-steps"></a>次のステップ

追加のコード サンプルについては、「[Microsoft ID プラットフォームのコード サンプル](sample-v2-code.md)」を参照してください。