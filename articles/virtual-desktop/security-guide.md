---
title: Windows Virtual Desktop のセキュリティに関するベスト プラクティス - Azure
description: Windows Virtual Desktop 環境のセキュリティを確保するためのベスト プラクティス。
author: heidilohr
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0ddbd4b798d37498af92cec40af6a80a88115fab
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014895"
---
# <a name="security-best-practices"></a>セキュリティの運用方法

Windows Virtual Desktop は、組織の安全を維持するための多くのセキュリティ機能を備えた、管理された仮想デスクトップ サービスです。 Windows Virtual Desktop のデプロイでは、Microsoft がお客様に代わってサービスの一部を管理します。 このサービスには、リモート デスクトップにどこからでもアクセスできるようにした場合に生じるリスクを軽減する、逆接続などの多くの高度なセキュリティ機能が組み込まれています。

この記事では、顧客の Windows Virtual Desktop のデプロイのセキュリティを維持するために、管理者として実行できる追加の手順について説明します。

## <a name="security-responsibilities"></a>セキュリティの責任

クラウド サービスが、従来のオンプレミスの仮想デスクトップ インフラストラクチャ (VDI) と異なる点は、セキュリティの責任の扱い方です。 たとえば、従来のオンプレミス VDI では、セキュリティのあらゆる側面の責任を顧客が負うことになります。 しかし、ほとんどのクラウド サービスでは、これらの責任は顧客と会社との間で共有されます。

Windows Virtual Desktop を使用する場合、一部のコンポーネントはご使用環境に合わせて既にセキュリティで保護されていますが、他の領域については、組織のセキュリティ ニーズに適合するように自分で構成する必要があるということを理解しておくことが重要です。

Windows Virtual Desktop のデプロイで、お客様の責任となるセキュリティ ニーズを次に示します。

| セキュリティ ニーズ | お客様の責任であるか |
|---------------|:-------------------------:|
|ID|はい|
|ユーザー デバイス (モバイルおよび PC)|はい|
|アプリのセキュリティ|はい|
|セッション ホスト OS|はい|
|デプロイの構成|はい|
|ネットワーク制御|はい|
|仮想化コントロール プレーン|いいえ|
|物理ホスト|いいえ|
|物理ネットワーク|いいえ|
|物理データセンター|いいえ|

お客様の責任でないセキュリティ ニーズは、Microsoft が対処します。

## <a name="azure-security-best-practices"></a>Azure のセキュリティに関するベスト プラクティス

Windows Virtual Desktop は、Azure のサービスです。 Windows Virtual Desktop のデプロイの安全性を最大限に高めるには、必ず周囲の Azure インフラストラクチャと管理プレーンもセキュリティで保護する必要があります。 インフラストラクチャをセキュリティで保護するには、より大規模な Azure エコシステムに Windows Virtual Desktop をどのように適合させるかを検討してください。 Azure エコシステムの詳細については、「[Azure セキュリティのベスト プラクティスとパターン](../security/fundamentals/best-practices-and-patterns.md)」を参照してください。

このセクションでは、Azure エコシステムをセキュリティで保護するためのベスト プラクティスについて説明します。

### <a name="enable-azure-security-center"></a>Azure Security Center を有効にする

サブスクリプション、仮想マシン、キー コンテナー、およびストレージ アカウントで Azure Security Center Standard を有効にすることをお勧めします。

Azure Security Center Standard を使用すると、次のことを実行できます。

* 脆弱性を管理する。
* PCI などの一般的なフレームワークを使ってコンプライアンスを評価する。
* 環境の全体的なセキュリティを強化する。

詳細については、「[Azure サブスクリプションでの Security Center Standard の利用開始](../security-center/security-center-get-started.md)」を参照してください。

### <a name="improve-your-secure-score"></a>セキュア スコアを向上させる

