---
title: Azure Stack Edge に関する Azure セキュリティ ベースライン
description: Azure Stack Edge セキュリティ ベースラインにより、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 460fd66ed4651248639334caa55eb8facbce866d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452375"
---
# <a name="azure-security-baseline-for-azure-stack-edge"></a>Azure Stack Edge に関する Azure セキュリティ ベースライン

このセキュリティ ベースラインによって、[Azure セキュリティ ベンチマーク バージョン 2.0](../security/benchmarks/overview.md) のガイダンスが Microsoft Azure Stack Edge に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、Azure セキュリティ ベンチマークおよび Azure Stack Edge に適用できる関連ガイダンスによって定義されている **セキュリティ制御** でグループ化されています。 Azure Stack Edge に適用できない **制御** は、除外されています。

Azure Stack Edge を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Stack Edge セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-controls-v2-network-security.md)」を参照してください。*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: 内部トラフィック用のセキュリティを実装する

**ガイダンス**:お客様は、Microsoft が提供する物理的な Azure Stack Edge デバイスを内部アクセス用のプライベート ネットワークに配置し、そのセキュリティ保護をさらに強化できます。 たとえば、Azure Stack Edge デバイスには、お客様の内部ネットワークを介してアクセスでき、お客様が構成した IP が必要です。 また、デバイスのユーザー インターフェイスにアクセスするためのアクセス パスワードは、お客様が選択します。 

内部トラフィックは、次の方法でさらに保護されます。

- Azure Stack Edge デバイスの Azure portal および SDK の管理には、トランスポート層セキュリティ (TLS) バージョン 1.2 が必要です。

- デバイスへのすべてのクライアント アクセスは、既定のセキュリティ保護プロトコルとして標準の TLS 1.2 を使用するローカル Web UI を通して行われます。

- お客様が Azure サブスクリプションで作成した Azure Stack Edge サービスに参加できるのは、承認された Azure Stack Edge Pro デバイスのみです。

追加情報については、参照先のリンクをご覧ください。
 
- [Azure Stack Edge Pro GPU デバイスにアクセスする Windows クライアント上で TLS 1.2 を構成する](azure-stack-edge-j-series-configure-tls-settings.md)

