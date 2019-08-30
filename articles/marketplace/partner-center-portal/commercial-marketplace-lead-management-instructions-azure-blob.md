---
title: 商業マーケットプレース プログラム内の Azure テーブル | Azure Marketplace
description: Azure BLOB 用にリード管理を構成します
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: evansma
ms.openlocfilehash: c67855422808f5ec4c81242edcece4e447f2f44f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69901455"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Azure BLOB でのリード管理の手順

>[!Caution]
>マーケットプレース オファーからリードを処理する Azure BLOB オプションは非推奨となりました。 現在、Azure BLOB 用のリード管理構成を使用して発行されたオファーがある場合は、潜在顧客を受け取ることができなくなります。 リード管理構成を他のリード管理オプションのいずれかに更新してください。 その他のオプションについては、[リード管理のランディング ページ](./commercial-marketplace-get-customer-leads.md)に関する記事をご覧ください。"

Azure Marketplace と AppSource のリードを受け取るためにパートナー センターで顧客関係管理 (CRM) システムが明示的にサポートされていない場合は、Azure BLOB を使用してこれらのリードを処理できます。 その後、データをエクスポートして CRM システムにインポートすることを選択できます。 この記事の手順では、Azure Storage アカウントと、そのアカウント下の Azure BLOB を作成するプロセスについて説明します。 さらに、Microsoft Flow を使用して新しいフローを作成し、オファーでリードを受け取ったときに電子メール通知を送信することもできます。


## <a name="how-to-configure-azure-blob"></a>Azure BLOB を構成する方法

1. Azure アカウントを持っていない場合は、[無料試用版アカウントを作成する](https://azure.microsoft.com/pricing/free-trial/)ことができます。
1. Azure アカウントがアクティブになった後、[Azure portal](https://portal.azure.com) にサインインします。
1. Azure portal 上で、次の手順を使用してストレージ アカウントを作成します。  
    1. 左側のメニュー バーにある **[+ リソースの作成]** を選択します。  **[新規]** ウィンドウ (ブレード) が、右側に表示されます。
    2. **[新規]** ウィンドウで **[ストレージ]** を選択します。  **[おすすめ]** 一覧が右側に表示されます。
    3. **[ストレージ アカウント]** を選択して、アカウントの作成を開始します。  「[ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)」の記事にある手順に従ってください。

    ![Azure ストレージ アカウントを作成する手順](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    ストレージ アカウントについて詳しくは、[クイック スタートのチュートリアル](https://docs.microsoft.com/azure/storage/)に関するページをご覧ください。  ストレージの価格について詳しくは、[ストレージの価格](https://azure.microsoft.com/pricing/details/storage/)に関する記事をご覧ください。

4. ストレージ アカウントがプロビジョニングされるまで待機します。通常は数分かかる処理です。  その後、 **[リソースをすべて表示する]** を選択するか、または Azure portal の左側のナビゲーション メニューバーから **[すべてのリソース]** を選択して、Azure portal の **[ホーム]** ページからお使いのストレージ アカウントへアクセスします。

    ![Azure ストレージ アカウントにアクセスする](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. ストレージ アカウントのウィンドウで、 **[アクセス キー ]** を選択し、キーの *[接続文字列]* 値をコピーします。 この値を保存します。これは、マーケットプレース オファーのリードを受け取るために発行ポータル内で提供する必要がある "*ストレージ アカウント接続文字列*" 値です。

     接続文字列の例を次に示します。

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure ストレージ キー](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. ストレージ アカウントのページで **[BLOB]** を選択します。

   ![Azure ストレージ キー](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. [BLOB] ページが表示されたら、 **[+ コンテナー]** ボタンを選択します。

8. 新しいコンテナーの**名前**を入力します。 コンテナー名は小文字である必要があり、英文字または数字で始まる必要があり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。 コンテナーと BLOB の名前の詳細については、「[Naming and referencing containers, blobs, and metadata (コンテナー、BLOB、およびメタデータの名前付けと参照)](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

    この値を保存します。これは、マーケットプレース オファーのリードを受け取るために発行ポータル内で提供する必要がある "*コンテナー名*" 値です。

9. コンテナーへのパブリック アクセスのレベルを **[Private (no anonymous access)]\(プライベート (匿名アクセスなし)\)** として設定します。

10. **[OK]** を選択してコンテナーを作成します。

    ![新しいコンテナー](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>リードを Azure BLOB に送信するようにオファーを構成する

発行ポータル内でオファーのリード管理情報を構成する準備ができたら、次の手順に従います。

1. オファーの **[オファーのセットアップ]** ページに移動します。
2. [リード管理] セクションで **[接続]** を選択します。

    ![オファーの接続](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. [接続の詳細] ポップアップ ウィンドウで、[リードのターゲット] として **[Azure Blob]** を選択します。

    ![接続の詳細](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. 手順に従って取得した **[コンテナー名]** と **[ストレージ アカウント接続文字列]** を指定します。

    * コンテナー名の例: `marketplaceleadcontainer`
    * ストレージ アカウント接続文字列の例:`DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![接続の詳細](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. **[保存]** を選択します。

    > [!NOTE]
    > オファーのリードを受け取るには、まずオファーの残りの部分の構成を完了して発行する必要があります。


