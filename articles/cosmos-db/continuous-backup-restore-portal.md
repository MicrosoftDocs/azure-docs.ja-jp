---
title: Azure Cosmos DB で Azure portal を使用して継続的バックアップとポイントインタイム リストアを構成する
description: Azure portal を使用して復元ポイントを特定し、継続的バックアップを構成する方法について説明します。 ここでは、ライブおよび削除されたアカウントを復元する方法を示します。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ee6eedbc078e1b9c07ed00922ce1c37b38410128
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381870"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-portal"></a>継続的バックアップとポイントインタイム リストア (プレビュー) を構成および管理する - Azure portal を使用
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB のポイントインタイム リストア機能 (継続的バックアップ モード) は、現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Cosmos DB の特定の時点への復元機能 (プレビュー) を使用すると、コンテナー内で誤って行われた変更から回復し、削除されたアカウント、データベース、またはコンテナーの復元や、任意のリージョン (バックアップが存在していた場所) への復元が行えます。 継続的バックアップ モードを使用すると、過去 30 日以内の任意の時点に復元できます。

この記事では、Azure portal を使用して復元ポイントを特定し、継続的バックアップを構成する方法について説明します。

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>継続的バックアップを使用してアカウントをプロビジョニングする

新しい Azure Cosmos DB アカウントを作成するときは、 **[バックアップ ポリシー]** オプションで **[継続的]** モードを選択し、新しいアカウントでポイントインタイム リストア機能を有効にします。 アカウントでこの機能が有効になると、すべてのデータベースとコンテナーで継続的バックアップを利用できるようになります。 ポイントインタイム リストアでは、データは常に新しいアカウントに復元されます。現在、既存のアカウントに復元することはできません。

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="継続的バックアップ構成を使用して Azure Cosmos DB アカウントをプロビジョニングします。" border="true":::

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>誤った変更からライブ アカウントを復元する

Azure portal を使用して、ライブ アカウント、またはその下にある選択したデータベースやコンテナーを復元できます。 次の手順に従って、データを復元します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. Azure Cosmos DB アカウントに移動して、 **[ポイントインタイム リストア]** ペインを開きます。

   > [!NOTE]
   > Azure portal の [復元] ペインは、`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` アクセス許可を持っている場合にのみ設定されます。 このアクセス許可を設定する方法の詳細については、[バックアップとアクセス許可の復元](continuous-backup-restore-permissions.md)に関する記事を参照してください。

