---
title: "データ セキュリティと暗号化のベスト プラクティス | Microsoft Docs"
description: "この記事では、 Azure の組み込み機能を利用した、データ セキュリティと暗号化に関する一連のベスト プラクティスについて説明します。"
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 06c308f41444e7fa7b227911ff8542149790710a


---
# <a name="azure-data-security-and-encryption-best-practices"></a>Azure のデータ セキュリティと暗号化のベスト プラクティス
クラウドにおけるデータ保護で重要なポイントの 1 つは、データが置かれうる状態と、その状態でどのような制御を使用できるのかを把握することです。 Azure のデータ セキュリティと暗号化のベスト プラクティスでは、次のデータの状態に関する推奨事項が定められています。

* 保存: ストレージ オブジェクト、コンテナー、データ型など、物理メディア (磁気ディスクまたは光学ディスク) に静的な状態で存在しているすべての情報が該当します。
* 転送中: ネットワークやサービス バスなどを経由して、コンポーネント間、場所間、プログラム間でデータが転送されているとき (オンプレミスとクラウド間の転送、ExpressRoute などのハイブリッド接続を含む)、または入出力処理の間、データが転送中であると見なされます。

この記事では、Azure のデータ セキュリティと暗号化に関する一連のベスト プラクティスについて説明します。 このベスト プラクティスは、Azure のデータ セキュリティと暗号化に関してマイクロソフトが蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

それぞれのベスト プラクティスについて、次の点を説明します。

* ベスト プラクティスの内容
* そのベスト プラクティスをお勧めする理由
* そのベスト プラクティスを実践しなかった場合に発生する可能性がある事態
* そのベスト プラクティスに代わる対処法
* そのベスト プラクティスを実践する方法

この「Azure のデータ セキュリティと暗号化のベスト プラクティス」の記事は、この記事の執筆時点における共通認識と、Azure プラットフォームの機能および機能セットに基づいています。 共通認識とテクノロジは時間が経つにつれて変化するため、そのような変化に対応するために、この記事は定期的に更新されます。

この記事で説明する Azure のデータ セキュリティと暗号化に関するベスト プラクティスは、次のとおりです。

* 多要素認証を適用する
* ロールベースのアクセス制御 (RBAC) を使用する
* Azure 仮想マシンを暗号化する
* ハードウェア セキュリティ モデルを使用する
* セキュリティ保護されたワークステーションを利用して管理する
* SQL データの暗号化を有効にする
* 転送中のデータを保護する
* ファイル レベルのデータ暗号化を適用する

## <a name="enforce-multi-factor-authentication"></a>多要素認証を適用する
Microsoft Azure におけるデータ アクセスとデータ制御で最初に行われるのが、ユーザーの認証です。 [Azure Multi-Factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md) とは、ユーザー名とパスワード以外の手段も使ってユーザーを認証する方法です。 この認証方法を利用することで、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することが可能です。

ユーザーに対する Azure MFA を有効にすると、ユーザーのサインインとトランザクションに新しいセキュリティ層が追加されます。 この場合、ファイル サーバーまたは SharePoint Online 上にあるドキュメントにアクセスするトランザクションが発生する可能性があります。 また、Azure MFA は不正に取得された資格情報によって組織データにアクセスされる危険を減らすためにも役立ちます。

たとえば、ユーザーに Azure MFA を適用し、電話やテキスト メッセージを使用して認証するように構成していたとします。攻撃者がユーザーの資格情報を不正に入手したとしても、ユーザーの携帯電話がなければ、リソースにアクセスすることはできません。 この新しい ID 保護層を追加しない場合、資格情報盗用攻撃を受けやすくなり、データの侵害につながる可能性があります。

認証をオンプレミスで管理する必要がある場合は、代わりに [Azure Multi-Factor Authentication Server](../multi-factor-authentication/multi-factor-authentication-get-started-server.md) (MFA オンプレミスとも呼ばれます) を利用できます。 この手法を利用すると、オンプレミスに MFA サーバーを置いていても、多要素認証を適用することができます。

Azure MFA の詳細については、「[クラウドでの Azure Multi-Factor Authentication Server の概要](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)」をご覧ください。

