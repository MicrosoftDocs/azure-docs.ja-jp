---
title: Azure Active Directory デバイスのデプロイを計画する
description: 組織のニーズに合った Azure AD デバイスの統合戦略を選択します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc631e4329c1df2cdcbfe57c2b43ccccf14afa85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936449"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>Azure Active Directory デバイスのデプロイを計画する

この記事は、デバイスを Azure AD と統合する方法を評価し、実装計画を選択するのに役立ちます。また、サポートされているデバイス管理ツールへの主要なリンクを提供します。

ユーザーがサインインするデバイスの環境は拡大しています。 組織は、デスクトップ、ラップトップ、携帯電話、タブレットなどのデバイスを提供する場合があります。 ユーザーが多くの自分のデバイスを使用したり、さまざまな場所から情報にアクセスしたりする場合があります。 このような環境で管理者としての職務は、すべてのデバイスで組織のリソースを安全な状態に保つことです。

Azure Active Directory (Azure AD) を使用すると、組織では、デバイス ID 管理を使用してこれらの目標を達成することができます。 デバイスを Azure AD に取り込んで、[Azure portal](https://portal.azure.com/) の中央の場所から制御できるようになりました。 これにより統合されたエクスペリエンスと強化されたセキュリティが得られ、新しいデバイスの構成に必要な時間が短縮されます。

デバイスを Azure AD に統合する方法は複数あります。

* Azure AD に[デバイスを登録](concept-azure-ad-register.md)することができます

* Azure AD (クラウド専用) に[デバイスを参加させる](concept-azure-ad-join.md)ことができます。または、

* オンプレミスの Active Directory と Azure AD 内のデバイス間で、[Hybrid Azure AD Join](concept-azure-ad-join-hybrid.md) を作成できます。 

## <a name="learn"></a>学習する

始める前に、[デバイス ID 管理の概要](overview.md)について理解しておいてください。

### <a name="benefits"></a>メリット

デバイスに Azure AD ID を指定することの主なメリットは次のとおりです。

* 生産性の向上 – Azure AD では、ユーザーがオンプレミスとクラウドのリソースに[シームレスなサインオン (SSO)](./azuread-join-sso.md) を実行でき、これにより、どこにいても生産性を高めることができます。

* セキュリティの強化 – Azure AD デバイスを使用すると、デバイスまたはユーザーの ID に基づいて、リソースに[条件付きアクセス ポリシー](../conditional-access/require-managed-devices.md)を適用できます。 条件付きアクセス ポリシーでは、[Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) を使用した追加の保護を提供できます。 デバイスを Azure AD に参加させることは、[パスワードレス認証](../authentication/concept-authentication-passwordless.md)戦略を使用してセキュリティを強化するための前提条件です。

* ユーザー エクスペリエンスの向上 – Azure AD のデバイス ID を使用すると、ユーザーが個人のデバイスと会社のデバイスの両方から組織のクラウドベースのリソースに簡単にアクセスできます。 管理者は、すべての Windows デバイスで統一されたエクスペリエンスを実現するために [Enterprise State Roaming](enterprise-state-roaming-overview.md) を有効にすることができます。

* デプロイと管理の簡素化 – デバイス ID 管理により、[Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)、[一括プロビジョニング](/mem/intune/enrollment/windows-bulk-enroll)、および[セルフサービス: Out-of-box experience (OOBE)](../user-help/user-help-join-device-on-network.md) を使用して、デバイスを Azure AD に取り込むプロセスを簡素化できます。 これらのデバイスは、[Microsoft Intune](/mem/intune/fundamentals/what-is-intune) などのモバイル デバイス管理 (MDM) ツールとそれらの ID を使用して、[Azure portal](https://portal.azure.com/) で管理できます。

### <a name="training-resources"></a>トレーニング リソース

ビデオ:[デバイス コントロールを使用した条件付きアクセス](https://youtu.be/NcONUf-jeS4)

FAQ:「[Azure AD デバイス管理の FAQ](faq.md)」および「[設定とデータのローミングに関する FAQ](enterprise-state-roaming-faqs.md)」 

## <a name="plan-the-deployment-project"></a>デプロイ プロジェクトを計画する

お客様の環境でこのデプロイの戦略を決定するときは、お客様の組織のニーズを考慮してください。

### <a name="engage-the-right-stakeholders"></a>適切な関係者を関わらせる

テクノロジ プロジェクトが失敗した場合、その原因は通常、影響、結果、および責任に対する想定の不一致です。 これらの落とし穴を回避するには、[適切な利害関係者が担当していることを確認](../fundamentals/active-directory-deployment-plans.md)し、プロジェクトにおけるその利害関係者の役割がよく理解されていることを確認します。 

このプランでは、次の利害関係者をリストに追加します。

| Role| 説明 |
| - | - |
| デバイス管理者| 計画が組織のデバイス要件を満たしていることを検証できるデバイス チームの代表。 |
| ネットワーク管理者| ネットワーク要件を満たしていることを確認できるネットワーク チームの代表。 |
| デバイス管理チーム| デバイスのインベントリを管理するチーム。 |
| OS 固有の管理チーム| 特定の OS バージョンをサポートおよび管理するチーム。 たとえば、Mac または iOS に重点を置いたチームがある場合があります。 |

### <a name="plan-communications"></a>連絡を計画する

コミュニケーションは、新しいサービスの成功に必要不可欠です。 ユーザー エクスペリエンスがどのように変わるのか、いつ変わるのか、問題が発生したときにサポートを受ける方法について、ユーザーに事前に連絡します。

### <a name="plan-a-pilot"></a>パイロットを計画する

統合方法の初期構成は、テスト環境で行うか、小さいグループのテスト用デバイスで行うことをお勧めします。 「[パイロットのベスト プラクティス](../fundamentals/active-directory-deployment-plans.md)」を参照してください。

Hybrid Azure AD Join のデプロイは簡単であり、100% がエンド ユーザーの操作を必要としない管理者のタスクです。 [Hybrid Azure AD Join の制御された検証](hybrid-azuread-join-control.md)を、組織全体で同時に有効にする前に実行することもできます。

## <a name="choose-your-integration-methods"></a>統合方法を選択する

組織では、1 つの Azure AD テナントで複数のデバイス統合方法を使用できます。 目標は、Azure AD でデバイスを安全に管理するために適した方法を選択することです。 所有権、デバイスの種類、主要な対象ユーザー、組織のインフラストラクチャなど、この決定を行う多くのパラメーターがあります。

使用する統合方法を決定する際には、次の情報を参考にしてください。

### <a name="decision-tree-for-devices-integration"></a>デバイス統合のデシジョン ツリー

組織が所有するデバイスのオプションを決定するには、このツリーを使用します。 

> [!NOTE]
> この図では、個人のデバイスや持ち込みのデバイス (BYOD) のシナリオは示していません。 この場合は、常に Azure AD の登録になります。

 ![デシジョン ツリー](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>比較表

iOS および Android デバイスは Azure AD 登録のみが可能です。 次の表に、Windows クライアント デバイスに関する大まかな考慮事項を示します。 これを概要として使用し、その後さまざまな統合方法について詳しく説明します。

| 考慮事項 | Azure AD 登録済み| Azure AD 参加| ハイブリッド Azure AD 参加 |
| - | - | - | - |
| **クライアント オペレーティング システム**| | |  |
| Windows 10 デバイス| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png) |
| Windows のダウンレベルのデバイス (Windows 8.1 または Windows 7)| | | ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png) |
|**サインイン オプション**| | |  |
| エンドユーザーのローカル資格情報| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| |  |
| Password| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png) |
| デバイスの PIN| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| |  |
| Windows Hello for Business| | ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png) |
| FIDO 2.0 セキュリティ キー| | ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png) |
| Microsoft Authenticator アプリ (パスワードレス)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png) |
|**主な機能**| | |  |
| クラウド リソースへの SSO| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png) |
| オンプレミスのリソースへの SSO| | ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png) |
| 条件付きアクセス <br> (デバイスは準拠としてマーク済みであることが必要) <br> (MDM による管理が必要)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png) | ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)|![これらの値にチェックマーク。](./media/plan-device-deployment/check.png) |
条件付きアクセス <br>(ハイブリッド Azure AD 参加済みデバイスが必要)| | | ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)
| Windows のログイン画面からのセルフサービス パスワード リセット| | ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png) |
| Windows Hello の PIN のリセット| | ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png) |
| デバイス間での Enterprise State Roaming| | ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Azure AD の登録 

