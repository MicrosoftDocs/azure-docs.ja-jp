---
title: "Visual Studio の接続済みサービスを利用して Azure Active Directory を追加する | Microsoft Docs"
description: "Visual Studio の [接続済みサービスの追加] ダイアログ ボックスを使用してアプリに Azure Active Directory を追加する"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 5ff7c4ee612fd1261d85870be4d6fcbd66b64735
ms.lasthandoff: 03/27/2017


---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Visual Studio の接続済みサービスを利用して Azure Active Directory を追加する
Azure Active Directory (Azure AD) を使用すると、ASP.NET MVC Web アプリケーションまたは Web API サービスの Active Directory Authentication のためのシングル サインオン (SSO) をサポートできます。 Azure Active Directory Authentication を使用すると、ユーザーは Azure Active Directory のアカウントを使用して Web アプリケーションに接続できます。 Web API で Azure Active Directory Authentication を使用する利点として、Web アプリケーションから API を公開するときにデータのセキュリティが強化されることなどが挙げられます。 Azure AD では、独自のアカウントとユーザー管理で別個の認証システムを管理する必要がありません。

## <a name="prerequisites"></a>前提条件
- Azure アカウント - Azure アカウントがない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)するか、[Visual Studio サブスクライバー特典を有効](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)にします。

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>[接続済みサービス] ダイアログを使用して Azure Active Directory に接続する
1. Visual Studio で、ASP.NET MVC プロジェクトまたは ASP.NET Web API プロジェクトを作成するか開きます。

1. ソリューション エクスプローラーで **[接続済みサービス]** ノードを右クリックし、コンテキスト メニューの **[接続済みサービスの追加]** を選択します。

1. **[接続済みサービス]** ページで、**[Azure Active Directory での認証]** を選択します。
   
    ![[接続済みサービス] ページ](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. **[Azure AD Authentication を構成する]** ウィザードの **[概要]** ページで、**[次へ]** をクリックします。
   
    ![[概要] ページ](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1. **[Azure AD Authentication を構成する]** ウィザードの **[シングル サインオン]** ページで、**[ドメイン]** ドロップダウン リストからドメインを選択します。 ドメインのリストには、[アカウント設定] ダイアログの一覧にあるアカウントでアクセスできるすべてのドメインが含まれます。 探しているドメインが見つからない場合は、代替として `mydomain.onmicrosoft.com` のようなドメイン名を入力できます。 Azure Active Directory アプリを作成したり、既存の Azure Active Directory アプリの設定を使用したりできます。 操作が完了したら、**[次へ]** をクリックします。
   
    ![[シングル サインオン] ページ](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)

1. **[Azure AD Authentication を構成する]** ウィザードの **[ディレクトリ アクセス]** ページで、**[ディレクトリ データの読み取り]** チェック ボックスがオンになっていることを確認します。 
   
    ![[ディレクトリ アクセス] ページ](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. **[完了]** をクリックして、プロジェクトの Azure AD Authentication を有効にするために必要な構成コードと参照を追加します。 Active Directory ドメインは [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) で確認できます。

1. Visual Studio に、プロジェクトがどのように変更されたのかを示す [[変更内容]](#how-your-project-is-modified) ページが表示されます。 すべて問題なく機能したことを確認する場合は、変更された構成ファイルのいずれかを開き、このページに記載されている設定が存在することを確認します。 

## <a name="how-your-project-is-modified"></a>プロジェクトを変更する方法
ウィザードを実行すると、Visual Studio によって Azure Active Directory および関連付けられた参照がプロジェクトに追加されます。 プロジェクトの構成ファイルとコード ファイルも変更され、Azure AD のサポートが追加されます。 Visual Studio による特定の変更はプロジェクトの種類によって異なります。 ASP.NET MVC プロジェクトを変更する方法の詳細については、 [変更内容 – MVC プロジェクト](http://go.microsoft.com/fwlink/p/?LinkID=513809)に関するページを参照してください。 Web API プロジェクトの場合、 [変更内容 - Web API プロジェクト](http://go.microsoft.com/fwlink/p/?LinkId=513810)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Active Directory の MSDN フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)
* [Azure Active Directory のドキュメント](https://azure.microsoft.com/documentation/services/active-directory/)
* [ブログ記事: Azure Active Directory の概要](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)


