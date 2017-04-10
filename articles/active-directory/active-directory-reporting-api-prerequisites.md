---
title: "Azure AD Reporting API にアクセスするための前提条件。  | Microsoft Docs"
description: "Azure AD Reporting API にアクセスするための前提条件の詳細"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b0f23cc578fb946247d492b9dee92cf7a9daafa1


---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Azure AD Reporting API にアクセスするための前提条件
[Azure AD Reporting API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) は、一連の REST ベースの API を使用してデータにプログラムによるアクセスを提供します。 これらの API は、さまざまなプログラミング言語とツールから呼び出すことができます。

Reporting API は、 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) を使用して Web API へのアクセスを承認します。 

Reporting API へのアクセスを準備するには、次が必要です。

1. Azure AD テナントでアプリケーションを作成します。 
2. Azure AD のデータにアクセスするための適切なアクセス許可をアプリケーションに付与します。
3. ディレクトリから構成設定を収集します。

質問、問題点、またはフィードバックについては、 [AAD レポート ヘルプ](mailto:aadreportinghelp@microsoft.com)にお問い合わせください。

## <a name="create-an-azure-ad-application"></a>Azure AD アプリケーションの作成
ディレクトリを構成し、Azure AD Reporting API にアクセスするには、Azure AD テナントの全体管理者ディレクトリ ロールに属している Azure サブスクリプションの管理者アカウントで Azure クラシック ポータルにサインインする必要があります。

> [!IMPORTANT]
> このような "admin" 特権を持った資格情報で実行されるアプリケーションはきわめて強力です。アプリケーションの ID/シークレットの資格情報は厳重に保管してください。
> 
> 

1. [Azure クラシック ポータル](https://manage.windowsazure.com)の左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/01.png) 
2. **[Active Directory]** の一覧から、ディレクトリを選択します。
3. 上部のメニューで、 **[アプリケーション]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/02.png) 
4. 下のバーで **[追加]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/03.png) 
5. **[実行する作業を選択してください]** ダイアログで、**[組織で開発中のアプリケーションを追加]** をクリックします。 
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/04.png) 
6. **[アプリケーション情報の指定]** ダイアログで、次の手順を実行します。 
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/05.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[名前]** ボックスに、名前 (例: Reporting API アプリケーション) を入力します。
   
    b. **[Web アプリケーションや Web API]** を選択します。
   
    c. **[次へ]**をクリックします。
7. **[アプリケーションのプロパティ]** ダイアログで、次の手順を実行します。 
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/06.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、「`https://localhost`」と入力します。
   
    b. **[アプリケーション ID/URI]** ボックスに、「```https://localhost/ReportingApiApp```」と入力します。
   
    c. **[完了]** をクリックします。

## <a name="grant-your-application-permission-to-use-the-api"></a>API を使用するためのアクセス許可をアプリケーションに付与します。
1. [Azure クラシック ポータル](https://manage.windowsazure.com/)の左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/01.png) 
2. **[Active Directory]** の一覧から、ディレクトリを選択します。
3. 上部のメニューで、 **[アプリケーション]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/02.png)
4. アプリケーションの一覧で、新しく作成したアプリケーションを選択します。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/07.png)
5. 上部のメニューで **[構成]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/08.png)
6. **[他のアプリケーションに対するアクセス許可]** セクションで、**Azure Active Directory** リソースについて、**[アプリケーションのアクセス許可]** ボックスの一覧の **[ディレクトリ データの読み取り]** を選択します。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/09.png)
7. 下のバーで **[保存]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/10.png)

## <a name="gather-configuration-settings-from-your-directory"></a>ディレクトリから構成設定を収集します。
このセクションでは、ディレクトリから次の設定を取得する方法を示しています。

* ドメイン名
* クライアント ID
* クライアント シークレット

Reporting API への呼び出しを構成するときに、これらの値が必要です。 

### <a name="get-your-domain-name"></a>ドメイン名を取得する
1. [Azure クラシック ポータル](https://manage.windowsazure.com)の左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/01.png) 
2. **[Active Directory]** の一覧から、ディレクトリを選択します。
3. 上部のメニューで **[ドメイン]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/11.png) 
4. **[ドメイン名]** 列でドメイン名をコピーします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/12.png) 

### <a name="get-the-applications-client-id"></a>アプリケーションのクライアント ID の取得
1. [Azure クラシック ポータル](https://manage.windowsazure.com)の左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/01.png) 
2. **[Active Directory]** の一覧から、ディレクトリを選択します。
3. 上部のメニューで、 **[アプリケーション]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/02.png) 
4. アプリケーションの一覧で、新しく作成したアプリケーションを選択します。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/07.png)
5. 上部のメニューで **[構成]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/08.png)
6. **クライアント ID**をコピーします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/13.png)

### <a name="get-the-applications-client-secret"></a>アプリケーションのクライアント シークレットの取得
アプリケーションのクライアント シークレットを取得するには、新しいキーを作成し、その新しいキーを保存するときにその値を保存する必要があります。これは、後でこの値を取得することができないためです。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)の左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/01.png) 
2. **[Active Directory]** の一覧から、ディレクトリを選択します。
3. 上部のメニューで、 **[アプリケーション]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/02.png) 
4. アプリケーションの一覧で、新しく作成したアプリケーションを選択します。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/07.png)
5. 上部のメニューで **[構成]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/08.png)
6. **[キー]** セクションで、次の手順に従います。 
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/14.png)
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 期間の一覧から期間を選択します。
   
    b. 下のバーで **[保存]**をクリックします。
   
    ![アプリケーションを登録する](./media/active-directory-reporting-api-prerequisites/10.png)
   
    c. キー値をコピーします。

## <a name="next-steps"></a>次のステップ
* プログラムによる方法で Azure AD Reporting API からデータにアクセスしますか。 「 [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started.md)」を確認してください。
* Azure Active Directory のレポート作成に関する詳細については、「 [Azure Active Directory レポート ガイド](active-directory-reporting-guide.md)」を参照してください。  




<!--HONumber=Dec16_HO5-->


