---
title: Microsoft Azure Data Box Edge デバイスのアクセス、電源、接続モード | Microsoft Docs
description: Azure へのデータ転送に役立つ Azure Data Box Edge デバイスのアクセス、電源、および接続モードを管理する方法について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 813563b500b9365289285a89536f2724fb87acad
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417804"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Azure Data Box Edge のアクセス、電源、接続モードを管理する

この記事では、Azure Data Box Edge のアクセス、電源、および接続モードを管理する方法について説明します。 これらの操作は、ローカル Web UI または Azure portal を使用して実行されます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * デバイスのアクセスを管理する
> * 接続モードを管理する
> * 電源を管理する


## <a name="manage-device-access"></a>デバイスのアクセスを管理する

Data Box Edge デバイスへのアクセスは、デバイスのパスワードを使用して制御されます。 パスワードは、ローカル Web UI を使用して変更できます。 また、Azure portal ではデバイスのパスワードをリセットすることもできます。

### <a name="change-device-password"></a>デバイスのパスワードを変更する

デバイスのパスワードを変更するには、ローカル UI で次の手順のようにします。

1. ローカル Web UI で、**[メンテナンス] > [Password change]\(パスワード変更\)** に移動します。
2. 現在のパスワードを入力し、新しいパスワードを入力ます。 指定するパスワードは 8 ～ 16 文字にする必要があります。 パスワードには、大文字、小文字、数字、および特殊文字のうち 3 種類の文字を使用する必要があります。 新しいパスワードを確認入力します。

    ![パスワードの変更](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. **[パスワードの変更]** を選択します。
 
### <a name="reset-device-password"></a>デバイスのパスワードをリセットする

リセット ワークフローでは、ユーザーは古いパスワードを思い出す必要がなく、パスワードを忘れた場合に便利です。 このワークフローは、Azure portal で実行します。

1. Azure portal で、**[概要] > [管理パスワードのリセット]** に移動します。

    ![[パスワードのリセット]](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. 新しいパスワードを入力し、それを確認します。 指定するパスワードは 8 ～ 16 文字にする必要があります。 パスワードには、大文字、小文字、数字、および特殊文字のうち 3 種類の文字を使用する必要があります。 **[リセット]** を選択します。

    ![[パスワードのリセット]](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>接続モードを管理する

デバイスは、既定の完全接続モードだけでなく、部分接続モードまたは完全切断モードで実行することもできます。 ここでは、これらの各モードについて説明します。

- **完全接続** - これは、デバイスが動作する通常の既定モードです。 このモードでは、データのクラウドのアップロードとダウンロードの両方が有効です。 Azure portal またはローカル Web UI を使用して、デバイスを管理できます。

- **部分切断** – このモードでは、デバイスは共有データをアップロードもダウンロードもできませんが、Azure portal からデバイスを管理することはできます。

    このモードは通常、従量制課金のサテライト ネットワークで使用され、目的はネットワーク帯域幅の消費量を最小限に抑えることです。 デバイス監視操作のため、最小限のネットワーク消費が発生する可能性があります。

- **切断** – このモードでは、デバイスはクラウドから完全に切断されており、クラウドのアップロードとダウンロードの両方が無効になっています。 デバイスは、ローカル Web UI からのみ管理できます。

    このモードは通常、デバイスをオフラインにするときに使用されます。

デバイスのモードを変更するには、次の手順のようにします。

1. デバイスのローカル Web UI で、**[構成] > [クラウドの設定]** に移動します。
2. デバイスを操作するモードをドロップダウン リストから選びます。 **[Fully connected]\(完全接続\)**、**[Partially connected]\(部分接続\)**、**[Fully disconnected]\(完全切断\)** から選択することができます。 デバイスを部分切断モードで実行するには、**[Azure portal management]\(Azure portal 管理\)** を有効にします。

    ![接続モード](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>電源を管理する

ローカル Web UI を使用して、物理デバイスをシャットダウンまたは再起動できます。 再起動する前に、データ サーバーの共有をオフラインにしてから、デバイスをオフラインにすることをお勧めします。 この操作により、データ破損の可能性を最小限に抑えられます。

1. ローカル Web UI で、**[メンテナンス] > [Power settings]\(電源設定\)** に移動します。
2. 何を行うかに応じて、**[シャットダウン]** または **[再起動]** を選択します。

    ![電源設定](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 確認を求められたら、**[はい]** を選択して続行します。

> [!NOTE]
> 物理デバイスをシャット ダウンした場合、デバイスの電源を入れるには、電源ボタンを押す必要があります。

## <a name="next-steps"></a>次の手順

- [共有を管理する](data-box-edge-manage-shares.md)方法を確認します。