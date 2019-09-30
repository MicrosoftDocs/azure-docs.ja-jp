---
title: マネージド ドメイン用のハイブリッド Azure Active Directory 参加の構成 | Microsoft Docs
description: マネージド ドメイン用のハイブリッド Azure Active Directory 参加を構成する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 787900918035dc8b14d3a173496ab1a23b0f93bb
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68813095"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>チュートリアル:マネージド ドメイン用のハイブリッド Azure Active Directory 参加の構成

組織内のユーザーと同様、デバイスは保護対象となる主要なアイデンティティです。 デバイスの ID を使用して、いつでもどこからでもリソースを保護できます。 この目標は、次のいずれかの方法を使用して、Azure Active Directory (Azure AD) にデバイス ID を取り込み、管理することで達成できます。

- Azure AD 参加
- ハイブリッド Azure AD 参加
- Azure AD の登録

Azure AD に自分のデバイスを取り込んで、クラウドとオンプレミスのリソースでのシングル サインオン (SSO) を実現することで、ユーザーの生産性を最大化できます。 同時に、[条件付きアクセス](../active-directory-conditional-access-azure-portal.md)を使用して、クラウドとオンプレミスのリソースへのアクセスを保護できます。

このチュートリアルでは、Active Directory ドメイン参加済みコンピューター デバイスのハイブリッド Azure AD 参加をマネージド環境で構成する方法について説明します。 

マネージド環境は、[パスワード ハッシュ同期 (PHS)](../hybrid/whatis-phs.md) または[パススルー認証 (PTA)](../hybrid/how-to-connect-pta.md) の[シームレス シングル サインオン](../hybrid/how-to-connect-sso.md)を使用してデプロイできます。 これらのシナリオでは、フェデレーション サーバーを認証用に構成する必要はありません。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ハイブリッド Azure AD 参加の構成
> * ダウンレベルの Windows デバイスの有効化
> * 参加済みデバイスの確認
> * トラブルシューティング

## <a name="prerequisites"></a>前提条件

このチュートリアルは、次の記事を理解していることを前提とします。

- [デバイス ID とは](overview.md)
- [ハイブリッド Azure AD 参加の実装を計画する方法](hybrid-azuread-join-plan.md)
- [Hybrid Azure AD Join の制御された検証を実行する方法](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD は、マネージド ドメインでのスマートカードや証明書をサポートしていません。

この記事のシナリオを構成するには、[Azure AD Connect の最新バージョン](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 以降) がインストールされている必要があります。

Azure AD Connect で、ハイブリッド Azure AD 参加済みにするデバイスのコンピュータ オブジェクトを Azure AD に対して同期済みであることを確認します。 コンピューター オブジェクトが特定の組織単位 (OU) に属している場合、これらの OU も、Azure AD Connect で同期するよう構成する必要があります。 Azure AD Connect を使用してコンピューター オブジェクトを同期する方法の詳細については、[Azure AD Connect を使用したフィルタリングの構成](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)に関する記事を参照してください。

バージョン 1.1.819.0 以降の Azure AD Connect には、ハイブリッド Azure AD 参加を構成するためのウィザードが用意されています。 このウィザードを使用すると、構成プロセスを大幅に簡略化できます。 このウィザードで、デバイス登録のためのサービス接続ポイント (SCP) を構成します。

この記事の構成手順は、Azure AD Connect でこのウィザードを使用することに基づいています。

ハイブリッド Azure AD 参加を使用するには、デバイスが組織のネットワーク内から次の Microsoft リソースにアクセスできる必要があります。  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (シームレス SSO を使用しているか、使用する予定の場合)

送信プロキシを介してインターネットにアクセスする必要がある組織の場合、Microsoft では、Windows 10 コンピューターを Azure AD にデバイス登録できるように [Web プロキシ自動発見 (WPAD) を実装](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10))することを推奨しています。 WPAD の構成と管理で問題が発生した場合、[自動検出のトラブルシューティング](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10))に関する記事を参照してください。 

WPAD を使用せず、コンピューターでプロキシ設定を構成する必要がある場合、Windows 10 1709 以降、これを実行できます。 詳細については、[グループ ポリシー オブジェクト (GPO) を使用した WinHTTP 設定の構成](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)に関するページを参照してください。

> [!NOTE]
> WinHTTP 設定を使用してコンピューターでプロキシ設定を構成すると、構成したプロキシに接続できないコンピューターでインターネットへの接続が失敗します。

組織が認証された送信プロキシ経由でのインターネットへのアクセスを必要とする場合、お使いの Windows 10 コンピューターが送信プロキシに対して正常に認証されることを確認する必要があります。 Windows 10 コンピューターではマシン コンテキストを使用してデバイス登録が実行されるため、マシン コンテキストを使用して送信プロキシ認証を構成する必要があります。 構成要件については、送信プロキシ プロバイダーに確認してください。

デバイスがシステム アカウントで上記の Microsoft リソースにアクセスできるかどうかを確認するには、「[デバイス登録接続のテスト](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)」スクリプトを使用できます。

## <a name="configure-hybrid-azure-ad-join"></a>ハイブリッド Azure AD 参加の構成

Azure AD Connect を使用してハイブリッド Azure AD 参加を構成するには、次のものが必要です。

- Azure AD テナントの全体管理者の資格情報
- 各フォレストのエンタープライズ管理者の資格情報

**Azure AD Connect を使用してハイブリッド Azure AD 参加を構成するには、次の手順に従います。**

1. Azure AD Connect を起動し、 **[構成]** を選択します。

   ![ようこそ](./media/hybrid-azuread-join-managed-domains/11.png)

