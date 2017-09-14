---
title: "Azure AD Reporting API にアクセスするための前提条件 | Microsoft Docs"
description: "Azure AD Reporting API にアクセスするための前提条件の詳細"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: 5fafd83c337e3c73260d89cdad7409a01ce5855b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/07/2017

---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Azure AD Reporting API にアクセスするための前提条件

[Azure AD Reporting API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) は、一連の REST ベースの API を使用してデータにプログラムによるアクセスを提供します。 これらの API は、さまざまなプログラミング言語とツールから呼び出すことができます。

Reporting API は、 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) を使用して Web API へのアクセスを承認します。 

API を介してレポート データにアクセスするには、次のいずれかのロールが割り当てられている必要があります。

- セキュリティ リーダー
- セキュリティ管理者
- 全体管理者


Reporting API へのアクセスを準備するには、次が必要です。

1. アプリケーションを登録する 
2. アクセス許可を付与する 
3. 構成設定を収集する 

質問、問題点、またはフィードバックについては、[サポート チケットを提出](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto)してください。

## <a name="register-an-azure-active-directory-application"></a>Azure Active Directory アプリケーションを登録する

スクリプトを使用して Reporting API にアクセスしている場合でも、アプリを登録する必要があります。 これにより、承認の呼び出しと、コードによるトークンの受信に必要な**アプリケーション ID** が得られます。

Azure AD Reporting API にアクセスするようにディレクトリを構成するには、Azure AD テナントの**全体管理者**ディレクトリ ロールのメンバーでもある Azure 管理者アカウントで Azure Portal にサインインする必要があります。

> [!IMPORTANT]
> このような "admin" 特権を持った資格情報で実行されるアプリケーションはきわめて強力です。アプリケーションの ID/シークレットの資格情報は厳重に保管してください。
> 


**Azure Active Directory アプリケーションを登録するには:**

1. [Azure Portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. **[Azure Active Directory]** ブレードで、**[アプリの登録]** をクリックします。

    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. **[アプリの登録]** ブレードで、上部のツール バーの **[新しいアプリケーションの登録]** をクリックします。

    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. **[作成]** ブレードで、次の手順を実行します。

    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. **[名前]** ボックスに、「`Reporting API application`」と入力します。

    b. **アプリケーションの種類**として **[Web アプリ/API]** を選択します。

    c. **[サインオン URL]** ボックスに、「`https://localhost`」と入力します。

    d. **[作成]**をクリックします。 


## <a name="grant-permissions"></a>アクセス許可を付与する 

この手順の目的は、アプリケーションに **Windows Azure Active Directory** API に対する **[ディレクトリ データの読み取り]** アクセス許可を付与することです。

![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/16.png)
 

**API を使用するためのアクセス許可をアプリケーションに付与するには:**

1. **[アプリの登録]** ブレードのアプリの一覧で、**[Reporting API application]** をクリックします。

2. **[Reporting API application]** ブレードで、上部のツール バーの **[設定]** をクリックします。 

    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. **[設定]** ブレードで **[必要なアクセス許可]** をクリックします。 

    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. **[必要なアクセス許可]** ブレードの **[API]** の一覧で、**[Windows Azure Active Directory]** をクリックします。 

    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. **[アクセスの有効化]** ブレードで、**[ディレクトリ データの読み取り]** を選択します。 

    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. 上部のツール バーで **[保存]** をクリックします。

    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

## <a name="gather-configuration-settings"></a>構成設定を収集する 
このセクションでは、ディレクトリから次の設定を取得する方法を示しています。

* ドメイン名
* クライアント ID
* クライアント シークレット

Reporting API への呼び出しを構成するときに、これらの値が必要です。 

### <a name="get-your-domain-name"></a>ドメイン名を取得する

**ドメイン名を取得するには:**

1. [Azure Portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. **[Azure Active Directory]** ブレードで、**[ドメイン名]** をクリックします。

    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. ドメインの一覧からドメイン名をコピーします。


### <a name="get-your-applications-client-id"></a>アプリケーションのクライアント ID を取得する

**アプリケーションのクライアント ID を取得するには:**

1. [Azure Portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. **[アプリの登録]** ブレードのアプリの一覧で、**[Reporting API application]** をクリックします。

3. **[Reporting API application]** ブレードの **[アプリケーション ID]** で、**[クリックしてコピー]** をクリックします。

    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>アプリケーションのクライアント シークレットを取得する
アプリケーションのクライアント シークレットを取得するには、新しいキーを作成し、その新しいキーを保存するときにその値を保存する必要があります。これは、後でこの値を取得することができないためです。

**アプリケーションのクライアント シークレットを取得するには:**

1. [Azure Portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. **[アプリの登録]** ブレードのアプリの一覧で、**[Reporting API application]** をクリックします。


3. **[Reporting API application]** ブレードで、上部のツール バーの **[設定]** をクリックします。 

    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. **[設定]** ブレードの **[API アクセス]** セクションで、**[キー]** をクリックします。 

    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. **[キー]** ブレードで、次の手順を実行します。

    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. **[説明]** ボックスに、「`Reporting API`」と入力します。

    b. **[有効期限]** として、**[2 年]** を選択します。

    c. [ **Save**] をクリックします。

    d. キー値をコピーします。


## <a name="next-steps"></a>次のステップ
* プログラムによる方法で Azure AD Reporting API からデータにアクセスしますか。 「 [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started.md)」を確認してください。
* Azure Active Directory のレポート作成に関する詳細については、「 [Azure Active Directory レポート ガイド](active-directory-reporting-guide.md)」を参照してください。  


