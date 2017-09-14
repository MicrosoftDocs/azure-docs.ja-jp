---
title: "Azure AD v2 の ASP.NET Web サーバーの概要 - 構成 | Microsoft Docs"
description: "OpenID 接続を使用して、従来の Web ブラウザー ベースのアプリケーションの ASP.NET ソリューションで Microsoft のサインインを実装します"
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
ms.openlocfilehash: 0c627802ccfba230dcde2dafffee26cb1c895791
ms.contentlocale: ja-jp


---

## <a name="create-an-application-express"></a>アプリケーションの作成 (簡易)
次の手順に従って *Microsoft アプリケーション登録ポータル*でアプリケーションを登録する必要があります。
1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)でアプリケーションを登録します。
2.  アプリケーションの名前とお使いのメール アドレスを入力します
3.  ガイド付きセットアップのオプションがオンになっていることを確認します
4.  指示に従って、アプリケーションにリダイレクト URL を追加します

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>ソリューションへのアプリケーション登録情報の追加 (上級)
次の手順に従って *Microsoft アプリケーション登録ポータル*でアプリケーションを登録する必要があります。
1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動して、アプリケーションを登録します
2. アプリケーションの名前とお使いのメール アドレスを入力します 
3.  ガイド付きセットアップのオプションがオフになっていることを確認します
4.  [`Add Platform`] \(プラットフォームの追加) をクリックし、[`Web`] を選択します
5.  Visual Studio に戻り、ソリューション エクスプローラーでプロジェクトを選択して [プロパティ] ウィンドウを確認します ([プロパティ] ウィンドウが表示されない場合は、F4 キーを押します)
6.  [SSL 有効] を `True` に変更します
7.  SSL URL をコピーし、登録ポータルのリダイレクト URL の一覧にこの URL を追加します<br/><br/>![プロジェクトのプロパティ](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />
8.  `configuration\appSettings` セクションの下のルート フォルダーにある `web.config` に、以下のコードを追加します。

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
<!-- Workaround for Docs conversion bug -->
<ol start="9">
<li>
`ClientId` を、さきほど登録したアプリケーション ID に置き換えます
</li>
<li>
`redirectUri` を、プロジェクトの SSL URL に置き換えます
</li>
</ol>
<!-- End Docs -->

