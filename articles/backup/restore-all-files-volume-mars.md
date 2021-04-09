---
title: MARS を使用してボリューム内のすべてのファイルを復元する
description: MARS エージェントを使用して、ボリューム内のすべてのファイルを復元する方法について説明します。
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 44c12809fc94f78721ab1788cb352076dfebabe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98612887"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>MARS エージェントを使用してボリューム内のすべてのファイルを復元する

この記事では、Microsoft Azure Recovery Services (MARS) エージェントのデータの回復ウィザードを使用して、ボリューム全体に含まれるすべてのバックアップ ファイルを復元する方法について説明します。 次のようにすることができます。

- ボリューム内のすべてのバックアップ ファイルを、バックアップが作成されたのと同じコンピューターに復元する。
- ボリューム内のすべてのバックアップ ファイルを、別のコンピューターに復元する。

>[!TIP]
>**[ボリューム]** オプションでは、指定されたボリューム内のすべてのバックアップ済みデータを復旧します。 このオプションでは、より高速な転送速度 (最大 40 MBps) が提供され、大きなサイズのデータやボリューム全体の復旧に推奨されます。
>
>**[個々のファイルおよびフォルダー] オプション** を使用すると、復旧ポイントのデータにすばやくアクセスできます。 個別のファイルの復旧に適しており、合計サイズは 80 GB 未満であることをお勧めします。 復旧中の転送またはコピーの速度は最大 6 MBps です。

## <a name="volume-level-restore-to-the-same-machine"></a>同じコンピューターへのボリューム レベルの復元

次の手順では、ボリューム内のすべてのバックアップ ファイルを復旧します。

1. **Microsoft Azure Backup** スナップインを開きます。 スナップインがインストールされている場所がわからない場合は、コンピューターまたはサーバーで **Microsoft Azure Backup** を検索します。 デスクトップ アプリが検索結果に表示されます。

1. **[データの回復]** を選択してウィザードを開始します。

    ![[データの回復] メニュー](./media/restore-all-files-volume-mars/recover.png)

