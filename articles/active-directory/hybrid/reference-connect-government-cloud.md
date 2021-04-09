---
title: Azure AD Connect:Azure Government クラウドのハイブリッド ID に関する考慮事項
description: Azure Government クラウドに Azure AD Connect をデプロイする場合の特別な考慮事項。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbb4298d0d9a9d7589c4a2055b4d55a0b852f951
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97504357"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Azure Government クラウドのハイブリッド ID に関する考慮事項

この記事では、Microsoft Azure Government クラウドでハイブリッド環境を統合する場合の考慮事項について説明します。 この情報は、Azure Government クラウドを操作している管理者と設計者のための参考資料として提供されています。

> [!NOTE]
> Microsoft Active Directory 環境 (オンプレミスのもの、または同じクラウド インスタンスの一部である IaaS にホストされているもの) を Azure Government クラウドと統合するには、[Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) の最新リリースにアップグレードする必要があります。

米国政府の国防総省エンドポイントの完全一覧については、[こちらのドキュメント](/office365/enterprise/office-365-u-s-government-dod-endpoints)を参照してください。

## <a name="azure-ad-pass-through-authentication"></a>Azure AD パススルー認証

次の情報では、パススルー認証と Azure Government クラウドの実装について説明されています。

### <a name="allow-access-to-urls"></a>URL へのアクセスを許可する

パススルー認証エージェントをデプロイする前に、お使いのサーバーと Azure AD の間にファイアウォールがあるかどうかを確認します。 Domain Name System (DNS) でブロックされているプログラムまたは安全なプログラムがお使いのファイアウォールまたはプロキシで許可される場合、次の接続を追加します。

> [!NOTE]
> 次のガイダンスは、Azure Government 環境用に [Azure AD アプリケーション プロキシ コネクタ](../manage-apps/what-is-application-proxy.md)をインストールする場合にも適用されます。

|URL |用途|
|-----|-----|
|&#42;.msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|エージェントではこれらの URL を使用し、Azure AD クラウド サービスと通信します。 |
|`mscrl.microsoft.us:80` </br>`crl.microsoft.us:80` </br>`ocsp.msocsp.us:80` </br>`www.microsoft.us:80`| エージェントでは、これらの URL を使用して、証明書が検証されます。|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;.microsoftonline.us </br>&#42;.microsoftonline-p.us </br>&#42;.msauth.net </br>&#42;.msauthimages.net </br>&#42;.msecnd.net</br>&#42;.msftauth.net </br>&#42;.msftauthimages.net</br>&#42;.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctldl.windowsupdate.us:80| エージェントでは、登録プロセスの間にこれらの URL が使用されます。

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Azure Government クラウド用にエージェントをインストールする

次の手順で Azure Government クラウド用にエージェントをインストールします。

1. コマンドライン ターミナルで、エージェントをインストールする実行可能ファイルが格納されているフォルダーに移動します。
1. Azure Government 用のインストールを指定する次のコマンドを実行します。

   パススルー認証の場合:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   アプリケーション プロキシの場合:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>シングル サインオン

### <a name="set-up-your-azure-ad-connect-server"></a>Azure AD Connect サーバーをセットアップする

サインオン方法としてパススルー認証を使用する場合、他に確認すべき前提条件はありません。 サインオン方法としてパスワード ハッシュ同期を使用する場合や、Azure AD Connect と Azure AD の間にファイアウォールがある場合は、次の点を確保してください。

- Azure AD Connect バージョン 1.1.644.0 以降を使用しています。
- DNS でブロックされているプログラムまたは安全なプログラムがファイアウォールまたはプロキシによって許可されている場合は、&#42;.msappproxy.us の URL に対するポート 443 での接続を追加します。

  そうでない場合は、毎週更新される Azure データセンターの IP 範囲へのアクセスを許可します。 この前提条件は、その機能を有効にした場合にのみ適用されます。 実際のユーザー サインオンに必要な条件ではありません。

### <a name="roll-out-seamless-single-sign-on"></a>シームレス シングル サインオンのロールアウト

次の指示に従い、Azure AD シームレス シングル サインオンをユーザーに段階的にロールアウトします。 まず、Active Directory でグループ ポリシーを利用し、すべてのユーザー、または選択したユーザーのイントラネット ゾーン設定に Azure AD の URL `https://autologon.microsoft.us` を追加します。

また、グループ ポリシーを使用し、 **[スクリプトを介したステータス バーの更新を許可する]** というイントラネットのゾーン ポリシー設定を有効にする必要があります。

## <a name="browser-considerations"></a>ブラウザーの考慮事項

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (すべてのプラットフォーム)

Mozilla Firefox は、Kerberos 認証を自動的には使用しません。 各ユーザーが、次の手順に従って、Firefox の設定に Azure AD の URL を手動で追加する必要があります。

1. Firefox を実行して、アドレス バーに「 **about:config** 」と入力します。 表示されたすべての通知を無視します。
1.  **network.negotiate-auth.trusted-uris**  という優先設定を探します。 この設定には、Kerberos 認証で Firefox が信頼するサイトが一覧表示されています。
1. 設定を右クリックし、 **[変更]** を選択します。
1. ボックスに「`https://autologon.microsoft.us`」と入力します。
1.  **[OK]**   を選択してから、ブラウザーをもう一度開きます。

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Chromium に基づく Microsoft Edge (すべてのプラットフォーム)

お使いの環境で  `AuthNegotiateDelegateAllowlist`  または `AuthServerAllowlist`  ポリシー設定をオーバーライドした場合は、それらの設定に Azure AD の URL `https://autologon.microsoft.us` を確実に追加してください。

### <a name="google-chrome-all-platforms"></a>Google Chrome (すべてのプラットフォーム)

お使いの環境で  `AuthNegotiateDelegateWhitelist`  または `AuthServerWhitelist`  ポリシー設定をオーバーライドした場合は、それらの設定に Azure AD の URL `https://autologon.microsoft.us` を確実に追加してください。

## <a name="next-steps"></a>次のステップ

- [パススルー認証](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [シングル サインオン](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
