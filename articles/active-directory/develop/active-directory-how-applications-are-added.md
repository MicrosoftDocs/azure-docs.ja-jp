---
title: "Azure Active Directory にアプリケーションを追加する方法。"
description: "この記事では、アプリケーションを Azure Active Directory のインスタンスに追加する方法について説明します。"
services: active-directory
documentationcenter: 
author: shoatman
manager: kbrint
editor: 
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2016
ms.author: shoatman
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 65c8a2c020d5ef7ce47905927a248b9e73d2ed1b
ms.lasthandoff: 03/29/2017


---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>アプリケーションを Azure AD に追加する方法と理由
Azure Active Directory のインスタンスでアプリケーションの一覧を表示したときに最初に理解しにくいことの 1 つは、アプリケーションの元の場所と追加された理由を理解することです。  この記事では、ディレクトリでのアプリケーションの表現方法と、アプリケーションがディレクトリに追加された方法を理解するのに役立つコンテキストについて説明します。

## <a name="what-services-does-azure-ad-provide-to-applications"></a>Azure AD がアプリケーションに提供するサービス
アプリケーションは、Azure AD が提供するサービスを利用するために Azure AD に追加されます。  次のようなサービスがあります。

* アプリの認証と承認
* ユーザーの認証と承認
* フェデレーションまたはパスワードを使用するシングル サインオン (SSO)
* ユーザーのプロビジョニングと同期
* ロール ベースのアクセス制御。ディレクトリを使用して、アプリ内でロールに基づく承認チェックを実行するためのアプリケーション ロールを定義します。
* oAuth 承認サービス (Office 365 や他の Microsoft アプリによって API/リソースへのアクセスを承認するために使用されます)
* アプリケーションの発行とプロキシ。プライベート ネットワークからインターネットにアプリを発行します。

## <a name="how-are-applications-represented-in-the-directory"></a>ディレクトリでのアプリケーションの表現方法
Azure AD では、アプリケーションはアプリケーション オブジェクトとサービス プリンシパル オブジェクトという 2 つのオブジェクトを使用して表されます。  アプリケーション オブジェクトは 1 つで、"home"/"owner" または "publishing" ディレクトリにおいて登録されます。サービス プリンシパル オブジェクトは 1 つ以上あり、アプリケーションが動作するすべてのディレクトリを表します。  

アプリケーション オブジェクトでは Azure AD に対してアプリが記述されており (マルチテナント サービス)、以下のものを含むことができます (*注*: この一覧がすべてではありません)。

* 名前、ロゴ、発行元
* シークレット (アプリの認証に使用される対称キーまたは非対称キー)
* API の依存関係 (oAuth)
* 発行済みの API/リソース/スコープ (oAuth)
* アプリのロール (RBAC)
* SSO のメタデータと構成 (SSO)
* ユーザー プロビジョニングのメタデータと構成
* プロキシのメタデータと構成

サービス プリンシパルは、アプリケーションが機能するすべてのディレクトリ (ホーム ディレクトリを含みます) におけるアプリケーションの記録です。  サービス プリンシパルには次のような特徴があります。

* アプリ ID プロパティを介してアプリケーション オブジェクトを逆参照します
* ローカル ユーザーとグループ アプリ ロールの割り当てを記録します
* ローカル ユーザーとアプリに許可された管理アクセス許可を記録します
  * 例: 特定のユーザーの電子メールにアクセスするためのアプリに対するアクセス許可
* 条件付きアクセス ポリシーを含むローカル ポリシーを記録します
* アプリのローカル代替ローカル設定を記録します
  * 要求変換ルール
  * 属性マッピング (ユーザーのプロビジョニング)
  * テナント固有のアプリ ロール (アプリがカスタム ロールをサポートする場合)
  * 名前/ロゴ

