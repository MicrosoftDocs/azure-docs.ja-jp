---
title: Azure Active Directory (Azure AD) で Hybrid Azure Active Directory 参加の実装を計画する方法 | Microsoft Docs
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
ms.openlocfilehash: abb050eb527e65b4fd31f3251d37fef7d51e867e
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655977"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>方法:Hybrid Azure Active Directory 参加の実装を計画する

デバイスはユーザーと同様、保護の対象であり、時と場所を選ばずにリソースを保護するために使用したいもう 1 つの重要な ID です。 この目標は、次のいずれかの方法を使用してデバイスの ID を Azure AD に設定して管理することで達成できます。

- Azure AD 参加
- ハイブリッド Azure AD 参加
- Azure AD の登録

Azure AD にデバイスを設定して、クラウドとオンプレミスのリソースでのシングル サインオン (SSO) を実現することで、ユーザーの生産性を最大化できます。 同時に、[条件付きアクセス](../active-directory-conditional-access-azure-portal.md)を使用して、クラウドとオンプレミスのリソースへのアクセスを保護することもできます。

オンプレミスの Active Directory (AD) 環境があるときに、AD ドメイン参加済みコンピューターを Azure AD に参加させたい場合は、ハイブリッド Azure AD 参加を実行してこれを実現できます。 この記事では、ご使用の環境でハイブリッド Azure AD 参加を実装するための関連する手順について説明します。 

## <a name="prerequisites"></a>前提条件

この記事では、[Azure Active Directory でのデバイス ID 管理の概要](../device-management-introduction.md)を理解していることを前提とします

> [!NOTE]
> Windows 10 のハイブリッド Azure AD 参加に必要なドメイン機能とフォレスト機能の最小レベルは Windows Server 2008 R2 です。

## <a name="plan-your-implementation"></a>実装の計画

ハイブリッド Azure AD の実装を計画するには、以下を理解する必要があります。

|   |   |
| --- | --- |
| ![○][1] | サポート対象デバイスを確認する |
| ![○][1] | 知っておくべきことを確認する |
| ![○][1] | ハイブリッド Azure AD 参加の制御された検証を確認する |
| ![○][1] | ID インフラストラクチャに基づいてシナリオを選択する |
| ![○][1] | ハイブリッド Azure AD 参加でのオンプレミス AD UPN サポートを確認する |

## <a name="review-supported-devices"></a>サポート対象デバイスを確認する

ハイブリッド Azure AD 参加は、幅広い Windows デバイスをサポートしています。 旧バージョンの Windows を実行しているデバイスの構成の場合、追加の手順または異なる手順が必要なので、サポートされるデバイスは 2 つのカテゴリにグループ化されます。

### <a name="windows-current-devices"></a>最新の Windows デバイス

- Windows 10
- Windows Server 2016
- Windows Server 2019

