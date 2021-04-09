---
title: バックアップ コンテナーの概要
description: バックアップ コンテナーの概要。
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: c189997ecc4814917182246b35003649d317ac77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92091289"
---
# <a name="backup-vaults-overview"></a>バックアップ コンテナーの概要

この記事では、バックアップ コンテナーの機能について説明します。 バックアップ コンテナーは、Azure Backup によってサポートされる特定の新しいワークロードのバックアップ データを格納する Azure のストレージ エンティティです。 バックアップ コンテナーを使用すると、Azure Database for PostgreSQL サーバーなど、さまざまな Azure サービスのバックアップ データと Azure Backup によってサポートされる新しいワークロードを保持できます。 バックアップ コンテナーを使用すると、管理オーバーヘッドを最小限に抑えながら、バックアップ データを簡単に整理できます。 バックアップ コンテナーは、Azure の Azure Resource Manager モデルに基づいており、次のような機能を提供します。

- **強化されたバックアップ データの保護機能**:バックアップ コンテナーでは、Azure Backup によりクラウド バックアップを保護するセキュリティ機能が提供されます。 このセキュリティ機能により、バックアップをセキュリティで保護することができ、運用サーバーとバックアップ サーバーが侵害された場合でもデータを安全に回復できます。 [詳細情報](backup-azure-security-feature.md)

