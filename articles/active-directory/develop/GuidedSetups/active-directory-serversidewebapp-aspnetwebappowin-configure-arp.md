---
title: "Azure AD v2 の ASP.NET Web サーバーの概要 - 構成 | Microsoft Docs"
description: "OpenID Connect 標準を使用する従来の Web ブラウザー ベースのアプリケーションで、ASP.NET ソリューションに Microsoft のサインインを実装する"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 8a1650a65e7980f4a13fa4edc7918b0099bb5464
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017


---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>アプリケーションの登録情報を使用した ASP.NET Web アプリの構成

この手順では、SSL を使用するようにプロジェクトを構成した後、SSL URL を使用してアプリケーションの登録情報を構成します。 その後、*web.config* を介して、アプリケーションの登録情報をソリューションに追加します。

1.  ソリューション エクスプローラーで、プロジェクト選択して [`Properties`] \(プロパティ) ウィンドウを確認します ([プロパティ] ウィンドウが表示されない場合は F4 キーを押します)。
2.  `SSL Enabled` を `True` に変更します。
3.  上の画像にある [`SSL URL`] の値をコピーしてこのページの最上部にある [`Redirect URL`] \(リダイレクト URL) フィールドに貼り付け、*[更新]* をクリックします。<br/><br/>![プロジェクトのプロパティ](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
4.  `configuration\appSettings` セクションのルート フォルダーにあるファイル `web.config` に、以下のコードを追加します。

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```

