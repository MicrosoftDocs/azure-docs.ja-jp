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
ms.openlocfilehash: 6c594dbab51c46c382c21b4d87595cd7322f6036
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465125"
---
パブリック エンドポイントを特定の仮想ネットワークに制限するストレージ アカウントに移動します。 ストレージ アカウントの目次で、 **[ファイアウォールと仮想ネットワーク]** を選択します。 

ページの上部で、 **[選択されたネットワーク]** ラジオ ボタンを選択します。 これにより、パブリック エンドポイントの制限を制御するためのいくつかの設定が表示されるようになります。 **[+ 既存の仮想ネットワークを追加]** をクリックして、パブリック エンドポイント経由でのストレージ アカウントへのアクセスを許可する特定の仮想ネットワークを選択します。 そのためには、仮想ネットワークと、その仮想ネットワークのサブネットを選択する必要があります。 

**[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** をオンにして、Azure File Sync などの信頼できるファーストパーティの Microsoft サービスに、ストレージ アカウントへのアクセスを許可します。

[![特定の仮想ネットワークがパブリック エンドポイント経由でストレージ アカウントへのアクセスを許可されている [ファイアウォールと仮想ネットワーク] ブレードのスクリーンショット](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)