登録されたデバイスは、多くの場合 [Microsoft Intune](/mem/intune/enrollment/device-enrollment) で管理されます。 デバイスは、オペレーティング システムに応じて、さまざまな方法で Intune に登録されます。 

Azure AD 登録済みデバイスでは、持ち込みのデバイス (BYOD) と会社所有のデバイスによるクラウド リソースへの SSO がサポートされています。 リソースへのアクセスは、デバイスとユーザーに適用された Azure AD の[条件付きアクセス ポリシー](../conditional-access/require-managed-devices.md)に基づいています。

### <a name="registering-devices"></a>デバイスの登録

登録されたデバイスは、多くの場合 [Microsoft Intune](/mem/intune/enrollment/device-enrollment) で管理されます。 デバイスは、オペレーティング システムに応じて、さまざまな方法で Intune に登録されます。 

BYOD と企業所有のモバイル デバイスは、ポータル サイト アプリをインストールするユーザーによって登録されます。

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

組織に最適なオプションがデバイスの登録である場合は、次のリソースを参照してください。

* この [Azure AD 登録済みデバイス](concept-azure-ad-register.md)の概要。

* このエンドユーザー向けの、[組織のネットワークへの個人のデバイスの登録](../user-help/user-help-register-device-on-network.md)に関するドキュメント。

