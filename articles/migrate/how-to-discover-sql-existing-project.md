---
title: 既存の Azure Migrate プロジェクト内の SQL Server インスタンスを検出する
description: 既存の Azure Migrate プロジェクト内の SQL Server インスタンスを検出する方法について説明します。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: ca0052eebd8d3c8e80943ca8c0e0346216436800
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452766"
---
# <a name="discover-sql-server-instances-in-an-existing-project"></a>既存のプロジェクト内の SQL Server インスタンスを検出する 

この記事では、Azure SQL 評価機能のプレビューより前に作成された [Azure Migrate](./migrate-services-overview.md) プロジェクト内にある SQL Server インスタンスとデータベースを検出する方法について説明します。

オンプレミス マシンで実行されている SQL Server インスタンスとデータベースを検出すると、Azure SQL への移行パスを特定し、調整するのに役立ちます。 Azure Migrate アプライアンスでは、ターゲット サーバーで実行している SQL Server インスタンスおよびデータベースにアクセスできるドメイン資格情報または SQL Server 認証資格情報を使用して、この検出を実行します。 この検出プロセスはエージェントレスです。つまり、ターゲット サーバーには何もインストールされていません。

> [!Note]
> VMware 環境で実行している SQL Server インスタンスおよびデータベースの検出と評価は、現在プレビュー段階にあります。 この機能を試すには、[**このリンク**](https://aka.ms/AzureMigrate/SQL)を使用して、**オーストラリア東部** リージョンにプロジェクトを作成します。 オーストラリア東部に既にプロジェクトがあり、この機能を試したい場合は、この記事で [**前提条件**](how-to-discover-sql-existing-project.md)が完了していることを確認してください。

## <a name="before-you-start"></a>開始する前に

- 次を完了したことを確認します。 
    - お客様のリージョンの SQL 評価機能が発表される前に、[Azure Migrate プロジェクト](./create-manage-projects.md)を作成した
    - プロジェクトに [Azure Migrate: 検出および評価](./how-to-assess.md)ツールを追加した
- [アプリ検出のサポートと要件](./migrate-support-matrix-vmware.md#vmware-requirements)を確認します。
-  アプリ検出を実行するサーバーに PowerShell バージョン 2.0 以降がインストールされていること、および VMware ツール (10.2.0 より後) がインストールされていることを確認します。
- Azure Migrate アプライアンスを展開するための[要件](./migrate-appliance.md)を確認します。
- リソースを作成するために[必要なロール](./create-manage-projects.md#verify-permissions)がサブスクリプションにあることを確認します。
- アプライアンスがインターネットにアクセスできることを確認します

## <a name="enable-discovery-of-sql-server-instances-and-databases"></a>SQL Server インスタンスおよびデータベースの検出を有効にする

1. Azure Migrate プロジェクトで、次のいずれかを実行します。
    - ハブ タイルで **[無効]** を選択します。または :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="SQL 検出が無効な Azure Migrate のハブ タイル":::
    - サーバー検出ページの [SQL インスタンス] 列のいずれかのエントリで **[無効]** を選択します。:::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="SQL 検出が無効な Azure Migrate の [検出済みサーバー] ブレード":::
2. [SQL Server インスタンスとデータベースの検出] で、次の手順に従います。
    - **[アップグレード]** を選択して、必要なリソースを作成します。
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Azure Migrate アプライアンスをアップグレードするボタン":::
    - アプライアンスで実行しているサービスが最新バージョンに更新されていることを確認します。 これを行うには、アプライアンス サーバーからアプライアンス構成マネージャーを起動し、[前提条件のセットアップ] パネルから [アプライアンス サービスを表示] を選択します。
        - アプライアンスとそのコンポーネントが自動的に更新されます:::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="アプライアンスのバージョンを確認します":::
    - アプライアンス構成マネージャーの [資格情報と検出ソースの管理] パネルで、検出される SQL Server インスタンスとデータベースに対して Sysadmin アクセス権を持つドメインまたは SQL Server 認証の資格情報を追加します。 
    アプライアンスの自動資格情報マッピング機能を利用することも、または[ここで](/azure/migrate/tutorial-discover-vmware#start-continuous-discovery)強調されている各サーバーに資格情報を手動でマップすることもできます。
        
    注意：
    - ソフトウェア インベントリが既に有効になっていることを確認するか、またはドメインまたはドメイン以外の資格情報を指定してソフトウェア インベントリを有効にしてください。 SQL Server インスタンスを検出するには、ソフトウェア インベントリを実行する必要があります。
    - アプライアンスでは、ドメイン資格情報が追加されたときに、AD で検証を試みます。 資格情報に関連付けられている AD サーバーへのネットワーク通信経路がアプライアンス サーバーにあることを確認してください。 SQL Server 認証に関連付けられている資格情報は検証されません。 

3. 必要な資格情報が追加されたら、[検出の開始] を選択してスキャンを開始します。

> [!Note] 
>Azure SQL の評価を作成する前に、SQL 検出の実行を許可してください。 SQL Server インスタンスとデータベースの検出を完了できない場合、評価レポートでそれぞれのインスタンスが **[不明]** としてマークされます。

## <a name="next-steps"></a>次のステップ

- [Azure SQL 評価](./how-to-create-azure-sql-assessment.md)を作成する方法をご覧ください
- [Azure SQL 評価](./concepts-azure-sql-assessment-calculation.md)の詳細をご覧ください