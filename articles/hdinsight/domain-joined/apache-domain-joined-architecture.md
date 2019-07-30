---
title: Enterprise セキュリティ パッケージを使用した Azure HDInsight アーキテクチャ
description: Enterprise セキュリティ パッケージを使用して HDInsight のセキュリティを計画する方法について説明します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 8b8c200979b70e145fca64746547b37dee558848
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720436"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>HDInsight で Enterprise セキュリティ パッケージを使用する

標準的な Azure HDInsight クラスターは、シングル ユーザー クラスターです。 これは、大規模なデータ ワークロードを構築する小規模なアプリケーション チームを抱えているほとんどの企業に適しています。 ユーザーごとにオンデマンドの専用クラスターを作成し、不要になったときに破棄することができます。 

多くの企業では、IT チームがクラスターを管理し、複数のアプリケーション チームがクラスターを共有するモデルに移行しています。 これらの大規模企業は、Azure HDInsight での各クラスターへのマルチユーザー アクセスが必要です。

HDInsight は、管理されている方法で、一般的な ID プロバイダーである Active Directory に依存します。 HDInsight を [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/overview.md) と統合することによって、ドメイン資格情報を使用してクラスターにアクセスできます。 

HDInsight での仮想マシン (VM) は、指定されたドメインに参加しているドメインです。 そのため、HDInsight 上で実行されているすべてのサービス (Apache Ambari、Apache Hive サーバー、Apache Ranger、Apache Spark Thrift サーバーなど) は、認証済みユーザーに対してシームレスに動作します。 さらに、管理者は、Apache Ranger を使用して強力な承認ポリシーを作成し、クラスター内のリソースに対するロールベースのアクセス制御を行うことができます。

## <a name="integrate-hdinsight-with-active-directory"></a>HDInsight を Active Directory と統合する

オープンソースの Apache Hadoop は、認証とセキュリティのために Kerberos プロトコルに依存しています。 したがって、Enterprise セキュリティ パッケージ (ESP) を使用する HDInsight クラスター ノードは、Azure AD DS によって管理されるドメインに参加済みです。 クラスター上の Hadoop コンポーネントには Kerberos セキュリティが構成されます。 

自動的に以下のものが作成されます。

- 各 Hadoop コンポーネントのサービス プリンシパル
- ドメインに参加している各マシンのマシン プリンシパル
- このようなサービスおよびマシンのプリンシパルを格納する各クラスターの組織単位 (OU)

これらの点を総合すると、次の要素から成る環境をセットアップする必要があります。

- Active Directory ドメイン (Azure AD DS で管理)。 **Azure HDInsight で使用するドメイン名は、39 文字以下である必要があります。**
- Azure AD DS で有効になっている Secure LDAP (LDAPS)。
- 個別の仮想ネットワークを選ぶ場合の HDInsight 仮想ネットワークから Azure AD DS 仮想ネットワークへの適切なネットワーク接続。 HDInsight 仮想ネットワーク内の VM は、仮想ネットワーク ピアリングを使用して Azure AD DS への通信経路を持つ必要があります。 HDInsight と Azure AD DS が同じ仮想ネットワーク内にデプロイされている場合、接続は自動的に提供されるので、これ以上アクションは必要ありません。

## <a name="set-up-different-domain-controllers"></a>異なるドメイン コントローラーの設定
現在、HDInsight では、クラスターの Kerberos 認証に使用するメイン ドメイン コントローラーとして、Azure AD DS だけがサポートされています。 ただし、設定によって HDInsight アクセスに対する Azure AD DS が有効にされることになる場合に限り、その他の複雑な Active Directory 設定が可能です。

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services
[Azure AD DS](../../active-directory-domain-services/overview.md) では、Windows Server Active Directory と完全に互換性のあるマネージド ドメインを提供します。 Microsoft は、高可用性 (HA) 設定のドメインの管理、修正プログラムの適用、監視を行います。 ドメイン コントローラーの管理について心配することなく、クラスターをデプロイすることができます。 

ユーザー、グループ、パスワードは、Azure AD から同期されます。 Azure AD インスタンスから Azure AD DS への一方向の同期により、同じ会社の資格情報を使用して、ユーザーがクラスターにサインインできます。 

詳細については、[Azure AD DS を使用する ESP への HDInsight クラスター構成](./apache-domain-joined-configure-using-azure-adds.md)に関する記事を参照してください。

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>オンプレミス Active Directory または IaaS VM 上の Active Directory

自分のドメインにオンプレミス Active Directory インスタンスまたはより複雑な Active Directory 設定がある場合、Azure AD Connect を使用してそれらの ID を Azure AD と同期することができます。 その後、Active Directory テナント上で Azure AD DS を有効にできます。 

Kerberos はパスワード ハッシュに依存するため、[Azure AD DS でパスワード ハッシュ同期を有効にする](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)必要があります。 

Active Directory フェデレーション サービス (AD FS) によるフェデレーションを使用している場合は、パスワード ハッシュ同期を有効にする必要があります(推奨される設定については、[こちらのビデオ](https://youtu.be/qQruArbu2Ew)をご覧ください)。パスワード ハッシュ同期は、AD FS インフラストラクチャに障害が発生した場合のディザスター リカバリーに役立ちます。また、漏えいした資格情報を保護するためにも役立ちます。 詳細については、「[Azure AD Connect 同期を使用したパスワード ハッシュ同期の実装](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)」を参照してください。 

Azure AD と Azure AD DS を使用せずに、IaaS VM 単独でオンプレミスの Azure AD または Azure AD DS を使用する構成は、ESP を使用する HDInsight クラスターではサポートされていません。

フェデレーションの使用時、パスワード ハッシュが正常に同期されていても認証エラーが発生する場合は、PowerShell サービス プリンシパルのクラウド パスワード認証が有効かどうかを確認します。 有効でない場合は、Azure AD テナントに[ホーム領域の検出 (HRD) ポリシー](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)を設定する必要があります。 HRD ポリシーを確認し、設定するには:

1. プレビューの [Azure AD PowerShell モジュール](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)をインストールします。

   ```powershell
   Install-Module AzureAD
   ```

2. グローバル管理者 (テナント管理者) の資格情報を使用して接続します。
   
   ```powershell
   Connect-AzureAD
   ```

3. Microsoft Azure PowerShell サービス プリンシパルが既に作成されているかどうかを確認します。

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. 存在しない場合は、サービス プリンシパルを作成します。

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. ポリシーを作成し、このサービス プリンシパルにアタッチします

   ```powershell
    # Determine whether policy exists
    Get-AzureADPolicy | Where {$_.DisplayName -eq "EnableDirectAuth"}

    # Create if not exists
    $policy = New-AzureADPolicy `
        -Definition @('{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}') `
        -DisplayName "EnableDirectAuth" `
        -Type "HomeRealmDiscoveryPolicy"

    # Determine whether a policy for the service principal exist
    Get-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId
    
    # Add a service principal policy if not exist
    Add-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId `
        -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>次の手順

* [ESP を使用する HDInsight クラスターを構成する](apache-domain-joined-configure-using-azure-adds.md)
* [ESP を使用する HDInsight クラスターのために Apache Hive ポリシーを構成する](apache-domain-joined-run-hive.md)
* [ESP を使用する HDInsight クラスターを管理する](apache-domain-joined-manage.md) 
