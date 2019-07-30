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
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2cdc6ea01e6c3555740102f319d0f4e8e4fc1c22
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181783"
---
## <a name="register-your-application"></a>アプリケーションの登録

アプリケーションを登録し、ソリューションにアプリケーション登録情報を追加する場合、2 つのオプションがあります。

### <a name="option-1-express-mode"></a>オプション 1:簡易モード

次の手順を実行すると、アプリケーションをすばやく登録できます。

1. 新しい [Azure portal の [アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) ウィンドウに移動します。
1. アプリケーションの名前を入力して **[登録]** をクリックします。
1. 画面の指示に従ってダウンロードし、1 回クリックするだけで、新しいアプリケーションが自動的に構成されます。

### <a name="option-2-advanced-mode"></a>オプション 2:詳細設定モード

アプリケーションを登録し、その登録情報をソリューションに手動で追加するには、次の手順を実行します。

1. Visual Studio に移動します。さらに、
   1. ソリューション エクスプローラーで、プロジェクトを選択してプロパティ ウィンドウを確認します (プロパティ ウィンドウが表示されない場合は F4 キーを押します)。
   1. [SSL 有効] を `True` に変更します。
   1. Visual Studio でプロジェクトを右クリックし、 **[プロパティ]** 、 **[Web]** タブの順に選択します。 *[サーバー]* セクションで、 *[プロジェクト URL]* が SSL URL になるように変更します。
   1. SSL URL をコピーします。 次の手順で、この URL を登録ポータルのリダイレクト URL の一覧のリダイレクト URL の一覧に追加します。<br/><br/>![プロジェクトのプロパティ](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. ご利用のアカウントで複数のテナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的の Azure AD テナントに設定します。
1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページに移動します。
1. **[新規登録]** を選択します。
1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。
   1. **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: `ASPNET-Tutorial`)。
   1. 手順 1 で Visual Studio からコピーした SSL URL (たとえば `https://localhost:44368/`) を **[応答 URL]** に追加し、 **[登録]** をクリックします。
1. **[認証]** メニューを選択し、 **[暗黙の付与]** の **[ID トークン]** を設定して、 **[保存]** を選択します。
1. `configuration\appSettings` セクションの下のルート フォルダーにある `web.config` に、以下のコードを追加します。

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. `ClientId` を、先ほど登録したアプリケーション ID に置き換えます。
1. `redirectUri` を、プロジェクトの SSL URL に置き換えます。
