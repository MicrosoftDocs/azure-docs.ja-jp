---
title: "Windows Server または Windows コンピューターへの Azure のデータの復元 | Microsoft Docs"
description: "Azure に格納されているデータを Windows Server または Windows コンピューターに復元する方法について説明します。"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 742f4b9e-c0ab-4eeb-8e22-ee29b83c22c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/16/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: 231dd61f95267b3a504ed70e9b3a5abc470b69b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Resource Manager デプロイメント モデルを使用した Windows Server または Windows Client コンピューターへのファイルの復元
> [!div class="op_single_selector"]
> * [Azure ポータル](backup-azure-restore-windows-server.md)
> * [クラシック ポータル](backup-azure-restore-windows-server-classic.md)
>
>

この記事では、バックアップ資格情報コンテナーからデータを復元する方法について説明します。 データを復元するには、Microsoft Azure Recovery Services (MARS) エージェントのデータの回復ウィザードを使用します。 データを復元するときには以下を行うことができます。

* バックアップが実行されたのと同じマシンにデータを復元する
* 別のコンピューターにデータを復元する

2017 年 1 月、マイクロソフトは MARS エージェントのプレビュー更新プログラムをリリースしました。 この更新プログラムではバグ修正の他に、インスタント リストアが実現し、書き込み可能な回復ポイントのスナップショットを回復ボリュームとしてマウントできるようになりました。 回復ボリュームを調べてから、ファイルをローカル コンピューターにコピーし、そこでファイルを選択して復元できます。

