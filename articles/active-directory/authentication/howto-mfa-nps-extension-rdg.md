---
title: RDG を Azure MFA NPS 拡張機能と統合する - Azure Active Directory
description: Microsoft Azure のネットワーク ポリシー サーバー拡張機能を使って、リモート デスクトップ ゲートウェイ インフラストラクチャを Azure MFA と統合します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22efe84826684e37abee65d64df13d63081aa79
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848342"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>ネットワーク ポリシー サーバー (NPS) 拡張機能と Azure AD を使用したリモート デスクトップ ゲートウェイ インフラストラクチャの統合

この記事では、Microsoft Azure のネットワーク ポリシー サーバー (NPS) 拡張機能を使用して、リモート デスクトップ ゲートウェイ インフラストラクチャを Azure Multi-Factor Authentication (MFA) と統合する方法について詳しく説明します。

Azure のネットワーク ポリシー サーバー (NPS) 拡張機能を使用すると、Azure のクラウドベースの [Multi-Factor Authentication (MFA)](multi-factor-authentication.md) を使用して、リモート認証ダイヤルイン ユーザー サービス (RADIUS) クライアント認証を保護することができます。 このソリューションは、ユーザーのサインインとトランザクションにセキュリティの第 2 レイヤーを追加するための 2 段階認証を提供します。

この記事では、Azure の NPS 拡張機能を使用して、NPS インフラストラクチャを Azure MFA と統合する手順について順を追って説明します。 これにより、リモート デスクトップ ゲートウェイにサインインしようとするユーザーを確実に検証できるようになります。

> [!NOTE]
> この記事は MFA サーバーのデプロイでは使用せず、Azure MFA (クラウドベース) のデプロイのみで使用してください。

ネットワーク ポリシーとアクセス サービス (NPS) により、組織は次のことができるようになります。

* 接続できるユーザー、接続が許可される時間帯、接続の期間、クライアントが接続に使用する必要があるセキュリティのレベルなどを指定して、ネットワーク要求を管理および制御するための集約された場所をそれぞれ定義できます。 これらのポリシーは、VPN またはリモート デスクトップ (RD) ゲートウェイ サーバーごとに指定するのではなく、集約された場所で一度に指定できます。 RADIUS プロトコルは、一元化された認証、承認、アカウンティング (AAA) を提供します。
* デバイスにネットワーク リソースへの無制限のアクセスを許可するか制限付きアクセスを許可するかを決定する、ネットワーク アクセス保護 (NAP) クライアント正常性ポリシーを制定し、強制できます。
* 802.1x 対応ワイヤレス アクセス ポイントとイーサネット スイッチへのアクセスに認証と承認を強制する手段を提供できます。

一般に、組織では NPS (RADIUS) を使用して VPN ポリシーの管理を簡素化し、一元化しています。 にもかかわらず、多くの組織が、NPS を使用してリモート デスクトップの接続承認ポリシー (RD CAP) の管理も簡素化し、一元化しています。

組織において NPS を Azure MFA とも統合することで、セキュリティを強化し、高水準のコンプライアンスを提供することができます。 このことは、リモート デスクトップ ゲートウェイにサインインする際の 2 段階認証をユーザーが確実に制定するための助けとなります。 ユーザーはアクセスの許可を得るために、ユーザー名/パスワードの組み合わせをユーザーが独自に管理している情報と共に提供する必要があります。 この情報は、信頼性があり、簡単に複製できないもの (携帯電話番号、固定電話番号、モバイル デバイス上のアプリケーションなど) である必要があります。 RDG は現在、2FA のために電話と Microsoft 認証子アプリ メソッドからのプッシュ通知をサポートしています。 サポートされている認証方法の詳細については、[ユーザーが使用できる認証方法の決定](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use)に関するセクションを参照してください。

Azure の NPS 拡張機能を利用できるようになる前は、NPS と Azure MFA の統合環境の 2 段階認証の実装を希望するお客様は、「[RADIUS を使用したリモート デスクトップ ゲートウェイと Azure Multi-Factor Authentication Server](howto-mfaserver-nps-rdg.md)」に記載されているように、オンプレミス環境に別の MFA サーバーを構成し、管理する必要がありました。