## <a name="azure-ad-join"></a>Azure AD 参加

Azure AD Join により、Windows を使用するクラウド中心のモデルに移行できます。 デバイス管理の最新化とデバイス関連の IT コストの削減を計画している場合は、これにより優れた基盤が提供されます。 Azure AD Join は、Windows 10 デバイスのみで機能します。 新しいデバイスの最初の選択肢として検討してください。

ただし、オンプレミスのリソースが組織のネットワーク上にある場合、[Azure AD 参加済みデバイスではオンプレミスのリソースへの SSO を実行でき](azuread-join-sso.md)、ファイル、印刷、その他のアプリケーションなどのオンプレミスのサービスに対して認証を行うことができます。

このオプションが組織に最適な場合は、次のリソースを参照してください。

* この [Azure AD 参加済みデバイス](concept-azure-ad-join.md)の概要。

* [Azure AD Join の実装計画](azureadjoin-plan.md)について理解を深める。

### <a name="provisioning-azure-ad-join-to-your-devices"></a>デバイスへの Azure AD Join のプロビジョニング

Azure AD Join をプロビジョニングするには、次の方法があります。

* セルフサービス: [Windows 10 の最初の実行エクスペリエンス](azuread-joined-devices-frx.md)

Windows 10 Professional または Windows 10 Enterprise のいずれかがデバイスにインストールされている場合、既定のエクスペリエンスは、会社所有のデバイスのセットアップ プロセスになります。

* [Windows の Out of Box Experience (OOBE) または Windows の設定から](../user-help/user-help-join-device-on-network.md)

