---
title: Azure AD .NET プロトコルの概要 | Microsoft Docs
description: Azure AD を利用してテナントの Web アプリケーションと Web API へのアクセスを承認するために HTTP メッセージを使用する方法。
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/18/2018
ms.author: priyamo
ms.openlocfilehash: d97aa34f8e76a1ea49841f26534b6a5403e6b5d7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125364"
---
## <a name="register-your-application-with-your-ad-tenant"></a>AD テナントへのアプリケーションの登録
まず、Azure Active Directory (Azure AD) テナントにアプリケーションを登録する必要があります。 これにより、アプリケーションのアプリケーション ID を取得でき、トークンを受信できるようになります。

* [Azure Portal](https://portal.azure.com) にサインインします。
* ページ右上隅にあるアカウントをクリックして Azure AD テナントを選択し、**[ディレクトリの切り替え]** ナビゲーションをクリックして、適切なテナントを選択します。 
  * アカウントの下の Azure AD テナントが 1 つのみの場合、または適切な Azure AD テナントを既に選択している場合は、この手順をスキップします。
* 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** をクリックします。
* **[アプリの登録]** をクリックし、**[新しいアプリケーションの登録]** をクリックします。
* 画面の指示に従い、新しいアプリケーションを作成します。 このチュートリアルでは、Web アプリケーションであるかネイティブ アプリケーションであるかは重要ではありませんが、Web アプリケーションまたはネイティブ アプリケーションの具体的な例を確認する場合は、[クイック スタート](../articles/active-directory/develop/v1-overview.md)をチェックしてください。
  * Web アプリケーションの場合は、**サインオン URL** (ユーザーが `http://localhost:12345` などにサインインできる、アプリのベース URL) を入力します。
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * ネイティブ アプリケーションの場合は、**リダイレクト URI**(Azure AD がトークン応答を返すために使用する) を入力します。 アプリケーション固有の値 (たとえば、 `http://MyFirstAADApp`
* 登録が完了すると、Azure AD により、アプリケーションに一意のクライアント ID (**アプリケーション ID**) が割り当てられます。 この値は次のセクションで必要になるので、アプリケーション ページからコピーします。
* Azure Portal でアプリケーションを見つけるには、**[アプリの登録]** をクリックし、**[アプリケーションをすべて表示]** をクリックします。
