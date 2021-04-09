---
title: セキュリティ保護とポリシーの使用
description: Azure での仮想マシンのセキュリティとポリシーについて説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: cynthn
ms.topic: conceptual
ms.openlocfilehash: 291b9a210bf5f8cc18ccf8a523e282d3fc85aa28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673356"
---
# <a name="secure-and-use-policies-on-virtual-machines-in-azure"></a>Azure で仮想マシンをセキュリティで保護し、ポリシーを使用する

実行するアプリケーションの仮想マシン (VM) は、常に安全な状態に保つ必要があります。 VM の安全を確保する手段としては、Azure のサービスや機能を通じて、VM へのアクセスやデータのストレージにセキュリティを確保する方法が挙げられます。 この記事では、VM とアプリケーションのセキュリティを維持するうえで役に立つ情報を提供します。

## <a name="antimalware"></a>マルウェア対策

最近のクラウド環境に対する脅威は変化が激しく、コンプライアンスとセキュリティの要件を満たすために効果的な保護を維持しなければならないという圧力はますます大きくなっています。 [Azure に対する Microsoft マルウェア対策](../security/fundamentals/antimalware.md)は、ウイルス、スパイウェアなどの悪意のあるソフトウェアの特定や駆除に役立つリアルタイムの保護機能です。 悪意のあることまたは望ましくないことが確認されているソフトウェアが VM 上で実行されようとしていたり、自らインストールを試みたりした場合に、その事実を把握できるようにアラートを構成することができます。 Linux または Windows Server 2008 を実行している VM ではサポートされていません。

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../security-center/security-center-introduction.md) は、VM に対する脅威の防御、検出、対応を可能にする機能です。 Security Center は、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

Security Center の Just-In-Time アクセスは、VM のデプロイ全体に適用できます。これによって Azure VM へのインバウンド トラフィックをロックダウンし、VM への接続が必要な場合は簡単にアクセスできるようにしつつ、攻撃に対する露出を減らすことができます。 Just-In-Time が有効で、ユーザー要求が VM にアクセスする場合、ユーザーが VM に対してどのアクセス許可を持っているかが Security Center によって確認されます。 ユーザーが適切なアクセス許可を持っている場合は要求が承認され、Security Center では、選択したポートへのインバウンド トラフィックを制限時間内だけで許可するように、ネットワーク セキュリティ グループ (NSG) が自動的に構成されます。 指定された時間が経過すると、Security Center により NSG が以前の状態に復元されます。 

## <a name="encryption"></a>暗号化

マネージド ディスクには、2つの暗号化方式が用意されています。 ひとつは、Azure Disk Encryption と呼ばれる OS レベルでの暗号化、そしてもうひとつは、サーバー側で暗号化を行うプラットフォーム レベルでの暗号化です。

### <a name="server-side-encryption"></a>サーバー側暗号化

Azure マネージド ディスクは、データをクラウドに永続化するときに、既定で自動的にデータを暗号化します。 サーバー側の暗号化によってデータが保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 Azure マネージド ディスク内のデータは、利用できる最も強力なブロック暗号の 1 つである 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)を使って透過的に暗号化され、FIPS 140-2 に準拠しています。

暗号化はマネージド ディスクのパフォーマンスに影響しません。 暗号化に追加コストはかかりません。

プラットフォーム マネージド キーを利用してお使いのマネージド ディスクを暗号化することも、お使いの独自のキーを使用して暗号化を管理することもできます。 独自のキーを使用して暗号化を管理する場合は、マネージド ディスク内のすべてのデータの暗号化と暗号化解除に使用する *カスタマー マネージド キー* を指定できます。 

サーバー側の暗号化の詳細については、[Windows](./disk-encryption.md) または [Linux](./disk-encryption.md) の記事を参照してください。

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

仮想マシン ([Windows VM](windows/disk-encryption-overview.md) および [Linux VM](linux/disk-encryption-overview.md)) のセキュリティとコンプライアンスを強化するために、Azure の仮想ディスクを暗号化できます。 Windows VM の仮想ディスクは、BitLocker を使って保存時に暗号化されます。 Linux VM の仮想ディスクは、dm-crypt を使って暗号化します。 

