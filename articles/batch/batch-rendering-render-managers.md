---
title: レンダー マネージャーのサポート
description: Azure Batch レンダー マネージャーの統合を使用します。 一般的なレンダー マネージャーのビルトイン サポートまたはアドオンについて説明します。
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: c44cbf86d8bf2fe83a6dc91dee1c4f58eec156c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "83726453"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>レンダー ファーム マネージャーで Azure Batch を使用する

既存のオンプレミスのレンダー ファームを使用している場合、レンダー マネージャーがレンダー ファームの容量とレンダー ジョブを制御することが大いに起こり得ます。

Azure では、一般的なレンダー マネージャーのビルトイン サポートまたはアドオンのいずれかを提供します。 これにより、従量課金制のアプリケーション ライセンスの VM や優先度の低い VM を含む、Azure の VM を追加および削除できます。

次のレンダー マネージャーがサポートされています。

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal Render](https://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure Render Hub

Azure Render Hub を使用すると、Azure レンダー ファームの作成と管理が簡単になります。  Render Hub では、PipelineFx Qube と Deadline 10 がネイティブにサポートされています。  詳細情報と詳細な手順については、[GitHub リポジトリ](https://github.com/Azure/azure-render-hub)を参照してください。

## <a name="using-azure-with-pipelinefx-qube"></a>Azure を PipelineFX Qube で使用する

Azure Render Hub は、Deadline を含む一般的なレンダー マネージャーをサポートしています。  Render Hub をデプロイして使用する手順については、[GitHub リポジトリ](https://github.com/Azure/azure-render-hub)を参照してください。

Azure Batch のプール VM を Qube のワーカーとして使用することを有効にするスクリプトや手順は、[GitHub のリポジトリ](https://github.com/Azure/azure-qube)でも入手できます。

## <a name="using-azure-with-royal-render"></a>Azure を Royal Render で使用する

Royal Render には Azure や Azure Batch の統合が組み込まれており、Azure ベースの VM を使用してレンダー ファームを拡張できます。 概要については、[ヘルプ ファイル](https://www.royalrender.de/help8/index.html?Cloudrendering.html)をご覧ください。

Azure の統合を使用する Royal Render のお客様の例については、[Jellyfish Pictures のカスタマー ストーリー](https://customers.microsoft.com/story/jellyfishpictures)をご覧ください。

## <a name="using-azure-with-thinkbox-deadline"></a>Thinkbox Deadline で Azure を使用する

Azure Render Hub は、Deadline を含む一般的なレンダー マネージャーをサポートしています。  Render Hub をデプロイして使用する手順については、[GitHub リポジトリ](https://github.com/Azure/azure-render-hub)を参照してください。

## <a name="next-steps"></a>次のステップ

必要に応じて GitHub の該当するプラグインや手順を使用して、お使いのレンダー マネージャーの Azure Batch の統合をお試しください。
