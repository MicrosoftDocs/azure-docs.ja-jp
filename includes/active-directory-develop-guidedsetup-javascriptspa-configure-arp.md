---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 7a6fa45d0f4ea09fbc84d3185aa0e58db165ac19
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59502955"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>アプリへのアプリケーション登録情報の追加

この手順では、アプリケーション登録情報のリダイレクト URL を構成してから、アプリケーション ID を JavaScript SPA アプリケーションに追加する必要があります。

### <a name="configure-redirect-url"></a>リダイレクト URL の構成

[`Redirect URL`] フィールドを Web サーバーに基づく index.html ページの URL に構成し、*[更新]* をクリックします。

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>リダイレクト URL を取得するための Visual Studio での手順
> リダイレクト URL を取得するには、次の手順に従います。
> 1. **ソリューション エクスプローラー**でプロジェクトを選択し、**[プロパティ]** ウィンドウを確認します。 **[プロパティ]** ウィンドウが表示されない場合は、**F4** キーを押します。
> 2. **URL** からクリップボードに値をコピーします。<br/> ![プロジェクトのプロパティ](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. このページの上部にある **[リダイレクト URL]** に値を貼り付けて、**[更新]** を選択します。

<p>

> #### <a name="setting-redirect-url-for-node"></a>Node のリダイレクト URL の設定
> Node.js の場合は、Web サーバーのポートを *server.js* ファイルで設定できます。 このチュートリアルでは、参照用にポート 30662 を使用しますが、その他に使用可能なポートも使用できます。 下記の手順に従って、アプリケーションの登録情報内にリダイレクト URL を設定します。<br/>
> `http://localhost:30662/` を、このページ上部の **[リダイレクト URL]** に設定するか、カスタム TCP ポートを使用している場合 (*[ポート]* はカスタム TCP ポートの番号) は `http://localhost:[port]/` を使用して、**[更新]** をクリックします

### <a name="configure-your-javascript-spa-application"></a>JavaScript SPA アプリケーションの構成

1. プロジェクトの設定中に作成された `index.html` ファイルで、アプリケーション登録情報を追加します。 `index.html` ファイルの本文の `<script></script>` タグ内で、先頭に次のコードを追加します。

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/common",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
