---
title: "Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス | Microsoft Docs"
description: " ワークロードを Azure IaaS に移行すると、設計を再評価する機会が得られます "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: barclayn
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 3123c8d780406c92f04592767e47c217c0a0ba73
ms.lasthandoff: 03/28/2017




---

# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス

Azure のサービスとしてのインフラストラクチャ (IaaS) へのワークロードの移行を検討し始めたとき、考慮事項の中によくご存知の項目があったのではないでしょうか。 仮想環境の保護については既に経験されているかもしれません。 Azure IaaS に移行する際は、仮想環境の保護に関する既存の専門知識を活用しながら、資産の保護に役立つ新しい各種オプションを利用できます。

オンプレミスのリソースを一対一で Azure に移動するわけではないことに注意してください。 新たな課題と新たなオプションを通じて、既存の設計、ツール、およびプロセスを再評価する機会が得られます。

お客様のセキュリティの責任は、クラウド サービスの種類に応じて決まります。 表は、Microsoft とお客様の責任の分担をまとめたものです。


![責任の分担](./media/azure-security-iaas/sec-cloudstack-new.png)


この記事では、組織のセキュリティ要件を満たすために Azure で使用できるオプションについて説明します。 ワークロードの種類によってセキュリティ要件が異なる可能性があることにご注意ください。 ここで説明するベスト プラクティスの 1 つだけを適用してシステムを保護できるわけではありません。 セキュリティの他の要素と同様、適切なオプションを選択しつつ、複数のソリューションが互いに不足した部分を補完し合う方法について検討する必要があります。

## <a name="use-privileged-access-workstations"></a>Privileged Access Workstation を使用する

組織がサイバー攻撃を受けるケースの大半では、管理者が処理を実行する際に管理者権限を持つアカウントを使用している点が狙われています。 通常は、これらの処理は悪意を持って行われるわけではなく、既存の構成とプロセスで許可されている操作です。 管理者ユーザーのほとんどは、リスクを概念として理解しつつも、リスクの高い操作を行ってしまっています。

電子メールのチェックやインターネットの閲覧といった、リスクがないように思える操作でも、 巧妙に作られた電子メールによって企業へのアクセス権が取得されるなど、管理者権限を持つアカウントが公開されて、閲覧操作を利用できる悪意のあるユーザーによってセキュリティが侵害される危険性があります。 偶発的なセキュリティ侵害のリスクを低減する方法として、Azure の管理タスクを実行する際はセキュリティ保護された管理ワークステーションを常に使用することを強くお勧めします。

Privileged Access Workstation (PAW) には、機密性の高いタスク向けのオペレーティング システムが搭載されており、インターネット上の攻撃や脅威要因から保護されます。 このような機密性の高いタスクとアカウントを、日常的に使用するワークステーションやデバイスから切り離すことで、フィッシング攻撃、アプリケーションと OS の脆弱性、さまざまな偽装攻撃、資格情報の盗難 (キーロガー、Pass the Hash、Pass the Ticket など) から強力に保護されます。

PAW アプローチは、管理者に割り当てられた管理アカウントを標準のユーザー アカウントと分離して使用するために確立された推奨手法を拡張したものです。 PAW は機密性の高いアカウント向けの信頼できるワークステーションです。

詳細情報と実装のガイダンスについては、「[Privileged Access Workstation](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations)」を参照してください。

## <a name="use-multi-factor-authentication"></a>Multi-Factor Authentication を使用する

これまで、ネットワーク境界は会社のデータへのアクセスを制御するために使用されていました。 クラウドを中心としたモバイル重視の世界では、ID が制御プレーンとなります。つまり、ID を使用して、あらゆるデバイスから IaaS サービスへのアクセスを制御し、 データが使用されている場所や方法を詳細に把握、分析します。 Azure ユーザーのデジタル ID の保護は、サブスクリプションを ID の盗難やその他のサイバー犯罪から保護するための基盤です。

