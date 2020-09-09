---
title: Azure Migrate に移行ツールを追加する
description: Azure Migrate に移行ツールを追加する方法を説明します。
ms.topic: article
ms.date: 04/26/2020
ms.openlocfilehash: b6ac8ed64d3f12783cc16f0428874a19d027adf9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109809"
---
# <a name="add-migration-tools"></a>移行ツールの追加

この記事では、[Azure Migrate](./migrate-services-overview.md) 内で移行ツールを追加する方法について説明します。

- 移行ツールの追加を希望し、まだ Azure Migrate プロジェクトを設定していない場合は、この[記事](how-to-add-tool-first-time.md)に従ってください。
- 移行に ISV ツールを追加した場合は、[手順に従って](prepare-isv-movere.md)、ツールを使用するための準備を行います。

## <a name="select-a-migration-scenario"></a>移行シナリオの選択

1. Azure Migrate プロジェクトで、 **[概要]** をクリックします。
2. 使用する移行シナリオを選択します。

    - Azure にマシンやワークロードを移行するには、 **[サーバーの評価と移行]** を選択します。
    - オンプレミスの SQL Server データベースを移行するには、 **[Assess and migrate databases] (データベースの評価と移行)** を選択します。
    - オンプレミスの Web アプリを移行するには、 **[Web アプリの評価と移行]** を選択します。
    - オンプレミスの大量のデータを Azure にオフライン モードで移行するには、 **[Data Box の注文]** を選択します。

    ![評価シナリオ](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>サーバー移行ツールの選択

1. **[サーバーの評価と移行]** をクリックします。
2. **[Azure Migrate - Servers]\(Azure Migrate - サーバー\)** で、移行ツールをまだ追加していない場合は、 **[移行ツール]** の下の **[Click here to add a migration tool]\(移行ツールを追加するにはここをクリックしてください\)** を選択します。 移行ツールを既に追加している場合は、 **[移行ツールをさらに追加しますか]** で **[変更]** を選択します。

    > [!NOTE]
    > 別のプロジェクトに移動する必要がある場合は、 **[Azure Migrate - Servers] (Azure Migrate - サーバー)** で、 **[See details for a different 移行プロジェクト] (別の移行プロジェクトの詳細を確認する)** の横にある **[ここをクリックしてください]** をクリックします。

3. **[Azure Migrate]** で、使用する移行ツールを選択します。
    - Azure Migrate Server Migration を使用する場合は、Azure Migrate プロジェクト内で直接、移行を設定して実行できます。
    - サードパーティ評価ツールを使用する場合は、ISV 用に用意されているリンクに移動し、提供されている指示に従って移行を実行します。

## <a name="select-a-database-migration-tool"></a>データベース移行ツールの選択

1. **[データベースの評価と移行]** をクリックします
2. **[データベース]** で、 **[ツールの追加]** をクリックします。
3. [ツールの追加] > **[移行ツールの選択]** で、データベースの移行に使用するツールを選択します。

## <a name="select-a-web-app-migration-tool"></a>Web アプリ移行ツールの選択

1. **[Assess and migrate web apps] (Web アプリの評価と移行)** をクリックします。
2. Azure App Service の Migration ツールへのリンクに従います。 この移行ツールを使用して、次のことを行います。

    - **オンラインでアプリを評価する**: Azure App Service Migration Assistant を使用して、オンラインでパブリック URL を使用してアプリを評価し、移行することができます。
    - **.NET/PHP**: 内部の .NET および PHP アプリの場合は、Migration Assistant をダウンロードして実行できます。

## <a name="order-an-azure-data-box"></a>Azure Data Box の注文

大量のデータを Azure に移行するには、オフライン データ転送用に Azure DAta Box を注文できます。

1. **[Data Box の注文]** をクリックします。
2. **[Azure Data Box の選択]** で、ご自身のサブスクリプションを指定します。 
3. 転送は、Azure へのインポートになります。 データ ソースと、データの Azure リージョンの宛先を指定します。

## <a name="next-steps"></a>次のステップ

Azure Migrate Server Migration を使用して、[Hyper-V](tutorial-migrate-hyper-v.md) または [VMware](tutorial-migrate-vmware.md) VM の移行を試します。
