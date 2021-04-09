---
title: Azure HDInsight でのエンタープライズ セキュリティの一般的なガイドライン
description: Enterprise セキュリティ パッケージのデプロイと管理を容易にするためのいくつかのベスト プラクティス。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 92ad8362f75cdf0613d4ee95f39c23aa6d4819bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933575"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Azure HDInsight でのエンタープライズ セキュリティの一般情報とガイドライン

セキュリティで保護された HDInsight クラスターをデプロイする場合、デプロイとクラスターの管理を容易にするためのベスト プラクティスがいくつかあります。 ここでは、いくつかの一般情報とガイドラインについて説明します。

## <a name="use-of-secure-cluster"></a>セキュリティで保護されたクラスターの使用

### <a name="recommended"></a>推奨

* クラスターが同時に複数のユーザーによって使用される。
* ユーザーによって同じデータに対するアクセス レベルが異なる。

### <a name="not-necessary"></a>不要

* (単一ユーザー アカウントのように) 自動化ジョブのみを実行する場合は、Standard クラスターで十分です。
* Standard クラスターを使用してデータのインポートを実行し、ユーザーが分析ジョブを実行できる、セキュリティで保護された別のクラスターで同じストレージ アカウントを使用することができます。

## <a name="use-of-local-account"></a>ローカル アカウントの使用

* 共有ユーザー アカウントまたはローカル アカウントを使用している場合、そのアカウントを使用して構成またはサービスを変更したユーザーを特定するのは困難になります。
* ローカル アカウントの使用は、ユーザーが組織の一員ではなくなった場合に問題になります。

## <a name="ranger"></a>Ranger

### <a name="policies"></a>ポリシー

* Ranger では、既定で、ポリシーとして **拒否** を使用します。

* 承認が有効になっているサービスを介してデータ アクセスが行われる場合:
  * Ranger 承認プラグインが呼び出され、要求のコンテキストが与えられます。
  * Ranger は、サービス用に構成されたポリシーを適用します。 Ranger ポリシーが失敗すると、アクセス チェックはファイル システムに委ねられます。 MapReduce などの一部のサービスでは、要求を送信している同じユーザーによってファイルまたはフォルダーが所有されているかどうかのみを確認します。 Hive のようなサービスでは、所有権の一致または適切なファイル システムのアクセス許可 (`rwx`) を確認します。

* Hive の場合、ユーザーは、アクセス許可の作成、更新、削除を行うアクセス許可を持っていることに加えて、ストレージ上のディレクトリとすべてのサブディレクトリに対する `rwx` アクセス許可を持っている必要があります。

* ポリシーは、個人ではなくグループに適用できます (推奨)。

* Ranger の承認者は、要求ごとにそのサービスの Ranger ポリシーすべてを評価します。 この評価は、ジョブまたはクエリの受け入れにかかる時間に影響を与える可能性があります。

### <a name="storage-access"></a>ストレージ アクセス

* ストレージの種類が WASB の場合、OAuth トークンは含まれません。
* Ranger が承認を実行した場合、ストレージ アクセスはマネージド ID を使用して行われます。
* Ranger が承認を実行しなかった場合、ストレージ アクセスはユーザーの OAuth トークンを使用して行われます。

### <a name="hierarchical-name-space"></a>階層型名前空間

階層型名前空間が有効になっていない場合:

* 継承されたアクセス許可はありません。
* ファイル システムのアクセス許可のうち、唯一機能するのは **ストレージ データ XXXX** Azure ロールで、Azure portal でユーザーに直接割り当てられます。

### <a name="default-hdfs-permissions"></a>既定の HDFS アクセス許可

