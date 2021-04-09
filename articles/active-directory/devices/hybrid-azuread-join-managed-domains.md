---
title: マネージド ドメイン用のハイブリッド Azure Active Directory 参加の構成 | Microsoft Docs
description: マネージド ドメイン用のハイブリッド Azure Active Directory 参加を構成する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4545e92767a427b8cd89af07ed4d06053685977a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578007"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>チュートリアル:マネージド ドメイン用のハイブリッド Azure Active Directory 参加の構成

このチュートリアルでは、Active Directory ドメイン参加済みデバイスのハイブリッド Azure Active Directory (Azure AD) 参加を構成する方法について説明します。 この方法では、オンプレミスの Active Directory と Azure AD の両方を含むマネージド環境がサポートされます。

組織内のユーザーと同様、デバイスは保護対象となる主要なアイデンティティです。 デバイスの ID を使用して、いつでもどこからでもリソースを保護できます。 この目標は、Azure AD でデバイスの ID を管理することで達成できます。 以下のいずれかの方法を使用します。

- Azure AD 参加
- ハイブリッド Azure AD 参加
- Azure AD の登録

この記事では、Hybrid Azure AD Join に重点を置いて説明します。

Azure AD に自分のデバイスを取り込んで、クラウドとオンプレミスのリソースでのシングル サインオン (SSO) を実現することで、ユーザーの生産性を最大化できます。 同時に、[条件付きアクセス](../conditional-access/howto-conditional-access-policy-compliant-device.md)を使用して、クラウドとオンプレミスのリソースへのアクセスを保護できます。

マネージド環境は、[パスワード ハッシュ同期 (PHS)](../hybrid/whatis-phs.md) または[パススルー認証 (PTA)](../hybrid/how-to-connect-pta.md) の[シームレス シングル サインオン](../hybrid/how-to-connect-sso.md)を使用してデプロイできます。 これらのシナリオでは、フェデレーション サーバーを認証用に構成する必要はありません。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ハイブリッド Azure AD 参加の構成
> * ダウンレベルの Windows デバイスの有効化
> * 参加済みデバイスの確認
> * トラブルシューティング

## <a name="prerequisites"></a>前提条件

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 以降)
- Azure AD テナントの全体管理者の資格情報
- 各フォレストのエンタープライズ管理者の資格情報

以下の記事の内容を確認します。

