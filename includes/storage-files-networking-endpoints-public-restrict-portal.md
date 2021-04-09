---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3c76988557bfa338bcfb606f568036422a536c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98798639"
---
パブリック エンドポイントを特定の仮想ネットワークに制限するストレージ アカウントに移動します。 ストレージ アカウントの目次で、 **[ネットワーク]** を選択します。 

ページの上部で、 **[選択されたネットワーク]** ラジオ ボタンを選択します。 これにより、パブリック エンドポイントの制限を制御するためのいくつかの設定が表示されるようになります。 **[+ 既存の仮想ネットワークを追加]** をクリックして、パブリック エンドポイント経由でのストレージ アカウントへのアクセスを許可する特定の仮想ネットワークを選択します。 そのためには、仮想ネットワークと、その仮想ネットワークのサブネットを選択する必要があります。 

**[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** をオンにして、Azure File Sync などの信頼できるファーストパーティの Microsoft サービスに、ストレージ アカウントへのアクセスを許可します。

[![特定の仮想ネットワークがパブリック エンドポイント経由でストレージ アカウントへのアクセスを許可されている [ネットワーク] ブレードのスクリーンショット](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)