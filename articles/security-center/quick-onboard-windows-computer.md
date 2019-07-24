---
title: Azure Security Center クイックスタート - Windows コンピューターでの Security Center の利用開始 | Microsoft Docs
description: このクイック スタートでは、Windows コンピューターで Microsoft Monitoring Agent をプロビジョニングする方法を示します。
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
ms.date: 12/3/2018
ms.author: rkarlin
ms.openlocfilehash: bee4618ff08c89bbdab7413ca7f7f74a266d96dd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58077906"
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>クイック スタート:Windows コンピューターでの Azure Security Center の利用開始
Azure サブスクリプションの利用を開始した後で、Microsoft Monitoring Agent をプロビジョニングして、オンプレミスや他のクラウドなど、Azure の外部で実行されているリソースに対して Security Center を有効にできます。

このクイック スタートでは、Windows コンピューターに Microsoft Monitoring Agent をインストールする方法を示します。

## <a name="prerequisites"></a>前提条件
セキュリティ センターを使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

このクイックスタートを開始するには、Security Center の Standard 価格レベルを使用する必要があります。 アップグレード手順については、「[クイックスタート: Azure サブスクリプションでの Security Center Standard の利用開始](security-center-get-started.md)」をご覧ください。 Security Center の Standard は無料でお試しいただけます。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

## <a name="add-new-windows-computer"></a>新しい Windows コンピューターの追加

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/) にサインインします。
2. **[Microsoft Azure]** メニューの **[セキュリティ センター]** を選択します。 **[セキュリティ センター - 概要]** が開きます。

   ![Security Center の概要][2]

3. Security Center メイン メニューの **[使用の開始]** を選択します。
4. **[Get started] (使用の開始)** タブを選択します。

   ![作業開始][3]

5. **[Azure 以外のコンピューターの新規追加]** で **[構成]** をクリックします。 Log Analytics ワークスペースの一覧が表示されます。 該当する場合、一覧には、自動プロビジョニングが有効になっているときに Security Center によって作成された既定のワークスペースが含まれます。 このワークスペースまたは使用する別のワークスペースを選択します。

    ![Azure 以外のコンピューターの追加](./media/quick-onboard-windows-computer/non-azure.png)

   **[ダイレクト エージェント]** ブレードに、Windows エージェントをダウンロードするためのリンクと、エージェントの構成時に使用するワークスペース ID のキーが表示されます。

6. ご使用のコンピューターのプロセッサの種類に適用できる **[Windows エージェントのダウンロード]** リンクを選択してセットアップ ファイルをダウンロードします。

7. **[ワークスペース ID]** の右側で、コピー アイコンをク選択し、ID をメモ帳に貼り付けます。

8. **[主キー]** の右側で、コピー アイコンを選択し、キーをメモ帳に貼り付けます。

## <a name="install-the-agent"></a>エージェントをインストールする
ターゲット コンピューターに、ダウンロードしたファイルをインストールする必要があります。

1. ターゲット コンピューターにファイルをコピーし、セットアップを実行します。
2. **[ようこそ]** ページで **[次へ]** をクリックします。
3. **[ライセンス条項]** ページの記述内容を確認し、**[同意する]** を選択します。
4. **[インストール先フォルダー]** ページで、既定のインストール フォルダーを変更するか、そのまま使用して、**[次へ]** を選択します。
5. **[エージェントのセットアップ オプション]** ページで、エージェントを接続する Azure Log Analytics を選択し、**[次へ]** を選択します。
6. **[Azure Log Analytics]** ページで、前の手順でメモ帳にコピーしておいた**ワークスペース ID** と**ワークスペース キー (主キー)** を貼り付けます。
7. コンピューターが Azure Government クラウド内の Log Analytics ワークスペースにレポートする必要がある場合は、**[Azure クラウド]** ドロップダウン リストから **[Azure US Government]** を選択します。  コンピューターがプロキシ サーバーを介して Log Analytics サービスと通信する必要がある場合は、**[詳細]** を選択し、プロキシ サーバーの URL とポート番号を指定します。
8. 必要な構成設定が完了したら、**[次へ]** を選択します。

   ![エージェントをインストールする][5]

9. **[インストールの準備完了]** ページで、設定内容を確認し、**[インストール]** を選択します。
10. **[構成は正常に終了しました]** ページで **[完了]** を選択します

完了すると、**コントロール パネル**に **Microsoft Monitoring Agent** が表示されます。 構成を検証して、エージェントが接続されていることを確認できます。

エージェントのインストールと構成について詳しくは、[Windows コンピューターの接続](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)に関するページをご覧ください。

これで、Azure VM と Azure 以外のコンピューターを 1 か所で監視できます。 **[計算]** に、すべての VM とコンピューターの概要が推奨事項と共に表示されます。 各列は、推奨事項の 1 つのセットを表します。 色は、その推奨事項の VM またはコンピューターの現在のセキュリティの状態を表します。 Security Center では、セキュリティの警告でのこれらのコンピューターの検出も明らかになります。

  ![[コンピューティング] ブレード][6]

**[計算]** ブレードには 2 種類のアイコンが表示されます。

![アイコン 1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Azure 以外のコンピューター

![アイコン 2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になった場合、Windows コンピューターからエージェントを削除できます。

エージェントを削除するには:

1. **[コントロール パネル]** を開きます。
2. **[プログラムと機能]** を開きます。
3. **[プログラムと機能]** で、**[Microsoft Monitoring Agent]** をクリックし、**[アンインストール]** をクリックします。

## <a name="next-steps"></a>次の手順
このクイック スタートでは、Windows コンピューターで Microsoft Monitoring Agent をプロビジョニングしました。 Security Center の使用方法について学習するには、セキュリティ ポリシーの構成とリソースのセキュリティの評価に関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル:セキュリティ ポリシーの定義と評価](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
