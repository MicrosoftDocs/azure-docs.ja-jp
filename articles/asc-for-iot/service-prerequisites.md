---
title: Azure Security Center for IoT の前提条件 | Microsoft Docs
description: Azure Security Center for IoT サービスの使用を開始するために必要なすべてのことに関する前提条件の詳細。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.openlocfilehash: 4440fec98d1f561da6375bcaadba4282076cc53b
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299472"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Azure Security Center for IoT の前提条件

この記事では、Azure Security Center for IoT サービスのさまざまな構成要素、このサービスを開始するために必要なこと、およびこのサービスの理解に役立つ基本的な概念について説明します。 

## <a name="minimum-requirements"></a>最小要件

- IoT Hub Standard レベル
    - RBAC ロールの**所有者**レベルの特権 
- [Log Analytics ワークスペース](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (推奨)
    - Azure Security Center の使用は推奨事項であり、必須ではありません。 Azure Security Center がない場合、IoT Hub 内の他の Azure リソースを表示することはできません。 
 
## <a name="working-with-azure-security-center-for-iot-service"></a>Azure Security Center for IoT サービスを使用する

Azure Security Center for IoT の分析情報とレポートは、Azure IoT Hub と Azure Security Center を使用して入手できます。 Azure IoT Hub で Azure Security Center for IoT を有効にするには、**所有者**レベルの特権を持つアカウントが必要です。 IoT Hub で ASC for IoT を有効にすると、Azure Security Center for IoT 分析情報が、Azure IoT Hub では **[セキュリティ]** 機能として、Azure Security Center では **[IoT]** として表示されます。 

## <a name="supported-service-regions"></a>サポートされているサービス リージョン 

Azure Security Center for IoT は現在、次の Azure リージョン内の IoT Hub でサポートされています。
  - 米国中部  
  - East US 
  - 米国東部 2
  - 米国中西部
  - 米国西部
  - 米国西部 2
  - 米国中南部
  - 米国中北部
  - カナダ中部
  - カナダ東部 
  - 北ヨーロッパ    
  - ブラジル南部
  - フランス中部  
  - 英国西部 
  - 英国南部
  - 西ヨーロッパ 
  - 北ヨーロッパ 
  - 西日本  
  - 東日本  
  - オーストラリア南東部
  - オーストラリア東部
  - 東アジア   
  - 東南アジア
  - 韓国中部
  - 韓国南部 
  - インド中部
  - インド南部

Azure Security Center for IoT では、ヨーロッパの全リージョンからのトラフィックがすべて西ヨーロッパ リージョンのデータ センターにルーティングされ、それ以外の全リージョンからのトラフィックがすべて米国中部リージョンのデータ センターにルーティングされます。  
  
## <a name="wheres-my-iot-hub"></a>自分の IoT Hub はどこにありますか?

開始する前に、ご自分の IoT Hub の場所を確認して、サービスの提供状況を確認してください。 

1. IoT ハブを開きます。 
2. **[Overview]** をクリックします。 
3. 表示される場所が[サポートされているサービス リージョン](#supported-service-regions)のいずれかと一致することを確認します。 


## <a name="supported-platforms-for-agents"></a>エージェントでサポートされているプラットフォーム 

Azure Security Center for IoT エージェントは、増え続けているデバイスとプラットフォームをサポートしています。 [サポートされているプラットフォームの一覧](how-to-deploy-agent.md)を参照して、既存または予定しているデバイス ライブラリを確認してください。  

## <a name="next-steps"></a>次の手順
- Azure IoT セキュリティの[概要](overview.md)を読む
- [このサービスを有効にする](quickstart-onboard-iot-hub.md)方法を学習する
- [Azure Security Center for IoT の FAQ](resources-frequently-asked-questions.md) を読む
- [Azure Security Center for IoT アラートを理解する](concept-security-alerts.md)方法を確認する
