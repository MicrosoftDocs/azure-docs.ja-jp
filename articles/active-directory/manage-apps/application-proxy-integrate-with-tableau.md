---
title: Azure Active Directory アプリケーション プロキシと Tableau | Microsoft Docs
description: Azure Active Directory (Azure AD) のアプリケーション プロキシを使用して、Tableau 配置にリモート アクセスを提供する方法を説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6720a5ad963bc73e11ef7b46150e946521928c01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258627"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory アプリケーション プロキシと Tableau 

Azure Active Directory アプリケーション プロキシと Tableau は連携動作し、アプリケーション プロキシを使用して Tableau 配置用のリモート アクセスを容易に提供できるようにします。 この記事では、このシナリオの構成方法について説明します。  

## <a name="prerequisites"></a>前提条件 

このシナリオでは、次が前提となっています。

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure)が構成されている 

- [アプリケーション プロキシ コネクタ](application-proxy-add-on-premises-application.md)がインストールされている 

 
## <a name="enabling-application-proxy-for-tableau"></a>アプリケーション プロキシを Tableau に有効化する 

アプリケーション プロキシは、Tableau が適切に動作するために必要な OAuth 2.0 付与フローをサポートしています。 つまり、このアプリケーションを有効にするために必要な特別な手順は必要なくなりました。ただし、以下の発行手順に従って設定することが必要です。


## <a name="publish-your-applications-in-azure"></a>アプリケーションを Azure に発行する 

Tableau を発行するには、Azure Portal でアプリケーションを発行する必要があります。

次の場合:

- ステップ 1 から 8 の詳細な手順については、[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](application-proxy-add-on-premises-application.md)を参照してください。 
- アプリケーション プロキシ フィールドの Tableau 値を検索する方法については、Tableau ドキュメントを参照してください。  

**アプリケーションを発行する**: 


1. アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。 

2. **[Azure Active Directory] > [エンタープライズ アプリケーション]** を選択します。 

3. ブレード上部の **[追加]** を選択します。 

4. **[オンプレミスのアプリケーション]** を選択します。 

5. 新しいアプリに関する情報を必須フィールドに入力します。 次のガイダンスに従って設定してください。 

    - **[内部 URL]**: このアプリケーションは、Tableau URL そのものである内部 URL を持っている必要があります。 たとえば、「 `https://adventure-works.tableau.com` 」のように入力します。 

    - **[事前認証方法]** : Azure Active Directory (推奨ですが必須ではありません) 

6. ブレード上部の **[追加]** を選択します。 アプリケーションが追加されて、クイック スタート メニューが表示されます。 

7. クイック スタート メニューで **[テスト用のユーザーを割り当てる]** を選択し、少なくとも 1 ユーザーをアプリケーションに追加します。 このテスト アカウントでオンプレミスのアプリケーションにアクセスできることを確認します。 

8. **[割り当て]** を選択して、テスト ユーザーの割り当てを保存します。 

9. (オプション) アプリの管理ページで **[シングル サインオン]** を選択します。 ドロップダウン メニューから **[統合 Windows 認証]** を選択し、Tableau 構成に基づいて必要なフィールドに記入します。 **[保存]** を選択します。 

 

## <a name="testing"></a>テスト 

これでアプリケーションをテストする準備ができました。 Tableau を発行するために使用した外部 URL にアクセスし、割り当てられているユーザーとして、両方のアプリケーションにログインします。



## <a name="next-steps"></a>次のステップ

Azure AD アプリケーション プロキシに関する詳細は、[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](application-proxy.md)をご覧ください。