Azure の NPS 拡張機能の登場により、オンプレミス ベースの MFA ソリューションとクラウド ベースの MFA ソリューションのどちらを RADIUS クライアント認証の保護用にデプロイするかを組織が選択できるようになりました。

## <a name="authentication-flow"></a>認証フロー

ユーザーは、リモート デスクトップ ゲートウェイ経由でのネットワーク リソースへのアクセスの許可を得るために、1 つの RD 接続承認ポリシー (RD CAP) と 1 つの RD リソース承認ポリシー (RD RAP) で指定された条件を満たす必要があります。 RD CAP では、RD ゲートウェイへの接続を許可するユーザーを指定します。 RD RAP では、ユーザーに RD ゲートウェイ経由での接続を許可するネットワーク リソース (リモート デスクトップやリモート アプリなど) を指定します。

RD ゲートウェイは、RD CAP に集約型ポリシー ストアを使用するように構成できます。 RD RAP は RD ゲートウェイで処理されるので、集約型ポリシーを使用することはできません。 RD CAP に集約型ポリシー ストアを使用するように構成された RD ゲートウェイの例として、セントラル ポリシー ストアとして機能する別の NPS サーバーの RADIUS クライアントがあります。

Azure の NPS 拡張機能を NPS およびリモート デスクトップ ゲートウェイと統合した場合、正常な認証フローは次のようになります。

1. リモート デスクトップ ゲートウェイ サーバーが、リソース (リモート デスクトップ セッションなど) に接続するための認証要求をリモート デスクトップ ユーザーから受信します。 RADIUS クライアントとして機能するリモート デスクトップ ゲートウェイ サーバーは、要求を RADIUS Access-Request メッセージに変換し、NPS 拡張機能がインストールされている RADIUS (NPS) サーバーにメッセージを送信します。
1. Active Directory でユーザー名とパスワードの組み合わせが検証され、ユーザーが認証されます。
1. NPS 接続要求とネットワーク ポリシーで指定されているすべての条件が満たされていれば (時刻やグループ メンバーシップの制約など)、NPS 拡張機能によって、Azure MFA を使用したセカンダリ認証の要求がトリガーされます。
1. Azure MFA は Azure AD と通信してユーザーの詳細を取得し、サポートされている方法を使用してセカンダリ認証を実行します。
1. MFA チャレンジが成功すると、Azure MFA は NPS 拡張機能に結果を伝えます。
1. 拡張機能がインストールされている NPS サーバーは、RD CAP ポリシーの RADIUS Access-Accept メッセージをリモート デスクトップ ゲートウェイ サーバーに送信します。
1. ユーザーは、要求したネットワーク リソースへの RD ゲートウェイ経由でのアクセスを許可されます。

## <a name="prerequisites"></a>前提条件

このセクションでは、Azure MFA をリモート デスクトップ ゲートウェイと統合する前に必要な前提条件について詳しく説明します。 作業を開始する前に、次の前提条件を満たしておく必要があります。  

* リモート デスクトップ サービス (RDS) インフラストラクチャ
* Azure MFA のライセンス
* Windows Server ソフトウェア
* ネットワーク ポリシーとアクセス サービス (NPS) のロール
* オンプレミスの Active Directory と同期された Azure Active Directory
* Azure Active Directory の GUID ID

### <a name="remote-desktop-services-rds-infrastructure"></a>リモート デスクトップ サービス (RDS) インフラストラクチャ

正常に稼働しているリモート デスクトップ サービス (RDS) インフラストラクチャが必要です。 このインフラストラクチャがない場合は、次のクイックスタート テンプレートを使用して、Azure 上に簡単に作成できます。[Create Remote Desktop Session Collection deployment ](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment)(リモート デスクトップ セッション コレクション デプロイの作成)。

テストのためにオンプレミスの RDS インフラストラクチャを手動ですばやく作成したい場合は、これをデプロイする手順に従います。
**詳細情報**: [Azure クイックスタートを使用した RDS のデプロイ](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure)と[基本的な RDS インフラストラクチャのデプロイ](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)。

### <a name="azure-mfa-license"></a>Azure MFA のライセンス

