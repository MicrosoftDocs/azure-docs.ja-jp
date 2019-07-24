---
title: インクルード ファイル
description: インクルード ファイル
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b6587a3928c2ddf0d00c90e07643525314690e42
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318296"
---
### <a name="virtual-machines"></a>Virtual Machines

|  |  |
|---------|---------|
| [リソース グループのカスタム VM イメージの許可](../articles/governance/policy/samples/allow-custom-vm-image.md) |  カスタム イメージが承認されたリソース グループに含まれている必要があります。 承認されたリソース グループの名前を指定します。 |
| [ストレージ アカウントおよび仮想マシンに対して許可された SKU](../articles/governance/policy/samples/allowed-skus-storage.md) | 承認された SKU がストレージ アカウントと仮想マシンで使用される必要があります。 組み込みのポリシーを使用して、承認された SKU を確認します。 承認された仮想マシンの SKU の配列と、承認されたストレージ アカウントの SKU の配列を指定します。 |
| [承認された VM イメージ](../articles/governance/policy/samples/allowed-custom-images.md) | 承認されたカスタム イメージのみを環境にデプロイする必要があります。 承認されたイメージ ID の配列を指定します。 |
| [拡張機能が存在しない場合の監査](../articles/governance/policy/samples/audit-extension-not-exist.md) | 仮想マシンに拡張機能がデプロイされていない場合に監査が行われます。 デプロイされたかどうかを確認する拡張機能の公開元と種類を指定します。 |
| [許可されない VM 拡張機能](../articles/governance/policy/samples/not-allowed-vm-extension.md) | 指定された拡張機能の使用を禁止します。 禁止されている拡張機能の種類を格納している配列を指定します。 |