セキュア スコアは、全体的なセキュリティを向上させるための推奨事項とベスト プラクティスに関する助言を提供します。 これらの推奨事項には、どれが最も重要かを選択するのに役立つように優先順位が付けられています。また、クイック修正のオプションを使用すると、潜在的な脆弱性に迅速に対処できます。 また、これらの推奨事項は時間の経過とともに更新されるため、環境のセキュリティを維持するための最良の方法について、常に最新の情報を得ることができます。 詳細については、「[Azure Security Center でセキュア スコアを向上する](../security-center/secure-score-security-controls.md)」を参照してください。

## <a name="windows-virtual-desktop-security-best-practices"></a>Windows Virtual Desktop のセキュリティに関するベスト プラクティス

Windows Virtual Desktop には、多くの組み込みのセキュリティ コントロールがあります。 このセクションでは、ユーザーとデータの安全性を維持するために使用できるセキュリティ コントロールについて説明します。

### <a name="require-multi-factor-authentication"></a>多要素認証が必要です

Windows Virtual Desktop のすべてのユーザーと管理者に多要素認証を要求すると、デプロイ全体のセキュリティが向上します。 詳細については、[Windows Virtual Desktop 向け Azure AD Multi-Factor Authentication の有効化](set-up-mfa.md)に関する記事を参照してください。

### <a name="enable-conditional-access"></a>条件付きアクセスを有効にする

[条件付きアクセス](../active-directory/conditional-access/overview.md)を有効にすると、Windows Virtual Desktop 環境へのアクセス権をユーザーに付与する前にリスクを管理できます。 アクセス権を付与するユーザーを決定するときは、そのユーザーが何者か、どのようにサインインするか、どのデバイスが使用されているかについても検討することをお勧めします。

### <a name="collect-audit-logs"></a>監査ログの収集

監査ログの収集を有効にすると、Windows Virtual Desktop に関連するユーザーと管理者のアクティビティを表示できます。 主要な監査ログの例を次に示します。

