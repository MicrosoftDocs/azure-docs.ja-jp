---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2019
ms.author: alkohli
ms.openlocfilehash: b7bb6045154b6579483329731dee64e1ca720bea
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89087551"
---
API を使用して Azure Stack Edge デバイスに仮想マシン (VM) を作成し、管理することができます。 これらの API は、ローカルの Azure Stack Edge エンドポイントを使用して呼び出される標準の Azure Resource Manager API です。 Azure Resource Manager API は、一貫した管理レイヤーを提供します。このケースでは、その管理レイヤーを通じて、デバイスに存在するローカル サブスクリプション内の VM を作成、更新、削除することができます。 Azure Stack Edge で実行されている Azure Resource Manager には、Azure PowerShell コマンドレットから接続することができます。