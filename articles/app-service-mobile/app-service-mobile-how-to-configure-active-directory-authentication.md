---
title: "App Services アプリケーションに Azure Active Directory 認証を構成する方法"
description: "App Services アプリケーションに Azure Active Directory 認証を構成する方法について説明します。"
author: mattchenderson
services: app-service
documentationcenter: 
manager: adrianha
editor: 
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 1f8813370995a7a55c9d87b8ec1b60ae0fe623f5
ms.lasthandoff: 03/09/2017


---
# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、認証プロバイダーとして Azure Active Directory を使用するように Azure App Services を構成する方法を示します。

## <a name="express"> </a>高速設定を使用して Azure Active Directory を構成する
1. [Azure ポータル]で、アプリケーションに移動します。 **[設定]**、**[認証/承認]** の順にクリックします。
2. [認証/承認] 機能が有効になっていない場合は、スイッチを **[オン]**に切り替えます。
3. **[Azure Active Directory]** をクリックし、**[管理モード]** の **[高速]** をクリックします。
4. **[OK]** をクリックして、Azure Active Directory にアプリケーションを登録します。 これで新しい登録が作成されます。 代わりに既存の登録を選択する場合は、 **[既存のアプリケーションの選択]** をクリックし、テナント内で以前に作成した登録の名前を検索します。
   登録をクリックして選択し、 **[OK]**をクリックします。 Azure Active Directory 設定ブレードで **[OK]** をクリックします。
   
   ![][0]
   
   App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。
5. (省略可能) サイトに対するアクセスを、Azure Active Directory で認証されたユーザーに限定するには、**[要求が認証されない場合に実行するアクション]** を **[Azure Active Directory でのログイン]** に設定します。 この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Azure Active Directory にリダイレクトされます。
6. **[保存]**をクリックします。

これで、アプリケーションで認証に Azure Active Directory を使用する準備ができました。

## <a name="advanced"> </a>(代替方法) 詳細設定を使用して Azure Active Directory を手動で構成する
構成設定を手動で行うこともできます。 これは、使用する AAD テナントが Azure へのサインインに使用するテナントと異なる場合に推奨されるソリューションです。 構成を完了するには、まず Azure Active Directory で登録を作成し、登録の一部の詳細を App Service に提供する必要があります。

### <a name="register"> </a>Azure Active Directory にアプリケーションを登録する
1. [Azure ポータル]にログオンし、アプリケーションに移動します。 **[URL]**をコピーします。 これを使用して、Azure Active Directory アプリケーションを構成します。
2. [[Active Directory]] にサインインして、 **Active Directory**に移動します。
   
    ![][2]
3. ディレクトリを選択し、上部の **[アプリケーション]** タブを選択します。 下部の **[追加]** をクリックして、新しいアプリの登録を作成します。
4. **[組織で開発中のアプリケーションを追加]**をクリックします。
5. アプリケーションの追加ウィザードで、アプリケーションの**名前**を入力し、種類として **[Web アプリケーションや Web API]** をクリックします。 その後、クリックして続行します。
6. **[サインオン URL]** ボックスに、先ほどコピーしたアプリケーション URL を貼り付けます。 **[アプリケーション ID URI]** ボックスにも同じ URL を入力します。 その後、クリックして続行します。
7. アプリケーションが追加されたら **[構成]** タブをクリックします。 **[シングル サインオン]** の **[応答 URL]** を編集して、アプリケーションの URL の末尾にパス */.auth/login/aad/callback* を追加します。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/aad/callback`」のように入力します。 HTTPS スキームを使用していることを確認します。
   
    ![][3]
8. **[保存]**をクリックします。 その後、アプリケーションの **クライアント ID** をコピーします。 後で、これを使用するようにアプリケーションを構成します。
9. 下部のコマンド バーで **[エンドポイントの表示]** をクリックして、**[フェデレーション メタデータ ドキュメント]** の URL をコピーし、そのドキュメントをダウンロードするか、ブラウザーでそのドキュメントに移動します。
10. ルートの **EntityDescriptor** 要素内に、テナント固有の GUID ("テナント ID") が後に続く `https://sts.windows.net/` の形式の **entityID** 属性があります。 この値をコピーします。これは**発行者の URL** として機能します。 後で、これを使用するようにアプリケーションを構成します。

### <a name="secrets"> </a>Azure Active Directory の情報をアプリケーションに追加する
1. [Azure ポータル]に戻り、アプリケーションに移動します。 **[設定]**、**[認証/承認]** の順にクリックします。
2. [認証/承認] 機能が有効になっていない場合は、スイッチを **[オン]**に切り替えます。
3. **[Azure Active Directory]** をクリックし、**[管理モード]** の **[詳細設定]** をクリックします。 以前に取得したクライアント ID と発行者の URL の値を貼り付けます。 次に、 **[OK]**をクリックします
   
   ![][1]
   
   App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。
4. (省略可能) サイトに対するアクセスを、Azure Active Directory で認証されたユーザーに限定するには、**[要求が認証されない場合に実行するアクション]** を **[Azure Active Directory でのログイン]** に設定します。 この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Azure Active Directory にリダイレクトされます。
5. **[保存]**をクリックします。

これで、アプリケーションで認証に Azure Active Directory を使用する準備ができました。

## <a name="optional-configure-a-native-client-application"></a>(省略可能) ネイティブ クライアント アプリケーションの構成
Azure Active Directory では、ネイティブ クライアントを登録して、アクセス許可のマッピングをさらに詳細に制御することもできます。 **Active Directory Authentication Library**などのライブラリを使用してログインを実行する場合は、この処理が必要です。

1. **Azure クラシック ポータル** の [[Active Directory]]に移動します。
2. ディレクトリを選択し、上部の **[アプリケーション]** タブを選択します。 下部の **[追加]** をクリックして、新しいアプリの登録を作成します。
3. **[組織で開発中のアプリケーションを追加]**をクリックします。
4. アプリケーションの追加ウィザードで、アプリケーションの**名前**を入力し、種類として **[ネイティブ クライアント アプリケーション]** をクリックします。 その後、クリックして続行します。
5. **[リダイレクト URI]** に、HTTPS スキームを使用してサイトの */.auth/login/done* エンドポイントを入力します。 この値は、*https://contoso.azurewebsites.net/.auth/login/done* のようにする必要があります。 Windows アプリケーションを作成する場合は、 [パッケージ SID](app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) を URI として使用します。
6. ネイティブ アプリケーションが追加されたら **[構成]** タブをクリックします。 **[クライアント ID]** を検索し、その値をメモします。
7. ページを **[他のアプリケーションに対するアクセス許可]** セクションが表示されるまで下へスクロールし、**[アプリケーションの追加]** ボタンをクリックします。
8. 前に登録した Web アプリケーションを検索し、プラス アイコンをクリックします。 次に、チェックをクリックしてダイアログ ボックスを閉じます。 Web アプリケーションが見つからない場合は、その登録に移動して、新しい応答 URL (現在の URL の HTTP バージョンなど) を追加し、[保存] をクリックしてから、これらの手順を繰り返します。アプリケーションが一覧に表示されるはずです。
9. 追加したばかりの新しいエントリで、**[デリゲートされたアクセス許可]** ドロップダウンを開き、**[<アプリ名> にアクセス]** を選択します。 その後、 **[保存]**をクリックします。

以上の手順で、App Service アプリケーションにアクセスできるネイティブ クライアント アプリケーションが構成されます。

## <a name="related-content"> </a>関連コンテンツ
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Azure ポータル]: https://portal.azure.com/
[[Active Directory]]: https://manage.windowsazure.com/
[alternative method]:#advanced

