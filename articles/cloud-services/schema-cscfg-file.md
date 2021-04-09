---
title: Azure Cloud Services (クラシック) 定義スキーマ (.cscfg ファイル) | Microsoft Docs
description: サービス構成 (.cscfg) ファイルでは、ロールごとに配置するロール インスタンスの数、構成値、およびロールの証明書のサムプリントを指定します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ba933d4981fb7ed209a7fb5d5c41113750f312de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743476"
---
# <a name="azure-cloud-services-classic-config-schema-cscfg-file"></a>Azure Cloud Services (クラシック) 構成スキーマ (.cscfg ファイル)

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そのため、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

サービス構成ファイルは、サービスのロールごとに配置するロール インスタンスの数、すべての構成設定の値、ロールに関連付けられているすべての証明書のサムプリントを指定します。 サービスが仮想ネットワークの一部である場合は、仮想ネットワーク構成ファイルだけでなく、サービス構成ファイルでネットワークの構成情報を指定してください。 サービス構成ファイルの既定の拡張子は .cscfg です。

サービス モデルは、[Cloud Service (クラシック) 定義スキーマ](schema-csdef-file.md)に関するページで説明されています。

既定では、Azure Diagnostics 構成スキーマ ファイルは `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` ディレクトリにインストールされます。 `<version>` は、インストールされている [Azure SDK](https://azure.microsoft.com/downloads/) バージョンで置き換えてください。

サービスでのロール構成の詳細については、[クラウド サービス モデルとは何か](cloud-services-model-and-package.md)に関するページをご覧ください。

## <a name="basic-service-configuration-schema"></a>基本的なサービス構成スキーマ
サービス構成ファイルの基本形式は次のとおりです。

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>スキーマ定義
次のトピックでは、`ServiceConfiguration` 要素のスキーマについて説明します。

- [Role スキーマ](schema-cscfg-role.md)
- [NetworkConfiguration スキーマ](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>サービス構成名前空間
サービス構成ファイルの XML 名前空間は、次のとおりです。`http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a>ServiceConfiguration 要素
`ServiceConfiguration` 要素は、サービス構成ファイルの最上位の要素です。

以下の表に、`ServiceConfiguration` 要素の属性を示します。 すべての属性値は、文字列型です。

| 属性 | 説明 |
| --------- | ----------- |
|serviceName|必須。 クラウド サービスの名前。 ここで与えられた名前は、サービス定義ファイルで指定された名前と一致する必要があります。|
|osFamily|省略可能。 クラウド サービスのロール インスタンスで実行されるゲスト OS を指定します。 サポートされるゲスト OS のリリース版については、「[Azure ゲスト OS リリースと SDK の互換性対応表](cloud-services-guestos-update-matrix.md)」をご覧ください。<br /><br /> `osFamily` 値を含めず、特定のゲスト OS バージョンについて `osVersion` 属性を設定していない場合、既定値 1 が使用されます。|
|osVersion|省略可能。 クラウド サービスのロール インスタンスで実行されるゲスト OS のバージョンを指定します。 ゲスト OS のバージョンの詳細については、「[Azure ゲスト OS リリースと SDK の互換性対応表](cloud-services-guestos-update-matrix.md)」をご覧ください。<br /><br /> ゲスト OS が自動的に最新バージョンにアップグレードされるように指定することができます。 これを行うには、`osVersion` 属性の値を `*` に設定します。 `*` に設定されると、指定された OS ファミリのゲスト OS の最新バージョンを使用してロール インスタンスがデプロイされ、ゲスト OS の新しいバージョンがリリースされたときに自動的にアップグレードします。<br /><br /> 特定のバージョンを手動で指定するには、「[Azure ゲスト OS リリースと SDK の互換性対応表](cloud-services-guestos-update-matrix.md)」の **今後、現在、移行中のゲスト OS バージョン** に関するセクションにある表から `Configuration String` を使用します。<br /><br /> `osVersion` 属性の既定値は `*` です。|
|schemaVersion|省略可能。 サービス構成スキーマのバージョンを指定します。 複数のバージョンの SDK が一緒にインストールされている場合、Visual Studio では、スキーマ バージョンにより、スキーマの検証に使用する適切な SDK ツールを選択できます。 スキーマとバージョンの互換性の詳細については、「[Azure ゲスト OS リリースと SDK の互換性対応表](cloud-services-guestos-update-matrix.md)」をご覧ください。|

サービス構成ファイルには、`ServiceConfiguration` 要素を 1 つ含める必要があります。 `ServiceConfiguration` 要素は、任意の数の `Role` 要素と、0 または 1 つの `NetworkConfiguration` 要素を含む場合があります。