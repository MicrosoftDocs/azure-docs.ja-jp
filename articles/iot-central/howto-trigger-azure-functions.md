---
title: Azure IoT Central で Webhook を使用して Azure Functions をトリガーする
description: Azure IoT Central でルールがトリガーされるたびに実行される関数アプリを作成します。
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 92d6f005018040e20c2df72dbc608a47bc8d9f08
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849016"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Azure IoT Central で Webhook を使用して Azure Functions をトリガーする

*このトピックはビルダーおよび管理者向けです。*

Azure Functions を使用して、IoT Central ルールからの Webhook 出力に対してサーバーレス コードを実行します。 Azure Functions を使用するために VM をプロビジョニングしたり、Web アプリを公開したりする必要はありません。代わりに、このコードをサーバーレスで実行することができます。 Azure Functions を使用して、Webhook ペイロードを SQL データベースや Event Grid などの最終的な送信先に送信する前に変換します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="how-to-connect-azure-functions"></a>Azure Functions を接続する方法

1. [Azure portal で新しい関数アプリを作成します](https://ms.portal.azure.com/#create/Microsoft.FunctionApp)。

    ![Azure portal で新しい関数アプリを作成する](media/howto-trigger-azure-functions/createfunction.png)

2. 関数アプリを展開し、[関数] の横にある **[+] ボタン**を選択します。 この関数が関数アプリの最初の関数である場合は、開発環境として **[ポータル内]** を選択し、 **[続行]** を選択します。

    ![関数アプリでカスタム関数を選択する](media/howto-trigger-azure-functions/customfunction.png)

3. **[webhook + API]** テンプレートを選択し、 **[作成]** を選択します。 このトピックでは、.NET ベースの Azure 関数を使用します。

    ![汎用 Webhook トリガーを選択する](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. 新しい関数で、 **[</> 関数の URL の取得]** を選択し、値をコピーして保存します。 この値を使用して、webhook を構成します。

    ![関数の URL を取得する](media/howto-trigger-azure-functions/getfunctionurl.png)

4. IoT Central で、関数アプリに接続するルールに移動します。

5. Webhook アクションを追加します。 **[表示名]** を入力し、以前にコピーした関数の URL を **[コールバック URL]** に貼り付けます。

    ![関数の URL をコールバック URL フィールドに入力する](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. ルールを保存します。 これで、ルールがトリガーされると、Webhook から関数アプリが呼び出され、実行されるようになります。 関数アプリで **[監視]** を選択すると、関数の呼び出し履歴が表示されます。 App Insights またはクラシック ビューを使用して履歴を表示することができます。

    ![関数の呼び出し履歴を監視する](media/howto-trigger-azure-functions/monitorfunction.PNG)

詳細については、[汎用 Webhook によってトリガーされる関数の作成](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function)に関する Azure Functions の記事を参照してください。

## <a name="next-steps"></a>次の手順
Webhook を設定して使用する方法がわかったら、[Microsoft Flow でワークフローを構築する方法](howto-add-microsoft-flow.md)について詳しく調べることをお勧めします。
