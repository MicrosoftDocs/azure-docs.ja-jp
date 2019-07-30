---
title: ネットワーク ポリシー サーバー拡張機能を使用して VPN を Azure MFA と統合する - Azure Active Directory
description: Microsoft Azure のネットワーク ポリシー サーバー拡張機能を使用して VPN インフラストラクチャを Azure MFA と統合します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4afe5c89a83f251ee354c955b6dc28b0bccc9e6c
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561136"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Azure のネットワーク ポリシー サーバー拡張機能を使用して VPN インフラストラクチャを Azure MFA と統合する

## <a name="overview"></a>概要

Azure のネットワーク ポリシー サーバー (NPS) 拡張機能を使用すると、組織はクラウド ベースの [Azure Multi-Factor Authentication (MFA)](howto-mfaserver-nps-rdg.md) を使用して 2 段階認証を提供することで、リモート認証ダイヤルイン ユーザー サービス (RADIUS) クライアント認証を保護することができます。

この記事では、Azure の NPS 拡張機能を使用して、NPS インフラストラクチャを MFA と統合する手順を説明します。 このプロセスを実行すると、VPN を使用してネットワークに接続しようとするユーザーに対して安全な 2 段階認証が使用されるようになります。

ネットワーク ポリシーとアクセス サービスにより、組織は次のことができるようになります。

* ネットワーク要求を管理および制御するための一元化された場所を割り当てて、以下を指定します。

  * 接続できるユーザー

  * 接続を許可する時刻

  * 接続の期間

  * クライアントが接続に使用する必要があるセキュリティのレベル

    各 VPN やリモート デスクトップ ゲートウェイ サーバーにポリシーを指定するのではなく、一元化された場所で処理される場合のポリシーを指定します。 一元化された認証、承認、アカウンティング (AAA) を提供するために、RADIUS プロトコルが使用されます。

* デバイスにネットワーク リソースへの無制限のアクセスを許可するか制限付きアクセスを許可するかを決定する、ネットワーク アクセス保護 (NAP) クライアント正常性ポリシーを制定し、強制できます。

* 802.1x 対応ワイヤレス アクセス ポイントとイーサネット スイッチへのアクセスに認証と承認を強制する方法を提供できます。
  詳細については、「[Network Policy Server](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)」(ネットワーク ポリシー サーバー) を参照してください。

組織がセキュリティを強化し、高水準のコンプライアンスを実現するには、NPS を Azure Multi-Factor Authentication と統合して、ユーザーが 2 段階認証を使用して VPN サーバーの仮想ポートに接続する方法があります。 ユーザーはアクセスの許可を得るために、ユーザー名とパスワードの組み合わせを、ユーザーが管理している他の情報と共に提供する必要があります。 これは、信頼できる、簡単に複製できない情報にする必要があります。 たとえば、携帯電話番号、固定電話番号、モバイル デバイス上のアプリケーションなどです。

Azure の NPS 拡張機能を利用できるようになる前は、NPS と MFA の統合環境の 2 段階認証の実装を希望するお客様は、オンプレミス環境に別の MFA サーバーを構成し、管理する必要がありました。 このような種類の認証は、RADIUS を使用したリモート デスクトップ ゲートウェイと Multi-Factor Authentication Server のセットアップと構成で提供されていました。

組織は Azure の NPS 拡張機能を使用して、オンプレミス ベースの MFA ソリューションとクラウド ベースの MFA ソリューションのどちらを RADIUS クライアント認証の保護用にデプロイするかを選択できるようになりました。

## <a name="authentication-flow"></a>Authentication flow

ユーザーが VPN サーバー上の仮想ポートに接続する場合、最初に多様なプロトコルを使用して認証を受ける必要があります。 これらのプロトコルでは、ユーザー名とパスワードの組み合わせと、証明書ベースの認証方法を使用できます。

認証と ID の確認に加え、ユーザーには適切なダイヤルインのアクセス許可が必要です。 単純な実装では、アクセスを許可するこれらのダイヤルインのアクセス許可は、Active Directory ユーザー オブジェクトで直接設定します。

![[Active Directory ユーザーとコンピューター] ユーザー プロパティの [ダイヤルイン] タブ](./media/howto-mfa-nps-extension-vpn/image1.png)

