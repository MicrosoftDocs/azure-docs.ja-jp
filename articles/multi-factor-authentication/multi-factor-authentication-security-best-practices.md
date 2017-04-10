---
title: "MFA のセキュリティのベスト プラクティス | Microsoft Docs"
description: "このドキュメントでは、Azure アカウントで Azure MFA を使用する場合のベスト プラクティスについて説明します。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 3be7d968-96bb-4320-8701-869fd04a2595
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 999361daa2faebe3e88cab0b6085a938d6f40e9d
ms.openlocfilehash: 24ccd50de9bc4ad01855112507ece59830f861ba
ms.lasthandoff: 02/17/2017

---

# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Azure AD アカウントで Azure Multi-Factor Authentication を使用するためのセキュリティのベスト プラクティス

認証プロセスの強化を望むほとんどの組織には、2 段階認証を選択することをお勧めします。 Azure Multi-Factor Authentication (MFA) を使用すると、企業はセキュリティやコンプライアンスの要件を満たすと同時に、ユーザーに簡単なサインイン エクスペリエンスを提供することが容易になります。 この記事では、Azure MFA の導入を計画する際に考慮すべきベスト プラクティスを紹介します。

## <a name="best-practices-for-a-cloud-deployment"></a>クラウド デプロイのベスト プラクティス

Azure MFA をすべてのユーザーに対して有効にする方法は&2; 通りあります。

* 各ユーザー用にライセンスを購入する (Azure MFA、Azure AD Premium、Enterprise Mobility + Security のいずれか)
* Multi-Factor Auth Provider を作成し、ユーザーごとまたは認証ごとに支払う

