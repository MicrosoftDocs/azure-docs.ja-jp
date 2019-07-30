---
title: Visual Studio の接続済みサービスを利用して Azure Active Directory を追加する
description: Visual Studio の [接続済みサービスの追加] ダイアログ ボックスを使用してアプリに Azure Active Directory を追加する
services: active-directory
ms.subservice: develop
author: ghogen
manager: douge
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: aaddev, vs-azure
ms.workload: azure-vs
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.collection: M365-identity-device-management
ms.openlocfilehash: acd83b78537f526f5131a6eea585427ecefef0d1
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320778"
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Visual Studio の接続済みサービスを利用して Azure Active Directory を追加する

Azure Active Directory (Azure AD) を使用すると、ASP.NET MVC Web アプリケーションまたは Web API サービスの Active Directory Authentication のためのシングル サインオン (SSO) をサポートできます。 Azure AD Authentication を使用すると、ユーザーは Azure Active Directory のアカウントを使用して Web アプリケーションに接続できます。 Web API で Azure AD 認証を利用する利点には、Web アプリケーションから API を公開するときにデータのセキュリティが強化されることが含まれます。 Azure AD では、独自のアカウントとユーザー管理で別個の認証システムを管理する必要がありません。

この記事と関連記事では、Active Directory に Visual Studio 接続済みサービス機能を使用する方法について詳しく説明します。 この機能は、Visual Studio 2015 以降で利用できます。

現在、Active Directory の接続済みサービスは ASP.NET Core アプリケーションをサポートしていません。

## <a name="prerequisites"></a>前提条件

- Azure アカウント : Azure アカウントがない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)するか、[Visual Studio サブスクライバー特典を有効](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)にします。
- **Visual Studio 2015** またはそれ以降。 [Visual Studio を今すぐダウンロードします](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>[接続済みサービス] ダイアログを使用して Azure Active Directory に接続する

1. Visual Studio で、ASP.NET MVC プロジェクトまたは ASP.NET Web API プロジェクトを作成するか開きます。 MVC、Web API、シングルページ アプリケーション、Azure API App、Azure Mobile App、および Azure Mobile Service テンプレートを使用できます。

1. **[プロジェクト] > [接続済みサービスの追加]** メニュー コマンドを選択するか、ソリューション エクスプローラーでプロジェクト以下にある **[接続済みサービス]** ノードをダブル クリックします。

1. **[接続済みサービス]** ページで、 **[Azure Active Directory での認証]** を選択します。

    ![[接続済みサービス] ページ](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. **[説明]** ページで **[次へ]** を選択します。 このページにエラーが表示される場合は、[Azure Active Directory の接続済みサービスでエラーを診断する方法](vs-active-directory-error.md)に関するページを参照してください。

    ![[概要] ページ](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. **[シングル サインオン]** ページで、 **[ドメイン]** ドロップダウン リストからドメインを選択します。 このリストには、Visual Studio の [アカウント設定] ダイアログ ( **[ファイル] > [アカウント設定]** ) に表示されている、アカウントからアクセスできるすべてのドメインが含まれています。探しているドメインが見つからない場合は、代替として `mydomain.onmicrosoft.com` のようなドメイン名を入力できます。 Azure Active Directory アプリを作成したり、既存の Azure Active Directory アプリの設定を使用したりできます。 操作が完了したら、 **[次へ]** をクリックします。

    ![[シングル サインオン] ページ](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. **[ディレクトリ アクセス]** ページで、 **[ディレクトリ データの読み取り]** オプションを選択します。 一般的に開発時にはこのオプションを含めます。

    ![[ディレクトリ アクセス] ページ](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. **[完了]** を選択して、Azure AD Authentication を有効にするプロジェクトの変更を開始します。 処理中は、Visual Studio に進行状況が表示されます。

    ![Active Directory 接続済みサービスの進行状況](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. プロセスが完了すると、Visual Studio からブラウザーが開かれ、プロジェクトの種類に応じて次のいずれかの記事が表示されます。

    - [.NET MVC プロジェクトの作業開始](vs-active-directory-dotnet-getting-started.md)
    - [WebAPI プロジェクトの作業開始](vs-active-directory-webapi-getting-started.md)

1. Active Directory ドメインは [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) でも確認できます。

## <a name="how-your-project-is-modified"></a>プロジェクトを変更する方法

ウィザードで接続済みサービスを追加すると、Visual Studio でプロジェクトに Azure Active Directory と関連する参照が追加されます。 プロジェクトの構成ファイルとコード ファイルも変更され、Azure AD のサポートが追加されます。 Visual Studio による特定の変更はプロジェクトの種類によって異なります。 詳細については、次の記事を参照してください。

- [.NET MVC プロジェクトの変更点](vs-active-directory-dotnet-what-happened.md)
- [Web API プロジェクトの変更点](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>次の手順

- [Azure AD の認証シナリオ](authentication-scenarios.md)
- [ASP.NET Web アプリへの "Microsoft でサインイン" の追加](quickstart-v1-aspnet-webapp.md)
