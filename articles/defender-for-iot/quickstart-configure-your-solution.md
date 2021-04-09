---
title: 'クイックスタート: Azure リソースを IoT ソリューションに追加する'
description: このクイックスタートでは、Azure Defender for IoT を使用してエンド ツー エンドの IoT ソリューションを構成する方法について説明します。
ms.topic: quickstart
ms.date: 01/25/2021
ms.openlocfilehash: 1bde15919f9fa69bb9f9de7459895a70e9b74f71
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781026"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>クイック スタート:Azure Defender for IoT ソリューションを構成する

この記事では、Defender for IoT を使用して IoT セキュリティ ソリューションの初期構成を実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

なし

## <a name="what-is-defender-for-iot"></a>Defender for IoT とは

Defender for IoT は、Azure ベースの IoT ソリューションに対して包括的なエンド ツー エンドのセキュリティを提供します。

Defender for IoT を使用すると、1 つのダッシュボードで IoT ソリューション全体を監視し、Azure 内のすべての IoT デバイス、IoT プラットフォーム、およびバックエンド リソースを明確に把握することができます。

Defender for IoT をお使いの IoT Hub で有効にすると、その IoT Hub に接続されていてお使いの IoT ソリューションに関連している他の Azure サービスが自動的に識別されます。

自動でリレーションシップを検出できるだけでなく、他のどの Azure リソース グループを IoT ソリューションの一部としてタグ付けするかを選択することもできます。

選択に応じて、サブスクリプション全体、リソース グループ、または単一のリソースを追加できます。

すべてのリソースのリレーションシップを定義すると、Defender for IoT によって、これらのリソースに対するセキュリティのレコメンデーションとアラートが Defender を通じて提供されるようになります。

## <a name="add-azure-resources-to-your-iot-solution"></a>IoT ソリューションへの Azure リソースの追加

IoT ソリューションに新しいリソースを追加するには、次のようにします。

1. Azure portal で **[IoT Hub]** を開きます。

1. **[セキュリティ]** の **[概要]** を選択し、続けて **[設定]** 、 **[Monitored Resources]\(監視対象リソース\)** の順に選択します。

1. **[編集]** を選択し、自分の IoT ソリューションに属している監視対象リソースを選択します。

1. **[追加]** を選択します。

お疲れさまでした。 IoT ソリューションに新しいリソース グループが追加されました。

これで、Defender for IoT によって、新たに追加されたリソース グループが監視され、関連するセキュリティのレコメンデーションとアラートが IoT ソリューションの一部として明確に示されるようになりました。

## <a name="next-steps"></a>次のステップ

次の記事に進んで、Defender-IoT-micro-agent の作成方法を学習してください。

> [!div class="nextstepaction"]
> [Defender-IoT-micro-agent の作成](quickstart-create-security-twin.md)
