---
title: Azure StorSimple から Azure Media Services アカウントへのファイルのアップロード | Microsoft Docs
description: この記事では、Azure StorSimple Data Manager の概要を説明します。 また、StorSimple からデータを抽出し、そのデータを資産として Azure Media Services アカウントにアップロードする方法を紹介するチュートリアルへのリンクも掲載しています。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 1dd09328-262b-43ef-8099-73241b49a925
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 0521904f0ed46b4c5309e5f9df980b1cd7d7d858
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009030"
---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>Azure StorSimple から Azure Media Services アカウントへのファイルのアップロード 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。
>
> 
> Azure StorSimple Data Manager は、現在、プライベート プレビュー段階にあります。 
> 

## <a name="overview"></a>概要

Media Services で、デジタル ファイルを資産にアップロードします。 資産には、ビデオ、オーディオ、画像、サムネイル コレクション、テキスト トラック、字幕ファイル (各ファイルのメタデータを含む) を追加できます。ファイルをアップロードすると、クラウドにコンテンツが安全に保存され、処理したりストリーミングしたりできるようになります。

[Azure StorSimple](../../storsimple/index.yml) では、クラウド ストレージがオンプレミス ソリューションの拡張機能として使用され、オンプレミス ストレージとクラウド ストレージをまたがってデータが自動的に階層化されます。 StorSimple デバイスは、データをクラウドに送信する前にデータの重複排除と圧縮を行うため、大きいファイルを非常に効率的にクラウドに送信できるようになります。 [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md) サービスで提供される API を使用すると、StorSimple からデータを抽出し、そのデータを AMS 資産として表示することができます。

## <a name="get-started"></a>はじめに

1. 資産の移行先となる [Media Services アカウントを作成](media-services-portal-create-account.md)します。
2. [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md) に関する記事の説明に従って、Data Manager プレビューにサインアップします。
3. StorSimple Data Manager アカウントを作成します。
4. 実行時に StorSimple デバイスからデータを抽出し、資産として AMS アカウントに移行するデータ変換ジョブを作成します。 

    ジョブの実行が開始されると、ストレージ キューが作成されます。 変換された BLOB の準備が完了すると、キューにはその BLOB に関するメッセージが設定されます。 このキューの名前は、ジョブ定義の名前と同じです。 このキューを使用すると、資産の準備がいつ完了したかを判断し、そのキューで実行されるように必要な Media Services の操作を呼び出すことができます。 たとえば、このキューを使用して、必要な Media Services コードが含まれている Azure 関数をトリガーすることができます。

## <a name="see-also"></a>関連項目

[Data Manager 内で .NET SDK を使用してジョブをトリガーする](../../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>次のステップ

これで、アップロードした資産をエンコードできます。 詳細については、 [資産のエンコード](media-services-portal-encode.md)に関するページをご覧ください。
