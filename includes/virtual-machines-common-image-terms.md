---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 50d71a3967e61e5d531f4bfeae3582b56230a0d2
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2019
ms.locfileid: "56825433"
---
## <a name="terminology"></a>用語集

Azure Marketplace イメージには、次の属性があります。

* **[発行者]**: イメージを作成した組織。 次に例を示します。Canonical、MicrosoftWindowsServer
* **[プラン]**: 発行元によって作成された関連するイメージのグループ名です。 次に例を示します。UbuntuServer、WindowsServer
* **SKU**:ディストリビューションのメジャー リリースなど、プランのインスタンス。 次に例を示します。18.04-LTS、2019-Datacenter
* **バージョン**:イメージの SKU のバージョン番号。 

プログラムで VM をデプロイした場合に Marketplace イメージを識別するには、これらの値をパラメーターとして個別に指定します。 一部のツールはイメージ *URN* を受け入れます。これは、次のように、これらの値を組み合わせてコロン (:) で区切ったものです:*Publisher*:*Offer*:*Sku*:*Version*。 URN では、バージョン番号を "latest" で置き換えることもできます。この場合、イメージの最新バージョンが選択されます。 

イメージの発行元が追加のライセンスや購入契約を提供する場合は、この契約条件に同意し、プログラムによるデプロイを有効にする必要があります。 プログラムで VM をデプロイする場合は、*購入プラン* パラメーターを指定する必要もあります。 [Marketplace の契約条件でイメージを展開する](#deploy-an-image-with-marketplace-terms)を参照してください。
