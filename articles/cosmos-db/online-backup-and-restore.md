---
title: Azure Cosmos DB でのオンライン バックアップとオンデマンドのデータ復元
description: この記事では、自動バックアップとオンデマンドのデータ復元のしくみについて説明します。 また、継続的および定期的な各バックアップ モードの違いについても説明します。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2629e9c6e048620d9490a1e091a16c138fd1e615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99525434"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Azure Cosmos DB でのオンライン バックアップとオンデマンドのデータ復元
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB では、データのバックアップが一定の間隔で自動的に取得されます。 自動バックアップは、データベース操作のパフォーマンスや可用性に影響を与えずに取得されます。 すべてのバックアップは、ストレージ サービスに個別に格納されます。 自動バックアップは、Azure Cosmos アカウント、データベース、またはコンテナーを誤って削除または更新した後でデータの回復が必要になったシナリオの場合に役に立ちます。 バックアップには、次の 2 つのモードがあります。

* **定期的バックアップ モード** - このモードは、すべての既存のアカウントの既定のバックアップ モードです。 このモードでは、バックアップは定期的な間隔で実行され、サポート チームに要求を作成することによってデータが復元されます。 このモードでは、アカウントのバックアップ間隔と保有期間を構成します。 最大保有期間は 1 か月まで延長されます。 最小バックアップ間隔は 1 時間です。  詳細については、[定期的バックアップ モード](configure-periodic-backup-restore.md)に関する記事をご覧ください。

* **継続的バックアップ モード** (現在はパブリック プレビュー) – このモードは、Azure Cosmos DB アカウントの作成時に選択します。 このモードを使用すると、過去 30 日以内の任意の時点に復元できます。 詳細については、[継続的バックアップ モードの概要](continuous-backup-restore-introduction.md)と、[Azure portal](continuous-backup-restore-portal.md)、[PowerShell](continuous-backup-restore-powershell.md)、[CLI](continuous-backup-restore-command-line.md)、および [Resource Manager](continuous-backup-restore-template.md) を使用した継続的バックアップの構成に関する記事をご覧ください。

  > [!NOTE]
  > 新しいアカウントを継続的バックアップで構成した場合は、Azure portal、PowerShell、または CLI を使用して、セルフサービス復元を行うことができます。 アカウントを継続的モードで構成した場合は、定期的モードに切り替えて戻すことはできません。 現在定期的バックアップモードである既存のアカウントを、継続的モードに変更することはできません。  

## <a name="next-steps"></a>次のステップ

次に、アカウントを定期的および継続的バックアップ モードで構成して管理する方法について学習できます。

* [定期的なバックアップ ポリシーを構成および管理する](configure-periodic-backup-restore.md)
* [継続的バックアップ](continuous-backup-restore-introduction.md) モードとは
* [Azure portal](continuous-backup-restore-portal.md)、[PowerShell](continuous-backup-restore-powershell.md)、[CLI](continuous-backup-restore-command-line.md)、または [Azure Resource Manager](continuous-backup-restore-template.md) を使用して、継続的バックアップを構成および管理します。
* 継続的バックアップ モードでデータを復元するために必要な[アクセス許可を管理](continuous-backup-restore-permissions.md)します。
