---
title: Video Indexer で複数のテナントを管理する - Azure
description: この記事では、Video Indexer で複数のテナントを管理するためのさまざまな統合オプションについて説明します。
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: 18f2cf3daa281400151ba223e1735e7138d97e8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "76990506"
---
# <a name="manage-multiple-tenants"></a>複数のテナントの管理

この記事では、Video Indexer で複数のテナントを管理するためのさまざまなオプションについて説明します。 自分のシナリオに最も適した方法を選択してください。

* テナントごとの Video Indexer アカウント
* すべてのテナントに対して単一の Video Indexer アカウント
* テナントごとの Azure サブスクリプション

## <a name="video-indexer-account-per-tenant"></a>テナントごとの Video Indexer アカウント

このアーキテクチャを使用すると、テナントごとに Video Indexer アカウントが作成されます。 テナントは、永続的なコンピューティング レイヤーで完全に分離されます。  

![テナントごとの Video Indexer アカウント](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>考慮事項

* お客様は、(お客様が手動で構成しない限り) ストレージ アカウントを共有することはありません。
* お客様は、コンピューティング (予約ユニット) を共有せず、相互のジョブ処理時間に影響を与えることはありません。
* Video Indexer アカウントを削除することによって、システムからテナントを簡単に削除することができます。
* テナント間でカスタム モデルを共有する機能はありません。

    カスタム モデルを共有するビジネス要件がないことを確認してください。
* テナントごとに複数の Video Indexer (および関連する Media Services) アカウントがあるため、管理が困難になります。

> [!TIP]
> [Video Indexer 開発者ポータル](https://api-portal.videoindexer.ai/)でシステムの管理者ユーザーを作成し、Authorization API を使用して、関連する[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token)をテナントに提供します。

## <a name="single-video-indexer-account-for-all-users"></a>すべてのユーザーに対して単一の Video Indexer アカウント

このアーキテクチャを使用する場合は、お客様がテナントの分離を担当します。 すべてのテナントで、1 つの Video Indexer アカウントと 1 つの Azure Media Service アカウントを使用する必要があります。 コンテンツをアップロード、検索、削除するとき、お客様はそのテナントに対して適切な結果になるようにフィルター処理を行う必要があります。

![すべてのユーザーに対して単一の Video Indexer アカウント](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

このオプションでは、テナントによってモデルをフィルター処理することにより、テナント間でカスタマイズ モデル (ユーザー、言語、ブランド) を共有または分離することができます。

[ビデオをアップロードする](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)ときに、テナントごとに異なるパーティション属性を指定することができます。 これにより、[検索 API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?) での分離が可能になります。 検索 API でパーティション属性を指定することにより、指定したパーティションのみの結果を取得します。 

### <a name="considerations"></a>考慮事項

* テナント間でコンテンツとカスタマイズ モデルを共有する機能。
* あるテナントによって他のテナントのパフォーマンスに影響があります。
* お客様は、Video Indexer の上に複雑な管理レイヤーを構築する必要があります。

> [!TIP]
> [優先順位](upload-index-videos.md)属性を使用して、テナント ジョブの優先順位を設定できます。

## <a name="azure-subscription-per-tenant"></a>テナントごとの Azure サブスクリプション 

このアーキテクチャを使用するときは、テナントごとに専用の Azure サブスクリプションを使用します。 ユーザーごとに、テナント サブスクリプションに新しい Video Indexer アカウントを作成します。

![テナントごとの Azure サブスクリプション](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>考慮事項

* これは、課金を分離できる唯一のオプションです。
* この統合により、テナントごとの Video Indexer アカウントより管理オーバーヘッドが増加します。 課金の要件がない場合は、この記事で説明した他のオプションのいずれかを使用することをお勧めします。

## <a name="next-steps"></a>次のステップ

[概要](video-indexer-overview.md)
