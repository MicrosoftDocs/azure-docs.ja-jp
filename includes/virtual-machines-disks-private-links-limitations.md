---
title: ファイルを含める
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 138e7efd33ee36a77fd56deda155a29017432302
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420289"
---
- ディスク アクセス オブジェクトにリンクできる仮想ネットワークは 1 つだけです。
- これらをリンクするには、仮想ネットワークがディスク アクセス オブジェクトと同じサブスクリプションにある必要があります。
- 同じディスク アクセス オブジェクトを使用して、最大 10 個のディスクまたはスナップショットを同時にインポートまたはエクスポートできます。
- 仮想ネットワークをディスク アクセス オブジェクトにリンクするために手動承認を要求することはできません。
- ディスク アクセス オブジェクトに関連付けられている増分スナップショットでは、差分機能はサポートされていません。
- AzCopy を使用して、ストレージ アカウントへのプライベート リンクを介してセキュリティで保護されているディスクおよびスナップショットの VHD をダウンロードすることはできません。 ただし、AzCopy を使用して VHD を VM にダウンロードすることはできます。
