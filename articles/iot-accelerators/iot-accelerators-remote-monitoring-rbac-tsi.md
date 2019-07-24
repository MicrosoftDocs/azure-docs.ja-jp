---
title: リモート監視のデータ アクセス制御 - Azure | Microsoft Docs
description: この記事では、リモート監視ソリューション アクセラレータで Time Series Insights テレメトリ エクスプローラー用のアクセス制御を構成する方法について説明します
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: ecc3cb32a4bbacb92c875133bf72fc0374bc24df
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46369985"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Time Series Insights テレメトリ エクスプローラー用のアクセス制御を構成する

この記事では、リモート監視ソリューション アクセラレータで Time Series Insights テレメトリ エクスプローラー用のアクセス制御を構成する方法について説明します。 ソリューション アクセラレータのユーザーが Time Series Insights エクスプ ローラーにアクセスできるようにするには、各ユーザーにデータのアクセス許可を付与する必要があります。

データ アクセス ポリシーは、データ クエリの発行、環境内での参照データの操作、環境に関連付けられた保存クエリとパースペクティブの共有を実行するためのアクセス許可を付与します。

[!INCLUDE [iot-tsi-data-access](../../includes/iot-tsi-data-access.md)]

## <a name="next-steps"></a>次の手順

この記事では、リモート監視ソリューション アクセラレータで Time Series Insights テレメトリ エクスプローラー用のアクセス制御を付与する方法について説明します。

リモート監視ソリューション アクセラレータの概念に関する詳細については、[リモート監視のアーキテクチャ](iot-accelerators-remote-monitoring-sample-walkthrough.md)に関するページをご覧ください

リモート監視ソリューションのカスタマイズの詳細については、「[マイクロサービスをカスタマイズして再展開する](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->」をご覧ください