---
title: チュートリアル - Azure Data Share プレビューを使用したデータの受け入れと受信
description: チュートリアル - Azure Data Share プレビューを使用したデータの受け入れと受信
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: fc63c1a0b3b496de8e5ecea58f79f1db9d872e80
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838443"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share-preview"></a>チュートリアル:Azure Data Share プレビューを使用したデータの受け入れと受信

このチュートリアルでは、Azure Data Share プレビューを使用してデータ共有の招待を受け入れる方法を学習します。 自分が共有しているデータを受信する方法と、自分が共有しているデータのスナップショットを常に最新に保つために通常の更新間隔を有効にする方法を学習します。 

> [!div class="checklist"]
> * Azure Data Share プレビューの招待を受け入れる方法
> * Azure Data Share プレビュー アカウントを作成する
> * 自分のデータの受信先を指定する
> * スケジュールされた更新のため、自分のデータ共有のサブスクリプションを作成する

## <a name="prerequisites"></a>前提条件
データ共有の招待を受け入れる前に、次に一覧表示されている多くの Azure リソースをプロビジョニングする必要があります。 

データ共有の招待を受け入れる前に、すべての前提条件を満たしていることを確認します。 

* Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* Azure Storage アカウント:[Azure Storage アカウント](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)をまだお持ちでない場合は、作成できます。 
* Data Share の招待:" **<yourdataprovider@domain.com>** からの Azure Data Share の招待" という件名の Microsoft Azure からの招待。
* ストレージ アカウントにロールの割り当てを追加する権限。これは、*Microsoft.Authorization/role assignments/write* 権限に含まれています。 この権限は、所有者ロール内に存在します。 
* Microsoft.DataShare のリソースプロバイダーの登録。 この手順の詳細については、[Azure リソースプロバイダー](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)のドキュメントを参照してください。 

> [!IMPORTANT]
> Azure Data Share を受け入れて受信するには、まず Microsoft.DataShare リソース プロバイダーを登録し、データを受け入れるストレージ アカウントの所有者になる必要があります。 「[Azure Data Share プレビューのトラブルシューティング](data-share-troubleshoot.md)」に記載されている指示に従って、データ共有リソース プロバイダーを登録し、自分自身をストレージ アカウントの所有者として追加します。 

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="open-invitation"></a>招待を開く

自分の受信トレイでデータ プロバイダーからの招待を確認します。 招待は Microsoft Azure からで、件名は " **<yourdataprovider@domain.com> からの Azure Data Share の招待**" になっています。 複数の招待がある場合は、正しい共有を確実に受け入れるため、共有名をメモしておきます。 

**[招待を表示]** を選択して、Azure で招待を確認します。 これにより [Received Share]\(受信した共有\) ビューに移動します。

![招待](./media/invitations.png "招待の一覧") 

表示する共有を選択します。 

## <a name="accept-invitation"></a>招待を受け入れる
**[使用条件]** を含む、すべてのフィールドを確認してください。 使用条件に同意する場合は、同意を示すボックスをオンにする必要があります。 

![使用条件](./media/terms-of-use.png "使用条件") 

*[Target Data Share Account]\(ターゲット データ共有アカウント\)* の下で、ご自分のデータ共有のデプロイ先となるサブスクリプションとリソース グループを選択します。 

**[Data Share Account]\(データ共有アカウント\)** フィールドには、既存のデータ共有アカウントがない場合には、 **[新規作成]** を選択します。 それ以外の場合は、自分のデータ共有を受け入れる既存のデータ共有アカウントを選択します。 

*[Received Share Name]\(受信した共有名\)* フィールドは、データの指定で指定された既定のままにしておくことも、受信した共有のために新しい名前を指定することもできます。 

![ターゲット データ共有アカウント](./media/target-data-share.png "ターゲット データ共有アカウント") 

使用条件に同意して共有の場所を指定したら、 *[Accept and Configure]\(受け入れと構成\)* を選択します。 このオプションを選択すると、共有サブスクリプションが作成され、次の画面でデータのコピー先のターゲット ストレージ アカウントを選択するように求められます。 

![受け入れオプション](./media/accept-options.png "受け入れオプション") 

今すぐ招待を受け入れて、後でストレージを構成する場合は、 *[Accept and Configure later]\(受け入れて後で構成する\)* を選択します。 このオプションを使用すると、後でターゲット ストレージ アカウントを構成できます。 後でストレージの構成を続行するには、データ共有の構成を再開する方法の詳しい手順について、[ストレージ アカウントの構成方法](how-to-configure-mapping.md)のページを参照してください。 

招待を受け入れたくない場合は、 *[拒否]* を選択します。 

## <a name="configure-storage"></a>ストレージの構成
*[Target Storage Settings]\(ターゲット ストレージ設定\)* の下で、自分のデータの受信先となるサブスクリプション、リソース グループ、ストレージ アカウントを選択します。 

![ターゲット ストレージ設定](./media/target-storage-settings.png "ターゲット ストレージ") 

自分のデータの定期的な更新を受信するには、スナップショットの設定が有効になっていることを確認します。 スナップショットの設定スケジュールは、ご利用のデータ プロバイダーでそれがデータ共有内に含まれている場合にのみ表示されることに注意してください。 

![スナップショットの設定](./media/snapshot-settings.png "スナップショットの設定") 

*[保存]* を選択します。 

## <a name="trigger-a-snapshot"></a>スナップショットをトリガーする

スナップショットをトリガーするには、[Received Share]\(受信した共有\)、[詳細] タブの順に選択し、 **[Trigger snapshot]\(スナップショットのトリガー\)** を選択します。 これで、データの完全なスナップショットまたは増分スナップショットをトリガーすることができます。 データ プロバイダーから初めてデータを受信する場合は、完全なコピーを選択します。 

![スナップショットのトリガー](./media/trigger-snapshot.png "スナップショットのトリガー") 

最終実行状態が*成功*の場合、ストレージ アカウント開いて受信したデータを表示します。 

どのストレージ アカウントを使用したかを確認するには、 **[データセット]** を選択します。 

![コンシューマー データセット](./media/consumer-datasets.png "コンシューマー データセットのマッピング") 

## <a name="view-history"></a>履歴を表示する
スナップショットの履歴を表示するには、[Received Share]\(受信した共有\)、[履歴] の順に移動します。 ここには、過去 60 日間に生成されたすべてのスナップショットの履歴があります。 

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure Data Share の受け入れ方法と受信方法について学習しました。 Azure Data Share の概念についてさらに詳しく学習するには、[概念:Azure Data Share の用語集](terminology.md)に進んでください。