単純な実装では、各 VPN サーバーは、各ローカル VPN サーバーで定義されているポリシーに基づいてアクセスを許可または拒否します。

大規模でスケーラブルな実装では、VPN アクセスを許可または拒否するポリシーは、RADIUS サーバーで一元化されます。 このような場合、VPN サーバーは、接続要求とアカウント メッセージを RADIUS サーバーに転送するアクセス サーバー (RADIUS クライアント) として機能します。 VPN サーバーの仮想ポートに接続するには、ユーザーは認証を受け、RADIUS サーバーで一元的に定義された条件を満たす必要があります。

Azure の NPS 拡張機能を NPS と統合した場合、正常な認証フローは次のようになります。

1. VPN サーバーが、リソース (リモート デスクトップ セッションなど) に接続するためのユーザー名とパスワードが含まれた認証要求を VPN ユーザーから受信します。
2. RADIUS クライアントとして機能する VPN サーバーは、要求を RADIUS *Access-Request* メッセージに変換し、NPS 拡張機能がインストールされている RADIUS サーバーに、(パスワードが暗号化された) メッセージを送信します。
3. Active Directory でユーザー名とパスワードの組み合わせが検証されます。 ユーザー名またはパスワードが正しくない場合、RADIUS サーバーは *Access-Reject* メッセージを送信します。
4. NPS 接続要求とネットワーク ポリシーで指定されているすべての条件が満たされていれば (時刻やグループ メンバーシップの制約など)、NPS 拡張機能によって、Azure Multi-Factor Authentication を使用したセカンダリ認証の要求がトリガーされます。
5. Azure Multi-Factor Authentication は Azure Active Directory と通信してユーザーの詳細を取得し、ユーザーが構成した方法 (携帯電話の呼び出し、テキスト メッセージ、モバイル アプリ) を使用してセカンダリ認証を実行します。
6. MFA チャレンジが成功すると、Azure Multi-Factor Authentication は結果を NPS 拡張機能に送信します。
7. 接続試行が認証され、承認されたら、拡張機能がインストールされている NPS は、RADIUS *Access-Accept* メッセージを VPN サーバー (RADIUS クライアント) に送信します。
8. ユーザーは、VPN サーバーの仮想ポートへのアクセスが許可され、暗号化された VPN トンネルを確立します。

## <a name="prerequisites"></a>前提条件

このセクションでは、MFA を VPN と統合する前に満たす必要がある前提条件について詳しく説明します。 作業を開始する前に、次の前提条件を満たしておく必要があります。

* VPN インフラストラクチャ
* ネットワーク ポリシーとアクセス サービス ロール
* Azure Multi-Factor Authentication のライセンス
* Windows Server ソフトウェア
* ライブラリ
* オンプレミスの Active Directory と同期された Azure Active Directory (Azure AD)
* Azure Active Directory の GUID ID

### <a name="vpn-infrastructure"></a>VPN インフラストラクチャ

この記事では、Microsoft Windows Server 2016 を使用する適切な作業用 VPN インフラストラクチャがあり、現時点では VPN サーバーが接続要求を RADIUS サーバーに転送するように構成されていないことを前提としています。 この記事では、中央の RADIUS サーバーを使用するように VPN インフラストラクチャを構成します。

適切な作業用 VPN インフラストラクチャがない場合は、Microsoft やサード パーティのサイトにある多数の VPN セットアップ チュートリアルで提供されるガイダンスに従って簡単に作成できます。

### <a name="the-network-policy-and-access-services-role"></a>ネットワーク ポリシーとアクセス サービス ロール

ネットワーク ポリシーとアクセス サービスは、RADIUS サーバーと RADIUS クライアントの機能を提供します。 この記事では、環境内のメンバー サーバーまたはドメイン コントローラーにネットワーク ポリシーとアクセス サービス ロールがインストールされていることを前提としています。 このガイドで、VPN 構成の RADIUS を構成します。 ネットワーク ポリシーとアクセス サービス ロールは、VPN サーバー*以外の*サーバーにインストールします。

