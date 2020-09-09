---
title: 組織の外部と共有する (Azure portal) - Azure Data Share のクイックスタート
description: このクイックスタートでは、Azure Data Share を使用して顧客やパートナーとデータを共有する方法について説明します。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: quickstart
ms.date: 08/19/2020
ms.openlocfilehash: 5ceaf949df88468b2239bd901f639ba6096b0d5f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269643"
---
# <a name="quickstart-share-data-using-azure-data-share-in-the-azure-portal"></a>クイック スタート:Azure portal で Azure Data Share を使用してデータを共有する

このクイックスタートでは、Azure portal を使用して、新しい Azure Data Share を設定する方法について説明します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。


## <a name="create-a-data-share-account"></a>Data Share アカウントを作成する

Azure リソース グループに Azure Data Share リソースを作成します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. ポータルの左上隅にある **[リソースの作成]** ボタン (+) を選択します。

1. *Data Share* を検索します。

1. **[Data Share]** を選択し、 **[作成]** を選択します。

1. Azure Data Share リソースの基本的な詳細に、次の情報を入力します。 

   **設定** | **推奨値** | **フィールドの説明**
   |---|---|---|
   | サブスクリプション | 該当するサブスクリプション | Data Share アカウントに使う Azure サブスクリプションを選択します。|
   | リソース グループ | *test-resource-group* | 既存のリソース グループを使用するか、新しいリソース グループを作成します。 |
   | 場所 | *米国東部 2* | Data Share アカウントのリージョンを選択します。
   | 名前 | *datashareaccount* | Data Share アカウントの名前を指定します。 |

1. **[Review + create]\(レビュー + 作成\)** を選択し、 **[作成]** を選択して Data Share アカウントをプロビジョニングします。 通常、新しい Data Share アカウントのプロビジョニングにかかる時間は約 2 分以下です。

1. デプロイが完了したら、 **[リソースに移動]** を選択します。

## <a name="create-a-share"></a>共有を作成する

1. Data Share の [概要] ページに移動します。

   ![データを共有する](./media/share-receive-data.png "データを共有する") 

1. **[Start sharing your data]\(データの共有の開始する\)** を選択します。

1. **［作成］** を選択します

1. 共有の詳細を入力します。 名前、共有の種類、共有の内容の説明、利用規約 (省略可能) を指定します。 

   ![EnterShareDetails](./media/enter-share-details.png "共有の詳細を入力する") 

1. **[続行]** をクリックします。

1. 共有にデータセットを追加するには、 **[データセットの追加]** を選択します。 

   ![共有にデータセットを追加する](./media/datasets.png "データセット")

1. 追加するデータセットの種類を選択します。 前の手順で選択した共有の種類 (スナップショットまたはインプレース) によって異なる種類のデータセット一覧が表示されます。 Azure SQL Database または Azure SQL Data Warehouse から共有する場合は、いくつかの SQL 資格情報を入力するように求められます。 前提条件の中で作成したユーザーを使用して、認証します。

   ![AddDatasets](./media/add-datasets.png "データセットを追加する")    

1. 共有するオブジェクトに移動し、[Add Datasets]\(データセットの追加\) を選択します。 

   ![SelectDatasets](./media/select-datasets.png "データセットを選択する")    

1. [Recipients]\(受信者\) タブで、[+ Add Recipient]\(+ 受信者の追加\) を選択して、データ コンシューマーのメール アドレスを入力します。

   ![AddRecipients](./media/add-recipient.png "受信者の追加") 

1. **[続行]** をクリックします。

1. スナップショット共有タイプを選択した場合は、データの更新をデータ コンシューマーに提供するスナップショット スケジュールを構成できます。 

   ![EnableSnapshots](./media/enable-snapshots.png "スナップショットを有効化する") 

1. 開始時刻と繰り返しの間隔を選択します。 

1. **[続行]** をクリックします。

1. [Review + Create]\(レビュー + 作成\) タブで、パッケージの内容、設定、受信者、および同期の設定を確認します。 **［作成］** を選択します

ご自分の Azure データ共有が作成され、データ共有の受信者が招待を受け取れる状態になりました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソースが不要になったら、**Data Share の [概要]** ページに移動し、 **[削除]** を選択して削除します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Data Share を作成する方法について説明しました。 データ コンシューマーがデータ共有を受け入れて受信できるようにする方法については、続けて[データの受け入れと受信](subscribe-to-data-share.md)に関するチュートリアルを行ってください。 
