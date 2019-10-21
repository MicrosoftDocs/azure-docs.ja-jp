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
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168382"
---
カスタム IPsec ポリシーを操作する場合は、次の要件に注意してください。

* **IKE** - IKE の場合、IKE 暗号化の任意のパラメーターと、IKE 整合性の任意のパラメーター、および DH グループの任意のパラメーターを選択できます。
* **IPsec** -  IPsec の場合、IPsec 暗号化の任意のパラメーターと、IPsec 整合性の任意のパラメーター、および PFS を選択できます。 IPsec 暗号化または IPsec 整合性のパラメーターのいずれかが GCM の場合、両方の設定のパラメーターは GCM である必要があります。

>[!NOTE]
> カスタム IPsec ポリシーでは、(既定の IPsec ポリシーとは異なり) レスポンダーとイニシエーターの概念はありません。 両側 (オンプレミスと Azure VPN ゲートウェイ) で、IKE フェーズ 1 および IKE フェーズ 2 に同じ設定が使用されます。 IKEv1 と IKEv2 の両方のプロトコルがサポートされています。 レスポンダーのみとしての Azure はサポートされていません。
>

**使用可能な設定とパラメーター**

| Setting | parameters |
|--- |--- |
| IKE 暗号化 | AES256、AES192、AES128 |
| IKE 整合性 | SHA384、SHA256、SHA1 |
| DH グループ | DHGroup24、ECP384、ECP256、DHGroup14、DHGroup2048、DHGroup2 |
| IPsec 暗号化 | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128 |
| IPsec 整合性 | GCMASE256、GCMAES192、GCMAES128、SHA256、SHA1 |
| PFS グループ | PFS24、ECP384、ECP256、PFS2048、PFS2 |
