---
title: IoT Hub で Azure Security Center for IoT サービスのプレビューを有効にする | Microsoft Docs
description: IoT Hub で Azure Security Center for IoT サービスを有効にする方法について説明します。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 59021d09f2af9d430b118acdeb8aa977094e683e
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862388"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>クイック スタート:IoT Hub でサービスを有効にする

> [!IMPORTANT]
> Azure Security Center for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、IoT Hub で Azure Security Center (ASC) for IoT プレビュー サービスを有効にする方法について説明します。  

> [!NOTE]
> Azure Security Center for IoT では、現在、Standard レベル以上の IoT Hub だけがサポートされています。
> Azure Security Center for IoT は、単一ハブのソリューションです。 複数のハブが必要な場合は、複数のソリューションが必要になります。 

## <a name="prerequisites-for-enabling-the-service"></a>サービスを有効にするための前提条件

- Log Analytics ワークスペース
  - 既定では、ASC for IoT によって、2 種類の情報 (**セキュリティ アラート**と**レコメンデーション**) が Log Analytics ワークスペースに保存されます。 
  - 追加の情報の種類として**未加工のイベント**のストレージを追加することもできます。 Log Analytics に**未加工のイベント**を保存すると、追加のストレージ コストがかかることに注意してください。 
- IoT Hub (Standard レベル以上)

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>IoT Hub で ASC for IoT を有効にする 

IoT Hub でセキュリティを有効にするには、以下の手順を実行します。 

1. Azure portal で **[IoT Hub]** を開きます。 
2. 左側のメニューの **[セキュリティ]** を選択して開きます。 
3. **[Enable IoT Security]\(IoT セキュリティを有効にする\)** を選択します。 
4. Log Analytics ワークスペースの詳細を指定します。 
   - **未加工イベント**のトグルを**オン**のままにして、ストレージの既定の情報の種類に加えて**未加工イベント**を保存することを選択します。 
   - **ツイン コレクション**のトグルを**オン**のままにして、**ツイン コレクション**を有効にすることを選択します。 
5. Click **OK**. 
6. **[Save]** をクリックします。 

お疲れさまでした。 IoT Hub で ASC for IoT を有効にすることができました。 

## <a name="next-steps"></a>次の手順

次の記事に進んで、ソリューションの構成方法を学習してください。

> [!div class="nextstepaction"]
> [ソリューションを構成する](quickstart-configure-your-solution.md)