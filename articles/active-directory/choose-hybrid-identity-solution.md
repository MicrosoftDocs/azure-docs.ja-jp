---
title: "Azure ハイブリッド ID ソリューションの選択 | Microsoft Docs"
description: "組織に最適な ID ガバナンスの決定を行えるように、ハイブリッド ID ソリューションの基本および推奨事項について説明します。"
keywords: 
author: jeffgilb
manager: femila
ms.reviewer: jsnow
ms.author: jeffgilb
ms.date: 7/5/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 5838e3276765f4f074bca2e3cae81b17edfa7c69
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017

---

# <a name="microsoft-hybrid-identity-solutions"></a>Microsoft ハイブリッド ID ソリューション
[Microsoft Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) ハイブリッド ID ソリューションを使用すると、オンプレミスのディレクトリ オブジェクトと Azure AD を同期したまま、オンプレミスでユーザーを管理できます。 オンプレミスの Windows Server Active Directory と Azure AD との同期を計画する際に最初に行う決断は、同期済み ID を使用するかフェデレーション済み ID を使用するかです。 同期済み ID (およびオプションでパスワード ハッシュ) を使用すると、ユーザーは、オンプレミスとクラウドベースの両方の組織リソースへのアクセスに同じパスワードを使用できます。 シングル サインオン (SSO) やオンプレミスの MFA などのより高度なシナリオ要件では、Active Directory フェデレーション サービス (AD FS) をフェデレーション済み ID にデプロイする必要があります。 

ハイブリッド ID の構成にはいくつかのオプションがあります。 この記事では、デプロイメントの容易さ、特定の ID、アクセス管理のニーズに基づいて、最適なものを選択するための情報を組織に提供します。 組織のニーズに最も合う ID モデルを検討するとき、時間、既存のインフラストラクチャ、複雑さ、コストも考慮する必要があります。 これらの要因は組織ごとに異なり、時間の経過とともに変化する場合があります。 ただし、要件が変化する場合、異なる ID モデルに柔軟に切り替えることもできます。

> [!TIP]
> これらのソリューションは、すべて [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) によって配信されます。

## <a name="synchronized-identity"></a>同期済み ID 
同期済み ID は、オンプレミスのディレクトリ オブジェクト (ユーザーおよびグループ) と Azure AD を同期する最も簡単な方法です。 

![同期済みハイブリッド ID](./media/choose-hybrid-identity-solution/synchronized-identity.png)

同期済み ID は最も簡単で高速なメソッドですが、ユーザーは、クラウドベースのリソース用に別のパスワードを管理する必要があります。 これを回避するには、(オプションで) Azure AD ディレクトリに[ユーザー パスワードのハッシュを同期](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization)することも可能です。 パスワード ハッシュを同期すると、ユーザーは、オンプレミスで使用しているものと同じユーザー名とパスワードを使用してクラウドベースの組織リソースにログインできます。 Azure AD Connect は、オンプレミスのディレクトリの変更を定期的にチェックし、Azure AD ディレクトリを同期された状態に保ちます。 オンプレミスの Active Directory でユーザー属性またはパスワードが変更されると、Azure AD で自動的に更新されます。 

多くの組織においては Office 365、SaaS アプリケーション、およびその他の Azure AD ベースのリソースへのユーザー サインインを可能にする必要があるのみなので、その場合は既定のパスワードの同期オプションをお勧めします。 これが役に立たない場合、パススルー認証と AD FS のいずれかを選択する必要があります。

> [!TIP]
> ユーザーのパスワードは、実際のユーザーのパスワードを表すハッシュ値の形式でオンプレミスの Windows Server Active Directory に格納されます。 ハッシュ値は、一方向の数学関数 (ハッシュ アルゴリズム) の結果として求められます。 一方向の関数の結果をパスワードのプレーンテキスト バージョンに戻す方法はありません。 パスワードのハッシュを使用してオンプレミスのネットワークにサインインすることはできません。 パスワードの同期を選択すると、Azure AD Connect は、オンプレミスの Active Directory からパスワード ハッシュを抽出し、パスワード ハッシュに追加のセキュリティ処理を適用してから Azure AD に同期します。 パスワードの同期とパスワードの書き戻しを組み合わせて使用すると、Azure AD でセルフ サービス パスワードのリセットを有効にすることもできます。 また、企業ネットワークに接続されたドメイン参加済みコンピューターのユーザーはシングル サインオン (SSO) を有効化できます。 シングル サインオンにおいて、有効になっているユーザーはユーザー名のみを入力して、クラウド リソースに安全にアクセスできます。 

