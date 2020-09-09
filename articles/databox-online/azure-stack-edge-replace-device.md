---
title: Azure Stack Edge デバイスを交換する | Microsoft Docs
description: Azure Stack Edge デバイスを交換する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 5d5e8cc3a959c7800019107d2c682e144449b46c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87094873"
---
# <a name="replace-your-azure-stack-edge-device"></a>Azure Stack Edge デバイスを交換する

この記事では、Azure Stack Edge デバイスを交換する方法について説明します。 既存のデバイスにハードウェア障害があるかアップグレードが必要な場合、交換用デバイスが必要です。 


この記事では、次の方法について説明します。

> [!div class="checklist"]
>
> * ハードウェアの問題のサポート チケットを開く
> * Azure portal で交換用デバイスのための新しいリソースを作成する
> * 交換用デバイスをインストールしてアクティブにする
> * 元のデバイスを返却する

## <a name="open-a-support-ticket"></a>サポート チケットを開く

既存のデバイスでハードウェア障害が発生した場合は、サポート チケットを開きます。 Microsoft サポートによって、このインスタンスにはフィールド交換ユニット (FRU) が利用できない、またはデバイスのハードウェア アップグレードが必要であると判断されます。 どちらの場合も、サポートは交換用デバイスを発注します。

1. デバイスを返却したいことを示して、Microsoft サポートでサポート チケットを開きます。 問題の種類に **[Azure Stack Edge Hardware]\(Azure Stack Edge ハードウェア\)** を選択します。

    ![サポート チケットを開く](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Microsoft サポート エンジニアからご連絡いたします。 発送先の詳細を指定します。
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>交換用デバイスのための新しいリソースを作成する

リソースを作成するには、次の手順に従います。

1. 「[新しいリソースを作成する](azure-stack-edge-deploy-prep.md#create-a-new-resource)」の手順に従って、交換用デバイスのためのリソースを作成します。 

2. 必ず、 **[I have a Azure Stack Edge device]\(Azure Stack Edge を持っています\)** チェック ボックスをオンにしてください。 

    ![交換用デバイスのためのリソース](media/azure-stack-edge-replace-device/replace-resource-1.png)  

## <a name="install-and-activate-the-replacement-device"></a>交換用デバイスをインストールしてアクティブにする

交換用デバイスをインストールしてアクティブにするには、次の手順に従います。

1. [デバイスをインストールします](azure-stack-edge-deploy-install.md)。

2. 以前に作成した新しいリソースに対して[デバイスをアクティブにします](azure-stack-edge-deploy-connect-setup-activate.md)。

## <a name="return-your-existing-device"></a>既存のデバイスを返却する

元のデバイスを返却するためのすべての手順に従います。

1. [デバイス上のデータを消去します](azure-stack-edge-return-device.md#erase-data-from-the-device)。
2. 元のデバイスの[デバイス返却を開始します](azure-stack-edge-return-device.md#initiate-device-return)。
3. [集荷をスケジュールします](azure-stack-edge-return-device.md#schedule-a-pickup)。
4. 返却するデバイスに関連付けられている[リソースを削除します](azure-stack-edge-return-device.md#delete-the-resource)。


## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge デバイスを返却する](azure-stack-edge-return-device.md)方法について説明します。
