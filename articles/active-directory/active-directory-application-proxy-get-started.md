---
title: "オンプレミス アプリへの安全なリモート アクセスを実現する方法"
description: "Azure AD アプリケーション プロキシを使用して、オンプレミス アプリへの安全なリモート アクセスを実現する方法について取り上げます。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 67f7f5b8d411d11c97a8666d1bfc3c0c5f1174ce
ms.contentlocale: ja-jp
ms.lasthandoff: 08/05/2017

---

# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法

現在、従業員は、どこでも、いつでも、どんなデバイスからでも生産的であることを望んでいます。 タブレット、電話、ラップトップを問わず、自分のデバイスで作業したいと考えています。 そして、すべてのアプリケーション (クラウドにある SaaS アプリとオンプレミスの社内アプリの両方) にアクセスできることを期待しています。 オンプレミスのアプリケーションへのアクセス提供には、従来、仮想プライベート ネットワーク (VPN) や非武装地帯 (DMZ) が必要でした。 これらのソリューションは、複雑でセキュリティ保護が困難であるだけでなく、設定と管理にコストがかかります。

もっと良い方法があります。

モバイルであること、クラウドであることを優先する世界の現在の従業員には、最新のリモート アクセス ソリューションが必要です。 Azure AD アプリケーション プロキシは、サービスとしてリモート アクセスを提供する Azure Active Directory の機能です。 つまり、デプロイ、使用、および管理が簡単です。

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="what-is-azure-active-directory-application-proxy"></a>Azure Active Directory アプリケーション プロキシとは何か
Azure AD アプリケーション プロキシは、オンプレミスでホストされている Web アプリケーションのシングル サインオンと安全なリモート アクセスを実現します。 発行する一部のアプリには、SharePoint サイト、Outlook Web Access、またはお持ちのその他の LOB Web アプリケーションが含まれます。 これらのオンプレミスの Web アプリケーションは、Azure AD (O365 で使用するのと同じ ID、およびコントロール プラットフォーム) に統合されます。 エンド ユーザーは、O365 にアクセスするのと同じようにオンプレミスのアプリケーションにアクセスでき、Azure AD に統合された他の SaaS アプリにもアクセスできます。 このソリューションをユーザーに提供するために、ネットワーク インフラストラクチャを変更したり、VPN を導入したりする必要はありません。

## <a name="why-is-application-proxy-a-better-solution"></a>アプリケーション プロキシのほうが優れたソリューションである理由
Azure AD アプリケーション プロキシは、すべてのオンプレミスのアプリケーションに、簡単、安全、かつコスト効率の高いリモート アクセス ソリューションを提供します。

Azure AD アプリケーション プロキシの特徴:

* **シンプル**
   * アプリケーション プロキシを使用するためにアプリケーションを変更または更新する必要はありません。 
   * ユーザーに、一貫性のある認証エクスペリエンスが提供されます。 MyApps ポータルを使用して、クラウド内の SaaS アプリとオンプレミスのアプリの両方でシングル サインオンを取得できます。 
* **セキュリティ保護**
   * Azure AD アプリケーション プロキシを使用してアプリを発行するときは、Azure の豊富な承認制御とセキュリティ分析を活用できます。 クラウド スケールのセキュリティと、条件付きアクセスや 2 段階認証などの Azure セキュリティ機能が提供されます。
   * ユーザーにリモート アクセスを許可するためにファイアウォールを介した着信接続を開く必要はありません。 
* **高いコスト効率**
   * アプリケーション プロキシはクラウドで動作するため、時間とコストを節約できます。 オンプレミスのソリューションでは、通常、DMZ、エッジ サーバー、またはその他の複雑なインフラストラクチャを設定し維持する必要があります。  

## <a name="what-kind-of-applications-work-with-application-proxy"></a>アプリケーション プロキシと連動するアプリケーションの種類
Azure AD アプリケーション プロキシを使用すると、次のようなさまざまな種類の内部アプリケーションにアクセスできます。

* 認証に[統合 Windows 認証](active-directory-application-proxy-sso-using-kcd.md)を使用する Web アプリ  
* フォーム ベースまたは[ヘッダー ベース](application-proxy-ping-access.md)のアクセスを使用する Web アプリ  
* さまざまなデバイスの豊富なアプリケーションに公開する Web API  
* [リモート デスクトップ ゲートウェイ](application-proxy-publish-remote-desktop.md)の背後でホストされているアプリケーション  
* Active Directory Authentication Library (ADAL) と統合されるリッチ クライアント アプリ