## <a name="use-role-based-access-control-rbac"></a>ロールベースのアクセス制御 (RBAC) を使用する
[知る必要性](https://en.wikipedia.org/wiki/Need_to_know)と[最小権限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)という 2 つのセキュリティ原則に基づいて、アクセスを制限します。 これは、データ アクセスにセキュリティ ポリシーを適用する必要がある組織にとって、絶対に欠かせないものです。 Azure のロールベースのアクセス制御 (RBAC) を使用して、特定のスコープ内のユーザー、グループ、アプリケーションにアクセス許可を割り当てることができます。 ロール割り当てのスコープには、サブスクリプション、リソース グループ、または単独のリソースを指定できます。

Azure の[組み込み RBAC ロール](../active-directory/role-based-access-built-in-roles.md)を利用して、ユーザーに権限を割り当てることができます。 クラウド事業者は、ストレージ アカウントを管理する必要がある場合は*ストレージ アカウント作成協力者*ロール、従来のストレージ アカウントを管理する場合は*従来のストレージ アカウント作成協力者*ロールの使用を検討してください。 VM とストレージ アカウントを管理する必要があるクラウド事業者は、それを*仮想マシン作成協力者*ロールに追加することを検討してください。

RBAC などの機能を利用したデータ アクセス制御を適用しない場合、ユーザーに必要以上の権限が付与される可能性があります。 これにより、一部のユーザーがアクセスする必要がないデータにアクセスできるようになり、データのセキュリティ侵害につながる恐れがあります。

Azure RBAC の詳細については、「[Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)」をご覧ください。

## <a name="encrypt-azure-virtual-machines"></a>Azure 仮想マシンを暗号化する
多くの組織にとって、データ プライバシー、コンプライアンス、データ主権を確保するうえで[保存データの暗号化](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/)は欠かせません。 Azure Disk Encryption を利用して、Windows および Linux の IaaS 仮想マシン (VM) ディスクを暗号化できます。 Azure Disk Encryption では、業界標準である Windows の BitLocker 機能と Linux の DM-Crypt 機能を利用して、OS およびデータ ディスクのボリュームの暗号化を提供します。

Azure Disk Encryption を利用してデータを保護し、組織のセキュリティ要件とコンプライアンス要件を達成することができます。 暗号化を使用して、不正なデータ アクセスに関連するリスクを減らすことも検討してください。 また、機密データをドライブに書き込む前にドライブを暗号化することもお勧めします。

Azure ストレージ アカウントの保存データを保護するために、VM のデータ ボリュームとブート ボリュームを必ず暗号化してください。 暗号化キーとシークレットは、[Azure Key Vault](../key-vault/key-vault-whatis.md) を利用して保護します。

オンプレミスの Windows サーバーについては、次の暗号化のベスト プラクティスを検討してください。

* データの暗号化に [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) を使用する
* 復旧情報を AD DS に格納する
* BitLocker キーのセキュリティ侵害が発生しているという懸念がある場合、ドライブをフォーマットしてドライブ内の BitLocker メタデータ インスタンスをすべて削除するか、ドライブ全体の暗号化を解除し、もう一度暗号化することをお勧めします。

データを暗号化しない組織は、悪意のあるユーザーや犯罪者にデータを盗まれたり、アカウントを乗っ取られて暗号化されていないデータに不正にアクセスされたりするなど、データの保全性に関する問題にさらされる可能性が高くなります。 このようなリスクに加えて、業界の規制を遵守する必要がある企業は、適切なセキュリティ制御を使用してデータのセキュリティ強化に努めていることを証明する必要があります。

Azure Disk Encryption の詳細については、「[Windows および Linux IaaS VM の Azure ディスク暗号化](azure-security-disk-encryption.md)」を参照してください。

## <a name="use-hardware-security-modules"></a>ハードウェア セキュリティ モジュールを使用する
業界標準の暗号化ソリューションでは、秘密キーを使ってデータを暗号化します。 そのため、このようなキーを安全に格納することが非常に重要です。 データの暗号化に使用される秘密キーはキー管理機能によって保存されるため、キー管理はデータ保護に欠かせない機能です。

Azure Disk Encryption では [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) を利用してディスク暗号化キーとシークレットを Key Vault サブスクリプションで制御および管理でき、Azure Storage に保存されている仮想マシン ディスク内のすべてのデータを確実に暗号化できます。 Azure Key Vault を使用して、キーとポリシーの使用状況を監査する必要があります。

適切なセキュリティ制御を実施して、データの暗号化に使用された秘密キーを保護しなければ、さまざまなリスクが生じます。 攻撃者が秘密キーを手に入れた場合、データの暗号化を解除して機密情報にアクセスできるようになります。

Azure における証明書管理に関する一般的な推奨事項については、「[Certificate Management in Azure: Do’s and Don’ts (Azure の証明書管理に関する注意事項)](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/)」を参照してください。

Azure Key Vault の詳細については、「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md)」を参照してください。