> [!NOTE]
> インスタント リストアを使用してデータを復元する場合は、[2017 年 1 月の Azure Backup 更新プログラム](https://support.microsoft.com/en-us/help/3216528?preview)が必要です。 また、バックアップ データは、サポート記事に掲載されているロケールの資格情報コンテナーで保護されている必要があります。 インスタント リストアをサポートするロケールの最新リストについては、[2017 年 1 月の Azure Backup 更新プログラム](https://support.microsoft.com/en-us/help/3216528?preview)をご覧ください。 現時点では、インスタント リストアを**使用できない**ロケールがあります。
>

インスタント リストアは、Azure Portal のRecovery Services コンテナーとクラシック ポータルのバックアップ コンテナーで使用できます。 インスタント リストアを使用する場合は、MARS 更新プログラムをダウンロードして、インスタント リストアを説明する手順に従います。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>インスタント リストアを使用して同じコンピューターにデータを回復する

ファイルを誤って削除してしまったため、バックアップを実行したのと同じコンピューターにそのファイルを復元するには、次の手順でデータを回復できます。

1. **Microsoft Azure Backup** スナップインを開きます。 スナップインがインストールされた場所がわからない場合は、コンピューターまたはサーバーで **Microsoft Azure Backup** を検索します。

    デスクトップ アプリが検索結果に表示されます。

2. **[データの回復]** をクリックして、ウィザードを開始します。

    ![データの回復](./media/backup-azure-restore-windows-server/recover.png)

3. 同じサーバーまたはコンピューターにデータを復元するには、**[作業の開始]** ウィンドウで、**[このサーバー] \ (`<server name>`)** を選択し、**[次へ]** をクリックします。

    ![[このサーバー] オプションを選択すると、データが同じマシンに復元されます](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. **[回復モードの選択]** ウィンドウで、**[個々のファイルおよびフォルダー]** を選択して、**[次へ]** をクリックします。

    ![Browse files](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. **[ボリュームと日付の選択]** ウィンドウで、復元するファイルやフォルダーが格納されているボリュームを選択します。

    カレンダーで回復ポイントを選択します。 任意の時点の回復ポイントから復元できます。 **太字**になっている日付では、少なくとも 1 つの回復ポイントを利用できます。 選択した日付で複数の回復ポイントを利用できる場合は、**[時間]** ドロップダウン メニューから、特定の回復ポイントを選択します。

    ![Volume and Date](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. 復元する回復ポイントを選択したら、**[マウント]** をクリックします。

    Azure Backup がローカルの回復ポイントをマウントし、回復ボリュームとして使用します。

7. **[ファイルの参照と回復]** ウィンドウで、**[参照]** をクリックして Windows エクスプローラーを開き、ファイルとフォルダーを検索します。

    ![Recovery options](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. Windows エクスプローラーで、復元するファイルやフォルダーをコピーして、サーバーまたはコンピューターの任意のローカルの場所に貼り付けます。 または回復ボリュームから直接ファイルを開くかストリーミングし、正しいバージョンが回復されていることを確認します。

    ![マウントされたボリュームからファイルとフォルダーをコピーしてローカルの保存先に貼り付ける](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. ファイルやフォルダーの復元が完了したら、**[ファイルの参照と回復]** ウィンドウで、**[マウント解除]** をクリックします。 ボリュームのマウントを解除するかどうかを確認するメッセージが表示されたら **[はい]** をクリックします。

    ![ボリュームのマウント解除と確認](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > [マウント解除] をクリックしない場合、回復ボリュームはマウントされた時刻から 6 時間マウントされたままになります。 ただし、マウント時間は、ファイル コピーが実行中の場合は最大 24 時間まで延長されます。 ボリュームのマウント中は、バックアップ操作が実行されません。 ボリュームのマウント中に実行されるようにスケジュール設定されているバックアップ操作は、回復ボリュームのマウント解除後に実行されます。
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>インスタント リストアを使用してデータを別のコンピューターに復元する
サーバー全体が失われた場合でも、Azure Backup から別のコンピューターにデータを回復できます。 次の手順はそのワークフローを示しています。


この手順で使用される用語は次のとおりです。

* *ソース コンピューター* – バックアップが実行され、現在は使用できなくなっている元のコンピューター。
* *ターゲット コンピューター* – データの回復先となるコンピューター。
* "*サンプルのコンテナー*" – "*ソース コンピューター*" と "*ターゲット コンピューター*" が登録されている Recovery Services コンテナー。 <br/>

> [!NOTE]
> 古いバージョンのオペレーティング システムが実行されているコンピューターには、バックアップを復元できません。 たとえば、Windows 7 コンピューターのバックアップは、Windows 8 以降のコンピューターで復元できます。 Windows 8 コンピューターのバックアップを Windows 7 のコンピューターには復元することはできません。
>
>

1. **ターゲット コンピューター** の *Microsoft Azure Backup*スナップインを開きます。

2. *ターゲット コンピューター*と*ソース コンピューター*が同じ Recovery Services コンテナーに登録されていることを確認します。

3. **[データの回復]** をクリックして、**[データの回復ウィザード]** を開きます。

    ![データの回復](./media/backup-azure-restore-windows-server/recover.png)

4. **[作業の開始]** ウィンドウで、**[別のサーバー]** を選択します。

    ![[別のサーバー]](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. *サンプルの資格情報コンテナー*に対応するコンテナー資格情報ファイルを指定し、**[次へ]** をクリックします。

    コンテナー資格情報ファイルが無効である (または有効期限が切れている) 場合、Azure Portal の *サンプルの資格情報コンテナー*から、新しいコンテナー資格情報ファイルをダウンロードします。 有効なコンテナー資格情報を指定すると、対応する Backup Vault の名前が表示されます。


6. **[バックアップ サーバーの選択]** ウィンドウで、表示されているコンピューターの一覧から*ソース コンピューター*を選択し、パスフレーズを入力します。 その後、 **[次へ]**をクリックします。

    ![List of machines](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. **[回復モードの選択]** ウィンドウで、**[個々のファイルおよびフォルダー]** を選択して、**[次へ]** をクリックします。

    ![Search](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. **[ボリュームと日付の選択]** ウィンドウで、復元するファイルやフォルダーが格納されているボリュームを選択します。

    カレンダーで回復ポイントを選択します。 任意の時点の回復ポイントから復元できます。 **太字**になっている日付では、少なくとも 1 つの回復ポイントを利用できます。 選択した日付で複数の回復ポイントを利用できる場合は、**[時間]** ドロップダウン メニューから、特定の回復ポイントを選択します。

    ![Search items](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. **[マウント]** をクリックして、*ターゲット マシン*の回復ボリュームを回復ポイントとしてローカルでマウントします。

10. **[ファイルの参照と回復]** ウィンドウで、**[参照]** をクリックして Windows エクスプローラーを開き、ファイルとフォルダーを検索します。

    ![Encryption](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. Windows エクスプローラーで、回復ボリュームからファイルやフォルダーをコピーして、*ターゲット コンピューター*の保存先に貼り付けます。 または回復ボリュームから直接ファイルを開くかストリーミングし、正しいバージョンが回復されていることを確認します。

    ![Encryption](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. ファイルやフォルダーの復元が完了したら、**[ファイルの参照と回復]** ウィンドウで、**[マウント解除]** をクリックします。 ボリュームのマウントを解除するかどうかを確認するメッセージが表示されたら **[はい]** をクリックします。

    ![暗号化](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > [マウント解除] をクリックしない場合、回復ボリュームはマウントされた時刻から 6 時間マウントされたままになります。 ただし、マウント時間は、ファイル コピーが実行中の場合は最大 24 時間まで延長されます。 ボリュームのマウント中は、バックアップ操作が実行されません。 ボリュームのマウント中に実行されるようにスケジュール設定されているバックアップ操作は、回復ボリュームのマウント解除後に実行されます。
    >

## <a name="troubleshooting"></a>トラブルシューティング
Azure Backup が、**[マウント]** のクリック後数分経過しても回復ボリュームを正常にマウントしない場合、または 1 つ以上のエラーによって回復ボリュームをマウントできない場合、回復を正常に開始するには、以下の手順に従ってください。

1.  進行中のマウント プロセスが数分間実行されている場合はキャンセルします。

2.  最新バージョンの Azure Backup エージェントを使用していることを確認します。 Azure Backup エージェントのバージョン情報を調べるには、Microsoft Azure Backup コンソールの **[アクション]** ウィンドウにある **[About Microsoft Azure Recovery Services Agent]\(Microsoft Azure Recovery Services エージェントについて\)** をクリックし、**バージョン**番号が[この記事](https://go.microsoft.com/fwlink/?linkid=229525)に示されているバージョン以上であることを確認します。 最新バージョンは [こちら](https://go.microsoft.com/fwLink/?LinkID=288905)からダウンロードできます。

3.  **[デバイス マネージャー]** -> **[ストレージ コントローラー]** に移動し、**Microsoft iSCSI イニシエーター**が見つかることを確認します。 見つかった場合は、下の手順 7. に進みます。 

4.  手順 3. で説明したように Microsoft iSCSI イニシエーター サービスが見つからない場合は、**[デバイス マネージャー]** -> **[ストレージ コントローラー]** の下に、**[不明なデバイス]** という名前の、ハードウェア ID **ROOT\ISCSIPRT** を持つエントリがあるかどうかを確認します。

5.  **[不明なデバイス]** を右クリックし、**[ドライバー ソフトウェアの更新]** を選択します。

6.  **[自動的に更新されたドライバ ソフトウェアを検索します]** を選択して、ドライバーを更新します。 更新が完了すると、下に示すように、**[不明なデバイス]** が **[Microsoft iSCSI イニシエーター]** に変わります。 

    ![暗号化](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  **[タスク マネージャー]** -> **[サービス (ローカル)]** -> **[Microsoft iSCSI イニシエーター サービス]** に移動します。 

    ![暗号化](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  Microsoft iSCSI イニシエーター サービスを再起動するには、サービスを右クリックして **[停止]** をクリックし、もう一度右クリックして **[開始]** をクリックします。

9.  インスタント リストアを使用して回復を再試行します。 

それでも回復が失敗する場合は、サーバーまたはクライアントを再起動します。 再起動が望ましくない場合、またはサーバーの再起動後も回復が失敗する場合は、別のコンピューターから回復を試してください。[Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) に移動し、サポート要求を送信することにより、Azure サポートにお問い合わせください。

## <a name="next-steps"></a>次のステップ
* ファイルとフォルダーを回復したので、 [バックアップを管理](backup-azure-manage-windows-server.md)できます。
