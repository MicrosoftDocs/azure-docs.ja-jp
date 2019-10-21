---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 02ced43f8c3fc7c83359b78362e8ad0feeab3070
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168414"
---
>[!NOTE]
>既定のポリシーを使用する場合、Azure は IPsec トンネルの設定中に、イニシエーターとレスポンダーの両方として動作できます。 レスポンダーのみとしての Azure の使用はサポートされていません。
>

### <a name="initiator"></a>イニシエーター

次のセクションでは、Azure がトンネルのイニシエーターになっているときに、サポートされているポリシーの組み合わせを一覧表示します。

**フェーズ 1**

* AES_256、SHA1、DH_GROUP_2
* AES_256、SHA_256、DH_GROUP_2
* AES_128、SHA1、DH_GROUP_2
* AES_128、SHA_256、DH_GROUP_2

**フェーズ 2**

* GCM_AES_256、GCM_AES_256、PFS_NONE
* AES_256、SHA_1、PFS_NONE
* AES_256、SHA_256、PFS_NONE
* AES_128、SHA_1、PFS_NONE

### <a name="responder"></a>応答側

次のセクションでは、Azure がトンネルの応答側になっているときに、サポートされているポリシーの組み合わせを一覧表示します。

**フェーズ 1**

* AES_256、SHA1、DH_GROUP_2
* AES_256、SHA_256、DH_GROUP_2
* AES_128、SHA1、DH_GROUP_2
* AES_128、SHA_256、DH_GROUP_2

**フェーズ 2**

* GCM_AES_256、GCM_AES_256、PFS_NONE
* AES_256、SHA_1、PFS_NONE
* AES_256、SHA_256、PFS_NONE
* AES_128、SHA_1、PFS_NONE
* AES_256、SHA_1、PFS_1
* AES_256、SHA_1、PFS_2
* AES_256、SHA_1、PFS_14
* AES_128、SHA_1、PFS_1
* AES_128、SHA_1、PFS_2
* AES_128、SHA_1、PFS_14
* AES_256、SHA_256、PFS_1
* AES_256、SHA_256、PFS_2
* AES_256、SHA_256、PFS_14
* AES_256、SHA_1、PFS_24
* AES_256、SHA_256、PFS_24
* AES_128、SHA_256、PFS_NONE
* AES_128、SHA_256、PFS_1
* AES_128、SHA_256、PFS_2
* AES_128、SHA_256、PFS_14