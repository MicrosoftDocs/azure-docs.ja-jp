---
title: セキュリティ機能の概要
description: バックアップ データを保護し、ビジネスのセキュリティ ニーズを満たすのに役立つ Azure Backup のセキュリティ機能について説明します。
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 9aa1909f1590b477d9a7f7a09ad0c2b1936e3e29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96325657"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Azure Backup のセキュリティ機能の概要

データを保護するために実行できる非常に重要な手順の 1 つは、信頼性の高いバックアップ インフラストラクチャを使用することです。 しかし、データを安全な方法でバックアップすること、およびバックアップを常に保護することも同様に重要です。 Azure Backup は、データの転送時と保存時の両方で、バックアップ環境にセキュリティを提供します。 この記事では、バックアップ データを保護し、ビジネスのセキュリティ ニーズを満たすのに役立つ Azure Backup のセキュリティ機能を示します。

## <a name="management-and-control-of-identity-and-user-access"></a>ID とユーザー アクセスの管理と制御

Recovery Services コンテナーによって使用されるストレージ アカウントは分離されており、悪意のある目的でユーザーがアクセスすることはできません。 アクセスが許可されるのは、復元などの Azure Backup 管理操作だけです。 Azure Backup では、[Azure のロールベースのアクセス制御 (Azure RBAC)](./backup-rbac-rs-vault.md) を使用したきめ細かなアクセスを通して、マネージド型の操作を管理することができます。 Azure RBAC を使用すると、チーム内で職務を分離し、職務を実行するために必要なアクセス権のみをユーザーに付与することができます。

Azure Backup では、バックアップの管理操作を制御する 3 つの[組み込みロール](../role-based-access-control/built-in-roles.md)が提供されます。

* バックアップ共同作成者 - Recovery Services コンテナーの削除と他のユーザーへのアクセス権の付与を除き、バックアップの作成と管理を行います
* バックアップ オペレーター - バックアップの削除とバックアップ ポリシーの管理を除き、共同作成者が行うすべての操作を行います
* バックアップ リーダー - すべてのバックアップ管理操作を表示するアクセス許可

[Azure Backup を管理するための Azure ロールベースのアクセス制御](./backup-rbac-rs-vault.md)について確認してください。

Azure Backup には、セキュリティ脆弱性の防止、検出、および対応を行うための、サービスに組み込まれているセキュリティ コントロールがいくつかあります。 [Azure Backup のセキュリティ コントロール](./security-baseline.md)について確認してください。

## <a name="separation-between-guest-and-azure-storage"></a>ゲストと Azure ストレージの分離

仮想マシンのバックアップと、VM のバックアップの SQL と SAP HANA を含む Azure Backup では、バックアップ データは Azure ストレージに格納され、ゲストはバックアップ ストレージやそのコンテンツに直接アクセスすることはできません。  仮想マシンのバックアップでは、バックアップス ナップショットの作成と保存は Azure のファブリックによって行われ、ゲストはアプリケーションの整合性バックアップのためにワークロードを停止する以外には何も関与しません。  SQL と SAP HANA では、バックアップ拡張機能によって、特定の BLOB に書き込むための一時的なアクセス権が取得されます。  こうすることで、セキュリティが侵害された環境でも、ゲストが既存のバックアップを改ざんしたり削除したりすることはできません。

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Azure VM バックアップにはインターネット接続は不要

Azure VM のバックアップを行うには、お使いの仮想マシンのディスクから Recovery Services コンテナーにデータを移動する必要があります。 ただし、必要な通信やデータ転送はすべて Azure バックボーン ネットワーク上でのみ行われ、仮想ネットワークにアクセスする必要はありません。 そのため、セキュリティで保護されたネットワーク内に配置された Azure VM のバックアップでは、IP や FQDN へのアクセスを許可する必要がありません。

## <a name="private-endpoints-for-azure-backup"></a>Azure Backup のプライベート エンドポイント

