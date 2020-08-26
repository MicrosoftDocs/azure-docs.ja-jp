---
title: カスタマー マネージド キーを使用したデータ暗号化 - Azure Database for MySQL
description: カスタマーマネージド キーによる Azure Database for MySQL のデータ暗号化では、保存データの保護に Bring Your Own Key (BYOK) を使用できます。 また、組織でキーとデータの管理における職務の分離を実装することもできます。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 8fca0195c2941e4ed1a859c3201adfc2a4a0a2ed
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067445"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>カスタマー マネージド キーを使用した Azure Database for MySQL のデータの暗号化

Azure Database for MySQL のカスタマーマネージド キーによるデータ暗号化では、保存データの保護に Bring Your Own Key (BYOK) を使用できます。 また、組織でキーとデータの管理における職務の分離を実装することもできます。 カスタマーマネージド暗号化を使用する場合、キーのライフサイクル、キーの使用アクセス許可、およびキーに対する操作の監査については、お客様の責任となり、お客様が完全に制御できます。

Azure Database for MySQL のカスタマーマネージド キーによるデータ暗号化は、サーバーレベルで設定されます。 特定のサーバーについては、キー暗号化キー (KEK) と呼ばれる、カスタマーマネージド キーを使用して、サービスによって使用されるデータ暗号化キー (DEK) を暗号化します。 KEK は、顧客が所有する、カスタマーマネージド [Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) インスタンスに格納される非対称キーです。 キー暗号化キー (KEK) とデータ暗号化キー (DEK) については、この記事の後半で詳しく説明します。

Key Vault は、クラウドベースの外部キー管理システムです。 可用性が高く、FIPS 140-2 レベル 2 で検証されたハードウェア セキュリティ モジュール (HSM) によって必要に応じてサポートされる、スケーラブルで安全な RSA 暗号化キー向けストレージが提供されます。 格納されているキーに直接アクセスすることはできませんが、承認されたエンティティに対する暗号化とその解除のサービスが提供されます。 Key Vault では、キーの生成、インポート、または[オンプレミス HSM デバイスからの転送](../key-vault/key-Vault-hsm-protected-keys.md)を行うことができます。