### <a name="a-diagram-of-application-objects-and-service-principals-across-directories"></a>ディレクトリ間のアプリケーション オブジェクトとサービス プリンシパルの関係を示した図
![アプリケーション オブジェクトおよびサービス プリンシパルが Azure AD インスタンスに存在する方法を示す図。][apps_service_principals_directory]

上の図を見るとわかるように、  マイクロソフトはアプリケーションを発行するために使用する 2 つのディレクトリを内部的に保持しています (左側)。

* 1 つはマイクロソフトのアプリ用 (Microsoft サービス ディレクトリ)
* 1 つは事前に統合されたサードパーティのアプリ用 (アプリ ギャラリー ディレクトリ)

Azure AD と統合するアプリケーションのパブリッシャー/ベンダーには、発行ディレクトリが必要です   (SAAS ディレクトリ)。

ユーザーが自分で追加するアプリケーションは次のとおりです。

* ユーザーが開発したアプリ (AAD と統合)
* シングル サインオン用に接続したアプリ
* Azure AD アプリケーション プロキシを使用して発行したアプリ

### <a name="a-couple-of-notes-and-exceptions"></a>いくつかの注意事項と例外
* すべてのサービス プリンシパルがアプリケーション オブジェクトを逆参照するわけではありません。  どういうことでしょうか。 Azure AD が最初に構築された時点では、アプリケーションに提供されるサービスははるかに限定的であり、サービス プリンシパルはアプリ ID を確立するのに十分でした。  元のサービス プリンシパルは、Windows Server Active Directory サービス アカウントとよく似ていました。  このため、現在でも、先にアプリケーション オブジェクトを作成せずに、Azure AD PowerShell を使用してサービス プリンシパルを作成できます。  Graph API では、サービス プリンシパルを作成する前に、アプリケーション オブジェクトが必要です。
* 現在、このような情報の中にはプログラムによって公開されていないものがあります。  次の情報は UI でのみ使用できます。
  * 要求変換ルール
  * 属性マッピング (ユーザーのプロビジョニング)