## <a name="manage-with-secure-workstations"></a>セキュリティ保護されたワークステーションを利用して管理する
ほとんどの攻撃はエンド ユーザーを標的としているため、エンドポイントが主要な攻撃目標の 1 つとなっています。 攻撃者はエンドポイントをセキュリティ侵害することで、ユーザーの資格情報を悪用して組織のデータにアクセスできるようになります。 ほとんどのエンドポイント攻撃は、エンドユーザーがローカル ワークステーションの管理者であるという状況を利用して行われます。

セキュリティで保護された管理ワークステーションを利用することで、このようなリスクを軽減できます。 ワークステーションの攻撃対象領域を減らすために、[Privileged Access Workstations (PAW)](https://technet.microsoft.com/library/mt634654.aspx) の利用をお勧めします。 このようなセキュリティで保護された管理ワークステーションを使用することで、攻撃を受ける可能性を減らし、データの安全性を高めることができます。 PAW を利用して、ワークステーションのセキュリティを強固にしましょう。 これは、機密性が求められるアカウント、タスク、データの保護に必要な高度なセキュリティを保証するために重要な手順です。

エンドポイントを保護しなければデータがリスクにさらされる危険性があるため、データの保存先 (クラウドまたはオンプレミス) に関わらず、そのデータを利用するすべてのデバイスにセキュリティ ポリシーを必ず適用してください。

Privileged Access Workstations の詳細については、[特権アクセスのセキュリティ保護](https://technet.microsoft.com/library/mt631194.aspx)に関するページをご覧ください。

## <a name="enable-sql-data-encryption"></a>SQL データの暗号化を有効にする
[Azure SQL Database Transparent Data Encryption](https://msdn.microsoft.com/library/dn948096.aspx) (TDE) を使用すると、データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、悪意のあるアクティビティの脅威からデータを保護できます。アプリケーションを変更する必要はありません。  TDE は、データベース暗号化キーと呼ばれる対称キーを使用してデータベース全体のストレージを暗号化します。

ストレージ全体を暗号化している場合でも、データベース自体を暗号化することは非常に重要です。 これは、多層防御型のデータ保護手法の 1 つです。 [Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) を使用している場合、クレジット カードや社会保障番号などの機密情報を保護する必要があるときには、さまざまな業界標準 (HIPAA、PCI など) の要件を満たす FIPS 140-2 検証済み 256 ビット AES 暗号化を使ってデータベースを暗号化できます。

データベースを TDE で暗号化する場合、[バッファー プール拡張機能](https://msdn.microsoft.com/library/dn133176.aspx) (BPE) に関連するファイルは暗号化されないことを理解しておく必要があります。 BPE 関連ファイルについては、BitLocker や[暗号化ファイル システム](https://technet.microsoft.com/library/cc700811.aspx) (EFS) などのファイル システム レベルの暗号化ツールを使用する必要があります。

セキュリティ管理者やデータベース管理者などの特権を持つユーザーは、TDE で暗号化されたデータベースのデータにもアクセスできるため、次の推奨事項にも従う必要があります。

* データベース レベルでの SQL 認証
* RBAC ロールを使用した Azure AD 認証
* ユーザーとアプリケーションは、別々のアカウントを使って認証する必要があります。 そうすることで、ユーザーとアプリケーションに付与されるアクセス許可を制限し、悪意のあるアクティビティのリスクを減らすことができます。
* 固定データベース ロール (db_datareader、db_datawriter など) を使用してデータベース レベルのセキュリティを実装しますが、アプリケーション用のカスタム ロールを作成して、選択したデータベース オブジェクトに明示的なアクセス許可を付与することもできます。

データベース レベルの暗号化を使用しない場合、SQL データベース内にあるデータを損なうような攻撃を受けやすくなる可能性があります。

SQL TDE 暗号化の詳細については、「[Azure SQL Database での Transparent Data Encryption](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx)」を参照してください。

## <a name="protect-data-in-transit"></a>転送中のデータを保護する
転送中のデータの保護は、データ保護戦略に欠かせない要素です。 データはさまざまな場所を経由して転送されるため、一般的には常時 SSL/TLS プロトコルを使用してデータをやり取りすることが推奨されています。 状況によっては、オンプレミスとクラウド インフラストラクチャ間の通信チャネル全体を、仮想プライベート ネットワーク (VPN) を使用して隔離する必要があります。

オンプレミス インフラストラクチャと Azure 間のデータ移動については、HTTPS や VPN などの適切なセキュリティ対策を検討してください。

オンプレミスにある複数のワークステーションから Azure へのアクセスをセキュリティ保護する必要がある場合には、[Azure のサイト間 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) を使用します。

オンプレミスにある 1 個のワークステーションから Azure へのアクセスをセキュリティ保護する必要がある場合には、[ポイント対サイト VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md) を使用します。

大規模なデータ セットは、[ExpressRoute](https://azure.microsoft.com/services/expressroute/) などの専用高速 WAN リンクを利用して移動できます。 ExpressRoute を使用する場合、[SSL/TLS](https://support.microsoft.com/kb/257591) などのプロトコルを使用してアプリケーション レベルでデータを暗号化することで、さらにセキュリティを強化できます。

Azure ポータルで Azure Storage を操作する場合、すべてのトランザクションは HTTPS 経由で行われます。 HTTPS 経由の [Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) も、[Azure Storage](https://azure.microsoft.com/services/storage/) と [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) の操作に使用できます。

転送中のデータを保護しない場合、[man-in-the-middle 攻撃](https://technet.microsoft.com/library/gg195821.aspx)、[盗聴](https://technet.microsoft.com/library/gg195641.aspx)、セッション ハイジャックに対して脆弱になります。 このような攻撃は、機密データへのアクセスを取得するための最初の手順として実行される場合があります。

Azure VPN オプションの詳細については、「[VPN Gateway の計画と設計](../vpn-gateway/vpn-gateway-plan-design.md)」を参照してください。

## <a name="enforce-file-level-data-encryption"></a>ファイル レベルのデータ暗号化を適用する
データのセキュリティ レベルをさらに高めるには、ファイルの場所に関係なくファイル自体を暗号化します。

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) は、暗号化、ID、承認ポリシーを使用してファイルとメールを保護します。 Azure RMS は組織内と組織外の両方でデータを保護できるため、携帯電話、タブレット、PC などの複数のデバイスに適用できます。 これが可能なのは、データが組織外に出たとしても Azure RMS による保護がデータに残るためです。

Azure RMS を使用してファイルを保護する場合、[FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html) を完全にサポートする業界標準の暗号化技術が適用されます。 データ保護に Azure RMS を利用すると、クラウド ストレージ サービスのような IT 部門の管轄外のストレージにファイルがコピーされたとしても、ファイルを確実に保護できます。 メールでファイルを共有する場合も同じです。メール メッセージの添付ファイルとしてファイルを保護し、保護された添付ファイルを開く方法をメール本文に記載しておきます。

Azure RMS の導入を計画するときは、次の準備を行うことをお勧めします。

* [RMS 共有アプリ](https://technet.microsoft.com/library/dn339006.aspx)をインストールします。 Office アドインをインストールすることによって、このアプリが Office アプリケーションと統合され、ユーザーが直接簡単にファイルを保護できるようになります。
* Azure RMS をサポートするようにアプリケーションとサービスを構成します。
* ビジネス要件を反映した[カスタム テンプレート](https://technet.microsoft.com/library/dn642472.aspx)を作成します。 たとえば、すべての極秘メールに適用する、極秘データ用テンプレートを作成します。

[データ分類](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf)やデータ保護が不十分な組織は、データ漏洩のリスクが高くなる可能性があります。 ファイルを適切に保護しなければ、ビジネスを分析し、不正使用を監視し、ファイルへの悪意のあるアクセスを防ぐことはできません。

Azure RMS の詳細については、[Azure Rights Management の概要](https://technet.microsoft.com/library/jj585016.aspx)に関するページをご覧ください。



<!--HONumber=Nov16_HO3-->


