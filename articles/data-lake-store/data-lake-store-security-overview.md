---
title: Azure Data Lake Storage Gen1 のセキュリティの概要 | Microsoft Docs
description: 認証、承認、ネットワーク分離、データ保護、監査など、Azure Data Lake Storage Gen1 のセキュリティ機能について説明します。
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: twooley
ms.openlocfilehash: 240018381a3139a6378141d78514e43ae469de5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92146296"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 のセキュリティ

多くの企業が、ビッグ データの分析によってビジネスに関する洞察を獲得し、それを意思決定に活かしています。 ただ、一部の組織はユーザーが多様化し、その数も増加の一途を辿っているばかりか、各種の規制が存在する複雑な環境に直面しています。 このため、重要なビジネス データはこれまで以上に強固なセキュリティをもって保管すると同時に、個々のユーザーには適切な水準のアクセス権を付与することがきわめて重要になっています。 Azure Data Lake Storage Gen1 は、このようなセキュリティ要件を満たすことを目指して設計されています。 この記事では、以下に示す Data Lake Storage Gen1 のセキュリティ機能について説明します。

* 認証
* 承認
* ネットワークの分離
* データ保護
* 監査

## <a name="authentication-and-identity-management"></a>認証と ID 管理

認証とは、ユーザーが Data Lake Storage Gen1 または Data Lake Storage Gen1 に接続しているサービスとやり取りするにあたり、ユーザーの ID を確認するプロセスを指します。 Data Lake Storage Gen1 では、ID の管理と認証に [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) を使用します。Azure Active Directory は、ユーザーとグループの管理を簡素化する包括的な ID およびアクセス管理クラウド ソリューションです。

Azure サブスクリプションはそれぞれ、Azure Active Directory のインスタンスに関連付けることができます。 ユーザーとサービスの ID のうち、Azure Active Directory サービスで定義されているものに限り、Data Lake Storage Gen1 アカウントにアクセスできます。アカウントへのアクセスには、Azure portal、コマンドライン ツールのほか、組織が Data Lake Storage Gen1 SDK を使って作成したクライアント アプリケーションを使用できます。 一元化されたアクセス制御メカニズムとして Azure Active Directory を使用する主な利点は次のとおりです。

* ID ライフサイクル管理の簡素化。 ユーザーまたはサービスの ID (サービス プリンシパル ID) を迅速に作成することができ、ディレクトリでアカウントを削除または無効にするだけで ID を取り消すことができます。
* 多要素認証。 [多要素認証](../active-directory/authentication/concept-mfa-howitworks.md) により、ユーザーのサインインとトランザクションにセキュリティ レイヤーを追加できます。
* OAuth、OpenID などの標準的なオープン プロトコルを使ったクライアント認証。
* エンタープライズ ディレクトリ サービスおよびクラウド ID プロバイダーとのフェデレーション。

## <a name="authorization-and-access-control"></a>承認とアクセス制御

Azure Active Directory の認証によってユーザーが Data Lake Storage Gen1 にアクセスできるようになった後は、承認によって Data Lake Storage Gen1 のアクセス許可を制御します。 Data Lake Storage Gen1 では、アカウント関連アクティビティとデータ関連アクティビティの承認が次のように区別されます。

* アカウント管理のための [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md)
* ストア内のデータにアクセスするための POSIX ACL

### <a name="azure-rbac-for-account-management"></a>アカウント管理のための Azure RBAC

Data Lake Storage Gen1 には既定で基本のロールが 4 つ定義されています。 これらのロールでは、Azure portal、PowerShell コマンドレット、REST API を使用して Data Lake Storage Gen1 アカウントに実行できる操作が異なります。 所有者ロールと共同作成者ロールは、アカウントに対してさまざまな管理機能を実行できます。 アカウント管理データを表示するだけのユーザーには閲覧者ロールを割り当てることができます。

![Azure ロール](./media/data-lake-store-security-overview/rbac-roles.png "Azure ロール")

ロールはアカウント管理のために割り当てられるものですが、一部のロールはデータへのアクセスにも影響しますので注意してください。 ユーザーがファイル システムに対して実行できる操作へのアクセス制御には、ACL を使用する必要があります。 以下の表は、既定のロールの管理権限とデータ アクセス権限の概要を示したものです。