- [クイックスタート - GPU 搭載の Azure Stack Edge Pro を使い始める](azure-stack-edge-gpu-quickstart.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: プライベート ネットワークをまとめて接続する

**ガイダンス**:お客様は、オンプレミスのプライベート ネットワークから Azure ネットワークに Azure Stack Edge デバイスを接続するために、ポイント対サイト仮想プライベート ネットワーク (VPN) を選択できます。 VPN を使用すると、お客様のデバイスから Azure へのトランスポート層セキュリティ経由でのデータの移動に対し、2 番目の暗号化レイヤーが提供されます。 

お客様は、Azure portal または Azure PowerShell を使用して、Azure Stack Edge デバイスでの仮想プライベート ネットワークを構成できます。

- [Azure PowerShell を使用して Azure Stack Edge Pro R および Azure Stack Edge Mini R 用の Azure VPN を構成する](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Azure Stack Edge Pro GPU デバイスにアクセスする Windows クライアント上で TLS 1.2 を構成する](azure-stack-edge-j-series-configure-tls-settings.md)

- [チュートリアル:Azure Stack Edge Pro R 用の証明書を構成する](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Azure サービスへのプライベート ネットワーク アクセスを確立する

**ガイダンス**:お客様は、オンプレミスのプライベート ネットワークから Azure ネットワークに Azure Stack Edge デバイスを接続するために、ポイント対サイト仮想プライベート ネットワーク (VPN) を選択できます。 VPN を使用すると、お客様のデバイスから Azure へのトランスポート層セキュリティ経由でのデータの移動に対し、2 番目の暗号化レイヤーが提供されます。 

- [Azure PowerShell を使用して Azure Stack Edge Pro R および Azure Stack Edge Mini R 用の Azure VPN を構成する](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Azure Stack Edge Pro GPU デバイスにアクセスする Windows クライアント上で TLS 1.2 を構成する](azure-stack-edge-j-series-configure-tls-settings.md)

- [チュートリアル:Azure Stack Edge Pro R 用の証明書を構成する](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: 外部ネットワーク攻撃からアプリケーションやサービスを保護する

**ガイダンス**:Azure Stack Edge デバイスにより、標準の Windows Server ネットワーク保護機能が組み込まれ、お客様はそれを構成できません。

お客様は、高度な分散型サービス拒否 (DDoS) 保護を備えたファイアウォールなどのネットワーク仮想アプライアンスを使用して、Azure Stack Edge デバイスに接続されているプライベート ネットワークを、外部の攻撃から保護することを選択できます。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: 侵入検出および侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**:Azure Stack Edge デバイスによって使用されるエンドポイントはすべて、Microsoft によって管理されます。 オンプレミスのシステムに展開する追加の制御については、お客様が責任を負うものとします。

Azure Stack Edge デバイスでは、独自の侵入検出機能を使用してサービスが保護されます。 

- [Azure Stack Edge のセキュリティについて理解する](azure-stack-edge-security.md)

- [Azure Stack Edge のポートに関する情報](azure-stack-edge-gpu-system-requirements.md)

- [ハードウェアおよびソフトウェアの侵入検出ログを取得する](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="identity-management"></a>ID 管理

*詳細については、[Azure セキュリティ ベンチマークの「ID 管理](../security/benchmarks/security-controls-v2-identity-management.md).* 」を参照してください。

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2:アプリケーション ID を安全かつ自動的に管理する

**ガイダンス**:すべての Azure Stack Edge デバイスには、Azure Active Directory (Azure AD) において、システム割り当てのマネージド ID が自動的に設定されます。 現在、マネージド ID は Azure Stack Edge でホストされる仮想マシンのクラウド管理に使用されます。

Azure Stack Edge デバイスは、ローカル アクセスに対してロックされた状態で起動されます。 ローカル デバイス管理者アカウントについては、ローカル Web ユーザー インターフェイスまたは PowerShell インターフェイスを使用してデバイスに接続し、強力なパスワードを設定する必要があります。 デバイス管理者の資格情報は、Azure Key Vault などの安全な場所に保管して管理し、組織の基準に従って管理者パスワードをローテーションします。

- [パスワードを使用して Azure Stack Edge デバイスを保護する](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3:アプリケーションのアクセスに Azure AD シングル サインオン (SSO) を使用する

**ガイダンス**:Azure Stack Edge エンドポイント デバイスについては、シングル サインオンはサポートされていません。 ただし、標準の Azure Active Directory (Azure AD) ベースのシングル サインオンを有効にして、Azure クラウド リソースへのアクセスをセキュリティで保護することができます。

- [Azure AD でのアプリケーションの SSO を理解する](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4:すべての Azure Active Directory ベースのアクセスに強力な認証制御を使用する

**ガイダンス**:多要素認証は強力な認証制御ですが、Azure Stack Edge サービス ユーザー向けのオプトイン機能です。 Azure portal での Azure Stack Edge デバイスのエッジ管理など、サポートされているシナリオに利用できます。 Azure Stack Edge デバイスへのローカル アクセスには多要素認証がサポートされていないことに注意してください。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5:アカウントの異常を監視してアラートを出す

**ガイダンス**:Azure Monitor を有効にして、Azure Stack Edge サービスに関するデバイスまたはコンテナーのログを収集します。 デバイスの Kubernetes クラスターで複数のコンピューティング アプリケーションを実行しているものなど、コンテナーを監視します。

さらに、監査ログが含まれるサポート パッケージを、Azure Stack Edge デバイスのローカル Web ユーザー インターフェイスで収集できます。 サポート パッケージは、Azure portal では利用できないことに注意してください。
 
- [Azure Stack Edge Pro デバイスで Azure Monitor を有効にする](azure-stack-edge-gpu-enable-azure-monitor.md) 

- [サポート パッケージを収集する](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6:条件に基づいて Azure リソースへのアクセスを制限する

**ガイダンス**:Azure Active Directory (Azure AD) 条件付きアクセスは、Azure Stack Edge サービスに対する認証のためのオプトイン機能です。 Azure Stack Edge サービスは、さまざまな地理的な場所にある Azure Stack Edge Pro デバイスを管理できる Azure portal のリソースです。 

- [条件付きアクセスとは](../active-directory/conditional-access/overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7:意図しない資格情報の公開を排除する

**ガイダンス**:ベスト プラクティスに従って、次のような資格情報を保護します。

- Azure の Azure Stack Edge リソースでデバイスをアクティブにするために使用されるアクティブ化キー。
- Azure Stack Edge デバイスにアクセスするためのサインイン資格情報。
- Azure Stack Edge デバイスの復旧を容易にできるキー ファイル。
- チャネル暗号化キー

承認されていないユーザーからのストレージ アカウントの保護に役立つように、定期的にストレージ アカウント キーをローテーションし、その後同期します。

- [Azure Stack Edge Pro のセキュリティとデータ保護](azure-stack-edge-security.md)

- [ストレージ キーの同期](azure-stack-edge-manage-shares.md#sync-storage-keys)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="privileged-access"></a>特権アクセス

*詳細については、[Azure セキュリティ ベンチマークの「特権アクセス](../security/benchmarks/security-controls-v2-privileged-access.md)」を参照してください。*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2:ビジネス クリティカルなシステムへの管理アクセスを制限する

**ガイダンス**:Azure Stack Edge ソリューションには、ビジネス クリティカルなデバイスへのアクセスを制限するための強力なアクセス制御を備えたコンポーネントがいくつかあります。 組織でデバイスを構成して管理するには、マイクロソフトエンタープライズ契約 (EA)、またはクラウド ソリューション プロバイダー (CSP)、または Microsoft Azure スポンサー プランのサブスクリプションが必要です。 

Azure Stack Edge サービスは、Azure でホストされた管理サービスとしての Azure セキュリティ機能によって保護されます。 リソースの暗号化キーは、任意のソフトウェア開発キット管理操作のデバイス プロパティで取得できますが、暗号化キーを表示できるのは、Resource Graph API のための適切なアクセス許可がある場合だけです。

Azure Stack Edge Pro デバイスは、データをローカルで処理し、それを Azure に送信することによってデータを変換するのに役立つオンプレミスのデバイスです。 お客様のデバイスは...

- Azure Stack Edge サービスにアクセスするには、アクティブ化キーが必要です。
- デバイスのパスワードによって常に保護されています。
- セキュア ブートが有効になっています。

- ロックダウンされたデバイスです。 デバイスのベースボード管理コントローラー (BMC) と BIOS は、パスワードで保護されています。 BIOS は、制限されたユーザー アクセスによって保護されています。
- Windows Defender Device Guard が実行されます。 Device Guard を使用すると、コード整合性ポリシーで定義した信頼されたアプリケーションのみを実行できます。

ベスト プラクティスに従って、Azure Stack Edge へのアクセスに使用されるすべての資格情報とシークレットを保護します。 

- [パスワードのベスト プラクティス](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3:ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure Stack Edge には、デバイスを構成できる "EdgeUser" という名前のユーザーがあります。 また、デバイスでのローカル Azure Resource Manager 機能用に、"EdgeArmUser" という Azure Resource Manager ユーザーもあります。 

ベスト プラクティスに従って、次のものを保護する必要があります。

- オンプレミス デバイスへのアクセスに使用される資格情報

- SMB 共有の資格情報。

- NFS 共有を使用するように構成されているクライアント システムへのアクセス。

- BLOB REST API を使用するときにローカル ストレージ アカウントにアクセスするために使用されるローカル ストレージ アカウント キー。

追加情報については、参照リンクを参照してください。

- [Azure Stack Edge デバイスのセキュリティに関する情報](azure-stack-edge-security.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6:特権アクセス ワークステーションを使用する

**ガイダンス**:セキュリティで保護されて分離されたワークステーションは、管理者、開発者、重要なサービス オペレーターのような機密性の高い役割のセキュリティにとって非常に重要です。 管理タスクには高度にセキュリティ保護されたユーザー ワークステーションを使用します。Azure Bastion は、使用しても、しなくてもかまいません。 Azure Active Directory (Azure AD)、Microsoft Defender Advanced Threat Protection (ATP)、Microsoft Intune を使用して、管理タスクのためにセキュリティで保護されたマネージド ユーザー ワークステーションを配置します。 

セキュリティで保護されたワークステーションを一元管理して、強力な認証、ソフトウェアとハードウェアのベースライン、制限された論理アクセスとネットワーク アクセスなどのセキュリティで保護された構成を実施できます。

- [特権アクセス ワークステーションを理解する](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [特権アクセス ワークステーションを展開する](/security/compass/privileged-access-deployment)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-controls-v2-data-protection.md)」を参照してください。*

### <a name="dp-2-protect-sensitive-data"></a>DP-2:機密データを保護する

**ガイダンス**:Azure Stack Edge では、すべての対話データが、承認されたユーザーだけがこのデータにアクセスできる機密情報として扱われます。 ベスト プラクティスに従って、Azure Stack Edge サービスへのアクセスに使用される資格情報を保護する必要があります。

- [Azure Stack Edge Pro のセキュリティとデータ保護](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4:転送中の機密情報を暗号化する

**ガイダンス**:Azure Stack Edge では、フライト中のデータにセキュリティ保護されたチャネルが使用されます。 次のとおりです。

- デバイスと Azure クラウドの間を移動するデータには、標準の TLS 1.2 が使用されます。 TLS 1.1 以前へのフォールバックはありません。 TLS 1.2 がサポートされていない場合は、エージェント通信がブロックされます。 Azure portal とソフトウェア開発キット (SDK) の管理にも、TLS 1.2 が必要です。

- クライアントがブラウザーのローカル Web ユーザー インターフェイス経由でデバイスにアクセスする場合は、標準の TLS 1.2 が既定のセキュリティで保護されたプロトコルとして使用されます

ベスト プラクティスとして、TLS 1.2 を使用するようにブラウザーを構成することをお勧めします。 データ サーバーからデータをコピーするときは、暗号化付き SMB 3.0 を使用してそれを保護します。

- [フライト中のデータを保護するためのベスト プラクティス](azure-stack-edge-security.md#protect-data-in-flight)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="asset-management"></a>アセット管理

*詳細については、[Azure セキュリティ ベンチマークの「アセット管理](../security/benchmarks/security-controls-v2-asset-management.md)」を参照してください。*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1:セキュリティ チームが資産のリスクを確実に可視化できるようにする

**ガイダンス**:セキュリティ チームに Azure テナントとサブスクリプションのセキュリティ閲覧者アクセス許可を付与して、セキュリティ チームが Azure Security Center を使用してセキュリティ上のリスクを監視できるようにします。 

セキュリティ チームの責任がどのように構造化されているかによって、セキュリティ リスクの監視は中央のセキュリティ チームまたはローカル チームの責任になります。 ただし、セキュリティ分析情報とリスクは、常に組織内で一元的に集計する必要があります。 

セキュリティ閲覧者のアクセス許可は、テナント全体 (ルート管理グループ) に幅広く適用することも、管理グループまたは特定のサブスクリプションにスコープ指定することもできます。 

ワークロードとサービスを可視化するには、追加のアクセス許可が必要になる場合があることに注意してください。 

- [セキュリティ閲覧者ロールの概要](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理グループの概要](../governance/management-groups/overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:許可されているユーザー (たとえば "EdgeArmUser") だけが、ローカル Azure Resource Manager を介して Azure Stack Edge デバイス API にアクセスできます。 ユーザー アカウントのパスワードは、Azure portal でのみ管理できます。 

- [Azure Resource Manager パスワードを設定する](azure-stack-edge-j-series-set-azure-resource-manager-password.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6:コンピューティング リソースで承認済みのアプリケーションのみを使用する

**ガイダンス**:ローカル環境に作成された仮想マシン上で、独自のアプリケーションを実行することができます。 Stack Edge デバイス上にローカル コンピューティング仮想マシンを作成するには、PowerShell スクリプトを使用します。 ローカル仮想マシンでは、信頼されたアプリケーションのみを実行することを強くお勧めします。 

- [Windows 環境で PowerShell スクリプトの実行を制御する方法](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-threat-detection"></a>ログと脅威検出

*詳細については、[Azure セキュリティ ベンチマークの「ログと脅威検出](../security/benchmarks/security-controls-v2-logging-threat-detection.md)」を参照してください。*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1:Azure リソースの脅威検出を有効にする

**ガイダンス**:Azure Stack Edge には、脅威検出機能はありません。 ただし、お客様は、オフラインでの脅威の検出と分析のために、サポート パッケージで監査ログを収集できます。 

- [Azure Stack Edge デバイスのサポート パッケージを収集する](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3:Azure ネットワーク アクティビティのログ記録を有効にする

**ガイダンス**:ダウンロード可能なサポート パッケージの一部として、Azure Stack Edge のネットワーク監査ログが有効になります。 これらのログを解析して、Azure Stack Edge デバイスのほぼリアルタイムの監視を実装できます。

- [Azure Stack Edge によるサポート パッケージの収集](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4:Azure リソースのログ記録を有効にする

**ガイダンス**:現在、ログを使用したリアルタイム監視は Azure Stack Edge についてはサポートされていません。 サポート パッケージを収集する機能が用意されており、それを使用すると、ファイアウォール、ソフトウェア、ハードウェアの侵入や、Azure Stack Edge Pro デバイスのシステム イベント ログなど、それに含まれるさまざまなログを分析することができます。 ソフトウェア侵入ログまたは既定のファイアウォール ログは、受信と送信のトラフィックについて収集されることに注意してください。

- [Azure Stack Edge のサポート パッケージを収集する](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5:セキュリティ ログの管理と分析を一元化する

**ガイダンス**:現在、ログを使用したリアルタイム監視は Azure Stack Edge についてはサポートされていません。 ただし、サポート パッケージを収集し、それに含まれているさまざまなログを分析できます。  サポート パッケージは圧縮されており、ユーザーが選択したパスにダウンロードされます。 パッケージを解凍して、それに含まれるシステム ログ ファイルを表示します。 また、これらのログを、分析のために、セキュリティ情報イベント管理ツールや、別の集中保管場所に送信することもできます。

- [Azure Stack Edge のサポート パッケージを収集する](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="lt-6-configure-log-storage-retention"></a>LT-6:ログの保持期間を構成する

**ガイダンス**:Azure Stack Edge デバイスでのログ ストレージ保持期間を変更することはできません。 必要に応じて、古いログが削除されます。 ログを長期間保持する必要がある場合は、一定の間隔でデバイスからサポート パッケージを収集できます。 

- [Azure Stack Edge のサポート パッケージを収集する](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7:承認された時刻同期ソースを使用する

**ガイダンス**:Azure Stack Edge では、Microsoft のネットワーク タイム プロバイダー サーバーである time.windows.com が使用されます。  また、お客様は、Azure Stack Edge を使用して、選択したネットワーク タイム プロトコル サーバーを構成することもできます。

- [Azure Stack Edge デバイスの時刻設定を構成する](azure-stack-edge-gpu-deploy-set-up-device-update-time.md#configure-time)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-controls-v2-incident-response.md)」を参照してください。*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 準備 – インシデント対応プロセスを Azure 用に更新する

**ガイダンス**:組織のインシデント対応計画に含まれるプロセスに関して、次のことを確認します。 

- セキュリティ インシデントに対応する

- Azure クラウド用に更新されている
 
- 即応できるよう定期的に訓練されている

エンタープライズ環境全体で標準化されたインシデント対応プロセスを使用して、セキュリティを実装することをお勧めします。

- [企業環境全体にセキュリティを実装する](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [インシデント対応のリファレンス ガイド](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 準備 – インシデント通知をセットアップする

**ガイダンス**:Azure Security Center でセキュリティ インシデントの連絡先情報を設定します。 この連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 また、インシデント対応のニーズに応じて、異なる Azure サービスでインシデント アラートと通知をカスタマイズするオプションもあります。 

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 検出と分析 – 高品質のアラートに基づいてインシデントを作成する 

**ガイダンス**:高品質のアラートを作成し、それらの品質を測定するためのプロセスがあることを確認します。 これにより、過去のインシデントから教訓を学び、アナリストに対するアラートに優先順位を付けることができるので、擬陽性のアラートの処理に時間を無駄にすることがありません。 過去のインシデントからの経験、検証されたコミュニティ ソース、およびログやさまざまなアラート ソースとの関連付けによってアラートを生成してクリーンアップするように設計されたツールに基づいて、高品質のアラートを構築できます。 

Azure Security Center では、多数の Azure 資産について高品質のアラートが提供されます。 Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。 Azure Sentinel を使用して高度な警告ルールを作成し、調査のための自動インシデントを生成します。 

エクスポート機能を使用して Security Center のアラートと推奨事項を Azure Sentinel にエクスポートし、Azure リソースへのリスクを特定します。 継続的なセキュリティのために、アラートと推奨事項を自動的にエクスポートするプロセスを作成することをお勧めします。

- [エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 検出と分析 – インシデントを調査する

**ガイダンス**:アナリストが潜在的なインシデントを調査するときに、さまざまなデータ ソースのクエリを実行して使用し、発生したアクティビティの完全なビューを構築できるようにします。 キル チェーン全体で潜在的な攻撃者のアクティビティを追跡して死角を回避するには、さまざまな種類のログを収集する必要があります。  また、履歴の参照のために、分析情報と学習がキャプチャされていることを確認します。  

調査のためのデータ ソースには、スコープ内のサービスおよび実行中のシステムから既に収集されている一元化されたログ ソースが含まれますが、次のものも含まれます。

- ネットワーク データ - ネットワーク セキュリティ グループのフロー ログ、Azure Network Watcher、Azure Monitor を使用して、ネットワーク フロー ログやその他の分析情報をキャプチャします。 

- 実行中のシステムのスナップショット: 

    - Azure 仮想マシンのスナップショット機能を使用して、実行中のシステムのディスクのスナップショットを作成します。 

    - オペレーティング システムのネイティブ メモリ ダンプ機能を選択して、実行中のシステムのメモリのスナップショットを作成します。

    - Azure サービスのスナップショット機能またはサードパーティのソフトウェア機能を使用して、実行中のシステムのスナップショットを作成します。

Azure Sentinel により、事実上すべてのログソースに対して広範な Data Analytics と、インシデントのライフサイクル全体を管理するためのケース管理ポータルが提供されます。 調査中のインテリジェンス情報を、追跡とレポートのためにインシデントに関連付けることができます。 

- [Windows マシンのディスクのスナップショットを作成する](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux マシンのディスクのスナップショットを作成する](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure サポートの診断情報とメモリ ダンプ コレクション](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel でインシデントを調査します](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 検出と分析 – インシデントの優先順位を付ける

**ガイダンス**:アラートの重要度と資産の機密性に基づいて、最初に注目するインシデントについてのコンテキストをアナリストに提供します。 Azure Security Center からの各アラートに割り当てられた重要度を使用して、最初に調査する必要があるアラートの優先順位を付けます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、タグを使用してリソースをマークし、Azure リソース (特に、機密データを処理するもの) を識別して分類するための命名システムを作成します。  インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 包含、根絶、復旧 – インシデントの処理を自動化する

**ガイダンス**:手動による反復タスクを自動化して、応答時間を短縮し、アナリストの負担を軽減します。 手動タスクの実行には時間がかかり、各インシデントの速度が低下し、アナリストが処理できるインシデントの数が減少します。 手動タスクではアナリストの疲労も増加します。これにより、遅延が発生する人的エラーのリスクが増加し、複雑なタスクに効果的に焦点を当てるアナリストの能力が低下します。 Azure Security Center と Azure Sentinel のワークフロー自動化機能を使用して、自動的にアクションをトリガーしたり、プレイブックを実行して受信したセキュリティ アラートに応答したりします。 プレイブックにより、通知の送信、アカウントの無効化、問題のあるネットワークの特定などのアクションが実行されます。 

- [Security Center でワークフロー自動化を構成する](../security-center/workflow-automation.md)

- [Azure Security Center で脅威への自動対応を設定する](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel で脅威への自動対応を設定します](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="posture-and-vulnerability-management"></a>体制と脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「体制と脆弱性の管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)」を参照してください。*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: コンピューティング リソースにセキュリティで保護された構成を確立する

**ガイダンス**:Azure Stack Edge には、お客様によって作成されるローカル仮想マシンのためのセキュリティで保護された構成を作成するサポートが用意されています。 Microsoft が提供するガイドラインを使用して、ローカル仮想マシンの作成中にセキュリティ ベースラインを確立することを強くお勧めします。

- [セキュリティ コンプライアンス ツールキットに含まれるセキュリティ ベースラインをダウンロードする](/windows/security/threat-protection/windows-security-baselines)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: コンピューティング リソースに対するセキュリティで保護された構成を維持する

**ガイダンス**:Azure Stack Edge には、お客様によって作成されたローカル仮想マシンのためのセキュリティで保護された構成を保持するサポートは用意されていません。 お客様には、セキュリティ コンプライアンス ツールキット (SCT) を使用して、コンピューティング リソースのセキュリティで保護された構成を保持することを強くお勧めします。

Azure Stack Edge によって管理されるホスト オペレーティング システムと仮想マシンについては、セキュリティ構成が維持されます。 

- [Windows のセキュリティ ベースライン](/windows/security/threat-protection/windows-security-baselines#using-security-baselines-in-your-organization)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: カスタム オペレーティング システムとコンテナーのイメージを安全に格納する

**ガイダンス**:Azure Stack Edge によって管理されるホスト オペレーティング システムと仮想マシンは、安全に格納されます。 

お客様は、独自の仮想マシンとコンテナー イメージを使用でき、それらについてのセキュリティで保護された管理の責任を負います。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: ソフトウェアの脆弱性を迅速かつ自動的に修復する

**ガイダンス**:Azure Stack Edge により、定期的に修正プログラムの更新が行われ、そのような更新プログラムが脆弱性の修復に利用できるようになるとお客様にアラートが提供されます。 最新のパッチを使用してデバイスと (お客様によって作成された) 仮想マシンを最新の状態に保つのは、お客様の責任です。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 定期的に攻撃シミュレーションを実施する

**ガイダンス**:必要に応じて、Azure リソースの侵入テストまたはレッド チーム アクティビティを実施し、セキュリティに関するすべての重大な調査結果が確実に修復されるようにします。
お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

- [Azure での侵入テスト](../security/fundamentals/pen-testing.md)

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="endpoint-security"></a>エンドポイント セキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「エンドポイントのセキュリティ](../security/benchmarks/security-controls-v2-endpoint-security.md)」を参照してください。*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1:エンドポイントでの検出と対応 (EDR) を使用する

**ガイダンス**:Azure Stack Edge には、エンドポイントの検出と応答 (EDR) の直接的なサポートはありません。 ただし、サポート パッケージを収集し、監査ログを取得することはできます。 これらのログを分析して、異常なアクティビティを確認できます。 

- [Azure Stack Edge デバイスのサポート パッケージを収集する](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2:一元管理された最新のマルウェア対策ソフトウェアを使用する

**ガイダンス**:Windows Defender Application Control (WDAC) と、事前に定義されたアプリケーションとスクリプトの実行のみを許可する厳密なコード整合性 (CI) ポリシーが、Azure Stack Edge によって使用されます。 Windows Defender のリアルタイム保護 (RTP) のマルウェア対策も有効になります。 お客様は、Azure Stack Edge デバイスでローカルに実行するために作成したコンピューティング VM で、マルウェア対策を実行できます。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="backup-and-recovery"></a>バックアップと回復

*詳細については、[Azure セキュリティ ベンチマークの「バックアップと回復」](../security/benchmarks/security-controls-v2-backup-recovery.md)を参照してください。*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1:定期的な自動バックアップを保証する

**ガイダンス**:Azure Stack Edge デバイスを定期的にバックアップすることが推奨され、Azure Backup や他のサードパーティ製データ保護ソリューションを使用してそれを実行し、デバイスに展開された仮想マシンに含まれるデータを保護できます。 

Cohesity、Commvault、Veritas などのサードパーティ製データ保護ソリューションを使用して、ローカル環境の SMB 共有または NFS 共有のデータのバックアップ ソリューションを提供できます。 

追加情報については、参照先のリンクをご覧ください。

- [デバイス障害への準備](azure-stack-edge-gpu-prepare-device-failure.md)

- [VM 上のファイルとフォルダーの保護](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="br-2-encrypt-backup-data"></a>BR-2:バックアップ データを暗号化する

**ガイダンス**:攻撃に対してバックアップが保護されていることを確認します。 これには、機密性が失われるのを防ぐためにバックアップを暗号化することも含まれます。  詳細については、選択したバックアップ ソリューションを参照してください。

- [Edge ローカル共有のデータを保護する](azure-stack-edge-gpu-prepare-device-failure.md#protect-data-in-edge-local-shares)

- [仮想マシン内のファイルとフォルダーを保護する](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:バックアップのデータ復元を定期的に実行します。 

- [Azure Stack Edge の復旧手順](azure-stack-edge-gpu-recover-device-failure.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4:キー紛失のリスクを軽減する

**ガイダンス**:カスタマー マネージド キーが含まれるすべての Azure Stack Edge バックアップが、組織のベスト プラクティスに従って保護されていることを確認します。 Azure Stack Edge デバイスは、Azure ストレージ アカウントに関連付けられ、Azure 内のデータの宛先リポジトリとして使用されます。 

Azure ストレージ アカウントへのアクセスは、そのストレージ アカウントに関連付けられた Azure サブスクリプションと 2 つの 512 ビット ストレージ アクセス キーによって制御されます。 Azure Stack Edge デバイスがストレージ アカウントにアクセスするときは、いずれかのアクセス キーが認証に使用されます。 他のキーは、定期的なキー ローテーションのために予約されています。 キーのローテーションにセキュリティに関するベスト プラクティスが使用されていることを確認します。

- [Azure ストレージ アカウント内の Azure Stack Edge デバイスのデータを保護する](azure-stack-edge-pro-r-security.md#protect-data-in-storage-accounts)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="governance-and-strategy"></a>ガバナンスと戦略

*詳細については、[Azure セキュリティ ベンチマークの「ガバナンスと戦略](../security/benchmarks/security-controls-v2-governance-strategy.md)」を参照してください。*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 資産の管理とデータ保護の戦略を定義する 

**ガイダンス**:システムとデータを継続的に監視および保護するための明確な戦略が文書化および伝達されるようにします。 ビジネスに不可欠なデータとシステムの検出、評価、保護、監視の優先順位を決定します。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   ビジネス リスクに応じたデータ分類標準

-   リスクと資産のインベントリに対するセキュリティ組織の可視性 

-   Azure サービスを使用するためのセキュリティ組織の承認 

-   ライフサイクルを通じた資産のセキュリティ

-   組織のデータ分類に従った必要なアクセス制御戦略

-   Azure ネイティブおよびサードパーティのデータ保護機能の使用

-   転送中および保存中のユース ケースのデータ暗号化要件

-   適切な暗号化標準

詳細については、次のリファレンスを参照してください。
- [Azure セキュリティ アーキテクチャに関する推奨事項 - ストレージ、データ、暗号化](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure のセキュリティの基礎 - Azure のデータ セキュリティ、暗号化、ストレージ](../security/fundamentals/encryption-overview.md)

- [クラウド導入フレームワーク - Azure のデータ セキュリティと暗号化のベスト プラクティス](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure セキュリティ ベンチマーク - アセット管理](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure セキュリティ ベンチマーク - データ保護](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 企業のセグメント化戦略を定義する 

**ガイダンス**:ID、ネットワーク、アプリケーション、サブスクリプション、管理グループ、その他のコントロールを利用し、アセットへのアクセスをセグメント化する企業規模の戦略を確立します。

セキュリティ分離の必要性と、互いと通信し、データにアクセスする必要があるシステムの日常的操作を有効にするという必要性のバランスを慎重に取ります。

セグメント化戦略は、ネットワーク セキュリティ、ID とアクセス モデル、アプリケーション アクセス許可とアクセス モデル、人的プロセスの制御など、あらゆるコントロールの種類を対象として確実に一貫性をもって実装します。

- [Azure のセグメント化戦略に関するガイド (動画)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure のセグメント化戦略に関するガイド (ドキュメント)](/security/compass/governance#enterprise-segmentation-strategy)

- [ネットワークのセグメント化を企業のセグメント化戦略に合わせる](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: セキュリティ態勢管理の戦略を定義する

**ガイダンス**:個々の資産とそれらがホストされている環境に対するリスクを継続的に測定し、軽減します。 高い価値を持つ資産と、攻撃に晒される可能性の高い部分 (公開されたアプリケーション、ネットワークのイングレス ポイントとエグレス ポイント、ユーザーと管理者のエンドポイントなど) を優先します。

- [Azure セキュリティ ベンチマーク - 体制と脆弱性の管理](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 組織の役割、責任、責務を整合させる

**ガイダンス**:セキュリティ組織における役割と責任に関する明確な戦略が文書化されて伝えられるようにします。 セキュリティに関する決定についてわかりやすく説明すること、共有される責任モデルについて全員に教育すること、クラウドをセキュリティで保護するテクノロジについて技術チームに教育することを優先とします。

- [Azure のセキュリティのベスト プラクティス 1 – 人: クラウド セキュリティに関する取り組みについてチームを教育する](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure のセキュリティのベスト プラクティス 2 - 人: クラウド セキュリティ テクノロジについてチームを教育する](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure のセキュリティのベスト プラクティス 3 - プロセス: クラウドのセキュリティに関する意思決定のアカウンタビリティを割り当てる](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: ネットワーク セキュリティ戦略を定義する

**ガイダンス**:組織全体のセキュリティ アクセス制御戦略の一環として、Azure ネットワーク セキュリティ アプローチを確立します。  

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   一元的なネットワーク管理とセキュリティ責任

-   エンタープライズ セグメント化戦略と一致する仮想ネットワーク セグメント化モデル

-   さまざまな脅威と攻撃のシナリオにおける修復戦略

-   インターネット エッジとイングレスおよびエグレス戦略

-   クラウドとオンプレミスのハイブリッド相互依存戦略

-   最新のネットワーク セキュリティ成果物 (例: ネットワーク図、参照ネットワーク アーキテクチャ)

詳細については、次のリファレンスを参照してください。
- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure セキュリティ ベンチマーク - ネットワーク セキュリティ](../security/benchmarks/index.yml)

- [Azure のネットワーク セキュリティの概要](../security/fundamentals/network-overview.md)

- [エンタープライズ ネットワーク アーキテクチャ戦略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID と特権アクセス戦略を定義する

**ガイダンス**:組織全体のセキュリティ アクセス制御戦略の一環として、Azure の ID と特権アクセス アプローチを確立します。  

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   一元化された ID と認証システム、および他の内部および外部 ID システムとのその相互接続

-   さまざまなユース ケースおよび条件における強力な認証方法

-   高い特権を持つユーザーの保護

-   異常なユーザー アクティビティの監視と処理  

-   ユーザー ID とアクセスの確認と調整のプロセス

詳細については、次のリファレンスを参照してください。

- [Azure セキュリティ ベンチマーク - ID 管理](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure セキュリティ ベンチマーク - 特権アクセス](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 管理のセキュリティの概要](../security/fundamentals/identity-management-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: ログ記録と脅威対応戦略を定義する

**ガイダンス**:コンプライアンス要件を満たしながら脅威を迅速に検出して修復するための、ログ記録と脅威対応戦略を確立します。 アナリストが、統合や手動による手順ではなく、脅威の対応に集中できるように、質の高いアラートとシームレスなエクスペリエンスを提供することを優先してください。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

-   セキュリティ運用 (SecOps) 組織の役割と責任 

-   NIST または他の業界フレームワークと一致する、明確に定義されたインシデント対応プロセス 

-   脅威の検出、インシデント対応、コンプライアンスのニーズに対応するためのログのキャプチャと保持

-   SIEM、Azure のネイティブ機能、その他のソースを使用した、脅威に関する情報の一元的な可視化と関連付け 

-   顧客、サプライヤー、および関心を持つパブリック パーティに関するコミュニケーションと通知の計画

-   ログ記録と脅威の検出、フォレンジクス、攻撃の修復と根絶など、インシデント処理に対する Azure ネイティブおよびサードパーティのプラットフォームの使用

-   インシデントとインシデント発生後のアクティビティを処理するためのプロセス (教訓や証拠の保持など)

詳細については、次のリファレンスを参照してください。

- [Azure セキュリティ ベンチマーク - ログと脅威検出](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure セキュリティ ベンチマーク - インシデント対応](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure のセキュリティのベスト プラクティス 4 - プロセス: クラウドのインシデント対応プロセスを更新する](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 導入フレームワーク、ログ、およびレポートの決定ガイド](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](../security/benchmarks/overview.md)」を参照してください。
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する