[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用して、仮想ネットワーク内のサーバーから Recovery Services コンテナーにデータを安全にバックアップできるようになりました。 プライベート エンドポイントでは、お使いのコンテナーには VNET アドレス空間に属する IP が使用されるため、どのパブリック IP にも仮想ネットワークを公開する必要はありません。 プライベート エンドポイントを使用して、お使いの Azure VM 内で実行される SQL データベースと SAP HANA データベースのバックアップと復元を行うことができます。 また、MARS エージェントを使用して、オンプレミス サーバーでも使用できます。

[こちら](./private-endpoints.md)から、Azure Backup のプライベート エンドポイントの詳細を参照してください。

## <a name="encryption-of-data"></a>データの暗号化

暗号化によりお使いのデータが保護され、組織のセキュリティとコンプライアンスのコミットメントを満たすのに役立ちます。 データの暗号化は、Azure Backup の多くの段階で行われます。

* Azure 内では、Azure ストレージとコンテナー間を転送中のデータは、[HTTPS によって保護](backup-support-matrix.md#network-traffic-to-azure)されます。 このデータは、Azure バックボーン ネットワークにとどまります。

* バックアップ データは、[プラットフォーム マネージド キー](backup-encryption.md)で自動的に暗号化され、これは明示的に操作を実行しなくても有効になっています。 Azure Key Vault に格納されている[カスタマー マネージド キー](encryption-at-rest-with-cmk.md)を使用して、バックアップされたデータを暗号化することもできます。 これは、Recovery Services コンテナーにバックアップ中のすべてのワークロードに適用されます。

* Azure Backup では、[Azure Disk Encryption](backup-azure-vms-encryption.md#encryption-support-using-ade) (ADE) で OS ディスクやデータ ディスクを暗号化している Azure VM と、[CMY で暗号化されたディスクを使用する VM](backup-azure-vms-encryption.md#encryption-using-customer-managed-keys) のバックアップと復元をサポートしています。 詳細については、[暗号化される Azure VM と Azure Backup について、より詳しく学習してください](./backup-azure-vms-encryption.md)。

* データがオンプレミスのサーバーから MARS エージェントを使用してバックアップされるときに、データは Azure Backup にアップロードする前にパスフレーズを使用して暗号化され、Azure Backup からダウンロードされた後でのみ、暗号化が解除されます。 [ハイブリッド バックアップの保護に役立つセキュリティ機能](#security-features-to-help-protect-hybrid-backups)について、詳細を確認してください。

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>意図しない削除からのバックアップ データの保護

Azure Backup には、削除後もバックアップ データを保護するためのセキュリティ機能が用意されています。 論理的な削除では、ユーザーが VM のバックアップを削除した場合、バックアップ データは追加で 14 日間保持されるので、データを失うことなくそのバックアップ項目を回復できます。 バックアップ データが "論理的な削除" 状態にあるこの追加の 14 日間の保有期間中は、ユーザーへのコストは発生しません。 [論理的な削除の詳細については、こちらを参照してください](backup-azure-security-feature-cloud.md)。

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>疑わしいアクティビティの監視とアラート

Azure Backup には、Azure Backup に関連するイベントのアクションを表示および構成する[組み込みの監視とアラートの機能](./backup-azure-monitoring-built-in-monitor.md)が用意されています。 [バックアップ レポート](./configure-reports.md)は、使用状況の追跡、バックアップと復元の監査、およびさまざまな細分性レベルでの主要な傾向の特定を行うための、ワンストップの宛先として機能します。 Azure Backup の監視およびレポート ツールを使用すると、承認されていない、疑わしい、または悪意のあるアクティビティが発生した場合に直ちにアラートを受け取ることができます。

## <a name="security-features-to-help-protect-hybrid-backups"></a>ハイブリッド バックアップを保護するためのセキュリティ機能

Azure Backup サービスは、Microsoft Azure Recovery Services (MARS) エージェントを使用して、ファイル、フォルダー、およびボリュームまたはシステム状態をオンプレミスのコンピューターから Azure にバックアップおよび復元します。 MARS では、ハイブリッド バックアップの保護に役立つセキュリティ機能が提供されるようになりました。 次のような機能が該当します。

* パスフレーズの変更など、重要な操作を実行するたびに、認証レイヤーが追加されます。 この検証により、有効な Azure 資格情報を持つユーザーのみがそのような操作を実行できるようになります。 [攻撃を防ぐ機能の詳細については、こちらを参照してください](./backup-azure-security-feature.md#prevent-attacks)。

* 削除されたバックアップ データは、削除日から追加で 14 日間保持されます。 これにより、特定の期間内のデータの復旧性が確保され、攻撃が行われてもデータが失われることはありません。 また、保持される最小復旧ポイントの数が非常に増えたため、データの破損を防ぐことができます。 [削除されたバックアップ データの復旧の詳細については、こちらを参照してください](./backup-azure-security-feature.md#recover-deleted-backup-data)。

* Microsoft Azure Recovery Services (MARS) エージェントを使用してバックアップされたデータの場合は、Azure Backup にアップロードする前にデータが暗号化され、Azure Backup からダウンロードした後にのみ暗号化が解除されるようにするために、パスフレーズが使用されます。 パスフレーズの詳細は、パスフレーズを作成したユーザーと、それを使用して構成されたエージェントでのみ使用できます。 そのサービスとの間で転送または共有されるものはありません。 これにより、(ネットワーク上の中間者攻撃などで) 誤って公開されたデータはパスフレーズなしで使用できず、パスフレーズはネットワーク経由で送信されないため、データの完全なセキュリティが確保されます。

## <a name="compliance-with-standardized-security-requirements"></a>標準化されたセキュリティ要件への準拠

個人データの収集と使用に関する国、地域、業界固有の要件に組織が準拠できるように、Microsoft Azure と Azure Storage では、包括的な認定と認証を提供しています。 [コンプライアンス認定の一覧については、こちらを参照してください](compliance-offerings.md)

## <a name="next-steps"></a>次のステップ

* [Azure Backup を使用したクラウド ワークロードを保護するためのセキュリティ機能](backup-azure-security-feature-cloud.md)
* [Azure Backup を使用したハイブリッド バックアップを保護するためのセキュリティ機能](backup-azure-security-feature.md)