---
title: ハイブリッド Azure Active Directory 参加を計画する - Azure Active Directory
description: ハイブリッド Azure Active Directory 参加済みデバイスの構成方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cadba181ea7d6a12ca64c78f3c7c58654d5f756f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500810"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>方法:Hybrid Azure Active Directory 参加の実装を計画する

デバイスはユーザーと同様、保護の対象であり、時と場所を選ばずにリソースを保護するために使用したいもう 1 つの重要な ID です。 この目標は、次のいずれかの方法を使用してデバイスの ID を Azure AD に設定して管理することで達成できます。

- Azure AD 参加
- ハイブリッド Azure AD 参加
- Azure AD の登録

Azure AD にデバイスを設定して、クラウドとオンプレミスのリソースでのシングル サインオン (SSO) を実現することで、ユーザーの生産性を最大化できます。 同時に、[条件付きアクセス](../conditional-access/overview.md)を使用して、クラウドとオンプレミスのリソースへのアクセスを保護することもできます。

オンプレミスの Active Directory (AD) 環境があるときに、AD ドメイン参加済みコンピューターを Azure AD に参加させたい場合は、ハイブリッド Azure AD 参加を実行してこれを実現できます。 この記事では、ご使用の環境でハイブリッド Azure AD 参加を実装するための関連する手順について説明します。 

## <a name="prerequisites"></a>前提条件

この記事では、[Azure Active Directory でのデバイス ID 管理の概要](./overview.md)を理解していることを前提とします

> [!NOTE]
> Windows 10 の Hybrid Azure AD 参加に最低限必要なドメイン コントローラー バージョンは、Windows Server 2008 R2 です。

## <a name="plan-your-implementation"></a>実装の計画

ハイブリッド Azure AD の実装を計画するには、以下を理解する必要があります。

> [!div class="checklist"]
> - サポート対象デバイスを確認する
> - 知っておくべきことを確認する
> - ハイブリッド Azure AD 参加の制御された検証を確認する
> - ID インフラストラクチャに基づいてシナリオを選択する
> - ハイブリッド Azure AD 参加でのオンプレミス AD UPN サポートを確認する

## <a name="review-supported-devices"></a>サポート対象デバイスを確認する

ハイブリッド Azure AD 参加は、幅広い Windows デバイスをサポートしています。 旧バージョンの Windows を実行しているデバイスの構成の場合、追加の手順または異なる手順が必要なので、サポートされるデバイスは 2 つのカテゴリにグループ化されます。

### <a name="windows-current-devices"></a>最新の Windows デバイス

- Windows 10
- Windows Server 2016
  - **注**:Azure 国内クラウドのお客様にはバージョン 1803 が必要です
- Windows Server 2019

Windows デスクトップ オペレーティング システムを実行しているデバイスの場合、サポートされているバージョンについてはこの記事「[Windows 10 リリース情報](/windows/release-information/)」を参照してください。 ベスト プラクティスとして、Microsoft は Windows 10 の最新バージョンにアップグレードすることをお勧めします。

### <a name="windows-down-level-devices"></a>ダウンレベルの Windows デバイス

