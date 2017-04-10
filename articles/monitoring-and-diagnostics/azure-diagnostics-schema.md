---
title: "Azure 診断構成スキーマのバージョンの一覧 |Microsoft Docs"
description: "Azure Virtual Machines、VM Scale Sets、Service Fabric、および Cloud Services のパフォーマンス カウンターの収集を構成するために使用します。"
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/09/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: ac1ae46a165baad675c6b2445f91e05694eae6f9
ms.openlocfilehash: 5d0894430dc915fc46d753a0b672de3b51ce3888
ms.lasthandoff: 02/22/2017


---
# <a name="list-of-azure-diagnostics-versions"></a>Azure 診断のバージョンの一覧
このページでは、Microsoft Azure SDK に付属する Azure 診断スキーマのバージョン一覧を示します。  

> [!NOTE]
> Azure 診断は、Azure Virtual Machines、Virtual Machine Scale Sets、Service Fabric、および Cloud Services からパフォーマンス カウンターやその他の統計情報を収集するために使用するコンポーネントです。  このページは、これらのサービスのいずれかを使用している場合にのみ該当します。
>

Azure 診断は、Azure Monitor、Application Insights、Log Analytics など、他の Microsoft 診断製品と共に使用します。

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK 診断バージョン配布グラフ  

|Azure SDK のバージョン | Azure 診断のバージョン | モデル|  
|------------------|---------------------------|------|  
|1.x               |1.0                         | プラグイン|  
|2.0 ～ 2.4         |1.0                         |"|  
|2.5               |1.2                         |拡張機能|  
|2.6               |1.3                         |"|  
|2.7               |1.4                         |"|  
|2.8               |1.5                         |"|  
|2.9               |1.6                         |"|
|2.96              |1.7                         |"|



 Azure 診断バージョン 1.0 は最初はプラグイン モデルに付属しており、Azure SDK をインストールすると、その SDK に付属する Azure 診断バージョンを入手できました。  

 SDK 2.5 (診断バージョン 1.2) 以降、Azure 診断は拡張機能モデルに移行しています。 新しい機能を利用するツールは新しい Azure SDK でのみ使用できましたが、診断を使用するクラウド サービスや仮想マシンはすべて、最新バージョンを Azure から直接選択します。  

 たとえば、まだ SDK 2.5 を使用しているすべてのユーザーが、新しい機能を使用しているかどうかに関係なく、診断 1.5 を読み込みます。  

## <a name="azure-diagnostics-schemas-index"></a>Azure 診断スキーマのインデックス  
[診断 1.0 構成スキーマ](azure-diagnostics-schema-1dot0.md)  

[診断 1.2 構成スキーマ](azure-diagnostics-schema-1dot2.md)  

[診断 1.3 およびそれ以降の構成スキーマ](azure-diagnostics-schema-1dot3-and-later.md)  

