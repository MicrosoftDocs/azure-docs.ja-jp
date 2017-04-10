---
title: "Azure アプリでのオンプレミスの Active Directory を使用した認証 | Microsoft Docs"
description: "Azure App Service の基幹業務アプリでオンプレミスの Active Directory を使用して認証を行うための各種オプションについて説明します。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: dde6b7e6-bf6a-4fa5-8390-3a18155d21bd
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 08/31/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f989ea86c76706af9413b2bb945956b1c6432e1d
ms.lasthandoff: 11/17/2016


---
# <a name="authenticate-with-on-premises-active-directory-in-your-azure-app"></a>Azure アプリでのオンプレミスの Active Directory を使用した認証
この記事では、 [Azure App Service](../app-service/app-service-value-prop-what-is.md)でオンプレミスの Active Directory (AD) を使用して認証を行う方法について説明します。 Azure アプリはクラウドでホストされますが、オンプレミスの AD ユーザーを安全に認証する方法はいくつかあります。 

## <a name="authenticate-through-azure-active-directory"></a>Azure Active Directory を使用した認証
Azure Active Directory テナントは、オンプレミスの AD との間でディレクトリの同期を行うことができます。 この方法を使用すると、AD ユーザーはインターネットからアプリにアクセスし、オンプレミスの資格情報を使用して認証を行うことができます。 さらに、Azure App Service には、 [この方法のターンキー ソリューション](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)が用意されています。 ボタンを数回クリックするだけで、Azure アプリのディレクトリ同期テナントで認証を有効にできます。 この方法には次の利点があります。

* アプリに認証コードを組み込む必要がありません。 認証処理を App Service に任せて、アプリの機能を充実させることに時間を割くことができます。
* [Azure AD Graph API](http://msdn.microsoft.com/library/azure/hh974476.aspx) により、Azure アプリからディレクトリ データにアクセスできます。
* [Azure Active Directory でサポートされているすべてのアプリケーション](/marketplace/active-directory/)(Office 365、Dynamics CRM Online、Microsoft Intune に加え、Microsoft 以外の多数のクラウド アプリケーションなど) に SSO を提供します。 
* Azure Active Directory では、ロールベースのアクセス制御がサポートされます。 コードに最小限の変更を加えるだけで [Authorize(Roles="X")] パターンを使用できます。

Azure Active Directory を使用して認証を行う基幹業務 Azure アプリを作成する方法については、「 [Create a line-of-business Azure app with Azure Active Directory authentication (Azure Active Directory 認証を使用した基幹業務 Azure アプリの作成)](web-sites-dotnet-lob-application-azure-ad.md)」を参照してください。

## <a name="authenticate-through-an-on-premises-sts"></a>オンプレミス STS を使用した認証
Active Directory フェデレーション サービス (AD FS) などのオンプレミスの Secure Token Service (STS) がある場合は、そのサービスを使用して Azure アプリの認証のフェデレーションを行うことができます。 この方法は、AD データを Azure に保存することが会社のポリシーによって禁止されている場合に最適です。 ただし、次の点に注意してください。

* STS トポロジはオンプレミスにデプロイする必要があり、コストと管理のオーバーヘッドが伴います。
* AD FS 管理者だけが、[証明書利用者の信頼と要求規則](http://technet.microsoft.com/library/dd807108.aspx)を構成できます。このため、開発者の選択肢が制限される場合があります。 一方[要求](http://technet.microsoft.com/library/ee913571.aspx)は、アプリケーションごとに管理、カスタマイズできます。
* オンプレミスの AD のデータにアクセスするためには、企業のファイアウォールを経由する別個のソリューションが必要です。

オンプレミスの STS を使用して認証を行う基幹業務 Azure アプリを作成する方法については、「 [Create a line-of-business Azure app with AD FS authentication (AD FS 認証を使用した基幹業務 Azure アプリの作成)](web-sites-dotnet-lob-application-adfs.md)」を参照してください。


