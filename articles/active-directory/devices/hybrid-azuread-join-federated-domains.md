---
title: フェデレーション ドメイン用のハイブリッド Azure Active Directory 参加の構成 | Microsoft Docs
description: フェデレーション ドメイン用のハイブリッド Azure Active Directory 参加を構成する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a37353615e35cd75c126c268de71d10077a9071
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268436"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>チュートリアル:フェデレーション ドメイン用のハイブリッド Azure Active Directory 参加の構成

組織内のユーザーと同様、デバイスは保護対象となる主要なアイデンティティです。 デバイスの ID を使用して、いつでもどこからでもリソースを保護できます。 この目標は、次のいずれかの方法を使用して、Azure Active Directory (Azure AD) にデバイス ID を取り込み、管理することで達成できます。

- Azure AD 参加
- ハイブリッド Azure AD 参加
- Azure AD の登録

Azure AD に自分のデバイスを取り込んで、クラウドとオンプレミスのリソースでのシングル サインオン (SSO) を実現することで、ユーザーの生産性を最大化できます。 同時に、[条件付きアクセス](../conditional-access/howto-conditional-access-policy-compliant-device.md)を使用して、クラウドとオンプレミスのリソースへのアクセスを保護できます。

フェデレーション環境には、以下の要件をサポートする ID プロバイダーが必要です。 Active Directory フェデレーション サービス (AD FS) を使用しているフェデレーション環境がある場合は、以下の要件は既にサポートされています。

- **WIAORMULTIAUTHN 要求:** この要求は、Windows ダウンレベル デバイスに対してハイブリッド Azure AD 参加を行うために必要です。
- **WS-Trust プロトコル:** このプロトコルは、Windows の現在のハイブリッド Azure AD 参加デバイスを Azure AD で認証するために必要です。
  AD FS を使用している場合は、次の WS-Trust エンドポイントを有効にする必要があります。`/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** と **adfs/services/trust/13/windowstransport** はどちらも、イントラネットに接続するエンドポイントとしてのみ有効にする必要があります。Web アプリケーション プロキシを介してエクストラネットに接続するエンドポイントとしては公開しないでください。 WS-Trust WIndows エンドポイントを無効にする方法の詳細については、[プロキシの WS-Trust Windows エンドポイントを無効にする方法](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)に関するセクションを参照してください。 どのエンドポイントが有効になっているかは、AD FS 管理コンソールの **[サービス]**  >  **[エンドポイント]** で確認できます。

このチュートリアルでは、AD FS を使用してフェデレーション環境で Active Directory ドメイン参加済みコンピューター デバイスのハイブリッド Azure AD 参加を構成する方法について説明します。

学習内容は次のとおりです。

> [!div class="checklist"]
> * ハイブリッド Azure AD 参加の構成
> * ダウンレベルの Windows デバイスの有効化
> * 登録の確認
> * トラブルシューティング

## <a name="prerequisites"></a>前提条件

このチュートリアルは、次の記事を理解していることを前提とします。

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

> [!WARNING]
> データ損失防止や Azure AD テナントの制限などのシナリオで SSL トラフィックを傍受するプロキシ サーバーを組織で使用している場合は、'https://device.login.microsoftonline.com ' へのトラフィックが TLS の中断と検査から除外されていることを確認してください。 'https://device.login.microsoftonline.com ' を除外しないと、クライアント証明書の認証に干渉し、デバイス登録とデバイスベースの条件付きアクセスに問題が発生する可能性があります。

Windows 10 1803 以降で、AD FS を使用したフェデレーション環境の即時的なハイブリッド Azure AD 参加が失敗した場合は、Azure AD Connect を利用して Azure AD のコンピューター オブジェクトを同期させます。これは後で、ハイブリッド Azure AD 参加のデバイス登録を完了するために使用されます。 Azure AD Connect で、ハイブリッド Azure AD 参加済みにするデバイスのコンピュータ オブジェクトを Azure AD に対して同期済みであることを確認します。 コンピューター オブジェクトが特定の組織単位 (OU) に属している場合、これらの OU も、Azure AD Connect で同期するよう構成する必要があります。 Azure AD Connect を使用してコンピューター オブジェクトを同期する方法の詳細については、[Azure AD Connect を使用したフィルタリングの構成](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)に関する記事を参照してください。

組織が送信プロキシ経由でのインターネットへのアクセスを必要とする場合、Microsoft では、Windows 10 コンピューターを Azure AD にデバイス登録できるように [Web プロキシ自動発見 (WPAD) を実装](/previous-versions/tn-archive/cc995261(v%3dtechnet.10))することを推奨しています。 WPAD の構成と管理で問題が発生した場合は、[自動検出のトラブルシューティング](/previous-versions/tn-archive/cc302643(v=technet.10))に関する記事を参照してください。 

WPAD を使用せずに自分のコンピューター上でプロキシ設定を構成する場合は、Windows 10 1709 以降で実行できます。 詳細については、[グループ ポリシー オブジェクト (GPO) を使用した WinHTTP 設定の構成](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo)に関する記事を参照してください。

> [!NOTE]
> WinHTTP 設定を使用して自分のコンピューター上でプロキシ設定を構成すると、構成されたプロキシに接続できないコンピューターは、インターネットに接続できなくなります。

組織が認証された送信プロキシ経由でのインターネットへのアクセスを必要とする場合、お使いの Windows 10 コンピューターが送信プロキシに対して正常に認証されることを確認する必要があります。 Windows 10 コンピューターではマシン コンテキストを使用してデバイス登録が実行されるため、マシン コンテキストを使用して送信プロキシ認証を構成する必要があります。 構成要件については、送信プロキシ プロバイダーに確認してください。

デバイスがシステム アカウントで上記の Microsoft リソースにアクセスできるかどうかを確認するには、「[デバイス登録接続のテスト](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)」スクリプトを使用できます。

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

> [!NOTE]
> Windows 7 のサポートは 2020 年 1 月 14 日に終了しました。 詳細については、[Windows 7 のサポート終了](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)に関する記事を参照してください。

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>デバイスの登録用のローカル イントラネット設定の構成

ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加を正常に完了するため、およびデバイスが Azure AD で認証を受けるときに証明書の指定を求めるメッセージが表示されないようにするために、対象のドメイン参加済みデバイスにポリシーをプッシュして、以下の URL を Internet Explorer のローカル イントラネット ゾーンに追加することができます。

- `https://device.login.microsoftonline.com`
- 組織の STS (フェデレーション ドメインの場合)
- `https://autologon.microsoftazuread-sso.com` (シームレス SSO の場合)

