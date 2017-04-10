---
title: "要求に対応するアプリ - Azure AD アプリ プロキシ | Microsoft Docs"
description: "ADFS 要求を受け入れてユーザーの安全なリモート アクセスを実現するオンプレミス ASP.NET アプリケーションを発行する方法。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 91e6211b-fe6a-42c6-bdb3-1fff0312db15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 081e45e0256134d692a2da7333ddbaafc7366eaa
ms.openlocfilehash: ff07a52f6a503f07f5919b63f345878571742cac


---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>アプリケーション プロキシで要求に対応するアプリケーションを利用する
要求に対応するアプリケーションは、Security Token Service (STS) へのリダイレクトを行います。STS は、ユーザーをアプリケーションにリダイレクトする前に、ユーザーにトークンの代わりの資格情報を要求します。 これらのリダイレクトで機能するようにアプリケーション プロキシを有効にするには、次の手順を実行する必要があります。

## <a name="prerequisites"></a>前提条件
この手順を実行する前に、要求に対応するアプリのリダイレクト先の STS が、オンプレミス ネットワークの外部でも使用できるようになっていることを確認してください。

## <a name="azure-classic-portal-configuration"></a>Azure クラシック ポータルの構成
1. 「 [アプリケーション プロキシを使用したアプリケーションの発行](active-directory-application-proxy-publish.md)」で説明されている手順に従って、アプリケーションを発行します。
2. アプリケーションの一覧で、要求に対応するアプリケーションを選択し、 **[構成]**をクリックします。
3. **[事前認証方法]** で **[パススルー]** を選択した場合は、**[外部 URL]** スキームとして **[HTTPS]** を選択してください。
4. **[事前認証方法]** として **Azure Active Directory** を選択した場合は、**[内部認証方法]** として **[なし]** を選択してください。

## <a name="adfs-configuration"></a>ADFS 構成
1. [ADFS 管理] を開きます。
2. **[証明書利用者信頼]** に移動し、アプリケーション プロキシで発行しているアプリを右クリックして、**[プロパティ]** を選択します。  

   ![[証明書利用者信頼] でアプリ名を右クリックしているスクリーンショット](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  

3. **[エンドポイント]** タブの **[エンドポイントの種類]** で、**[WS-Federation]** を選択します。
4. **[信頼された URL]** で、アプリケーション プロキシの **[外部 URL]** で入力した URL を入力し、**[OK]** をクリックします。  

   ![エンドポイントを追加し、信頼された URL 値を設定しているスクリーン ショット](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>次のステップ
* [シングル サインオンを有効にする](active-directory-application-proxy-sso-using-kcd.md)
* [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](active-directory-application-proxy-troubleshoot.md)
* [ネイティブ クライアント アプリケーションからプロキシ アプリケーションを操作できるようにする](active-directory-application-proxy-native-client.md)





<!--HONumber=Feb17_HO1-->


