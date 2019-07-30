---
title: System Center 2012 R2 DPM を使用して Exchange サーバーを Azure Backup にバックアップする
description: System Center 2012 R2 DPM を使用して Exchange サーバーを Azure Backup にバックアップする方法について説明する
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: kasinh
ms.openlocfilehash: 2d3670e2120e7c203e40d39ba9d82537da877ee5
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466742"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>System Center 2012 R2 DPM を使用して Exchange サーバーを Azure Backup にバックアップする
この記事では、Microsoft Exchange Server を Azure Backup にバックアップするために System Center 2012 R2 Data Protection Manager (DPM) サーバーを構成する方法を説明します。  

## <a name="updates"></a>更新プログラム
Azure Backup に DPM サーバーを正常に登録するには、System Center 2012 R2 DPM 用の最新の更新プログラムのロールアップと、Azure Backup エージェントの最新バージョンをインストールする必要があります。 [Microsoft カタログ](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager)から最新の更新プログラムのロールアップを取得します。

> [!NOTE]
> この記事の例では、Azure Backup エージェントのバージョン 2.0.8719.0 と、System Center 2012 R2 DPM に対する更新プログラムのロールアップ 6 がインストールされています。
>
>

## <a name="prerequisites"></a>前提条件
先に進む前に、Microsoft Azure Backup を使用してワークロードを保護する上で必要なすべての [前提条件](backup-azure-dpm-introduction.md#prerequisites-and-limitations) が満たされていることを確認します。 該当する前提条件を以下に示します。

* Azure サイトに対するバックアップ コンテナーが作成済みである。
* エージェントとコンテナーの資格情報が DPM サーバーにダウンロード済みである。
* DPM サーバーにエージェントがインストールされている。
* コンテナーの資格情報を使用して DPM サーバーを登録済みである。
* Exchange 2016 を保護する場合は、DPM 2012 R2 UR9 以降にアップグレードしてください。

## <a name="dpm-protection-agent"></a>DPM 保護エージェント
Exchange サーバーに DPM 保護エージェントをインストールするには、次の手順に従います。

1. ファイアウォールが正しく構成されていることを確認します。 「 [エージェントに対するファイアウォール例外の構成](https://technet.microsoft.com/library/Hh758204.aspx)」を参照してください。
2. DPM 管理者コンソールで **[管理]、[エージェント]、[インストール]** の順にクリックし、Exchange サーバーにエージェントをインストールします。 詳細な手順については、「 [DPM 保護エージェントのインストール](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) 」を参照してください。

## <a name="create-a-protection-group-for-the-exchange-server"></a>Exchange サーバーの保護グループを作成する
1. DPM 管理者コンソールで **[保護]** をクリックし、次にツール リボンの **[新規]** をクリックして **[新しい保護グループの作成]** ウィザードを開きます。
2. ウィザードの **[ようこそ]** 画面で **[次へ]** をクリックします。
3. **[保護グループの種類の選択]** 画面で、 **[サーバー]** を選択し、 **[次へ]** をクリックします。
4. 保護する Exchange サーバー データベースを選択し、 **[次へ]** をクリックします。

   > [!NOTE]
   > Exchange 2013 を保護する場合は、「 [Exchange 2013 の前提条件](https://technet.microsoft.com/library/dn751029.aspx)」を確認してください。
   >
   >

    次の例では、Exchange 2010 データベースが選択されています。

    ![グループ メンバーの選択](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. データ保護方法を選択します。

    保護グループに名前を付けて、次のオプションの両方を選択します。

   * ディスクを使用した短期的な保護を利用する
   * オンライン保護を利用する
6. **[次へ]** をクリックします。
7. Exchange Server データベースの整合性を確認する場合は、 **[Eseutil を実行してデータの整合性をチェックする]** オプションを選択します。

    このオプションを選択すると、Exchange サーバー上で **eseutil** コマンドを実行したときに I/O トラフィックが生成されるのを防ぐために、DPM サーバー上でバックアップの整合性チェックが実行されます。

   > [!NOTE]
   > このオプションを使用するには、DPM サーバー上の C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin ディレクトリに Ese.dll ファイルと Eseutil.exe ファイルをコピーする必要があります。 これを行わないと、次のエラーがトリガーされます。  
   > ![eseutil エラー](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. **[次へ]** をクリックします。
9. **[コピー バックアップ]** 用のデータベースを選択し、 **[次へ]** をクリックします。

   > [!NOTE]
   > データベースの少なくとも 1 つの DAG コピーに対して “完全バックアップ” が選択されていない場合、ログは切り捨てられません。
   >
   >
10. **[短期的なバックアップ]** の目標を構成し、 **[次へ]** をクリックします。
11. 使用可能なディスク領域を確認し、 **[次へ]** をクリックします。
12. DPM サーバーで初期レプリケーションを作成する時刻を選択し、 **[次へ]** をクリックします。
13. 整合性チェック オプションを選択し、 **[次へ]** をクリックします。
14. Azure にバックアップするデータベースを選択し、 **[次へ]** をクリックします。 例:

    ![オンライン保護データの指定](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. **Azure Backup** のスケジュールを定義し、 **[次へ]** をクリックします。 例:

    ![オンライン バックアップ スケジュールの指定](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > オンライン回復ポイントは高速完全回復ポイントに基づいています。 そのため、オンライン回復ポイントは、高速完全回復ポイントに指定した時刻より後にスケジュールする必要があります。
    >
    >
16. **Azure Backup** のリテンション期間ポリシーを構成し、 **[次へ]** をクリックします。
17. オンライン レプリケーション オプションを選択し、 **[次へ]** をクリックします。

    データベースの規模が大きい場合は、ネットワーク経由で最初のバックアップを作成するのに時間がかかる可能性があります。 この問題を回避するには、オフライン バックアップを作成します。  

    ![オンライン保持ポリシーの指定](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. 設定を確認し、 **[グループの作成]** をクリックします。
19. **[閉じる]** をクリックします。

## <a name="recover-the-exchange-database"></a>Exchange データベースを回復する
1. Exchange データベースを回復するには、DPM 管理者コンソールで **[回復]** をクリックします。
2. 回復する Exchange データベースを特定します。
3. *[回復時刻]* ドロップダウン リストからオンライン回復ポイントを選択します。
4. **[回復]** をクリックして、**回復ウィザード**を開始します。

オンライン回復ポイントでは、5 種類の回復があります。

* **元の Exchange Server の場所に回復する:** データは元の Exchange サーバーに回復します。
* **Exchange Server 上の別のデータベースに回復する:** データは別の Exchange サーバー上の別のデータベースに回復します。
* **回復用データベースに回復する:** データは Exchange 回復データベース (RDB) に回復します。
* **回復用データベースに回復する:** データはネットワーク フォルダーに回復します。
* **テープにコピーする:** テープ ライブラリまたはスタンドアロンのテープ ドライブが DPM サーバーに接続され、そこで構成されている場合、回復ポイントは空きテープにコピーされます。

    ![オンライン レプリケーションの選択](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>次の手順
* [Azure Backup FAQ](backup-azure-backup-faq.md)
