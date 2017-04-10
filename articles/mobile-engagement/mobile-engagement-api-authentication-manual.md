---
title: "Mobile Engagement REST API を使用した認証 - 手動の設定"
description: "Mobile Engagement REST API の認証を手動で設定する方法について説明します"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: c6190a5a5aba325b15aef97610c804f5441ef7ad
ms.openlocfilehash: 9d6132e1a01be489b8e8e28a0219cf8a0b50b318


---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Mobile Engagement REST API を使用した認証 - 手動の設定
これは、「 [Azure Mobile Engagement - 認証への API の使用](mobile-engagement-api-authentication.md)」の付録のドキュメントです。 このドキュメントを先に読み、内容を把握してください。 このドキュメントでは、Azure ポータルを使用して Mobile Engagement REST API の認証を設定するために 1 回限りの設定を行う代替の方法について説明します。 

> [!NOTE]
> 以下の手順はこの [Active Directory ガイド](../azure-resource-manager/resource-group-create-service-principal-portal.md) に基づいており、Mobile Engagement API の認証に必要なものに対応してカスタマイズしています。 そのため、以下の手順を詳しく理解したい場合は、このガイドを参照してください。 
> 
> 

1. [従来のポータル](https://manage.windowsazure.com/)によって Azure アカウントにログインします。
2. 左側のペインで **[Active Directory]** を選択します。
   
     ![Active Directory の選択][1]
3. Azure ポータルで **[既定の Active Directory]** を選択します。 
   
     ![ディレクトリの選択][2]
   
   > [!IMPORTANT]
   > このアプローチは、アカウントの既定の Active Directory で作業する場合にのみ機能し、アカウントで作成した Active Directory でこれを実行する場合は機能しません。 
   > 
   > 
4. ディレクトリ内のアプリケーションを表示するには、 **[アプリケーション]**をクリックします。
   
     ![アプリケーションの表示][3]
5. **[追加]**をクリックします。 
   
     ![アプリケーションの追加][4]
6. **[組織で開発中のアプリケーションを追加]**
   
     ![新規アプリケーション][5]
7. アプリケーションの名前を入力し、アプリケーションの種類を **[WEB アプリケーションや WEB API]** として選択し、[次へ] ボタンをクリックします。
   
     ![アプリケーションの名前指定][6]
8. **[サインオン URL]** と **[アプリケーション ID/URI]** に任意のダミー URL を指定できます。 ここのシナリオではそれらを使用せず、URL 自体は検証されません。  
   
     ![アプリケーションのプロパティ][7]
9. この最後に、次のように以前に入力した名前を持つ AAD アプリケーションが表示されます。 これは **AD\_APP\_NAME** で、これを書き留めておきます。  
   
     ![アプリケーション名][8]
10. アプリケーション名をクリックし、 **[構成]**をクリックします。
    
      ![アプリケーションの構成][9]
11. API 呼び出しの **CLIENT\_ID** として使用されるクライアント ID を書き留めておきます。 
    
     ![アプリケーションの構成][10]
12. **[キー]** セクションまで下へスクロールして、できれば期間が 2 年間 (有効期限) のキーを追加し、**[保存]** をクリックします。 
    
     ![アプリケーションの構成][11]
13. キーに対して表示されている値をすぐにコピーします。それはこの時点しか表示されず、保存されないため、再び表示されることはありません。 なくしてしまった場合は、新しいキーを生成する必要があります。 これは、API 呼び出しの **CLIENT_SECRET** になります。 
    
     ![アプリケーションの構成][12]
    
    > [!IMPORTANT]
    > このキーは、指定した期間の最後に期限切れになるため、その時が来たら更新してください。そうしないと、API 認証が機能しなくなります。 このキーが侵害されていると思われる場合は、削除して、再作成することもできます。
    > 
    > 
14. **[エンドポイントの表示]** ボタンをクリックすると、**[アプリケーションのエンドポイント]** ダイアログ ボックスが開きます。 
    
    ![][13]
15. [アプリケーションのエンドポイント] ダイアログ ボックスから、 **[Oauth 2.0 トークン エンドポイント]**をコピーします。 
    
    ![][14]
16. このエンドポイントは、次の形式になります。ここで、URL の GUID は **TENANT_ID** であるため、これを書き留めておきます。 
    
        https://login.microsoftonline.com/<GUID>/oauth2/token
17. ここで、このアプリケーションのアクセス許可の構成に進みます。 このために、 [Azure ポータル](https://portal.azure.com)を開く必要があります。 
18. **[リソース グループ]** をクリックし、**[Mobile Engagement]** リソース グループを見つけます。  
    
    ![][15]
19. **[Mobile Engagement]** リソース グループをクリックして、ここの **[設定]** ブレードに移動します。 
    
    ![][16]
20. [設定] ブレードの **[ユーザー]** をクリックし、**[追加]** をクリックして、ユーザーを追加します。 
    
    ![][17]
21. **[ロールの選択]**
    
    ![][18]
22. **[所有者]**
    
    ![][19]
23. 検索ボックスで、アプリケーションの名前 **AD\_APP\_NAME** を検索します。 既定では、これは表示されません。 見つかったら、それを選択し、ブレードの下部の **[選択]** をクリックします。 
    
    ![][20]
24. **[アクセスを追加]** ブレードで、それが **1 ユーザー、0 グループ**と表示されます。 このブレードの **[OK]** をクリックして変更を確認します。 
    
    ![][21]

これで必要な AAD 構成が完了し、API を呼び出すためにすべて設定されました。 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png






<!--HONumber=Dec16_HO4-->