| ロール | 管理権限 | データ アクセス権限 | 説明 |
| --- | --- | --- | --- |
| ロールの割り当てなし |なし |ACL によって管理 |Azure portal または Azure PowerShell コマンドレットを使って Data Lake Storage Gen1 を参照することはできません。 ユーザーは、コマンドライン ツールのみ使用できます。 |
| 所有者 |All |All |所有者ロールは、スーパーユーザーです。 このロールではあらゆるものを管理できるほか、データに対するフル アクセスが認められます。 |
| Reader |読み取り専用 |ACL によって管理 |閲覧者ロールでは、どのユーザーがどのロールに割り当てられているかなど、アカウント管理に関するあらゆる情報を表示できます。 ただし、閲覧者ロールでは変更を加えることはできません。 |
| Contributor |ロールの追加と削除を除くすべて |ACL によって管理 |共同作業者ロールでは、デプロイ、アラートの作成と管理など、アカウントの一部の側面を管理できます。 共同作業者ロールでは、ロールを追加または削除することはできません。 |
| User Access Administrator |ロールの追加と削除 |ACL によって管理 |ユーザー アクセス管理者ロールでは、アカウントへのユーザー アクセスを管理できます。 |

手順については、[ユーザーまたはセキュリティ グループを Data Lake Storage Gen1 アカウントに割り当てる](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts)方法に関するセクションをご覧ください。

### <a name="using-acls-for-operations-on-file-systems"></a>ACL を使用したファイル システムでの操作

Data Lake Storage Gen1 は Hadoop 分散ファイル システム (HDFS) と同じく階層構造を備えたファイル システムであり、[POSIX ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists)をサポートしています。 所有者ロール、所有者グループ、その他のユーザーおよびグループのリソースに対する読み取り (r)、書き込み (w) および実行 (x) のアクセス許可を制御します。 Data Lake Storage Gen1 では、ルート フォルダー、サブフォルダー、個々のファイルで ACL を有効にすることができます。 Data Lake Storage Gen1 のコンテキストにおける ACL のしくみの詳細については、[Data Lake Storage Gen1 でのアクセス制御](data-lake-store-access-control.md)に関するページを参照してください。

