---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/21/2019
ms.author: alkohli
ms.openlocfilehash: 0c6845f081ccbe42e70964eaa939d58597e3b69b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185545"
---
1. デバイスのローカル Web UI に移動し、デバイスにサインインします。 デバイスのロックが解除されていることを確認します。

2. **[ネットワーク設定]** ページに移動します。 クライアントへの接続に使用するネットワーク インターフェイスのデバイスの IP アドレスをメモします。

3. リモート Windows クライアントを使用している場合は、管理者として **メモ帳** を起動し、`C:\Windows\System32\Drivers\etc` にあるホスト ファイルを開きます。

4. hosts ファイルに `<Device IP address> <Blob service endpoint>` というエントリを追加します。

    Azure portal で作成した Edge ストレージ アカウントから Blob service エンドポイントを取得しました。 Blob service エンドポイントのサフィックスのみを使用します。

    次の図を参考にしてください。 `hosts` ファイルを保存します。

    ![Windows クライアント上の hosts ファイルを変更する](media/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint/hosts-file-1.png)