-   [Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md)
-   [Azure Active Directory アクティビティ ログ](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [セッション ホスト](../azure-monitor/agents/agent-windows.md)
-   [Windows Virtual Desktop 診断ログ](../virtual-desktop/diagnostics-log-analytics.md)
-   [Key Vault ログ](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>RemoteApp を使用する

デプロイ モデルを選択するときは、仮想デスクトップ全体、または選択したアプリケーションのみへのアクセス権をリモート ユーザーに提供できます。 リモート アプリケーション (つまり RemoteApp) は、ユーザーが仮想デスクトップでアプリを操作するときにシームレスなエクスペリエンスを提供します。 RemoteApp は、アプリケーションで公開されているリモート マシンのサブセットのみをユーザーが操作できるようにすることで、リスクを軽減します。

### <a name="monitor-usage-with-azure-monitor"></a>Azure Monitor を使用して使用状況を監視する

[Azure Monitor](https://azure.microsoft.com/services/monitor/) を使用して、Windows Virtual Desktop サービスの使用状況と可用性を監視します。 サービスに影響するイベントが発生したときにすぐに通知を受け取れるように、Windows Virtual Desktop サービス用の[サービス正常性アラート](../service-health/alerts-activity-log-service-notifications-portal.md)を作成することを検討してください。

## <a name="session-host-security-best-practices"></a>セッション ホストのセキュリティに関するベスト プラクティス

セッション ホストは、Azure サブスクリプションと仮想ネットワーク内で実行される仮想マシンです。 Windows Virtual Desktop デプロイの全体的なセキュリティは、セッション ホストに配置するセキュリティ コントロールで決まります。 このセクションでは、セッション ホストのセキュリティを維持するためのベスト プラクティスについて説明します。

### <a name="enable-screen-capture-protection-preview"></a>画面キャプチャ保護を有効にする (プレビュー)

画面キャプチャの保護機能を使用することで、機密情報がクライアント エンドポイントでキャプチャされないようにします。 この機能を有効にすると、スクリーンショットおよびスクリーン共有でリモート コンテンツが自動的にブロックまたは非表示になります。 また、画面内の内容を継続的にキャプチャしている可能性がある悪意のあるソフトウェアからも非表示になります。 この機能の使用中は、エンドポイントへリモート コンテンツがコピーされることを防ぐために、クリップボードのリダイレクトを無効にすることをお勧めします。

このポリシーは、レジストリ キーを構成することによって、ホスト レベルで適用されます。 このポリシーを有効にするには、PowerShell を開き、次のコマンドレットを実行して **fEnableScreenCaptureProtection** レジストリ キーを設定します。

```powershell
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableScreenCaptureProtection /t REG_DWORD /d 1
```

この新機能をテストするには:

- 検証環境でホスト プールがプロビジョニングされていることを確認します。
- Windows デスクトップ クライアント バージョン 1.2.1526 以降をダウンロードしてインストールしていることを確認します。

>[!NOTE]
>プレビュー期間中、この機能は Windows 10 エンドポイントからの完全なデスクトップ接続のみでサポートされます。

### <a name="enable-endpoint-protection"></a>Endpoint Protection を有効にする

既知の悪意のあるソフトウェアからデプロイを保護するには、すべてのセッション ホストで Endpoint Protection を有効にすることをお勧めします。 Windows Defender ウイルス対策、またはサードパーティ製のプログラムを使用できます。 詳細については、[VDI 環境への Windows Defender ウイルス対策の展開ガイド](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus)に関するページを参照してください。

FSLogix などのプロファイル ソリューションや、VHD ファイルをマウントするその他のソリューションでは、VHD ファイルの拡張子を除外することをお勧めします。

### <a name="install-an-endpoint-detection-and-response-product"></a>エンドポイントの検出と応答製品をインストールする

エンドポイントの検出と応答 (EDR) 製品をインストールして、高度な検出と応答の機能を提供することをお勧めします。 [Azure Security Center](../security-center/security-center-services.md) が有効になったサーバー オペレーティング システムでは、EDR 製品をインストールすると Defender ATP がデプロイされます。 クライアント オペレーティング システムの場合は、[Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) またはサードパーティ製の製品をこれらのエンドポイントにデプロイできます。

### <a name="enable-threat-and-vulnerability-management-assessments"></a>脅威と脆弱性の管理の評価を有効にする

オペレーティング システムとアプリケーションに存在するソフトウェアの脆弱性を特定することは、環境のセキュリティを維持するために不可欠です。 Azure Security Center は、サーバー オペレーティング システムの脆弱性評価によって問題のある箇所を特定するのに役立ちます。 また、デスクトップ オペレーティング システムの脅威と脆弱性の管理を提供する Defender ATP を使用することもできます。 Azure Security Center と Defender ATP を使用することをお勧めしていますが、必要であれば、サードパーティ製品を使用することもできます。

### <a name="patch-software-vulnerabilities-in-your-environment"></a>環境内のソフトウェアの脆弱性を修正する

脆弱性を特定したら、それを修正する必要があります。 これは、実行中のオペレーティング システム、その内部にデプロイされているアプリケーション、新しいマシンの作成元であるイメージを含む仮想環境にも適用されます。 ベンダーの修正プログラムに関する通知情報に従い、修正プログラムを適時に適用します。 新しくデプロイされたマシンのセキュリティを可能な限り確保するために、基本イメージに修正プログラムを毎月適用することをお勧めします。

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>最大非アクティブ時間と切断のポリシーを確立する

非アクティブなときにユーザーをサインアウトすると、リソースが保持され、承認されていないユーザーがアクセスするのを防止できます。 タイムアウトではユーザーの生産性とリソース使用量のバランスを取ることをお勧めします。 ステートレス アプリケーションを操作するユーザーの場合は、マシンをオフにしてリソースを保持する、より積極的なポリシーを検討してください。 シミュレーションや CAD のレンダリングなど、ユーザーがアイドル状態の場合に実行を継続する、実行時間の長いアプリケーションを切断すると、ユーザーの作業が中断され、場合によってはコンピューターの再起動が必要になることもあります。

### <a name="set-up-screen-locks-for-idle-sessions"></a>アイドル セッションの画面ロックを設定する

アイドル時間中にマシンの画面をロックし、ロックを解除するためには認証を要求するように Windows Virtual Desktop を構成することによって、望ましくないシステム アクセスを防止することができます。

### <a name="establish-tiered-admin-access"></a>階層化された管理アクセスを確立する

仮想デスクトップへの管理者アクセスをユーザーに付与しないことをお勧めします。 ソフトウェア パッケージが必要な場合は、Microsoft Endpoint Manager などの構成管理ユーティリティを使って使用可能にすることをお勧めします。 マルチセッション環境では、ユーザーによるソフトウェアの直接インストールを許可しないことをお勧めします。

### <a name="consider-which-users-should-access-which-resources"></a>どのユーザーがどのリソースにアクセスする必要があるかを検討する

セッション ホストは、既存のデスクトップ デプロイの拡張と考えてください。 ネットワーク リソースへのアクセスは、ネットワークのセグメント化やフィルター処理などを使用して、環境内の他のデスクトップと同じように制御することをお勧めします。 既定では、セッション ホストはインターネット上のあらゆるリソースに接続できます。 Azure Firewall、ネットワーク仮想アプライアンス、またはプロキシーの使用など、トラフィックを制限できるいくつかの方法があります。 トラフィックを制限する必要がある場合は、Windows Virtual Desktop が正常に機能できるように、必ず適切な規則を追加してください。

### <a name="manage-office-pro-plus-security"></a>Office Pro Plus のセキュリティを管理する

セッション ホストをセキュリティで保護することに加え、その内部で実行されているアプリケーションをセキュリティで保護することも重要です。 Office Pro Plus は、セッション ホストにデプロイされる最も一般的なアプリケーションの 1 つです。 Office のデプロイでのセキュリティを向上させるには、Microsoft 365 Apps for enterprise に[セキュリティ ポリシー アドバイザー](/DeployOffice/overview-of-security-policy-advisor)を使用することをお勧めします。 このツールは、セキュリティを強化するために、デプロイに適用できるポリシーを識別します。 セキュリティ ポリシー アドバイザーは、セキュリティと生産性への影響に基づいたポリシーの推奨も行います。

### <a name="other-security-tips-for-session-hosts"></a>セッション ホストに対するその他のセキュリティ ヒント

オペレーティング システムの機能を制限することで、セッション ホストのセキュリティを強化することができます。 実行できるいくつかの操作を次に示します。

- リモート デスクトップ セッションで、ドライブ、プリンター、および USB デバイスをユーザーのローカル デバイスにリダイレクトすることによって、デバイスのリダイレクトを制御します。 セキュリティ要件を評価し、これらの機能を無効にする必要があるかどうかを確認することをお勧めします。

- ローカルおよびリモート ドライブのマッピングを非表示にして、Windows エクスプローラーのアクセスを制限します。 これにより、ユーザーが、システム構成とユーザーに関する不要な情報を検出するのを防止できます。

- 環境内のセッション ホストへの直接 RDP アクセスは避けてください。 管理またはトラブルシューティングのために直接 RDP アクセスが必要な場合は、[Just-In-Time](../security-center/security-center-just-in-time.md) アクセスを有効にして、セッション ホストでの潜在的な攻撃対象領域を制限します。

- ユーザーがローカルおよびリモートのファイル システムにアクセスする場合は、制限付きアクセス許可を付与します。 ローカルおよびリモートのファイル システムが最小限の特権でアクセス制御リストを使用するようにすることで、アクセス許可を制限できます。 こうすると、ユーザーは必要なものにのみアクセスでき、重要なリソースを変更または削除することはできません。

- 不要なソフトウェアがセッション ホストで実行されないようにします。 セッション ホストのセキュリティを強化するために App Locker を有効にできます。これにより、許可したアプリのみをホストで実行できるようになります。

## <a name="windows-virtual-desktop-support-for-trusted-launch"></a>Windows Virtual Desktop でのトラステッド起動のサポート

トラステッド起動とは、ルートキット、ブート キット、カーネルレベルのマルウェアなどの攻撃ベクトルによる "スタックの最下部" の脅威から保護することを目的として強化されたセキュリティ機能を提供するものであり、Gen2 Azure VM で利用できます。 トラステッド起動の強化されたセキュリティ機能を以下に示します。これらはすべて、Windows Virtual Desktop でサポートされています。 トラステッド起動の詳細については、「[Azure 仮想マシン用のトラステッド起動 (プレビュー)](../virtual-machines/trusted-launch.md)」を参照してください。

### <a name="secure-boot"></a>セキュア ブート

セキュア ブートとは、プラットフォーム ファームウェアでサポートされるモードであり、マルウェアベースのルートキットおよびブート キットからファームウェアを保護するためのものです。 このモードでは、署名された OS とドライバーでのみコンピューターを起動することができます。 

### <a name="monitor-boot-integrity-using-remote-attestation"></a>リモート構成証明を使用してブート整合性を監視する

リモート構成証明は、使用している VM の正常性を確認するための優れた方法です。 リモート構成証明によって、メジャーブート レコードが存在すること、本物であること、仮想トラステッドプラットフォームモジュール (vTPM) から取得されたものであることが確認されます。 正常性チェックとして、プラットフォームが正しく起動されたという確実性が暗号化されて提供されます。 

### <a name="vtpm"></a>vTPM

vTPM は、ハードウェアのトラステッド プラットフォーム モジュール (TPM) の仮想化バージョンであり、VM ごとに TPM の仮想インスタンスがあります。 vTPM により、VM のブート チェーン全体 (UEFI、OS、システム、ドライバー) の整合性測定を実行することで、リモート構成証明が有効になります。 

ご利用の VM 上では、vTPM を有効にしてリモート構成証明を使用することをお勧めします。 vTPM を有効にすると、フルボリューム暗号化を行って保存データを保護する BitLocker 機能を有効にすることもできます。 vTPM を使用するいずれの機能でも、特定の VM にシークレットがバインドされます。 プールされたシナリオでユーザーが Windows Virtual Desktop サービスに接続した場合に、ユーザーをホスト プール内の任意の VM にリダイレクトすることができます。 機能の設計方法によっては、これが影響を及ぼす可能性があります。

>[!NOTE]
>FSLogix プロファイル データを格納している特定のディスクを暗号化する場合は、BitLocker を使用しないでください。

### <a name="virtualization-based-security"></a>仮想化ベースのセキュリティ

仮想化ベースのセキュリティ (VBS) では、ハイパーバイザーを使用して、OS からアクセスできないセキュリティで保護されたメモリ領域が作成され、分離されます。 ハイパーバイザーで保護されたコード整合性 (HVCI) でも Windows Defender Credential Guard でも、VBS を使用して、脆弱性からの保護を強化できます。 

#### <a name="hypervisor-protected-code-integrity"></a>ハイパーバイザーで保護されたコード整合性

HVCI は、VBS を使用することで、悪意のあるまたは検証されていないコードの注入や実行から Windows カーネルモード プロセスを保護する、システムの強力な軽減策です。

#### <a name="windows-defender-credential-guard"></a>Windows Defender Credential Guard

Windows Defender Credential Guard では、VBS を使用してシークレットを分離および保護することで、特権のあるシステム ソフトウェアからのみそれらにアクセスできるようにすることができます。 これにより、それらのシークレットへの不正アクセスや、Pass-the-Hash 攻撃などの資格情報の盗難攻撃が防止されます。

### <a name="deploy-trusted-launch-in-your-windows-virtual-desktop-environment"></a>Windows Virtual Desktop 環境でのトラステッド起動をデプロイする

現在、Windows Virtual Desktop では、ホスト プールのセットアップ プロセス中におけるトラステッド起動の自動構成はサポートされていません。 Windows Virtual Desktop 環境でトラステッド起動を使用するには、トラステッド起動を通常どおりにデプロイしてから、目的のホスト プールに手動で仮想マシンを追加する必要があります。

## <a name="nested-virtualization"></a>入れ子になった仮想化

次のオペレーティング システムでは、入れ子になった仮想化を Windows Virtual Desktop 上で実行することがサポートされています。

- Windows Server 2016
- Windows Server 2019
- Windows 10 Enterprise
- Windows 10 Enterprise マルチセッション

## <a name="next-steps"></a>次のステップ

多要素認証を有効にする方法については、[多要素認証の設定](set-up-mfa.md)に関する記事を参照してください。
