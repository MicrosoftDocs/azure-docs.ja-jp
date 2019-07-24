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
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 5eaee4f932c4e42f6fed3d839314346b3a93f360
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799544"
---
## <a name="register-your-application"></a>アプリケーションの登録

2 つのどちらかの方法でアプリケーションを登録できます。

### <a name="option-1-express-mode"></a>オプション 1:簡易モード

次の手順を実行すると、アプリケーションをすばやく登録できます。
1. [Azure portal の [アプリケーションの登録]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs) に移動します。
1. アプリケーションの名前を入力し、**[登録]** を選択します。
1. 画面の指示に従ってダウンロードし、1 回クリックするだけで、新しいアプリケーションが自動的に構成されます。

### <a name="option-2-advanced-mode"></a>オプション 2:詳細設定モード

アプリケーションを登録し、ソリューションにアプリケーション登録情報を追加するには、次の手順を実行します。
1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. ご利用のアカウントで複数のテナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的の Azure AD テナントに設定します。
1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページに移動します。
1. **[新規登録]** を選択します。
   - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: `Win-App-calling-MsGraph`)。
   - **[サポートされているアカウントの種類]** セクションで、**[Accounts in any organizational directory and personal Microsoft accounts (for example, Skype, Xbox, Outlook.com)]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント (例: Skype、Xbox、Outlook.com)\)** を選択します。
   - **[登録]** を選択して、アプリケーションを作成します。
1. アプリのページの一覧から **[認証]** を選択します。
1. **[リダイレクト URL]** セクションで、**[パブリック クライアント (モバイル、デスクトップ) に推奨されるリダイレクト URI]** セクションを探し、**["urn:ietf:wg:oauth:2.0:oob]** を選択します。
1. **[保存]** を選択します。
1. Visual Studio に移動し、*App.xaml.cs* ファイルを開き、`Enter_the_Application_Id_here` を登録してコピーしたアプリケーション ID に置き換えます。

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