また、ユーザーのローカル イントラネット ゾーンで **[スクリプトを介したステータス バーの更新を許可する]** を有効にする必要があります。

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>ダウンレベルの Windows コンピューターへの Microsoft Workplace Join のインストール

ダウンレベルの Windows デバイスを登録するには、組織で [Windows 10 以外のコンピューター向けの Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554) をインストールする必要があります。 Windows 10 以外のコンピューター向けの Microsoft Workplace Join は、Microsoft ダウンロード センターで入手できます。

 [Microsoft Endpoint Configuration Manager](/configmgr/) などのソフトウェア ディストリビューション システムを使用して、このパッケージをデプロイできます。 このパッケージは、`quiet` パラメーターを使用した標準のサイレント インストール オプションをサポートしています。 Configuration Manager の Current Branch には、完了した登録を追跡する機能など、以前のバージョンにはない利点が追加されています。

インストーラーによって、ユーザー コンテキストで実行されるシステムにスケジュール済みタスクが作成されます。 このタスクは、ユーザーが Windows にサインインするとトリガーされます。 このタスクでは、デバイスは Azure AD で認証が行われた後、そのユーザー資格情報を使用してサイレントに Azure AD に参加します。

## <a name="verify-the-registration"></a>登録の確認

デバイスの状態を特定して確認するには、次の 3 つの方法があります。

### <a name="locally-on-the-device"></a>デバイス上でローカルに

1. Windows PowerShell を開きます。
2. 「`dsregcmd /status`」と入力します。
3. **AzureAdJoined** と **DomainJoined** の両方が **YES** に設定されていることを確認します。
4. **DeviceId** を使用すると、Azure portal または PowerShell のいずれかで、サービスの状態を比較できます。

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

1. [直接リンク](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)を使用して、デバイス ページに移動します。
2. デバイスを特定する方法については、[Azure portal を使用してデバイス ID を管理する方法](./device-management-azure-portal.md)に関するページをご覧ください。
3. **[登録済み]** 列に **[保留中]** と表示されている場合、Hybrid Azure AD Join は完了していません。 フェデレーション環境では、登録に失敗し、デバイスを同期するように AAD Connect が構成されている場合にのみ、この問題が発生する可能性があります。
4. **[登録済み]** 列に**日付/時刻**が含まれている場合、Hybrid Azure AD Join は完了しています。

### <a name="using-powershell"></a>PowerShell の使用

**[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)** を使用して、Azure テナントのデバイス登録状態を確認します。 このコマンドレットは、[Azure Active Directory PowerShell モジュール](/powershell/azure/active-directory/install-msonlinev1?view=azureadps-2.0)内にあります。

**Get-MSolDevice** コマンドレットを使用してサービスの詳細を確認する場合:

- Windows クライアントの ID と一致する**デバイス ID** を備えたオブジェクトが存在する必要があります。
- **DeviceTrustType** の値は **[ドメイン参加済み]** です。 この設定は、Azure AD ポータルの **[デバイス]** ページの **[ハイブリッド Azure AD 参加済み]** 状態に相当します。
- 条件付きアクセスで使用されるデバイスの場合、**Enabled** の値は **True**、**DeviceTrustLevel** の値は **Managed** です。

1. Windows PowerShell を管理者として開きます。
2. 「`Connect-MsolService`」と入力して Azure テナントに接続します。

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>すべての Hybrid Azure AD 参加済みデバイスをカウントする ( **[保留中]** 状態を除く)

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>**[保留中]** 状態を含むすべての Hybrid Azure AD 参加済みデバイスをカウントする

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>すべての Hybrid Azure AD 参加済みデバイスを一覧表示する

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>**[保留中]** 状態を含むすべての Hybrid Azure AD 参加済みデバイスを一覧表示する

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>1 つのデバイスの詳細情報を表示するには、次のようにします。

1. 「`get-msoldevice -deviceId <deviceId>`」と入力します (これは、デバイスでローカルに取得された **DeviceId** です)。
2. **[有効]** が **[True]** に設定されていることを確認します。

## <a name="troubleshoot-your-implementation"></a>実装のトラブルシューティング

ドメイン参加済み Windows デバイスのハイブリッド Azure AD 参加を行うときに問題が発生した場合は、以下を参照してください。

- [dsregcmd コマンドを使用したデバイスのトラブルシューティング](./troubleshoot-device-dsregcmd.md)
- [最新の Windows デバイスのハイブリッド Azure AD 参加のトラブルシューティング](troubleshoot-hybrid-join-windows-current.md)
- [ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加のトラブルシューティング](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>次のステップ

[Azure portal を使用してデバイス ID を管理する](device-management-azure-portal.md)方法を学習します。

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png