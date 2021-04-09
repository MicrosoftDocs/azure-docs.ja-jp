---
title: アンカーの検索方法
description: 検索 API を呼び出す場合のさまざまな方法について説明します
author: pamistel
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: pamistel
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 13aa12be5a336363bbe3bcbf3e3fb354a8fa3074
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048478"
---
# <a name="understanding-the-anchorlocatecriteria-class"></a>AnchorLocateCriteria クラスについて
この記事では、アンカーを照会する場合に使用できるさまざまなオプションについて説明します。 AnchorLocateCriteria クラス、そのオプション、およびオプションの有効な組み合わせについて見ていきます。

## <a name="anchor-locate-criteria"></a>アンカーの検索条件
[AnchorLocateCriteria クラス](/dotnet/api/microsoft.azure.spatialanchors.anchorlocatecriteria)は、以前に作成したアンカーをサービスに照会する場合に役立ちます。 ウォッチャーごとに 1 つの AnchorLocateCriteria オブジェクトをいつでも使用できます。 各 AnchorLocateCriteria オブジェクトには、次のプロパティのうち **1 つだけ** 含める必要があります: [Identifiers](#identifiers)、[NearAnchor](#nearanchor)、[NearDevice](#neardevice)。 必要に応じて、[Strategy](#strategy)、[BypassCache](#bypasscache)、[RequestedCategories](#requestedcategories) などの追加のプロパティを設定することもできます。 

### <a name="properties"></a>Properties
ご利用のウォッチャーでは、次のプロパティのうち **1 つだけ** を定義します。
#### <a name="identifiers"></a>識別子
*既定値: 空の文字列配列*

Identifiers を使用すると、検索するアンカーについてアンカー ID のリストを定義できます。 アンカー ID は、アンカーの作成が正常に完了すると、最初に返されます。 Identifiers を指定した場合、AnchorLocateCriteria では、要求されたアンカーのセットをアンカー ID と一致するアンカーに制限します。 このプロパティは、文字列配列を使用して指定されます。 

#### <a name="nearanchor"></a>NearAnchor
*既定値: 設定されていません*

NearAnchor を使用すると、AnchorLocateCriteria で、要求されたアンカーのセットを選択されたアンカーから必要な距離内にあるアンカーに制限するように指定することができます。 この選択したアンカーを、ソース アンカーとして指定する必要があります。 また、ソース アンカーからの必要な距離を設定し、返されるアンカーの最大数を設定して、検索にさらに制限をかけることもできます。
このプロパティは、NearAnchorCriteria オブジェクトを使用して指定します。

#### <a name="neardevice"></a>NearDevice
*既定値: 設定されていません*

NearDevice を使用すると、AnchorLocateCriteria で、要求されたアンカーのセットをデバイスの物理的な場所の近くにあるものに制限するように指定することができます。 有効になっているセンサーを使用すれば、デバイス周辺のアンカーを容易に検出することができます。 アンカーを検出できる可能性を最大限に高めるには、該当するすべてのセンサーへのセッション アクセスを許可するように SensorCapabilities を構成する必要があります。 このプロパティの設定方法および使用方法の詳細については、「[粗い再局在化 - Azure Spatial Anchors | Microsoft Docs](./coarse-reloc.md)」ならびに [C#](../how-tos/set-up-coarse-reloc-unity.md)、[Objective-C](../how-tos/set-up-coarse-reloc-unity.md)、[Swift](../how-tos/set-up-coarse-reloc-swift.md)、[Java](../how-tos/set-up-coarse-reloc-java.md)、[C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)、[C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md) で "*粗い再局在化を使用してアンカーを作成および検索する方法*" を参照してください。
このプロパティは、NearDeviceCriteria オブジェクトを使用して指定します。

### <a name="additional-properties"></a>追加のプロパティ
#### <a name="bypasscache"></a>BypassCache
*既定値: false*

セッション内でアンカーが作成または検索された場合、それはキャッシュにも格納されます。  このプロパティを false に設定すると、同じセッション内の後続のクエリからは、キャッシュされた値が返されます。 ASA サービスへの要求は行われません。

#### <a name="requestedcategories"></a>RequestedCategories
*既定値: Properties | Spatial*

このプロパティは、AnchorLocateCriteria を使用してクエリから返されるデータを特定する場合に使用されます。 既定値を使用するとプロパティと空間データの両方が返されます。プロパティと空間データの両方が必要な場合は、この値を変更しないでください。 このプロパティは、AnchorDataCategory 列挙型を使用して指定できます。

AnchorDataCategory 列挙値 | 返されるデータ
-----|------------
なし | データは返されません。
Properties| AppProperties などのアンカー プロパティが返されます。
Spatial| アンカーに関する空間情報が返されます。

#### <a name="strategy"></a>戦略
*既定値: AnyStrategy*

Strategy を使用すると、アンカーの検索方法をさらに定義できます。 Strategy プロパティは、LocateStrategy 列挙型を使用して指定できます。

LocateStrategy 列挙値 | 説明
---------------|------------
AnyStrategy | この戦略を使用すると、システムによって VisualInformation と Relationship 戦略の組み合わを使用して、アンカーを検索することができます。 
VisualInformation|この方法を指定すると、現在の環境からのビジュアル情報をアンカーのビジュアル占有領域のものと照合することによってアンカーを検索することが試みられます。 アンカーのビジュアル占有領域とは、アンカーに現在関連付けられているビジュアル情報を指します。 このビジュアル情報は、通常、アンカーの作成中に収集されますが、これに限定されるものではありません。 現在、この方法は NearDevice または Identifier プロパティとの組み合わせでのみ使用できます。
リレーションシップ|この方法を指定すると、既存の[接続されたアンカー](./anchor-relationships-way-finding.md#connect-anchors)を使用してアンカーの検索が試みられます。 現在、この方法は NearAnchor または Identifiers プロパティとの組み合わせでのみ使用できます。 Identifiers プロパティと組み合わせて使用する場合、同じセッション内でユーザーは、Identifiers 配列で ID が指定されているアンカーへの接続関係が既に確立されているアンカーを事前に検索している必要があります。 


### <a name="valid-combinations-of-locatestrategy-and-anchorlocatecriteria-properties"></a>LocateStrategy と AnchorLocateCriteria プロパティの有効な組み合わせ 

現在、Strategy と AnchorLocateCriteria プロパティのすべての組み合わせがシステムで許可されているわけではありません。 次の表に利用可能な組み合わせを示します。



プロパティ | AnyStrategy | リレーションシップ | VisualInformation
-------- | ------------|--------------|-------------------
識別子 | &check;    | &check;     | &check;
NearAnchor  | &check;   (既定では Relationship) | &check;    | 
NearDevice  | &check;    |   | &check;




## <a name="next-steps"></a>次のステップ

AnchorLocateCriteria クラスを使用したその他の例については、「[Azure Spatial Anchors を使用してアンカーを作成および探知する方法](../create-locate-anchors-overview.md)」を参照してください。