Azure MFA のライセンスが必要です。Azure AD Premium、またはそれが含まれているバンドルを通して入手できます。 使用量ベースの Azure MFA のライセンス (ユーザーごと、認証ごとのライセンスなど) は、NPS 拡張機能に対応していません。 詳細については、「[Azure Multi-Factor Authentication の入手方法](concept-mfa-licensing.md)」をご覧ください。 テスト目的で。試用版サブスクリプションをご利用いただけます。

### <a name="windows-server-software"></a>Windows Server ソフトウェア

NPS 拡張機能を使用するには、NPS 役割サービスがインストールされた Windows Server 2008 R2 SP1 以上が必要です。 このセクションの手順はすべて Windows Server 2016 を使用して実行されました。

### <a name="network-policy-and-access-services-nps-role"></a>ネットワーク ポリシーとアクセス サービス (NPS) のロール

NPS 役割サービスは、RADIUS サーバー/クライアントの機能とネットワーク アクセス ポリシー正常性サービスを提供します。 この役割は、インフラストラクチャ内の少なくとも 2 台のコンピューター (リモート デスクトップ ゲートウェイと別のメンバー サーバーまたはドメイン コントローラー) にインストールする必要があります。 既定では、この役割はリモート デスクトップ ゲートウェイとして構成されているコンピューターに既に存在します。  また、ドメイン コントローラーやメンバー サーバーなど、別のコンピューターにも NPS の役割をインストールする必要があります。

