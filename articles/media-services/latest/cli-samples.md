---
title: Azure CLI の例 - Azure Media Services | Microsoft Docs
description: Azure Media Services 用の Azure CLI の例
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 03/11/2019
ms.author: juliako
ms.openlocfilehash: dee7f831562dc1f4b2478d13b204aab1d8455e1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57840632"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Azure Media Services 用の Azure CLI の例

次の表に、Azure Media Services 用の Azure CLI の例へのリンクが含まれています。

## <a name="examples"></a>例

|  |  |
|---|---|
|**スケール**||
| [メディア占有ユニットをスケーリングする](media-reserved-units-cli-how-to.md)|Media Services v3 または Video Indexer によってトリガーされる音声分析と動画分析ジョブでは、お使いのアカウントを 10 個の S3 の MRU でプロビジョニングすることを強くお勧めします。 <br/>このスクリプトは、CLI を使用してメディア占有ユニット (MRU) をスケーリングする方法を示します。|
|**アカウント**||
| [Media Services アカウントを作成する](create-account-cli-how-to.md) | このスクリプトは、Azure Media Services アカウントを作成します。 |
| [アカウントの資格情報をリセットする](./scripts/cli-reset-account-credentials.md)|アカウントの資格情報をリセットし、app.config settings 設定を元に戻します。|
|**資産**||
| [資産を作成する](./scripts/cli-create-asset.md)|コンテンツをアップロードする Media Services Asset を作成します。|
| [ファイルをアップロードする](./scripts/cli-upload-file-asset.md)|ローカル ファイルをストレージ コンテナーにアップロードします。|
| **トランスフォーム**と**ジョブ**||
| [トランスフォームを作成する](./scripts/cli-create-transform.md)|トランスフォームの作成方法を示します。 トランスフォームは、ビデオまたはオーディオ ファイルを処理するためのタスクの単純なワークフローを記述します (多くの場合、"レシピ" と呼ばれます)。<br/> 必要な名前が付いていて、"レシピ" が含まれているトランスフォームが既に存在するかどうかを必ず確認する必要があります。 該当する場合は、それを再利用します。 |
| [カスタム変換を使用してエンコードする](custom-preset-cli-howto.md) | 特定のシナリオやデバイス要件をターゲットとするカスタム プリセットを作成する方法を説明します。|
| [ジョブを作成する](./scripts/cli-create-jobs.md)|HTTPS URL を使用する単純なエンコードされたトランスフォームにジョブを送信します。|
| [EventGrid を作成する](./scripts/cli-create-event-grid.md)|ジョブ状態変更用のアカウント レベルの Event Grid サブスクリプションを作成します。|
| **配信する**||
| [資産を公開する](./scripts/cli-publish-asset.md)| ストリーミング ロケーターを作成し、ストリーミング URL を戻します。 |
| [Filter](filters-dynamic-manifest-cli-howto.md)| ビデオ オンデマンド資産のためのフィルターを構成し、CLI を使用して[アカウント フィルター](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)と[資産フィルター](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)を作成する方法を示します。 

## <a name="see-also"></a>関連項目

- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
- [クイック スタート:ビデオ ファイルのストリーム配信 - CLI](stream-files-cli-quickstart.md)