- Windows 8.1
- Windows 7 のサポートは 2020 年 1 月 14 日に終了しました。 詳しくは、[Windows 7 のサポート終了](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)に関する記事をご覧ください。
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2。 Windows Server 2008 および 2008 R2 のサポート情報については、「[Windows Server 2008 のサポート終了に備える](https://www.microsoft.com/cloud-platform/windows-server-2008)」を参照してください。

最初の計画手順として、環境を確認し、ダウンレベルの Windows デバイスをサポートする必要があるかどうかを判断する必要があります。

## <a name="review-things-you-should-know"></a>知っておくべきことを確認する

### <a name="unsupported-scenarios"></a>サポートされていないシナリオ
- ID データを複数の Azure AD テナントに同期する単一 AD フォレストで構成されている環境の場合、ハイブリッド Azure AD 参加は現在サポートされていません。

- ハイブリッド Azure AD 参加は、ドメイン コントローラー (DC) ロールを実行している Windows Server ではサポートされていません。

- 資格情報のローミングや、ユーザー プロファイルまたは必須のプロファイルのローミングを使用している場合、Hybrid Azure AD 参加は Windows ダウンレベル デバイスではサポートされていません。

- Server Core OS では、どの種類のデバイス登録もサポートされていません。

- ユーザー状態移行ツール (USMT) では、デバイス登録は処理されません。  

### <a name="os-imaging-considerations"></a>OS イメージングの考慮事項
- システム準備ツール (Sysprep) を使用していて、インストールに **pre-Windows 10 1809** イメージを使用している場合は、そのイメージが既にハイブリッド Azure AD 参加として Azure AD に登録されているデバイスからのものではないことを確認します。

- 仮想マシン (VM) のスナップショットを利用して追加の VM を作成する場合は、そのスナップショットが、既にハイブリッド Azure AD 参加として Azure AD に登録されている VM からのものではないことを確認します。

- 再起動時にディスクへの変更をクリアする [統合書き込みフィルター](/windows-hardware/customize/enterprise/unified-write-filter) および類似のテクノロジーを使用している場合、デバイスが Hybrid Azure AD に結合した後にそれらを適用する必要があります。 Hybrid Azure AD の結合を完了する前にこのようなテクノロジーを有効にすると、再起動のたびにデバイスが切断されることになります

### <a name="handling-devices-with-azure-ad-registered-state"></a>Azure AD 登録状態のデバイスの処理
Windows 10 ドメイン参加済みデバイスが既にテナントへの [Azure AD 登録済み](overview.md#getting-devices-in-azure-ad)である場合、デバイスは、Hybrid Azure AD 参加済みでかつ Azure AD に登録済みの二重状態になる可能性があります。 このシナリオに自動的に対処するには、(KB4489894 が適用された) Windows 10 1803 以上にアップグレードすることをお勧めします。 1803 より前のリリースでは、Hybrid Azure AD 参加を有効にする前に、Azure AD の登録済み状態を手動で削除する必要があります。 1803 以降のリリースでは、この二重状態を回避するために次の変更が行われています。

- "<i>デバイスが Hybrid Azure AD 参加済みになり、同じユーザーがログインした後</i>"、ユーザーの既存の Azure AD 登録済み状態は自動的に削除されます。 たとえば、ユーザー A がデバイスに Azure AD 登録済み状態を持っている場合は、ユーザー A がデバイスにログインしたときにのみ、ユーザー A の二重状態はクリーンアップされます。 同じデバイスに複数のユーザーがいる場合、それらのユーザーがログインすると、二重状態は個別にクリーンアップされます。 Azure AD の登録済み状態が削除されるだけでなく、Windows 10 では、登録が自動登録を介して Azure AD 登録の一部として行われた場合に、Intune またはその他の MDM からもデバイスの登録が解除されます。
- デバイス上のすべてのローカル アカウントの Azure AD 登録済みの状態は、この変更による影響を受けません。 これは、ドメイン アカウントにのみ適用されます。 そのため、ローカル アカウントの Azure AD 登録済みの状態は、ユーザーがドメイン ユーザーではないため、ユーザーのログオン後でも自動的には削除されません。 
- 次のレジストリ値を HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin に追加すると、ドメイン参加済みデバイスが Azure AD 登録済みになることを防ぐことができます:"BlockAADWorkplaceJoin"=dword:00000001。
- Windows 10 1803 では、Windows Hello for Business が構成されている場合、ユーザーは、二重状態のクリーンアップ後に Windows Hello for Business を再設定する必要があります。この問題は KB4512509 で解決されています

> [!NOTE]
> Windows 10 では Azure AD の登録済み状態がローカルで自動的に削除されますが、Azure AD 内のデバイス オブジェクトは、Intune で管理されている場合に、直ちに削除されません。 Azure AD 登録済みの状態の削除を確認するには、dsregcmd /status を実行し、それに基づいて、デバイスが Azure AD に登録されていないものと見なすことができます。

### <a name="additional-considerations"></a>その他の注意点
- 環境で仮想デスクトップ インフラストラクチャ (VDI) を使用する場合は、「[デバイス ID とデスクトップ仮想化](./howto-device-identity-virtual-desktop-infrastructure.md)」を参照してください。

- Hybrid Azure AD 参加は、FIPS に準拠している TPM 2.0 でサポートされており、TPM 1.2 ではサポートされていません。 FIPS に準拠している TPM 1.2 がデバイスにある場合は、Hybrid Azure AD 参加を進める前に、それらを無効にする必要があります。 TPM の FIPS モードを無効にするためのツールは、TPM の製造元に依存するため、Microsoft では用意していません。 サポートが必要な場合は、お使いのハードウェアの OEM にお問い合わせください。 

- Windows 10 1903 リリース以降、TPM 1.2 はハイブリッド Azure AD 結合では使用されず、それらの TPM を含むデバイスは TPM を持っていないものと見なされます。

- UPN の変更は、Windows 10 2004 Update 以降でのみサポートされます。 Windows 10 2004 Update より前のデバイスの場合は、ユーザーのデバイスで SSO および条件付きアクセスに関する問題が発生する可能性があります。 この問題を解決するには、Azure AD からデバイスの参加を解除 (昇格された特権を使用して "dsregcmd /leave" を実行) し、再度参加する (自動的に行われる) 必要があります。 なお、この問題は Windows Hello for Business でサインインしているユーザーには影響しません。

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>ハイブリッド Azure AD 参加の制御された検証を確認する

すべての前提条件がそろったら、Windows デバイスが、Azure AD テナントにデバイスとして自動的に登録されます。 Azure AD でのこれらのデバイス ID の状態は、ハイブリッド Azure AD 参加と呼ばれます。 この記事で説明する概念の詳細については、[Azure Active Directory でのデバイス ID 管理の概要](overview.md)に関する記事を参照してください。

組織では、ハイブリッド Azure AD 参加を組織全体で同時に有効にする前に、その制御された検証を実行する必要がある場合があります。 実行方法については、[ハイブリッド Azure AD 参加の制御された検証](hybrid-azuread-join-control.md)に関する記事を参照してください。

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>ID インフラストラクチャに基づいてシナリオを選択する

Hybrid Azure AD 参加は、UPN がルーティング可能かどうかに応じて、マネージド環境とフェデレーション環境の両方で動作します。 サポートされるシナリオについては、ページ下部の表を参照してください。  

### <a name="managed-environment"></a>マネージド環境

マネージド環境は、[パスワード ハッシュ同期 (PHS)](../hybrid/whatis-phs.md) または[パススルー認証 (PTA)](../hybrid/how-to-connect-pta.md) の[シームレス シングル サインオン](../hybrid/how-to-connect-sso.md)を使用してデプロイできます。

これらのシナリオでは、フェデレーション サーバーを認証用に構成する必要はありません。

> [!NOTE]
> [段階的なロールアウトを使用するクラウド認証](../hybrid/how-to-connect-staged-rollout.md)は、Windows 10 1903 Update 以降でのみサポートされます

### <a name="federated-environment"></a>フェデレーション環境

フェデレーション環境には、以下の要件をサポートする ID プロバイダーが必要です。 Active Directory フェデレーション サービス (AD FS) を使用しているフェデレーション環境がある場合は、以下の要件は既にサポートされています。

- **WIAORMULTIAUTHN 要求:** この要求は、Windows ダウンレベル デバイスに対してハイブリッド Azure AD 参加を行うために必要です。
- **WS-Trust プロトコル:** このプロトコルは、Windows の現在のハイブリッド Azure AD 参加デバイスを Azure AD で認証するために必要です。 AD FS を使用している場合は、次の WS-Trust エンドポイントを有効にする必要があります。`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** と **adfs/services/trust/13/windowstransport** はどちらも、イントラネットに接続するエンドポイントとしてのみ有効にする必要があります。Web アプリケーション プロキシを介してエクストラネットに接続するエンドポイントとして公開することはできません。 WS-Trust WIndows エンドポイントを無効にする方法の詳細については、[プロキシの WS-Trust Windows エンドポイントを無効にする方法](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)に関するセクションを参照してください。 どのエンドポイントが有効になっているかは、AD FS 管理コンソールの **[サービス]**  >  **[エンドポイント]** で確認できます。

> [!NOTE]
> Azure AD は、マネージド ドメインでのスマートカードや証明書をサポートしていません。

バージョン 1.1.819.0 以降の Azure AD Connect には、ハイブリッド Azure AD 参加を構成するためのウィザードが用意されています。 このウィザードを使用すると、構成プロセスを大幅に簡略化できます。 Azure AD Connect の必要なバージョンをインストールすることができない場合は、[デバイス登録を手動で構成する方法](hybrid-azuread-join-manual.md)に関するページを参照してください。 

お使いの ID インフラストラクチャと一致するシナリオに基づいて、以下を参照してください。

- [フェデレーション環境用のハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-federated-domains.md)
- [マネージド環境用のハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>ハイブリッド Azure AD 参加でのオンプレミス AD ユーザー UPN サポートを確認する

ときには、オンプレミスの AD ユーザー UPN が Azure AD UPN と異なる場合があります。 このような場合、Windows 10 の Hybrid Azure AD 参加では、[認証方法](../hybrid/choose-ad-authn.md)、ドメインの種類、および Windows 10 のバージョンに基づいて、オンプレミスの AD UPN のサポートが提供されます。 環境内に存在できるオンプレミスの AD UPN は 2 種類あります。

- ルーティング可能なユーザーの UPN: ルーティング可能な UPN には、ドメイン レジストラーに登録されている有効な確認済みドメインがあります。 たとえば、contoso.com が Azure AD 内のプライマリ ドメインの場合、contoso.org は、Contoso 社によって所有され、[Azure AD で確認](../fundamentals/add-custom-domain.md)されているオンプレミスの AD 内のプライマリ ドメインです。
- ルーティング不可能なユーザーの UPN: ルーティング不可能な UPN には、確認済みドメインはありません。 組織のプライベート ネットワーク内でのみ適用されます。 たとえば、contoso.com が Azure AD 内のプライマリ ドメインの場合、contoso.local はオンプレミスの AD 内のプライマリ ドメインですが、インターネットで確認可能なドメインではなく、Contoso 社のネットワーク内でのみ使用されます。

> [!NOTE]
> このセクションの情報は、オンプレミスのユーザー UPN に対してのみ適用されます。 オンプレミスのコンピューター ドメイン サフィックスには適用されません (例: computer1.contoso.local)。

次の表は、Windows 10 の Hybrid Azure AD 参加における、これらのオンプレミスの AD UPN のサポートに関する詳細を示しています

| オンプレミスの AD UPN の種類 | ドメインの種類 | Windows 10 のバージョン | 説明 |
| ----- | ----- | ----- | ----- |
| ルーティング可能 | フェデレーション | 1703 リリースから | 一般公開 |
| ルーティング不可能 | フェデレーション | 1803 リリースから | 一般公開 |
| ルーティング可能 | マネージド | 1803 リリースから | 一般公開されており、Windows ロック画面の Azure AD SSPR はサポートされていません。 オンプレミスの UPN は、Azure AD の `onPremisesUserPrincipalName` 属性に同期する必要があります |
| ルーティング不可能 | マネージド | サポートされていません | |

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [フェデレーション環境用のハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-federated-domains.md)
> [マネージド環境用のハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
