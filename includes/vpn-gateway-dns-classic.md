---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c5e3a095b312d36cb05a78e84a13bc2a73d95d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91875565"
---
この構成では DNS 設定は必須ではありませんが、VM 間で名前解決を行う場合は DNS が必要です。 値を指定しても新しい DNS サーバーは作成されません。 指定する DNS サーバーの IP アドレスは、接続先のリソースの名前を解決できる DNS サーバーの IP アドレスである必要があります。

仮想ネットワークを作成した後は、名前解決を処理するために、DNS サーバーの IP アドレスを追加できます。 仮想ネットワークの設定を開き、DNS サーバーを選択し、名前解決に使用する DNS サーバーの IP アドレスを追加します。

1. ポータルで仮想ネットワークを見つけます。
2. 仮想ネットワークのページで、 **[設定]** セクションの **[DNS サーバー]** を選択します。
3. DNS サーバーを追加します。
4. 設定を保存するには、ページの上部にある **[保存]** をクリックします。