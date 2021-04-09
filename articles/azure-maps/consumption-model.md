---
title: ルート指定のための車両消費モデル | Microsoft Azure Maps
description: Azure Maps によってサポートされる消費モデル (Combustion と Electric) について学習します。 各モデルで使用されるパラメーターを確認し、パラメーターの制約を表示します。
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: b44186d783a249192a8c13ee97063034ee319df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013141"
---
# <a name="consumption-model"></a>消費モデル

ルーティング サービスでは、車両固有の消費モデルの詳細な説明用のパラメーターのセットが提供されます。
**vehicleEngineType** の値に応じて、_Combustion_ と _Electric_ の 2 つのプリンシパル消費モデルがサポートされます。 異なるモデルに属するパラメーターを同じ要求で指定するのは正しくありません。 また、消費モデルのパラメーターは、**travelMode** 値 _bicycle_ および _pedestrian_ と共に使用することはできません。

## <a name="parameter-constraints-for-consumption-model"></a>消費モデルのパラメーターの制約

どちらの消費モデルでも、パラメーターを指定する際にいくつかの依存関係があります。 つまり、一部のパラメーターを明示的に指定する場合は、他のパラメーターの指定が必要になることがあります。 次に、注意すべきこれらの依存関係を示します。

* ユーザーは、すべてのパラメーターで **constantSpeedConsumption** を指定する必要があります。 **constantSpeedConsumption** を指定しない場合、他の消費モデル パラメーターを指定するとエラーになります。 **vehicleWeight** パラメーターは、この要件の例外です。
* **accelerationEfficiency** と **decelerationEfficiency** は、常に、ペアとして指定する必要があります (つまり、両方とも指定するか、両方とも指定しない)。
* **accelerationEfficiency** と **decelerationEfficiency** の両方を指定する場合、それらの値の積が 1 を超えてはなりません (永久運動を防ぐため)。
* **uphillEfficiency** と **downhillEfficiency** は、常に、ペアとして指定する必要があります (つまり、両方とも指定するか、両方とも指定しない)。
* **uphillEfficiency** と **downhillEfficiency** の両方を指定する場合、それらの値の積が 1 を超えてはなりません (永久運動を防ぐため)。
* ユーザーは \*__Efficiency__ パラメーターを指定する場合、**vehicleWeight** も指定する必要があります。 **vehicleEngineType** が _combustion_ の場合は、**fuelEnergyDensityInMJoulesPerLiter** も指定する必要があります。
* **maxChargeInkWh** と **currentChargeInkWh** は、常に、ペアとして指定する必要があります (つまり、両方とも指定するか、両方とも指定しない)。

> [!NOTE]
> **constantSpeedConsumption** だけを指定する場合は、傾斜や車両加速度などの他の消費側面は消費計算に考慮されません。

## <a name="combustion-consumption-model"></a>Combustion 消費モデル

Combustion 消費モデルは、**vehicleEngineType** を _combustion_ に設定すると使用されます。
このモデルに属するパラメーターは次のとおりです。 詳細については、パラメーター セクションを参照してください。

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Electric 消費モデル

Electric 消費モデルは、**vehicleEngineType** を _electric_ に設定すると使用されます。
このモデルに属するパラメーターは次のとおりです。 詳細については、パラメーター セクションを参照してください。

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>消費パラメーターの適切な値

消費パラメーターの特定のセットは、セットが明示的な要件をすべて満たしても、拒否される場合があります。 そのようなことが起きるのは、特定のパラメーターの値、または複数のパラメーターの値の組み合わせが、結果として不適切な大きさの消費値になると見なされる場合です。 そのような場合、通常は入力エラーを示すので、消費パラメーターがすべて適切な値になるように注意します。 消費パラメーターの特定のセットが拒否された場合は、関連するエラー メッセージに理由の説明が含まれます。
パラメーターの詳細な説明には、両方のモデルの適切な値の例が含まれます。
