---
title: Azure Stack Edge Pro デバイスをリセットして再アクティブ化する | Microsoft Docs
description: ご利用の Azure Stack Edge Pro デバイスのデータをワイプし、再アクティブ化する方法について説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/03/2020
ms.author: alkohli
ms.openlocfilehash: 95fbe5dc4a934fee10c558f640bc24f261203a33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443529"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro デバイスをリセットして再アクティブ化する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、デバイスで問題が発生した場合や、何らかの理由で最初からやり直す必要がある場合に、Azure Stack Edge Pro デバイスをリセット、再構成、および再アクティブ化する方法について説明します。

デバイスをリセットしてデータを削除した後、デバイスを新しいリソースとして再アクティブ化する必要があります。 デバイスをリセットするとデバイスの構成が削除されるため、ローカル Web UI を使用してデバイスを再構成する必要があります。

この記事では、次のことについて説明します。

> [!div class="checklist"]
>
> * デバイス上のデータ ディスクのデータを削除する
> * 新しい注文を作成し、デバイスを再構成してアクティブ化することで、デバイスを再アクティブ化する

## <a name="reset-data-from-the-device"></a>デバイスのデータをリセットする

デバイス上のデータ ディスクのデータを削除するには、ご利用のデバイスをリセットする必要があります。

リセットする前に、必要に応じて、デバイス上のローカル コピーを作成します。 デバイスから Azure Storage コンテナーにデータをコピーできます。

>[!IMPORTANT]
> デバイスをリセットすると、すべてのローカル データとワークロードがデバイスから消去されます。これは、元に戻すことができません。 デバイスを最初からやり直す場合にのみデバイスをリセットしてください。

ローカル Web UI または PowerShell でデバイスをリセットできます。 PowerShell の手順については、「[デバイスをリセットする](./azure-stack-edge-connect-powershell-interface.md#reset-your-device)」を参照してください。

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

## <a name="reactivate-device"></a>デバイスを再アクティブ化する

デバイスをリセットした後、デバイスを新しいリソースとして再アクティブ化する必要があります。 新しい注文を作成した後、新しいリソースを再構成して再アクティブ化する必要があります。

既存のデバイスを再アクティブ化するには、次の手順を実行します。

1. 「[新しいリソースを作成する](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)」の手順に従って、既存のデバイスについて新しい注文を作成します。 **[配送先住所]** タブで、 **[I already have a device]\(既にデバイスを持っています\)** を選択します。

   ![[配送先住所] に新しいデバイスを指定しない](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [アクティブ化キーを取得します](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#get-the-activation-key)。

1. [デバイスに接続します](azure-stack-edge-gpu-deploy-connect.md)。

1. [デバイスのネットワークを構成します](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。

1. [デバイス設定を構成します](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)。

1. [証明書を構成します](azure-stack-edge-gpu-deploy-configure-certificates.md)。

1. [デバイスをアクティブ化します](azure-stack-edge-gpu-deploy-activate.md)。

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro デバイスに接続する](azure-stack-edge-gpu-deploy-connect.md)方法を確認します。
