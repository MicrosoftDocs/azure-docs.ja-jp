---
title: 'Azure Backup: Recovery Services コンテナーの作成'
description: バックアップと復旧ポイントを格納する Recovery Services コンテナーの作成
author: sogup
manager: vijayts
keywords: Recovery Services コンテナー; Azure VM バックアップ; Azure VM 復元;
ms.service: backup
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: sogup
ms.openlocfilehash: 95ffe6aa0f65690e2df71c94b87d3e9f1ac7f7e5
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465168"
---
# <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーは、経時的に作成されたバックアップと復旧ポイントを格納するエンティティです。 Recovery Services コンテナーには、保護される仮想マシンに関連付けられたバックアップ ポリシーも含まれます。

Recovery Services コンテナーを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com/) でサブスクリプションにサインインします。

2. 左側のメニューから、 **[すべてのサービス]** を選択します。

    ![[すべてのサービス] を選択する](./media/backup-create-rs-vault/click-all-services.png)

3. **[すべてのサービス]** ダイアログ ボックスに、「**Recovery Services**」と入力します。 入力に従って、リソースの一覧がフィルター処理されます。 リソースの一覧から **[Recovery Services コンテナー]** を選択します。

    ![[Recovery Services コンテナー] と入力して選択する](./media/backup-create-rs-vault/all-services.png)

    サブスクリプションに Recovery Services コンテナーの一覧が表示されます。

4. **[Recovery Services コンテナー]** ダッシュボードで **[追加]** を選択します。

    ![[Recovery Services コンテナー] を追加する](./media/backup-create-rs-vault/add-button-create-vault.png)

    **[Recovery Services コンテナー]** ダイアログ ボックスが開きます。 **[名前]** 、 **[サブスクリプション]** 、 **[リソース グループ]** 、 **[場所]** に値を入力します。

    ![[Recovery Services コンテナー] を構成する](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **[名前]** :コンテナーを識別するフレンドリ名を入力します。 名前は Azure サブスクリプションに対して一意である必要があります。 2 文字以上で、50 文字以下の名前を指定します。 名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。
   - **サブスクリプション**:使用するサブスクリプションを選択します。 1 つのサブスクリプションのみのメンバーの場合は、その名前が表示されます。 どのサブスクリプションを使用すればよいかがわからない場合は、既定 (推奨) のサブスクリプションを使用してください。 職場または学校アカウントが複数の Azure サブスクリプションに関連付けられている場合に限り、複数の選択肢が存在します。
   - **[リソース グループ]** :既存のリソース グループを使用するか、新しいリソース グループを作成します。 サブスクリプションの使用可能なリソース グループの一覧を表示するには、 **[既存のものを使用]** を選択し、ドロップダウン リスト ボックスからリソースを選択します。 新しいリソース グループを作成するには、 **[新規作成]** を選択し、名前を入力します。 リソース グループの詳細については、「[Azure Resource Manager の概要](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)」を参照してください。
   - **[場所]** :コンテナーの地理的リージョンを選択します。 仮想マシンを保護するコンテナーを作成する場合、コンテナーは仮想マシンと同じリージョンにある**必要があります**。

      > [!IMPORTANT]
      > VM の場所が不明な場合は、ダイアログ ボックスを閉じます。 ポータルの仮想マシンの一覧に移動します。 いくつかのリージョンに仮想マシンがある場合は、各リージョンで Recovery Services コンテナーを作成します。 まず最初の場所にコンテナーを作成してから、別の場所にコンテナーを作成します。 バックアップ データを格納するためにストレージ アカウントを指定する必要はありません。 Recovery Services コンテナーと Azure Backup サービスで自動的に処理されます。
      >
      >

5. Recovery Services コンテナーを作成する準備ができたら、 **[作成]** を選択します。

    ![[Recovery Services コンテナー] を作成する](./media/backup-create-rs-vault/click-create-button.png)

    Recovery Services コンテナーの作成に時間がかかることがあります。 **[通知]** 領域 (ポータルの右上隅) で、状態の通知を監視します。 コンテナーが作成されると、Recovery Services コンテナーの一覧に表示されます。 コンテナーが表示されない場合は、 **[最新の情報に更新]** を選択します。

     ![バックアップ コンテナーの一覧を更新する](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>ストレージ冗長性の設定

Azure Backup では、コンテナーのストレージが自動的に処理されます。 ストレージをレプリケートする方法を指定する必要があります。

1. **[Recovery Services コンテナー]** ブレードで、新しいコンテナーをクリックします。 **[設定]** セクションの **[プロパティ]** をクリックします。
2. **[プロパティ]** で、 **[バックアップ構成]** の **[更新]** をクリックします。

3. ストレージのレプリケーションの種類を選択し、 **[保存]** をクリックします。

     ![新しいコンテナーのストレージ構成を設定する](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - プライマリ バックアップ ストレージ エンドポイントとして Azure を使用している場合は、引き続き既定の **geo 冗長**設定を使用することをお勧めします。
   - プライマリ バックアップ ストレージ エンドポイントとして Azure を使用しない場合、 **[ローカル冗長]** を選択します。これにより、Azure Storage のコストを削減できます。
   - [geo](../storage/common/storage-redundancy-grs.md) 冗長と[ローカル](../storage/common/storage-redundancy-lrs.md)冗長の詳細をご確認ください。

> [!NOTE]
> Recovery Services コンテナーの**ストレージのレプリケーションの種類** (ローカル冗長/geo 冗長) の変更は、コンテナーでバックアップを構成する前に行う必要があります。 バックアップをいったん構成すると、変更オプションは無効になり、**ストレージのレプリケーションの種類**は変更できません。 

## <a name="next-steps"></a>次の手順

Recovery Services コンテナーの[詳細情報](backup-azure-recovery-services-vault-overview.md)。
Recovery Services コンテナーの削除の[詳細情報](backup-azure-delete-vault.md)。
