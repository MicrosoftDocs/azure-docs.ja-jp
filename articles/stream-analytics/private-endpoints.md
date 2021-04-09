---
title: Azure Stream Analytics クラスターでマネージド プライベート エンドポイントを作成および削除する
description: Azure Stream Analytics クラスターでプライベート エンドポイントを管理する方法について説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 9939130782594c03a497d98ce6cd9b33b28eadec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101718405"
---
# <a name="create-and-delete-managed-private-endpoints-in-an-azure-stream-analytics-cluster"></a>Azure Stream Analytics クラスターでマネージド プライベート エンドポイントを作成および削除する

クラスターで実行されている Azure Stream Analytics ジョブを接続して、ファイアウォールまたは Azure Virtual Network (VNet) の内側にあるリソースの入力と出力を行うことができます。 最初に、Stream Analytics クラスター内のリソース (Azure Event Hub、Azure SQL Database など) 用のマネージド プライベート エンドポイントを作成します。 次に、入力または出力からのプライベート エンドポイント接続を承認します。

接続を承認すると、Stream Analytics クラスターで実行されているすべてのジョブが、プライベート エンドポイント経由でリソースにアクセスできるようになります。 この記事では、Stream Analytics クラスターでプライベート エンドポイントを作成および削除する方法を説明します。 Azure SQL Database、Azure Storage、Azure Data Lake Storage Gen2、Azure Event Hubs、Azure Service Bus のプライベート エンドポイントを作成できます。 他のサービスのプライベート エンドポイントは、間もなく追加される予定です。 

## <a name="create-managed-private-endpoint-in-stream-analytics-cluster"></a>Stream Analytics クラスター内にマネージド プライベート エンドポイントを作成する

このセクションでは、Stream Analytics クラスター内にプライベート エンドポイントを作成する方法について説明します。

1. Azure portal 上で、目的の Stream Analytics クラスターを見つけて選択します。

1. **[設定]** で、 **[Managed private endpoints]\(マネージド プライベート エンドポイント\)** を選択します。

1. **[New]\(新規\)** を選択し、次の情報を入力して、プライベート エンドポイント経由で安全にアクセスするリソースを選択します。

   |設定|値|
   |---|---|
   |名前|プライベート エンドポイントの名前を入力します。 この名前を取得する場合は、一意の名前を作成します。|
   |接続方法|**[マイ ディレクトリ内の Azure リソースに接続します]** を選択します。<br><br>自分のリソースの 1 つを選択し、プライベート エンドポイントを使用して安全に接続したり、共有されているリソース ID または別名を使用して、他のユーザーのリソースに接続したりすることができます。|
   |サブスクリプション|サブスクリプションを選択します。|
   |リソースの種類|[リソースにマップするリソースの種類](../private-link/private-endpoint-overview.md#private-link-resource)を選択します。|
   |リソース|プライベート エンドポイントを使用して接続するリソースを選択します。|
   |ターゲット サブリソース|プライベート エンドポイントからアクセスできるようになる、上記で選択したリソースのサブリソースの種類。|

   ![プライベート エンドポイントの作成エクスペリエンス](./media/private-endpoints/create-private-endpoint.png)

1. ターゲット リソースからの接続を承認します。 たとえば、前のステップで Azure SQL Database インスタンスへのプライベート エンドポイントを作成した場合、この SQL Database インスタンスに移動し、承認される必要がある保留中の接続を確認する必要があります。 接続要求が表示されるまで、数分かかる場合があります。

    ![プライベート エンドポイントを承認する](./media/private-endpoints/approve-private-endpoint.png)

1. Stream Analytics クラスターに戻り、数分以内に状態が **[保留中の顧客による承認]** から **[保留中の DNS セットアップ]** を経て **[セットアップの完了]** に変わることを確認できます。

## <a name="delete-a-managed-private-endpoint-in-a-stream-analytics-cluster"></a>Stream Analytics クラスター内のマネージド プライベート エンドポイントを削除する

1. Azure portal 上で、目的の Stream Analytics クラスターを見つけて選択します。

1. **[設定]** で、 **[Managed private endpoints]\(マネージド プライベート エンドポイント\)** を選択します。

1. 削除するプライベート エンドポイントを選択し、 **[削除]** を選択します。

   ![プライベート エンドポイントを削除する](./media/private-endpoints/delete-private-endpoint.png)

## <a name="next-steps"></a>次のステップ

Azure Stream Analytics クラスターでプライベート エンドポイントを管理する方法の概要がわかりました。 次に、クラスターをスケーリングし、クラスターでジョブを実行する方法について学習できます。

* [Azure Stream Analytics クラスターのスケーリング](scale-cluster.md)
* [Stream Analytics クラスター内で Stream Analytics ジョブを管理する](manage-jobs-cluster.md)
