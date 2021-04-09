---
title: Azure portal を使用して SAP ソリューション向け Azure Monitor をデプロイする
description: Azure portal を使用して SAP ソリューション向け Azure Monitor をデプロイする
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: d9febb4efba85d47abe1cc11a3cb52dc0393c036
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672012"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Azure portal を使用して SAP ソリューション向け Azure Monitor をデプロイする

SAP ソリューション向け Azure Monitor は、[Azure portal](https://azure.microsoft.com/features/azure-portal) を使用して作成できます。 この方法では、SAP ソリューション向け Azure Monitor をデプロイし、プロバイダーを構成するために、ブラウザーベースのユーザー インターフェイスが提供されます。

## <a name="sign-in-to-azure-portal"></a>Azure Portal にサインインする

Azure portal (https://portal.azure.com) にサインインする

## <a name="create-monitoring-resource"></a>監視リソースを作成する

1. **Azure Marketplace** から **[SAP ソリューション向け Azure Monitor]** を選択します。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="図は、Azure Marketplace から [SAP ソリューション向け Azure Monitor] を選択する例を示しています。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. **[基本]** タブで、必要な値を指定します。 該当する場合は、既存の Log Analytics ワークスペースを使用できます。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Azure portal 構成オプションを示しています。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. 仮想ネットワークを選択する際に、監視対象のシステムがその VNET 内から到達可能であることを確認してください。 

   > [!IMPORTANT]
   > Microsoft とデータを共有するために **[共有]** を選択すると、サポート チームに追加のサポートを提供できます。

## <a name="configure-providers"></a>プロバイダーを構成する

### <a name="sap-hana-provider"></a>SAP HANA プロバイダー 

1. **[プロバイダー]** タブを選択して、構成するプロバイダーを追加します。 複数のプロバイダーを 1 つずつ追加することも、監視リソースのデプロイ後に追加することもできます。 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="[プロバイダー] タブで SAP ソリューション向け Azure Monitor に追加のプロバイダーを追加する例を示しています。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. **[プロバイダーの追加]** を選択し、ドロップダウンから **[SAP HANA]** を選択します。 

   > [!IMPORTANT]
   > SAP HANA プロバイダーが SAP HANA "マスター" ノードに対して構成されていることを確認してください。

3. HANA サーバーのプライベート IP を入力します。

4. 使用するデータベース テナントの名前を入力します。 任意のテナントを選択できますが、**SYSTEMDB** を使用すると、さまざまな監視領域が有効になるため、これを使用することをお勧めします。 

5. HANA データベースに関連付けられている SQL ポート番号を入力します。 ポート番号は、 **[3]**  +  **[instance#]**  +  **[13]** の形式で入力する必要があります (例: 30013)。 

6. 使用するデータベース ユーザー名を入力します。 データベース ユーザーに **監視** および **カタログ読み取り** ロールが割り当てられていることを確認します。 

7. 完了したら、 **[プロバイダーの追加]** を選択します。 必要に応じて他のプロバイダーを続けて追加するか、 **[確認および作成]** を選択してデプロイを完了します。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="プロバイダー情報を追加するときの構成オプションの画像です。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>高可用性クラスター (Pacemaker) プロバイダー

1. ドロップダウンから **[High-availability cluster (Pacemaker)]\(高可用性クラスター (Pacemaker)\)** を選択します。 

   > [!IMPORTANT]
   > 高可用性クラスター (Pacemaker) プロバイダーを構成するには、各ノードに ha_cluster_provider がインストールされていることを確認します。 詳細については、[HA クラスター エクスポーター](https://github.com/ClusterLabs/ha_cluster_exporter#installation)に関するセクションを参照してください。

2. Prometheus エンドポイントを http://IP:9664/metrics の形式で入力します。 
 
3. システム ID (SID)、ホスト名、およびクラスター名を入力します。

4. 完了したら、 **[プロバイダーの追加]** を選択します。 必要に応じて他のプロバイダーを続けて追加するか、 **[確認および作成]** を選択してデプロイを完了します。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="画像は HA クラスター Pacemaker プロバイダーに関連するオプションを示しています。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="os-linux-provider"></a>OS (Linux) プロバイダー 

1. ドロップダウンから [OS (Linux)] を選択します 

> [!IMPORTANT]
> OS (Linux) プロバイダーを構成するには、各 BareMetal インスタンスに Node_Exporter がインストールされていることを確認してください。 詳細については、「 [Node_Exporter](https://github.com/prometheus/node_exporter)」を参照してください

2. 名前を入力します。これは、BareMetal インスタンスの識別子になります。
3. Node Exporter エンドポイントを http://IP:9100/metrics の形式で入力します。
4. 完了したら、 **[プロバイダーの追加]** を選択します。 必要に応じて他のプロバイダーを続けて追加するか、 **[確認および作成]**  を選択してデプロイを完了します。 


### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server プロバイダー

1. Microsoft SQL Server プロバイダーを追加する前に、SQL Server Management Studio で次のスクリプトを実行して、プロバイダーの構成に必要な適切なアクセス許可を持つユーザーを作成する必要があります。

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. **[プロバイダーの追加]** を選択し、ドロップダウンから **[Microsoft SQL Server]** を選択します。 

3. Microsoft SQL Server に関連付けられている情報を使用して、フィールドに入力します。 

4. 完了したら、 **[プロバイダーの追加]** を選択します。 必要に応じて他のプロバイダーを続けて追加するか、 **[確認および作成]** を選択してデプロイを完了します。

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="画像は Microsoft SQL Server プロバイダーの追加に関連する情報を示しています。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

## <a name="next-steps"></a>次のステップ

[SAP ソリューション向け Azure Monitor](azure-monitor-overview.md) の詳細