## <a name="pass-through-authentication"></a>パススルー認証
[Azure AD のパススルー認証](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication)は、オンプレミスの Active Directory を使用して、Azure AD ベースのサービスの単純なパスワード検証ソリューションを提供します。 組織のセキュリティとコンプライアンスのポリシーが、ハッシュされたフォームであっても、ユーザーのパスワードの送信を許可しておらず、ドメインに参加したデバイスのデスクトップの SSO をサポートする必要があるだけの場合、パススルー認証を使用して評価することをお勧めします。 パススルー認証では、DMZ でのデプロイは必要ありません。よって、AD FS と比較するとデプロイ インフラストラクチャが単純です。 この認証方法により、ユーザーが Azure AD を使用してサインインするとき、ユーザーのパスワードがオンプレミスの Active Directory に対して直接検証されます。

![パススルー認証](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

パススルー認証では、複雑なネットワーク インフラストラクチャの必要はなく、クラウドにオンプレミス パスワードを格納する必要もありません。 パススルー認証は、シングル サインオンと組み合わせて使用すると、Azure AD やその他のクラウド サービスにサインインするときに、本当の意味で認証が統合されます。

パススルー認証は Azure AD Connect を使用して構成されます。これは、パスワードの検証要求をリッスンするシンプルなオンプレミス エージェントを使用します。 エージェントは複数のコンピューターに簡単にデプロイでき、高可用性と負荷分散を実現できます。 すべての通信は送信のみであるため、DMZ にコネクタをインストールする必要がありません。 コネクタを使用する際のサーバー コンピューターの要件は次のとおりです。

- Windows Server 2012 R2 以降
- ユーザーの検証が行われるフォレスト内のドメインに参加済みであること

> [!NOTE]
> Azure AD パススルー認証は、現在プレビュー中で、Web ブラウザー ベースのクライアントと、最新の認証をサポートする Office クライアントでサポートされています。 従来の Office クライアントや Exchange ActiveSync (モバイル デバイス上のネイティブ電子メール クライアントを含む) など、サポートされていないクライアントの場合、同等の最新の認証手段を使用することをお勧めします。 最新の認証手段を使用すると、パススルー認証が利用できるだけでなく、多要素認証などの条件付きアクセス ポリシーを適用することもできます。 

現在のところ、Azure AD に参加している Windows 10 デバイスを使用している場合は、パススルー認証はサポートされません。 ただし、自動フォールバックとしてパスワード ハッシュ同期を使用して、Windows 10 および前記の従来のクライアントをサポートできます。 プレビューでは、Azure AD Connect のサインイン オプションとしてパススルー認証を選択した場合、パスワード ハッシュ同期が既定で有効になります。


## <a name="federated-identity-ad-fs"></a>フェデレーション ID (AD FS)
Office 365 やその他のクラウド サービスにユーザーがアクセスする方法をさらに制御するために、[Active Directory フェデレーション サービス (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server-2016) を使用して、シングル サイン オン (SSO) でディレクトリの同期をセットアップできます。 AD FS でユーザーのサインインのフェデレーション処理を行うと、ユーザーの資格情報を検証するオンプレミスのサーバーに認証が委任されます。 このモデルでは、オンプレミスの Active Directory の資格情報が Azure AD に渡されることはありません。

![フェデレーション ID](./media/choose-hybrid-identity-solution/federated-identity.png)

このサインイン方法は、ID フェデレーションとも呼ばれ、すべてのユーザーの認証がオンプレミスで制御され、管理者はより厳格なアクセス制御を実装できます。 AD FS を使用した ID フェデレーションは、最も複雑なオプションであり、オンプレミスの環境に追加のサーバーをデプロイする必要があります。 ID フェデレーションは、Active Directory と AD FS インフラストラクチャの年中無休 (1 日 24 時間、週 7 日間) のサポートを提供することも約束します。 オンプレミスのインターネット アクセス、ドメイン コントローラー、または AD FS サーバーを使用できなくなった場合にユーザーがクラウド サービスにサインインできなくなるため、この高度なサポートが必要です。

> [!TIP]
> Active Directory フェデレーション サービス (AD FS) でフェデレーションを使用する場合、AD FS インフラストラクチャで障害が発生した際のバックアップとして、オプションでパスワード同期を設定することもできます。


## <a name="common-scenarios-and-recommendations"></a>一般的なシナリオと推奨事項
以降では、ハイブリッド ID とアクセス管理に関する一般的なシナリオをいくつか取り上げると共に、それぞれのシナリオで最適と思われるハイブリッド ID オプションを紹介します。

|必要事項|PWS と SSO<sup>1</sup>| PTA と SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|新しいユーザー、連絡先、およびオンプレミスの Active Directory で作成したグループ アカウントを自動的にクラウドに同期|![推奨](./media/choose-hybrid-identity-solution/ic195031.png)| ![推奨](./media/choose-hybrid-identity-solution/ic195031.png) |![推奨](./media/choose-hybrid-identity-solution/ic195031.png)|
|テナントを Office 365 ハイブリッド シナリオにセット アップ|![推奨](./media/choose-hybrid-identity-solution/ic195031.png)| ![推奨](./media/choose-hybrid-identity-solution/ic195031.png) |![推奨](./media/choose-hybrid-identity-solution/ic195031.png)|
|ユーザーがオンプレミスのパスワードを使用してサインインしたりクラウド サービスにアクセスしたりすることを有効化|![推奨](./media/choose-hybrid-identity-solution/ic195031.png)| ![推奨](./media/choose-hybrid-identity-solution/ic195031.png) |![推奨](./media/choose-hybrid-identity-solution/ic195031.png)|
|企業の資格情報を使用したシングル サインオンの実装|![推奨](./media/choose-hybrid-identity-solution/ic195031.png)| ![推奨](./media/choose-hybrid-identity-solution/ic195031.png) |![推奨](./media/choose-hybrid-identity-solution/ic195031.png)|
|クラウドにパスワード ハッシュを保存しないことを確認| |![推奨](./media/choose-hybrid-identity-solution/ic195031.png)|![推奨](./media/choose-hybrid-identity-solution/ic195031.png)|
|オンプレミスの Multi-factor Authentication ソリューションを有効化| | |![推奨](./media/choose-hybrid-identity-solution/ic195031.png)|
|ユーザー<sup>4</sup>のスマートカード認証をサポート| | |![推奨](./media/choose-hybrid-identity-solution/ic195031.png)|
|Office ポータル および Windows 10 デスクトップにパスワードの有効期限通知を表示| | |![推奨](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> シングル サインオンによるパスワード同期。 

> <sup>2</sup> パススルー認証およびシングル サインオン。 

> <sup>3</sup> AD FS を使用したフェデレーション シングル サインオン。

> <sup>4</sup> AD FS は、エンタープライズ PKI と統合すると、証明書を使用したサインインが可能になります。 これらの証明書は、MDM や GPO などの信頼できるプロビジョニング チャネル、スマートカードの証明書 (PIV/CAC カードなど) または Hello for Business (証明書信頼) を経由してデプロイされるソフト証明書を使用できます。 スマートカード認証のサポートの詳細については、[このブログ](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)を参照してください。


## <a name="next-steps"></a>次のステップ
[Azure の概念実証環境で詳しく学ぶ](https://aka.ms/aad-poc)

[Azure AD Connect のインストール](http://go.microsoft.com/fwlink/?LinkId=615771)

[ハイブリッド ID 同期の監視](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)