## <a name="how-does-application-proxy-work"></a>アプリケーション プロキシのしくみ
アプリケーション プロキシを動作させるために構成する必要がある 2 つのコンポーネントは、コネクタと外部エンドポイントです。 

コネクタは、ネットワーク内の Windows Server 上に置かれる軽量エージェントです。 コネクタは、クラウド内のアプリケーション プロキシ サービスからオンプレミスのアプリケーションへのトラフィック フローを促進します。 発信接続のみ使用するため、受信ポートを開いたり、DMZ に配置したりする必要はありません。 コネクタはステートレスで、必要に応じて情報がクラウドから取得されます。 負荷分散や認証の方法など、コネクタについて詳しくは、「[Azure AD アプリケーション プロキシ コネクタを理解する](application-proxy-understand-connectors.md)」をご覧ください。 

外部エンドポイントは、ユーザーがネットワークの外部からアプリケーションに到達する方法です。 決定した外部 URL に直接移動するか、MyApps ポータルからアプリケーションにアクセスすることができます。 ユーザーは、これらのエンドポイントの 1 つに移動すると、Azure AD で認証され、コネクタを介してオンプレミスのアプリケーションにルーティングされます。

 ![Azure AD アプリケーション プロキシ ダイアグラム](./media/active-directory-application-proxy-get-started/azureappproxxy.png)

1. ユーザーがアプリケーション プロキシ サービス経由でアプリケーションにアクセスすると、認証のための Azure AD サインイン ページが表示されます。
2. サインインに成功するとトークンが生成され、クライアント デバイスに送信されます。
3. クライアントはここでアプリケーション プロキシ サービスにトークンを送信します。アプリケーション プロキシ サービスは、そのトークンからユーザー プリンシパル名 (UPN) とセキュリティ プリンシパル名 (SPN) を取得し、アプリケーション プロキシ コネクタに要求を送信します。
4. シングル サインオンを構成した場合、コネクタはユーザーの代わりに必要な追加の認証を実行します。
5. コネクタはオンプレミスのアプリケーションに要求を送信します。  
6. 応答はアプリケーション プロキシ サービスとコネクタ経由でユーザーに送信されます。

### <a name="single-sign-on"></a>シングル サインオン
Azure AD アプリケーション プロキシは、統合 Wndows 認証 (IWA) や要求に対応するアプリケーションへのシングル サインオン (SSO) を提供します。 発行対象のアプリケーションで IWA が使用されている場合、アプリケーション プロキシが Kerberos の制約付き委任を使い、見かけ上ユーザーとして振る舞うことで、SSO を実現します。 Azure Active Directory を信頼している要求に対応するアプリケーションでは、Azure AD によってユーザーが認証済みであるために SSO が機能します。

Kerberos について詳しくは、「[All you want to know about Kerberos Constrained Delegation (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd)」(Kerberos の制約付き委任 (KCD) のすべて) をご覧ください。

### <a name="managing-apps"></a>アプリの管理
アプリケーション プロキシを使用してアプリが発行されたら、Azure Portal で他のエンタープライズ アプリと同様に管理することができます。 条件付きアクセスや 2 段階認証などの Azure Active Directory セキュリティ機能を使用して、ユーザーのアクセス許可を制御し、アプリのブランド化をカスタマイズできます。 

## <a name="get-started"></a>作業開始

アプリケーション プロキシを構成する前に、サポートされている [Azure Active Directory エディション](https://azure.microsoft.com/pricing/details/active-directory/)と、自分がグローバル管理者になっている Azure AD ディレクトリがあることを確認します。

アプリケーション プロキシは 2 ステップで開始します。

1. [アプリケーション プロキシを有効にしてコネクタを構成する](active-directory-application-proxy-enable.md)。    
2. [アプリケーションを発行する。](active-directory-application-proxy-publish.md) ウィザードを使ってオンプレミスのアプリを発行し、リモートからアクセスできるようにします。

## <a name="whats-next"></a>次の手順
最初のアプリを発行した後、アプリケーション プロキシを使ってできることは他にもたくさんあります。

* [シングル サインオンを有効にする](active-directory-application-proxy-sso-using-kcd.md)
* [独自のドメイン名でアプリケーションを発行する](active-directory-application-proxy-custom-domains.md)
* [Azure AD アプリケーション プロキシ コネクタを理解する](application-proxy-understand-connectors.md)
* [既存のオンプレミス プロキシ サーバーと連携する](application-proxy-working-with-proxy-servers.md) 
* [カスタム ホーム ページを設定する](application-proxy-office365-app-launcher.md)

最新のニュースと更新プログラムについては、 [アプリケーション プロキシに関するブログ](http://blogs.technet.com/b/applicationproxyblog/)