1. **[追加のタスク]** ページで、 **[デバイス オプションの構成]** を選択し、 **[次へ]** を選択します。

   ![追加のタスク](./media/hybrid-azuread-join-managed-domains/12.png)

1. **[概要]** ページで **[次へ]** を選択します。

   ![概要](./media/hybrid-azuread-join-managed-domains/13.png)

1. **[Azure AD に接続]** ページで、Azure AD テナントの全体管理者の資格情報を入力します。  

   ![Azure への接続](./media/hybrid-azuread-join-managed-domains/14.png)

1. **[デバイス オプション]** ページで、 **[ハイブリッド Azure AD 参加の構成]** を選択し、 **[次へ]** を選択します。

   ![デバイス オプション](./media/hybrid-azuread-join-managed-domains/15.png)

1. **[SCP]** ページで、Azure AD Connect で SCP を構成するフォレストごとに次の手順を実行し、 **[次へ]** を選択します。

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. フォレストを選択します。
   1. 認証サービスを選択します。
   1. **[追加]** を選択して、エンタープライズ管理者の資格情報を入力します。

1. **[デバイスのオペレーティング システム]** ページで、Active Directory 環境内のデバイスで使用されているオペレーティング システムを選択し、 **[次へ]** を選択します。

   ![デバイスのオペレーティング システム](./media/hybrid-azuread-join-managed-domains/17.png)

1. **[構成の準備完了]** ページで、 **[構成]** を選択します。

   ![構成の準備完了](./media/hybrid-azuread-join-managed-domains/19.png)

1. **[構成が完了しました]** ページで、 **[終了]** を選択します。

   ![構成の完了](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>ダウンレベルの Windows デバイスの有効化

ドメイン参加済みデバイスの一部がダウンレベルの Windows デバイスである場合は、以下の操作が必要です。

- デバイスの登録用のローカル イントラネット設定の構成
- シームレス SSO の構成
- ダウンレベルの Windows コンピューターへの Microsoft Workplace Join のインストール

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>デバイスの登録用のローカル イントラネット設定の構成

ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加を正常に完了するため、およびデバイスが Azure AD で認証を受けるときに証明書の指定を求めるメッセージが表示されないようにするために、対象のドメイン参加済みデバイスにポリシーをプッシュして、以下の URL を Internet Explorer のローカル イントラネット ゾーンに追加することができます。

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

また、ユーザーのローカル イントラネット ゾーンで **[スクリプトを介したステータス バーの更新を許可する]** を有効にする必要があります。

### <a name="configure-seamless-sso"></a>シームレス SSO の構成

Azure AD クラウド認証方法として [PHS](../hybrid/whatis-phs.md) または [PTA](../hybrid/how-to-connect-pta.md) を使用するマネージド ドメインで、ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加を正常に完了するには、[シームレス SSO を構成](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)する必要もあります。

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>ダウンレベルの Windows コンピューターへの Microsoft Workplace Join のインストール

ダウンレベルの Windows デバイスを登録するには、組織で [Windows 10 以外のコンピューター向けの Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554) をインストールする必要があります。 Windows 10 以外のコンピューター向けの Microsoft Workplace Join は、Microsoft ダウンロード センターで入手できます。

 [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) などのソフトウェア ディストリビューション システムを使用して、このパッケージをデプロイできます。 このパッケージは、`quiet` パラメーターを使用した標準のサイレント インストール オプションをサポートしています。 Configuration Manager の Current Branch には、完了した登録を追跡する機能など、以前のバージョンにはない利点が追加されています。

インストーラーによって、ユーザー コンテキストで実行されるシステムにスケジュール済みタスクが作成されます。 このタスクは、ユーザーが Windows にサインインするとトリガーされます。 このタスクでは、デバイスは Azure AD で認証が行われた後、そのユーザー資格情報を使用してサイレントに Azure AD に参加します。

## <a name="verify-the-registration"></a>登録の確認

Azure テナントでのデバイス登録状態を確認するために、[Azure Active Directory PowerShell モジュール](/powershell/azure/install-msonlinev1?view=azureadps-2.0)の **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** コマンドレットを使用できます。

**Get-MSolDevice** コマンドレットを使用してサービスの詳細を確認する場合:

- Windows クライアントの ID と一致する**デバイス ID** を備えたオブジェクトが存在する必要があります。
- **DeviceTrustType** の値は **[ドメイン参加済み]** でなければなりません。 この設定は、Azure AD ポータルの **[デバイス]** ページの **[ハイブリッド Azure AD 参加済み]** 状態に相当します。
- 条件付きアクセスで使用されるデバイスの場合、**Enabled** の値は **True**、**DeviceTrustLevel** の値は **Managed** でなければなりません。

**サービスの詳細を確認するには**:

1. Windows PowerShell を管理者として開きます。
1. 「`Connect-MsolService`」と入力して Azure テナントに接続します。  
1. 「 `get-msoldevice -deviceId <deviceId>` 」を入力します。
1. **[有効]** が **[True]** に設定されていることを確認します。

## <a name="troubleshoot-your-implementation"></a>実装のトラブルシューティング

ドメイン参加済み Windows デバイスのハイブリッド Azure AD 参加を行うときに問題が発生した場合は、以下を参照してください。

- [最新の Windows デバイスのハイブリッド Azure AD 参加のトラブルシューティング](troubleshoot-hybrid-join-windows-current.md)
- [ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加のトラブルシューティング](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>次の手順

[Azure portal を使用してデバイス ID を管理する](device-management-azure-portal.md)方法を学習します。
