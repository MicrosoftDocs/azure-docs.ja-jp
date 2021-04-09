---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7700b1118c4f04643607f44a474338ffdd5c09e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86231027"
---
- Ultra Disk はサポートされません。
- VM または仮想マシン スケール セットで Azure Disk Encryption (bitlocker/VM-Decrypt を使用するゲスト VM の暗号化) が有効になっている場合、有効にすることはできません。
- ホストでの暗号化が有効になっているディスクで Azure Disk Encryption を有効にすることはできません。
- 既存の仮想マシン スケール セットで暗号化を有効にすることができます。 しかし、暗号化を有効にした後に作成された新しい VM のみが自動的に暗号化されます。
- 暗号化するには、既存の VM の割り当てを解除して再割り当てする必要があります。