* 既定では、ユーザーは HDFS 上の **/** フォルダーにアクセスできません (アクセスを成功させるためには、ストレージ BLOB 所有者ロールに属している必要があります)。
* MapReduce などのステージング ディレクトリでは、ユーザー固有のディレクトリが作成され、`sticky _wx` アクセス許可が提供されます。 ユーザーはその下にファイルとフォルダーを作成できますが、他の項目は表示されません。

### <a name="url-auth"></a>URL 承認

URL 承認が有効になっている場合:

* 構成には、URL 承認で扱われるプレフィックスが含まれます (`adl://` など)。
* アクセスがこの URL を対象とする場合、Ranger は、ユーザーが許可リストに含まれているかどうかを確認します。
* Ranger では、細かい設定が可能なポリシーは確認されません。

## <a name="resource-groups"></a>リソース グループ

クラスター リソースを区別できるように、クラスターごとに新しいリソース グループを使用します。

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSG、ファイアウォール、内部ゲートウェイ

* ネットワーク セキュリティ グループ (NSG) を使用して、仮想ネットワークをロックダウンします。
* ファイアウォールを使用して、アウトバウンド アクセス ポリシーを処理します。
* パブリック インターネットに対して開かれていない内部ゲートウェイを使用します。

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) は、Microsoft のクラウドベースの ID およびアクセス管理サービスです。

### <a name="policies"></a>ポリシー

* IP アドレス ベースのポリシーを使用して、条件付きアクセス ポリシーを無効にします。 これには、クラスターがデプロイされている VNET でサービス エンドポイントを有効にする必要があります。 MFA 用に外部サービス (AAD 以外) を使用する場合、IP アドレス ベースのポリシーが動作しません。

* フェデレーション ユーザーには `AllowCloudPasswordValidation` ポリシーが必要です。 HDInsight は Azure AD からトークンを取得するためにユーザー名とパスワードを直接使用するため、このポリシーは、すべてのフェデレーション ユーザーに対して有効にする必要があります。

* 信頼できる IP を使用した条件付きアクセス バイパスが必要な場合は、サービス エンドポイントを有効にします。

### <a name="groups"></a>グループ

* クラスターをデプロイする場合は常にグループを使用します。
* Azure AD を使用して、グループのメンバーシップを管理します (クラスター内の個々のサービスを管理するよりも簡単です)。

### <a name="user-accounts"></a>ユーザー アカウント

* シナリオごとに一意のユーザー アカウントを使用します。 たとえば、インポートにはあるアカウント、クエリやその他の処理ジョブには別のアカウントを使用します。
* 個々のポリシーではなく、グループベースの Ranger ポリシーを使用します。
* クラスターにアクセスする必要がなくなったユーザーを管理する方法について計画を立てます。

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) では、Windows Server Active Directory と完全に互換性のあるマネージド ドメイン サービス (ドメイン参加、グループ ポリシー、ライトウェイト ディレクトリ アクセス プロトコル (LDAP)、Kerberos 認証、NTLM 認証など) が提供されます。

セキュリティで保護されたクラスターがドメインに参加するには、Azure AD DS が必要です。
HDInsight は、オンプレミスのドメイン コントローラーまたはカスタム ドメイン コントローラーに依存できません。これは、過剰なフォールト ポイント、資格情報の共有、DNS アクセス許可などが伴うためです。 詳細については、[Azure AD DS の FAQ](../../active-directory-domain-services/faqs.md) に関するページを参照してください。

### <a name="azure-ad-ds-instance"></a>Azure AD DS インスタンス

* `.onmicrosoft.com domain` を使用してインスタンスを作成します。 こうすると、ドメインにサービスを提供する DNS サーバーは複数存在しません。
* LDAPS 用の自己署名証明書を作成し、Azure AD DS にアップロードします。
* クラスターのデプロイに、ピアリングされた仮想ネットワークを使用します (HDInsight ESP クラスターをデプロイするチームが多数ある場合はこれが役立ちます)。 これにより、ドメイン コントローラーを使用して仮想ネットワーク上のポート (NSG) を開く必要がなくなります。
* 仮想ネットワークの DNS を適切に構成します (Azure AD DS ドメイン名は、ホスト ファイル エントリなしで解決する必要があります)。
* アウトバウンド トラフィックを制限している場合は、[HDInsight でのファイアウォールのサポート](../hdinsight-restrict-outbound-traffic.md)に関するページを必ずお読みください。

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Azure AD から Azure AD DS に同期されるプロパティ

* Azure AD Connect では、オンプレミスから Azure AD に同期されます。
* Azure AD DS では、Azure AD から同期されます。

Azure AD DS では、Azure AD のオブジェクトが定期的に同期されます。 Azure portal の Azure AD DS ブレードには、同期状態が表示されます。 同期の各ステージでは、一意のプロパティが競合状態になり、名前が変更されることがあります。 Azure AD から Azure AD DS へのプロパティ マッピングに注意してください。

詳細については、「[Azure AD の UserPrincipalName の設定](../../active-directory/hybrid/plan-connect-userprincipalname.md)」および [Azure AD DS の同期のしくみ](../../active-directory-domain-services/synchronization.md)に関するページを参照してください。

### <a name="password-hash-sync"></a>パスワード ハッシュの同期

* パスワードの同期は、他のオブジェクトの種類とは異なります。 Azure AD と Azure AD DS で同期されるのは、不可逆のパスワード ハッシュのみです。
* AD Connect を介して、オンプレミスから Azure AD を有効にする必要があります。
* Azure AD から Azure AD DS への同期は自動的に行われます (待ち時間は 20 分未満です)。
* パスワード ハッシュが同期されるのは、変更されたパスワードがある場合のみです。 パスワード ハッシュの同期を有効にすると、既存のパスワードはすべて、不可逆的に保存されるため、自動的に同期されません。 パスワードを変更すると、パスワード ハッシュが同期されます。

### <a name="computer-objects-location"></a>コンピューター オブジェクトの場所

各クラスターは、単一の OU に関連付けられています。 内部ユーザーは、OU 内にプロビジョニングされます。 すべてのノードは、同じ OU にドメイン参加しています。

### <a name="active-directory-administrative-tools"></a>Active Directory 管理ツール

Active Directory 管理ツールを Windows Server VM にインストールする手順については、[管理ツールのインストール](../../active-directory-domain-services/tutorial-create-management-vm.md)に関するページを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="cluster-creation-fails-repeatedly"></a>クラスターの作成に繰り返し失敗する

最も一般的な理由:

* DNS 構成が正しくないため、クラスター ノードのドメイン参加に失敗する。
* NSG の制限が厳しすぎるため、ドメイン参加が阻止される。
* マネージド ID に十分なアクセス許可がない。
* (別のライブ クラスターまたは削除されたクラスターに対して) クラスター名の最初の 6 文字が一意ではない。

## <a name="authentication-setup-and-configuration"></a>認証のセットアップと構成

### <a name="user-principal-name-upn"></a>ユーザー プリンシパル名 (UPN)

* すべてのサービスに小文字を使用してください。UPN は ESP クラスターでは大文字と小文字が区別されませんが、
* Azure AD-DS では、UPN プレフィックスが両方の SAMAccountName に一致する必要があります。 メール フィールドとの一致は必要ありません。

### <a name="ldap-properties-in-ambari-configuration"></a>Ambari 構成での LDAP プロパティ

HDInsight クラスター構成に影響を与える Ambari プロパティの完全な一覧については、[Ambari LDAP 認証のセットアップ](https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap2-4.html)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [HDInsight での Azure Active Directory Domain Services を使用した Enterprise セキュリティ パッケージの構成](./apache-domain-joined-configure-using-azure-adds.md)

* [Azure Active Directory ユーザーを HDInsight クラスターに同期する](../hdinsight-sync-aad-users-to-cluster.md)
