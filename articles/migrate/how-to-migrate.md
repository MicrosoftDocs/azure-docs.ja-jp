---
title: Azure Migrate に移行ツールを追加する
description: Azure Migrate に移行ツールを追加する方法を説明します。
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 97051e97ec9868f6941b579241e16e62fdd2162b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96751786"
---
# <a name="add-migration-tools"></a>移行ツールの追加

この記事では、[Azure Migrate](./migrate-services-overview.md) 内で移行ツールを追加する方法について説明します。

- 移行ツールの追加を希望し、まだ Azure Migrate プロジェクトを設定していない場合は、この[記事](create-manage-projects.md)に従ってください。
- 移行に ISV ツールを追加した場合は、[手順に従って](prepare-isv-movere.md)、ツールを使用するための準備を行います。

## <a name="select-a-migration-scenario"></a>移行シナリオの選択

1. Azure Migrate プロジェクトで、 **[概要]** をクリックします。
2. 使用する移行シナリオを選択します。

    - Azure にマシンやワークロードを移行するには、 **[サーバーの評価と移行]** を選択します。
    - オンプレミスのデータベースを移行するには、 **[データベースの評価と移行]** を選択します。
    - オンプレミスの Web アプリを移行するには、 **[さらに探索]**  >  **[Web Apps]** の順に選択します。
    - Data Box を使用して Azure にデータを移行するには、 **[さらに探索]**  >  **[Data Box]** の順に選択します。

    ![移行シナリオを選択するためのオプション](./media/how-to-migrate/migrate-scenario.png)


## <a name="select-a-server-migration-tool"></a>サーバー移行ツールの選択

1. ツールを追加する:

    - ポータルの **[サーバーの評価と移行]** オプションを使用して Azure Migrate プロジェクトを作成してある場合は、Azure Migrate Server Migration ツールがプロジェクトに自動的に追加されます。 移行ツールをさらに追加するには、 **[サーバー]** 内の **[移行ツール]** の横にある **[ツールをさらに追加する]** を選択します。
    
         ![さらに移行ツールを追加するためのボタン](./media/how-to-migrate/add-migration-tools.png)

    - 別のオプションを使用してプロジェクトを作成していて、移行ツールをまだ用意していない場合は、 **[サーバー]**  >  **[移行ツール]** で、 **[ここをクリックしてください]** を選択します。

    ![最初の移行ツールを追加するためのボタン](./media/how-to-migrate/no-migration-tool.png)

2. **[Azure Migrate]**  >  **[ツールの追加]** で、追加するツールを選択します。 次に、 **[ツールの追加]** を選択します。

    ![一覧から評価ツールを選択する](./media/how-to-migrate/select-migration-tool.png)


## <a name="select-a-database-migration-tool"></a>データベース移行ツールの選択

ポータルの **[データベースの評価と移行]** オプションを使用して Azure Migrate プロジェクトを作成してある場合は、Database Migration ツールがプロジェクトに自動的に追加されます。 

1. Database Migration ツールがプロジェクトに含まれていない場合は、 **[データベース]**  >  **[評価ツール]** で、 **[ここをクリックしてください]** を選択します。
    
    ![データベース移行ツールを追加する](./media/how-to-migrate/no-database-migration-tool.png)


2. **[Azure Migrate]**  >  **[ツールの追加]** で、Database Migration ツールを選択します。 次に、 **[ツールの追加]** を選択します。

    ![一覧から Database Migration ツールを選択する](./media/how-to-migrate/select-database-migration-tool.png)

    

## <a name="select-a-web-app-migration-tool"></a>Web アプリ移行ツールの選択

ポータルの **[さらに探索]**  >  **[Web Apps]** オプションを使用して Azure Migrate プロジェクトを作成してある場合は、Web アプリ移行ツールがプロジェクトに自動的に追加されます。 

1. Web アプリ移行ツールがプロジェクトに含まれていない場合は、 **[Web Apps]**  >  **[評価ツール]** で、 **[ここをクリックしてください]** を選択します。

    ![Web アプリ移行ツールを追加する](./media/how-to-migrate/no-web-app-migration-tool.png)
 

2. **[Azure Migrate]**  >  **[ツールの追加]** で、Web アプリ移行ツールを選択します。 次に、 **[ツールの追加]** を選択します。

    ![一覧から webapp 評価ツールを選択する](./media/how-to-migrate/select-web-app-migration-tool.png)


## <a name="order-an-azure-data-box"></a>Azure Data Box の注文

大量のデータを Azure に移行するには、オフライン データ転送用の Azure Data Box を注文できます。

1. **[概要]** で、 **[さらに探索]** を選択します。
2. **[さらに探索]** で、 **[Data Box]** を選択します。
3. **[Data Box の使用を開始する]** で、Data Box を注文するときに使用するサブスクリプションとリソース グループを選択します。
4. **[転送の種類]** は、Azure へのインポートになります。 データが存在する国と、データの転送先とする Azure リージョンを指定します。 
5. **[適用]** をクリックして、設定を保存します。

## <a name="next-steps"></a>次のステップ

Azure Migrate Server Migration ツールを使用して、[Hyper-V](tutorial-migrate-hyper-v.md) または [VMware](tutorial-migrate-vmware.md) VM の移行を試します。
