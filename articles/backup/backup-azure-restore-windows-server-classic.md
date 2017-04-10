---
title: "クラシック デプロイ モデルを使用した Azure からの Windows Server または Windows Client へのデータの復元 | Microsoft Docs"
description: "Windows Server または Windows クライアントからの復元方法について説明します。"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 85585dfc-c764-4e8c-8f0e-40b969640ac2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/31/2017
ms.author: saurse;trinadhk;markgal;
translationtype: Human Translation
ms.sourcegitcommit: be6bc58ab856309004904626db166331b29199a8
ms.openlocfilehash: aa8ccc15971bed76d7ce8fd554e6a0f89d985fb8
ms.lasthandoff: 02/02/2017


---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-the-classic-deployment-model"></a>クラシック デプロイメント モデルを使用した Windows Server または Windows Client コンピューターへのファイルの復元
> [!div class="op_single_selector"]
> * [クラシック ポータル](backup-azure-restore-windows-server-classic.md)
> * [Azure ポータル](backup-azure-restore-windows-server.md)
>
>

この記事では、バックアップ コンテナーからデータを復元する方法について説明します。 データを復元するには、Microsoft Azure Recovery Services (MARS) エージェントのデータの回復ウィザードを使用します。 データを復元するときには以下を行うことができます。

* バックアップが実行されたのと同じマシンにデータを復元する
* 別のコンピューターにデータを復元する

2017 年 1 月、マイクロソフトは MARS エージェントのプレビュー更新プログラムをリリースしました。 この更新プログラムではバグ修正の他に、インスタント リストアが実現し、書き込み可能な回復ポイントのスナップショットを回復ボリュームとしてマウントできるようになりました。 回復ボリュームを調べてから、ファイルをローカル コンピューターにコピーし、そこでファイルを選択して復元できます。