* サービス プリンシパル オブジェクトおよびアプリケーション オブジェクトの詳細については、Azure AD Graph REST API のリファレンス ドキュメントを参照してください。  *ヒント*: Azure AD Graph API のドキュメントは、現在手に入る Azure AD に関するスキーマ参照に最も近いものです。  
  * [アプリケーション](https://msdn.microsoft.com/library/azure/dn151677.aspx)
  * [サービス プリンシパル](https://msdn.microsoft.com/library/azure/dn194452.aspx)

## <a name="how-are-apps-added-to-my-azure-ad-instance"></a>Azure AD インスタンスへのアプリの追加方法
Azure AD にアプリを追加するにはさまざまな方法があります。

* [Azure Active Directory アプリ ギャラリー](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Azure Active Directory に統合されているサードパーティのアプリにサインアップ/サインインします (例: [Smartsheet](https://app.smartsheet.com/b/home)、[DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
  * サインアップ/サインインの間に、ユーザーには、アプリに対してプロファイルにアクセスするためのアクセス許可および他のアクセス許可を与えることが求められます。  最初のユーザーがそれに同意した時点で、アプリを表すサービス プリンシパルがディレクトリに追加されます。
* [Office 365](http://products.office.com/)
  * Office 365 をサブスクライブするか、または試用を開始すると、Office 365 に関連するすべての機能を提供するために使用されるさまざまなサービスを表す 1 つまたは複数のサービス プリンシパルがディレクトリに作成されます。
  * SharePoint などの一部の Office 365 サービスは、ワークフローを含むコンポーネント間で安全に通信できるように、実行中にサービス プリンシパルを作成します。
* Microsoft Azure 管理ポータルで開発しているアプリを追加します。https://msdn.microsoft.com/library/azure/dn132599.aspx を参照してください。
* Visual Studio を使用して開発中のアプリを追加します。以下を参照してください。
  * [ASP.Net の認証方法](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
  * [接続されたサービス](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* [Azure AD アプリケーション プロキシ](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* SAML またはパスワード SSO を使用するシングル サインオンにアプリを接続します
* Azure でのさまざまな開発者エクスペリエンスや、デベロッパー センターでの API エクスプローラー エクスペリエンスなど、その他多数

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Azure AD インスタンスにアプリケーションを追加する権限のあるユーザー
グローバル管理者のみが以下のことを実行できます。

* Azure AD アプリ ギャラリー (事前に統合されたサード パーティのアプリ) からアプリを追加します
* Azure AD アプリケーション プロキシを使用してアプリを発行します

ディレクトリ内のすべてのユーザーは、開発しているアプリケーションを追加する権限を持ち、共有するアプリケーションまたは組織データにアクセスできるアプリケーションを決定できます。  *ユーザーがアプリにサインアップ/サインインしてアクセス許可を付与すると、サービス プリンシパルが作成されることに注意してください。*

これは最初は重要なことのように思えますが、次の点に留意してください。

* アプリは、長い間、登録されたりディレクトリに記録されたりしなくても、ユーザー認証に Windows Server Active Directory を利用することができました。  現在では、いくつのアプリがどのような目的でディレクトリを使用しているかを正確に認識できるようになっています。
* 管理者主導によるアプリの発行および登録プロセスは必要はありません。  Active Directory フェデレーション サービスでは、開発者に代わって管理者が証明書利用者としてアプリを追加することが必要な場合がありました。  今では開発者がセルフ サービスできます。
* ユーザーがビジネス目的で組織のアカウントを使用してアプリにサインイン/サインアップするのは良いことです。  後でユーザーが組織を離れると、使用していたアプリケーションのアカウントにアクセスできなくなります。
* どのようなデータがどのアプリケーションと共有されていたのかについての記録を残すのは良いことです。  データはかつてより移動性が高くなっており、誰がどのデータをどのアプリケーションと共有したのかについての明確な記録は便利です。
* Azure AD を oAuth に使用するアプリは、そのユーザーがアプリケーションに与えることができるアクセス許可および管理者が同意する必要があるアクセス許可を厳密に決定します。  管理者のみがより広い範囲で重要なアクセス許可に同意できる必要があることは言うまでもありません。
* ユーザーによるアプリの追加およびデータへのアクセスの許可は監査されるイベントなので、Azure 管理ポータルで監査レポートを見てアプリがディレクトリに追加された方法を判別できます。

**注:** *Microsoft 自体が何か月も既定の構成を使用して運用しています*

その結果、Azure 管理ポータルでディレクトリ構成を変更することにより、ディレクトリ内のユーザーがアプリケーションを追加したり、アプリケーションと共有する情報を決定したりできないようにすることが可能であることがわかっています。  Azure 管理ポータルのディレクトリの [構成] タブで、以下の構成にアクセスできます。

![統合されたアプリの設定を構成するための UI のスクリーンショット][app_settings]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次のステップ
Azure AD にアプリケーションを追加する方法およびアプリ用にサービスを構成する方法の詳細について学習します。

* 開発者: [Azure Active Directory にアプリケーションを統合する方法を学習します](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* 開発者: [GitHub で Azure Active Directory と統合されたアプリのサンプル コードを確認します](https://github.com/AzureADSamples)
* 開発者および IT プロフェッショナル: [Azure Active Directory Graph API の REST API ドキュメントを確認します](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* IT プロフェッショナル: [アプリ ギャラリーから Azure Active Directory に事前に統合されたアプリケーションを使用する方法を学習します](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* IT プロフェッショナル: [特定の事前統合アプリの構成に関するチュートリアルを探します](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* IT プロフェッショナル: [Azure Active Directory アプリケーション プロキシを使用してアプリを発行する方法について学習します](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## <a name="see-also"></a>関連項目
* [Article Index for Application Management in Azure Active Directory](../active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]:../media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg

