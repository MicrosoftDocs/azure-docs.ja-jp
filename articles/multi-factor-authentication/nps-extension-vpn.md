---
title: "NPS 拡張機能を使用した Azure MFA と VPN の統合 | Microsoft Docs"
description: "この記事では、Microsoft Azure のネットワーク ポリシー サーバー (NPS) 拡張機能を使用して、VPN インフラストラクチャを Azure MFA と統合する方法について説明します。"
services: active-directory
keywords: "Azure MFA, VPN の統合, Azure Active Directory, ネットワーク ポリシー サーバー拡張機能"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: kgremban
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: b47e9b321b2fd0d0db9762003531b0fe9f045f07
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-your-vpn-infrastructure-with-azure-multi-factor-authentication-mfa-using-the-network-policy-server-nps-extension-for-azure"></a>Azure のネットワーク ポリシー サーバー (NPS) 拡張機能を使用して、VPN インフラストラクチャを Azure Multi-Factor Authentication (MFA) と統合します。

## <a name="overview"></a>概要

Azure のネットワーク ポリシー サーバー (NPS) 拡張機能を使用すると、組織はクラウド ベースの [Azure Multi-Factor Authentication (MFA)](multi-factor-authentication-get-started-server-rdg.md) を使用して 2 段階認証を提供することで、リモート認証ダイヤルイン ユーザー サービス (RADIUS) クライアント認証を保護することができます。

この記事では、VPN を使用してネットワークへの接続を試みるユーザーに対して安全な 2 段階認証を有効にするために、Azure の NPS 拡張機能を使用して NPS インフラストラクチャを Azure MFA と統合する手順について説明します。 

ネットワーク ポリシーとアクセス サービス (NPS) により、組織は次のことができるようになります。

* ネットワーク要求を管理および制御するための中央の場所を指定して、接続できるユーザー、接続が許可される時刻、接続の期間、クライアントが接続に使用する必要があるセキュリティのレベルなどを指定できます。 これらのポリシーは、VPN サーバーまたはリモート デスクトップ (RD) ゲートウェイ サーバーごとに指定するのではなく、中央の場所で一度に指定できます。 一元化された認証、承認、アカウンティング (AAA) を提供するために、RADIUS プロトコルが使用されます。 
* デバイスにネットワーク リソースへの無制限のアクセスを許可するか制限付きアクセスを許可するかを決定する、ネットワーク アクセス保護 (NAP) クライアント正常性ポリシーを確立し、適用できます。
* 802.1x 対応ワイヤレス アクセス ポイントとイーサネット スイッチへのアクセスに認証と承認を適用する手段を提供できます。    

詳細については、「[Network Policy Server (NPS) (ネットワーク ポリシー サーバー (NPS))](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)」をご覧ください。 

セキュリティを強化し、高水準のコンプライアンスを提供するために、組織は NPS を Azure MFA と統合して、ユーザーが 2 段階認証を使用して VPN サーバーの仮想ポートに接続できるようにすることが可能です。 ユーザーはアクセスの許可を得るために、ユーザー名/パスワードの組み合わせと、ユーザーが管理している情報を提供する必要があります。 この情報は、信頼性があり、簡単に複製できないもの (携帯電話番号、固定電話番号、モバイル デバイス上のアプリケーションなど) である必要があります。

Azure の NPS 拡張機能を利用できるようになる前は、NPS と Azure MFA の統合環境の 2 段階認証の実装を希望するお客様は、「RADIUS を使用したリモート デスクトップ ゲートウェイと Azure Multi-Factor Authentication Server」に記載されているように、オンプレミス環境に別の MFA サーバーを構成し、管理する必要がありました。

Azure の NPS 拡張機能の提供により、組織は RADIUS クライアント認証を保護するために、オンプレミス ベースの MFA ソリューションとクラウド ベースの MFA ソリューションのどちらをデプロイするかを選択できるようになりました。
 
## <a name="authentication-flow"></a>認証フロー
ユーザーは、VPN サーバーの仮想ポートに接続するときに、まず、さまざまなプロトコルを使用して認証する必要があります。これらのプロトコルにより、ユーザー名/パスワードの組み合わせと証明書ベースの認証方法を使用できるようになります。 

認証と ID の確認に加え、ユーザーには適切なダイヤルインのアクセス許可が必要です。 単純な実装では、アクセスを許可するこれらのダイヤルインのアクセス許可は、Active Directory ユーザー オブジェクトで直接設定します。 

 ![ユーザー プロパティ](./media/nps-extension-vpn/image1.png)

単純な実装では、各 VPN サーバーは、各ローカル VPN サーバーで定義されているポリシーに基づいてアクセスを許可または拒否します。

大規模でスケーラブルな実装では、VPN アクセスを許可または拒否するポリシーは、RADIUS サーバーで一元化されます。 この場合、VPN サーバーは、接続要求とアカウント メッセージを RADIUS サーバーに転送するアクセス サーバー (RADIUS クライアント) として機能します。 VPN サーバーの仮想ポートに接続するには、ユーザーは認証を受け、RADIUS サーバーで一元的に定義された条件を満たす必要があります。 

Azure の NPS 拡張機能を NPS と統合した場合、正常な認証フローは次のようになります。

1. VPN サーバーが、リソース (リモート デスクトップ セッションなど) に接続するためのユーザー名とパスワードが含まれた認証要求を VPN ユーザーから受信します。 
2. RADIUS クライアントとして機能する VPN サーバーは、要求を RADIUS Access-Request メッセージに変換し、NPS 拡張機能がインストールされている RADIUS (NPS) サーバーに、(パスワードが暗号化された) メッセージを送信します。 
3. Active Directory でユーザー名とパスワードの組み合わせが検証されます。 ユーザー名/パスワードが正しくない場合、RADIUS サーバーは Access-Reject メッセージを送信します。 
4. NPS 接続要求とネットワーク ポリシーで指定されているすべての条件が満たされていれば (時刻やグループ メンバーシップの制約など)、NPS 拡張機能によって、Azure MFA を使用したセカンダリ認証の要求がトリガーされます。 
5. Azure MFA は Azure Active Directory と通信してユーザーの詳細を取得し、ユーザーが構成した方法 (テキスト メッセージ、モバイル アプリなど) を使用してセカンダリ認証を実行します。 
6. MFA チャレンジが成功すると、Azure MFA は NPS 拡張機能に結果を伝えます。
7. 接続試行が認証され、承認されたら、拡張機能がインストールされている NPS サーバーは、RADIUS Access-Accept メッセージを VPN サーバー (RADIUS クライアント) に送信します。
8. ユーザーは、VPN サーバーの仮想ポートへのアクセスが許可され、暗号化された VPN トンネルを確立します。

## <a name="prerequisites"></a>前提条件
このセクションでは、Azure MFA をリモート デスクトップ ゲートウェイと統合する前に必要な前提条件について詳しく説明します。 作業を開始する前に、次の前提条件を満たしておく必要があります。

* VPN インフラストラクチャ
* ネットワーク ポリシーとアクセス サービス (NPS) の役割
* Azure MFA のライセンス
* Windows Server ソフトウェア
* ライブラリ
* オンプレミスの AD と同期された Azure AD 
* Azure Active Directory の GUID ID

### <a name="vpn-infrastructure"></a>VPN インフラストラクチャ
この記事では、Microsoft Windows Server 2016 を使用する適切な作業用 VPN インフラストラクチャがあり、現時点では VPN サーバーが接続要求を RADIUS サーバーに転送するように構成されていないことを前提としています。 このガイドで、中央の RADIUS サーバーを使用するように VPN インフラストラクチャを構成します。

適切な作業用インフラストラクチャがない場合は、Microsoft やサード パーティのサイトにある多数の VPN セットアップ チュートリアルで提供されるガイダンスに従って、このインフラストラクチャを簡単に作成できます。 

### <a name="network-policy-and-access-services-nps-role"></a>ネットワーク ポリシーとアクセス サービス (NPS) の役割

NPS 役割サービスは、RADIUS サーバーと RADIUS クライアントの機能を提供します。 この記事では、環境内のメンバー サーバーまたはドメイン コントローラーに NPS の役割がインストールされていることを前提としています。 このガイドで、VPN 構成の RADIUS を構成します。 NPS の役割は、VPN サーバー_以外_のサーバーにインストールします。