1. **[使用の開始]** ページで、データを同じサーバーまたはコンピューターに復元するには、 **[このサーバー (サーバー名)]**  >  **[次へ]** を選択します。

    ![[作業の開始] ページ](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. **[回復モードの選択]** ページで、 **[ボリューム]**  >  **[次へ]** を選択します。

    ![回復モードの選択](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. **[ボリュームと日付の選択]** ページで、復元するボリュームを選択します。

    カレンダーで回復ポイントを選択します。 **太字** になっている日付では、少なくとも 1 つの回復ポイントを利用できます。 1 日の中で複数の復旧ポイントを使用できる場合は、 **[時間]** ドロップダウン メニューから特定の復旧ポイントを選択します。

     ![ボリュームと日付を選択する](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. **[回復オプションの指定]** ページで、復元動作を構成します。
    1. 回復先を選択します。
        - **[元の場所]** :データを元のパスに復元します。
        - **[別の場所]** :別の場所を指定してそこにデータを復元します。
    1. **[バックアップ内の項目が回復先に既にある場合]** の動作を指定します。
        - **[Create copies so that you have both versions]\(コピーを作成して両方のバージョンを保持する\)** :同じ名前のファイルが既に存在する場合、回復ポイントのデータがコピーとして復元されます。 コピーに付けられるローカライズしたファイル名のプレフィックスには、次のいずれかの形式のローカル復元ジョブ時刻が使用されます。
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **[回復したバージョンで既存のバージョンを上書きする]** :同じ名前のファイルが既に存在する場合、その内容は回復ポイントのデータで置き換えられます。
        - **[回復先に既に存在する項目は復旧しない]** :同じ名前のファイルが既に存在する場合は、スキップされます。
    1. ファイルを回復ポイントの元の権限で復元する必要がある場合は、 **[Enable Restore access control list (ACL) permissions to the file or folder being recovered]\(回復するファイルまたはフォルダーに対し、アクセス制御リスト (ACL) のアクセス許可を復元する\)** を有効にします。
        ![回復オプションの指定](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. **[確認]** ペインで回復の詳細を確認し、 **[回復]** を選択します。

    ![確認の詳細](./media/restore-all-files-volume-mars/confirmation-details.png)

1. 「**回復の進行状況**」ページで、回復ジョブの進行状況を監視します。 ウィザードは安全に閉じることもでき、回復操作はバックグラウンドで続行されます。 進行状況をもう一度表示するには、ダッシュボードで回復ジョブをダブルクリックします。

## <a name="volume-level-restore-to-an-alternate-machine"></a>別のコンピューターへのボリューム レベルの復元

次の手順では、ボリューム内のすべてのバックアップ ファイルを別のコンピューターに回復します。 この手順は、サーバー全体が失われた場合の Azure Backup からのデータの回復に使用できます。

これらの手順には、次の用語が含まれています。

- *ソース コンピューター* – バックアップが取得され、現在は使用できない元のコンピューター。
- *ターゲット コンピューター* – データの回復先となるコンピューター。
- *サンプルのコンテナー* – ソース コンピューターとターゲット コンピューターが登録されている Recovery Services コンテナー。

> [!NOTE]
> バックアップを、以前のバージョンのオペレーティング システムを実行しているターゲット コンピューターに復元することはできません。 たとえば、Windows 7 コンピューターから取得されたバックアップは Windows 7 (以降の) コンピューターで復元できます。 Windows 10 コンピューターから取得されたバックアップは、Windows 7 コンピューターに復元できません。

1. ターゲット コンピューターで **Microsoft Azure Backup** スナップインを開きます。

1. ターゲット コンピューターとソース コンピューターが同じ Recovery Services コンテナーに登録されていることを確認します。

1. **[データの回復]** を選択して **[データの回復ウィザード]** を開きます。

    ![[データの回復] が強調表示された Azure Backup のスクリーンショット (別のコンピューターに復元)](./media/backup-azure-restore-windows-server/recover.png)

1. **[使用の開始]** ページで、 **[別のサーバー]** を選択します。

    ![データの回復ウィザードの [使用の開始] ページのスクリーンショット (別のコンピューターに復元)](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. サンプルのコンテナーに対応するコンテナー資格情報ファイルを指定し、 **[次へ]** を選択します。

    コンテナー資格情報ファイルが無効である (または期限が切れている) 場合は、Azure portal で[サンプルのコンテナーから新しいコンテナー資格情報ファイルをダウンロードします](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file)。 有効なコンテナー資格情報を指定すると、対応するバックアップ コンテナーの名前が表示されます。

1. **[バックアップ サーバーの選択]** ページで、表示されているコンピューターの一覧からソース コンピューターを選択し、パスフレーズを指定します。 **[次へ]** を選択します。

    ![データの回復ウィザードの [バックアップ サーバーの選択] ページのスクリーンショット (別のコンピューターに復元)](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. **[回復モードの選択]** ページで、 **[ボリューム]**  >  **[次へ]** を選択します。

    ![回復モードの選択](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. **[ボリュームと日付の選択]** ページで、復元するボリュームを選択します。

    カレンダーで回復ポイントを選択します。 **太字** になっている日付では、少なくとも 1 つの回復ポイントを利用できます。 1 日の中で複数の復旧ポイントを使用できる場合は、 **[時間]** ドロップダウン メニューから特定の復旧ポイントを選択します。

     ![ボリュームと日付を選択する](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. **[回復オプションの指定]** ページで、復元動作を構成します。
    1. 回復先を選択します。
        - **[元の場所]** :データを元のパスに復元します。
        - **[別の場所]** :別の場所を指定してそこにデータを復元します。
    1. **[バックアップ内の項目が回復先に既にある場合]** の動作を指定します。
        - **[Create copies so that you have both versions]\(コピーを作成して両方のバージョンを保持する\)** :同じ名前のファイルが既に存在する場合、回復ポイントのデータがコピーとして復元されます。 コピーに付けられるローカライズしたファイル名のプレフィックスには、次のいずれかの形式のローカル復元ジョブ時刻が使用されます。
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **[回復したバージョンで既存のバージョンを上書きする]** :同じ名前のファイルが既に存在する場合、その内容は回復ポイントのデータで置き換えられます。
        - **[回復先に既に存在する項目は復旧しない]** :同じ名前のファイルが既に存在する場合は、スキップされます。
    1. ファイルを回復ポイントの元の権限で復元する必要がある場合は、 **[Enable Restore access control list (ACL) permissions to the file or folder being recovered]\(回復するファイルまたはフォルダーに対し、アクセス制御リスト (ACL) のアクセス許可を復元する\)** を有効にします。
        ![回復オプションの指定](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. **[確認]** ペインで回復の詳細を確認し、 **[回復]** を選択します。

    ![確認の詳細](./media/restore-all-files-volume-mars/confirmation-details.png)

1. 「**回復の進行状況**」ページで、回復ジョブの進行状況を監視します。 ウィザードは安全に閉じることもでき、回復操作はバックグラウンドで続行されます。 進行状況をもう一度表示するには、ダッシュボードで回復ジョブをダブルクリックします。

## <a name="next-steps"></a>次のステップ

- ファイルとフォルダーを回復したので、 [バックアップを管理](backup-azure-manage-windows-server.md)できます。
- [ファイルとフォルダーのバックアップに関する一般的な質問](backup-azure-file-folder-backup-faq.md)を確認する。