複数ユーザーを対象に ACL を定義するときは、 [セキュリティ グループ](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)を使うことをお勧めします。 ユーザーをセキュリティ グループに追加し、ファイルまたはフォルダーの ACL をそのセキュリティ グループに割り当てます。 割り当てられたアクセス許可のエントリ数は最大 28 に制限されているため、これは割り当てられたアクセス許可を提供する場合に役立ちます。 Azure Active Directory のセキュリティ グループを使用して Data Lake Storage Gen1 に保存されているデータのセキュリティを強化する方法について詳しくは、「[ユーザーまたはセキュリティ グループを ACL として Data Lake Storage Gen1 ファイル システムに割り当てる](data-lake-store-secure-data.md#filepermissions)」をご覧ください。

![アクセス許可を一覧表示する](./media/data-lake-store-security-overview/adl.acl.2.png "アクセス許可を一覧表示する")

## <a name="network-isolation"></a>ネットワークの分離

データ ストアに対するアクセスをネットワーク レベルで制御する場合にも、Data Lake Storage Gen1 が役立ちます。 ファイアウォールを設定し、信頼されたクライアントの IP アドレス範囲を定義できます。 IP アドレス範囲が定義されていると、IP アドレスがその範囲に該当するクライアントだけが Data Lake Storage Gen1 に接続できます。

![ファイアウォール設定と IP アクセス](./media/data-lake-store-security-overview/firewall-ip-access.png "ファイアウォール設定と IP アドレス")

Azure 仮想ネットワーク (VNet) では、Data Lake Gen 1 のサービス タグをサポートしています。 サービス タグは、指定された Azure サービスからの IP アドレス プレフィックスのグループを表します。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。 詳細については、[Azure サービス タグの概要](../virtual-network/service-tags-overview.md)に関するページを参照してください。

## <a name="data-protection"></a>データ保護

Data Lake Storage Gen1 は、データをそのライフ サイクル全体にわたって保護します。 転送中のデータについては、Data Lake Storage Gen1 では業界標準のトランスポート層セキュリティ (TLS 1.2) プロトコルを使用して、ネットワーク上のデータを保護します。

![Data Lake Storage Gen1 の暗号化](./media/data-lake-store-security-overview/adls-encryption.png "Data Lake Storage Gen1 の暗号化")

Data Lake Storage Gen1 では、アカウントに格納されているデータを暗号化することもできます。 データを暗号化するかどうかを選択できます。 暗号化することを選択した場合、Data Lake Storage Gen1 に保存されているデータは、永続メディアに保存される前に暗号化されます。 そのような場合、Data Lake Storage Gen1 によって、データは保存前に暗号化され、取得前に復号されます。データにアクセスするクライアントにとっては完全に透過的となります。 データの暗号化および暗号化解除を行うためにクライアント側でコードを変更する必要はありません。

キー管理については、Data Lake Storage Gen1 では、MEK (マスター暗号化キー) を管理するためのモードが 2 つ提供されます。この鍵は、Data Lake Storage Gen1 に保存されているデータを復号する際に必要です。 Data Lake Storage Gen1 に MEK の管理を任せることも、Azure Key Vault アカウントを使用して MEK の所有権を保持することもできます。 キー管理のモードは、Data Lake Storage Gen1 アカウントの作成時に指定します。 暗号化関連の構成を提供する方法の詳細については、「[Azure Portal で Azure Data Lake Storage Gen1 の使用を開始する](data-lake-store-get-started-portal.md)」をご覧ください。

## <a name="activity-and-diagnostic-logs"></a>アクティビティ ログと診断ログ

アカウント管理関連のアクティビティまたはデータ関連のアクティビティのどちらのログを探しているかに応じて、アクティビティ ログまたは診断ログを使用できます。

* アカウント管理関連のアクティビティは Azure Resource Manager API を使用し、アクティビティ ログを使用して Azure Portal に表示されます。
* データ関連のアクティビティでは、WebHDFS REST API を使用します。これらのアクティビティは、診断ログを使用して Azure ポータルに表示されます。

### <a name="activity-log"></a>アクティビティ ログ

組織は、特定のインシデントを詳しく調査する必要がある場合、規制に準拠するためにアカウント管理アクティビティの十分な監査証跡が必要になることがあります。 Data Lake Storage Gen1 は組み込みの監視機能を備えており、すべてのアカウント管理アクティビティをログに記録します。

アカウント管理の監査証跡では、ログが必要な列を選んで表示できます。 また、アクティビティ ログを Azure Storage にエクスポートすることもできます。

![アクティビティ ログ](./media/data-lake-store-security-overview/activity-logs.png "アクティビティ ログ")

アクティビティ ログの操作の詳細については、「[リソースのアクションを監査するアクティビティ ログの表示](../azure-resource-manager/management/view-activity-logs.md)」を参照してください。

### <a name="diagnostics-logs"></a>診断ログ

Azure portal でデータ アクセス監査および診断ログを有効にし、それらのログを Azure Blob Storage アカウント、イベント ハブ、または Azure Monitor ログに送信できます。

![診断ログ](./media/data-lake-store-security-overview/diagnostic-logs.png "診断ログ")

Data Lake Storage Gen1 の診断ログの操作の詳細については、「[Data Lake Storage Gen1 の診断ログへのアクセス](data-lake-store-diagnostic-logs.md)」を参照してください。

## <a name="summary"></a>まとめ

企業顧客は、セキュリティで保護された使いやすいデータ分析クラウド プラットフォームを求めています。 Data Lake Storage Gen1 は、Azure Active Directory 統合による ID 管理と認証、ACL ベースの承認、ネットワーク分離、転送中のデータや保存データの暗号化、監査を通して、これらの要件への対処に役立つように設計されています。

Data Lake Storage Gen1 に追加を希望する機能がある場合には、[Data Lake Storage Gen1 UserVoice フォーラム](https://feedback.azure.com/forums/327234-data-lake)からフィードバックをお送りください。

## <a name="see-also"></a>関連項目

* [Azure Data Lake Storage Gen1 の概要](data-lake-store-overview.md)
* [Data Lake Storage Gen1 の使用を開始する](data-lake-store-get-started-portal.md)
* [Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)