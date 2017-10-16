---
title: "複数の Azure 仮想マシンの更新を管理する | Microsoft Docs"
description: "Azure 仮想マシンを更新の対象にします。"
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.openlocfilehash: 89bf87f27fdf276068cba261fc6ae1660307e0b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>複数の Azure 仮想マシンの更新を管理する

更新管理では、Azure 仮想マシンの更新プログラムと修正プログラムを管理できます。
[Azure Automation](automation-offering-get-started.md) アカウントから、仮想マシンをすばやく更新の対象に追加し、利用可能な更新プログラムの状態を評価し、必要な更新プログラムのインストール スケジュールを設定し、デプロイの結果から更新管理が有効になっているすべての仮想マシンに更新プログラムが正常に適用されたことを確認できます。

## <a name="prerequisites"></a>前提条件

このガイドの手順を完了するには、次のものが必要です。

* Azure Automation アカウント。 Azure Automation 実行アカウントの作成手順については、 [Azure 実行アカウント](automation-sec-configure-azure-runas-account.md)に関するページをご覧ください。
* Azure Resource Manager 仮想マシン (クラシックではありません)。 VM の作成手順については、「 [Azure ポータルで初めての Windows 仮想マシンを作成する](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="enable-update-management-for-azure-virtual-machines"></a>Azure 仮想マシンの更新管理を有効にする

1. Azure Portal で Automation アカウントを開きます。
2. 画面左側の **[更新管理]** を選択します。
3. 画面上部の **[Azure VM の追加]** をクリックします。
    ![VM の追加](./media/manage-update-multi/update-onboard-vm.png)
4. 追加する仮想マシンを選択します。 **[更新管理の有効化]** 画面が開きます。
5. **[有効]**をクリックします。

   ![更新管理を有効にする](./media/manage-update-multi/update-enable.png)

仮想マシンに対する更新管理が有効になります。

## <a name="view-update-assessment"></a>更新の評価を表示する

**更新管理**が有効になると、**[更新管理]** 画面が表示されます。 **[不足している更新プログラム]** タブに、デプロイされていない更新プログラムの一覧が表示されます。

## <a name="schedule-an-update-deployment"></a>更新プログラムのデプロイをスケジュールする

更新プログラムをインストールするには、リリース スケジュールとサービス期間に従ってデプロイをスケジュールします。
デプロイに含める更新の種類を選択できます。 たとえば、緊急更新プログラムやセキュリティ更新プログラムを追加し、更新プログラムのロールアップを除外できます。

**[更新管理]** 画面の上部にある **[更新プログラムの展開のスケジュール]** をクリックして、1 台以上の仮想マシンの新しい更新プログラムのデプロイをスケジュールします。 **[新しい更新プログラムの展開]** 画面で、次の情報を指定します。

* **名前**: 更新プログラムのデプロイを識別する一意の名前を指定します。
* **OS の種類**: [Windows] または [Linux] を選択します。
* **更新するコンピューター**: 更新する仮想マシンを選択します。

  ![更新する仮想マシンを選択する](./media/manage-update-multi/update-select-computers.png)

* **更新プログラムの分類**: 更新プログラムのデプロイによってデプロイに追加されるソフトウェアの種類を選択します。 分類の種類は次のとおりです。
  * 緊急更新プログラム
  * セキュリティ更新プログラム
  * 更新プログラムのロールアップ
  * Feature Pack
  * Service Pack
  * 定義の更新
  * ツール
  * 更新プログラム
* **スケジュール設定**: 既定の日時 (現在の時刻から 30 分後) を使用するか、別の日時を指定します。
   デプロイを 1 回行うか、定期的なスケジュールを設定するかを指定することもできます。 定期的なスケジュールを設定するには、[繰り返し] の下の [繰り返し] オプションをクリックします。

   ![更新プログラムのスケジュール設定画面](./media/manage-update-multi/update-set-schedule.png)

* **メンテナンス期間 (分)**: 更新プログラムをデプロイする期間を指定します。  これにより、定義したサービス期間内に変更が確実に実行されます。

スケジュールの構成が完了したら、**[作成]** ボタンをクリックして、状態ダッシュボードに戻ります。
**[スケジュール済み]** の表に、作成したデプロイ スケジュールが表示されていることを確認してください。

> [!WARNING]
> 再起動が必要な更新では、仮想マシンは自動的に再起動されます。

## <a name="view-results-of-an-update-deployment"></a>更新プログラムのデプロイの結果を表示する

スケジュールされたデプロイの開始後、**[更新管理]** 画面の **[更新プログラムの展開]** タブに、デプロイの状態が表示されます。
実行中の場合、状態は **[処理中]** と表示されます。 正常に完了すると、状態は **[成功]** に変わります。
デプロイ時に 1 つ以上の更新プログラムでエラーが発生した場合、状態は **[部分的に失敗]** になります。

![更新プログラムのデプロイの状態 ](./media/manage-update-multi/update-view-results.png)

完了した更新プログラムのデプロイをクリックすると、その更新プログラムのデプロイ用のダッシュボードが表示されます。

**[更新プログラムを実行した結果]** タイルに表示されるのは、仮想マシンに対する更新プログラムの合計数とデプロイ結果の概要です。
右側の表に、各更新プログラムとインストールの結果の詳細が示されます。これは、次の値のいずれかになります。

* 試行されていません: メンテナンス期間として定義した時間が十分ではなかったため、更新プログラムがインストールされませんでした。
* 成功: 更新できました。
* 失敗: 更新できませんでした。

デプロイで作成されたすべてのログ エントリを確認するには、**[すべてのログ]** をクリックします。

ターゲットの仮想マシンでの更新プログラムのデプロイを管理する Runbook のジョブ ストリームを確認するには、**[出力]** タイルをクリックします。

デプロイで発生したエラーの詳細情報を確認するには、**[エラー]** をクリックします。

## <a name="next-steps"></a>次のステップ

* 更新管理の詳細については、[更新管理](../operations-management-suite/oms-solution-update-management.md)に関する記事を参照してください。