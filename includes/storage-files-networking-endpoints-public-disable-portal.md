---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 295b9fc842d926d0a8b264ed5200b60ac7bd2261
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465147"
---
パブリック エンドポイントへのすべてのアクセスを制限するストレージ アカウントに移動します。 ストレージ アカウントの目次で、 **[ファイアウォールと仮想ネットワーク]** を選択します。

ページの上部で、 **[選択されたネットワーク]** ラジオ ボタンを選択します。 これにより、パブリック エンドポイントの制限を制御するためのいくつかの設定が表示されるようになります。 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** をオンにして、Azure File Sync などの信頼できるファーストパーティの Microsoft サービスに、ストレージ アカウントへのアクセスを許可します。

[![適切な制限が設定されている [ファイアウォールと仮想ネットワーク] ブレードのスクリーンショット](media/storage-files-networking-endpoints-public-disable-portal/disable-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-disable-portal/disable-public-endpoint-0.png#lightbox)