アカウントを保護するための最も有益な手順の 1 つに、2 要素認証があります。 2 要素認証は、パスワードと他の要素を組み合わせて認証する方法です。 2 要素認証を使用すると、他のユーザーのパスワードを不正に入手したユーザーによるアクセスのリスクを軽減できます。

多要素認証を実現する [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護できます。 電話、テキスト メッセージ、モバイル アプリによる通知など、簡単な検証方法を通じて確実な認証を行うことができます。 ユーザーは自分の好きな方法を選べます。

Microsoft 認証モバイル アプリを使うと、Multi-Factor Authentication を最も簡単に利用できます。このアプリは、Windows、IOS、および Android を実行しているモバイル デバイスで使用できます。 Windows 10 の最新リリースと、Azure Active Directory と統合されたオンプレミスの Active Directory があれば、[Windows Hello for Business](../active-directory/active-directory-azureadjoin-passport-deployment.md) を使用して Azure リソースへのシームレスなシングル サインオンが可能です。 その場合は、Windows 10 デバイスが認証の 2 つ目の要素として使用されます。

Azure サブスクリプションを管理するアカウントや Virtual Machines にサインインできるアカウントで Multi-Factor Authentication を使用すると、パスワードのみを使用する場合よりも強力なセキュリティを実現できます。 他の形式の 2 要素認証を使用することもできますが、まだ運用システムに導入されていない場合は、デプロイが複雑になる可能性があります。

次のスクリーンショットは、Azure Multi-Factor Authentication に使用できるオプションの一部を示しています。

![Multi-Factor Authentication のオプション](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>管理アクセスを制限および制約する

Azure サブスクリプションを管理できるアカウントの保護はきわめて重要です。 このようなアカウントのセキュリティが侵害されると、データの機密性と整合性を確保するための他のすべての手順は価値がなくなります。 [エドワード スノーデン](https://en.wikipedia.org/wiki/Edward_Snowden)による機密情報の漏えい事件からわかるように、内部からの攻撃は組織のセキュリティ全体に甚大な脅威を与えます。

次に示すような条件に従って、管理者権限についてユーザーを評価してください。

- 管理特権を必要とするタスクを実行しているか。
- どのくらいの頻度でタスクが実行されるか。
- 別の管理者が代わりにタスクを実行できない理由があるか。

特権を付与するためのその他すべての既知の代替アプローチ、および各アプローチを許容できない理由を文書化する。

ジャスト イン タイム管理を使用すると、管理者権限が不要になった場合に、その権限を持つ不要なアカウントが存在しないようにすることができます。 アカウントには期間限定の管理者権限が割り当てられるため、管理者は自分の業務を行うことができ、 シフトが終了するかタスクが完了すると、その権限が削除されます。

[Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md) を使用すると、組織内でアクセスを管理、監視、制御できます。 組織のユーザーがとった行動を常に把握することができます。 また、対象管理者という概念を取り入れて、Azure AD にジャスト イン タイム管理を導入することもできます。 これらは、管理権限を付与する可能性のあるアカウントを持つ個人です。 この種類のユーザーには、アクティブ化プロセスを経て期間限定の管理権限を付与できます。


## <a name="use-devtest-labs"></a>DevTest Labs を使用する

ラボ環境と開発環境として Azure を使用すると、ハードウェアの調達によって生じる遅れを排除してテストと開発における俊敏性を獲得できます。 残念なことに、Azure に関する知識の不足や Azure を迅速に採用したいという願望が原因で、管理者に過度な権限が割り当てられてしまうことがあります。 このリスクがもとで、意図せず組織が内部からの攻撃にさらされるおそれがあります。 ユーザーに、必要とされるよりもはるかに多くのアクセス権が付与される可能性もあります。

[Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) サービスでは、[Azure のロール ベースのアクセス制御 ](../active-directory/role-based-access-control-what-is.md) (RBAC) を使用します。 RBAC を使用すると、チーム内での職務を複数のロールに分けて、業務を行うために必要なレベルのアクセス権だけをユーザーに付与することができます。 RBAC には、あらかじめ定義されたロール (所有者、ラボ ユーザー、共同作成者) があります。 これらのロールを使用して、外部のパートナーに権限を割り当て、コラボレーションを大幅に簡略化することもできます。

RBAC を使用しているため、追加の[カスタム ロール](../devtest-lab/devtest-lab-grant-user-permissions-to-specific-lab-policies.md)も作成できます。 DevTest Labs は、アクセス許可の管理だけでなく、環境のプロビジョニング プロセスも簡略化されています。 開発環境やテスト環境を利用するチームが直面する他の一般的な課題に対処するようにも設計されています。 DevTest Labs を使用するには多少の準備が必要ですが、長期的に見ると、チームの業務の効率化につながります。

Azure DevTest Labs の機能には次のようなものがあります。

- ユーザーが使用できるオプションを管理者が制御します。 管理者は、VM の許容サイズ、VM の最大数、VM を起動およびシャットダウンするタイミングなどの項目を一元管理できます。
- ラボ環境の作成の自動化
- コストの追跡
- 一時的な共同作業に使用する VM の配布の簡略化
- ユーザーがテンプレートを使用してラボをプロビジョニングできるセルフサービス
- 使用量の管理と制限

![DevTest Labs を使用したラボの作成](./media/azure-security-iaas/devtestlabs.png)

DevTest Labs の使用に伴って発生する追加コストはありません。 ラボ、ポリシー、テンプレート、およびアーティファクトの作成はすべて無料です。 仮想マシン、ストレージ アカウント、仮想ネットワークなど、ラボ内で使用する Azure リソースに対してのみ課金されます。



## <a name="control-and-limit-endpoint-access"></a>エンドポイント アクセスを制御および制限する

Azure でのラボまたは運用システムのホストは、インターネットからシステムにアクセスする必要があることを意味します。 既定で、新しい Windows 仮想マシンにはインターネットからアクセス可能な RDP ポートがあり、Linux 仮想マシンの場合は SSH ポートが開いています。 承認されていないアクセスのリスクを最小限に抑えるために、公開されるエンドポイントを制限するための手順を実行する必要があります。

Azure に用意されているテクノロジを使用すると、これらの管理エンドポイントへのアクセスを制限するのに役立ちます。 Azure で使用できるのは、[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md) (NSG) です。 Azure Resource Manager を使用してデプロイする場合は、NSG で、すべてのネットワークから管理エンドポイント (RDP または SSH) へのアクセスのみを制限します。 NSG について検討する場合は、ルーターの ACL について検討してください。 NSG を使用すると、Azure ネットワークのさまざまなセグメント間のネットワーク通信を厳密に制御できます。 これは、境界ネットワークなどの分離されたネットワークを作成する場合と同様です。 NSG はトラフィックを検査しませんが、ネットワークのセグメント化に役立ちます。


Azure では、オンプレミスのネットワークから[サイト間 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) を構成できます。 サイト間 VPN を構成すると、オンプレミス ネットワークがクラウドに拡張されます。 ここでも NSG が役立ちます。ローカル ネットワーク以外のすべての場所からのアクセスを禁止するよう NSG を変更できます。 まず VPN 経由で Azure ネットワークに接続してから管理タスクを実行するよう要求できます。

サイト間 VPN は、オンプレミス リソースと綿密に統合された運用システムを Azure にホストしている場合に最適なオプションです。

また、[ポイント対サイト](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) オプションは、オンプレミス リソースへのアクセスが不要なシステムを管理する場合にも使用できます。 このようなシステムは専用の Azure 仮想ネットワークに分離できます。 管理者は、管理ワークステーションから Azure がホストする環境への VPN を構築できます。

>[!NOTE]
>どちらの VPN オプションでも、インターネットから管理エンドポイントへのアクセスを禁止するように NSG の ACL を再構成できます。

検討する価値のあるもう 1 つのオプションは、[リモート デスクトップ ゲートウェイ](../multi-factor-authentication/multi-factor-authentication-get-started-server-rdg.md)のデプロイメントです。 このデプロイメントを使用すると、リモート デスクトップ サーバーに HTTPS 経由で安全に接続し、きめ細かく接続を制御できます。

利用できる機能には次のようなものがあります。

- 特定のシステムから要求への接続を制限する管理者オプション
- スマート カード認証または Azure Multi-Factor Authentication
- ユーザーがゲートウェイ経由で接続できるシステムの制御
- デバイスとディスクのリダイレクトの制御

## <a name="use-a-key-management-solution"></a>キー管理ソリューションを使用する

クラウドにあるデータを保護するうえで、安全なキー管理は不可欠です。 [Azure Key Vault](../key-vault/key-vault-whatis.md) を使用すると、暗号化キーや (パスワードなどの) 小規模の秘密情報をハードウェア セキュリティ モジュール (HSM) に安全に格納できます。 さらに安心感を高めたい場合には、HSM でキーのインポートや生成を行うことができます。

お客様のキーは Microsoft が FIPS 140-2 Level 2 適合の HSM (ハードウェアおよびファームウェア) で処理します。 キーの使用状況は、Azure ログを使用して監視と監査を行えます。ログを Azure またはご使用のセキュリティ情報/イベント管理 (SIEM) に渡して、詳細な解析を行い、脅威を検出することもできます。

Azure サブスクリプションを持つユーザーはだれでも、Key Vault を作成して使用できます。 Key Vault は開発者とセキュリティ管理者にとってメリットがありますが、組織内で Azure サービスを管理する担当者も実装と管理を実行できます。


## <a name="encrypt-virtual-disks-and-disk-storage"></a>仮想ディスクとディスク ストレージを暗号化する

[Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) は、未承認のアクセスによるデータの盗難や流出の脅威に対処します。このような脅威では、ディスクが持ち出され、 セキュリティ制御を迂回するために別のシステムにディスクが接続されることがあります。 Disk Encryption では、Windows の [BitLocker](https://technet.microsoft.com/library/hh831713) や Linux の DM-Crypt を使用してオペレーティング システムとデータ ドライブを暗号化します。 また、暗号化キーの制御と管理のために Key Vault と統合されています。 この機能は、Standard VM と、Premium Storage を使用する VM で利用できます。

詳細については、「[Windows および Linux IaaS VM の Azure ディスク暗号化](azure-security-disk-encryption.md)」をご覧ください。

[Azure Storage Service Encryption](../storage/storage-service-encryption.md) は保存データを保護します。 この機能は、ストレージ アカウント レベルで有効にします。 データセンターに書き込まれる際にデータが暗号化され、データにアクセスすると自動的に暗号化が解除されます。 列マッピングは、次のシナリオをサポートしています。

- ブロック BLOB、追加 BLOB、ページ BLOB の暗号化
- オンプレミスから Azure に移動したアーカイブ済み VHD とテンプレートの暗号化
- VHD を使用して作成した IaaS VM の基になっている OS とデータ ディスクの暗号化

Azure Storage Service Encryption を使用する前に、次に示す 2 つの制限事項を確認してください。

- 従来のストレージ アカウントでは使用できません。
- 暗号化されるのは、暗号化を有効にした後に書き込まれたデータのみです。

## <a name="use-a-centralized-security-management-system"></a>セキュリティの一元管理システムを使用する

サーバーは、セキュリティの問題と見なされる可能性のある修正プログラムの適用、構成、イベント、アクティビティを監視する必要があります。 このような問題に対処するために、[Security Center](https://azure.microsoft.com/services/security-center/) と [Operations Management Suite の Security & Compliance](https://azure.microsoft.com/services/security-center/) を使用できます。 この 2 つのオプションは、オペレーティング システム内の構成にとどまらず、 基盤となるインフラストラクチャの構成 (ネットワーク構成や仮想アプライアンスの使用状況など) も監視できます。

## <a name="manage-operating-systems"></a>オペレーティング システムを管理する

IaaS のデプロイメントでも、環境内の他のサーバーやワークステーションと同様に、デプロイするシステムを管理する必要があります。 修正プログラムの適用、セキュリティの強化、権限の割り当てなど、システムのメンテナンスに関連するアクティビティはユーザーが行います。 オンプレミスのリソースと緊密に統合されているシステムでは、オンプレミスで使用しているのと同じツールと手順を使用して、ウイルス対策、マルウェア対策、修正プログラムの適用、バックアップなどの処理を行うことが推奨されます。

### <a name="harden-systems"></a>システムを強化する
Azure IaaS のすべての仮想マシンを強化して、インストールされるアプリケーションで必要なサービス エンドポイントだけが公開されるようにする必要があります。 Windows 仮想マシンの場合は、[Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) ソリューションのベースラインとして Microsoft が発表した推奨事項に従ってください。

Security Compliance Manager は無料のツールです。 このツールでは、グループ ポリシーと System Center Configuration Manager を使用して、デスクトップ、従来のデータセンター、プライベート クラウド/パブリック クラウドを短時間で構成および管理できます。

Security Compliance Manager には、テスト済みのデプロイの準備完了ポリシーと Desired Configuration Management 構成パックが用意されています。 これらのベースラインは、[Microsoft のセキュリティ ガイド](https://technet.microsoft.com/en-us/library/cc184906.aspx)の推奨事項と業界のベスト プラクティスに基づいています。 構成のずれを管理し、コンプライアンス要件に対応して、セキュリティ上の脅威を削減することができます。

Security Compliance Manager を使用すると、2 つの方法でコンピューターの現在の構成をインポートできます。 1 つは、Active Directory ベースのグループ ポリシーをインポートする方法です。 もう 1 つは、"ゴールデン マスター" 参照コンピューターの構成をインポートする方法です。この方法では、[LocalGPO ツール](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/)を使用してローカル グループ ポリシーをバックアップし、 そのローカル グループ ポリシーを Security Compliance Manager にインポートします。

使用する標準を業界のベスト プラクティスと比較してカスタマイズし、新しいポリシーと Desired Configuration Management 構成パックを作成してください。 Windows 10 Anniversary Update と Windows Server 2016 を含む、サポート対象のすべてのオペレーティング システム用のベースラインが発行されています。


### <a name="install-and-manage-antimalware"></a>マルウェア対策をインストールして管理する

運用環境とは別にホストされている環境では、マルウェア対策の拡張機能を使用して、仮想マシンとクラウド サービスを保護できます。 この拡張機能は [Azure Security Center](../security-center/security-center-intro.md) と統合されています。


[Microsoft マルウェア対策](azure-security-antimalware.md)には、リアルタイム保護、スケジュールされたスキャン、マルウェアの修復、定義の更新、エンジンの更新、サンプル レポート、除外イベントの収集、[PowerShell のサポート](https://msdn.microsoft.com/library/dn771715.aspx)などの機能が含まれます。

![Azure のマルウェア対策](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>最新のセキュリティ更新プログラムをインストールする
お客様が最初に Azure に移動するワークロードに、ラボと外部向けのシステムがあります。 Azure でホストされた仮想マシンで、インターネットへのアクセスが必要なアプリケーションまたはサービスをホストする場合は、修正プログラムの適用を忘れずに実行してください。 これは、オペレーティング システムへの修正プログラムの適用だけではありません。 サード パーティ アプリケーションでも、修正プログラムの未適用による脆弱性が原因で問題が発生する可能性があります。このような問題は、適切な修正プログラム管理が行われていれば回避できます。

### <a name="deploy-and-test-a-backup-solution"></a>バックアップ ソリューションをデプロイしてテストする

セキュリティ更新プログラムと同様、バックアップについても、他の操作と同じ方法で対処する必要があります。 これは、クラウドに拡張する運用環境に含まれるシステムに該当します。 テスト用システムと開発用システムは、ユーザーがオンプレミス環境の経験で使い慣れているのと同様の復元機能を提供できるバックアップ戦略に沿う必要があります。

Azure に移動する運用ワークロードは、可能な限り、既存のバックアップ ソリューションと統合するのが適切です。 または、[Azure Backup](../backup/backup-azure-arm-vms.md) を使用してバックアップ要件に対処することもできます。


## <a name="monitor"></a>監視

[Security Center](../security-center/security-center-intro.md) は Azure リソースのセキュリティの状態を継続的に評価して、潜在的なセキュリティ脆弱性を特定します。 推奨事項の一覧では、必要な制御を構成する手順を説明します。

たとえば、次のようになります。

- 悪意のあるソフトウェアを識別して削除するためのマルウェア対策をプロビジョニングする
- 仮想マシンへのトラフィックを制御するためにネットワーク セキュリティ グループとルールを構成する
- Web アプリケーションを対象とする攻撃から保護するために Web アプリケーション ファイアウォールをプロビジョニングする
- 不足しているシステムの更新プログラムをデプロイする
- 推奨基準と一致しない OS 構成に対処する

次の図は、Security Center で有効にできるオプションの一部を示しています。

![Azure Security Center のポリシー](./media/azure-security-iaas/security-center-policies.png)

[Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) は、Microsoft のクラウドベースの IT 管理ソリューションです。Operations Management Suite を使用して、オンプレミスとクラウドのインフラストラクチャを管理し、保護することができます。 Operations Management Suite はクラウドベースのサービスとして実装されているため、インフラストラクチャ リソースに最小限の投資をするだけですぐにデプロイできます。

新しい機能が追加されると自動的に配信されるため、継続的なメンテナンスやアップグレードのコストが節約されます。 また、Operations Management Suite は System Center Operations Manager と統合されています。 Operations Management Suite には、[セキュリティとコンプライアンス](../operations-management-suite/oms-security-getting-started.md) モジュールをはじめとして、Azure のワークロードを効率的に管理するためのさまざまなコンポーネントが用意されています。

Operations Management Suite のセキュリティとコンプライアンスの機能を使用すると、リソースに関する情報を表示できます。 情報は、次の 4 つの主要カテゴリで構成されます。

- **セキュリティ ドメイン**: この領域では、時間の経過に伴うセキュリティ レコードを詳しく調査します。 また、マルウェアの評価、更新プログラムの評価、ネットワーク セキュリティ情報、ID とアクセスの情報、セキュリティ イベントが発生したコンピューターを確認できます。 Azure Security Center のダッシュボードにも簡単にアクセスできます。
- **注目に値する問題**: アクティブな問題の数と重要度をすばやく特定できます。
- **検出 (プレビュー)**: リソースに対して攻撃が発生したときにセキュリティの警告を視覚化することで、攻撃パターンを特定できます。
- **脅威インテリジェンス**: 悪意のある送信側 IP トラフィックを持つサーバーの総数、悪意のある脅威の種類、このような IP の発信元を示すマップを視覚化することで、攻撃パターンを特定できます。
- **一般的なセキュリティ クエリ**: 環境を監視するために使用できる最も一般的なセキュリティ クエリの一覧が表示されます。 これらのクエリの 1 つをクリックすると、**[検索]** ブレードが開き、そのクエリの結果が表示されます。

次のスクリーンショットは、Operations Management Suite で表示できる情報の例を示しています。

![Operations Management Suite のセキュリティ ベースライン](./media/azure-security-iaas/oms-security-baseline.png)



## <a name="next-steps"></a>次のステップ


* [Azure セキュリティ チームのブログ](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)
* [Azure セキュリティのベスト プラクティスとパターン](security-best-practices-and-patterns.md)

