---
title: "オンプレミス アプリへの安全なリモート アクセスを実現する方法"
description: "Azure AD アプリケーション プロキシを使用して、オンプレミス アプリへの安全なリモート アクセスを実現する方法について取り上げます。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2e7815702f2d2f4ce935826c4769838727a83696
ms.openlocfilehash: 7d1be1dea6ed4ecda196743f592456a5b977e9b0
ms.lasthandoff: 02/14/2017


---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法
> [!NOTE]
> アプリケーション プロキシは、Azure Active Directory の Premium または Basic エディションにアップグレードしている場合にのみ利用できる機能です。 詳細については、「 [Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。
> 
> 

現在、従業員は、どこでも、いつでも、どんなデバイスからでも生産的であることを望んでいます。 タブレット、電話、ラップトップを問わず、自分のデバイスで作業したいと考えています。 そして、すべてのアプリケーション (クラウドにある SaaS アプリとオンプレミスの社内アプリの両方) にアクセスできることを期待しています。 オンプレミスのアプリケーションへのアクセス提供には、従来、仮想プライベート ネットワーク (VPN) や非武装地帯 (DMZ)、オンプレミスのリバース プロキシが必要でした。 これらのソリューションは、複雑でセキュリティ保護が困難であるだけでなく、設定と管理にコストがかかります。

もっと良い方法があります。

モバイルであること、クラウドであることを優先する世界の現在の従業員には、最新のリモート アクセス ソリューションが必要です。 Azure AD アプリケーション プロキシは、Azure Active Directory Premium オファリングの機能であり、サービスとしてリモート アクセスを提供します。 つまり、デプロイ、使用、および管理が簡単です。

## <a name="what-is-azure-active-directory-application-proxy"></a>Azure Active Directory アプリケーション プロキシとは何か
Azure AD アプリケーション プロキシは、オンプレミスでホストされている Web アプリケーションのシングル サインオンと安全なリモート アクセスを実現します。 これには、SharePoint サイト、Outlook Web Access、またはお持ちのその他の LOB Web アプリケーションを含めることができます。 これらのオンプレミスの Web アプリケーションは、Azure AD (O365 で使用するのと同じ ID、およびコントロール プラットフォーム) に統合されます。 これにより、エンドユーザーは、O365 にアクセスするようにオンプレミスのアプリケーションにアクセスでき、Azure AD に統合された他の SaaS アプリにもアクセスできます。 このソリューションをユーザーに提供するために、ネットワーク インフラストラクチャを変更したり、VPN を導入したりする必要はありません。

## <a name="why-is-this-a-better-solution"></a>優れたソリューションである理由
Azure AD アプリケーション プロキシは、すべてのオンプレミスのアプリケーションに、簡単、安全、かつコスト効率の高いリモート アクセス ソリューションを提供します。

Azure AD アプリケーション プロキシは:  

* クラウドで動作するため、時間とコストを節約できます。 オンプレミスのソリューションでは、DMZ、エッジ サーバー、またはその他の複雑なインフラストラクチャを設定し維持する必要があります。  
* ファイアウォール経由で受信接続を開く必要がないため、オンプレミスのソリューションよりも設定が簡単で安全です。  
* 優れたセキュリティを提供します。 Azure AD アプリケーション プロキシを使用してアプリを発行するときは、Azure の豊富な承認制御とセキュリティ分析を活用できます。 つまり、アプリを変更しなくても、既存のすべてのアプリのセキュリティ機能が強化されます。  
* ユーザーに、一貫性のある認証エクスペリエンスを提供します。 シングル サインオンにより、エンド ユーザーは生産的であるために必要なすべてのアプリに、1 つのパスワードで簡単かつシンプルにアクセスできます。  

## <a name="what-kind-of-applications-work-with-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシと連動するアプリケーションの種類
Azure AD アプリケーション プロキシを使用すると、次のようなさまざまな種類の内部アプリケーションにアクセスできます。

* 認証に統合 Windows 認証を使用する Web アプリケーション  
* フォーム ベースのアクセスを使用する Web アプリケーション  
* さまざまなデバイスの豊富なアプリケーションに公開する Web API  
* リモート デスクトップ ゲートウェイの背後でホストされているアプリケーション  

## <a name="how-does-the-service-work-with-connectors"></a>このサービスがコネクタと連携するしくみ
アプリケーション プロキシは、社内ネットワークに "コネクタ" と呼ばれる軽量の Windows Server サービスをインストールすることによって機能します。 コネクタを使用すると、受信ポートを開いたり、DMZ に配置したりする必要はありません。 アプリのトラフィック量が多い場合は、コネクタを増設すれば、このサービスによって負荷分散が行われます。 コネクタはステートレスで、すべての情報が必要に応じてクラウドから取得されます。

コネクタの詳細については、「[Understand Azure AD Application Proxy connectors (Azure AD アプリケーション プロキシ コネクタについて)](application-proxy-understand-connectors.md)」を参照してください。 

ユーザーは、アプリケーションにリモートでアクセスするときは、公開されているエンドポイントに接続します。 ユーザーは、Azure AD で認証され、コネクタを介してオンプレミスのアプリケーションにルーティングされます。

 ![Azure AD アプリケーション プロキシ ダイアグラム](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

1. ユーザーがアプリケーション プロキシ経由でアプリケーションにアクセスすると、認証のための Azure AD サインイン ページが表示されます。
2. サインインに成功すると、トークンが生成され、ユーザーに送信されます。
3. ユーザーはここでアプリケーション プロキシにトークンを送信します。アプリケーション プロキシは、そのトークンからユーザー プリンシパル名 (UPN) とセキュリティ プリンシパル名 (SPN) を取得し、コネクタに要求を送信します。
4. コネクタは、ユーザーの代理として内部 (Windows) 認証に使用できる Kerberos チケットを要求します。 これは Kerberos の制約付き委任として知られています。
5. Active Directory では、Kerberos チケットを取得します。
6. チケットは、アプリケーション サーバーに送信されて検証されます。
7. 応答がアプリケーション プロキシ経由でエンドユーザーに送信されます。

### <a name="single-sign-on"></a>シングル サインオン
Azure AD アプリケーション プロキシは、統合 Wndows 認証 (IWA) や要求に対応するアプリケーションへのシングル サインオン (SSO) を提供します。 発行対象のアプリケーションで IWA が使用されている場合、アプリケーション プロキシが Kerberos の制約付き委任を使い、見かけ上ユーザーとして振る舞うことで、SSO を実現します。 Azure Active Directory を信頼している要求に対応するアプリケーションでは、Azure AD によってユーザーが認証済みであるために SSO が成立します。

Kerberos について詳しくは、「[All you want to know about Kerberos Constrained Delegation (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd)」(Kerberos の制約付き委任 (KCD) のすべて) をご覧ください。

## <a name="how-to-get-started"></a>ファースト ステップ
Azure AD Basic または Azure AD Premium サブスクリプションに加え、自分が全体管理者となっている Azure AD ディレクトリが必要です。 ディレクトリ管理者、およびアプリにアクセスするユーザーについても、Azure AD Basic または Azure AD Premium のライセンスが必要となります。 詳細については、「 [Azure Active Directory のエディション](active-directory-editions.md) 」をご覧ください。

アプリケーション プロキシの設定は、次の&2; つの手順で行います。

1. [アプリケーション プロキシを有効にしてコネクタを構成する](active-directory-application-proxy-enable.md)。    
2. [アプリケーションを発行する。](active-directory-application-proxy-publish.md) ウィザードを使ってオンプレミスのアプリを発行し、リモートからアクセスできるようにします。

## <a name="whats-next"></a>次の手順
アプリケーション プロキシを使ってできることは他にもたくさんあります。

* [独自のドメイン名でアプリケーションを発行する](active-directory-application-proxy-custom-domains.md)
* [既存のオンプレミス プロキシ サーバーと連携する](application-proxy-working-with-proxy-servers.md) 
* [シングル サインオンを有効にする](active-directory-application-proxy-sso-using-kcd.md)
* [要求に対応するアプリケーションを利用する](active-directory-application-proxy-claims-aware-apps.md)
* [条件付きアクセスを有効にする](active-directory-application-proxy-conditional-access.md)

最新のニュースと更新プログラムについては、 [アプリケーション プロキシに関するブログ](http://blogs.technet.com/b/applicationproxyblog/)


