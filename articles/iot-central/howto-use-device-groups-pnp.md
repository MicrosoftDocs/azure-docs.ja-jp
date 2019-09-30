---
title: Azure IoT Central アプリケーションでデバイス グループを使用する | Microsoft Docs
description: オペレーターとして、Azure IoT Central アプリケーションでデバイス グループを使用する方法。
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 07f1df101442e8ae43b26cebc60c8452d475d0f3
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878983"
---
# <a name="use-device-groups-in-your-azure-iot-central-application-preview-features"></a>Azure IoT Central アプリケーションでデバイス グループを使用する (プレビュー機能)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

この記事では、オペレーター向けに、Azure IoT Central アプリケーションでデバイス グループを使用する方法について説明します。

デバイス グループは、指定されたいくつかの条件に一致しているためにグループ化されたデバイスの一覧です。 デバイス グループは、デバイスをより小さい論理グループにグループ化することで、デバイスを大規模に管理、視覚化、および分析するのに役立ちます。 たとえば、シアトルのすべてのエアコン デバイス グループの一覧を作成して、技術者が自分の担当するデバイスを検索できるようにできます。 この記事では、デバイス グループを作成および構成する方法を説明します。

## <a name="create-a-device-group"></a>デバイス グループを作成する

デバイス グループを作成するには、次の手順を実行します。

1. 左側のナビゲーション メニューで **[Device Groups]\(デバイス グループ\)** を選択します。

1. **[+新規]** を選択します。

    ![新しいデバイス グループ](media/howto-use-device-groups-pnp/image1.png)

1. デバイス グループにアプリケーション全体で一意の名前を付けます。 説明を追加することもできます。 デバイス グループに含めることができるのは、1 つのデバイス テンプレートのデバイスだけです。 このグループに使用するデバイス テンプレートを選択します。

1. プロパティ、比較演算子、および値を選択して、デバイス グループのデバイスを識別するクエリを作成します。 デバイス グループに配置する**すべての**条件を満たす複数のクエリとデバイスを追加できます。 作成したデバイス グループには、アプリケーションにアクセスできるすべてのユーザーがアクセスできるため、だれでもデバイス グループを表示、変更、または削除できます。

    ![デバイス グループのクエリ](media/howto-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > デバイス グループは動的なクエリです。 デバイスの一覧を表示するたびに、一覧内のデバイスが異なることがあります。 一覧は、現在クエリの条件を満たしているデバイスによって異なります。

1. **[保存]** を選択します。

## <a name="analytics"></a>Analytics

デバイス グループの分析は、左側のナビゲーション メニューにあるメインの分析タブと同じです。 分析については、[分析を作成する方法](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)に関する記事に詳細情報が記載されています。

## <a name="next-steps"></a>次の手順

ここでは、Azure IoT Central アプリケーションでデバイス グループを使用する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [テレメトリのルールを作成する方法](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