* [Windows Auto pilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [一括登録](/mem/intune/enrollment/windows-bulk-enroll)

慎重に[これらのアプローチを比較](azureadjoin-plan.md)した後に、デプロイ手順を選択してください。

Azure AD Join がデバイスにとって最適なソリューションであり、そのデバイスは既に別の状態になっている可能性があると判断する場合があります。 アップグレードに関する考慮事項を次に示します。

| 現在のデバイスの状態| 目的のデバイスの状態| 操作方法 |
| - | - | - |
| オンプレミスのドメイン参加済み| Azure AD Join| Azure AD に参加する前に、オンプレミスのドメインからデバイスを切断する |
| Hybrid Azure AD Join| Azure AD Join| Azure AD に参加する前に、オンプレミスのドメインと Azure AD からデバイスを切断する |
| Azure AD 登録済み| Azure AD Join| Azure AD に参加する前にデバイスの登録を解除する |


## <a name="hybrid-azure-ad-join"></a>ハイブリッド Azure AD 参加

オンプレミスの Active Directory 環境があるときに、Active Directory ドメイン参加済みコンピューターを Azure AD に参加させたい場合は、Hybrid Azure AD Join を使用してこれを実現できます。 これは、Windows の最新のデバイスと Windows のダウンレベルのデバイスの両方を含む、[さまざまな種類の Windows デバイス](hybrid-azuread-join-plan.md)をサポートしています。

ほとんどの組織には、ドメイン参加済みデバイスが既にあり、グループ ポリシーまたは System Center Configuration Manager (SCCM) を使用して管理されています。 そのような場合は、Hybrid Azure AD Join を、既存の投資を活用しながら利点の利用を開始するように構成することをお勧めします。

Hybrid Azure AD Join が組織に最適なオプションの場合は、次のリソースを参照してください。

* この [Hybrid Azure AD 参加済みデバイス](concept-azure-ad-join-hybrid.md)の概要。

* [Hybrid Azure AD Join の実装](hybrid-azuread-join-plan.md)計画について理解を深める。

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>デバイスへの Hybrid Azure AD Join のプロビジョニング

[ID インフラストラクチャを確認します](hybrid-azuread-join-plan.md)。 Azure AD Connect には、Hybrid Azure AD Join を構成するためのウィザードが用意されています。

* [フェデレーション ドメイン](hybrid-azuread-join-federated-domains.md)

* [マネージド ドメイン](hybrid-azuread-join-managed-domains.md)

必要なバージョンの Azure AD Connect をインストールすることができない場合は、[Hybrid Azure AD Join を手動で構成する方法](hybrid-azuread-join-manual.md)に関するページを参照してください。 

> [!NOTE] 
> オンプレミスのドメインに参加している Windows 10 デバイスは、既定で Hybrid Azure AD 参加済みになるように、Azure AD への自動参加を試行します。 これは、適切な環境を設定した場合にのみ成功します。 

Hybrid Azure AD Join がデバイスにとって最適なソリューションであり、そのデバイスは既に別の状態になっている可能性があると判断する場合があります。 アップグレードに関する考慮事項を次に示します。

| 現在のデバイスの状態| 目的のデバイスの状態| 操作方法 |
| - | - | - |
| オンプレミスのドメイン参加| Hybrid Azure AD Join| Azure AD 接続または AD FS を使用して Azure に参加する |
| オンプレミスのワークグループ参加済みまたは新規| Hybrid Azure AD Join| [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) でサポートされています。 それ以外の場合、デバイスは Hybrid Azure AD Join の前にオンプレミスのドメイン参加済みである必要があります |
| Azure AD 参加済み| Hybrid Azure AD Join| Azure AD から切断します。これにより、オンプレミスのワークグループまたは新しい状態になります。 |
| Azure AD に登録済み| Hybrid Azure AD Join| Windows のバージョンによって異なります。 [これらの考慮事項を参照してください](hybrid-azuread-join-plan.md)。 |

## <a name="manage-your-devices"></a>デバイスを管理する

デバイスを Azure AD に登録または参加させたら、デバイス ID を管理するための中央の場所として [Azure portal](https://portal.azure.com/) を使用します。 Azure Active Directory デバイスのページでは、次のことができます。

* [デバイス設定を構成する](device-management-azure-portal.md#configure-device-settings)
* Windows デバイスを管理するには、ローカル管理者である必要があります。 [Azure AD は、Azure AD 参加済みデバイスのこのメンバーシップを更新](assign-local-admin.md)して、デバイス マネージャーの役割を持つデバイスを、参加済みのすべてのデバイスに管理者として自動的に追加します。

[古いデバイスを管理する](manage-stale-devices.md)ことで環境をクリーンな状態に保って、現在のデバイスの管理に関するリソースに集中するようにしてください。

* [デバイス関連の監査ログを確認する](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>サポートされているデバイス管理ツール

管理者は、追加のデバイス管理ツールを使用して、これらの登録済みと参加済みのデバイスをセキュリティで保護し、さらに制御することができます。 これらのツールでは、ストレージの暗号化、パスワードの複雑さ、ソフトウェアのインストール、およびソフトウェアの更新を必要とするなど、組織に必要な構成を適用する手段が提供されます。 

統合デバイスでサポートされているプラットフォームとサポートされていないプラットフォームを確認します。

| デバイス管理ツール| Azure AD 登録済み| Azure AD 参加| ハイブリッド Azure AD 参加|
| - | - | - | - |
| [モバイル デバイス管理 (MDM)](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>例:Microsoft Intune| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)|  |
| [Microsoft Intune および Microsoft Endpoint Configuration Manager との共同管理](/mem/configmgr/comanage/overview) <br>(Windows 10 以降)| | ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)| ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)|  |
| [グループ ポリシー](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>(Windows のみ)| | | ![これらの値にチェックマーク。](./media/plan-device-deployment/check.png)|  |



 登録済みの iOS または Android デバイスのデバイス管理の有無にかかわらず、[Microsoft Intune のモバイル アプリケーション管理 (MAM)](/mem/intune/apps/app-management) を検討することをお勧めします。

 また、管理者は、組織内の Windows オペレーティング システムをホストする[仮想デスクトップ インフラストラクチャ (VDI) プラットフォームをデプロイ](howto-device-identity-virtual-desktop-infrastructure.md)して、管理を効率化し、リソースの統合と一元化によってコストを削減することもできます。 

### <a name="troubleshoot-device-identities"></a>デバイス ID のトラブルシューティング

* [dsregcmd コマンドを使用したデバイスのトラブルシューティング](troubleshoot-device-dsregcmd.md)

* [Azure Active Directory の Enterprise State Roaming の設定のトラブルシューティング](enterprise-state-roaming-troubleshooting.md)

ドメイン参加済み Windows デバイスのハイブリッド Azure AD 参加を行うときに問題が発生した場合は、以下を参照してください。

* [最新の Windows デバイスのハイブリッド Azure AD 参加のトラブルシューティング](troubleshoot-hybrid-join-windows-current.md)

* [ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加のトラブルシューティング](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>次のステップ

* [Azure AD 参加の実装を計画する](azureadjoin-plan.md)
* [ハイブリッド Azure AD 参加の実装を計画する](hybrid-azuread-join-plan.md)
* [デバイス ID を管理する](device-management-azure-portal.md)