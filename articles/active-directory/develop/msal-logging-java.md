---
title: MSAL for Java でのエラーと例外のログ記録
titleSuffix: Microsoft identity platform
description: MSAL for Java でエラーと例外をログに記録する方法について説明します。
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
ms.openlocfilehash: d3fa13a6751b2d8acf1fc99aecbf174f1823fb0b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954920"
---
# <a name="logging-in-msal-for-java"></a>MSAL for Java でのログ記録

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

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

## <a name="next-steps"></a>次のステップ

追加のコード サンプルについては、「[Microsoft ID プラットフォームのコード サンプル](sample-v2-code.md)」を参照してください。