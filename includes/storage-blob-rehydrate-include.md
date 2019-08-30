---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 03674a51566ab89791725d1a51c7af12ed6949e5
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2019
ms.locfileid: "68952887"
---
アーカイブ ストレージ内のデータを読み取るには、まず、BLOB の層をホットまたはクールに変更する必要があります。 このプロセスはリハイドレートと呼ばれ、完了までに数時間かかることがあります。 リハイドレートのパフォーマンスを最適化するために、サイズの大きい BLOB を使用することをお勧めします。 複数の小さな BLOB を同時にリハイドレートすると、余分に時間がかかる場合があります。 現在、High (プレビュー) と Standard という 2 つのリハイドレート優先度があります。これは、[BLOB 層の設定](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)操作または [BLOB のコピー](https://docs.microsoft.com/rest/api/storageservices/copy-blob)操作に対するオプションの *x-ms-rehydrate-priority* プロパティを使用して設定できます。

* **Standard の優先度**:このリハイドレート要求は、受信した順序で処理され、最大で 15 時間かかることがあります。
* **High の優先度 (プレビュー)** :このリハイドレート要求は、Standard の要求より優先され、1 時間未満で完了することがあります。 BLOB のサイズと現在の需要によっては、High の優先度の処理にかかる時間が 1 時間を超える場合があります。 High の優先度の要求は、Standard の優先度の要求より優先されることが保証されます。

> [!NOTE]
> Standard の優先度は、アーカイブでの既定のリハイドレート オプションです。 High の優先度は、Standard の優先度のリハイドレートよりもコストがかかる高速オプションのため、通常は緊急でデータを復元する状況において使用するために予約されています。

リハイドレート中に、層が変更されたかどうかを確認するために BLOB の*アーカイブ ステータス* プロパティをチェックすることができます。 ステータスは、変更先の層に応じて "rehydrate-pending-to-hot" または "rehydrate-pending-to-cool" になります。 完了すると、アーカイブ ステータス プロパティが削除され、BLOB の*アクセス層*プロパティに新しい階層としてホット層またはクール層が反映されます。
