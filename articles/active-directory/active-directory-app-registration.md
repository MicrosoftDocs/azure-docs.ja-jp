---
title: "Azure Active Directory アプリケーションの登録 | Microsoft Docs"
description: "この記事では、Azure Portal を使用して、Azure Active Directory にアプリケーションを登録する方法について説明します"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 7dc7b89f-653f-405a-b5f4-2c1288720c15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2016
ms.author: priyamo
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d2dd84007832c56f5893f0ea9f5effcf5ec5816b
ms.lasthandoff: 03/29/2017

---

# <a name="register-your-application-with-your-azure-active-directory-tenant"></a>Azure Active Directory テナントにアプリケーションを登録する

Azure Portal を使用して、Azure Active Directory (Azure AD) テナントにアプリケーションを登録できます。 これにより、アプリケーションのアプリケーション ID が作成され、トークンを受信できるようになります。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ページの右上隅のアカウント名を選択して、Azure AD テナントを選択します。
3. 左側のナビゲーション ウィンドウで **[More Services (その他のサービス)]** を選択し、**[アプリの登録]**、**[追加]** の順にクリックします。
4. 画面の指示に従い、新しいアプリケーションを作成します。 Web アプリケーションまたはネイティブ アプリケーションの具体的な例については、[クイック スタート](active-directory-developers-guide.md)をご覧ください。
  * Web アプリケーションの場合は、**サインオン URL** (ユーザーが `http://localhost:12345` などにサインインできる、アプリのベース URL) を入力します。
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * ネイティブ アプリケーションの場合は、**リダイレクト URI** (Azure AD がトークン応答を返すために使用する) を入力します。 アプリケーション固有の値 (たとえば、 `http://MyFirstAADApp`
5. 登録が完了すると、Azure AD により、アプリケーションに一意のクライアント ID (アプリケーション ID) が割り当てられます。

## <a name="update-application-settings-from-the-azure-portal"></a>Azure Portal からのアプリケーション設定の更新

Azure Portal を使用して、既存のアプリケーションの設定を簡単に変更できます。 たとえば、Azure AD がトークン応答を発行する場所である応答 URL を構成できます。 また、アプリケーションの Microsoft Graph API へのアクセスの許可など、他のアプリケーションに対するアクセス許可を構成することもできます。 これらすべては、アプリケーションの設定ページで行うことができます。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ページの右上隅のアカウント名を選択して、Azure AD テナントを選択します。
3. 左側のナビゲーション ウィンドウで **[More Services (その他のサービス)]** を選択して、**[アプリの登録]** をクリックし、一覧からアプリケーションを選択します。
4. **[設定]** をクリックして、アプリケーションの設定ページを開きます。
  * **[プロパティ]** ページでは、アプリケーションに関する全般的な情報を変更できます。 これには、アプリケーションの名前、サインオン URL、ログアウト URL などが含まれます。
  * **応答 URL** ページでは、Azure AD がトークン応答を送信する場所である応答 URL を追加できます。
  * **[所有者]** ページでは、アプリケーションの所有者を追加できます。
  * **[アクセス許可]** ページでは、アプリのアクセス許可を構成できます。 たとえば、Microsoft Graph API にアクセスするには、**[追加]** をクリックし、API セレクターの **[Microsoft Graph]** を選択してから、必要なアクセス許可 (**ディレクトリ データの読み取り**など) を選択します。
  * **[キー]** ページでは、アプリケーション シークレットを追加できます。 シークレットは作成直後にしか表示されないため、後で使用できるようにコピーしておきます。

## <a name="use-the-inline-manifest-editor"></a>インライン マニフェスト エディターの使用

インラインのマニフェスト エディターを使用すると、Azure Portal に直接公開されていない特定のアプリケーション プロパティを変更できます。 たとえば、これを使用して、アプリケーションのアプリ ID URI を変更するか、既定の承認許可コード フローではなく OAuth2.0 暗黙的フローを有効にすることができます。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ページの右上隅のアカウント名を選択して、Azure AD テナントを選択します。
3. 左側のナビゲーション ウィンドウで **[More Services (その他のサービス)]** を選択して、**[アプリの登録]** をクリックし、一覧からアプリケーションを選択します。
4. アプリケーション ページで **[マニフェスト]** をクリックしてインライン マニフェスト エディターを開きます。
5. マニフェストに直接変更を加え、準備ができたら保存します。 または、マニフェストをダウンロードして、お気に入りのエディターで開いたり、更新されたマニフェストをアップロードしたりすることもできます。

## <a name="next-steps"></a>次のステップ

1. Azure AD を使用した認証を実行するアプリケーションの詳細なチュートリアルについては、[クイック スタート](active-directory-developers-guide.md)をご覧ください。
2. コード サンプルの完全な一覧については、[GitHub](https://github.com/azure-samples) をご覧ください。

