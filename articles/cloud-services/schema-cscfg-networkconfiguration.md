---
title: Azure Cloud Services (クラシック) NetworkConfiguration スキーマ | Microsoft Docs
description: Virtual Network と DNS の値を指定する、サービス構成ファイルの NetworkConfiguration 要素の子要素について説明します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
'---thor': tagore
ms.openlocfilehash: acf4c050ade21a6e5fc51ee6ace512eff00360ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743459"
---
# <a name="azure-cloud-services-classic-config-networkconfiguration-schema"></a>Azure Cloud Services (クラシック) 構成 NetworkConfiguration スキーマ

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そのため、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

サービス構成ファイルの `NetworkConfiguration` 要素は、仮想ネットワークと DNS の値を指定します。 これらの設定は、クラウド サービスのオプションです。

仮想ネットワークと関連付けられたスキーマの詳細については、次のリソースをご覧ください。

- [Cloud Service (クラシック) 構成スキーマ](schema-cscfg-file.md)
- [Cloud Service (クラシック) 定義スキーマ](schema-csdef-file.md)
- [仮想ネットワークの作成 (クラシック)](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)

## <a name="networkconfiguration-element"></a>NetworkConfiguration 要素
次の例は、`NetworkConfiguration` 要素とその子要素を示します。

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

次の表は、`NetworkConfiguration` 要素の子要素の説明です。

| 要素       | 説明 |
| ------------- | ----------- |
| AccessControl | 省略可能。 クラウド サービス内のエンドポイントにアクセスするためのルールを指定します。 アクセス制御の名前は、`name` 属性の文字列で定義されます。 `AccessControl` 要素には、1 つ以上の `Rule` 要素が含まれています。 複数の `AccessControl` 要素を定義できます。|
| ルール | 省略可能。 IP アドレスの指定されたサブネット範囲に対して実行されるアクションを指定します。 ルールの順序は `order` 属性の文字列値で定義されます。 ルール番号が小さいほど、優先度は高くなります。 たとえば、ルールは、100、200、および 300 の順序番号で指定できます。 100 の順序番号のルールは、200 の順序であるルールよりも優先されます。<br /><br /> ルールのアクションは `action` 属性の文字列で定義されます。 次のいずれかの値になります。<br /><br /> -   `permit`– 指定されたサブネット範囲からのパケットのみがエンドポイントと通信できることを指定します。<br />-   `deny`– 指定されたサブネット範囲内のエンドポイントへのアクセスが拒否されることを指定します。<br /><br /> ルールによって影響を受ける IP アドレスのサブネット範囲は、`remoteSubnet` 属性の文字列で定義されます。 ルールの説明は `description` 属性の文字列で定義されます。|
| EndpointAcl | 省略可能。 アクセス制御ルールのエンドポイントへの割り当てを指定します。 エンドポイントを含むロールの名前は、`role` 属性の文字列で定義されます。 エンドポイントの名前は、`endpoint` 属性の文字列で定義されます。 エンドポイントに適用される `AccessControl` ルールのセットの名前は、`accessControl` 属性の文字列で定義されます。 複数の `EndpointAcl` 要素を定義することができます。|
| DnsServer | 省略可能。 DNS サーバーの設定を指定します。 仮想ネットワークを使用しない DNS サーバーの設定を指定できます。 DNS サーバーの名前は、`name` 属性の文字列で定義されます。 DNS サーバーの IP アドレスは、`IPAddress` 属性の文字列で定義されます。 IP アドレスは、有効な IPv4 アドレスである必要があります。|
| VirtualNetworkSite | 省略可能。 クラウド サービスをデプロイする仮想ネットワーク サイトの名前を指定します。 この設定では、仮想ネットワーク サイトは作成されません。 お使いの仮想ネットワークのネットワーク ファイルに既に定義されているサイトを参照します。 クラウド サービスは 1 つの仮想ネットワークの 1 メンバーにのみなることができます。 この設定を指定しないと、クラウド サービスは仮想ネットワークにデプロイされません。 仮想ネットワークの名前は、`name` 属性の文字列で定義されます。|
| InstanceAddress | 省略可能。 仮想ネットワーク内の 1 つのサブネットまたはサブネットのセットへのロールの関連付けを指定します。 ロール名をインスタンス アドレスに関連付ける場合、このロールに関連付けたいサブネットを指定できます。 `InstanceAddress` はサブネット要素を含んでいます。 この 1 つまたは複数のサブネットに関連付けられているロールの名前は、`roleName` 属性の文字列で定義されます。|
| Subnet | 省略可能。 ネットワーク構成ファイル内のサブネット名に対応するサブネットを指定します。 サブネットの名前は、`name` 属性の文字列で定義されます。|
| ReservedIP | 省略可能。 デプロイに関連付けられる予約済み IP アドレスを指定します。 予約済み IP アドレスの作成には、Create Reserved IP Address を使用する必要があります。 クラウド サービスの各々のデプロイは、1 つの予約済み IP アドレスに関連付けることができます。 予約済み IP アドレスの名前は、`name` 属性の文字列で定義されます。|

## <a name="see-also"></a>参照
[Cloud Service (クラシック) 構成スキーマ](schema-cscfg-file.md)