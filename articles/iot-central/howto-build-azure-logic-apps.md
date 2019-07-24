---
title: Azure Logic Apps 内で IoT Central コネクタを使用してワークフローを作成する | Microsoft Docs
description: Azure Logic Apps 内で IoT Central コネクタを使用して、ワークフローのトリガーや、ワークフロー内のデバイスの作成、更新、および削除を行います。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 635c8d0f149895798eece8cf3b48712ab74374ea
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759884"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Azure Logic Apps 内で IoT Central コネクタを使用してワークフローを作成する

*このトピックはビルダーおよび管理者向けです。*

Azure Logic Apps を使用して、クラウド サービスとオンプレミス システムにまたがったアプリとデータを統合する、自動化されたスケーラブルなワークフローを構築します。 Azure Logic Apps には、多数の Azure サービス、Microsoft サービス、他のサービスとしてのソフトウェア (SaaS) 製品にまたがる多くのコネクタがあります。 Azure Logic Apps 内で IoT Central コネクタを使用すると、IoT Central 内で規則がトリガーされたときにワークフローをトリガーできます。 また、IoT Central コネクタ内のアクションを使用して、デバイスの作成、デバイスのプロパティと設定の更新、デバイスの削除を行うこともできます。

Microsoft Flow 内で IoT Central コネクタを使用できます。 Azure Logic Apps と Microsoft Flow はどちらも設計者優先の統合サービスですが、対象となる利用者が少し異なります。 Flow では、すべてのオフィス ワーカーが必要なビジネス ワークフローを構築できるように支援します。 Logic Apps では、データの接続に必要な統合を作成できるように IT プロフェッショナルを支援します。 Flow と Logic Apps の比較は[こちら](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs)をご覧ください。 Microsoft Flow で IoT Central コネクタを使用するワークフローを構築する方法は[こちら](howto-add-microsoft-flow.md)をご覧ください。

## <a name="prerequisites"></a>前提条件

- 従量課金制アプリケーション
- ロジック アプリを作成して管理する Azure アカウントおよびサブスクリプション

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>規則がトリガーされたときに、ワークフローをトリガーする

このセクションでは、規則がトリガーされたときに Microsoft Teams にメッセージを送信する方法を説明します。 イベントのイベント ハブへの送信、新しい Azure DevOps 作業項目の作成、SQL Server への新しい行の挿入などを他のコネクタを使用して実行するように、ワークフローを構成できます。

1. 最初に、[IoT Central 内で規則を作成](howto-create-telemetry-rules.md)します。 規則の条件を保存したら、新しいアクションとして **[Azure Logic Apps]** タイルを選択します。 **[Azure portal で作成する]** を選択します。 新しいロジック アプリを作成できる Azure portal が表示されます。 場合によっては Azure アカウントにサインインする必要があります。

1. 新しいロジック アプリを作成するために必要な情報を入力します。 新しいロジック アプリをプロビジョニングする Azure サブスクリプションを選択できます。 IoT Central アプリを作成したサブスクリプションと同じにする必要はありません。 **作成**を選択します。

    ![Azure portal でロジック アプリを作成する](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. ロジック アプリが正常に作成されたら、Logic Apps デザイナーに自動的に移動します。 **[空のロジック アプリ]** を選択します。 

    ![空のロジック アプリを作成する](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. デザイナーで "iot central" を検索して、**[When a rule is fired]\(規則が実行されるとき\)** トリガーを選択します。 IoT Central アプリにサインインしたときのアカウントでコネクタにサインインします。

    ![IoT Central コネクタにサインインする](./media/howto-build-azure-logic-apps/addtrigger.png)

1. サインインが成功したら、フィールドが表示されます。 ドロップダウンから **[アプリケーション]** と **[規則]** を選択します。

    ![アプリケーションと規則を選択する](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. 新しいアクションを追加します。 「**投稿 メッセージ チーム**」を検索し、Microsoft Teams コネクタから **[Post a message]\(メッセージを投稿\)** を選択します。 Microsoft Teams で使用するアカウントでコネクタにサインインします。

1. アクションで、**[チーム]** と **[チャネル]** を選択します。 **[メッセージ]** フィールドに、各メッセージの内容を入力します。 IoT Central 規則からの"*動的なコンテンツ*"を含め、デバイス名やタイムスタンプなどの重要な情報を通知に渡すことができます。
    > [!NOTE]
    > [動的なコンテンツ] ウィンドウ内の **[See more]** \(もっと見る\) のテキストを選択し、規則をトリガーした測定値とプロパティ値を取得します。

    ![動的なウィンドウを開いた状態でのロジック アプリ編集アクション](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. アクションの編集が完了したら、**[保存]** を選択します。

1. IoT Central アプリに戻ると、この規則の [Actions]\(アクション\) 区分の下に Azure Logic Apps アクションが表示されます。

ワークフローの作成は、Azure Portal 内の Logic Apps で IoT Central コネクタを使用して、いつでも始めることができます。 ワークフローの作成を開始したら、接続先の IoT Central アプリと規則を選択すると、同じように作動します。 毎回 IoT Central の規則のページから開始する必要はありません。

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>ワークフロー内でデバイスを作成、更新、および削除する

ロジック アプリにワークフローを構築するとき、IoT Central コネクタを使用してアクションを追加できます。 使用可能なアクションは、**[Create a device]\(デバイスを作成\)**, **[Update a device]\(デバイスを更新\)**、および **[Delete a device]\(デバイスを削除\)** です。

> [!NOTE]
> **[Update a device]\(デバイスを更新\)** と **[Delete a device]\(デバイスを削除\)** では、更新または削除する既存のデバイスの ID が必要です。 **Device Explorer** で IoT Central のデバイスの ID を取得できます。

![IoT Central の Device Explorer のデバイス ID](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>次の手順

ここでは、Microsoft Flow を使用して、ワークフローを作成する方法を学びました。推奨される次のステップは、[デバイスの管理](howto-manage-devices.md)です。
