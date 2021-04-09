---
title: Azure Cloud Services (クラシック) の定義。NetworkTrafficRules スキーマ | Microsoft Docs
description: ロールの内部エンドポイントにアクセスできるロールを制限する NetworkTrafficRules について説明します。 サービス定義ファイルのロールと組み合わせて使用します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2c8ab53068b71652d03d03bf79a224fe5e34dff3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98739770"
---
# <a name="azure-cloud-services-classic-definition-networktrafficrules-schema"></a>Azure Cloud Services (クラシック) 定義: NetworkTrafficRules スキーマ

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そのため、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

`NetworkTrafficRules` ノードは、ロールが互いに通信する方法を指定する、サービス定義ファイル内の省略可能な要素です。 特定のロールの内部エンドポイントにアクセスできるロールを制限します。 `NetworkTrafficRules` はスタンドアロン要素ではなく、サービス定義ファイルで 2 つ以上のロールと組み合わされます。

サービス定義ファイルの既定の拡張子は .csdef です。

> [!NOTE]
>  `NetworkTrafficRules` ノードは、Azure SDK バージョン 1.3 以上を使用している場合のみ利用可能です。

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>ネットワーク トラフィック規則の基本サービス定義スキーマ
ネットワーク トラフィック定義を含むサービス定義ファイルの基本形式は次のとおりです。

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>スキーマ要素
サービス定義ファイルの `NetworkTrafficRules` ノードには、このトピックの以降のセクションで詳しく説明するように、次の要素が含まれます。

[NetworkTrafficRules 要素](#NetworkTrafficRules)

[OnlyAllowTrafficTo 要素](#OnlyAllowTrafficTo)

[Destinations 要素](#Destinations)

[RoleEndpoint 要素](#RoleEndpoint)

AllowAllTraffic 要素

[WhenSource 要素](#WhenSource)

[FromRole 要素](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a> NetworkTrafficRules 要素
`NetworkTrafficRules` 要素は、どのロールが別のロールのどのエンドポイントと通信できるかを指定します。 サービスには、`NetworkTrafficRules` 定義を 1 つ含めることができます。

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo 要素
`OnlyAllowTrafficTo` 要素は、送信先エンドポイントのコレクションおよびこれらと通信できるロールを記述します。 複数の `OnlyAllowTrafficTo` ノードを指定できます。

##  <a name="destinations-element"></a><a name="Destinations"></a> Destinations 要素
`Destinations` 要素は、通信できる RoleEndpoint のコレクションを記述します。

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a> RoleEndpoint 要素
`RoleEndpoint` 要素は、通信を許可するロール上のエンドポイントを記述します。 ロールに複数のエンドポイントがある場合は、複数の `RoleEndpoint` 要素を指定することができます。

| 属性      | Type     | 説明 |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | 必須。 トラフィックの宛先として許可するエンドポイントの名前。|
| `roleName`     | `string` | 必須。 通信の相手として許可する Web ロールの名前。|

## <a name="allowalltraffic-element"></a>AllowAllTraffic 要素
`AllowAllTraffic` 要素は、`Destinations` ノードで定義されたエンドポイントとの通信をすべてのロールに許可する規則です。

##  <a name="whensource-element"></a><a name="WhenSource"></a> WhenSource 要素
`WhenSource` 要素は、`Destinations` ノードで定義されたエンドポイントと通信できるロールのコレクションを記述します。

| 属性 | Type     | 説明 |
| --------- | -------- | ----------- |
| `matches` | `string` | 必須。 通信を許可するときに適用する規則を指定します。 有用な値は現在 `AnyRule` のみです。|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a> FromRole 要素
`FromRole` 要素は、`Destinations` ノードで定義されたエンドポイントと通信できるロールを指定します。 エンドポイントと通信できるロールが複数ある場合、複数の `FromRole` 要素を指定することができます。

| 属性  | Type     | 説明 |
| ---------- | -------- | ----------- |
| `roleName` | `string` | 必須。 通信の許可元のロールの名前。|

## <a name="see-also"></a>参照
[Cloud Service (クラシック) 定義スキーマ](schema-csdef-file.md)




