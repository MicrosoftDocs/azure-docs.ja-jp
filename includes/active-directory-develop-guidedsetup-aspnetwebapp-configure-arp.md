---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 5940195207f85d8011f61336c0318e456c2a8a4c
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58214020"
---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>アプリケーションの登録情報を使用した ASP.NET Web アプリの構成

この手順では、SSL を使用するようにプロジェクトを構成した後、SSL URL を使用してアプリケーションの登録情報を構成します。 その後、*web.config* を介して、アプリケーションの登録情報をソリューションに追加します。

1. ソリューション エクスプローラーで、プロジェクト選択して [`Properties`] \(プロパティ) ウィンドウを確認します ([プロパティ] ウィンドウが表示されない場合は F4 キーを押します)。
2. `SSL Enabled` を `True` に変更します。
3. 上の画像にある [`SSL URL`] の値をコピーしてこのページの最上部にある [`Redirect URL`] \(リダイレクト URL) フィールドに貼り付け、*[更新]* をクリックします。<br/><br/>![プロジェクトのプロパティ](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4. `configuration\appSettings` セクションのルート フォルダーにあるファイル `web.config` に、以下のコードを追加します。

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
