---
title: MSAL for Android でのエラーと例外のログ記録
titleSuffix: Microsoft identity platform
description: MSAL for Android でエラーと例外をログに記録する方法について説明します。
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
ms.openlocfilehash: ce0929adbb2b0213cfd4ee61fe795a2d5f33c648
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954886"
---
# <a name="logging-in-msal-for-android"></a>Android 用の MSAL でのログ記録

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

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

## <a name="next-steps"></a>次のステップ

追加のコード サンプルについては、「[Microsoft ID プラットフォームのコード サンプル](sample-v2-code.md)」を参照してください。