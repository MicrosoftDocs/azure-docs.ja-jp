---
title: インクルード ファイル
description: インクルード ファイル
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: 4f6099975ad6968313e3083f2e7f5e3220db03cb
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71241070"
---
## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

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

   - **Name**:コンテナーを識別するフレンドリ名を入力します。 名前は Azure サブスクリプションに対して一意である必要があります。 2 文字以上で、50 文字以下の名前を指定します。 名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。
   - **サブスクリプション**:使用するサブスクリプションを選択します。 1 つのサブスクリプションのみのメンバーの場合は、その名前が表示されます。 どのサブスクリプションを使用すればよいかがわからない場合は、既定 (推奨) のサブスクリプションを使用してください。 職場または学校アカウントが複数の Azure サブスクリプションに関連付けられている場合に限り、複数の選択肢が存在します。
   - **[リソース グループ]** :既存のリソース グループを使用するか、新しいリソース グループを作成します。 サブスクリプションの使用可能なリソース グループの一覧を表示するには、 **[既存のものを使用]** を選択し、ドロップダウン リスト ボックスからリソースを選択します。 新しいリソース グループを作成するには、 **[新規作成]** を選択し、名前を入力します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../articles/azure-resource-manager/resource-group-overview.md)」を参照してください。
   - **[場所]** :コンテナーの地理的リージョンを選択します。 仮想マシンを保護するコンテナーを作成する場合、コンテナーは仮想マシンと同じリージョンにある**必要があります**。

      > [!IMPORTANT]
      > VM の場所が不明な場合は、ダイアログ ボックスを閉じます。 ポータルの仮想マシンの一覧に移動します。 いくつかのリージョンに仮想マシンがある場合は、各リージョンで Recovery Services コンテナーを作成します。 まず最初の場所にコンテナーを作成してから、別の場所にコンテナーを作成します。 バックアップ データを格納するためにストレージ アカウントを指定する必要はありません。 Recovery Services コンテナーと Azure Backup サービスで自動的に処理されます。
      >
      >

5. Recovery Services コンテナーを作成する準備ができたら、 **[作成]** を選択します。

    ![[Recovery Services コンテナー] を作成する](./media/backup-create-rs-vault/click-create-button.png)

    Recovery Services コンテナーの作成に時間がかかることがあります。 **[通知]** 領域 (ポータルの右上隅) で、状態の通知を監視します。 コンテナーが作成されると、Recovery Services コンテナーの一覧に表示されます。 コンテナーが表示されない場合は、 **[最新の情報に更新]** を選択します。

     ![バックアップ コンテナーの一覧を更新する](./media/backup-create-rs-vault/refresh-button.png)
