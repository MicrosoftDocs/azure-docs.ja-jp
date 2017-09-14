---
title: "Azure AD v2 Windows デスクトップの概要 - 構成 | Microsoft Docs"
description: "Windows デスクトップ .NET (XAML) アプリケーションでアクセス トークンを取得し、Azure Active Directory v2 エンドポイントで保護されている API を呼び出す方法 | Microsoft Azure | Microsoft Azure"
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
ms.openlocfilehash: 1dfaa7ade664e43dcb9aa788b0197ca17e6ec4cc
ms.contentlocale: ja-jp


---

## <a name="create-an-application-express"></a>アプリケーションの作成 (簡易)
次の手順に従って *Microsoft アプリケーション登録ポータル*でアプリケーションを登録する必要があります。
1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure)でアプリケーションを登録します。
2.  アプリケーションの名前とお使いのメール アドレスを入力します
3.  ガイド付きセットアップのオプションがオンになっていることを確認します。
4.  手順に従ってアプリケーション ID を取得し、それをコードに貼り付けます。

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>ソリューションへのアプリケーション登録情報の追加 (上級)
次の手順に従って *Microsoft アプリケーション登録ポータル*でアプリケーションを登録する必要があります。
1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動して、アプリケーションを登録します
2. アプリケーションの名前とお使いのメール アドレスを入力します 
3. ガイド付きセットアップのオプションがオフになっていることを確認します。
4. [`Add Platform`] \(プラットフォームの追加) をクリックしてから [`Native Application`] \(ネイティブ アプリケーション) を選択し、[保存] をクリックします。
5. アプリケーション ID の GUID をコピーし、Visual Studio に戻って `App.xaml.cs` を開き、`your_client_id_here` を登録したアプリケーション ID に置き換えます。

```csharp
private static string ClientId = "your_application_id_here";
```