Azure の仮想ディスクを暗号化するための料金はかかりません。 暗号化キーは、ソフトウェア保護を使って Azure Key Vault に格納されます。または、FIPS 140-2 レベル 2 標準に認定された Hardware Security Module (HSM) でキーをインポートまたは生成することもできます。 これらの暗号化キーは、VM に接続された仮想ディスクの暗号化/暗号化解除に使われます。 これらの暗号化キーの制御を維持し、その使用を監査することができます。 Azure Active Directory サービス プリンシパルは、VM の電源がオンまたはオフになったときにこれらの暗号化キーを発行するためのセキュリティで保護されたメカニズムを提供します。

## <a name="key-vault-and-ssh-keys"></a>Key Vault と SSH キー

シークレットと証明書は、リソースとしてモデル化して [Key Vault](../key-vault/general/basic-concepts.md) で提供することができます。 [Windows VM](windows/key-vault-setup.md) のキー コンテナーは Azure PowerShell で、[Linux VM](linux/key-vault-setup.md) のキー コンテナーは Azure CLI で作成できます。 暗号化用のキーを作成することもできます。

キー コンテナー アクセス ポリシーでは、キー、シークレット、証明書へのアクセス許可を個別に付与します。 たとえば、ユーザーにキーのみのアクセス権を付与し、シークレットのアクセス権は付与しないようにすることができます。 ただし、キー、シークレット、または証明書へのアクセス権は、コンテナー レベルで付与されます。 つまり、[キー コンテナー アクセス ポリシー](../key-vault/general/secure-your-key-vault.md)では、オブジェクト レベルのアクセス許可がサポートされません。

VM に接続するときは、公開キー暗号化を使用して、より安全な方法で VM にサインインできるようにする必要があります。 このプロセスでは、ユーザー名とパスワードを使用する代わりに、SSH (Secure Shell) コマンドを使用して公開キーと秘密キーを交換して、自分を認証します。 パスワードは、ブルートフォース攻撃に対して脆弱です。これは、特に Web サーバーなどのインターネットに接続された仮想マシンに当てはまります。 Secure Shell (SSH) キー ペアを使用すると、認証に SSH キーを使う [Linux VM](linux/mac-create-ssh-keys.md) を作成でき、サインインするためのパスワードが不要になります。 [Windows VM](linux/ssh-from-windows.md) から SSH キーを使って Linux VM に接続することもできます。

## <a name="managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID

クラウド アプリケーションの構築時における一般的な課題は、クラウド サービスへの認証用のコードで資格情報をどのように管理するかです。 資格情報を安全に保つことは重要な課題です。 資格情報は開発者のワークステーションに表示されないこと、またソース管理にチェックインされないことが理想です。 資格情報やシークレットなど、各種キーを安全に保管する手段としては Azure Key Vault がありますが、それらを取得するためには、コードから Key Vault に対して認証を行わなければなりません。 

この問題を解決するのが、Azure Active Directory (Azure AD) の Azure リソースのマネージド ID 機能です。 Azure AD で自動的に管理される ID を Azure サービスに提供する機能となります。 この ID を使用すれば、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。  VM 上で実行されているコードは、VM 内からのみアクセス可能な次の 2 つのエンドポイントにトークンを要求できます。 このサービスの詳細については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の概要ページを確認してください。   

## <a name="policies"></a>ポリシー

社内の [Windows VM](./windows/policy.md) と [Linux VM](./linux/policy.md) には、[Azure ポリシー](../governance/policy/overview.md)を使って必要な動作を定義することができます。 ポリシーを使用すると、さまざまな習慣や規則を企業全体に適用できます。 望ましい行動を強制することによって、組織の成功に貢献しつつ、リスクを軽減することができます。

## <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) を使用すると、チーム内の職務を分離し、VM 上のユーザーに自分の職務を実行するために必要な量のアクセスのみを付与できます。 すべてのユーザーに VM への無制限のアクセス許可を付与するのではなく、特定の操作のみを許可することができます。 VM のアクセス制御は、[Azure Portal](../role-based-access-control/role-assignments-portal.md) で構成できるほか、[Azure CLI](/cli/azure/role) または [Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) を使って構成することもできます。


## <a name="next-steps"></a>次のステップ
- Azure Security Center を使って [Linux](../security/fundamentals/overview.md) または [Windows](/previous-versions/azure/virtual-machines/tutorial-azure-security) の仮想マシンのセキュリティを監視する手順を参照します。