> [!NOTE]
> この機能は、Azure Database for MySQL で "汎用" および "メモリ最適化" の価格レベルがサポートされているすべての Azure リージョンで使用できます。 その他の制限事項については、「[制限事項](concepts-data-encryption-mysql.md#limitations)」セクションを参照してください。

## <a name="benefits"></a>メリット

Azure Database for MySQL のカスタマーマネージド キーによるデータ暗号化には、次の利点があります。

* キーを削除してデータベースにアクセスできないようにすることで、ユーザーがデータアクセスを完全に制御できる 
* キーライフサイクルの完全な制御 (企業ポリシーに合わせたキーの交換を含む)
* Azure Key Vault でのキーの一元的な管理と整理
* セキュリティ責任者と、DBA およびシステム管理者の間での職務の分離を実装できる


## <a name="terminology-and-description"></a>用語と説明

**データ暗号化キー (DEK)** :データのパーティションまたはブロックの暗号化に使用される対称 AES256 キー。 データの各ブロックを異なるキーで暗号化することによって、暗号化分析攻撃がより困難になります。 DEK へのアクセスは、特定のブロックを暗号化および暗号化するリソース プロバイダーまたはアプリケーション インスタンス必要になります。 DEK を新しいキーで置き換える場合に、新しいキーを使用して再暗号化する必要があるのは、その関連ブロック内のデータのみです。

**キー暗号化キー (KEK)** :DEK の暗号化に使用される暗号化キー。 Key Vault を離れることがない KEK では、DEK 自体を暗号化および制御できます。 KEK へのアクセス権を持つエンティティは、DEK を必要とするエンティティとは異なる場合があります。 D の複合化には KEK が必要であるため、KEK を削除することにより DEK を効率的に削除できる有効な単一ポイントになります。

KEK で暗号化された DEK は、個別に格納されます。 KEK へのアクセス権を持つエンティティでのみ、これらの DEK の暗号化を解除できます。 詳細については、[保存時の暗号化のセキュリティ](../security/fundamentals/encryption-atrest.md)に関するページを参照してください。

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>カスタマーマネージド キーによるデータ暗号化のしくみ

![Bring Your Own Key の概要を示す図](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

MySQL サーバーで DEK の暗号化のために Key Vault に格納されているカスタマーマネージド キーを使用する場合、Key Vault 管理者がサーバーに次のアクセス権を付与します。

* **get**:Key Vault 内のキーの公開部分とプロパティを取得します。
* **wrapKey**:DEK を暗号化できるようにします。 暗号化された DEK は Azure Database for MySQL に格納されます。
* **unwrapKey**:DEK の暗号化を解除できるようにします。 Azure Database for MySQL でデータを暗号化または復号化するには、暗号化解除された DEK が必要となります

Key Vault 管理者は、後で監査できるように、[Key Vault の監査イベントのログ記録を有効](../azure-monitor/insights/key-vault-insights-overview.md)にすることもできます。

Key Vault に格納されているカスタマーマネージド キーを使用するようにサーバーが構成されている場合、暗号化のためにサーバーから DEK が Key Vault に送信されます。 Key Vault から暗号化された DEK が返され、ユーザー データベースに格納されます。 同様に、必要に応じて、保護された DEK が暗号化解除のためにサーバーから Key Vault に送信されます。 ログ記録が有効になっている場合、監査者は Azure Monitor を使用して Key Vault の監査イベント ログを確認できます。

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Azure Database for MySQL のデータ暗号化を構成するための要件

Key Vault を構成するための要件を以下に示します。

* Key Vault と Azure Database for MySQL は、同じ Azure Active Directory (Azure AD) テナントに属している必要があります。 テナント間の Key Vault とサーバーの対話はサポートされていません。 後で Key Vault リソースを移動する場合は、データ暗号化を再構成する必要があります。
* キー (または Key Vault) を誤って削除した場合のデータ損失から保護するには、Key Vault で論理的な削除機能を有効にします。 論理的に削除されたリソースは 90 日間保持されます (その間にユーザーが復旧または消去した場合を除く)。 復旧と消去のアクションには、Key Vault のアクセス ポリシーに関連付けられた独自のアクセス許可があります。 論理的な削除機能は既定ではオフになっていますが、PowerShell または Azure CLI を介して有効にすることができます (Azure portal を介して有効にできないことに注意してください)。
* Azure Database for MySQL に対し、その一意のマネージド ID を使用して、get、wrapKey、および unwrapKey アクセス許可による Key Vault へのアクセスを付与します。 Azure portal では、MySQL でのデータ暗号化を有効にすると、一意の "サービス" ID が自動的に作成されます。 Azure portal を使用する場合の詳細な手順については、[MySQL のデータ暗号化の構成](howto-data-encryption-portal.md)に関するページを参照してください。

カスタマーマネージド キーを構成するための要件を以下に示します。

* DEK の暗号化に使用されるカスタマーマネージド キーは、非対称の RSA 2048 のみです。
* キーがアクティブ化された日時 (設定する場合) は、過去の日付と時刻にする必要があります。 有効期限 (設定する場合) は、将来の日付と時刻にする必要があります。
* キーは、"*有効*" 状態になっている必要があります。
* Key Vault に[既存のキーをインポート](https://docs.microsoft.com/rest/api/keyvault/ImportKey/ImportKey)する場合は、サポートされているファイル形式 (`.pfx`、`.byok`、`.backup`) で提供してください。

## <a name="recommendations"></a>推奨事項

カスタマーマネージド キーを使用してデータ暗号化を利用する場合、Key Vault の構成に関する推奨事項は次のとおりです。

* Key Vault でリソース ロックを設定して、この重要なリソースを削除できるユーザーを制御し、誤削除や許可されていない削除を防ぎます。
* すべての暗号化キーの監査およびレポートを有効にします。 Key Vault で提供されるログは、他のセキュリティ情報およびイベント管理ツールに簡単に挿入できます。 Azure Monitor Log Analytics は、既に統合されているサービスの一例です。
* DEK のラップおよびラップ解除操作のアクセスを高速化するために、Key Vault と Azure Database for MySQL が同じリージョンにあることを確認します。
* Azure KeyVault を**プライベート エンドポイントと選択されたネットワーク**のみにロックダウンし、*信頼された Microsoft* サービスのみがリソースを保護できるようにします。

    ![trusted-service-with-AKV](media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png)

カスタマーマネージド キーを構成する場合の推奨事項は次のとおりです。

* カスタマーマネージド キーのコピーを安全な場所に保管するか、エスクロー サービスにエスクローします。

* Key Vault でキーを生成する場合は、初めてキーを使用する前に、キーのバックアップを作成します。 バックアップは Key Vault にのみ復元できます。 バックアップ コマンドの詳細については、「[Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey)」を参照してください。

## <a name="inaccessible-customer-managed-key-condition"></a>カスタマーマネージド キーのアクセス不可状態

Key Vault でカスタマー マネージド キーを使用してデータ暗号化を構成する場合に、サーバーをオンラインに保つには、このキーへの継続的なアクセスが必要です。 サーバーで Key Vault のカスタマーマネージド キーにアクセスできなくなった場合、サーバーでは 10 分以内にすべての接続を拒否し始めます。 サーバーで対応するエラー メッセージが発行され、サーバーの状態が "*アクセス不可*" に変更されます。 サーバーがこの状態になる理由としては、次のようなものがあります。

* データ暗号化が有効になっている Azure Database for MySQL 単一サーバーに対してポイントインタイム リストア サーバーを作成すると、新しく作成されたサーバーは "*アクセス不可*" 状態になります。 これは [Azure portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) または [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers) から修正できます。
* データ暗号化が有効になっている Azure Database for MySQL 単一サーバーに対して読み取りレプリカを作成すると、レプリカ サーバーは "*アクセス不可*" 状態になります。 これは [Azure portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) または [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers) から修正できます。
* KeyVault を削除すると、Azure Database for MySQL 単一サーバーはキーにアクセスできなくなり、"*アクセス不可*" 状態に移行します。 [Key Vault](../key-vault/general/soft-delete-cli.md#deleting-and-purging-key-vault-objects) を復旧し、データ暗号化を再検証して、サーバーを "*使用可能*" にします。
* KeyVault のキーを削除すると、Azure Database for MySQL 単一サーバーはキーにアクセスできなくなり、"*アクセス不可*" 状態に移行します。 [キー](../key-vault/general/soft-delete-cli.md#deleting-and-purging-key-vault-objects)を復旧し、データ暗号化を再検証して、サーバーを "*使用可能*" にしてください。
* Azure KeyVault に格納されているキーの有効期限が切れると、キーは無効になり、Azure Database for MySQL 単一サーバーは "*アクセス不可*" 状態に移行します。 [CLI](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-set-attributes) を使用してキーの有効期限を延長した後、データの暗号化を再検証して、サーバーを "*使用可能*" にします。

### <a name="accidental-key-access-revocation-from-key-vault"></a>Key Vault からの誤ったキー アクセスの失効

Key Vault に対する十分なアクセス権を持つユーザーが、次のことを行うことで、キーへのサーバー アクセスを誤って無効にしてしまうことがあります。

* サーバーから Key Vault の get、wrapKey、unwrapKey アクセス許可を取り消す。
* キーを削除する。
* Key Vault を削除する。
* Key Vault のファイアウォール規則を変更する。
* Azure AD でサーバーのマネージド ID を削除する。

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Key Vault でカスタマーマネージド キーを監視する

データベースの状態を監視したり、透過的データ暗号化保護機能アクセスができなくなった場合のアラートを有効にしたりするには、Azure の次の機能を構成します。

* [Azure Resource Health](../service-health/resource-health-overview.md):カスタマー キーにアクセスできなくなったアクセス不可のデータベースでは、データベースへの最初の接続が拒否された後、"アクセス不可" と表示されます。
* [アクティビティ ログ](../service-health/alerts-activity-log-service-notifications.md):カスタマーマネージド Key Vault 内のカスタマー キーへのアクセスに失敗すると、アクティビティ ログにエントリが追加されます。 これらのイベントに対してアラートを作成した場合は、できるだけ早くアクセスを再開できます。

* [アクション グループ](../azure-monitor/platform/action-groups.md):必要に応じて通知とアラートを送信するように、これらのグループを定義します。

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Key Vault 内の顧客のマネージド キーを使用して復元およびレプリケートする

Key Vault に格納されている顧客のマネージド キーで Azure Database for MySQL が暗号化された後、新しく作成されたサーバーのコピーも暗号化されます。 この新しいコピーは、ローカルまたは geo リストア操作を使用するか、読み取りレプリカを使用して作成することができます。 しかし、暗号化のための新しい顧客のマネージド キーを反映するように、コピーを変更することはできます。 カスタマーマネージド キーが変更された場合、最新のキーを使用してサーバーの古いバックアップが開始されます。

復元中または読み取りレプリカの作成中にカスタマーマネージド データの暗号化を設定する際の問題を回避するには、マスターおよび復元またはレプリカ サーバーでこれらの手順に従うことが重要です。

* マスター Azure Database for MySQL から、復元または読み取りレプリカの作成プロセスを開始します。
* 新しく作成されたサーバー (復元またはレプリカ) は、アクセス不可状態のままにしておきます。これは、その一意の ID に Key Vault へのアクセス許可がまだ付与されていないためです。
* 復元またはレプリカ サーバーで、データ暗号化設定のカスタマーマネージド キーを再検証して、Key Vault に格納されているキーへのラップおよびラップ解除アクセス許可が新しく作成されたサーバーに付与されるようにします。

## <a name="limitations"></a>制限事項

Azure Database for MySQL の場合、カスタマー マネージド キー (CMK) を使用した保存データの暗号化のサポートには、いくつかの制限があります。

* この機能のサポートは、**General Purpose** および **Memory Optimized** 価格レベルに限定されています。
* この機能は、16 TB までのストレージをサポートしているリージョンとサーバーでのみサポートされています。 最大 16 TB のストレージをサポートする Azure リージョンの一覧については、[こちらの](concepts-pricing-tiers.md#storage)ドキュメントにあるストレージのセクションを参照してください

    > [!NOTE]
    > - 上記のリージョンで作成されたすべての新しい MySQL サーバーで、カスタマー マネージャー キーによる暗号化のサポートを**利用できます**。 ポイント イン タイム リストア (PITR) サーバーまたは読み取りレプリカは、理論的には "新規" に適合しません。
    > - プロビジョニングされたサーバーによって最大 16 TB がサポートされているかどうかを検証するには、ポータルの [価格レベル] ブレードにアクセスして、プロビジョニング済みのサーバーでサポートされる最大ストレージ サイズを確認できます。 スライダーを最大 4 TB まで動かすことができる場合、サーバーでは、カスタマー マネージド キーを使用した暗号化がサポートされていない可能性があります。 ただし、データは常にサービス マネージド キーを使用して暗号化されます。 質問がある場合は、AskAzureDBforMySQL@service.microsoft.com までご連絡ください。

* RSA 2048 暗号化キーを使用した暗号化のみがサポートされています。

## <a name="next-steps"></a>次のステップ

[Azure portal を使用して Azure Database for MySQL のカスタマー マネージド キーによるデータ暗号化を設定する](howto-data-encryption-portal.md)方法について学習します。
