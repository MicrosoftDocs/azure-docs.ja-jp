---
title: Azure Migrate で評価/移行ツールを追加する
description: Azure Migrate プロジェクトを作成し、評価/移行ツールを追加する方法について説明します。
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: a94e3cc18f46c457d6ed54ef88c62adefb07c5b9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102533"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>評価/移行ツールの初回追加

この記事では、評価ツールまたは移行ツールを [Azure Migrate](./migrate-services-overview.md) プロジェクトに初めて追加する方法について説明します。  
Azure Migrate は、オンプレミスのアプリとワークロード、およびプライベート/パブリック クラウド VM の検出、評価、および Azure への移行を追跡するための中央ハブとなります。 このハブには、評価および移行のための Azure Migrate ツールのほか、他のツールや独立系ソフトウェア ベンダー (ISV) の[オファリング](migrate-services-overview.md#isv-integration)が用意されています。 

## <a name="check-permissions-to-create-project"></a>プロジェクトを作成するためのアクセス許可を確認する

Azure Migrate プロジェクトをまだ作成していない場合は、適切なアクセス許可があることを確認します。

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. [アクセスの確認] で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。 共同作成者または所有者のアクセス許可を持っている必要があります。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。

## <a name="create-a-project-and-add-a-tool"></a>プロジェクトの作成とツールの追加

Azure サブスクリプション内に新しい Azure Migrate プロジェクトを設定し、ツールを追加します。

- Azure Migrate のプロジェクトは、評価または移行しようとしている環境から収集された検出、評価、移行のメタデータを格納するために使用されます。 
- プロジェクトでは、検出されたアセットを追跡し、評価と移行を調整することができます。

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。
2. **[サービス]** で **[Azure Migrate]** を選択します。

    ![Azure Migrate の設定](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. **[概要]** で **[サーバーの評価と移行]** をクリックします。
4. **[サーバーの検出、評価、移行]** で、 **[サーバーの評価と移行]** をクリックします。

    ![サーバーの検出と評価](./media/how-to-add-tool-first-time/assess-migrate.png)

1. **[サーバーの検出、評価、移行]** で、 **[ツールの追加]** をクリックします。
2. **[移行プロジェクト]** で、自分の Azure サブスクリプションを選択し、リソース グループがない場合は作成します。
3. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地理的な場所を指定します。  [パブリック](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。

    ![Azure Migrate プロジェクトの作成](./media/how-to-add-tool-first-time/migrate-project.png)

    - プロジェクトのために指定した地理的な場所は、オンプレミスの VM から収集されたメタデータを格納するためにのみ使用されます。 実際の移行では、任意のターゲット リージョンを選択できます。
    - ある地域の特定のリージョン内にプロジェクトをデプロイする必要がある場合は、次の API を使用してプロジェクトを作成します。 サブスクリプション ID、リソース グループ名、およびプロジェクト名を場所と共に指定します。 [パブリック](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)の地域/リージョンを確認します。

        `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. **[次へ]** をクリックし、評価ツールまたは移行ツールを追加します。

    > [!NOTE]
    > プロジェクトを作成するときは、少なくとも 1 つの評価ツールまたは移行ツールを追加する必要があります。

5. **[評価ツールの選択]** で、評価ツールを追加します。 評価ツールが不要な場合は、 **[今は評価ツールの追加をスキップします]**  >  **[次へ]** を選択します。 
2. **[移行ツールの選択]** で、必要に応じて移行ツールを追加します。 今すぐ移行ツールが必要でない場合は、 **[今は移行ツールの追加をスキップします]**  >  **[次へ]** を選択します。
3. **[ツールの確認と追加]** で設定を確認し、 **[ツールの追加]** をクリックします。

プロジェクトを作成した後、サーバーとワークロード、データベース、Web アプリの評価と移行のための追加のツールを選択できます。

## <a name="create-additional-projects"></a>追加のプロジェクトの作成

状況によっては、追加の Azure Migrate プロジェクトの作成が必要になる場合があります。 たとえば、データセンターが地理的に異なる場所にある場合、または地理的に異なる場所にメタデータを格納する必要がある場合です。 次のように、追加のプロジェクトを作成します。

1. 現在の Azure Migrate プロジェクト内で、 **[サーバー]** または **[データベース]** をクリックします。
2. 右上隅の現在のプロジェクト名の横にある **[変更]** をクリックします。
3. **[設定]** で、[ここをクリックして**新しいプロジェクトを作成する] を選択**します。
4. 前の手順で説明したように、新しいプロジェクトを作成し、新しいツールを追加します。

## <a name="next-steps"></a>次のステップ

- 作業の開始: [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool)、または [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) 内)。
- ISV ツール (Movere) を追加した場合は、[手順を確認](prepare-isv-movere.md)して、Azure Migrate にツールをリンクするための準備を行います。
- 追加の[評価](how-to-assess.md)ツールと[移行](how-to-migrate.md)ツールを追加する方法を確認します。 
