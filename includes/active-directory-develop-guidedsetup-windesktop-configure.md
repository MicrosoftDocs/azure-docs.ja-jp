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
ms.openlocfilehash: c4dc3d422e18d9ee41bf16ac3e6f22c3d7e466d7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181351"
---
## <a name="register-your-application"></a>アプリケーションの登録

2 つのどちらかの方法でアプリケーションを登録できます。

### <a name="option-1-express-mode"></a>オプション 1:簡易モード

次の手順を実行すると、アプリケーションをすばやく登録できます。
1. [Azure portal の [アプリケーションの登録]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs) に移動します。
1. アプリケーションの名前を入力し、 **[登録]** を選択します。
1. 画面の指示に従ってダウンロードし、1 回クリックするだけで、新しいアプリケーションが自動的に構成されます。

### <a name="option-2-advanced-mode"></a>オプション 2:詳細設定モード

アプリケーションを登録し、ソリューションにアプリケーション登録情報を追加するには、次の手順を実行します。
1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. ご利用のアカウントで複数のテナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的の Azure AD テナントに設定します。
1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページに移動します。
1. **[新規登録]** を選択します。
   - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: `Win-App-calling-MsGraph`)。
   - **[サポートされているアカウントの種類]** セクションで、 **[Accounts in any organizational directory and personal Microsoft accounts (for example, Skype, Xbox, Outlook.com)]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント (例: Skype、Xbox、Outlook.com)\)** を選択します。
   - **[登録]** を選択して、アプリケーションを作成します。
1. アプリのページの一覧から **[認証]** を選択します。
   1. **[リダイレクト URI]** セクションの、リダイレクト URI の一覧で:
   1. **[種類]** 列で、 **[パブリック クライアント (モバイルとデスクトップ)]** を選択します。
   1. **[リダイレクト URI]** 列に「`urn:ietf:wg:oauth:2.0:oob`」と入力します。
1. **[保存]** を選択します。
1. Visual Studio に移動し、*App.xaml.cs* ファイルを開き、`Enter_the_Application_Id_here` を登録してコピーしたアプリケーション ID に置き換えます。

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
