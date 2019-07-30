---
title: Azure Data Share プレビューの監視方法
description: Azure Data Share プレビューの監視方法
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 869c1ed41d7f78df184461bc1d8cab6c6eb8d426
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788522"
---
# <a name="monitor-azure-data-share-preview"></a>Azure Data Share プレビューを監視する 

この記事では、Azure Data Share プレビューを使用してデータ共有を監視する方法について説明します。 データ プロバイダーは、データ共有リレーションシップのさまざまな側面を監視することができます。 データ コンシューマーがデータ共有の招待を承諾したかどうか、共有サブスクリプションを作成してデータの使用を開始したかどうかなど、すべての詳細を監視できます。 

データ コンシューマーは、Azure サブスクリプションにトリガーされるスナップショットを監視できます。 

## <a name="monitor-invitation-status"></a>招待の状態を監視する

データ共有の招待の状態を表示するには、[Sent shares] (送信した共有) -> [招待] に移動します。 

![招待の状態](./media/invitation-status.png "招待の状態") 

招待は、以下の 3 つの状態をとることができます。

* 保留中 - データ共有の受信者がまだ招待を承諾していません。
* 承認済み - データ共有の受信者が招待を承諾しました。
* 拒否 - データ共有の受信者が招待を拒否しました。

> [!IMPORTANT]
> 既に承諾された招待を削除しても、アクセス権を取り消すことにはなりません。 今後、スナップショットがデータ コンシューマーのストレージ アカウントにコピーされないようにしたい場合は、 *[Share subscriptions] (共有サブスクリプション)* タブからアクセスを取り消す必要があります。 

## <a name="monitor-share-subscriptions"></a>共有サブスクリプションを監視する

共有サブスクリプションの状態を表示するには、[Sent Shares] (送信した共有) -> [Share Subscriptions] (共有サブスクリプション) に移動します。 ここでは、招待を承諾した後にデータ コンシューマーによって作成されたアクティブなサブスクリプションに関する詳細情報が提供されます。 共有サブスクリプションを選択して *[Revoke] (取り消す)* を選択すると、今後のデータ コンシューマーへの更新を停止することができます。 

## <a name="snapshot-history"></a>スナップショットの履歴 

履歴タブから、データ コンシューマーのテナントにコピーされたスナップショットを表示することができます。 各スナップショットの間隔について、頻度と期間を監視できます。 

![スナップショットの履歴](./media/sent-shares.png "スナップショットの履歴") 

実行開始日をクリックすると、各スナップショットの実行に関する詳細情報を表示できます。 

## <a name="next-steps"></a>次の手順 

[Azure Data Share の用語](terminology.md)についての詳細情報