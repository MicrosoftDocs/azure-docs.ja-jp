---
title: Azure Security Center for IoT ソリューションのプレビューを構成する | Microsoft Docs
description: Azure Security Center for IoT を使用してエンド ツー エンドの IoT ソリューションを構成する方法について説明します。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 64a04861b14e48eaa14a369546dd9d54bf59f019
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58861923"
---
# <a name="quickstart-configure-your-iot-solution"></a>クイック スタート:IoT ソリューションを構成する

> [!IMPORTANT]
> Azure Security Center for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、ASC for IoT を使用して IoT セキュリティ ソリューションの初期構成を実行する方法について説明します。 

## <a name="azure-security-center-asc-for-iot"></a>Azure Security Center (ASC) for IoT

ASC for IoT では、Azure ベースの IoT ソリューションに対して包括的なエンド ツー エンドのセキュリティが提供されます。

ASC for IoT を使用すると、1 つのダッシュボードで IoT ソリューション全体を監視し、Azure 内のすべての IoT デバイス、IoT プラットフォーム、およびバックエンド リソースを明確に把握することができます。

ASC for IoT を IoT ハブで有効にすると、IoT ハブに接続されていて IoT ソリューションに関連している他の Azure サービスが自動的に識別されます。

自動でリレーションシップを検出できるだけでなく、他のどの Azure リソースを IoT ソリューションの一部としてタグ付けするかを選択することもできます。
選択に応じて、サブスクリプション全体、リソース グループ、または単一のリソースを追加できます。

すべてのリソースのリレーションシップを定義すると、ASC for IoT によって、これらのリソースに対するセキュリティのレコメンデーションとアラートが Azure Security Center を通じて提供されるようになります。

## <a name="add-azure-resources-to-your-iot-solution"></a>IoT ソリューションへの Azure リソースの追加

IoT ソリューションに新しいリソースを追加するには、次の操作を行います。 

1. Azure portal で **[IoT Hub]** を開きます。 
2. 左側のメニューの **[セキュリティ]** の下の **[リソース]** を選択して開きます。 
3. **[リソースの追加]** を選択します。
4. IoT ソリューションに属するリソースを選択します。
5. **[追加]** をクリックします。 

お疲れさまでした。 IoT ソリューションに新しいリソースが追加されました。

これで、ASC for IoT によって、新たに追加されたリソースが監視され、関連するセキュリティのレコメンデーションとアラートが IoT ソリューションの一部として明確に示されるようになりました。

## <a name="next-steps"></a>次の手順

次の記事に進んで、セキュリティ モジュールの作成方法を学習してください。

> [!div class="nextstepaction"]
> [セキュリティ モジュールを作成する](quickstart-create-security-twin.md)