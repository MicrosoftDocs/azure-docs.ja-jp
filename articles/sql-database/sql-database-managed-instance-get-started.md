---
title: Azure portal:SQL Database マネージド インスタンスの作成 | Microsoft Docs
description: SQL Database マネージド インスタンス、ネットワーク環境、アクセス用のクライアント VM を作成します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 09/26/2019
ms.openlocfilehash: 4f9ea699bd6d09f902a3ff97c95ff3455926a9d8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350923"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>クイック スタート:Azure SQL Database マネージド インスタンスの作成

このクイックスタートでは、Azure portal で Azure SQL Database [マネージド インスタンス](sql-database-managed-instance.md)を作成する方法について説明します。

> [!IMPORTANT]
> 制限については、[サポートされているリージョン](sql-database-managed-instance-resource-limits.md#supported-regions)と[サポートされているサブスクリプションの種類](sql-database-managed-instance-resource-limits.md#supported-subscription-types)に関するセクションを参照してください。

## <a name="sign-in-to-azure-portal"></a>Azure Portal にサインインする

Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

[Azure ポータル](https://portal.azure.com/)にサインインします。

## <a name="create-a-managed-instance"></a>マネージド インスタンスを作成する

次の手順に従って、マネージド インスタンスを作成します。

1. Azure portal の左側のメニューにある **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「**Azure SQL**」と入力します。
2. **[+ 追加]** を選択して、 **[Select SQL deployment option]\(SQL デプロイ オプションの選択\)** ページを開きます。 **[マネージド インスタンス]** タイルの **[詳細の表示]** を選択すると、Azure SQL Database マネージド インスタンスに関する追加情報を表示できます。
3. **作成** を選択します。

   ![マネージド インスタンスを作成する](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. **[Azure SQL Database マネージド インスタンスの作成]** プロビジョニング フォーム上のタブを使用して、必須のオプションと任意のオプションの情報を追加します。 以下のセクションで、これらのタブについて説明します。

### <a name="basics"></a>基本

- **[基本]** タブで必須情報を記入します。これは、マネージド インスタンスをプロビジョニングするために必要な最小限の情報セットです。

   ![マネージド インスタンスを作成するための [基本] タブ](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   このタブで必要となる情報については、次の表を参考にしてください。

   | Setting| 推奨値 | 説明 |
   | ------ | --------------- | ----------- |
   | **サブスクリプション** | 該当するサブスクリプション。 | 新しいリソースを作成するアクセス許可を与えるサブスクリプション。 |
   | **リソース グループ** | 新規または既存のリソース グループ。|有効なリソース グループ名については、[名前付け規則と制限](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   | **マネージド インスタンス名** | 有効な名前。|有効な名前については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   | **[リージョン]** |マネージド インスタンスの作成先となるリージョン。|リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。|
   | **マネージド インスタンス管理者ログイン** | 有効なユーザー名。 | 有効な名前については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 "serveradmin" は予約済みのサーバー レベルのロールであるため、使用しないでください。|
   | **パスワード** | 有効なパスワード。| パスワードは 16 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|

- **[マネージド インスタンスの構成]** を選択して、コンピューティング リソースとストレージ リソースのサイズを指定し、価格レベルを確認します。 スライダーまたはテキスト ボックスを使用して、記憶域のサイズと仮想コアの数を指定します。 完了したら、 **[適用]** を選択して選択内容を保存します。 

   ![マネージド インスタンスのフォーム](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

- マネージド インスタンスを作成する前に選択内容を確認するには、 **[確認と作成]** を選択できます。 または、ネットワーク オプションを構成します。これを行うには、 **[次へ: ネットワーク]** を選択します。

### <a name="networking"></a>ネットワーク

- **[ネットワーク]** タブにオプションの情報を記入します。この情報を省略すると、ポータルによって既定の設定が適用されます。

   ![マネージド インスタンスを作成するための [ネットワーク] タブ](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   このタブで必要となる情報については、次の表を参考にしてください。

   | Setting| 推奨値 | 説明 |
   | ------ | --------------- | ----------- |
   | **Virtual Network** | **[新しい仮想ネットワークの作成]** または有効な仮想ネットワークとサブネットを選択します。| ネットワークまたはサブネットが使用できない場合は、新しいマネージド インスタンスのターゲットとして選択する前に、[ネットワーク要件を満たすように変更する](sql-database-managed-instance-configure-vnet-subnet.md)必要があります。 マネージド インスタンスのネットワーク環境を構成するための要件については、[マネージド インスタンスの仮想ネットワークの構成](sql-database-managed-instance-connectivity-architecture.md)に関する記事を参照してください。 |
   | **接続の種類** | 接続の種類として、プロキシまたはリダイレクトを選択します。|接続の種類の詳細については、[Azure SQL Database の接続ポリシー](sql-database-connectivity-architecture.md#connection-policy)に関するページを参照してください。|
   | **パブリック エンドポイント**  | **[有効化]** を選択します。 | パブリック データ エンドポイントを介してマネージド インスタンスにアクセスできるようにするには、このオプションを有効にする必要があります。 | 
   | **許可するアクセス元** (**パブリック エンドポイント**が有効な場合) | いずれかのオプションを選択します。   |ポータルでの操作により、パブリック エンドポイントを使用するセキュリティ グループを構成できます。 </br> </br> 実際のシナリオに基づいて、次のいずれかのオプションを選択します。 </br> <ul> <li>**Azure サービス**:Power BI または別のマルチテナント サービスから接続する場合は、このオプションをお勧めします。 </li> <li> **インターネット**:マネージド インスタンスを迅速に作成する必要がある場合にテスト目的で使用します。 運用環境ではお勧めしません。 </li> <li> **アクセスなし**:このオプションでは**拒否**セキュリティ規則が作成されます。 パブリック エンドポイントを介してマネージド インスタンスにアクセスできるようにするには、この規則を変更します。 </li> </ul> </br> パブリック エンドポイントのセキュリティの詳細については、「[パブリック エンドポイントで安全に Azure SQL Database マネージド インスタンスを使用する](sql-database-managed-instance-public-endpoint-securely.md)」を参照してください。|

- マネージド インスタンスを作成する前に、 **[確認と作成]** を選択して、選択内容を確認できます。 または、さらにカスタム設定を構成します。これを行うには、 **[次へ: 追加設定]** を選択します。

### <a name="additional-settings"></a>追加設定

- **[追加設定]** タブにオプションの情報を記入します。この情報を省略すると、ポータルによって既定の設定が適用されます。

   ![マネージド インスタンスを作成するための [追加設定] タブ](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   このタブで必要となる情報については、次の表を参考にしてください。

   | Setting| 推奨値 | 説明 |
   | ------ | --------------- | ----------- |
   | **照合順序** | マネージド インスタンスに対して使用する照合順序を選択します。 SQL Server からデータベースを移行する場合は、`SELECT SERVERPROPERTY(N'Collation')` を使用してソースの照合順序を確認し、その値を使用してください。| 照合順序の詳細については、「[サーバーの照合順序の設定または変更](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation)」を参照してください。|   
   | **タイム ゾーン** | マネージド インスタンスで監視するタイム ゾーンを選択します。|詳細については、[タイム ゾーン](sql-database-managed-instance-timezone.md)に関するページを参照してください。|
   | **フェールオーバー セカンダリとして使用する** | **[はい]** を選択します。 | マネージド インスタンスをフェールオーバー グループのセカンダリとして使用するには、このオプションを有効にします。|
   | **プライマリ マネージド インスタンス** ( **[フェールオーバー セカンダリとして使用する]** が **[はい]** に設定されている場合) | 作成しているマネージド インスタンスと同じ DNS ゾーンに参加することになる既存のプライマリ マネージド インスタンスを選択します。 | この手順により、フェールオーバー グループの作成後の構成が有効になります。 詳細については、[チュートリアル: SQL Database マネージド インスタンスをフェールオーバー グループに追加する](sql-database-managed-instance-failover-group-tutorial.md)」を参照してください。|

### <a name="review--create"></a>確認と作成

5. マネージド インスタンスを作成する前に選択内容を確認するには、 **[確認と作成]** タブを選択します。

   ![マネージド インスタンスを確認して作成するためのタブ](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

6. **[作成]** を選択して、マネージド インスタンスのプロビジョニングを開始します。

> [!IMPORTANT]
> マネージト インスタンスのデプロイは、実行時間の長い操作です。 サブネットへの最初のインスタンスのデプロイは、通常、既存のマネージド インスタンスが含まれるサブネットへのデプロイよりもはるかに長い時間がかかります。 平均的なプロビジョニング時間については、「[マネージド インスタンスの管理操作](sql-database-managed-instance.md#managed-instance-management-operations)」を参照してください。

### <a name="monitor-deployment-progress"></a>デプロイの進行状況を監視する

7. **[通知]** アイコンを選択してデプロイの状態を表示します。

   ![マネージド インスタンスのデプロイの進行状況](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

8. 通知内の **[デプロイを実行しています]** を選択して [マネージド インスタンス] ウィンドウを開き、デプロイの進行状況を詳しく監視します。 

> [!TIP]
> Web ブラウザーを閉じているか、[デプロイの進行状況] 画面から移動している場合は、次の手順に従ってその画面を見つけます。
> 1. Azure portal で、マネージド インスタンスをデプロイしている ( **[基本]** タブの) リソース グループを開きます。
> 2. **[デプロイ]** を選択します。
> 3. 進行中のマネージド インスタンスのデプロイ操作を選択します。

## <a name="post-deployment-operations"></a>デプロイ後の操作

作成されたリソースを確認したり、ネットワークの設定を微調整したり、ホストの接続情報 (FQDN) を取得したりするには、このセクションの手順に従ってください。

### <a name="view-resources-created"></a>作成されたリソースを表示する

マネージド インスタンスが正常にデプロイされた後、作成されたリソースを確認するには、次の手順に従います。

1. マネージド インスタンスのリソース グループを開きます。 「[マネージド インスタンスを作成する](#create-a-managed-instance)」クイック スタートで作成したリソースを確認します。

   ![マネージド インスタンスのリソース](./media/sql-database-managed-instance-get-started/resources.png)

### <a name="view-and-fine-tune-network-settings"></a>ネットワークの設定を表示して微調整する

ネットワークの設定を必要に応じて微調整するには、次の点を調査します。

1. ルート テーブルを選択して、作成されたユーザー定義ルート (UDR) を確認します。

   ![ルート テーブル](./media/sql-database-managed-instance-get-started/route-table.png)

2. ルート テーブルで、マネージド インスタンスの仮想ネットワークから、またはその内部でトラフィックをルーティングするエントリを確認します。 ルート テーブルを手動で作成または構成する場合は、これらのエントリをマネージド インスタンスのルート テーブルに確実に作成します。

   ![ローカルへのマネージド インスタンス サブネットのエントリ](./media/sql-database-managed-instance-get-started/udr.png)

3. リソース グループに戻り、ネットワーク セキュリティ グループを選択します。

   ![ネットワーク セキュリティ グループ](./media/sql-database-managed-instance-get-started/network-security-group.png)

4. 受信および送信セキュリティ規則を確認します。 

   ![セキュリティ規則](./media/sql-database-managed-instance-get-started/security-rules.png)

> [!IMPORTANT]
> マネージド インスタンス用のパブリック エンドポイントを構成した場合は、パブリック インターネットからマネージド インスタンスに接続できるよう、ポートを開放してネットワーク トラフィックを許可する必要があります。詳細については、[マネージド インスタンス用のパブリック エンドポイントの構成](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)に関するページを参照してください。
>

### <a name="retrieve-connection-details-to-managed-instance"></a>マネージド インスタンスへの接続情報を取得する

マネージド インスタンスに接続するには、次の手順に従って、ホスト名と完全修飾ドメイン名 (FQDN) を取得します。

1. リソース グループに戻り、マネージド インスタンスを選択します。

   ![リソース グループ内のマネージド インスタンス](./media/sql-database-managed-instance-get-started/managed-instance.png)

2. **[概要]** タブで、 **[ホスト]** プロパティを探します。 次のクイック スタートで使用するマネージド インスタンスのホスト名をコピーします。

   ![ホスト名](./media/sql-database-managed-instance-get-started/host-name.png)

   コピーされた値は、マネージド インスタンスへの接続に使用できる完全修飾ドメイン名 (FQDN) を表します。 たとえば、このアドレスは *your_host_name.a1b2c3d4e5f6.database.windows.net* のような形式になります。

## <a name="next-steps"></a>次の手順

マネージド インスタンスに接続する方法を学習する
- アプリケーションの接続オプションの概要については、[マネージド インスタンスにアプリケーションを接続する](sql-database-managed-instance-connect-app.md)方法に関するページを参照してください。
- Azure 仮想マシンからマネージド インスタンスに接続する方法を説明するクイックスタートについては、[Azure 仮想マシン接続の構成](sql-database-managed-instance-configure-vm.md)に関するページを参照してください。
- ポイント対サイト接続を使用してオンプレミスのクライアント コンピューターからマネージド インスタンスに接続する方法を説明するクイックスタートについては、[ポイント対サイト接続の構成](sql-database-managed-instance-configure-p2s.md)に関する記事を参照してください。

既存の SQL Server データベースをオンプレミスからマネージド インスタンスに復元する 
- [移行用の Azure Database Migration Service](../dms/tutorial-sql-server-to-managed-instance.md) を使用してデータベース バックアップ ファイルから復元します。 
- [T-SQL RESTORE コマンド](sql-database-managed-instance-get-started-restore.md)を使用してデータベース バックアップ ファイルから復元します。

組み込みのトラブルシューティング インテリジェンスを使用したマネージド インスタンス データベースの高度なパフォーマンス監視については、[Azure SQL Analytics を使用した Azure SQL Database の監視](../azure-monitor/insights/azure-sql.md)に関するページを参照してください。
