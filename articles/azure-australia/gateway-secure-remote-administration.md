---
title: Azure Australia におけるゲートウェイの安全なリモート管理
description: オーストラリア リージョン内でオーストラリア政府のポリシー、規制、および法律に固有の要件を満たす安全なリモート管理を構成するにあたってのガイダンス。
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 1e4c4712312faf2274a4a0737c4fc1f7ce39f98e
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68824198"
---
# <a name="secure-remote-administration-of-your-gateway-in-azure-australia"></a>Azure Australia におけるゲートウェイの安全なリモート管理

あらゆるシステムの可用性と整合性にとって、管理作業が安全に実施され、制御されていることが不可欠です。 管理作業は、安全なデバイスから安全な接続を介して実施する必要があります。また、その背後には強力な認証と承認のプロセスが存在しなければなりません。 安全なリモート管理は、承認された操作だけが、承認された管理者によってのみ実施されることを保証するものです。

この記事では、Azure にホストされた、インターネットでアクセスできるシステムを対象に、Australian Cyber Security Centre (ACSC) のコンシューマー ガイダンスと ACSC の Information Security Manual (ISM) の意図に沿った安全なリモート管理機能の導入について取り上げます。

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Australian Cyber Security Centre (ACSC) の要件

連邦政府システムの全体的なセキュリティ要件は ISM に規定されています。 安全な管理に向けて連邦政府機関を支援するために、ACSC では、「[ACSC Protect: 安全な管理](https://www.acsc.gov.au/publications/protect/secure-administration.htm)」を発行しました。

このドキュメントには、安全な管理の重要性が詳しく書かれているほか、安全な管理環境を導入するための 1 つの方法が提案されています。 このドキュメントでは、安全な管理のソリューションの構成要素について、次のように示されています。

|要素   |説明   |
|---|---|
|特権アクセス制御   |特権アカウントへのアクセスを制御することは、特権アカウントの悪用を防止する基本的なセキュリティ制御です。 アクセスの制御手法には、"最小限の特権" と "必要な特権" の概念に加え、サービス アカウントとスタッフの異動を管理するためのプロセスと手順が含まれます。   |
|多要素認証   |ユーザー名とパスフレーズ以外にも、物理トークンやスマートカードなどの追加の認証ファクターを導入することで、重要な資産の保護を強化することができます。 特権アカウントの資格情報が敵対者によって侵害されたとしても、すべての管理操作はまず何らかの形態の多要素認証を経なければならないため、その影響は大幅に軽減されます。|
|特権ワークステーション|既知の安全な環境を管理タスクに使用することで、さらなるセキュリティ制御が導入されるため、ネットワーク侵害のリスクを軽減できます。|
|ログ記録と監査   |セキュリティや管理に関連してワークステーションやサーバー、ネットワーク デバイス、ジャンプ ボックスで発生したイベントの生成、収集、分析を自動化することで、セキュリティ侵害やその試みを検出することができます。 自動化によって組織がより迅速に対応できるようになるため、セキュリティ侵害の影響が軽減されます。|
|ネットワークのセグメント化と分離|ネットワークを論理ゾーン (各種セキュリティ ドメインなど) にセグメント化し、ゾーン間を流れるデータの種類を制限してそれらの論理ネットワークを分離することによって、侵入拡大を抑制することができます。 セグメント化によって、敵対者がさらに別のリソースにアクセスするのを阻止することができます。|
|ジャンプ ボックス|ジャンプ ボックスは、一般に Microsoft のリモート デスクトップ サービスや Secure Shell (SSH) ソフトウェアを利用する堅牢化されたリモート アクセス サーバーです。 すべての管理操作が専用ホストから実行されるという点で、ジャンプ ボックスは、管理者が重要なシステムにアクセスする際の足掛かりとしての役割を果たします。|

この記事では、以上の要素を使用して Azure にデプロイされたシステムの安全な管理を実現する方法を示すリファレンス アーキテクチャを紹介します。

## <a name="architecture"></a>アーキテクチャ

安全な管理機能を実現するためには、連動してまとまりのあるソリューションを形成する複数のコンポーネントが必要があります。 ここで紹介するリファレンス アーキテクチャでは、「[ACSC Protect: 安全な管理](https://www.acsc.gov.au/publications/protect/secure-administration.htm)」に記載されている各要素にコンポーネントがマップされます。

![Azure の安全なリモート管理のアーキテクチャ](media/remote-admin.png)

## <a name="components"></a>コンポーネント

このアーキテクチャは、必要なアクセス許可だけが特権アカウントに与えられること、特権アカウントが安全に識別されること、さらに承認されたデバイスから、制御および監査された安全な通信メカニズムを介してのみ、特権アカウントが管理インターフェイスにアクセスできるように設計されています。

|解決策| コンポーネント|要素|
|---|---|---|
|安全なデバイス |<ul><li>特権ワークステーション</li><li>モバイル デバイス</li><li>Microsoft Intune</li><li>グループ ポリシー</li><li>ジャンプ サーバー (要塞ホスト)</li><li>Just-In-Time (JIT) 管理</li></ul> |<ul><li>特権ワークステーション</li><li>ジャンプ ボックス</li></ul>|
|セキュリティで保護された通信 |<ul><li>Azure portal</li><li>Azure VPN Gateway</li><li>リモート デスクトップ (RD) ゲートウェイ</li><li>ネットワーク セキュリティ グループ (NSG)</li></ul> |<ul><li>ネットワークのセグメント化と分離</li></ul>|
|強力な認証 |<ul><li>ドメイン コントローラー (DC)</li><li>Azure Active Directory (Azure AD)</li><li>ネットワーク ポリシー サーバー (NPS)</li><li>Azure MFA</li></ul> |<ul><li>多要素認証</li></ul> |
|強力な承認 |<ul><li>Identity and Access Management (IAM)</li><li>Privileged Identity Management (PIM)</li><li>条件付きアクセス</li></ul>|<ul><li>特権アクセス制御</li></ul>|
|||

>[!NOTE]
>ログと監査の要素について詳しくは、[ゲートウェイのログ記録、監査、可視性](gateway-log-audit-visibility.md)に関する記事を参照してください。

## <a name="administration-workflow"></a>管理ワークフロー

Azure にデプロイされたシステムの管理は、Azure の構成の管理と、Azure にデプロイされたワークロードの管理という異なる 2 つのカテゴリに分類されます。 Azure の構成は、Azure portal で行われます。一方、ワークロードの管理は、管理メカニズムを通じて行われます。たとえば、リモート デスクトップ プロトコル (RDP) や Secure Shell (SSH) が使用されるほか、PaaS の機能に関しては、SQL Management Studio などのツールが使用されます。

管理のためのアクセスは、「アーキテクチャ」に記載したコンポーネントを含む、複数のステップから成るプロセスで行います。さらに、Azure のワークロードにアクセスするためには、Azure portal および Azure の構成にアクセスする必要があります。

>[!NOTE]
> ここで説明するステップは、Azure のグラフィカル ユーザー インターフェイス (GUI) コンポーネントを使用した一般的なプロセスです。 これらのステップは、PowerShell など、他のインターフェイスを使用して実施することもできます。

### <a name="azure-configuration-and-azure-portal-access"></a>Azure の構成と Azure portal のアクセス

|手順 |説明 |
|---|---|
|特権ワークステーションのサインイン |管理者が管理者資格情報を使用して特権ワークステーションにサインインします。 非管理者アカウントが特権ワークステーションに対して認証されたり、管理者アカウントが非特権ワークステーションに対して認証されたりすることがないよう、グループ ポリシーによって制御されます。 Microsoft Intune は、特権ワークステーションのコンプライアンスを管理して、最新のソフトウェアのパッチやマルウェア対策などのコンプライアンス要件を満たせるようにします。 |
|Azure portal へのサインイン |トランスポート層セキュリティ (TLS) を使用して暗号化された Azure portal に対し、管理者が Web ブラウザーを開いてアクセスし、管理者資格情報を使用してサインインします。 認証要求は Azure Active Directory で直接処理されるか、Active Directory フェデレーション サービス (AD FS) やパススルー認証といった認証メカニズムを使用して処理されます。 |
|Azure MFA |特権アカウントの登録済みのモバイル デバイスに対し、Azure MFA から認証要求が送信されます。 セキュリティ要件への準拠を徹底するため、モバイル デバイスは Intune によって管理されます。 管理者はまず、モバイル デバイスに対して認証を行った後、PIN または生体認証システムを使用して Microsoft Authenticator アプリに対して認証を行う必要があります。その後、試行された認証が Azure MFA に対して承認されます。 |
|条件付きアクセス |条件付きアクセス ポリシーは、認証の試行をチェックして、必要な要件 (接続元の IP アドレス、特権アカウントのグループ メンバーシップ、Intune によってレポートされた特権ワークステーションの管理ステータスとコンプライアンス ステータスなど) が満たされていることを確認します。 |
|Privileged Identity Management (PIM) |これで管理者は Azure portal を使用して、PIM 経由で承認された特権ロールをアクティブ化するか、そのアクティブ化を要求することができます。 特権アカウントに放置された管理特権がないこと、また特権アクセスに対するすべての要求が、管理タスクを遂行するために必要な期間に限定されていることが PIM によって保証されます。 また、PIM では監査用に、すべての要求およびアクティブ化のログが提供されます。 |
|ID 管理とアクセス管理|特権アカウントが安全に識別されてロールがアクティブ化されると、管理者は、ID 管理とアクセス管理を通じてアクセス許可が割り当てられた Azure サブスクリプションとリソースにアクセスできるようになります。|

特権アカウントで Azure portal に管理アクセスできるようにするステップが完了したら、ワークロードへのアクセスを構成し、管理接続を確立することができます。

### <a name="azure-workload-administration"></a>Azure ワークロードの管理

|手順 |説明|
|---|---|
|Just-In-Time (JIT) アクセス|仮想マシンにアクセスするために、管理者が JIT を使用して、RD ゲートウェイの IP アドレスからジャンプ サーバーへの RDP (またはジャンプ サーバーから対象となるワークロード仮想マシンへの RDP あるいは SSH) アクセスを要求します。|
|Azure VPN Gateway|管理者が、その特権ワークステーションから Azure VPN Gateway へのポイント対サイト IPsec VPN 接続を確立します。接続の確立には証明書認証が実行されます。|
|RD ゲートウェイ|リモート デスクトップ接続の構成に指定された RD ゲートウェイがあるジャンプ サーバーへの RDP 接続を管理者が試みます。 RD ゲートウェイには、Azure VPN Gateway 接続経由で到達可能なプライベート IP アドレスが割り当てられています。 要求されたジャンプ サーバーへのアクセスを特権アカウントに承認するかどうかは、RD ゲートウェイ上のポリシーによって制御されます。 RD ゲートウェイは、管理者に資格情報を求め、その認証要求をネットワーク ポリシー サーバー (NPS) に転送します。|
|ネットワーク ポリシー サーバー (NPS)|NPS は、RD ゲートウェイから認証要求を受け取り、Active Directory に対してユーザー名とパスワードを検証した後、Azure Active Directory に要求を送信して Azure MFA 認証要求をトリガーします。|
|Azure MFA|特権アカウントの登録済みのモバイル デバイスに対し、Azure MFA から認証要求が送信されます。 セキュリティ要件への準拠を徹底するため、モバイル デバイスは Intune によって管理されます。 管理者はまず、モバイル デバイスに対して認証を行った後、PIN または生体認証システムを使用して Microsoft Authenticator アプリに対して認証を行う必要があります。その後、試行された認証が Azure MFA に対して承認されます。|
|ジャンプ サーバー|認証に成功すると、RDP 接続は、トランスポート層セキュリティ (TLS) を使用して暗号化された後、暗号化された IPsec トンネルを通じて Azure VPN Gateway に送信され、RD ゲートウェイを介してジャンプ サーバーに送信されます。 管理者は、JIT 要求に指定されたワークロード仮想マシンに対し、ジャンプ サーバーから RDP または SSH で接続することができます。|

## <a name="general-guidance"></a>一般的なガイダンス

この記事に記載されているコンポーネントを導入するにあたっては、次の一般的なガイダンスが適用されます。

* サービスが利用できるリージョンを確認し、すべてのデータが承認された場所内に維持されるようにします。また、保護されたワークロードについては、オーストラリア中部またはオーストラリア中部 2 を最優先のデプロイ先にします。

* 個々のサービスの認定状況については、ドキュメント『*Azure - ACSC 認定レポート - Protected 2018*』を参照してください。このレポートに記載のない関連コンポーネントについては、『*ACSC コンシューマー ガイド - Microsoft Azure at PROTECTED*』に従って自己評価を実施します。

* 必要な認証コンポーネント (Azure AD、ADFS、PTA など) にアクセスするためのネットワーク接続と必要なプロキシ構成を行います。

* Azure Policy を使用して要件への準拠を監視し、徹底します。

* 仮想マシン (特に Active Directory ドメイン コントローラー) が暗号化されたストレージ アカウントに格納されていること、また Azure Disk Encryption が使用されていることを確認します。

* この記事に記載されている技術的な制御を担う、ID と管理特権の堅牢な管理プロセスとガバナンスを作成し、保守します。

|Resource|URL|
|---|---|
|オーストラリアの規制とポリシーのコンプライアンス ドキュメント|[オーストラリアの規制とポリシーのコンプライアンス ドキュメント](https://aka.ms/au-irap)|
|Azure 製品 - オーストラリアのリージョンと非リージョン|[Azure 製品 - オーストラリアのリージョンと非リージョン](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|サイバー セキュリティ インシデントを軽減するための戦略|[サイバー セキュリティ インシデントを軽減するための戦略](https://acsc.gov.au/infosec/mitigationstrategies.htm)|
|ACSC Protect: 安全な管理|[ACSC Protect: 安全な管理](https://acsc.gov.au/publications/protect/secure-administration.htm)|
|方法:ネットワーク ポリシー サーバー (NPS) 拡張機能と Azure AD を使用したリモート デスクトップ ゲートウェイ インフラストラクチャの統合|[NPS と Azure AD を使用した RD ゲートウェイの統合](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-rdg)|

## <a name="component-guidance"></a>コンポーネント ガイダンス

このセクションでは、安全なリモート管理のアーキテクチャ全体における各コンポーネントの目的とその役割について取り上げます。 リファレンス ドキュメント、ガイド、チュートリアルなど参考になるリソースへのリンクも別途掲載しています。

## <a name="secure-devices"></a>安全なデバイス

特権ユーザーが管理の役割を果たすために使用する物理デバイスは、悪意のあるアクターにとって利用価値の高いターゲットです。 物理デバイスのセキュリティと整合性を維持し、悪意のあるソフトウェアの侵入を阻止してセキュリティ侵害を確実に防ぐことが、安全なリモート管理の機能を実現するための重要な要素となっています。 これには、アプリケーションのホワイトリスト登録、アプリケーションへのパッチ適用、アプリケーションのセキュリティ強化、オペレーティング システムへのパッチ適用など、ACSC の「サイバー セキュリティ インシデントを軽減するために不可欠な 8 つの戦略」に規定された高優先度のセキュリティ構成が含まれます。 デバイスの状態を確実に組織の要件に準拠させるためには、こうした機能のインストール、構成、監査、検証、レポートが必要です。

### <a name="privileged-workstation"></a>特権ワークステーション

特権ワークステーションは、管理業務を遂行する目的で使用可能な、セキュリティを強化したマシンであり、アクセスできるのは管理者アカウントだけです。 特権ワークステーションには、実行できるソフトウェアやネットワーク リソースへのアクセス、インターネットへのアクセスを制限するためのポリシーと構成が整備されている必要があります。また、万一デバイスが盗難の被害に遭ったりセキュリティが侵害されたりした場合のために、資格情報が保護されている必要があります。

|リソース|Link|
|---|---|
|特権アクセス ワークステーションのアーキテクチャ概要|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)|
|特権アクセスの保護に関する参考資料|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)|

### <a name="mobile-device"></a>モバイル デバイス

モバイル デバイスは、その携行性とサイズから、不慮の紛失や盗難のリスクがより高く、適切にセキュリティで保護する必要があります。 デバイスへのアクセスのために認証を強制する機能や位置情報サービスを使用した追跡可能性、暗号化機能、リモートからワイプする機能を踏まえると、モバイル デバイスは、通常のデバイスよりも強力な認証要素を備えているといえます。 Azure の追加の認証要素としてモバイル デバイスを使用する際は、電話や携帯ショートメール (SMS) ではなく、PIN または生体認証で Microsoft Authenticator アプリを使用するようにデバイスを構成することをお勧めします。

|リソース|Link|
|---|---|
|Azure AD の認証方法|[https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)|
|Microsoft Authenticator アプリの使い方|[https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app](https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app)|

### <a name="microsoft-intune"></a>Microsoft Intune

Intune は、モバイル デバイスとアプリを管理する Enterprise Mobility + Security のコンポーネントです。 ID とアクセスの制御のための Azure Active Directory や、データ保護のための Azure Information Protection など、他のコンポーネントと密接に統合されます。 Intune は、ワークステーションやモバイル デバイスがリソースにアクセスする際に準拠すべき要件を設定するポリシーに加え、管理デバイスの状態に関する分析情報を入手するためのレポート機能や監査機能を備えています。

|リソース|Link|
|---|---|
|Microsoft Intune のドキュメント|[https://docs.microsoft.com/intune/](https://docs.microsoft.com/intune/)|
|Intune におけるデバイス コンプライアンスの概要|[https://docs.microsoft.com/intune/device-compliance-get-started](https://docs.microsoft.com/intune/device-compliance-get-started)|

### <a name="group-policy"></a>グループ ポリシー

グループ ポリシーは、オペレーティング システムとアプリケーションの構成を制御する目的で使用されます。 システムの認証、承認、監査の設定が、セキュリティ ポリシーによって制御されます。 グループ ポリシーを使用して、特権ワークステーションのセキュリティを強化したり、管理者資格情報を保護したり、非特権アカウントが特権デバイスにアクセスできないよう制限したりします。

|リソース|Link|
|---|---|
|ローカル サインインを許可するグループ ポリシーの設定|[https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally)|

### <a name="jump-server--bastion-host"></a>ジャンプ サーバー (要塞ホスト)

ジャンプ サーバー (要塞ホスト) は管理の中央拠点です。 管理業務を遂行するために必要なツールだけでなく、管理ポートでリソースに接続するために必要なネットワーク アクセスも備わっています。 ジャンプ サーバーは、この記事で取り上げる仮想マシン ワークロードを管理するための中央拠点ですが、SQL などの PaaS (サービスとしてのプラットフォーム) 機能を管理するための承認済み拠点として構成することもできます。 PaaS 機能へのアクセスは、ID 制御とネットワーク制御を使用してサービスごとに制限できます。

|リソース|Link|
|---|---|
|安全な管理用のホストを実装する|[https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts)|

### <a name="just-in-time-jit-access"></a>Just-In-Time (JIT) アクセス

JIT は、ネットワーク セキュリティ グループ (NSG) を使用して、仮想マシン上の管理プロトコル (RDP や SSH など) へのアクセスをブロックする Azure Security Center の機能です。 仮想マシン上でホストされたアプリケーションは、そのまま通常どおり機能しますが、管理アクセス権を取得するためには、それを要求する必要があり、要求したアクセス権は期間限定で付与されます。 すべての要求は、監査用にログに記録されます。

|リソース |Link |
|---|---|
|Just-In-Time (JIT) アクセスの管理|[https://docs.microsoft.com/azure/security-center/security-center-just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|
|Azure の Just-In-Time VM アクセスを自動化する|[https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access](https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access)|

## <a name="secure-communication"></a>安全な通信

管理作業の通信トラフィックには、管理者資格情報など、特に機密性の高い情報が含まれている可能性があるため、それに応じた管理と保護が必要となります。 安全な通信を確保するためには、傍受を防ぐ信頼性の高い暗号化機能と、管理トラフィックを承認済みのエンド ポイントに限定するネットワークのセグメント化と制限が欠かせません。また、システムが侵入を受けた場合にその拡大を抑制する必要があります。

### <a name="azure-portal"></a>Azure portal

Azure portal との通信はトランスポート層セキュリティ (TLS) を使用して暗号化されており、Azure portal の使用は ACSC による認定を受けています。 連邦政府機関は、『*ACSC コンシューマー ガイド*』にある推奨事項に従い、最新バージョンの TLS とサポート対象の暗号アルゴリズムを使用するようにその Web ブラウザーを構成する必要があります。

|リソース |Link |
|---|---|
|Azure の暗号化の概要 - 転送中の暗号化|[https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit](https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit)|

### <a name="azure-vpn-gateway"></a>Azure VPN Gateway

Azure VPN Gateway は、特権ワークステーションから Azure への暗号化された安全な接続を提供します。 Azure VPN Gateway は、安全な IPsec 通信手段として ACSC によって認定されています。 連邦政府機関は、『ACSC コンシューマー ガイド』や『ACSC 認定レポート』などの特定のガイダンスに従って Azure VPN Gateway を構成する必要があります。

|リソース |Link |
|---|---|
|ポイント対サイト接続について|[https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about)|
|Azure VPN Gateway の暗号の詳細|[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto)|
|Azure VPN Gateway の構成|[Azure VPN Gateway の構成](vpn-gateway.md)|

### <a name="remote-desktop-rd-gateway"></a>リモート デスクトップ (RD) ゲートウェイ

RD ゲートウェイは、システムへの RDP 接続を制御、承認するための安全なメカニズムです。 RDP トラフィックをハイパーテキスト転送プロトコル セキュア (HTTPS) にカプセル化し、TLS を使用して暗号化するしくみになっています。 TLS が管理トラフィックのための追加的なセキュリティ レイヤーとなります。

|リソース |Link |
|---|---|
|リモート デスクトップ サービスのアーキテクチャ|[https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)|

### <a name="network-security-groups-nsgs"></a>ネットワーク セキュリティ グループ (NSG)

NSG は、サブネットや仮想マシンに入ってくるネットワーク トラフィックまたはそれらから出て行くネットワーク トラフィックのアクセス制御リスト (ACL) として機能します。 NSG は、ネットワークのセグメント化の働きをし、システム間で許可された通信のフローを制御したり制限したりするメカニズムを備えています。 NSG は、管理プロトコルへのアクセスを許可したり拒否したりするための Just-In-Time (JIT) 管理の核となるコンポーネントです。

|リソース |Link |
|---|---|
|Azure セキュリティ グループの概要|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|方法:仮想ネットワークを計画する|[https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|

## <a name="strong-authentication"></a>強力な認証

特権ユーザーにシステムへのアクセスを許可する前に、そのユーザーを安全に識別することは、安全な管理の重要な構成要素となります。 特権アカウントに関連付けられている資格情報を保護し、悪意のあるアクターが偽装や資格情報の盗用によってシステムにアクセスするのを阻止するメカニズムが備わっていなければなりません。

### <a name="domain-controller-dc"></a>ドメイン コントローラー (DC)

大まかに言えば、DC とは、ドメイン内のすべてのユーザー、コンピューター、グループを含んだ Active Directory データベースのコピーをホストするものです。 DC は、ユーザーとコンピューターの認証を行います。 このアーキテクチャでは、DC が Azure 内の仮想マシンとしてホストされ、ジャンプ サーバーやワークロードの仮想マシンに接続する特権アカウントの認証サービスを提供します。

|リソース |Link |
|---|---|
|Active Directory Domain Services の概要|[https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)|

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Azure AD は、Azure の認証サービスです。 クラウドにおける

ID を格納し、Azure 環境における認証と承認を提供します。 Azure AD は、Azure AD Connect を通じて Active Directory と同期させることができます。また、Active Directory フェデレーション サービス (AD FS) および Azure AD Connect を通じてフェデレーション認証を提供できます。 Azure AD は、安全な管理の核となるコンポーネントです。

|リソース |Link |
|---|---|
|Azure Active Directory のドキュメント|[https://docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory)|
|ハイブリッド ID のドキュメント|[https://docs.microsoft.com/azure/active-directory/hybrid](https://docs.microsoft.com/azure/active-directory/hybrid)|

### <a name="network-policy-server-nps"></a>ネットワーク ポリシー サーバー (NPS)

NPS は、認証とポリシーのためのサーバーであり、高度な認証と承認のプロセスを提供します。 このアーキテクチャでは、NPS サーバーが、RD ゲートウェイの認証要求に Azure MFA 認証を統合する働きをします。 NPS には、Azure AD の Azure MFA との統合をサポートする専用のプラグインが備わっています。

|リソース |Link |
|---|---|
|ネットワーク ポリシー サーバーのドキュメント|[https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)|

### <a name="azure-mfa"></a>Azure MFA

Azure MFA は、Azure Active Directory 内で提供される認証サービスです。Azure portal などのクラウド リソースへのアクセスに関して、ユーザー名とパスワードの域を超えた認証要求が可能となります。 Azure MFA はさまざまな認証方法をサポートしていますが、このアーキテクチャでは、Microsoft Authenticator アプリを利用してセキュリティの強化と NPS との統合を実現しています。

|リソース |Link |
|---|---|
|動作のしくみ: Azure Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)|
|方法:クラウドベースの Azure Multi-Factor Authentication をデプロイする|[https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)|

## <a name="strong-authorisation"></a>強力な承認

特権アカウントが安全に識別されたら、リソースへのアクセスを許可することができます。 特定のアカウントに割り当てられた特権は、承認によって制御、管理されます。 強力な承認プロセスは、管理特権を制限するという、ACSC のサイバー セキュリティ インシデントを軽減するために不可欠な 8 つの戦略に適合しています。

### <a name="identity-and-access-management"></a>ID 管理とアクセス管理

Azure 内で特権操作を実行するためのアクセス権は、そのアカウントに割り当てられたロールに基づきます。 Azure には、特定のタスクを実施する固有のアクセス許可を与えられたロールが豊富に、かつ細かな粒度で備わっています。 サブスクリプション レベルやリソース グループ レベルなど、さまざまなレベルでこれらのロールを付与することができます。 ロールの割り当てとアクセス許可の管理は、Azure Active Directory のアカウントとグループに基づいて行われ、Azure 内でアクセスの制御 (IAM) を通じて管理されます。

|リソース |Link |
|---|---|
|Azure のロール ベースのアクセス制御|[https://docs.microsoft.com/azure/role-based-access-control](https://docs.microsoft.com/azure/role-based-access-control)|
|ロールの定義について|[https://docs.microsoft.com/azure/role-based-access-control/role-definitions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions)|

### <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

PIM は、特権ロールへのアクセスを制御する Azure Active Directory コンポーネントです。 特権アカウントには永続的な特権アクセスは必要なく、代わりに、特権が必要な作業を遂行するために一定期間、特権アクセスを要求する機能を与えることができます。 PIM は、特権アクセスの保守と制限に関して追加の制御を実現すると共に、特権使用のインスタンスを追跡するためのログ記録と監査機能を備えています。

|リソース |Link |
|---|---|
|Privileged Identity Management (PIM) のドキュメント|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)|
|PIM の使用を開始する|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started)|

### <a name="conditional-access"></a>条件付きアクセス

条件付きアクセスは、条件に基づいてリソースへのアクセスを許可または拒否する Azure Active Directory のコンポーネントです。 ネットワークの場所や、デバイスの種類、準拠状態、グループ メンバーシップなどを条件にできます。 管理者アカウントのグループ メンバーシップと Intune を通じて MFA、デバイス管理、コンプライアンスを強化するために、条件付きアクセスが使用されます。

|リソース |Link |
|---|---|
|条件付きアクセスのドキュメント|[https://docs.microsoft.com/azure/active-directory/conditional-access](https://docs.microsoft.com/azure/active-directory/conditional-access)|
|方法:条件付きアクセスを使用してクラウド アプリへのアクセスにマネージド デバイスを要求する|[https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|

## <a name="next-steps"></a>次の手順

[ゲートウェイのイグレス トラフィックの管理と制御](gateway-ingress-traffic.md)に関する記事で、Azure におけるゲートウェイ コンポーネントを使用したトラフィック フロー制御の詳細を確認してください。