- [デバイス ID とは](overview.md)
- [方法: ハイブリッド Azure Active Directory 参加の実装の計画](hybrid-azuread-join-plan.md)
- [ハイブリッド Azure AD 参加の検証を制御する](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD は、マネージド ドメインでのスマートカードや証明書をサポートしていません。

Azure AD Connect で、ハイブリッド Azure AD 参加済みにするデバイスのコンピュータ オブジェクトを Azure AD に対して同期済みであることを確認します。 コンピューター オブジェクトが特定の組織単位 (OU) に属している場合は、これらの OU を Azure AD Connect で同期するように構成します。 Azure AD Connect を使用してコンピューター オブジェクトを同期する方法の詳細については、「[組織単位ベースのフィルター処理](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)」を参照してください。

> [!NOTE]
> デバイス登録構成の過程でデバイス登録の同期参加を成功させるには、Azure AD Connect 同期構成から既定のデバイス属性を除外しないでください。 AAD と同期される既定のデバイス属性について詳しくは、[Azure AD Connect によって同期される属性](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10)に関するセクションを参照してください。

バージョン 1.1.819.0 以降の Azure AD Connect には、Hybrid Azure AD Join を構成するためのウィザードが用意されています。 このウィザードを使用すると、構成プロセスを大幅に簡略化できます。 このウィザードで、デバイス登録のためのサービス接続ポイント (SCP) を構成します。

この記事の構成手順は、Azure AD Connect でこのウィザードを使用することに基づいています。

ハイブリッド Azure AD 参加では、デバイスが組織のネットワーク内から次の Microsoft リソースにアクセスできる必要があります。  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (シームレス SSO を使用しているか、使用する予定の場合)

> [!WARNING]
> データ損失防止や Azure AD テナントの制限などのシナリオで SSL トラフィックを傍受するプロキシ サーバーを組織で使用している場合は、'https://device.login.microsoftonline.com ' へのトラフィックが TLS の中断と検査から除外されていることを確認してください。 'https://device.login.microsoftonline.com ' を除外しないと、クライアント証明書の認証に干渉し、デバイス登録とデバイスベースの条件付きアクセスに問題が発生する可能性があります。

組織がアウトバウンド プロキシ経由でのインターネットへのアクセスを必要とする場合は、Windows 10 コンピューターを Azure AD にデバイス登録できるように、[Web プロキシ自動発見 (WPAD) を実装](/previous-versions/tn-archive/cc995261(v=technet.10))することができます。 WPAD の構成と管理の問題に対処するには、「[自動検出のトラブルシューティング](/previous-versions/tn-archive/cc302643(v=technet.10))」を参照してください。 更新プログラム 1709 より前の Windows 10 デバイスでは、Hybrid Azure AD Join と連携するようにプロキシを構成するためのオプションとして WPAD のみを選択できます。 

WPAD を使用しない場合は、Windows 10 1709 以降のコンピューター上で WinHTTP プロキシ設定を構成できます。 詳細については、「[GPO によってデプロイされる WinHTTP プロキシ設定](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo)」を参照してください。

> [!NOTE]
> WinHTTP 設定を使用して自分のコンピューター上でプロキシ設定を構成すると、構成されたプロキシに接続できないコンピューターは、インターネットに接続できなくなります。

組織が認証されたアウトバウンド プロキシ経由でのインターネットへのアクセスを必要とする場合、お使いの Windows 10 コンピューターがアウトバウンド プロキシに対して正常に認証されることを確認してください。 Windows 10 コンピューターではマシン コンテキストを使用してデバイス登録が実行されるため、マシン コンテキストを使用してアウトバウンド プロキシ認証を構成します。 構成要件については、送信プロキシ プロバイダーに確認してください。

[デバイス登録接続のテスト](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/)のスクリプトを使用して、デバイスがシステム アカウントで上記の Microsoft リソースにアクセスできることを確認します。

## <a name="configure-hybrid-azure-ad-join"></a>ハイブリッド Azure AD 参加の構成

Azure AD Connect を使用して Hybrid Azure AD Join を構成するには:

1. Azure AD Connect を起動し、 **[構成]** を選択します。

1. **[追加のタスク]** で、 **[デバイス オプションの構成]** を選択し、 **[次へ]** を選択します。

   ![追加のタスク](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. **[概要]** で **[次へ]** を選択します。

1. **[Azure AD に接続]** で、Azure AD テナントの全体管理者の資格情報を入力します。  

1. **[デバイス オプション]** で、 **[ハイブリッド Azure AD 参加の構成]** を選択し、 **[次へ]** を選択します。

   ![デバイス オプション](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. **[デバイスのオペレーティング システム]** で、Active Directory 環境内のデバイスで使用されているオペレーティング システムを選択し、 **[次へ]** を選択します。

   ![デバイスのオペレーティング システム](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. **[SCP の構成]** で、Azure AD Connect で SCP を構成するフォレストごとに次の手順を実行し、 **[次へ]** を選択します。

   1. **フォレスト** を選択します。
   1. **認証サービス** を選択します。
   1. **[追加]** を選択して、エンタープライズ管理者の資格情報を入力します。

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. **[構成の準備完了]** で、 **[構成]** を選択します。

1. **[構成が完了しました]** で、 **[終了]** を選択します。

## <a name="enable-windows-down-level-devices"></a>ダウンレベルの Windows デバイスの有効化

ドメイン参加済みデバイスの一部がダウンレベルの Windows デバイスである場合は、以下の操作が必要です。

- デバイスの登録用のローカル イントラネット設定の構成
- シームレス SSO の構成
- ダウンレベルの Windows コンピューターに対する Microsoft Workplace Join のインストール

> [!NOTE]
> Windows 7 のサポートは 2020 年 1 月 14 日に終了しました。 詳細については、[Windows 7 のサポートの終了](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020)に関するページを参照してください。

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>デバイスの登録用のローカル イントラネット設定の構成

ダウンレベルの Windows デバイスの Hybrid Azure AD Join を完了するため、およびデバイスが Azure AD で認証を受けるときに証明書の指定を求めるメッセージが表示されないようにするために、ドメイン参加済みデバイスにポリシーをプッシュして、以下の URL を Internet Explorer のローカル イントラネット ゾーンに追加することができます。

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

また、ユーザーのローカル イントラネット ゾーンで **[スクリプトを介したステータス バーの更新を許可する]** を有効にする必要があります。

### <a name="configure-seamless-sso"></a>シームレス SSO の構成

Azure AD クラウド認証方法として[パスワード ハッシュ同期](../hybrid/whatis-phs.md)または[パススルー認証](../hybrid/how-to-connect-pta.md)を使用しているマネージド ドメインで、ダウンレベルの Windows デバイスの Hybrid Azure AD Join を完了するには、[シームレス SSO を構成](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)する必要もあります。

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>ダウンレベルの Windows コンピューターに対する Microsoft Workplace Join のインストール

ダウンレベルの Windows デバイスを登録するには、組織で [Windows 10 以外のコンピューター向けの Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554) をインストールする必要があります。 Windows 10 以外のコンピューター向けの Microsoft Workplace Join は、Microsoft ダウンロード センターで入手できます。

 [Microsoft Endpoint Configuration Manager](/configmgr/) などのソフトウェア ディストリビューション システムを使用して、このパッケージをデプロイできます。 このパッケージは、`quiet` パラメーターを使用した標準のサイレント インストール オプションをサポートしています。 Configuration Manager の現在のバージョンには、完了した登録を追跡する機能など、以前のバージョンにはない利点が追加されています。

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
3. **[登録済み]** 列に **[保留中]** と表示されている場合、Hybrid Azure AD Join は完了していません。
4. **[登録済み]** 列に **日付/時刻** が含まれている場合、Hybrid Azure AD Join は完了しています。

### <a name="using-powershell"></a>PowerShell の使用

**[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)** を使用して、Azure テナントのデバイス登録状態を確認します。 このコマンドレットは、[Azure Active Directory PowerShell モジュール](/powershell/azure/active-directory/install-msonlinev1)内にあります。

**Get-MSolDevice** コマンドレットを使用してサービスの詳細を確認する場合:

- Windows クライアントの ID と一致する **デバイス ID** を備えたオブジェクトが存在する必要があります。
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

ドメイン参加済み Windows デバイスの Hybrid Azure AD Join を行うときに問題が発生した場合は、以下を参照してください。

- [dsregcmd コマンドを使用したデバイスのトラブルシューティング](./troubleshoot-device-dsregcmd.md)
- [ハイブリッド Azure Active Directory 参加済みデバイスのトラブルシューティング](troubleshoot-hybrid-join-windows-current.md)
- [ハイブリッド Azure Active Directory 参加済みダウンレベル デバイスのトラブルシューティング](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>次のステップ

次の記事に進んで、Azure portal を使用してデバイス ID を管理する方法を学習します。
> [!div class="nextstepaction"]
> [デバイス ID を管理する](device-management-azure-portal.md)
