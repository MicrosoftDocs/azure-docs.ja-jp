---
title: フェデレーション ドメイン用のハイブリッド Azure Active Directory 参加の構成 | Microsoft Docs
description: フェデレーション ドメイン用のハイブリッド Azure Active Directory 参加を構成する方法について説明します。
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
ms.openlocfilehash: 738b4f47054081f0fb1b1a530bdf21cbf07a7726
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204697"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>チュートリアル:フェデレーション ドメイン用のハイブリッド Azure Active Directory 参加の構成

組織内のユーザーと同様、デバイスは保護対象となる主要な ID です。 デバイスの ID を使用して、いつでもどこからでもリソースを保護できます。 この目標は、次のいずれかの方法を使用して、Azure Active Directory (Azure AD) にデバイス ID を取り込み、管理することで達成できます。

- Azure AD 参加
- ハイブリッド Azure AD 参加
- Azure AD の登録

Azure AD に自分のデバイスを取り込んで、クラウドとオンプレミスのリソースでのシングル サインオン (SSO) を実現することで、ユーザーの生産性を最大化できます。 同時に、[条件付きアクセス](../active-directory-conditional-access-azure-portal.md)を使用して、クラウドとオンプレミスのリソースへのアクセスを保護できます。

このチュートリアルでは、Active Directory フェデレーション サービス (AD FS) を使用して、フェデレーション環境で Active Directory ドメイン参加済みコンピューター デバイスの Hybrid Azure AD Join を構成する方法について説明します。

> [!NOTE]
> フェデレーション環境で AD FS 以外の ID プロバイダーを使用する場合、その ID プロバイダーで WS-Trust プロトコルがサポートされていることを確認する必要があります。 WS-Trust は、現在のハイブリッド Azure AD 参加済み Windows デバイスを Azure AD で認証するために必要です。 ハイブリッド Azure AD 参加が必要なダウンレベルの Windows デバイスがある場合、ID プロバイダーで WIAORMULTIAUTHN 要求がサポートされている必要があります。 

学習内容は次のとおりです。

> [!div class="checklist"]
> * ハイブリッド Azure AD 参加の構成
> * ダウンレベルの Windows デバイスの有効化
> * 登録の確認
> * トラブルシューティング

## <a name="prerequisites"></a>前提条件

このチュートリアルは、次の記事を理解していることを前提としています。

- [デバイス ID とは](overview.md)
- [ハイブリッド Azure AD 参加の実装を計画する方法](hybrid-azuread-join-plan.md)
- [Hybrid Azure AD Join の制御された検証を実行する方法](hybrid-azuread-join-control.md)

このチュートリアルのシナリオを構成するための要件を次に示します。

- Windows Server 2012 R2 と AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) バージョン 1.1.819.0 以降

バージョン 1.1.819.0 以降の Azure AD Connect には、ハイブリッド Azure AD 参加を構成するためのウィザードが用意されています。 このウィザードを使用すると、構成プロセスを大幅に簡略化できます。 関連するウィザードを使用すると、次の操作を実行できます。

- デバイス登録のためのサービス接続ポイント (SCP) を構成する
- Azure AD 証明書利用者の信頼をバックアップする
- Azure AD 信頼のクレーム規則を更新する

この記事の構成手順は、Azure AD Connect のウィザードの使用に基づいています。 以前のバージョンの Azure AD Connect がインストールされている場合は、このウィザードを使用するために 1.1.819 以降にアップグレードする必要があります。 Azure AD Connect の最新バージョンをインストールすることができない場合は、[ハイブリッド Azure AD 参加を手動で構成する方法](hybrid-azuread-join-manual.md)に関するページを参照してください。

ハイブリッド Azure AD 参加では、デバイスが組織のネットワーク内から次の Microsoft リソースにアクセスできる必要があります。  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- 組織のセキュリティ トークン サービス (STS) (フェデレーション ドメインの場合)
- `https://autologon.microsoftazuread-sso.com` (シームレス SSO を使用しているか、使用する予定の場合)

Windows 10 1803 以降で、AD FS を使用したフェデレーション環境の即時的なハイブリッド Azure AD 参加が失敗した場合は、Azure AD Connect を利用して Azure AD のコンピューター オブジェクトを同期させます。これは後で、ハイブリッド Azure AD 参加のデバイス登録を完了するために使用されます。 Azure AD Connect で、ハイブリッド Azure AD 参加済みにするデバイスのコンピュータ オブジェクトを Azure AD に対して同期済みであることを確認します。 コンピューター オブジェクトが特定の組織単位 (OU) に属している場合、これらの OU も、Azure AD Connect で同期するよう構成する必要があります。 Azure AD Connect を使用してコンピューター オブジェクトを同期する方法の詳細については、[Azure AD Connect を使用したフィルタリングの構成](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)に関する記事を参照してください。

組織が送信プロキシ経由でのインターネットへのアクセスを必要とする場合、Microsoft では、Windows 10 コンピューターを Azure AD にデバイス登録できるように [Web プロキシ自動発見 (WPAD) を実装](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10))することを推奨しています。 WPAD の構成と管理で問題が発生した場合は、[自動検出のトラブルシューティング](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10))に関する記事を参照してください。 

WPAD を使用せずに自分のコンピューター上でプロキシ設定を構成する場合は、Windows 10 1709 以降で実行できます。 詳細については、[グループ ポリシー オブジェクト (GPO) を使用した WinHTTP 設定の構成](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)に関する記事を参照してください。

> [!NOTE]
> WinHTTP 設定を使用して自分のコンピューター上でプロキシ設定を構成すると、構成されたプロキシに接続できないコンピューターは、インターネットに接続できなくなります。