Windows デスクトップ オペレーティング システムを実行しているデバイスの場合、サポートされているバージョンについてはこの記事「[Windows 10 リリース情報](https://docs.microsoft.com/windows/release-information/)」を参照してください。 ベスト プラクティスとして、Microsoft は Windows 10 の最新バージョンにアップグレードすることをお勧めします。

### <a name="windows-down-level-devices"></a>ダウンレベルの Windows デバイス

- Windows 8.1
- Windows 7。 Windows 7 のサポート情報については、この記事「[Windows 7 のサポート終了が近づいています](https://www.microsoft.com/en-us/windowsforbusiness/end-of-windows-7-support)」を参照してください
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

最初の計画手順として、環境を確認し、ダウンレベルの Windows デバイスをサポートする必要があるかどうかを判断する必要があります。

## <a name="review-things-you-should-know"></a>知っておくべきことを確認する

ID データを複数の Azure AD テナントに同期する単一 AD フォレストで構成されている環境の場合、ハイブリッド Azure AD 参加は現在サポートされていません。

仮想デスクトップ インフラストラクチャ (VDI) を使用している場合、ハイブリッド Azure AD 参加は現在サポートされていません。

Hybrid Azure AD 参加は、FIPS に準拠している TPM ではサポートされていません。 FIPS に準拠している TPM がデバイスにある場合は、Hybrid Azure AD 参加を進める前に、それらを無効にする必要があります。 TPM の FIPS モードを無効にするためのツールは、TPM の製造元に依存するため、Microsoft では用意していません。 サポートが必要な場合は、お使いのハードウェアの OEM にお問い合わせください。

ハイブリッド Azure AD 参加は、ドメイン コントローラー (DC) ロールを実行している Windows Server ではサポートされていません。

資格情報のローミングまたはユーザー プロファイルのローミングを使用している場合、ハイブリッド Azure AD 参加は Windows ダウンレベル デバイスではサポートされていません。

システム準備ツール (Sysprep) を使用していて、インストールに **pre-Windows 10 1809** イメージを使用している場合は、そのイメージが既にハイブリッド Azure AD 参加として Azure AD に登録されているデバイスからのものではないことを確認します。

仮想マシン (VM) のスナップショットを利用して追加の VM を作成する場合は、そのスナップショットが、既にハイブリッド Azure AD 参加として Azure AD に登録されている VM からのものではないことを確認します。

Windows 10 ドメイン参加済みデバイスが既にテナントへの [Azure AD 登録済み](https://docs.microsoft.com/azure/active-directory/devices/overview#getting-devices-in-azure-ad)である場合、Hybrid Azure AD 参加を有効にする前に、その状態の削除を検討することを強くお勧めします。 Windows 10 1809 リリース以降では、この二重状態を回避するために次の変更が行われています。

- デバイスが Hybrid Azure AD 参加済みになった後、既存の Azure AD 登録済み状態は自動的に削除されます。
- レジストリ キー HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001 を追加することで、ドメイン参加済みデバイスが Azure AD 登録済みになることを防ぐことができます。
- この変更は現在、KB4489894 が適用された Windows 10 1803 リリースでご利用いただけます。 ただし、Windows Hello for Business が構成されている場合、二重状態のクリーンアップ後に Windows Hello for Business を再設定する必要があります。

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>ハイブリッド Azure AD 参加の制御された検証を確認する

すべての前提条件がそろったら、Windows デバイスが、Azure AD テナントにデバイスとして自動的に登録されます。 Azure AD でのこれらのデバイス ID の状態は、ハイブリッド Azure AD 参加と呼ばれます。 この記事で説明する概念の詳細については、[Azure Active Directory でのデバイス ID 管理の概要](overview.md)と [Hybrid Azure Active Directory 参加の実装の計画](hybrid-azuread-join-plan.md)に関する記事を参照してください。

組織では、ハイブリッド Azure AD 参加を組織全体で同時に有効にする前に、その制御された検証を実行する必要がある場合があります。 実行方法については、[ハイブリッド Azure AD 参加の制御された検証](hybrid-azuread-join-control.md)に関する記事を参照してください。

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>ID インフラストラクチャに基づいてシナリオを選択する

ハイブリッド Azure AD 参加は、マネージド環境とフェデレーション環境の両方で動作します。  

### <a name="managed-environment"></a>マネージド環境

マネージド環境は、[パスワード ハッシュ同期 (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) または[パススルー認証 (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) の[シームレス シングル サインオン](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)を使用してデプロイできます。

これらのシナリオでは、フェデレーション サーバーを認証用に構成する必要はありません。

### <a name="federated-environment"></a>フェデレーション環境

フェデレーション環境には、以下の要件をサポートする ID プロバイダーが必要です。

- **WS-Trust プロトコル:** このプロトコルは、Windows の現在のハイブリッド Azure AD 参加デバイスを Azure AD で認証するために必要です。
- **WIAORMULTIAUTHN 要求:** この要求は、Windows ダウンレベル デバイスに対してハイブリッド Azure AD 参加を行うために必要です。

Active Directory フェデレーション サービス (AD FS) を使用しているフェデレーション環境がある場合は、上記の要件は既にサポートされています。

> [!NOTE]
> Azure AD は、マネージド ドメインでのスマートカードや証明書をサポートしていません。

バージョン 1.1.819.0 以降の Azure AD Connect には、ハイブリッド Azure AD 参加を構成するためのウィザードが用意されています。 このウィザードを使用すると、構成プロセスを大幅に簡略化できます。 Azure AD Connect の必要なバージョンをインストールすることができない場合は、[デバイス登録を手動で構成する方法](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual)に関するページを参照してください。 

お使いの ID インフラストラクチャと一致するシナリオに基づいて、以下を参照してください。

- [フェデレーション環境用のハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-federated-domains.md)
- [マネージド環境用のハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>ハイブリッド Azure AD 参加でのオンプレミス AD UPN サポートを確認する

ときには、オンプレミスの AD UPN が Azure AD UPN と異なる場合があります。 このような場合、Windows 10 の Hybrid Azure AD 参加では、[認証方法](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)、ドメインの種類、および Windows 10 のバージョンに基づいて、オンプレミスの AD UPN のサポートが提供されます。 環境内に存在できるオンプレミスの AD UPN は 2 種類あります。

- ルーティング可能な UPN:ルーティング可能な UPN には、ドメイン レジストラーに登録されている有効な確認済みドメインがあります。 たとえば、contoso.com が Azure AD 内のプライマリ ドメインの場合、contoso.org は、Contoso 社によって所有され、[Azure AD で確認](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)されているオンプレミスの AD 内のプライマリ ドメインです。
- ルーティング不可能な UPN:ルーティング不可能な UPN には、確認済みドメインはありません。 組織のプライベート ネットワーク内でのみ適用されます。 たとえば、contoso.com が Azure AD 内のプライマリ ドメインの場合、contoso.local はオンプレミスの AD 内のプライマリ ドメインですが、インターネットで確認可能なドメインではなく、Contoso 社のネットワーク内でのみ使用されます。

次の表は、Windows 10 の Hybrid Azure AD 参加における、これらのオンプレミスの AD UPN のサポートに関する詳細を示しています

| オンプレミスの AD UPN の種類 | ドメインの種類 | Windows 10 のバージョン | 説明 |
| ----- | ----- | ----- | ----- |
| ルーティング可能 | フェデレーション | 1703 リリースから | 一般公開 |
| ルーティング不可能 | フェデレーション | 1803 リリースから | 一般公開 |
| ルーティング可能 | マネージド | サポートされていません | |
| ルーティング不可能 | マネージド | サポートされていません | |

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [フェデレーション環境用のハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-federated-domains.md)
> [マネージド環境用のハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