Windows Server 2012 以降に NPS 役割サービスをインストールする方法については、「[Install a NAP Health Policy Server (NAP 正常性ポリシー サーバーのインストール)](https://technet.microsoft.com/library/dd296890.aspx)」をご覧ください。 Windows Server 2016 では、ネットワーク アクセス保護 (NAP) は使用されていません。 NPS をドメイン コントローラーにインストールする際の推奨事項など、NPS のベスト プラクティスについては、「[Best Practices for NPS (NPS のベスト プラクティス)](https://technet.microsoft.com/library/cc771746)」をご覧ください。

### <a name="licenses"></a>ライセンス

Azure MFA のライセンスが必要です。ライセンスは、Azure AD Premium、Enterprise Mobility + Security (EMS)、または MFA サブスクリプションを通じて入手できます。 詳細については、「[Azure Multi-Factor Authentication の入手方法](multi-factor-authentication-versions-plans.md)」をご覧ください。 テスト目的で。試用版サブスクリプションをご利用いただけます。

### <a name="software"></a>ソフトウェア

NPS 拡張機能を使用するには、NPS 役割サービスがインストールされた Windows Server 2008 R2 SP1 以上が必要です。 このガイドの手順はすべて Windows Server 2016 を使用して実行されました。

### <a name="libraries"></a>ライブラリ

これらのライブラリは拡張機能を含めて自動的にインストールされます。

-   [Visual Studio 2013 (X64) の Visual C++ 再頒布可能パッケージ](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Windows PowerShell 用 Microsoft Azure Active Directory モジュール バージョン 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Windows PowerShell 用 Microsoft Azure Active Directory モジュールは、セットアップ プロセスの一環として実行する構成スクリプトによってインストールされます (モジュールがまだ存在しない場合)。 このモジュールをまだインストールしていない場合、事前にインストールする必要はありません。

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>オンプレミスの Active Directory と同期された Azure Active Directory 

NPS 拡張機能を使用するには、オンプレミスのユーザーを Azure Active Directory と同期し、ユーザーの Multi-Factor Authentication を有効する必要があります。 このガイドでは、AD Connect を使用してオンプレミスのユーザーが Azure Active Directory と同期されていることを前提としています。 ユーザーの MFA を有効にする手順については後述します。
Azure AD Connect については、「[オンプレミスのディレクトリと Azure Active Directory の統合](../active-directory/connect/active-directory-aadconnect.md)」をご覧ください。 

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory の GUID ID 
NPS をインストールするには、Azure Active Directory の GUID が必要です。 Azure Active Directory の GUID を確認する手順については、次のセクションで説明します。

## <a name="configure-radius-for-vpn-connections"></a>VPN 接続用の RADIUS の構成

NPS サーバーの役割がメンバー サーバーにインストールされている場合、VPN 接続を要求する VPN クライアントを認証および承認するように構成する必要があります。 

このセクションでは、ネットワーク ポリシー サーバーの役割はインストールされているが、インフラストラクチャで使用できるように構成されていないことを前提としています。

>[!NOTE]
>一元化された RADIUS サーバーを認証に使用する作業用 VPN サーバーが既にある場合は、このセクションを省略して構いません。
>

### <a name="register-server-in-active-directory"></a>Active Directory にサーバーを登録する
このシナリオで正常に機能させるには、NPS サーバーを Active Directory に登録する必要があります。

1. サーバー マネージャーを開きます。
2. サーバー マネージャーで **[ツール]** をクリックし、**[ネットワーク ポリシー サーバー]** をクリックします。 
3. [ネットワーク ポリシー サーバー] コンソールで、**[NPS (ローカル)]** を右クリックし、**[Active Directory にサーバーを登録]** をクリックします。 **[OK]** を 2 回クリックします。

 ![ネットワーク ポリシー サーバー](./media/nps-extension-vpn/image2.png)

4. 次の手順で使用するため、コンソールは開いたままにしておきます。

### <a name="use-wizard-to-configure-radius-server"></a>ウィザードを使用して RADIUS サーバーを構成する
(ウィザード ベースの) 標準構成オプションまたは詳細構成オプションを使用して、RADIUS サーバーを構成できます。 このセクションでは、ウィザード ベースの標準構成オプションを使用することを想定しています。

1. [ネットワーク ポリシー サーバー] コンソールで、**[NPS (ローカル)]** をクリックします。
2. [標準構成] で **[ダイヤルアップ接続または VPN 接続用の RADIUS サーバー]** を選択し、**[VPN またはダイヤルアップを構成する]** をクリックします。

 ![VPN の構成](./media/nps-extension-vpn/image3.png)

3. [ダイヤルアップまたは仮想プライベート ネットワークの接続の種類の選択] ページで、**[仮想プライベート ネットワーク (VPN) 接続]** を選択し、**[次へ]** をクリックします。

 ![仮想プライベート ネットワーク](./media/nps-extension-vpn/image4.png)

4. [ダイヤルアップまたは VPN サーバーの指定] ページで、**[追加]** をクリックします。
5. **[新しい RADIUS クライアント]** ダイアログ ボックスで、フレンドリ名を指定し、VPN サーバーの解決可能な名前または IP アドレスを入力します。共有シークレットのパスワードも入力します。 この共有シークレットのパスワードは、長い複雑なものにします。 このパスワードは次のセクションの手順で必要になるため、記録しておいてください。

 ![新しい RADIUS クライアント](./media/nps-extension-vpn/image5.png)

6. **[OK]** をクリックし、**[次へ]** をクリックします。
7. **[認証方法の構成]** ページで、既定の選択 ([Microsoft 暗号化認証バージョン 2 (MS-CHAP v2)]) をそのまま使用するか、別のオプションを選択し、**[次へ]** をクリックします。

  >[!NOTE]
  >拡張認証プロトコル (EAP) を構成する場合は、MS CHAP v2 または PEAP を使用する必要があります。 他の EAP はサポートされていません。
 
8. [ユーザー グループの指定] ページで **[追加]** をクリックし、適切なグループを選択します (グループが存在する場合)。 それ以外の場合は、すべてのユーザーにアクセスを許可するために、選択フィールドを空白のままにしておきます。

 ![ユーザー グループの指定](./media/nps-extension-vpn/image7.png)

9. **[次へ]** をクリックします。
10. [IP フィルターの指定] ページで、**[次へ]** をクリックします。
11. [暗号化設定の指定] ページで既定の設定をそのまま使用し、**[次へ]** をクリックします。

 ![暗号化の指定](./media/nps-extension-vpn/image8.png)

12. [領域名の指定] では、名前を空白のままにし、既定の設定をそのまま使用して、**[次へ]** をクリックします。

 ![領域名の指定](./media/nps-extension-vpn/image9.png)

13. [新しいダイヤルアップ接続または仮想プライベート ネットワーク接続および RADIUS クライアントの完了] ページで、**[完了]** をクリックします。

 ![接続の完了](./media/nps-extension-vpn/image10.png)

### <a name="verify-radius-configuration"></a>RADIUS 構成を確認する
このセクションでは、ウィザードを使用して作成した構成について詳しく説明します。

1. NPS サーバーで、[NPS (ローカル)] コンソールの [RADIUS クライアント] を展開し、**[RADIUS クライアント]** を選択します。
2. 詳細ウィンドウで、ウィザードを使用して作成した RADIUS クライアントを右クリックし、**[プロパティ]** をクリックします。 RADIUS クライアント (VPN サーバー) のプロパティが次のように表示されます。

 ![VPN のプロパティ](./media/nps-extension-vpn/image11.png)

3. **[キャンセル]** をクリックします。
4. NPS サーバーで、[NPS (ローカル)] コンソールの **[ポリシー]** を展開し、**[接続要求ポリシー]** を選択します。 次の画像のような VPN 接続ポリシーが表示されます。

 ![接続要求](./media/nps-extension-vpn/image12.png)

5. [ポリシー] の **[ネットワーク ポリシー]** を選択します。 次の画像のような仮想プライベート ネットワーク (VPN) 接続ポリシーが表示されます。

 ![ネットワークのプロパティ](./media/nps-extension-vpn/image13.png)

## <a name="configure-vpn-server-to-use-radius-authentication"></a>RADIUS 認証を使用するための VPN サーバーの構成
このセクションでは、RADIUS 認証を使用するように VPN サーバーを構成します。 このセクションでは、VPN サーバーの動作中の構成はあるが、RADIUS 認証を使用するように VPN サーバーが構成されていないことを前提としています。 VPN サーバーの構成後、構成が予想どおりに動作していることを確認します。

>[!NOTE]
>RADIUS 認証を使用する動作中の VPN サーバー構成が既にある場合は、このセクションを省略して構いません。
>

### <a name="configure-authentication-provider"></a>認証プロバイダーを構成する
1. VPN サーバーで、サーバー マネージャーを開きます。
2. サーバー マネージャーで **[ツール]** をクリックし、**[ルーティングとリモート アクセス]** をクリックします。
3. [ルーティングとリモート アクセス] コンソールで、**\[サーバー名\] (ローカル)** を右クリックし、**[プロパティ]** をクリックします。

 ![ルーティングとリモート アクセス](./media/nps-extension-vpn/image14.png)
 
4. **[{サーバー名} (ローカル) のプロパティ]** ダイアログ ボックスで、**[セキュリティ]** タブをクリックします。 
5. **[セキュリティ]** タブで、[認証プロバイダー] の **[RADIUS 認証]** をクリックし、**[構成]** をクリックします。

 ![RADIUS 認証](./media/nps-extension-vpn/image15.png)
 
6. [RADIUS 認証] ダイアログ ボックスで、**[追加]** をクリックします。
7. [RADIUS サーバーの追加] で、[サーバー名] に、前のセクションで構成した RADIUS サーバーの名前または IP アドレスを追加します。
8. [共有シークレット] で **[変更]** をクリックし、先ほど作成して記録しておいた共有シークレットのパスワードを追加します。
9. [タイムアウト (秒)] で、値を **30** ～ **60** に変更します。 これは、2 つ目の認証要素を完了するための十分な時間を確保するために必要となります。
 
 ![RADIUS サーバーの追加](./media/nps-extension-vpn/image16.png)
 
10. すべてのダイアログ ボックスを閉じるまで **[OK]** をクリックします。

### <a name="test-vpn-connectivity"></a>VPN 接続をテストする
このセクションでは、VPN 仮想ポートに接続しようとしたときに、VPN クライアントが RADIUS サーバーによって認証され、承認されることを確認します。 このセクションは、VPN クライアントとして Windows 10 を使用していることを前提としています。 

>[!NOTE]
>VPN サーバーに接続するように VPN クライアントを既に構成しており、設定を保存済みの場合は、VPN 接続オブジェクトの構成と保存に関連する手順は省略して構いません。
>

1. VPN クライアント コンピューターで、**[スタート]** ボタンをクリックし、**[設定]** (歯車アイコン) をクリックします。
2. [Windows の設定] で、**[ネットワークとインターネット]** をクリックします。
3. **[VPN]** をクリックします。
4. **[VPN 接続を追加する]** をクリックします。
5. [VPN 接続を追加する] で、[VPN プロバイダー] として [Windows (ビルトイン)] を指定し、必要に応じて残りのフィールドを設定して、**[保存]** をクリックします。 

 ![VPN 接続の追加](./media/nps-extension-vpn/image17.png)
 
6. コントロール パネルの **[ネットワークと共有センター]** を開きます。
7. **[アダプターの設定の変更]** をクリックします。

 ![アダプターの設定の変更](./media/nps-extension-vpn/image18.png)

8. VPN ネットワーク接続を右クリックし、[プロパティ] をクリックします。 

 ![VPN ネットワークのプロパティ](./media/nps-extension-vpn/image19.png)

9. VPN のプロパティ ダイアログ ボックスで、**[セキュリティ]** タブをクリックします。 
10. [セキュリティ] タブで、**[Microsoft CHAP Version 2 (MS-CHAP v2)]** だけがオンになっていることを確認し、[OK] をクリックします。

 ![プロトコルの許可](./media/nps-extension-vpn/image20.png)

11. VPN 接続を右クリックし、**[接続]** をクリックします。
12. [設定] ページで **[接続]** をクリックします。

成功した接続は、次に示すように、RADIUS サーバーのセキュリティ ログにイベント ID 6272 として表示されます。

 ![イベントのプロパティ](./media/nps-extension-vpn/image21.png)

## <a name="troubleshoot-guide"></a>トラブルシューティング ガイド
認証と承認に一元化された RADIUS サーバーを使用するように VPN サーバーを構成するまでは VPN 構成が動作していたとします。 この場合、RADIUS サーバーの構成に誤りがあるか、無効なユーザー名またはパスワードを使用したことが原因で問題が発生している可能性があります。 たとえば、ユーザー名に代替 UPN サフィックスを使用すると、ログイン試行が失敗することがあります (最良の結果を得るには、同じアカウント名を使用する必要があります)。 

これらの問題のトラブルシューティングを行うときは、まず、RADIUS サーバーのセキュリティ イベント ログを調べることをお勧めします。 イベントの検索時間を節約するには、次に示すように、イベント ビューアーのロールベースの [ネットワーク ポリシーとアクセス サービス] カスタム ビューを使用します。 イベント ID 6273 は、ネットワーク ポリシー サーバーがユーザーに対してアクセスを拒否したイベントを示しています。 

 ![イベント ビューアー](./media/nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Multi-Factor Authentication の構成
このセクションでは、ユーザーの MFA を有効にし、アカウントに 2 段階認証を設定する手順について説明します。 

### <a name="enable-multi-factor-authentication"></a>Multi-Factor Authentication を有効にする
ここでは、Azure AD アカウントの MFA を有効にします。 **クラシック ポータル**を使用して、ユーザーの MFA を有効にします。 

1. ブラウザーを開き、[https://manage.windowsazure.com](https://manage.windowsazure.com) に移動します。 
2. 管理者としてログオンします。
3. ポータルの左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

 ![既定のディレクトリ](./media/nps-extension-vpn/image23.png)

4. [名前] 列で、**[既定のディレクトリ]** (または、該当する場合は別のディレクトリ) をクリックします。
5. [クイック スタート] ページで、**[構成]** をクリックします。

 ![既定のディレクトリの構成](./media/nps-extension-vpn/image24.png)

6. [構成] ページで [多要素認証] セクションまで下へスクロールし、**[サービス設定の管理]** をクリックします。

 ![MFA 設定の管理](./media/nps-extension-vpn/image25.png)
 
7. [多要素認証] ページで既定のサービス設定を確認し、**[ユーザー]** をクリックします。 

 ![MFA ユーザー](./media/nps-extension-vpn/image26.png)
 
8. [ユーザー] ページで、MFA を有効するユーザーを選択し、**[有効化]** をクリックします。

 ![プロパティ](./media/nps-extension-vpn/image27.png)
 
9. メッセージが表示されたら、**[Multi-Factor Auth を有効にする]** をクリックします。

 ![MFA の有効化](./media/nps-extension-vpn/image28.png)
 
10. **[閉じる]**をクリックします。 
11. ページを更新します。 MFA の状態が [有効] に変更されます。

ユーザーの Multi-Factor Authentication を有効にする方法については、[クラウドでの Azure Multi-Factor Authentication の概要](multi-factor-authentication-get-started-cloud.md)に関する記事をご覧ください。 

### <a name="configure-accounts-for-two-step-verification"></a>2 段階認証用にアカウントを構成する
アカウントの MFA を有効にすると、ユーザーは 2 段階認証を使用する 2 つ目の認証要素に使用する信頼済みデバイスの構成を正常に完了するまで、MFA ポリシーによって管理されたリソースにはサインインできません。

このセクションでは、2 段階認証で使用する信頼済みデバイスを構成します。 構成で使用できるオプションは次のとおりです。

* **モバイル アプリ**:  Windows Phone、Android、または iOS デバイスに Microsoft Authenticator アプリをインストールします。 組織のポリシーに応じて、2 つのモードのいずれかでこのアプリを使用する必要があります。[確認のため通知を受け取る] では、デバイスに通知がプッシュされます。[確認コードを使用する] では、30 秒ごとに更新される確認コードを入力する必要があります。 
* **携帯電話呼び出しまたはテキスト**:  自動音声通話を使用するか、テキスト メッセージを受信できます。 通話オプションでは、呼び出しに応答し、# 記号を押して認証を行います。 テキスト オプションでは、テキスト メッセージに返信するか、サインイン インターフェイスに確認コードを入力します。
* **会社電話呼び出し**:  このプロセスは、上記の自動音声通話の場合と同じです。

モバイル アプリを使用して確認のためのプッシュ通知を受け取るようにデバイスを設定するには、次の手順に従います。

1. [https://aka.ms/mfasetup](https://aka.ms/mfasetup) にログオンするか、MFA 対応の資格情報を使用して認証する必要がある任意のサイト ([https://portal.azure.com](https://portal.azure.com) など) にログオンします。 
2. ユーザー名とパスワードを使用してサインインすると、アカウントに追加のセキュリティ確認を設定するよう求める画面が表示されます。

 ![追加のセキュリティ](./media/nps-extension-vpn/image29.png)

3. **[今すぐセットアップ]** をクリックします。
4. [追加のセキュリティ確認] ページで、連絡先の種類 (認証用電話、会社電話、またはモバイル アプリ) を選択します。 次に、国または地域を選択し、方法を選択します。 方法は選択した連絡先の種類によって異なります。 たとえば、モバイル アプリを選択した場合は、確認のための通知を受け取るか、確認コードを使用するかを選択できます。 以下の手順では、連絡先の種類として **[モバイル アプリ]** を選択するものとします。

 ![電話認証](./media/nps-extension-vpn/image30.png)

5. [モバイル アプリ] を選択し、**[確認のため通知を受け取る]** をクリックして、**[セットアップ]** をクリックします。 

 ![モバイル アプリによる確認](./media/nps-extension-vpn/image31.png)
 
6. Authenticator モバイル アプリをデバイスにインストールします (まだインストールしていない場合)。 
7. モバイル アプリの指示に従って、表示されたバー コードをスキャンするか、情報を手動で入力し、**[完了]** をクリックします。

 ![モバイル アプリの構成](./media/nps-extension-vpn/image32.png)

8. [追加のセキュリティ確認] ページで、**[連絡してください]** をクリックし、デバイスに送信された通知に返信します。
9. [追加のセキュリティ確認] ページで、モバイル アプリにアクセスできなくなった場合の連絡先の電話番号を入力し、**[次へ]** をクリックします。

 ![携帯電話番号](./media/nps-extension-vpn/image33.png)
 
10. [追加のセキュリティ確認] で、**[完了]** をクリックします。

これで、2 つ目の確認方法を提供するようにデバイスが構成されました。 アカウントに 2 段階認証を設定する方法については、「[アカウントへの 2 段階認証の設定](./end-user/multi-factor-authentication-end-user-first-time.md)」をご覧ください。

## <a name="install-and-configure-nps-extension"></a>NPS 拡張機能のインストールと構成

このセクションでは、VPN サーバーでのクライアント認証に Azure MFA を使用するように VPN を構成する手順について説明します。

NPS 拡張機能をインストールして構成したら、このサーバーによって処理される RADIUS ベースのすべてのクライアント認証に Azure MFA を使用する必要があります。 すべての VPN ユーザーが Azure MFA に登録されているわけではない場合は、MFA を使用するように構成されていないユーザーを認証するために、別の RADIUS サーバーを設定できます。 また、チャレンジされたユーザーが 2 つ目の認証要素を提供できるようにするレジストリ エントリを作成することもできます (ユーザーが MFA に登録されている場合のみ)。 

_HKLM\SOFTWARE\Microsoft\AzureMfa に REQUIRE_USER_MATCH_ という名前の新しい文字列値を作成し、値を TRUE または FALSE に設定します。 

 ![ユーザー照合が必要](./media/nps-extension-vpn/image34.png)
 
値が TRUE に設定されているか、値が設定されていない場合は、すべての認証要求が MFA チャレンジの対象となります。 値が FALSE に設定されている場合は、MFA に登録されているユーザーにのみ、MFA チャレンジが発行されます。 FALSE の設定は、オンボード期間中にテスト環境または運用環境でのみ使用します。

### <a name="acquire-azure-active-directory-guid-id"></a>Azure Active Directory の GUID ID を取得する

NPS 拡張機能の構成の一環として、管理者資格情報と Azure AD テナントの Azure Active Directory ID を入力する必要があります。 次の手順は、テナント ID を取得する方法を示しています。

1. Azure テナントのグローバル管理者として Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。
2. 左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。
3. **[プロパティ]**をクリックします。
4. **[コピー]** アイコンをクリックして、ディレクトリ ID をクリップボードにコピーします。
 
 ![ディレクトリ ID](./media/nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>NPS 拡張機能のインストール
ネットワーク ポリシーとアクセス サービス (NPS) の役割がインストールされ、設計で RADIUS サーバーとして機能するサーバーに、NPS 拡張機能をインストールする必要があります。 リモート デスクトップ サーバーには NPS 拡張機能をインストールしないでください。

1. [https://aka.ms/npsmfa](https://aka.ms/npsmfa) から NPS 拡張機能をダウンロードします。 
2. セットアップ実行可能ファイル (NpsExtnForAzureMfaInstaller.exe) を NPS サーバーにコピーします。
3. NPS サーバーで **NpsExtnForAzureMfaInstaller.exe** をダブルクリックします。 メッセージが表示されたら、**[実行]** をクリックします。
4. [NPS Extension for Azure MFA]\(Azure MFA の NPS 拡張機能\) ダイアログ ボックスで、ソフトウェア ライセンス条項を確認し、**[同意する]** チェック ボックスをオンにして、**[インストール]** をクリックします。

 ![NPS 拡張機能](./media/nps-extension-vpn/image36.png)
 
5. [NPS Extension for Azure MFA]\(Azure MFA の NPS 拡張機能\) ダイアログ ボックスで、**[閉じる]** をクリックします。  

 ![セットアップ完了](./media/nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>PowerShell スクリプトを使用して NPS 拡張機能で使用する証明書を構成する
セキュリティで保護された通信と保証を確保するには、NPS 拡張機能で使用する証明書を構成する必要があります。 NPS コンポーネントには、NPS で使用する自己署名証明書を構成する Windows PowerShell スクリプトが含まれています。 

このスクリプトは、次のアクションを実行します。

* 自己署名証明書を作成する
* Azure AD のサービス プリンシパルに証明書の公開キーを関連付ける
* ローカル コンピューターのストアに証明書を格納する
* ネットワーク ユーザーに証明書の秘密キーへのアクセスを許可する
* ネットワーク ポリシー サーバー サービスを再起動する

独自の証明書を使用する場合は、Azure AD のサービス プリンシパルへの証明書の公開キーの関連付けなどを行う必要があります。
スクリプトを使用するには、Azure Active Directory の管理者資格情報と、先ほどコピーした Azure Active Directory のテナント ID を拡張機能に提供します。 NPS 拡張機能がインストールされている各 NPS サーバーでスクリプトを実行します。

1. 管理用の Windows PowerShell プロンプトを開きます。
2. PowerShell プロンプトで、「_cd ‘c:\Program Files\Microsoft\AzureMfa\Config’_」と入力し、**Enter** キーを押します。
3. 「_.\AzureMfsNpsExtnConfigSetup.ps1_」と入力し、**Enter** キーを押します。 
 * Azure Active Directory PowerShell モジュールがインストールされているかどうかがチェックされます。 インストールされていない場合は、スクリプトによってモジュールがインストールされます。
 
 ![PowerShell](./media/nps-extension-vpn/image38.png)
 
4. PowerShell モジュールのインストールの確認後、Azure Active Directory PowerShell モジュールのダイアログ ボックスが表示されます。 ダイアログ ボックスで、Azure AD の管理者資格情報とパスワードを入力し、**[サインイン]** をクリックします。 
 
 ![PowerShell のサインイン](./media/nps-extension-vpn/image39.png)
 
5. メッセージが表示されたら、先ほどクリップボードにコピーしたテナント ID を貼り付け、**Enter** キーを押します。 

 ![テナント ID](./media/nps-extension-vpn/image40.png)

6. スクリプトによって自己署名証明書が作成され、他の構成変更が実行されます。 出力は次の画像のようになります。

 ![自己署名証明書](./media/nps-extension-vpn/image41.png)

7. サーバーを再起動します。
 
### <a name="verify-configuration"></a>構成の確認
構成を確認するには、VPN サーバーとの新しい VPN 接続を確立する必要があります。 プライマリ認証用の資格情報の入力が正常に完了すると、次に示すように、セカンダリ認証が成功するまで待ってから VPN 接続が確立されます。 

 ![構成の確認](./media/nps-extension-vpn/image42.png)

Azure MFA で以前に構成したセカンダリ検証方法で認証に成功すると、リソースに接続されます。 ただし、セカンダリ認証に失敗した場合、リソースへのアクセスは拒否されます。 

次の例では、Windows Phone の Authenticator アプリを使用してセカンダリ認証を提供しています。

 ![アカウントの確認](./media/nps-extension-vpn/image43.png)

第 2 の方法を使用して正常に認証されると、VPN サーバーの仮想ポートへのアクセスが許可されます。 ただし、信頼済みデバイスでモバイル アプリを使用したセカンダリ認証方法を使用する必要があったため、ユーザー名とパスワードの組み合わせだけを使用する場合よりもログイン プロセスの安全性が高まります。

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>成功したログオン イベントのイベント ビューアーのログを表示する
Windows イベント ビューアーのログで成功したログオン イベントを表示するには、次の Windows PowerShell コマンドを発行して、NPS サーバーの Windows セキュリティ ログを照会します。

イベント ビューアーのセキュリティ ログの成功したログオン イベントを照会するには、次のコマンドを使用します。
* _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

 ![セキュリティ イベント ビューアー](./media/nps-extension-vpn/image44.png)
 
次に示すように、セキュリティ ログや [ネットワーク ポリシーとアクセス サービス] カスタム ビューを表示することもできます。

 ![ネットワーク ポリシーへのアクセス](./media/nps-extension-vpn/image45.png)

Azure MFA の NPS 拡張機能がインストールされているサーバーで、拡張機能に固有のイベント ビューアーのアプリケーション ログ (**アプリケーションとサービス ログ\Microsoft\AzureMfa**) を確認できます。 

* _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

 ![イベント数](./media/nps-extension-vpn/image46.png)

## <a name="troubleshoot-guide"></a>トラブルシューティング ガイド
構成が予想どおりに動作していない場合、トラブルシューティングでは、まず、Azure MFA を使用するようにユーザーが構成されていることを確認します。 ユーザーに、[https://portal.azure.com](https://portal.azure.com) に接続してもらいます。ユーザーがセカンダリ認証を求められ、正常に認証できれば、Azure MFA の構成には問題がないことがわかります。

ユーザーの Azure MFA が機能している場合、関連するイベント ログを確認する必要があります。 関連するイベント ログとして、前のセクションで説明したセキュリティ イベント ログ、ゲートウェイの操作ログ、Azure MFA ログがあります。 

失敗したログオン イベント (イベント ID 6273) を示すセキュリティ ログの出力例を次に示します。

 ![セキュリティ ログ](./media/nps-extension-vpn/image47.png)

Azure MFA ログの関連イベントを次に示します。

 ![Azure MFA ログ](./media/nps-extension-vpn/image48.png)

高度なトラブルシューティング オプションを実行するには、NPS サービスがインストールされているサーバーで NPS データベース形式のログ ファイルを参照します。 これらのログ ファイルは、コンマ区切りのテキスト ファイルとして _%SystemRoot%\System32\Logs_ フォルダーに作成されています。 これらのログ ファイルについては、「[Interpret NPS Database Format Log Files (NPS データベース形式のログ ファイルの解釈)](https://technet.microsoft.com/library/cc771748.aspx)」をご覧ください。 

これらのログ ファイルのエントリは、スプレッドシートやデータベースにインポートしないと解釈が困難です。 ログ ファイルの解釈に役立つ多数の IAS パーサーがオンラインで見つかります。 このようなダウンロード可能な[シェアウェア アプリケーション](http://www.deepsoftware.com/iasviewer)の出力を次に示します。 

 ![シェアウェア アプリケーション](./media/nps-extension-vpn/image49.png)

最後に、その他のトラブルシューティング オプションとして、Wireshark や [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx) などのプロトコル アナライザーを使用できます。 Wireshark の次の画像は、VPN サーバーと NPS サーバー間の RADIUS メッセージを示しています。

 ![Microsoft Message Analyzer](./media/nps-extension-vpn/image50.png)

詳細については、「[Azure Multi-Factor Authentication と既存の NPS インフラストラクチャの統合](multi-factor-authentication-nps-extension.md)」をご覧ください。  

## <a name="next-steps"></a>次のステップ
[Azure Multi-Factor Authentication の入手方法](multi-factor-authentication-versions-plans.md)

[RADIUS を使用したリモート デスクトップ ゲートウェイと Multi-Factor Authentication Server](multi-factor-authentication-get-started-server-rdg.md)

[オンプレミスのディレクトリと Azure Active Directory の統合](../active-directory/connect/active-directory-aadconnect.md)