組織が認証された送信プロキシ経由でのインターネットへのアクセスを必要とする場合、お使いの Windows 10 コンピューターが送信プロキシに対して正常に認証されることを確認する必要があります。 Windows 10 コンピューターではマシン コンテキストを使用してデバイス登録が実行されるため、マシン コンテキストを使用して送信プロキシ認証を構成する必要があります。 構成要件については、送信プロキシ プロバイダーに確認してください。

## <a name="configure-hybrid-azure-ad-join"></a>ハイブリッド Azure AD 参加の構成

Azure AD Connect を使用してハイブリッド Azure AD 参加を構成するには、次のものが必要です。

- Azure AD テナントの全体管理者の資格情報  
- 各フォレストのエンタープライズ管理者の資格情報
- AD FS 管理者の資格情報

**Azure AD Connect を使用してハイブリッド Azure AD 参加を構成するには**:

1. Azure AD Connect を起動し、 **[構成]** を選択します。

   ![ようこそ](./media/hybrid-azuread-join-federated-domains/11.png)

1. **[追加のタスク]** ページで、 **[デバイス オプションの構成]** を選択し、 **[次へ]** を選択します。

   ![追加のタスク](./media/hybrid-azuread-join-federated-domains/12.png)

1. **[概要]** ページで **[次へ]** を選択します。

   ![概要](./media/hybrid-azuread-join-federated-domains/13.png)

1. **[Azure AD に接続]** ページで、Azure AD テナントの全体管理者の資格情報を入力し、 **[次へ]** を選択します。

   ![Azure への接続](./media/hybrid-azuread-join-federated-domains/14.png)

1. **[デバイス オプション]** ページで、 **[ハイブリッド Azure AD 参加の構成]** を選択し、 **[次へ]** を選択します。

   ![デバイス オプション](./media/hybrid-azuread-join-federated-domains/15.png)

1. **[SCP]** ページで、次の手順を実行し、 **[次へ]** を選択します。

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. フォレストを選択します。
   1. 認証サービスを選択します。 組織が Windows 10 クライアントのみを使用していて、ユーザーがコンピューターまたはデバイスの同期を構成済みか組織でシームレス SSO が使用されている場合を除き、 **[AD FS サーバー]** を選択する必要があります。
   1. **[追加]** を選択して、エンタープライズ管理者の資格情報を入力します。

1. **[デバイスのオペレーティング システム]** ページで、対象の Active Directory 環境内のデバイスで使用されているオペレーティング システムを選択し、 **[次へ]** を選択します。

   ![デバイスのオペレーティング システム](./media/hybrid-azuread-join-federated-domains/17.png)

1. **[フェデレーションの構成]** ページで、AD FS 管理者の資格情報を入力し、 **[次へ]** を選択します。

   ![フェデレーションの構成](./media/hybrid-azuread-join-federated-domains/18.png)

1. **[構成の準備完了]** ページで、 **[構成]** を選択します。

   ![構成の準備完了](./media/hybrid-azuread-join-federated-domains/19.png)

1. **[構成が完了しました]** ページで、 **[終了]** を選択します。

   ![構成の完了](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>ダウンレベルの Windows デバイスの有効化

ドメイン参加済みデバイスの一部がダウンレベルの Windows デバイスである場合は、以下の操作が必要です。

- デバイスの登録用のローカル イントラネット設定の構成
- ダウンレベルの Windows コンピューターへの Microsoft Workplace Join のインストール

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>デバイスの登録用のローカル イントラネット設定の構成

ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加を正常に完了するため、およびデバイスが Azure AD で認証を受けるときに証明書の指定を求めるメッセージが表示されないようにするために、対象のドメイン参加済みデバイスにポリシーをプッシュして、以下の URL を Internet Explorer のローカル イントラネット ゾーンに追加することができます。

- `https://device.login.microsoftonline.com`
- 組織の STS (フェデレーション ドメインの場合)
- `https://autologon.microsoftazuread-sso.com` (シームレス SSO の場合)

また、ユーザーのローカル イントラネット ゾーンで **[スクリプトを介したステータス バーの更新を許可する]** を有効にする必要があります。

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>ダウンレベルの Windows コンピューターへの Microsoft Workplace Join のインストール

ダウンレベルの Windows デバイスを登録するには、組織で [Windows 10 以外のコンピューター向けの Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554) をインストールする必要があります。 Windows 10 以外のコンピューター向けの Microsoft Workplace Join は、Microsoft ダウンロード センターで入手できます。

 [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) などのソフトウェア ディストリビューション システムを使用して、このパッケージをデプロイできます。 このパッケージは、`quiet` パラメーターを使用した標準のサイレント インストール オプションをサポートしています。 Configuration Manager の Current Branch には、完了した登録を追跡する機能など、以前のバージョンにはない利点が追加されています。

インストーラーによって、ユーザー コンテキストで実行されるシステムにスケジュール済みタスクが作成されます。 このタスクは、ユーザーが Windows にサインインするとトリガーされます。 このタスクでは、デバイスは Azure AD で認証が行われた後、そのユーザー資格情報を使用してサイレントに Azure AD に参加します。

## <a name="verify-the-registration"></a>登録の確認

Azure テナントでのデバイス登録状態を確認するために、[Azure Active Directory PowerShell モジュール](/powershell/azure/install-msonlinev1?view=azureadps-2.0)の **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** コマンドレットを使用できます。

**Get-MSolDevice** コマンドレットを使用してサービスの詳細を確認する場合:

- Windows クライアントの ID と一致する**デバイス ID** を備えたオブジェクトが存在する必要があります。
- **DeviceTrustType** の値は **[ドメイン参加済み]** でなければなりません。 この設定は、Azure AD ポータルの **[デバイス]** の下の **[ハイブリッド Azure AD 参加済み]** 状態に相当します。
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

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