- **Azure ロールベースのアクセス制御 (Azure RBAC)** : Azure RBAC を使用すると、Azure のアクセス制御を詳細に管理できます。 [Azure にはさまざまな組み込みのロールがあります](../role-based-access-control/built-in-roles.md)。また、Azure Backup には、[復旧ポイントを管理するための 3 つの組み込みのロールがあります](backup-rbac-rs-vault.md)。 バックアップ コンテナーは Azure RBAC と互換性があります。これにより、定義された一連のユーザー ロールへのバックアップと復元のアクセスが制限されます。 [詳細情報](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>バックアップ コンテナーのストレージ設定

バックアップ コンテナーは、時間の経過と共に作成されたバックアップと復旧ポイントを格納するエンティティです。 バックアップ コンテナーには、保護対象の仮想マシンに関連付けられたバックアップ ポリシーも格納されます。

- Azure Backup では、コンテナーのストレージが自動的に処理されます。 バックアップ コンテナーを作成するときに、ビジネス ニーズに合ったストレージの冗長性を選択します。

- ストレージの冗長性の詳細については、[geo](../storage/common/storage-redundancy.md#geo-redundant-storage) と[ローカル](../storage/common/storage-redundancy.md#locally-redundant-storage)の冗長性に関する記事を参照してください。

## <a name="encryption-settings-in-the-backup-vault"></a>バックアップ コンテナーの暗号化設定

このセクションでは、バックアップ コンテナーに格納されるバックアップ データを暗号化するために使用できるオプションについて説明します。

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>プラットフォーム マネージド キーを使用したバックアップ データの暗号化

既定では、すべてのデータが、プラットフォーム マネージド キーを使用して暗号化されます。 この暗号化を有効にするためにユーザーが明示的なアクションを実行する必要はありません。 これは、バックアップ コンテナーにバックアップされるすべてのワークロードに適用されます。

## <a name="create-a-backup-vault"></a>バックアップ コンテナーの作成

バックアップ コンテナーは、時間の経過と共に作成された復旧ポイントを格納する管理エンティティであり、バックアップ関連の操作を実行するためのインターフェイスを提供します。 たとえば、オンデマンドのバックアップの作成、復元の実行、バックアップ ポリシーの作成などの操作です。

バックアップ コンテナーを作成するには、次の手順に従います。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( <https://portal.azure.com> ) にサインインします。

### <a name="create-backup-vault"></a>バックアップ コンテナーの作成

1. 検索ボックスに「**バックアップ コンテナー**」と入力します。
1. **[サービス]** で **[バックアップ コンテナー]** を選択します。
1. **[バックアップ コンテナー]** ページで、 **[追加]** を選択します。
1. **[基本]** タブの **[プロジェクトの詳細]** で、正しいサブスクリプションが選択されていることを確認し、 **[新しいリソース グループの作成]** を選択します。 名前として「*myResourceGroup*」と入力します。

  ![新しいリソース グループの作成](./media/backup-vault-overview/new-resource-group.png)

1. **[インスタンスの詳細]** で、 **[バックアップ コンテナー名]** に「*myVault*」と入力し、 **[リージョン]** で任意のリージョン (この例では *[米国東部]* ) を選択します。
1. 次に、 **[ストレージ冗長]** を選択します。 コンテナーに項目を保護した後で、ストレージ冗長を変更することはできません。
1. プライマリ バックアップ ストレージ エンドポイントとして Azure を使用している場合は、引き続き既定の **geo 冗長** 設定を使用することをお勧めします。
1. プライマリ バックアップ ストレージ エンドポイントとして Azure を使用しない場合、 **[ローカル冗長]** を選択します。これにより、Azure ストレージのコストを削減できます。
1. [geo](../storage/common/storage-redundancy.md#geo-redundant-storage) 冗長と[ローカル](../storage/common/storage-redundancy.md#locally-redundant-storage)冗長の詳細をご確認ください。

  ![ストレージ冗長を選択する](./media/backup-vault-overview/storage-redundancy.png)

1. ページの下部にある [確認と作成] ボタンを選択します。

    ![[確認と作成] を選択する](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>バックアップ コンテナーの削除

このセクションでは、バックアップ コンテナーを削除する方法について説明します。 依存関係を削除してからコンテナーの削除を行うための手順が含まれています。

### <a name="before-you-start"></a>開始する前に

次のいずれかの依存関係があるバックアップ コンテナーは削除できません。

- 保護されたデータ ソース (PostgreSQL サーバーの Azure データベースなど) が格納されているコンテナーは削除できません。
- バックアップ データが含まれるコンテナーを削除することはできません。

依存関係を削除せずにコンテナーを削除しようとすると、次のエラー メッセージが表示されます。

>Cannot delete the Backup vault as there are existing backup instances or backup policies in the vault. (既存のバックアップ インスタンスまたはバックアップ ポリシーがバックアップ コンテナー内に存在するため、このコンテナーを削除できません。) Delete all backup instances and backup policies that are present in the vault and then try deleting the vault. (コンテナー内に存在するすべてのバックアップ インスタンスとバックアップ ポリシーを削除してから、コンテナーの削除をもう一度お試しください。)

### <a name="proper-way-to-delete-a-vault"></a>コンテナーを削除する正しい方法

>[!WARNING]
下記の操作は破壊的であり、元に戻すことはできません。 保護されたサーバーに関連付けられているすべてのバックアップ データとバックアップ項目が、完全に削除されます。 慎重に進めてください。

コンテナーを正しく削除するには、次の順序で手順に従う必要があります。

- 保護された項目があるかどうかを確認する必要があります。
  - 左側のナビゲーション バーの **[Backup Instances]\(バックアップ インスタンス\)** に移動します。 ここに一覧表示されているすべての項目を最初に削除する必要があります。

これらの手順を完了したら、引き続きコンテナーを削除できます。

### <a name="delete-the-backup-vault"></a>バックアップ コンテナーの削除

コンテナーにアイテムがなくなったら、コンテナー ダッシュボードで **[削除]** を選択します。 コンテナーを削除するかどうかを確認するメッセージが表示されます。

![コンテナーを削除する](./media/backup-vault-overview/delete-vault.png)

1. コンテナーを削除することを確認するために、 **[はい]** を選択します。 コンテナーが削除されます。 ポータルが **[新規作成]** サービス メニューに戻ります。

## <a name="monitor-and-manage-the-backup-vault"></a>バックアップ コンテナーの監視および管理

このセクションでは、バックアップ コンテナーの **[概要]** ダッシュボードを使用して、バックアップ コンテナーの監視と管理を行う方法について説明します。 [概要] ペインには、 **[ジョブ]** と **[インスタンス]** の 2 つのタイルがあります。

![概要ダッシュボード](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>バックアップ インスタンスの管理

**[ジョブ]** タイルには、バックアップ コンテナーのすべてのバックアップおよび復元関連のジョブの概要が表示されます。 このタイルで数字を選択すると、ジョブの詳細情報を表示でき、特定のデータ ソースの種類、操作の種類、状態を確認できます。

![バックアップ インスタンス](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>バックアップ ジョブの管理

**[Backup Instances]\(バックアップ インスタンス\)** タイルには、バックアップ コンテナーのすべてのバックアップ インスタンスの概要が表示されます。 このタイルで数字を選択すると、バックアップ インスタンスの詳細情報を表示でき、特定のデータ ソースの種類と保護の状態を確認できます。

![バックアップ ジョブ](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>次のステップ

- [Azure PostgreSQL データベースでバックアップを構成する](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