1. 復元するには、次の詳細を入力します。

   * **復元ポイント (UTC)** – 過去 30 日以内のタイムスタンプ。 アカウントは、そのタイムスタンプの時点で存在している必要があります。 復元ポイントは UTC で指定できます。 復元する時刻の秒数にできるだけ近づけることができます。 [復元ポイントの特定](#event-feed)に関するヘルプについては、**こちらをクリック** リンクを選択してください。

   * **場所** – アカウントが復元される宛先リージョン。 アカウントは、指定されたタイムスタンプの時点でこのリージョンに存在している必要があります (例: 米国西部または米国東部)。 アカウントは、ソース アカウントが存在していたリージョンにのみ復元できます。

   * **Restore Resource (復元リソース)** – **アカウント全体** か、**選択したデータベースまたはコンテナー** を復元するかを選択できます。 データベースとコンテナーは、指定されたタイムスタンプの時点で存在している必要があります。 選択された復元ポイントと場所に基づいて、復元リソースが設定されます。これによりユーザーは、復元する必要がある特定のデータベースまたはコンテナーを選択できるようになります。

   * **リソース グループ** - ターゲット アカウントが作成および復元されるリソース グループ。 このリソース グループは、既に存在している必要があります。

   * **Restore Target Account (復元ターゲット アカウント)** - ターゲット アカウント名。 ターゲット アカウント名は、新しいアカウントを作成するときと同じガイドラインに従う必要があります。 このアカウントは、復元プロセスによって、ソース アカウントが存在するのと同じリージョンに作成されます。
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="Azure portal で誤った変更からライブ アカウントを復元します。" border="true":::

1. 上のパラメーターを選択したら、 **[送信]** ボタンを選択して復元を開始します。 復元コストは、指定したリージョン内のストレージに対するデータ量と料金に基づく 1 回限りの料金です。 詳細については、[価格](continuous-backup-restore-introduction.md#continuous-backup-pricing)に関するセクションを参照してください。

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>イベント フィードを使用して復元時刻を特定する

Azure portal で復元ポイントの時刻を入力する際に、復元ポイントを特定するのにヘルプが必要な場合は、**こちらをクリック** リンクを選択すると、イベント フィードのブレードが表示されます。 イベント フィードには、ソース アカウントのデータベースおよびコンテナーに対する作成、置換、削除イベントに関する完全に忠実な一覧が表示されます。 

たとえば、特定のコンテナーが削除または更新される前の時点に復元する必要がある場合は、このイベント フィードを確認してください。 イベントは、発生時刻順 (降順) に時系列で表示され、最近のイベントが上部に表示されます。 結果を参照して、イベントの前後の時間を選択することで、時刻をさらに絞り込むことができます。

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="イベント フィードを使用して復元ポイントの時刻を特定します。" border="true":::

> [!NOTE]
> イベント フィードには、項目リソースへの変更は表示されません。 復元のために、いつでも過去 30 日以内の任意のタイムスタンプ (その時点でアカウントが存在する限り) を手動で指定できます。

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>削除されたアカウントを復元する

Azure portal を使用すると、削除されたアカウントを削除後 30 日以内に完全に復元できます。 次の手順に従って、削除されたアカウントを復元します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. グローバル検索バーで「*Azure Cosmos DB*」リソースを検索します。 すべての既存のアカウントが一覧表示されます。
1. 次に、 **[復元]** ボタンを選択します。 [復元] ペインに、保持期間 (削除時刻から 30 日間) 以内に復元できる削除済みアカウントの一覧が表示されます。
1. 復元するアカウントを選択します。

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="削除されたアカウントを Azure portal から復元します。" border="true":::

   > [!NOTE]
   > 注:Azure portal の [復元] ペインは、`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` アクセス許可を持っている場合にのみ設定されます。 このアクセス許可を設定する方法の詳細については、[バックアップとアクセス許可の復元](continuous-backup-restore-permissions.md)に関する記事を参照してください。

1. 復元するアカウントを選択し、削除されたアカウントを復元するために次の詳細情報を入力します。

   * **復元ポイント (UTC)** – 過去 30 日以内のタイムスタンプ。 アカウントは、そのタイムスタンプの時点で存在している必要があります。 復元ポイントは UTC で指定します。 復元する時刻の秒数にできるだけ近づけることができます。

   * **場所** – アカウントを復元する必要がある宛先リージョン。 ソース アカウントは、指定されたタイムスタンプの時点でこのリージョンに存在している必要があります。 例: 米国西部または米国東部。  

   * **リソース グループ** - ターゲット アカウントが作成および復元されるリソース グループ。 このリソース グループは、既に存在している必要があります。

   * **Restore Target Account (復元ターゲット アカウント)** – ターゲット アカウント名は、新しいアカウントを作成するときと同じガイドラインに従う必要があります。 このアカウントは、復元プロセスによって、ソース アカウントが存在するのと同じリージョンに作成されます。

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>復元操作の状態を追跡する

復元操作を開始した後、ポータルの右上隅にある **[通知]** ベル アイコンを選択します。 これにより、復元しているアカウントの状態を示すリンクが表示されます。 復元の進行中は、アカウントの状態は "*Creating*" (作成中) になります。復元操作が完了すると、アカウントの状態は "*Online*" (オンライン) に変わります。

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="操作が完了すると、復元されたアカウントの状態は、作成中からオンラインに変わります。" border="true":::

## <a name="next-steps"></a>次のステップ

* [Azure CLI](continuous-backup-restore-command-line.md)、[PowerShell](continuous-backup-restore-powershell.md)、または [Azure Resource Manager](continuous-backup-restore-template.md) を使用して、継続的バックアップを構成および管理します。
* [継続的バックアップ モードのリソース モデル](continuous-backup-restore-resource-model.md)
* 継続的バックアップ モードでデータを復元するために必要な[アクセス許可を管理](continuous-backup-restore-permissions.md)します。
