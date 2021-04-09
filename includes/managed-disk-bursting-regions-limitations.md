---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 01/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 5658b68081fae8dab528030f7fc1cd6fe4935730
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178361"
---
- 512 GiB 未満の Premium SSD でオンデマンド バーストを有効にすることはできません。 512 GiB 未満の Premium SSD では、常にクレジットベースのバーストが使用されます。
- オンデマンド バーストは、Premium SSD でのみサポートされています。 オンデマンド バーストが有効になっている Premium SSD を別のディスクの種類に切り替えた場合、ディスク バーストは無効になります。
- オンデマンド バーストは、パフォーマンス レベルが変更されたときに自動的には無効になりません。 パフォーマンス レベルを変更するが、ディスク バーストを維持しない場合は、無効にする必要があります。
- オンデマンド バーストは、ディスクが VM から切断されている場合、または VM が停止されている場合にのみ有効にできます。 オンデマンド バーストは、有効になってから 12 時間後に無効にすることができます。