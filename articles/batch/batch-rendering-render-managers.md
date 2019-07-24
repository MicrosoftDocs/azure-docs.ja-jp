---
title: レンダー マネージャーのサポート - Azure Batch
description: Azure Batch のレンダー マネージャーの統合を使用してレンダリングに Azure を使用する
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: bcc66a73e3d7986b177b13eb309ad664a006b960
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790062"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>レンダー ファーム マネージャーで Azure Batch を使用する

既存のオンプレミスのレンダー ファームを使用している場合、レンダー マネージャーがレンダー ファームの容量とレンダー ジョブを制御することが大いに起こり得ます。

Azure では、一般的なレンダー マネージャーのビルトイン サポートまたはアドオンのいずれかを提供します。 これにより、従量課金制のアプリケーション ライセンスの VM や優先度の低い VM を含む、Azure の VM を追加および削除できます。

次のレンダー マネージャーがサポートされています。

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](http://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="using-azure-with-pipelinefx-qube"></a>Azure を PipelineFX Qube で使用する

Azure Batch のプール VM を Qube のワーカーとして使用することを有効にするスクリプトや手順は、[GitHub のリポジトリ](https://github.com/Azure/azure-qube)に用意されています。

## <a name="using-azure-with-royal-render"></a>Azure を Royal Render で使用する

Royal Render には Azure や Azure Batch の統合が組み込まれており、Azure ベースの VM を使用してレンダー ファームを拡張できます。 概要については、[ヘルプ ファイル](http://www.royalrender.de/help8/index.html?Cloudrendering.html)をご覧ください。

Azure の統合を使用する Royal Render のお客様の例については、[Jellyfish Pictures のカスタマー ストーリー](https://customers.microsoft.com/story/jellyfishpictures)をご覧ください。

## <a name="using-azure-with-thinkbox-deadline"></a>Thinkbox Deadline で Azure を使用する

Azure Batch のプール VM を Deadline のスレーブとして使用することを有効にするスクリプトや手順は、[GitHub のリポジトリ](https://github.com/Azure/azure-deadline)に用意されています。

## <a name="next-steps"></a>次の手順

必要に応じて GitHub の該当するプラグインや手順を使用して、お使いのレンダー マネージャーの Azure Batch の統合をお試しください。