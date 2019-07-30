---
title: Azure portal:SQL Database マネージド インスタンスの作成 | Microsoft Docs
description: SQL Database マネージド インスタンス、ネットワーク環境、アクセス用のクライアント VM を作成します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 27439758c9e2b909bb7179b69c7aade81429f502
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070483"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>クイック スタート:Azure SQL Database マネージド インスタンスの作成

このクイック スタートでは、Azure portal で Azure SQL Database [マネージド インスタンス](sql-database-managed-instance.md)を作成する方法について説明します。

> [!IMPORTANT]
> 制限については、[サポートされているリージョン](sql-database-managed-instance-resource-limits.md#supported-regions)と[サポートされているサブスクリプションの種類](sql-database-managed-instance-resource-limits.md#supported-subscription-types)に関するセクションを参照してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-managed-instance"></a>マネージド インスタンスを作成する

次の手順に従って、マネージド インスタンスを作成します。

1. Azure portal の左上隅にある **[リソースの作成]** を選択します。
2. **[マネージド インスタンス]** を探し、 **[Azure SQL Managed Instance]** を選択します。
3. **作成** を選択します。

   ![マネージド インスタンスを作成する](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. 次の表の情報を参考にして、 **[SQL マネージド インスタンス]** フォームに必要な情報を入力します。

   | Setting| 推奨値 | 説明 |
   | ------ | --------------- | ----------- |
   | **サブスクリプション** | 該当するサブスクリプション。 | 新しいリソースを作成するアクセス許可を与えるサブスクリプション。 |
   |**マネージド インスタンス名**|有効な名前。|有効な名前については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|
   |**マネージド インスタンス管理者ログイン**|有効なユーザー名。|有効な名前については、[名前付け規則と制限事項](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 "serveradmin" は予約済みのサーバー レベルのロールであるため、使用しないでください。|
   |**パスワード**|有効なパスワード。|パスワードは 16 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
   |**タイム ゾーン**|マネージド インスタンスで監視するタイム ゾーン。|詳細については、[タイム ゾーン](sql-database-managed-instance-timezone.md)に関するページを参照してください。|
   |**照合順序**|マネージド インスタンスに対して使用する照合順序。|SQL Server からデータベースを移行する場合は、`SELECT SERVERPROPERTY(N'Collation')` を使用してソースの照合順序を確認し、その値を使用してください。 照合順序の詳細については、「[サーバーの照合順序の設定または変更](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation)」を参照してください。|
   |**Location**|マネージド インスタンスの作成先となる場所。|リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。|
   |**Virtual Network**|**[新しい仮想ネットワークの作成]** または有効な仮想ネットワークとサブネットを選択します。| ネットワークまたはサブネットが使用できない場合は、新しいマネージド インスタンスのターゲットとして選択する前に、[ネットワーク要件を満たすように変更する](sql-database-managed-instance-configure-vnet-subnet.md)必要があります。 マネージド インスタンスのネットワーク環境を構成するための要件については、[マネージド インスタンスの仮想ネットワークの構成](sql-database-managed-instance-connectivity-architecture.md)に関する記事を参照してください。 |
   |**パブリック エンドポイントを有効にする**   |パブリック エンドポイントを有効にする場合は、このオプションをオンにします。   |パブリック データ エンドポイントを介してマネージ インスタンスにアクセスできるようにするには、 **[パブリック エンドポイントを有効にする]** をオンにする必要があります。| 
   |**許可するアクセス元**   |いずれかのオプションを選択します。 <ul> <li>**Azure サービス**</li> <li>**Internet**</li> <li>**アクセスなし**</li></ul>   |ポータルでの操作により、パブリック エンドポイントを使用してセキュリティ グループを構成できます。 </br> </br> 実際のシナリオに基づいて、次のいずれかのオプションを選択します。 </br> <ul> <li>Azure サービス - Power BI やその他のマルチテナント サービスから接続する場合にお勧めします。 </li> <li> インターネット - マネージド インスタンスを迅速に作成する必要がある場合にテスト目的で使用します。 運用環境での使用はお勧めしません。 </li> <li> アクセスなし - このオプションでは拒否セキュリティ規則が作成されます。 パブリック エンドポイントを介してマネージド インスタンスにアクセスできるようにするには、この規則を変更する必要があります。 </li> </ul> </br> パブリック エンドポイントのセキュリティの詳細については、「[パブリック エンドポイントで安全に Azure SQL Database マネージド インスタンスを使用する](sql-database-managed-instance-public-endpoint-securely.md)」を参照してください。|
   |**接続の種類**|接続の種類として、プロキシまたはリダイレクトを選択します。|接続の種類の詳細については、[Azure SQL Database の接続ポリシー](sql-database-connectivity-architecture.md#connection-policy)に関するページを参照してください。|
   |**リソース グループ**|新規または既存のリソース グループ。|有効なリソース グループ名については、[名前付け規則と制限](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。|

   ![マネージド インスタンスのフォーム](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. マネージド インスタンスをインスタンス フェールオーバー グループのセカンダリとして使用するには、チェックアウトを選択し、DnsAzurePartner マネージド インスタンスを指定します。 この機能はプレビュー段階のため、スクリーンショットには表示されていません。
6. **[価格レベル]** を選択して、コンピューティング リソースとストレージ リソースのサイズを指定し、価格レベルのオプションを確認します。 [汎用] 価格レベルでは、32 GB のメモリと 16 仮想コアが既定値となっています。
7. スライダーまたはテキスト ボックスを使用して、記憶域のサイズと仮想コアの数を指定します。
8. 完了したら、 **[適用]** を選択して選択内容を保存します。 
9. **[作成]** を選択してマネージド インスタンスをデプロイします。
10. **[通知]** アイコンを選択してデプロイの状態を表示します。

    ![マネージド インスタンスのデプロイの進行状況](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. **[デプロイを実行しています]** を選択して [マネージド インスタンス] ウィンドウを開き、デプロイの進行状況を詳しく監視します。 

> [!IMPORTANT]
> サブネット内の最初のインスタンスの場合、通常、デプロイ時間は後続のインスタンスよりもはるかに長くなります。 デプロイ操作が予想以上に長くなった場合でも、デプロイ操作を取り消さないでください。

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>リソースの確認と完全修飾サーバー名の取得

デプロイが正常に完了したら、作成されたリソースを確認し、以降のクイック スタートで使用する完全修飾サーバー名を取得します。

1. マネージド インスタンスのリソース グループを開きます。 「[マネージド インスタンスを作成する](#create-a-managed-instance)」クイック スタートで作成したリソースを表示します。

   ![マネージド インスタンスのリソース](./media/sql-database-managed-instance-get-started/resources.png)

2. ルート テーブルを選択して、作成されたユーザー定義ルート (UDR) テーブルを確認します。

   ![ルート テーブル](./media/sql-database-managed-instance-get-started/route-table.png)

3. ルート テーブルで、マネージド インスタンス仮想ネットワークから、またはその内部でトラフィックをルーティングするエントリを確認します。 ルート テーブルを手動で作成または構成する場合は、これらのエントリをルート テーブルに作成する必要があります。

   ![ローカルへのマネージド インスタンス サブネットのエントリ](./media/sql-database-managed-instance-get-started/udr.png)

4. リソース グループに戻り、ネットワーク セキュリティ グループを選択して、セキュリティ規則を確認します。

   ![ネットワーク セキュリティ グループ](./media/sql-database-managed-instance-get-started/network-security-group.png)

5. 受信および送信セキュリティ規則を確認します。 マネージド インスタンスのパブリック エンドポイントを構成した場合は、[パブリック エンドポイントの構成](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)に関する記事で詳細情報を参照してください。

   ![セキュリティ規則](./media/sql-database-managed-instance-get-started/security-rules.png)

6. リソース グループに戻り、マネージド インスタンスを選択します。

   ![マネージド インスタンス](./media/sql-database-managed-instance-get-started/managed-instance.png)

7. **[概要]** タブで、 **[ホスト]** プロパティを探します。 次のクイックスタートで使用するマネージド インスタンスの完全修飾ホスト アドレスをコピーします。

   ![ホスト名](./media/sql-database-managed-instance-get-started/host-name.png)

   名前は、**your_machine_name.a1b2c3d4e5f6.database.windows.net** のようになります。

## <a name="next-steps"></a>次の手順

- マネージド インスタンスに接続する方法を学習する
  - アプリケーションの接続オプションの概要については、[マネージド インスタンスにアプリケーションを接続する](sql-database-managed-instance-connect-app.md)方法に関するページを参照してください。
  - Azure 仮想マシンからマネージド インスタンスに接続する方法を説明するクイックスタートについては、[Azure 仮想マシン接続の構成](sql-database-managed-instance-configure-vm.md)に関するページを参照してください。
  - ポイント対サイト接続を使用してオンプレミスのクライアント コンピューターからマネージド インスタンスに接続する方法を説明するクイックスタートについては、[ポイント対サイト接続の構成](sql-database-managed-instance-configure-p2s.md)に関する記事を参照してください。
- 既存の SQL Server データベースをオンプレミスからマネージド インスタンスに復元する 
    - [移行用の Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) を使用してデータベース バックアップ ファイルから復元します。 
    - [T-SQL RESTORE コマンド](sql-database-managed-instance-get-started-restore.md)を使用してデータベース バックアップ ファイルから復元します。
- 組み込みのトラブルシューティング インテリジェンスを使用したマネージド インスタンス データベースの高度なパフォーマンス監視については、[Azure SQL Analytics を使用した Azure SQL Database の監視](../azure-monitor/insights/azure-sql.md)に関するページを参照してください。
