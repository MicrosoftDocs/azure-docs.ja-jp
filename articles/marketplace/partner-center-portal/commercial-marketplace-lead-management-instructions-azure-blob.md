---
title: Azure Blob Storage によるリード管理 - Microsoft コマーシャル マーケットプレース
description: Azure BLOB を使用して Microsoft AppSource および Azure Marketplace のリードを構成する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/01/2020
ms.openlocfilehash: bd2f3d40b1aea1c0133f95e069ebfd527d30bd59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94491105"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>Azure Blob Storage を使用し、コマーシャル マーケットプレースの潜在顧客を管理する

>[!Caution]
>Azure BLOB ストレージのコマーシャル マーケットプレース サポートは非推奨になっており、オファーからリードを処理することはできなくなりました。 コマーシャル マーケットプレース オファーに現在、Azure BLOB 向けにリード管理が構成されている場合、潜在顧客を受け取ることができなくなります。 リード管理構成を他のリード管理オプションのいずれかに更新してください。 その他のオプションについては、[リード管理のランディング ページ](./commercial-marketplace-get-customer-leads.md)に関する記事をご覧ください。"

 Microsoft AppSource と Azure Marketplace のリードを受け取るためにパートナー センターで顧客関係管理 (CRM) システムが明示的にサポートされていない場合、Azure Blob Storage を使用できます。 その後、データをエクスポートして CRM システムにインポートすることを選択できます。 この記事の手順では、Azure Storage アカウントと、そのアカウント下の BLOB を作成するプロセスについて説明します。 さらに、Power Automate を使用して新しいフローを作成し、オファーでリードを受信したときに電子メール通知を送信することもできます。

>[!NOTE]
>以下の手順で使用される Power Automate コネクタには、Power Automate の有料サブスクリプションが必要です。 この記事の手順を実行する前に、次のことを考慮してください。

## <a name="configure-azure-blob-storage"></a>Azure Blob Storage の構成

1. Azure アカウントを持っていない場合は、[無料試用版アカウントを作成する](https://azure.microsoft.com/pricing/free-trial/)ことができます。

2. Azure アカウントがアクティブになった後、[Azure portal](https://portal.azure.com) にサインインします。

3. Azure portal 上で、次の手順を使用してストレージ アカウントを作成します。  
    1. 左側のメニュー バーにある **[+ リソースの作成]** を選択します。  **[新規]** ウィンドウ (ブレード) が、右側に表示されます。
    2. **[新規]** ウィンドウで **[ストレージ]** を選択します。  **[おすすめ]** 一覧が右側に表示されます。
    3. **[ストレージ アカウント]** を選択して、アカウントの作成を開始します。  「[ストレージ アカウントの作成](../../storage/common/storage-account-create.md?tabs=azure-portal)」の記事にある手順に従ってください。

    ![Azure ストレージ アカウントを作成する手順](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    ストレージ アカウントの詳細については、こちらの[クイックスタート](../../storage/blobs/storage-quickstart-blobs-portal.md)を参照してください。  ストレージの価格について詳しくは、[ストレージの価格](https://azure.microsoft.com/pricing/details/storage/)に関する記事をご覧ください。

4. ストレージ アカウントがプロビジョニングされるまで待機します。通常は数分かかる処理です。  その後、 **[リソースをすべて表示する]** を選択するか、または Azure portal の左側のナビゲーション メニューバーから **[すべてのリソース]** を選択して、Azure portal の **[ホーム]** ページからお使いのストレージ アカウントへアクセスします。

    ![Azure ストレージ アカウントにアクセスする](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. ストレージ アカウントのウィンドウで、 **[アクセス キー ]** を選択し、キーの *[接続文字列]* 値をコピーします。 この値を保存します。これは、マーケットプレース オファーのリードを受け取るために発行ポータル内で提供する必要がある "*ストレージ アカウント接続文字列*" 値です。

     接続文字列の例を次に示します。

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![[接続文字列] テキスト ボックスが強調表示された [アクセス キー] ページを示すスクリーンショット。](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. ストレージ アカウントのページで **[BLOB]** を選択します。

   ![BLOB が選択された Azure Storage アカウント ページのスクリーンショット](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. [BLOB] ページが表示されたら、 **[+ コンテナー]** ボタンを選択します。

8. 新しいコンテナーの **名前** を入力します。 コンテナー名は小文字である必要があり、英文字または数字で始まる必要があり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。 コンテナーと BLOB の名前の詳細については、「[Naming and referencing containers, blobs, and metadata (コンテナー、BLOB、およびメタデータの名前付けと参照)](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

    この値を保存します。これは、マーケットプレース オファーのリードを受け取るために発行ポータル内で提供する必要がある "*コンテナー名*" 値です。

9. コンテナーへのパブリック アクセスのレベルを **[Private (no anonymous access)]\(プライベート (匿名アクセスなし)\)** として設定します。

10. **[OK]** を選択してコンテナーを作成します。

    ![新しいコンテナー](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>リードを Azure Blob Storage に送信するようにオファーを構成する

発行ポータル内でオファーのリード管理情報を構成する準備ができたら、次の手順に従います。

1. オファーの **[オファーのセットアップ]** ページに移動します。
2. **[潜在顧客]** セクションで、 **[接続]** を選択します。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-blob/customer-leads.png" alt-text="[潜在顧客]":::

3. [接続の詳細] ポップアップ ウィンドウで、[リードのターゲット] として **[Azure Blob]** を選択します。

    ![接続の詳細](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. 手順に従って取得した **[コンテナー名]** と **[ストレージ アカウント接続文字列]** を指定します。

    * コンテナー名の例: `marketplaceleadcontainer`
    * ストレージ アカウント接続文字列の例:`DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![接続の詳細](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. **[保存]** を選択します。

    > [!NOTE]
    > オファーのリードを受け取るには、まずオファーの残りの部分の構成を完了して発行する必要があります。