---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 79ae38db73d55021572d04f693e5cb809e9bd056
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228969"
---
予期される値と既定の余白の上限と下限を含むデータが返されます。 実際には、[sensitivity] パラメーターを定義し、(ExpectedValue + sensitivity * UpperMargin) を境界の上限、(ExpectedValue - sensitivity * LowerMargin) を境界の下限として使用して異常ポイントをチューニングすることができます。 [sensitivity] の値は、1 より大きい必要があります。 チューニング用のダイアグラムを以下にいくつか示します。

> [!NOTE]
> ダイアグラムは、サンプル アプリケーションで生成されたものではありません。 サンプル アプリケーションと別のツールを使って作成されています。

![チューニング: sensitivity = 1.0](../media/sensitivity_1.png)
![チューニング: sensitivity = 1.5](../media/sensitivity_1.5.png)
![チューニング: sensitivity = 2](../media/sensitivity_2.png)
![チューニング: sensitivity = 3.5](../media/sensitivity_3.5.png)
