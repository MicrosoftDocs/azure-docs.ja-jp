---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 75acbb30c2bf811b7ae72d6939b9f164554fdd32
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98859856"
---
- 2,048 ビット、3,072 ビットおよび 4,096 ビットのサイズの[ソフトウェアと HSM の RSA キー](../articles/key-vault/keys/about-keys.md)のみがサポートされており、その他のキーまたはサイズはサポートされていません。
    - [HSM](../articles/key-vault/keys/hsm-protected-keys.md) キーには、**premium** レベルの Azure Key Vault が必要です。
- サーバー側の暗号化とカスタマー マネージド キーを使用して暗号化されたカスタム イメージから作成されたディスクは、同じカスタマー マネージド キーを使用して暗号化する必要があり、同じサブスクリプション内に存在する必要があります。
- サーバー側の暗号化とカスタマー マネージド キーで暗号化されたディスクから作成されたスナップショットは、同じカスタマー マネージド キーを使用して暗号化する必要があります。
- お使いのカスタマー マネージド キー (Azure Key Vault、ディスク暗号化セット、VM、ディスク、およびスナップショット) に関連するすべてのリソースは、同じサブスクリプションとリージョンに存在する必要があります。
- カスタマー マネージド キーで暗号化されたディスク、スナップショット、イメージは、別のリソース グループおよびサブスクリプションへは移動できません。
- 現在または以前に Azure Disk Encryption を使用して暗号化されたマネージド ディスクは、カスタマー マネージド キーを使用して暗号化することはできません。
- サブスクリプションごとに、リージョンに最大で 1000 のディスク暗号化セットしか作成できません。
- カスタマー マネージド キーを共有イメージ ギャラリーで使用する方法の詳細については、「[プレビュー:イメージの暗号化にカスタマー マネージド キーを使用する](../articles/virtual-machines/image-version-encryption.md)」を参照してください。