> [!NOTE]
> インスタント リストアを使用してデータを復元する場合は、[2017 年 1 月の Azure Backup 更新プログラム](https://support.microsoft.com/en-us/help/3216528?preview)が必要です。 また、バックアップ データは、サポート記事に掲載されているロケールの資格情報コンテナーで保護されている必要があります。 インスタント リストアをサポートするロケールの最新リストについては、[2017 年 1 月の Azure Backup 更新プログラム](https://support.microsoft.com/en-us/help/3216528?preview)をご覧ください。 現時点では、インスタント リストアを**使用できない**ロケールがあります。
>

インスタント リストアは、Azure Portal のRecovery Services コンテナーとクラシック ポータルのバックアップ コンテナーで使用できます。 インスタント リストアを使用する場合は、MARS 更新プログラムをダウンロードして、インスタント リストアを説明する手順に従います。


## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>インスタント リストアを使用して同じコンピューターにデータを回復する

ファイルを誤って削除してしまったため、バックアップを実行したのと同じコンピューターにそのファイルを復元するには、次の手順でデータを回復できます。

1. **Microsoft Azure Backup** スナップインを開きます。 スナップインがインストールされた場所がわからない場合は、コンピューターまたはサーバーで **Microsoft Azure Backup** を検索します。

    デスクトップ アプリが検索結果に表示されます。

2. **[データの回復]** をクリックして、ウィザードを開始します。

    ![データの回復](./media/backup-azure-restore-windows-server/recover.png)

3. 同じサーバーまたはコンピューターにデータを復元するには、**[作業の開始]** ウィンドウで、**[このサーバー] (`<server name>`)** を選択し、**[次へ]** をクリックします。

    ![[このサーバー] オプションを選択すると、データが同じマシンに復元されます](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. **[回復モードの選択]** ウィンドウで、**[個々のファイルおよびフォルダー]** を選択して、**[次へ]** をクリックします。

    ![Browse files](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. **[ボリュームと日付の選択]** ウィンドウで、復元するファイルやフォルダーが格納されているボリュームを選択します。

    カレンダーで回復ポイントを選択します。 任意の時点の回復ポイントから復元できます。 **太字**になっている日付では、少なくとも&1; つの回復ポイントを利用できます。 選択した日付で複数の回復ポイントを利用できる場合は、**[時間]** ドロップダウン メニューから、特定の回復ポイントを選択します。

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
    > [マウント解除] をクリックしない場合は、回復ボリュームはマウントされた時刻から&6; 時間、マウントされたままになります。 ボリュームのマウント中は、バックアップ操作が実行されません。 ボリュームのマウント中に実行されるようにスケジュール設定されているバックアップ操作は、回復ボリュームのマウント解除後に実行されます。
    >


## <a name="recover-data-to-the-same-machine"></a>同じコンピューターへのデータの回復
ファイルを誤って削除してしまったため、バックアップを実行したのと同じコンピューターにそのファイルを復元するには、次の手順でデータを回復できます。

1. **Microsoft Azure Backup** スナップインを開きます。
2. [ **データの回復** ] をクリックして、ワークフローを開始します。

    ![データの回復](./media/backup-azure-restore-windows-server-classic/recover.png)
3. バックアップ ファイルを同じコンピューターに復元する場合は、**[このサーバー (*コンピューター名*)]** オプションを選択します。

    ![Same machine](./media/backup-azure-restore-windows-server-classic/samemachine.png)
4. **[ファイルの参照]** または **[ファイルの検索]** を選択します。

    パスがわかっている&1; つまたは複数のファイルを復元する場合は、既定のオプションをそのまま使用します。 フォルダー構造がわからず、ファイルを検索する場合は、 **[ファイルの検索]** オプションを選択します。 ここでは、既定のオプションを使用します。

    ![Browse files](./media/backup-azure-restore-windows-server-classic/browseandsearch.png)
5. ファイルの復元元となるボリュームを選択します。

    任意の時点から復元できます。 カレンダー コントロールに **太字** で表示される日付は、復元ポイントが存在していることを示しています。 日付を選択すると、バックアップ スケジュールに基づいて (バックアップ操作が成功していれば)、 **[時間]** ドロップダウンから特定の時点を選択できます。

    ![Volume and Date](./media/backup-azure-restore-windows-server-classic/volanddate.png)
6. 回復する項目を選択します。 復元するフォルダーまたはファイルを複数選択することができます。

    ![Select files](./media/backup-azure-restore-windows-server-classic/selectfiles.png)
7. 回復パラメーターを指定します。

    ![Recovery options](./media/backup-azure-restore-windows-server-classic/recoveroptions.png)

   * 元の場所に復元することも (ファイルまたはフォルダーが上書きされます)、同じコンピューターの別の場所に復元することもできます。
   * 復元するファイルまたはフォルダーが復元先の場所に存在する場合、コピーを作成するか (同じファイルの&2; つのバージョンが作成されます)、復元先の場所にあるファイルを上書きするか、復元先に存在するファイルの回復をスキップすることができます。
   * 復元されるファイルの ACL を復元するという既定のオプションは、そのままにしておくことを強くお勧めします。
8. これらのうちいずれか&1; つを指定して、[ **次へ**] をクリックします。 このコンピューターにファイルを復元する回復ワークフローが開始します。

## <a name="recover-to-an-alternate-machine"></a>別のコンピューターへの回復
サーバー全体が失われた場合でも、 Azure Backup から別のコンピューターにデータを回復できます。 次の手順はそのワークフローを示しています。  

この手順で使用される用語は次のとおりです。

* *ソース コンピューター* – バックアップが実行され、現在は使用できなくなっている元のコンピューター。
* *ターゲット コンピューター* – データの回復先となるコンピューター。
* "*サンプルの資格情報コンテナー*" – "*ソース コンピューター*" と "*ターゲット コンピューター*" が登録されているバックアップ資格情報コンテナー。 <br/>

> [!NOTE]
> コンピューターのバックアップは、以前のバージョンのオペレーティング システムが実行されているコンピューターには復元できません。 たとえば、バックアップが Windows 7 コンピューターで行われた場合、Windows 8 以上のコンピューターにそのバックアップを復元できます。 ただし、その逆は当てはまりません。
>
>

1. **ターゲット コンピューター** の *Microsoft Azure Backup*スナップインを開きます。
2. "*ターゲット コンピューター*" および "*ソース コンピューター*" が同じバックアップ資格情報コンテナーに登録されていることを確認します。
3. [ **データの回復** ] をクリックして、ワークフローを開始します。

    ![データの回復](./media/backup-azure-restore-windows-server-classic/recover.png)
4. **[別のサーバー]**

    ![[別のサーバー]](./media/backup-azure-restore-windows-server-classic/anotherserver.png)
5. *サンプルの資格情報コンテナー*に対応するコンテナー資格情報ファイルを指定します。 コンテナー資格情報ファイルが無効である (または有効期限が切れている) 場合は、Azure クラシック ポータルの *サンプルの資格情報コンテナー* から、新しいコンテナー資格情報ファイルをダウンロードします。 資格情報コンテナーの資格情報ファイルを指定すると、その資格情報コンテナーの資格情報ファイルに対するバックアップ資格情報コンテナーが表示されます。
6. 表示されているコンピューターの一覧から *ソース コンピューター* を選択します。

    ![List of machines](./media/backup-azure-restore-windows-server-classic/machinelist.png)
7. **[ファイルの検索]** または **[ファイルの参照]** オプションのいずれかを選択します。 ここでは、 **[ファイルを検索]** オプションを使用します。

    ![Search](./media/backup-azure-restore-windows-server-classic/search.png)
8. 次の画面で、ボリュームと日付を選択します。 復元するフォルダー名とファイル名を検索します。

    ![Search items](./media/backup-azure-restore-windows-server-classic/searchitems.png)
9. ファイルの復元先にする場所を選択します。

    ![Restore location](./media/backup-azure-restore-windows-server-classic/restorelocation.png)
10. "*ソース コンピューター*" を "*サンプルの資格情報コンテナー*" に登録する際に指定した暗号化パスフレーズを入力します。

    ![暗号化](./media/backup-azure-restore-windows-server-classic/encryption.png)
11. 入力して [ **回復**] ボタンをクリックすると、指定した復元先でバックアップ ファイルの復元が開始します。

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>インスタント リストアを使用してデータを別のコンピューターに復元する
サーバー全体が失われた場合でも、 Azure Backup から別のコンピューターにデータを回復できます。 次の手順はそのワークフローを示しています。

この手順で使用される用語は次のとおりです。

* *ソース コンピューター* – バックアップが実行され、現在は使用できなくなっている元のコンピューター。
* *ターゲット コンピューター* – データの回復先となるコンピューター。
* "*サンプルのコンテナー*" – "*ソース コンピューター*" と "*ターゲット コンピューター*" が登録されている Recovery Services コンテナー。 <br/>

> [!NOTE]
> 古いバージョンのオペレーティング システムが実行されているコンピューターには、バックアップを復元できません。 たとえば、Windows 7 コンピューターのバックアップは、Windows 8 以降のコンピューターで復元できます。 Windows 8 コンピューターのバックアップを Windows 7 のコンピューターには復元することはできません。
>
>

1. **ターゲット コンピューター** の *Microsoft Azure Backup*スナップインを開きます。

2. "*ターゲット コンピューター*" と "*ソース コンピューター*" が同じ Recovery Services コンテナーに登録されていることを確認します。

3. **[データの回復]** をクリックして、**[データの回復ウィザード]** を開きます。

    ![データの回復](./media/backup-azure-restore-windows-server/recover.png)

4. **[作業の開始]** ウィンドウで、**[別のサーバー]** を選択します。

    ![[別のサーバー]](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. *サンプルの資格情報コンテナー*に対応するコンテナー資格情報ファイルを指定し、**[次へ]** をクリックします。

    コンテナー資格情報ファイルが無効である (または有効期限が切れている) 場合、Azure Portal の "*サンプルの資格情報コンテナー*" から、新しいコンテナー資格情報ファイルをダウンロードします。 有効なコンテナー資格情報を指定すると、対応する Backup Vault の名前が表示されます。

6. **[バックアップ サーバーの選択]** ウィンドウで、表示されているコンピューターの一覧から "*ソース コンピューター*" を選択し、パスフレーズを入力します。 その後、 **[次へ]**をクリックします。

    ![List of machines](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. **[回復モードの選択]** ウィンドウで、**[個々のファイルおよびフォルダー]** を選択して、**[次へ]** をクリックします。

    ![Search](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. **[ボリュームと日付の選択]** ウィンドウで、復元するファイルやフォルダーが格納されているボリュームを選択します。

    カレンダーで回復ポイントを選択します。 任意の時点の回復ポイントから復元できます。 **太字**になっている日付では、少なくとも&1; つの回復ポイントを利用できます。 選択した日付で複数の回復ポイントを利用できる場合は、**[時間]** ドロップダウン メニューから、特定の回復ポイントを選択します。

    ![Search items](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. **[マウント]** をクリックして、"*ターゲット マシン*" の回復ボリュームを回復ポイントとしてローカルでマウントします。

10. **[ファイルの参照と回復]** ウィンドウで、**[参照]** をクリックして Windows エクスプローラーを開き、ファイルとフォルダーを検索します。

    ![暗号化](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. Windows エクスプローラーで、回復ボリュームからファイルやフォルダーをコピーして、"*ターゲット コンピューター*" の保存先に貼り付けます。 または回復ボリュームから直接ファイルを開くかストリーミングし、正しいバージョンが回復されていることを確認します。

    ![暗号化](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. ファイルやフォルダーの復元が完了したら、**[ファイルの参照と回復]** ウィンドウで、**[マウント解除]** をクリックします。 ボリュームのマウントを解除するかどうかを確認するメッセージが表示されたら **[はい]** をクリックします。

    ![暗号化](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > [マウント解除] をクリックしない場合は、回復ボリュームはマウントされた時刻から&6; 時間、マウントされたままになります。 ボリュームのマウント中は、バックアップ操作が実行されません。 ボリュームのマウント中に実行されるようにスケジュール設定されているバックアップ操作は、回復ボリュームのマウント解除後に実行されます。
    >


## <a name="next-steps"></a>次のステップ
* [Azure Backup FAQ](backup-azure-backup-faq.md)
* [Azure Backup フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=290933)にアクセスします。

## <a name="learn-more"></a>詳細情報
* [Azure Backup の概要](http://go.microsoft.com/fwlink/p/?LinkId=222425)
* [Azure 仮想マシンのバックアップ](backup-azure-vms-introduction.md)
* [Microsoft ワークロードのバックアップ](backup-azure-dpm-introduction.md)