Windows Server 2012 以前に NPS 役割サービスをインストールする方法については、「[Install a NAP Health Policy Server (NAP 正常性ポリシー サーバーのインストール)](https://technet.microsoft.com/library/dd296890.aspx)」をご覧ください。 NPS をドメイン コントローラーにインストールする際の推奨事項など、NPS のベスト プラクティスについては、「[Best Practices for NPS (NPS のベスト プラクティス)](https://technet.microsoft.com/library/cc771746)」をご覧ください。

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>オンプレミスの Active Directory と同期された Azure Active Directory

NPS 拡張機能を使用するには、オンプレミスのユーザーを Azure AD と同期し、ユーザーの MFA を有効する必要があります。 このセクションでは、AD Connect を使用してオンプレミスのユーザーが Azure AD と同期されていることを前提としています。 Azure AD Connect については、「[オンプレミスのディレクトリと Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)」をご覧ください。

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory の GUID ID

NPS 拡張機能をインストールするには、Azure AD の GUID が必要です。 Azure AD の GUID を確認する手順については後述します。

## <a name="configure-multi-factor-authentication"></a>Multi-Factor Authentication の構成

このセクションでは、Azure MFA をリモート デスクトップ ゲートウェイと統合する手順について説明します。 ユーザーが多要素デバイスまたはアプリケーションを自己登録するには、管理者が Azure MFA サービスを構成しておく必要があります。

[クラウドでの Azure Multi-Factor Authentication の概要](howto-mfa-getstarted.md)に関する記事に記載されている手順に従って、Azure AD ユーザーの MFA を有効にします。

### <a name="configure-accounts-for-two-step-verification"></a>2 段階認証用にアカウントを構成する

アカウントの MFA を有効にすると、2 段階認証を使用して認証される 2 つ目の認証要素に使用する信頼済みデバイスの構成を正常に完了するまで、MFA ポリシーによって管理されたリソースにはサインインできません。

「[Azure Multi-Factor Authentication とは何ですか](../user-help/multi-factor-authentication-end-user.md)」に記載されている手順に従って、ユーザー アカウントで MFA 用のデバイスを正しく構成します。

## <a name="install-and-configure-nps-extension"></a>NPS 拡張機能のインストールと構成

このセクションでは、リモート デスクトップ ゲートウェイでのクライアント認証に Azure MFA を使用するように RDS インフラストラクチャを構成する手順について説明します。

### <a name="acquire-azure-active-directory-guid-id"></a>Azure Active Directory の GUID ID を取得する

NPS 拡張機能の構成の一環として、管理者資格情報と Azure AD テナントの Azure AD ID を入力する必要があります。 次の手順は、テナント ID を取得する方法を示しています。

1. Azure テナントの全体管理者として [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。
1. **[プロパティ]** を選択します。
1. [プロパティ] ブレードで、次のように [ディレクトリ ID] の横の **[コピー]** アイコンをクリックして、ID をクリップボードにコピーします。

   ![Azure portal からのディレクトリ ID の取得](./media/howto-mfa-nps-extension-rdg/image1.png)

### <a name="install-the-nps-extension"></a>NPS 拡張機能のインストール

ネットワーク ポリシーとアクセス サービス (NPS) の役割がインストールされているサーバーに NPS 拡張機能をインストールします。 これが、設計で RADIUS サーバーとして機能します。

> [!Important]
> リモート デスクトップ ゲートウェイ サーバーには NPS 拡張機能をインストールしないでください。
>

1. [NPS 拡張機能](https://aka.ms/npsmfa)をダウンロードします。
1. セットアップ実行可能ファイル (NpsExtnForAzureMfaInstaller.exe) を NPS サーバーにコピーします。
1. NPS サーバーで **NpsExtnForAzureMfaInstaller.exe** をダブルクリックします。 メッセージが表示されたら、 **[実行]** をクリックします。
1. [NPS Extension For Azure MFA Setup]\(Azure MFA セットアップの NPS 拡張機能\) ダイアログ ボックスで、 **[ライセンス条項と条件に同意します。]** チェック ボックスをオンにして、 **[インストール]** をクリックします。
1. [NPS Extension For Azure MFA Setup]\(Azure MFA セットアップの NPS 拡張機能\) ダイアログ ボックスで、 **[閉じる]** をクリックします。

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>PowerShell スクリプトを使用して NPS 拡張機能で使用する証明書を構成する

次に、セキュリティで保護された通信と保証を確保するために、NPS 拡張機能で使用する証明書を構成する必要があります。 NPS コンポーネントには、NPS で使用する自己署名証明書を構成する Windows PowerShell スクリプトが含まれています。

このスクリプトは、次のアクションを実行します。

* 自己署名証明書を作成する
* Azure AD のサービス プリンシパルに証明書の公開キーを関連付ける
* ローカル コンピューターのストアに証明書を格納する
* ネットワーク ユーザーに証明書の秘密キーへのアクセスを許可する
* ネットワーク ポリシー サーバー サービスを再起動する

独自の証明書を使用する場合は、Azure AD のサービス プリンシパルへの証明書の公開キーの関連付けなどを行う必要があります。

スクリプトを使用するには、Azure AD の管理者資格情報と、先ほどコピーした Azure AD のテナント ID を拡張機能に提供します。 NPS 拡張機能がインストールされている各 NPS サーバーでスクリプトを実行します。 次に、次を実行します。

1. 管理用の Windows PowerShell プロンプトを開きます。
1. PowerShell プロンプトで「`cd ‘c:\Program Files\Microsoft\AzureMfa\Config’`」と入力し、**Enter** キーを押します。
1. 「`.\AzureMfaNpsExtnConfigSetup.ps1`」と入力して **Enter** キーを押します。 Azure Active Directory PowerShell モジュールがインストールされているかどうかがチェックされます。 インストールされていない場合は、スクリプトによってモジュールがインストールされます。

   ![Azure AD PowerShell での AzureMfaNpsExtnConfigSetup.ps1 の実行](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. PowerShell モジュールのインストールの確認後、Azure Active Directory PowerShell モジュールのダイアログ ボックスが表示されます。 ダイアログ ボックスで、Azure AD の管理者資格情報とパスワードを入力し、 **[サインイン]** をクリックします。

   ![PowerShell での Azure AD への認証](./media/howto-mfa-nps-extension-rdg/image5.png)

1. メッセージが表示されたら、先ほどクリップボードにコピーしたディレクトリ ID を貼り付け、**Enter** キーを押します。

   ![PowerShell でのディレクトリ ID の入力](./media/howto-mfa-nps-extension-rdg/image6.png)

1. スクリプトによって自己署名証明書が作成され、他の構成変更が実行されます。 出力は次の画像のようになります。

   ![自己署名証明書を示す PowerShell の出力](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>リモート デスクトップ ゲートウェイでの NPS コンポーネントの構成

このセクションでは、リモート デスクトップ ゲートウェイの接続承認ポリシーと他の RADIUS 設定を構成します。

認証フローでは、リモート デスクトップ ゲートウェイと、NPS 拡張機能がインストールされている NPS サーバー間で RADIUS メッセージを交換する必要があります。 つまり、リモート デスクトップ ゲートウェイと NPS 拡張機能がインストールされている NPS サーバーの両方で RADIUS クライアント設定を構成する必要があります。

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>セントラル ストアを使用するようにリモート デスクトップ ゲートウェイの接続承認ポリシーを構成する

リモート デスクトップの接続承認ポリシー (RD CAP) では、リモート デスクトップ ゲートウェイ サーバーに接続するための要件を指定します。 RD CAP は、ローカルに保存することも (既定)、NPS を実行している RD CAP のセントラル ストアに保存することもできます。 RDS で Azure MFA の統合を構成するには、セントラル ストアの使用を指定する必要があります。

1. RD ゲートウェイ サーバーで**サーバー マネージャー**を開きます。
1. メニューの **[ツール]** をクリックし、 **[リモート デスクトップ サービス]** をポイントして、 **[リモート デスクトップ ゲートウェイ マネージャー]** をクリックします。
1. RD ゲートウェイ マネージャーで、 **\[サーバー名\] (ローカル)** を右クリックし、 **[プロパティ]** をクリックします。
1. プロパティ ダイアログ ボックスで、 **[RD CAP ストア]** タブを選択します。
1. [RD CAP ストア] タブで、 **[NPS を実行するセントラル サーバー]** を選択します。 
1. **[NPS を実行するサーバーの名前または IP アドレスの入力]** フィールドに、NPS 拡張機能がインストールされているサーバーの IP アドレスまたはサーバー名を入力します。

   ![NPS サーバーの名前または IP アドレスを入力する](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. **[追加]** をクリックします。
1. **[共有シークレット]** ダイアログ ボックスで、共有シークレットを入力し、 **[OK]** をクリックします。 この共有シークレットを記録し、記録を安全な場所に保管してください。

   >[!NOTE]
   >共有シークレットは、RADIUS サーバーと RADIUS クライアント間の信頼を確立するために使用されます。 長い複雑なシークレットを作成してください。
   >

   ![信頼を確立するための共有シークレットの作成](./media/howto-mfa-nps-extension-rdg/image11.png)

1. **[OK]** をクリックしてダイアログ ボックスを閉じます。

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>リモート デスクトップ ゲートウェイの NPS で RADIUS のタイムアウト値を構成する

ユーザーの資格情報の検証から、2 段階認証の実行、応答の受信、RADIUS メッセージへの応答までの時間を確保するために、RADIUS のタイムアウト値を調整する必要があります。

1. RD ゲートウェイ サーバーで、サーバー マネージャーを開きます。 メニューで **[ツール]** をクリックし、 **[ネットワーク ポリシー サーバー]** をクリックします。
1. **[NPS (ローカル)]** コンソールで、 **[RADIUS クライアントとサーバー]** を展開し、 **[Remote RADIUS Server]\(リモート RADIUS サーバー\)** を選択します。

   ![リモート RADIUS サーバーを示すネットワーク ポリシー サーバー管理コンソール](./media/howto-mfa-nps-extension-rdg/image12.png)

1. 詳細ウィンドウで、 **[TS GATEWAY SERVER GROUP]** をダブルクリックします。

   >[!NOTE]
   >この RADIUS サーバー グループは、NPS ポリシーのセントラル サーバーを構成したときに作成されました。 RD ゲートウェイは、このサーバーまたはサーバーのグループ (グループに複数のサーバーが含まれている場合) に RADIUS メッセージを転送します。
   >

1. **[TS GATEWAY SERVER GROUP のプロパティ]** ダイアログ ボックスで、RD CAP を保存するように構成した NPS サーバーの IP アドレスまたは名前を選択し、 **[編集]** をクリックします。

   ![以前に構成された NPS サーバーの IP または名前を選択する](./media/howto-mfa-nps-extension-rdg/image13.png)

1. **[RADIUS サーバーの編集]** ダイアログ ボックスで、 **[負荷分散]** タブを選択します。
1. **[負荷分散]** タブの **[要求が破棄されたとみなされる応答待ち時間 (秒数)]** フィールドで、既定値の 3 を 30 ～ 60 秒の値に変更します。
1. **[サーバーが利用不可能とみなされる要求間隔 (秒数)]** フィールドで、既定値の 30 秒を、前の手順で指定した値以上の値に変更します。

   ![[負荷分散] タブで RADIUS サーバーのタイムアウト設定を編集する](./media/howto-mfa-nps-extension-rdg/image14.png)

1. **[OK]** を 2 回クリックして、ダイアログ ボックスを閉じます。

### <a name="verify-connection-request-policies"></a>接続要求ポリシーを確認する

既定では、接続承認ポリシーに集約型ポリシー ストアを使用するように RD ゲートウェイを構成すると、CAP 要求を NPS サーバーに転送するように RD ゲートウェイが構成されます。 Azure MFA 拡張機能がインストールされている NPS サーバーが、RADIUS アクセス要求を処理します。 次の手順は、既定の接続要求ポリシーを確認する方法を示しています。  

1. RD ゲートウェイの [NPS (ローカル)] コンソールで、 **[ポリシー]** を展開し、 **[接続要求ポリシー]** を選択します。
1. **[TS GATEWAY AUTHORIZATION POLICY]** をダブルクリックします。
1. **[TS GATEWAY AUTHORIZATION POLICY のプロパティ]** ダイアログ ボックスで、 **[設定]** タブをクリックします。
1. **[設定]** タブで、[接続要求の転送] の **[認証]** をクリックします。 認証のために要求を転送するように RADIUS クライアントが構成されています。

   ![サーバー グループを指定する認証設定を構成する](./media/howto-mfa-nps-extension-rdg/image15.png)

1. **[キャンセル]** をクリックします。

>[!NOTE]
> 接続要求ポリシーの作成の詳細については、記事「[接続要求ポリシーの構成](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy)」のドキュメントを参照してください。 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>NPS 拡張機能がインストールされているサーバーでの NPS の構成

NPS 拡張機能がインストールされている NPS サーバーは、リモート デスクトップ ゲートウェイの NPS サーバーと RADIUS メッセージを交換できる必要があります。 このメッセージ交換を可能にするには、NPS 拡張サービスがインストールされているサーバーで NPS コンポーネントを構成する必要があります。

### <a name="register-server-in-active-directory"></a>Active Directory にサーバーを登録する

このシナリオで正常に機能させるには、NPS サーバーを Active Directory に登録する必要があります。

1. NPS サーバーで、**サーバー マネージャー**を開きます。
1. サーバー マネージャーで **[ツール]** をクリックし、 **[ネットワーク ポリシー サーバー]** をクリックします。
1. [ネットワーク ポリシー サーバー] コンソールで、 **[NPS (ローカル)]** を右クリックし、 **[Active Directory にサーバーを登録]** をクリックします。
1. **[OK]** を 2 回クリックします。

   ![Active Directory に NPS サーバーを登録する](./media/howto-mfa-nps-extension-rdg/image16.png)

1. 次の手順で使用するため、コンソールは開いたままにしておきます。

### <a name="create-and-configure-radius-client"></a>RADIUS クライアントを作成して構成する

リモート デスクトップ ゲートウェイは、NPS サーバーの RADIUS クライアントとして構成する必要があります。

1. NPS 拡張機能がインストールされている NPS サーバーで、 **[NPS (ローカル)]** コンソールの **[RADIUS クライアント]** を右クリックし、 **[新規]** をクリックします。

   ![NPS コンソールで新しい RADIUS クライアントを作成する](./media/howto-mfa-nps-extension-rdg/image17.png)

1. **[新しい RADIUS クライアント]** ダイアログ ボックスで、リモート デスクトップ ゲートウェイ サーバーのフレンドリ名 (例: _Gateway_) と、IP アドレスまたは DNS 名を指定します。
1. **[共有シークレット]** フィールドと **[共有シークレットの確認入力]** フィールドに、前に使用したシークレットを入力します。

   ![フレンドリ名と IP または DNS アドレスを構成する](./media/howto-mfa-nps-extension-rdg/image18.png)

1. **[OK]** をクリックして、[新しい RADIUS クライアント] ダイアログ ボックスを閉じます。

### <a name="configure-network-policy"></a>ネットワーク ポリシーを構成する

Azure MFA 拡張機能がインストールされている NPS サーバーは、接続承認ポリシー (CAP) の指定された集約型ポリシー ストアであることをここで思い出してください。 そのため、有効な接続要求を承認するために、NPS サーバーに CAP を実装する必要があります。  

1. NPS サーバーで [NPS (ローカル)] コンソールを開き、 **[ポリシー]** を展開し、 **[ネットワーク ポリシー]** をクリックします。
1. **[他のアクセス サーバーへの接続]** を右クリックし、 **[ポリシーの複製]** をクリックします。

   ![他のアクセス サーバーへの接続ポリシーを複製する](./media/howto-mfa-nps-extension-rdg/image19.png)

1. **[Copy of Connections to other access servers]\(他のアクセス サーバーへの接続のコピー\)** を右クリックし、 **[プロパティ]** をクリックします。
1. **[Copy of Connections to other access servers]\(他のアクセス サーバーへの接続のコピー\)** ダイアログ ボックスで、 **[ポリシー名]** に適切な名前 (例: _RDG_CAP_) を入力します。 **[ポリシーを有効にする]** チェック ボックスをオンにし、 **[アクセスを許可する]** を選択します。 必要に応じて、**ネットワーク アクセス サーバーの種類**で **[リモート デスクトップ ゲートウェイ]** を選択します。このフィールドは、 **[未指定]** のままにしておくこともできます。

   ![ポリシーに名前を付け、有効にして、アクセス権を付与する](./media/howto-mfa-nps-extension-rdg/image21.png)

1. **[制約]** タブで、 **[認証方法をネゴシエートせずにクライアントに接続を許可する]** をオンにします。

   ![クライアントの接続を許可する認証方法を変更する](./media/howto-mfa-nps-extension-rdg/image22.png)

1. 必要に応じて、 **[条件]** タブをクリックし、接続が承認されるために満たす必要がある条件 (特定の Windows グループのメンバーシップなど) を追加します。

   ![必要に応じて接続条件を指定する](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Click **OK**. 対応するヘルプ トピックの表示を促すメッセージが表示されたら、 **[いいえ]** をクリックします。
1. 新しいポリシーが一覧の一番上に表示されていること、ポリシーが有効になっていること、ポリシーがアクセスを許可していることを確認します。

   ![ポリシーを一覧の先頭に移動する](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>構成の確認

構成を確認するには、適切な RDP クライアントでリモート デスクトップ ゲートウェイにサインインする必要があります。 接続承認ポリシーで許可され、Azure MFA が有効になっているアカウントを使用してください。

次の画像に示すように、**リモート デスクトップ Web アクセス** ページを使用できます。

![リモート デスクトップ Web アクセスでのテスト](./media/howto-mfa-nps-extension-rdg/image25.png)

プライマリ認証の資格情報の入力が正常に完了すると、次に示すように、[リモート デスクトップ接続] ダイアログ ボックスにリモート接続の開始の状態が表示されます。 

Azure MFA で以前に構成したセカンダリ認証方法で認証に成功すると、リソースに接続されます。 ただし、セカンダリ認証に失敗した場合、リソースへのアクセスは拒否されます。 

![リモート接続を開始するリモート デスクトップ接続](./media/howto-mfa-nps-extension-rdg/image26.png)

次の例では、Windows Phone の Authenticator アプリを使用してセカンダリ認証を提供しています。

![検証を示す Windows Phone Authenticator アプリの例](./media/howto-mfa-nps-extension-rdg/image27.png)

セカンダリ認証方法を使用した認証に成功すると、リモート デスクトップ ゲートウェイに通常どおりログインします。 ただし、信頼済みデバイスでモバイル アプリを使用したセカンダリ認証方法を使用する必要があるため、他の方法よりもサインイン プロセスの安全性が高まります。

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>成功したログオン イベントのイベント ビューアーのログを表示する

Windows イベント ビューアーのログで成功したサインイン イベントを表示するには、次の Windows PowerShell コマンドを発行して、Windows ターミナル サービスのログと Windows のセキュリティ ログを照会します。

ゲートウェイの操作ログ _(Event Viewer\Applications and Services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_ の成功したサインイン イベントを照会するには、次の PowerShell コマンドを使用します。

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* このコマンドは、ユーザーがリソース承認ポリシーの要件 (RD RAP) を満たしており、アクセスが許可されたことを示す Windows イベントを表示します。

![PowerShell を使用したイベントの表示](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* このコマンドは、ユーザーが接続承認ポリシーの要件を満たしていることを示すイベントを表示します。

![PowerShell を使用した接続承認ポリシーの表示](./media/howto-mfa-nps-extension-rdg/image29.png)

このログを表示し、イベント ID 300 と 200 でフィルター処理することもできます。 イベント ビューアーのセキュリティ ログの成功したログオン イベントを照会するには、次のコマンドを使用します。

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* このコマンドは、セントラル NPS サーバーまたは RD ゲートウェイ サーバーで実行できます。

![成功したログオン イベントのサンプル](./media/howto-mfa-nps-extension-rdg/image30.png)

次に示すように、セキュリティ ログや [ネットワーク ポリシーとアクセス サービス] カスタム ビューを表示することもできます。

![ネットワーク ポリシーとアクセス サービス イベント ビューアー](./media/howto-mfa-nps-extension-rdg/image31.png)

Azure MFA の NPS 拡張機能がインストールされているサーバーで、拡張機能に固有のイベント ビューアーのアプリケーション ログ (_アプリケーションとサービス ログ\Microsoft\AzureMfa_) を確認できます。

![イベント ビューアー AuthZ アプリケーション ログ](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>トラブルシューティング ガイド

構成が予想どおりに動作していない場合、トラブルシューティングでは、まず、Azure MFA を使用するようにユーザーが構成されていることを確認します。 ユーザーに、[Azure Portal](https://portal.azure.com) に接続してもらいます。 ユーザーがセカンダリ検証を求められ、正常に認証できれば、Azure MFA の構成には問題がないことがわかります。

ユーザーの Azure MFA が機能している場合、関連するイベント ログを確認する必要があります。 関連するイベント ログとして、前のセクションで説明したセキュリティ イベント ログ、ゲートウェイの操作ログ、Azure MFA ログがあります。

失敗したログオン イベント (イベント ID 6273) を示すセキュリティ ログの出力例を次に示します。

![失敗したログオン イベントのサンプル](./media/howto-mfa-nps-extension-rdg/image33.png)

Azure MFA ログの関連イベントを次に示します。

![イベント ビューアーでの Azure MFA ログのサンプル](./media/howto-mfa-nps-extension-rdg/image34.png)

高度なトラブルシューティング オプションを実行するには、NPS サービスがインストールされているサーバーで NPS データベース形式のログ ファイルを参照します。 これらのログ ファイルは、コンマ区切りのテキスト ファイルとして _%SystemRoot%\System32\Logs_ フォルダーに作成されています。

これらのログ ファイルについては、「[Interpret NPS Database Format Log Files (NPS データベース形式のログ ファイルの解釈)](https://technet.microsoft.com/library/cc771748.aspx)」をご覧ください。 これらのログ ファイルのエントリは、スプレッドシートやデータベースにインポートしないと解釈するのが難しい可能性があります。 ログ ファイルの解釈に役立つ IAS パーサーがオンラインでいくつか見つかります。

次の画像は、このようなダウンロード可能な[シェアウェア アプリケーション](https://www.deepsoftware.com/iasviewer)の出力を示しています。

![シェアウェア アプリ IAS パーサーのサンプル](./media/howto-mfa-nps-extension-rdg/image35.png)

最後に、その他のトラブルシューティング オプションとして、[Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx) などのプロトコル アナライザーを使用できます。

次の Microsoft Message Analyzer の画像は RADIUS プロトコルでフィルター処理された、ユーザー名 **CONTOSO\AliceC** を含むネットワーク トラフィックを示しています。

![フィルター処理されたトラフィックを示す Microsoft Message Analyzer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>次の手順

[Azure Multi-Factor Authentication の入手方法](concept-mfa-licensing.md)

[RADIUS を使用したリモート デスクトップ ゲートウェイと Multi-Factor Authentication Server](howto-mfaserver-nps-rdg.md)

[オンプレミスのディレクトリと Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)