Windows Server 2012 以降にネットワーク ポリシーとアクセス サービス ロール サービスをインストールする方法については、「[Install a NAP Health Policy Server](https://technet.microsoft.com/library/dd296890.aspx)」(NAP 正常性ポリシー サーバーのインストール) を参照してください。 Windows Server 2016 では、NAP は非推奨となります。 NPS をドメイン コントローラーにインストールする際の推奨事項など、NPS のベスト プラクティスについては、「[Best Practices for NPS](https://technet.microsoft.com/library/cc771746)」(NPS のベスト プラクティス) を参照してください。

### <a name="azure-mfa-license"></a>Azure MFA のライセンス

Azure Multi-Factor Authentication にはライセンスが必要です。ライセンスは、Azure AD Premium、Enterprise Mobility + Security、または Multi-Factor Authentication スタンドアロン ライセンスで入手できます。 使用量ベースの Azure MFA のライセンス (ユーザーごと、認証ごとのライセンスなど) は、NPS 拡張機能に対応していません。 詳細については、「[Azure Multi-Factor Authentication の入手方法](concept-mfa-licensing.md)」をご覧ください。 テスト目的で。試用版サブスクリプションをご利用いただけます。

### <a name="windows-server-software"></a>Windows Server ソフトウェア

NPS 拡張機能を使用するには、ネットワーク ポリシーとアクセス サービス ロールがインストールされた Windows Server 2008 R2 SP1 以降が必要です。 このガイドの手順はすべて Windows Server 2016 を使用して実行されました。

### <a name="libraries"></a>ライブラリ

NPS 拡張機能を含め、次のライブラリが自動的にインストールされます。

-   [Visual Studio 2013 (X64) の Visual C++ 再頒布可能パッケージ](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Windows PowerShell 用 Microsoft Azure Active Directory モジュール バージョン 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Microsoft Azure Active Directory PowerShell モジュールがまだインストールされていない場合は、セットアップ プロセスの一環で実行した構成スクリプトを使用してインストールされます。 このモジュールをまだインストールしていない場合、事前にインストールする必要はありません。

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>オンプレミスの Active Directory と同期された Azure Active Directory

NPS 拡張機能を使用するには、オンプレミスのユーザーを Azure Active Directory と同期し、ユーザーの MFA を有効する必要があります。 このガイドでは、Azure AD Connect を使用してオンプレミスのユーザーが Azure Active Directory と同期されていることを前提としています。 ユーザーの MFA を有効にする手順については後述します。

Azure AD Connect については、「[オンプレミスのディレクトリと Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)」を参照してください。

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory の GUID ID

NPS 拡張機能をインストールするには、Azure Active Directory の GUID が必要です。 Azure Active Directory の GUID を確認する手順については、次のセクションで説明します。

## <a name="configure-radius-for-vpn-connections"></a>VPN 接続用の RADIUS の構成

NPS ロールがメンバー サーバーにインストールされている場合、VPN 接続を要求する VPN クライアントを認証および承認するように構成する必要があります。 

このセクションでは、ネットワーク ポリシーとアクセス サービス ロールはインストールされているが、インフラストラクチャで使用できるように構成されていないことを前提としています。

> [!NOTE]
> 一元化された RADIUS サーバーを認証に使用する作業用 VPN サーバーが既にある場合は、このセクションを省略して構いません。
>

### <a name="register-server-in-active-directory"></a>Active Directory にサーバーを登録する

このシナリオで正常に機能させるには、NPS サーバーを Active Directory に登録する必要があります。

1. サーバー マネージャーを開きます。

2. サーバー マネージャーで、 **[ツール]** 、 **[ネットワーク ポリシー サーバー]** の順に選択します。

3. [ネットワーク ポリシー サーバー] コンソールで、 **[NPS (ローカル)]** を右クリックし、 **[Active Directory にサーバーを登録]** を選択します。 **[OK]** を 2 回選択します。

    ![[Active Directory にサーバーを登録] メニュー オプション](./media/howto-mfa-nps-extension-vpn/image2.png)

4. 次の手順で使用するため、コンソールは開いたままにしておきます。

### <a name="use-wizard-to-configure-the-radius-server"></a>ウィザードを使用して RADIUS サーバーを構成する

(ウィザード ベースの) 標準構成オプションまたは詳細構成オプションを使用して、RADIUS サーバーを構成できます。 このセクションでは、ウィザード ベースの標準構成オプションを使用していることを想定しています。

1. [ネットワーク ポリシー サーバー] コンソールで、 **[NPS (ローカル)]** を選択します。

2. **[標準構成]** で **[ダイヤルアップ接続または VPN 接続用の RADIUS サーバー]** を選択し、 **[VPN またはダイヤルアップを構成する]** を選択します。

    ![ダイヤルアップまたは VPN 接続用の RADIUS サーバーを構成する](./media/howto-mfa-nps-extension-vpn/image3.png)

3. **[ダイヤルアップまたは仮想プライベート ネットワークの接続の種類の選択]** ウィンドウで、 **[仮想プライベート ネットワーク (VPN) 接続]** を選択し、 **[次へ]** を選択します。

    ![仮想プライベート ネットワーク接続を構成する](./media/howto-mfa-nps-extension-vpn/image4.png)

4. **[ダイヤルアップまたは VPN サーバーの指定]** ウィンドウで、 **[追加]** を選択します。

5. **[新しい RADIUS クライアント]** ウィンドウで、フレンドリ名を指定し、VPN サーバーの解決可能な名前または IP アドレスを入力してから、共有シークレットのパスワードも入力します。 この共有シークレットのパスワードは、長い複雑なものにします。 このパスワードは次のセクションで必要になるので、メモしておきます。

    ![新しい RADIUS クライアントの作成ウィンドウ](./media/howto-mfa-nps-extension-vpn/image5.png)

6. **[OK]** を選択し、 **[次へ]** を選択します。

7. **[認証方法の構成]** ウィンドウで、既定の選択 ( **[Microsoft 暗号化認証バージョン 2 [MS-CHAPv2]]** ) をそのまま使用するか、別のオプションを選択し、 **[次へ]** を選択します。

    > [!NOTE]
    > 拡張認証プロトコル (EAP) を構成する場合は、Microsoft チャレンジ ハンドシェイク認証プロトコル (CHAPv2) または Protected Extensible Authentication Protocol (PEAP) を使用する必要があります。 他の EAP はサポートされていません。

8. **[ユーザー グループの指定]** ウィンドウで **[追加]** を選択し、適切なグループを選択します。 グループが存在しない場合は、すべてのユーザーにアクセスを許可するために、選択フィールドを空白のままにしておきます。

    ![アクセスを許可または拒否する [ユーザー グループの指定] ウィンドウ](./media/howto-mfa-nps-extension-vpn/image7.png)

9. **[次へ]** を選択します。

10. **[IP フィルターの指定]** ウィンドウで **[次へ]** を選択します。

11. **[暗号化設定の指定]** ウィンドウで既定の設定をそのまま使用し、 **[次へ]** を選択します。

    ![[暗号化設定の指定] ウィンドウ](./media/howto-mfa-nps-extension-vpn/image8.png)

12. **[領域名の指定]** ウィンドウでは、領域名を空白のままにし、既定の設定をそのまま使用して、 **[次へ]** を選択します。

    ![[領域名の指定] ウィンドウ](./media/howto-mfa-nps-extension-vpn/image9.png)

13. **[新しいダイヤルアップ接続または仮想プライベート ネットワーク接続および RADIUS クライアントの完了]** ウィンドウで、 **[完了]** を選択します。

    ![構成の完了ウィンドウ](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>RADIUS 構成を確認する

このセクションでは、ウィザードを使用して作成した構成について詳しく説明します。

1. ネットワーク ポリシー サーバーで、[NPS (ローカル)] コンソールの **[RADIUS クライアント]** を展開し、 **[RADIUS クライアント]** を選択します。

2. 詳細ウィンドウで、作成した RADIUS クライアントを右クリックし、 **[プロパティ]** を選択します。 RADIUS クライアント (VPN サーバー) のプロパティが次のように表示されます。

    ![VPN のプロパティと構成を確認する](./media/howto-mfa-nps-extension-vpn/image11.png)

3. **[キャンセル]** を選択します。

4. ネットワーク ポリシー サーバーで、[NPS (ローカル)] コンソールの **[ポリシー]** を展開し、 **[接続要求ポリシー]** を選択します。 次の図のように VPN 接続ポリシーが表示されます。

    ![VPN 接続ポリシーを示す接続要求ポリシー](./media/howto-mfa-nps-extension-vpn/image12.png)

5. **[ポリシー]** の **[ネットワーク ポリシー]** を選択します。 次の画像のような仮想プライベート ネットワーク (VPN) 接続ポリシーが表示されます。

    ![仮想プライベート ネットワーク接続ポリシーを示すネットワーク ポリシー](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>RADIUS 認証を使用するための VPN サーバーの構成

このセクションでは、RADIUS 認証を使用するように VPN サーバーを構成します。 このセクションでは、VPN サーバーの動作中の構成はあるが、RADIUS 認証を使用するように構成されていないことを前提としています。 VPN サーバーの構成後、構成が予想どおりに動作していることを確認します。

> [!NOTE]
> RADIUS 認証を使用する動作中の VPN サーバー構成が既にある場合は、このセクションを省略して構いません。
>

### <a name="configure-authentication-provider"></a>認証プロバイダーを構成する

1. VPN サーバーで、サーバー マネージャーを開きます。

2. サーバー マネージャーで **[ツール]** を選択し、 **[ルーティングとリモート アクセス]** を選択します。

3. **[ルーティングとリモート アクセス]** ウィンドウで、 **[\<サーバー名> (ローカル)]** を右クリックし、 **[プロパティ]** を選択します。

4. **[\<サーバー名> (ローカル) のプロパティ]** ウィンドウで、 **[セキュリティ]** タブを選択します。

5. **[セキュリティ]** タブで、 **[認証プロバイダー]** の **[RADIUS 認証]** を選択し、 **[構成]** を選択します。

    ![RADIUS 認証プロバイダーを構成する](./media/howto-mfa-nps-extension-vpn/image15.png)

6. **[RADIUS 認証]** ウィンドウで **[追加]** を選択します。

7. **[RADIUS サーバーの追加]** ウィンドウで、次の手順を実行します。

    a. **[サーバー名]** ボックスに、前のセクションで構成した RADIUS サーバーの名前または IP アドレスを入力します。

    b. **[共有シークレット]** で **[変更]** を選択し、先ほど作成してメモしておいた共有シークレットのパスワードを入力します。

    c. **[タイムアウト (秒)]** ボックスで、値として「**30**」を入力します。  
    タイムアウト値は、2 つ目の認証要素を完了するための十分な時間を確保するために必要です。

    ![タイムアウトを構成する [RADIUS サーバーの追加] ウィンドウ](./media/howto-mfa-nps-extension-vpn/image16.png)

8. **[OK]** を選択します。

### <a name="test-vpn-connectivity"></a>VPN 接続をテストする

このセクションでは、VPN 仮想ポートに接続しようとしたときに、VPN クライアントが RADIUS サーバーによって認証され、承認されることを確認します。 この手順では、VPN クライアントに Windows 10 を使用していることを前提としています。

> [!NOTE]
> VPN サーバーに接続するように VPN クライアントを既に構成しており、設定を保存済みの場合は、VPN 接続オブジェクトの構成と保存に関連する手順は省略して構いません。
>

1. VPN クライアント コンピューターで **[スタート]** ボタンを選択し、 **[設定]** ボタンを選択します。

2. **[Windows 設定]** ウィンドウで **[ネットワークとインターネット]** を選択します。

3. **[VPN]** を選択します。

4. **[VPN 接続を追加する]** を選択します。

5. **[VPN 接続を追加する]** ウィンドウの **[VPN プロバイダー]** ボックスで **[Windows (ビルトイン)]** を選択し、必要に応じて残りのフィールドを設定して、 **[保存]** を選択します。

    ![[VPN 接続を追加する] ウィンドウ](./media/howto-mfa-nps-extension-vpn/image17.png)

6. **[コントロール パネル]** に移動し、 **[ネットワークと共有センター]** を選択します。

7. **[アダプターの設定の変更]** を選択します。

    ![[ネットワークと共有センター] - [アダプターの設定の変更]](./media/howto-mfa-nps-extension-vpn/image18.png)

8. VPN ネットワーク接続を右クリックし、 **[プロパティ]** を選択します。

9. [VPN のプロパティ] ウィンドウで、 **[セキュリティ]** タブを選択します。

10. **[セキュリティ]** タブで、 **[Microsoft CHAP Version 2 (MS-CHAP v2)]** だけがオンになっていることを確認し、 **[OK]** を選択します。

    ![[次のプロトコルを許可する] オプション](./media/howto-mfa-nps-extension-vpn/image20.png)

11. VPN 接続を右クリックし、 **[接続]** を選択します。

12. **[設定]** ウィンドウで、 **[接続]** を選択します。  
    成功した接続は、次に示すように、RADIUS サーバーのセキュリティ ログにイベント ID 6272 として表示されます。

    ![成功した接続を示す [イベントのプロパティ] ウィンドウ](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>RADIUS のトラブルシューティング

認証と承認に一元化された RADIUS サーバーを使用するように VPN サーバーを構成するまでは VPN 構成が動作していたとします。 構成が動作していた場合、RADIUS サーバーの構成に誤りがあるか、無効なユーザー名またはパスワードを使用したことが原因で問題が発生している可能性があります。 たとえば、ユーザー名に代替 UPN サフィックスを使用している場合、サインイン試行が失敗する可能性があります。 同じアカウント名を使用することをお勧めします。

これらの問題のトラブルシューティングを行うときは、まず、RADIUS サーバーのセキュリティ イベント ログを調べることをお勧めします。 イベントの検索時間を節約するには、次に示すように、イベント ビューアーのロールベースの [ネットワーク ポリシーとアクセス サービス] カスタム ビューを使用します。 "イベント ID 6273" は、NPS がユーザーに対してアクセスを拒否したイベントを示しています。

![NPAS イベントを示すイベント ビューアー](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Multi-Factor Authentication の構成

ユーザーに Multi-Factor Authentication を構成する方法については、「[クラウドベースの Azure Multi-Factor Authentication のデプロイの計画](howto-mfa-getstarted.md#create-conditional-access-policy)」および「[アカウントへの 2 段階認証の設定](../user-help/multi-factor-authentication-end-user-first-time.md)」を参照してください

## <a name="install-and-configure-the-nps-extension"></a>NPS 拡張機能のインストールと構成

このセクションでは、VPN サーバーでのクライアント認証に MFA を使用するように VPN を構成する手順について説明します。

NPS 拡張機能をインストールして構成したら、このサーバーによって処理される RADIUS ベースのすべてのクライアント認証に MFA を使用する必要があります。 すべての VPN ユーザーを Azure Multi-Factor Authentication に登録していない場合、次のいずれかを実行できます。

* 別の RADIUS サーバーを設定して、MFA を使用するように構成されていないユーザーを認証します。

* ユーザーが Azure Multi-Factor Authentication に登録されている場合、チャレンジされたユーザーが 2 つ目の認証要素を提供できるようにするレジストリ エントリを作成します。

_HKLM\SOFTWARE\Microsoft\AzureMfa に REQUIRE_USER_MATCH_ という名前の新しい文字列値を作成し、値を *True* または *False* に設定します。

![[ユーザー照合が必要] 設定](./media/howto-mfa-nps-extension-vpn/image34.png)

値が *True* に設定されているか空の場合は、すべての認証要求が MFA チャレンジの対象となります。 値が *False* に設定されている場合は、Azure Multi-Factor Authentication に登録されているユーザーにのみ、MFA チャレンジが発行されます。 *False* の設定は、オンボード期間中にテスト環境または運用環境でのみ使用します。

### <a name="obtain-the-azure-active-directory-guid-id"></a>Azure Active Directory GUID ID の取得

NPS 拡張機能の構成の一環として、管理者資格情報と Azure AD テナントの ID を入力する必要があります。 次の手順で ID を取得します。

1. Azure テナントの全体管理者として [Azure Portal](https://portal.azure.com) にサインインします。

2. 左側のウィンドウで、 **[Azure Active Directory]** ボタンを選択します。

3. **[プロパティ]** を選択します。

4. Azure AD ID をコピーするには、 **[コピー]** ボタンを選択します。

    ![Azure portal での Azure AD ディレクトリ ID](./media/howto-mfa-nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>NPS 拡張機能のインストール

ネットワーク ポリシーとアクセス サービスの役割がインストールされ、設計で RADIUS サーバーとして機能するサーバーに、NPS 拡張機能をインストールする必要があります。 VPN サーバーには NPS 拡張機能を*インストールしないでください*。

1. [Microsoft ダウンロード センター](https://aka.ms/npsmfa)から NPS 拡張機能をダウンロードします。

2. セットアップ実行可能ファイル (*NpsExtnForAzureMfaInstaller.exe*) を NPS サーバーにコピーします。

3. NPS サーバーで **NpsExtnForAzureMfaInstaller.exe** をダブルクリックし、メッセージに従って **[実行]** を選択します。

4. **[NPS Extension For Azure MFA Setup]\(Azure MFA セットアップの NPS 拡張機能\)** ウィンドウで、 **[ライセンス条項と条件に同意します]** チェック ボックスをオンにして、 **[インストール]** を選択します。

    ![[NPS Extension For Azure MFA Setup]\(Azure MFA セットアップの NPS 拡張機能\) ウィンドウ](./media/howto-mfa-nps-extension-vpn/image36.png)

5. **[NPS Extension For Azure MFA Setup]\(Azure MFA セットアップの NPS 拡張機能\)** ウィンドウで、 **[閉じる]** を選択します。  

    ![[セットアップ完了] 確認ウィンドウ](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>PowerShell スクリプトを使用して NPS 拡張機能で使用する証明書を構成する

セキュリティで保護された通信と保証を確保するには、NPS 拡張機能で使用する証明書を構成します。 NPS コンポーネントには、NPS で使用する自己署名証明書を構成する Windows PowerShell スクリプトが含まれています。

このスクリプトは、次のアクションを実行します。

* 自己署名証明書を作成する。
* Azure AD のサービス プリンシパルに証明書の公開キーを関連付ける。
* ローカル コンピューターのストアに証明書を格納する。
* ネットワーク ユーザーに証明書の秘密キーへのアクセスを許可する。
* NPS サービスを再起動する。

独自の証明書を使用する場合は、Azure AD のサービス プリンシパルへの証明書の公開キーの関連付けなどを行う必要があります。

スクリプトを使用するには、Azure Active Directory の管理者資格情報と、先ほどコピーした Azure Active Directory のテナント ID を拡張機能に提供します。 NPS 拡張機能がインストールされている各 NPS サーバーでスクリプトを実行します。

1. Windows PowerShell を管理者として実行します。

2. PowerShell コマンド プロンプトで、「**cd "c:\Program Files\Microsoft\AzureMfa\Config"** 」と入力し、Enter キーを押します。

3. 次のコマンド プロンプトで「 **.\AzureMfaNpsExtnConfigSetup.ps1**」と入力し、Enter キーを押します。 このスクリプトで、Azure AD PowerShell モジュールがインストールされているかどうかがチェックされます。 インストールされていない場合は、スクリプトによってモジュールがインストールされます。

    ![AzureMfsNpsExtnConfigSetup.ps1 構成スクリプトの実行](./media/howto-mfa-nps-extension-vpn/image38.png)

    PowerShell モジュールのインストールの確認後、Azure Active Directory PowerShell モジュールのサインイン ウィンドウが表示されます。

4. Azure AD の管理者の資格情報とパスワードを入力し、 **[次へ]** を選択します。

    ![Azure AD PowerShell への認証](./media/howto-mfa-nps-extension-vpn/image39.png)

5. コマンド プロンプトに先ほどコピーしたテナント ID を貼り付け、Enter キーを押します。

    ![以前にコピーした Azure AD ディレクトリ ID を入力する](./media/howto-mfa-nps-extension-vpn/image40.png)

    スクリプトによって自己署名証明書が作成され、他の構成変更が実行されます。 出力は次の図のようになります。

    ![自己署名証明書を示す PowerShell ウィンドウ](./media/howto-mfa-nps-extension-vpn/image41.png)

6. サーバーを再起動します。

### <a name="verify-the-configuration"></a>構成を確認する

構成を確認するには、VPN サーバーとの新しい VPN 接続を確立する必要があります。 プライマリ認証用の資格情報の入力が正常に完了すると、次に示すように、セカンダリ認証が成功するまで待ってから VPN 接続が確立されます。

![Windows 設定の [VPN] ウィンドウ](./media/howto-mfa-nps-extension-vpn/image42.png)

Azure MFA で以前に構成したセカンダリ検証方法で認証に成功すると、リソースに接続されます。 ただし、セカンダリ認証に失敗した場合、リソースへのアクセスは拒否されます。

次の例では、Windows Phone の Microsoft Authenticator アプリでセカンダリ認証を提供しています。

![Windows Phone での MFA プロンプトの例](./media/howto-mfa-nps-extension-vpn/image43.png)

第 2 の方法を使用して正常に認証されると、VPN サーバーの仮想ポートへのアクセスが許可されます。 信頼済みデバイスでモバイル アプリを使用したセカンダリ認証方法を使用する必要があったため、ユーザー名とパスワードの組み合わせだけを使用する場合よりもサインイン プロセスの安全性が高まります。

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>成功したサインイン イベントのイベント ビューアーのログを表示する

Windows イベント ビューアーのログで成功したサインイン イベントを表示するには、次の PowerShell コマンドを入力して、NPS サーバーの Windows セキュリティ ログを照会します。

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![PowerShell セキュリティ イベント ビューアー](./media/howto-mfa-nps-extension-vpn/image44.png)

次に示すように、セキュリティ ログや [ネットワーク ポリシーとアクセス サービス] カスタム ビューを表示することもできます。

![ネットワーク ポリシー サーバー ログの例](./media/howto-mfa-nps-extension-vpn/image45.png)

Azure Multi-Factor Authentication の NPS 拡張機能がインストールされているサーバーで、拡張機能に固有のイベント ビューアーのアプリケーション ログ (*アプリケーションとサービス ログ\Microsoft\AzureMfa*) を確認できます。

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![イベント ビューアー AuthZ ログ ウィンドウの例](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>トラブルシューティング ガイド

構成が予想どおりに動作していない場合、トラブルシューティングでは、まず MFA を使用するようにユーザーが構成されていることを確認します。 ユーザーに、[Azure Portal](https://portal.azure.com) に接続してもらいます。 ユーザーがセカンダリ認証を求められ、正常に認証できれば、MFA の構成には問題がないことがわかります。

ユーザーの MFA が機能している場合、関連するイベント ビューアー ログを確認する必要があります。 関連するイベント ログとして、前のセクションで説明したセキュリティ イベント ログ、ゲートウェイの操作ログ、Azure Multi-Factor Authentication ログがあります。

失敗したサインイン イベント (イベント ID 6273) が表示されたセキュリティ ログの例を次に示します。

![失敗したサインイン イベントが表示されたセキュリティ ログ](./media/howto-mfa-nps-extension-vpn/image47.png)

Azure Multi-Factor Authentication ログの関連するイベントを次に示します。

![Azure Multi-Factor Authentication のログ](./media/howto-mfa-nps-extension-vpn/image48.png)

高度なトラブルシューティングを実行するには、NPS サービスがインストールされているサーバーで NPS データベース形式のログ ファイルを参照します。 ログ ファイルは、コンマ区切りのテキスト ファイルとして _%SystemRoot%\System32\Logs_ フォルダーに作成されています。 ログ ファイルの詳細については、「[Interpret NPS Database Format Log Files](https://technet.microsoft.com/library/cc771748.aspx)」(NPS データベース形式のログ ファイルの解釈) を参照してください。

これらのログ ファイルのエントリは、スプレッドシートやデータベースにインポートしないと解釈が困難です。 ログ ファイルを解釈する場合に役立つ Internet Authentication Service (IAS) 解析ツールは、オンラインで多数見つかります。 このようなダウンロード可能な[シェアウェア アプリケーション](https://www.deepsoftware.com/iasviewer)の出力を次に示します。

![シェアウェア アプリ IAS パーサーのサンプル](./media/howto-mfa-nps-extension-vpn/image49.png)

その他のトラブルシューティングには、Wireshark や [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx) などのプロトコル アナライザーを使用できます。 Wireshark の次の画像は、VPN サーバーと NPS 間の RADIUS メッセージを示しています。

![フィルター処理されたトラフィックを示す Microsoft Message Analyzer](./media/howto-mfa-nps-extension-vpn/image50.png)

詳細については、「[Azure Multi-Factor Authentication と既存の NPS インフラストラクチャの統合](howto-mfa-nps-extension.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

[Azure Multi-Factor Authentication の入手方法](concept-mfa-licensing.md)

[RADIUS を使用したリモート デスクトップ ゲートウェイと Multi-Factor Authentication Server](howto-mfaserver-nps-rdg.md)

[オンプレミスのディレクトリと Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)
