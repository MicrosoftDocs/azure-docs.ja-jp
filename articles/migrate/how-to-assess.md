---
title: Azure Migrate で評価ツールを追加する
description: Azure Migrate で評価ツールを追加する方法について説明します。
ms.topic: how-to
ms.date: 04/26/2020
ms.openlocfilehash: 5151135a57b7c2780485d85299ead5df83ea851e
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122117"
---
# <a name="add-assessment-tools"></a>評価ツールの追加

この記事では、[Azure Migrate](./migrate-services-overview.md) で評価ツールを追加する方法について説明します。 

- 評価ツールを追加する必要があり、まだ Azure Migrate プロジェクトを用意していない場合は、この[記事](how-to-add-tool-first-time.md)に従ってください。
- 評価のために ISV ツール (Movere) を追加した場合は、[この手順に従って](prepare-isv-movere.md)、ツールを使用するための準備を行います。

## <a name="select-an-assessment-scenario"></a>評価シナリオを選択する

1. Azure Migrate プロジェクトで、 **[概要]** をクリックします。
2. 使用する評価シナリオを選択します。

    - Azure に移行するコンピューターやワークロードを検出して評価するには、 **[サーバーの評価と移行]** を選択します。
    - オンプレミスの SQL Server データベースを評価するには、 **[Assess and migrate databases] (データベースの評価と移行)** を選択します。
    - オンプレミスの Web アプリを評価するには、 **[Assess and migrate web apps] (Web アプリの評価と移行)** を選択します。

    ![評価シナリオ](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>サーバー評価ツールを選択する 

1. **[サーバーの評価と移行]** をクリックします。
2. **[Azure Migrate - Servers] (Azure Migrate - サーバー)** で、評価ツールをまだ追加していない場合は、 **[評価用ツール]** で、 **[Click here to add an assessment tool] (評価ツールを追加するにはここをクリック)** を選択します。 評価ツールを既に追加している場合は、 **[Add more assessment tools] (評価ツールをさらに追加)** で **[変更]** を選択します。

    > [!NOTE]
    > 別のプロジェクトに移動する必要がある場合は、 **[Azure Migrate - Servers] (Azure Migrate - サーバー)** で、 **[See details for a different 移行プロジェクト] (別の移行プロジェクトの詳細を確認する)** の横にある **[ここをクリックしてください]** をクリックします。

3. **[Azure Migrate]** で、使用する評価ツールを選択します。

    - Azure Migrate Server Assessment を使用する場合は、Azure Migrate プロジェクトで直接評価を設定、実行、および表示できます。
    - 別の評価ツールを使用する場合は、そのサイトに用意されているリンクに移動し、提供されている手順に従って評価を実行します。


## <a name="select-a-database-assessment-tool"></a>データベース評価ツールを選択する

1. **[データベースの評価と移行]** をクリックします
2. **[データベース]** で、 **[ツールの追加]** をクリックします。
3. [ツールの追加] > **[評価ツールの選択]** で、データベースを評価するために使用するツールを選択します。

## <a name="select-a-web-app-assessment-tool"></a>Web アプリ評価ツールを選択する

1. **[Assess and migrate web apps] (Web アプリの評価と移行)** をクリックします。
2. Azure App Service の Migration ツールへのリンクに従います。 この移行ツールを使用して、次のことを行います。

    - **オンラインでアプリを評価する**: Azure App Service Migration Assistant を使用して、パブリック URL でオンラインでアプリを評価できます。
    - **.NET/PHP**: 内部の .NET および PHP アプリの場合は、Migration Assistant をダウンロードして実行できます。



## <a name="next-steps"></a>次のステップ

[VMware](tutorial-prepare-vmware.md) VM、[Hyper-V](tutorial-prepare-hyper-v.md)、または[物理サーバー](tutorial-prepare-physical.md)の Azure Migrate Server Assessment を使用した評価を試みます
