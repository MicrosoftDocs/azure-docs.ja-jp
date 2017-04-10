---
title: "Azure CLI を使用して Azure サービスの逆引き DNS レコードを管理する | Microsoft Docs"
description: "Resource Manager で Azure CLI を使用して Azure サービスの逆引き DNS レコードまたは PTR レコードを管理する方法"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c655707e-1156-4893-b163-0b228ffd25d2
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 1ac7bc232082100cd0f0fe3e337930a6c5e9ede9
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli"></a>Azure CLI を使用して Azure サービスの逆引き DNS レコードを管理する方法

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [dns-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン

次のいずれかの CLI バージョンを使用してタスクを完了できます。

* [Azure CLI 1.0](dns-reverse-dns-record-operations-cli-nodejs.md) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI。
* [Azure CLI 2.0](dns-reverse-dns-record-operations-cli.md) - Resource Manager デプロイ モデル用の次世代 CLI。

## <a name="introduction"></a>はじめに

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

クラシック デプロイメント モデルの詳細については、「[Azure PowerShell を使用して Azure サービス (クラシック) の逆引き DNS レコードを管理する方法](dns-reverse-dns-record-operations-classic-ps.md)」を参照してください。


## <a name="validation-of-reverse-dns-records"></a>逆引き DNS レコードの検証
Azure では、サード パーティがお客様の DNS ドメインにマッピングされた逆引き DNS レコードを作成できないようにするために、以下のいずれかに該当する場合のみ逆引き DNS レコードを作成できるようにしています。

* "ReverseFqdn" が、逆引き DNS で指定されたパブリック IP アドレス リソースの "Fqdn"、または同じサブスクリプションに含まれるいずれかのパブリック IP アドレスの "Fqdn" と同じである (例: "ReverseFqdn" が "contosoapp1.northus.cloudapp.azure.com." である)。
* "ReverseFqdn" が、逆引き DNS で指定されたパブリック IP アドレスの名前または IP か、または同じサブスクリプションに含まれるいずれかのパブリック IP アドレスの "Fqdn" または IP に対して前方解決される (例: "ReverseFqdn" が、"contosoapp1.northus.cloudapp.azure.com" の CName エイリアスの "app1.contoso.com" である )。

検証チェックが実行されるのは、パブリック IP アドレスの逆引き DNS プロパティの設定時または変更時のみです。 定期的な再検証は行われません。

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>既存の Public IP Addresses への逆引き DNS の追加
逆引き DNS を既存のパブリック IP アドレスに追加するには、azure network public-ip set を使用します。

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

まだ DNS 名を持っていない既存のパブリック IP アドレスに逆引き DNS を追加する場合は、DNS 名も指定する必要があります。 DNS 名を指定するには、azure network public-ip set を使用します。

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

## <a name="create-a-public-ip-address-with-reverse-dns"></a>逆引き DNS でのパブリック IP アドレスの作成
逆引き DNS プロパティを指定して新しいパブリック IP アドレスを追加するには、azure network public-ip create を使用します。

```azurecli
az network public-ip create --name PublicIp --resource-group NRP-DemoRG-PS --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>既存の Public IP Addresses の逆引き DNS の表示
既存のパブリック IP アドレスで構成されている値を確認するには、azure network public-ip show を使用します。

```azurecli
 az network public-ip show --name PublicIp --resource-group NRP-DemoRG-PS
```

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>既存のパブリック IP アドレスの逆引き DNS を削除する
既存のパブリック IP アドレスの逆引き DNS プロパティを削除するには、azure network public-ip set を使用します。 この操作を行うには、ReverseFqdn プロパティの値を空白にします。

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn ""
```

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]