### <a name="licenses"></a>ライセンス
![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Azure AD Premium ライセンスまたは Enterprise Mobility + Security ライセンスがある場合、既に Azure MFA を所有していることになります。 組織では、すべてのユーザーを&2; 段階認証機能の対象にするために特別な操作を行う必要はありません。 ユーザーにライセンスを割り当てるだけで、MFA をオンにすることができます。

Multi-Factor Authentication を設定する場合は、次のヒントを考慮してください。

* 認証ごとの Multi-Factor Auth Provider は作成しないでください。 認証ごとに Multi-Factor Auth Provider を作成すると、既にライセンスを持っているユーザーからの認証要求に対しても支払いすることになる可能性があります。
* 一部のユーザーにライセンスがない場合は、組織内の残りのユーザーを対象とするユーザーごとの Muti-Factor Auth Provider を作成できます。 
* Azure AD Connect は、オンプレミスの Active Directory 環境を Azure AD ディレクトリと同期している場合にのみ必要です。 オンプレミスの Active Directory インスタンスと同期されない Azure AD ディレクトリを使用している場合、Azure AD Connect は必要ありません。

### <a name="multi-factor-auth-provider"></a>多要素認証プロバイダー
![Multi-Factor Auth Provider](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Azure MFA を含むライセンスがない場合、クラウドに Azure MFA を導入するための最初の手順は、MFA Auth Provider を作成することです。 MFA は、Azure Active Directory を所有しているグローバル管理者が既定で使用できますが、Multi-Factor Auth Provider により、すべてのユーザーが&2; 段階認証機能の対象になります。 

Auth Provider を作成する場合は、ディレクトリを選択するとともに次の点を考慮する必要があります。

* Multi-Factor Auth Provider の作成に Azure AD ディレクトリは必要ありませんが、ディレクトリがあるとより高度な機能を利用できます。 Azure AD ディレクトリに Auth Provider を関連付けると、次に示す機能が有効になります。  
  * すべてのユーザーを&2; 段階認証の対象にする  
  * 管理ポータル、カスタムの案内応答、レポートなどの追加機能をグローバル管理者に提供する
* オンプレミスの Active Directory 環境を Azure AD ディレクトリと同期する場合、DirSync または AAD Sync のどちらかが必要です。 Active Directory のオンプレミスのインスタンスと同期されない Azure AD ディレクトリを使用する場合、DirSync または AAD Sync は必要ありません。
* ビジネスに最も適した使用モデルを選択してください。 使用モデルはいったん選択すると変更できません。 2 つのモデルを次に示します。
  * 認証ごと: 認証ごとに課金されます。 特定のユーザーではなく、特定のアプリにアクセスするすべてのユーザーに対して&2; 段階認証を実行する場合は、このモデルを使用します。
  * 有効化されたユーザーごと: Azure MFA を有効にしたユーザーごとに課金されます。 Azure AD Premium または Enterprise Mobility Suite ライセンスを所有しているユーザーと所有していないユーザーが混在している場合は、このモデルを使用します。

### <a name="supportability"></a>サポート
ユーザーの大半は、パスワードのみを使用した認証に慣れているため、企業はこのプロセスに関してすべてのユーザーに認識してもらうことが重要です。 このような認識により、ユーザーが MFA 関連の重要でない問題についてヘルプ デスクに問い合わせる可能性は低くなります。 ただし、一部のシナリオでは MFA を一時的に無効にすることが必要になる場合があります。 次のガイドラインに従って、これらのシナリオへの対処方法を理解してください。

* モバイル アプリや携帯電話で通知や電話呼び出しが受信されないためにユーザーがサインインできないシナリオに対処するためのトレーニングを、テクニカル サポート担当者が受講済みであることを確認します。 テクニカル サポート担当者は、[ワンタイム バイパスを有効に](multi-factor-authentication-whats-next.md#one-time-bypass)し、2 段階認証を "バイパス" することによってユーザーを&1; 回だけ認証できるようにすることができます。 バイパスは一時的なものであり、指定された秒数が経過すると無効になります。
* 2 段階認証を最小限に留める方法として、Azure MFA の[信頼できる IP 機能](multi-factor-authentication-whats-next.md#trusted-ips)の利用を検討してください。 この機能を使用すると、管理者常駐型テナントまたはフェデレーション テナントの管理者は、会社のローカル イントラネットからサインインするユーザーの&2; 段階認証をバイパスできます。 この機能は、Azure AD Premium、Enterprise Mobility Suite、または Azure Multi-Factor Authentication ライセンスを所有している Azure AD テナントで使用できます。

## <a name="best-practices-for-an-on-premises-deployment"></a>オンプレミス デプロイのベスト プラクティス
企業で独自のインフラストラクチャを利用して MFA を有効にすることを決定した場合は、Azure Multi-Factor Authentication Server をオンプレミスでデプロイする必要があります。 MFA Server のコンポーネントを次の図に示します。

![既定の MFA Server コンポーネント: コンソール、同期エンジン、管理ポータル、クラウド サービス](./media/multi-factor-authentication-security-best-practices/server.png)
\*既定ではインストールされていません\**既定ではインストールされていますが、有効になっていません

Azure Multi-Factor Authentication Server を使用すると、Azure AD アカウントがアクセスするクラウドのリソースとオンプレミスのリソースをセキュリティで保護することができます。 ただし、これを実行できるのは、フェデレーションを使用した場合のみです。  つまり、AD FS を用意し、それを Azure AD テナントとフェデレーションしておく必要があります。
Multi-Factor Authentication Server を設定する場合は、次の点を考慮してください。

* Active Directory フェデレーション サービス (AD FS)を使用して Azure AD リソースをセキュリティで保護している場合、認証の第&1; 段階は AD FS を使用してオンプレミスで実行されます。 第&2; 段階は、要求を受け入れることによりオンプレミスで実行されます。
* AD FS フェデレーション サーバーに Azure Multi-Factor Authentication Server をインストールする必要はありません。 ただし、AD FS を実行中の Windows Server 2012 R2 には、AD FS 用の Multi-Factor Authentication アダプターをインストールしておく必要があります。 サポートされているバージョンであれば、サーバーを別のコンピューターにインストールすることができ、AD FS アダプターを AD FS フェデレーション サーバーに別個にインストールすることができます。 
* Multi-Factor Authentication AD FS アダプターのインストール ウィザードは、Active Directory 内に PhoneFactor Admins というセキュリティ グループを作成した後、このグループに AD FS サービス アカウントを追加します。 PhoneFactor Admins グループが実際に作成されていることと、AD FS サービス アカウントがこのグループのメンバーであることを、ドメイン コントローラーで確認することをお勧めします。 必要に応じて、AD FS サービス アカウントをドメイン コント ローラーの PhoneFactor Admins グループに手動で追加します。

### <a name="user-portal"></a>ユーザー ポータル
このポータルはインターネット インフォメーション サーバー (IIS) Web サイトで動作します。ここでは、セルフ サービス機能を使用できるほか、ユーザー管理機能一式が用意されています。 このコンポーネントを構成するには、次のガイドラインに従ってください。

* IIS 6 以降を使用する
* ASP.NET v2.0.507207 をインストールして登録する
* このサーバーが境界ネットワークにデプロイできることを確認する

### <a name="app-passwords"></a>アプリ パスワード
組織が SSO で Azure AD とフェデレーションされているときに Azure MFA を使用する場合は、アプリ パスワードを使用する際に次の点に注意してください。

* アプリ パスワードは Azure AD によって検証されます。したがってフェデレーションをバイパスします。 フェデレーションは、アプリ パスワードを設定するときにのみ使用されます。
* フェデレーション (SSO) ユーザーの場合、パスワードは組織 ID の中に保存されます。 ユーザーが退職した場合、その情報は、DirSync を使用してリアルタイムで 組織 ID に送信される必要があります。 アカウントの無効化/削除を同期させるには最大&3; 時間かかる可能性があり、Azure AD 内のアプリ パスワードの無効化/削除が遅れることがあります。
* オンプレミスのクライアント アクセス制御の設定は、アプリ パスワードでは受け入れられません。
* アプリ パスワードに対するオンプレミスの認証ログ/監査機能はありません。
* ある種の高度なアーキテクチャ設計では、2 段階認証をクライアントで使用するときに、認証場所によっては、組織のユーザー名とパスワードをアプリ パスワードと組み合わせて使用する必要があります。 オンプレミスのインフラストラクチャに対して認証するクライアントの場合は、組織のユーザー名とパスワードを使用します。 Azure AD に対して認証するクライアントはアプリケーション パスワードを使用します。
* 既定では、ユーザーはアプリ パスワードを作成できません。 ユーザーにアプリ パスワードの作成を許可する必要がある場合は、**[ブラウザーではないアプリケーションへのサインイン用にアプリケーション パスワードの作成を許可する]** オプションを選択します。

## <a name="additional-considerations"></a>追加の考慮事項
オンプレミスでデプロイされる各コンポーネントの追加の考慮事項とガイダンスについては、次の一覧を参照してください。

- [Active Directory フェデレーション サービス](multi-factor-authentication-get-started-adfs.md)を使用した Azure Multi-Factor Authentication のセットアップ。
- [RADUIS 認証](multi-factor-authentication-get-started-server-radius.md)を使用した Azure MFA Server のセットアップと構成。
- [IIS 認証](multi-factor-authentication-get-started-server-iis.md)を使用した Azure MFA Server のセットアップと構成。
- [Windows 認証](multi-factor-authentication-get-started-server-windows.md)を使用した Azure MFA Server のセットアップと構成。
- [LDAP 認証](multi-factor-authentication-get-started-server-ldap.md)を使用した Azure MFA Server のセットアップと構成。
- [RADIUS を使用したリモート デスクトップ ゲートウェイと Multi-Factor Authentication Server](multi-factor-authentication-get-started-server-rdg.md) での Azure MFA Server のセットアップと構成。
- Azure MFA Server と [Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md) 間の同期のセットアップと構成。
- [Azure Multi-Factor Authentication Server モバイル アプリ Web サービスのデプロイ](multi-factor-authentication-get-started-server-webservice.md)。
- Cisco ASA、Citrix Netscaler、Juniper/Pulse Secure などの VPN アプライアンスでの LDAP または RADIUS を使用した [Azure Multi-Factor Authentication による高度な VPN の構成](multi-factor-authentication-advanced-vpn-configurations.md)。

## <a name="next-steps"></a>次のステップ
この記事では Azure MFA のベスト プラクティスに重点を置いて説明しましたが、このほかにも、MFA のデプロイを計画する際に利用できるリソースがあります。 このプロセスで役立つ重要な記事を次に示します。

* [Azure Multi-Factor Authentication のレポート](multi-factor-authentication-manage-reports.md)
* [2 段階認証登録エクスペリエンス](multi-factor-authentication-end-user-first-time.md)
* [Azure Multi-Factor Authentication についてよく寄せられる質問 (FAQ)](multi-factor-authentication-faq.md)


