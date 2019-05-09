---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: c44b39effdc6d8fcdc144915ec7b51489e3798cd
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302361"
---
実行するアプリケーションの仮想マシン (VM) は常に安全な状態に保つ必要があります。 VM の安全を確保する手段としては、Azure のサービスや機能を通じて、VM へのアクセスやデータのストレージにセキュリティを確保する方法が挙げられます。 この記事では、VM とアプリケーションのセキュリティを維持するうえで役に立つ情報を提供します。

## <a name="antimalware"></a>マルウェア対策

最近のクラウド環境に対する脅威は変化が激しく、コンプライアンスとセキュリティの要件を満たすために効果的な保護を維持しなければならないという圧力はますます大きくなっています。 [Azure に対する Microsoft マルウェア対策](../articles/security/azure-security-antimalware.md)は、ウイルス、スパイウェアなどの悪意のあるソフトウェアの特定や駆除に役立つリアルタイムの保護機能です。 悪意のあることまたは望ましくないことが確認されているソフトウェアが VM 上で実行されようとしていたり、自らインストールを試みたりした場合に、その事実を把握できるようにアラートを構成することができます。

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../articles/security-center/security-center-intro.md) は、VM に対する脅威の防御、検出、対応を可能にする機能です。 Security Center は、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

Security Center の Just-In-Time アクセスは、VM のデプロイ全体に適用できるため、Azure VM へのインバウンド トラフィックをロックダウンし、VM への接続が必要な場合は簡単にアクセスできるようにしつつ、攻撃に対する露出を減らすことができます。 Just-In-Time が有効で、ユーザー要求が VM にアクセスする場合、ユーザーが VM に対してどのアクセス許可を持っているかが Security Center によって確認されます。 ユーザーが適切なアクセス許可を持っている場合は要求が承認され、Security Center では、選択したポートへのインバウンド トラフィックを制限時間内だけで許可するように、ネットワーク セキュリティ グループ (NSG) が自動的に構成されます。 指定された時間が経過すると、Security Center により NSG が以前の状態に復元されます。 

## <a name="encryption"></a>暗号化

仮想マシン ([Windows VM](../articles/virtual-machines/windows/encrypt-disks.md) および [Linux VM](../articles/virtual-machines/linux/encrypt-disks.md)) のセキュリティとコンプライアンスを強化するために、Azure の仮想ディスクを暗号化できます。 Windows VM の仮想ディスクは、BitLocker を使って保存時に暗号化されます。 Linux VM の仮想ディスクは、dm-crypt を使って暗号化します。 

Azure の仮想ディスクを暗号化するための料金はかかりません。 暗号化キーは、ソフトウェア保護を使って Azure Key Vault に格納されます。または、FIPS 140-2 レベル 2 標準に認定された Hardware Security Module (HSM) でキーをインポートまたは生成することもできます。 これらの暗号化キーは、VM に接続された仮想ディスクの暗号化/暗号化解除に使われます。 これらの暗号化キーの制御を維持し、その使用を監査することができます。 Azure Active Directory サービス プリンシパルは、VM の電源がオンまたはオフになったときにこれらの暗号化キーを発行するためのセキュリティで保護されたメカニズムを提供します。

## <a name="key-vault-and-ssh-keys"></a>Key Vault と SSH キー

シークレットと証明書は、リソースとしてモデル化して [Key Vault](../articles/key-vault/key-vault-whatis.md) で提供することができます。 [Windows VM](../articles/virtual-machines/windows/key-vault-setup.md) のキー コンテナーは Azure PowerShell で、[Linux VM](../articles/virtual-machines/linux/key-vault-setup.md) のキー コンテナーは Azure CLI で作成できます。 暗号化用のキーを作成することもできます。

キー コンテナー アクセス ポリシーでは、キー、シークレット、証明書へのアクセス許可を個別に付与します。 たとえば、ユーザーにキーのみのアクセス権を付与し、シークレットのアクセス権は付与しないようにすることができます。 ただし、キー、シークレット、または証明書へのアクセス権は、コンテナー レベルで付与されます。 つまり、[キー コンテナー アクセス ポリシー](../articles/key-vault/key-vault-secure-your-key-vault.md)では、オブジェクト レベルのアクセス許可がサポートされません。

VM に接続するときは、公開キー暗号化を使用して、より安全な方法で VM にサインインできるようにする必要があります。 このプロセスでは、ユーザー名とパスワードを使用する代わりに、SSH (Secure Shell) コマンドを使用して公開キーと秘密キーを交換して、自分を認証します。 パスワードは、ブルートフォース攻撃に対して脆弱です。これは、特に Web サーバーなどのインターネットに接続された仮想マシンに当てはまります。 Secure Shell (SSH) キー ペアを使用すると、認証に SSH キーを使う [Linux VM](../articles/virtual-machines/linux/mac-create-ssh-keys.md) を作成でき、サインインするためのパスワードが不要になります。 [Windows VM](../articles/virtual-machines/linux/ssh-from-windows.md) から SSH キーを使って Linux VM に接続することもできます。

## <a name="managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID

クラウド アプリケーションの構築時における一般的な課題は、クラウド サービスへの認証用のコードで資格情報をどのように管理するかです。 資格情報を安全に保つことは重要な課題です。 資格情報は開発者のワークステーションに表示されないこと、またソース管理にチェックインされないことが理想です。 資格情報やシークレットなど、各種キーを安全に保管する手段としては Azure Key Vault がありますが、それらを取得するためには、コードから Key Vault に対して認証を行わなければなりません。 

この問題を解決するのが、Azure Active Directory (Azure AD) の Azure リソースのマネージド ID 機能です。 Azure AD で自動的に管理される ID を Azure サービスに提供する機能となります。 この ID を使用すれば、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。  VM 上で実行されているコードは、VM 内からのみアクセス可能な次の 2 つのエンドポイントにトークンを要求できます。 このサービスの詳細については、[Azure リソースのマネージド ID](../articles/active-directory/managed-identities-azure-resources/overview.md) の概要ページを確認してください。   

## <a name="policies"></a>ポリシー

社内の [Windows VM](../articles/virtual-machines/windows/policy.md) と [Linux VM](../articles/virtual-machines/linux/policy.md) には、[Azure ポリシー](../articles/azure-policy/azure-policy-introduction.md)を使って必要な動作を定義することができます。 ポリシーを使用すると、さまざまな習慣や規則を企業全体に適用できます。 望ましい行動を強制することによって、組織の成功に貢献しつつ、リスクを軽減することができます。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

[ロールベースのアクセス制御 (RBAC)](../articles/role-based-access-control/overview.md) を使用すると、チーム内で職務を分離し、職務に必要なアクセス許可のみを VM のユーザーに付与することができます。 すべてのユーザーに VM への無制限のアクセス許可を付与するのではなく、特定の操作のみを許可することができます。 VM のアクセス制御は、[Azure Portal](../articles/role-based-access-control/role-assignments-portal.md) で構成できるほか、[Azure CLI](https://docs.microsoft.com/cli/azure/role) または [Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md) を使って構成することもできます。


## <a name="next-steps"></a>次の手順
- Azure Security Center を使って [Linux](../articles/virtual-machines/linux/tutorial-azure-security.md) または [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md) の仮想マシンのセキュリティを監視する手順を参照します。