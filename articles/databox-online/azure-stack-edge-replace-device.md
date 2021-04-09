---
title: Azure Stack Edge Pro デバイスを交換する | Microsoft Docs
description: Azure Stack Edge Pro デバイスを交換する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2196c9463569dc43092b38de58e0103104efed0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443473"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro デバイスを交換する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro デバイスを交換する方法について説明します。 既存のデバイスにハードウェア障害があるかアップグレードが必要な場合、交換用デバイスが必要です。 


この記事では、次の方法について説明します。

> [!div class="checklist"]
>
> * ハードウェアの問題のサポート チケットを開く
> * Azure portal で交換用デバイスの新しい注文を作成する
> * 交換用デバイスをインストールしてアクティブにする
> * 元のデバイスを返却する

## <a name="open-a-support-ticket"></a>サポート チケットを開く

既存のデバイスでハードウェア障害が発生した場合は、次の手順に従ってサポート チケットを開きます。

1. デバイスを返却したいことを示して、Microsoft サポートでサポート チケットを開きます。 **Azure Stack Edge Pro のハードウェア** の問題の種類を選択し、 **[ハードウェアの問題]** サブタイプを選択します。  

    ![サポート チケットを開く](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Microsoft サポート エンジニアは、現場交換ユニット (FRU) が問題を解決できるかどうかを判断し、このインスタンスで使用できるかどうかを確認します。 FRU が使用できない場合、またはデバイスにハードウェアのアップグレードが必要な場合は、新しい注文を作成し、古いデバイスを返却するための指示がサポートによって出されます。

## <a name="create-a-new-order"></a>新しい注文を作成する

[[新しいリソースを作成]](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource) の手順に従って、交換用デバイスのライセンス認証用の新しいリソースを作成します。

> [!NOTE]
> 既存のリソースに対する交換用デバイスのアクティブ化はサポートされていません。 新しいリソースは、新しい注文と見なされます。 デバイスが出荷されてから 14 日後に請求が開始されます。

## <a name="install-and-activate-the-replacement-device"></a>交換用デバイスをインストールしてアクティブにする

交換用デバイスをインストールしてアクティブにするには、次の手順に従います。

1. [デバイスをインストールします](azure-stack-edge-deploy-install.md)。
2. 以前に作成した新しいリソースに対して[デバイスをアクティブにします](azure-stack-edge-deploy-connect-setup-activate.md)。

## <a name="return-your-existing-device"></a>既存のデバイスを返却する

元のデバイスを返却するためのすべての手順に従います。

1. [デバイス上のデータを消去します](azure-stack-edge-return-device.md#erase-data-from-the-device)。
2. 元のデバイスの[デバイス返却を開始します](azure-stack-edge-return-device.md#initiate-device-return)。
3. [集荷をスケジュールします](azure-stack-edge-return-device.md#schedule-a-pickup)。
4. デバイスを Microsoft が受け取ったら、[[リソースを削除する]](azure-stack-edge-return-device.md#delete-the-resource) で返却したデバイスに関連付けられているリリースを削除できます。


## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro デバイスを返却する](azure-stack-edge-return-device.md)方法を確認します。
