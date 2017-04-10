---
title: "PowerShell を使用する従来の Azure サービスの逆引き DNS レコード | Microsoft Docs"
description: "Azure DNS では、クラシック デプロイ モデルで PowerShell を使用して、Azure サービスの逆引き DNS レコードまたは PTR レコードを管理できます。"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9c24d176-6bce-4277-a14c-80fe44a20a87
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: dd020bf625510eb90af2e1ad19c155831abd7e75
ms.openlocfilehash: 399cfcad5c17cdb12a4063e11e1ff353e88e56a7
ms.lasthandoff: 02/10/2017


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>Azure PowerShell を使用して Azure サービス (クラシック) の逆引き DNS レコードを管理する方法

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md)の&2; 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 [Resource Manager モデルを使用してこれらの手順を実行する](dns-reverse-dns-record-operations-ps.md)方法について説明します。

## <a name="validation-of-reverse-dns-records"></a>逆引き DNS レコードの検証
Azure では、サード パーティがお客様の DNS ドメインにマッピングされた逆引き DNS レコードを作成できないようにするために、以下のいずれかに該当する場合のみ逆引き DNS レコードを作成できるようにしています。

* 逆引き DNS の FQDN が、その逆引き DNS で指定されたクラウド サービスの名前か、または同じサブスクリプションに含まれるいずれかのクラウド サービスの名前である (例: 逆引き DNS が "contosoapp1.cloudapp.net" である)。
* 逆引き DNS の FQDN が、その逆引き DNS で指定されたクラウド サービスの名前または IP か、同じサブスクリプションに含まれるいずれかのクラウド サービスの名前または IP に対して前方解決される (例: 逆引き DNS が、contosoapp1.cloudapp.net の CName エイリアスの "app1.contoso.com" である )。

検証チェックが実行されるのは、クラウド サービスの逆引き DNS プロパティの設定時または変更時のみです。 定期的な再検証は行われません。

## <a name="add-reverse-dns-to-existing-cloud-services"></a>既存の Cloud Services への逆引き DNS の追加
既存のクラウド サービスに逆引き DNS レコードを追加するには、"Set-AzureService" コマンドレットを使います。

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

## <a name="create-a-cloud-service-with-reverse-dns"></a>逆引き DNS によるクラウド サービスの作成
逆引き DNS プロパティを指定して新しいクラウド サービスを追加するには、"Set-AzureService" コマンドレットを使います。

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

## <a name="view-reverse-dns-for-existing-cloud-services"></a>既存の Cloud Services の逆引き DNS の表示
既存のクラウド サービスで構成されている値を確認するには、"Get-AzureService" コマンドレットを使います。

```powershell
Get-AzureService "contosoapp1"
```

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>既存の Cloud Services からの逆引き DNS の削除
既存のクラウド サービスから逆引き DNS プロパティを削除するには、"Set-AzureService" コマンドレットを使います。 この操作を行うには、逆引き DNS プロパティの値を空白にします。

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]


