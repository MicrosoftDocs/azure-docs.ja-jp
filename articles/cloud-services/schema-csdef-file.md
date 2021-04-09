---
title: Azure Cloud Services (クラシック) 定義スキーマ (.csdef ファイル) | Microsoft Docs
description: サービス定義 (.csdef) ファイルは、サービスの利用可能なロール、エンドポイント、および構成値を含む、アプリケーションのサービス モデルを定義します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: b98534b049698ea95c6738ce3404dd5ef8ff7a28
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502266"
---
# <a name="azure-cloud-services-classic-definition-schema-csdef-file"></a>Azure Cloud Services (クラシック) 定義スキーマ (.csdef ファイル)

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そのため、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

サービス定義ファイルは、アプリケーションのサービス モデルを定義します。 このファイルには、クラウド サービスで使用できるロールの定義が含まれ、サービス エンドポイントの指定やサービスの構成設定の確立を行います。 構成設定の値は、[クラウド サービス (クラシック) 構成スキーマ](/previous-versions/azure/reference/ee758710(v=azure.100))に関するページの説明に従って、サービス構成ファイルで設定されます。

既定では、Azure Diagnostics 構成スキーマ ファイルは `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` ディレクトリにインストールされます。 `<version>` は、インストールされている [Azure SDK](https://www.windowsazure.com/develop/downloads/) バージョンで置き換えてください。

サービス定義ファイルの既定の拡張子は .csdef です。

## <a name="basic-service-definition-schema"></a>基本サービス定義スキーマ
サービス定義ファイルには、`ServiceDefinition` 要素を 1 つ含める必要があります。 サービス定義には、ロール (`WebRole` または `WorkerRole`) 要素を少なくとも 1 つ含める必要があります。 また、単一の定義に定義された最大 25 のロールを含めることができ、ロールの種類を混在させることができます。 サービス定義には、省略可能な `NetworkTrafficRules` 要素も含まれます。この要素は、指定した内部エンドポイントと通信できるロールを制限します。 また、省略可能な `LoadBalancerProbes` 要素も含まれます。この要素には、顧客が定義した、エンドポイントの正常性プローブが含まれます。

サービス定義ファイルの基本形式は次のとおりです。

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>スキーマ定義
以下のトピックでは、スキーマについて説明されています。

- [LoadBalancerProbe スキーマ](schema-csdef-loadbalancerprobe.md)
- [WebRole スキーマ](schema-csdef-webrole.md)
- [WorkerRole スキーマ](schema-csdef-workerrole.md)
- [NetworkTrafficRules スキーマ](schema-csdef-networktrafficrules.md)

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a> ServiceDefinition 要素
`ServiceDefinition` 要素は、サービス定義ファイルの最上位の要素です。

以下の表に、`ServiceDefinition` 要素の属性を示します。

| 属性               | 説明 |
| ----------------------- | ----------- |
| name                    |必須。 サービスの名前。 サービス アカウント内で一意となる名前を使用してください。|
| topologyChangeDiscovery | 省略可能。 トポロジの変更通知の種類を指定します。 次のいずれかの値になります。<br /><br /> -   `Blast` - 更新プログラムをすべてのロール インスタンスにできるだけ早く送信します。 オプションを選択する場合、ロールでは、再起動せずにトポロジの更新プログラムを処理できる必要があります。<br />-   `UpgradeDomainWalk` – 前のロール インスタンスが更新プログラムを正常に受け入れた後に、更新プログラムを各インスタンスに順次送信します。|
| schemaVersion           | 省略可能。 サービス定義スキーマのバージョンを指定します。 複数のバージョンの SDK が一緒にインストールされている場合、Visual Studio では、スキーマ バージョンにより、スキーマの検証に使用する適切な SDK ツールを選択できます。|
| upgradeDomainCount      | 省略可能。 このサービスのロールが割り当てられるアップグレード ドメインの数を指定します。 そのサービスをデプロイすると、ロール インスタンスがアップグレード ドメインに割り当てられます。 詳しくは、「[クラウド サービス ロールまたはデプロイを更新する](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment)」、[仮想マシンの可用性の管理](../virtual-machines/availability.md)に関する記事、[クラウド サービス モデルの概要](./cloud-services-model-and-package.md)に関する記事をご覧ください。<br /><br /> アップグレード ドメインを最大 20 まで指定できます。 指定しない場合、アップグレード ドメインの既定の数は 5 です。|