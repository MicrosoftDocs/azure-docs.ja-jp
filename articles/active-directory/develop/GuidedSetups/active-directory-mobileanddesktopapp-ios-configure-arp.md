---
title: "Azure AD v2 iOS の概要 - 構成 (ARP) | Microsoft Docs"
description: "iOS (Swift) アプリケーションで、アクセス トークンを必要とする API を Azure Active Directory v2 エンドポイントから呼び出す方法"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 5ce5d26943839d9fe53508d396825289d77cf2f8
ms.contentlocale: ja-jp
ms.lasthandoff: 08/15/2017

---
## <a name="add-the-applications-registration-information-to-your-app"></a>アプリへのアプリケーション登録情報の追加

この手順では、アプリケーション ID をプロジェクトに追加する必要があります。

1.  `ViewController.swift` で、'`let kClientID`' で始まる行を次に変更します。
```swift
let kClientID = "[Enter the application Id here]"
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Ctrl キーを押しながら [<code>Info.plist</code>] をクリックしてコンテキスト メニューを表示し、<code>Open As</code>> <code>Source Code</code>
 をクリックします</li>
<li>
ルート ノード <code>dict</code> の下に次の内容を追加します。
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Enter the application Id here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```

