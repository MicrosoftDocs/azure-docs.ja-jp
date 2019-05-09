---
title: Azure Security Center クイックスタート - Linux コンピューターでの Security Center の利用開始 | Microsoft Docs
description: このクイック スタートでは、Linux コンピューターで Security Center の利用を開始する方法を説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2018
ms.author: rkarlin
ms.openlocfilehash: 9f4e001909fb739aa368e5201649e85cce9906d3
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521922"
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>クイック スタート:Linux コンピューターでの Azure Security Center の利用開始
Azure サブスクリプションの利用を開始した後で、Linux エージェントをプロビジョニングして、オンプレミスや他のクラウドなど、Azure の外部で実行されている Linux リソースに対して Security Center を有効にできます。

このクイックスタートでは、Linux コンピューターに Linux エージェントをインストールする方法を示します。

## <a name="prerequisites"></a>前提条件
セキュリティ センターを使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

このクイックスタートを開始するには、Security Center の Standard 価格レベルを使用する必要があります。 アップグレード手順については、「[クイックスタート: Azure サブスクリプションでの Security Center Standard の利用開始](security-center-get-started.md)」をご覧ください。 Security Center の Standard は無料でお試しいただけます。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

## <a name="add-new-linux-computer"></a>新しい Linux コンピューターの追加

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/) にサインインします。
2. **[Microsoft Azure]** メニューの **[セキュリティ センター]** を選択します。 **[セキュリティ センター - 概要]** が開きます。

   ![Security Center の概要][2]

3. Security Center メイン メニューの **[使用の開始]** を選択します。
4. **[作業の開始]** タブを選択します。![[作業の開始]][3]

5. **[Azure 以外のコンピューターの新規追加]** の **[構成]** をクリックします。Log Analytics ワークスペースの一覧が表示されます。 該当する場合、一覧には、自動プロビジョニングが有効になっているときに Security Center によって作成された既定のワークスペースが含まれます。 このワークスペースまたは使用する別のワークスペースを選択します。

    ![Azure 以外のコンピューターの追加](./media/quick-onboard-linux-computer/non-azure.png)

6. **[直接エージェント]** ページの **[Linux 用エージェントのインストールとオンボード]** で、**[コピー]** を選択して *wget*コマンドをコピーします。

7. メモ帳を開き、このコマンドを貼り付けます。 このファイルを、Linux コンピューターからアクセスできる場所に保存します。

## <a name="install-the-agent"></a>エージェントをインストールする

1. Linux コンピューターで、前に保存したファイルを開きます。 コンテンツ全体を選択し、コピーしてターミナル コンソールを開き、コマンドを貼り付けます。
2. インストールの完了後、*pgrep* コマンドを実行して、*omsagent* がインストールされていることを検証できます。 次に示すように、コマンドから *omsagent* PID (プロセス ID) が返されます。

   ![エージェントをインストールする][5]

Linux 用の Security Center エージェントのログは */var/opt/microsoft/omsagent/\<ワークスペース ID>/log/* にあります

  ![エージェントのログ][6]

しばらくすると (最大 30 分かかる場合があります)、新しい Linux コンピューターが Security Center に表示されます。

これで、Azure VM と Azure 以外のコンピューターを 1 か所で監視できます。 **[計算]** に、すべての VM とコンピューターの概要が推奨事項と共に表示されます。 各列は、推奨事項の 1 つのセットを表します。 色は、その推奨事項の VM またはコンピューターの現在のセキュリティの状態を表します。 Security Center では、セキュリティの警告でのこれらのコンピューターの検出も明らかになります。

  ![[計算] ブレード][7] **[計算]** ブレードには 2 種類のアイコンが表示されます。

  ![アイコン 1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Azure 以外のコンピューター

  ![アイコン 2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になった場合、Linux コンピューターからエージェントを削除できます。

エージェントを削除するには:

1. Linux エージェントの[ユニバーサル スクリプト](https://github.com/Microsoft/OMS-Agent-for-Linux/releases)をコンピューターにダウンロードします。
2. コンピューター上で引数 *--purge* を指定して .sh バンドル ファイルを実行します。これにより、エージェントとその構成が完全に削除されます。

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>次の手順
このクイック スタートでは、Linux コンピューターでエージェントをプロビジョニングしました。 Security Center の使用方法について学習するには、セキュリティ ポリシーの構成とリソースのセキュリティの評価に関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル:セキュリティ ポリシーの定義と評価](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/get-started.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
