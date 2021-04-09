---
title: オンプレミスの SQL Server Integration Services (SSIS) ジョブを Azure Data Factory に移行する
description: この記事では、SQL Server Management Studio を使用して、SQL Server Integration Services (SSIS) のジョブを Azure Data Factory のパイプライン、アクティビティ、トリガーに移行する方法について説明します。
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: ec10abfd6f2fc221a9e86203b2faa0d173d67675
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379592"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>SSMS を使用して SQL Server エージェント ジョブを ADF に移行する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[オンプレミスの SQL Server Integration Services (SSIS) ワークロードを ADF の SSIS に移行する](scenario-ssis-migration-overview.md)ときは、SSIS パッケージが移行された後で、SQL Server Management Studio (SSMS) の **SSIS ジョブ移行ウィザード** を使用して、SQL Server Integration Services パッケージのジョブ ステップの種類で、Azure Data Factory (ADF) のパイプライン、アクティビティ、スケジュール トリガーへの、SQL Server エージェント ジョブの一括移行を実行できます。

一般に、該当するジョブ ステップの種類で選択した SQL Agent ジョブに対し、**SSIS ジョブ移行ウィザード** で次のことを実行できます。

- オンプレミスの SSIS パッケージの場所を、パッケージの移行先にマップします。これには ADF の SSIS からアクセスできます。
    > [!NOTE]
    > ファイル システムのパッケージの場所だけがサポートされます。
- 次のように、該当するジョブ ステップで該当するジョブを対応する ADF リソースに移行します。

|SQL Agent ジョブ オブジェクト  |ADF リソース  |Notes|
|---------|---------|---------|
|SQL Agent ジョブ|pipeline     |パイプラインの名前は *Generated for \<job name>* になります。 <br> <br> 組み込みのエージェントジョブには適用されません。 <li> SSIS サーバー メンテナンス ジョブ <li> syspolicy_purge_history <li> collection_set_* <li> mdw_purge_data_* <li> sysutility_*|
|SSIS ジョブ ステップ|SSIS パッケージの実行アクティビティ|<li> アクティビティの名前は \<step name> になります。 <li> ジョブ ステップで使用されるプロキシ アカウントは、このアクティビティの Windows 認証として移行されます。 <li> ジョブ ステップで定義されている、 *[32 ビット ランタイムを使用する]* を除く *[実行オプション]* は、移行時に無視されます。 <li> ジョブ ステップで定義されている "*検証*" は、移行時に無視されます。|
|schedule      |スケジュール トリガー        |スケジュール トリガーの名前は *Generated for \<schedule name>* になります。 <br> <br> SQL Agent ジョブ スケジュールの以下のオプションは、移行時に無視されます。 <li> 第 2 レベルの間隔。 <li> *[SQL Server エージェントの開始時に自動的に開始]* <li> *[CPU がアイドル状態になったときに開始]* <li> *[平日]* と *[土日]* <time zone> <br> SQL Agent ジョブ スケジュールが ADF スケジュール トリガーに移行された後の相違点は次のとおりです。 <li> ADF スケジュール トリガーの後続の実行が、先行してトリガーされた実行の実行状態に依存していません。 <li> ADF スケジュール トリガーの繰り返し構成は、SQL Agent ジョブの [一日のうちの頻度] とは異なります。|

- ローカル出力フォルダーに Azure Resource Manager (ARM) テンプレートを生成し、データ ファクトリに直接、または後ほど手動で配置します。 ADF Resource Manager テンプレートの詳細については、「[Microsoft.DataFactory リソースの種類](/azure/templates/microsoft.datafactory/allversions)」を参照してください。

## <a name="prerequisites"></a>前提条件

この記事で説明する機能には、SQL Server Management Studio バージョン 18.5 以降が必要になります。 最新バージョンの SSMS を入手するには、「[SQL Server Management Studio (SSMS) のダウンロード](/sql/ssms/download-sql-server-management-studio-ssms)」を参照してください。

## <a name="migrate-ssis-jobs-to-adf"></a>SSIS ジョブを ADF に移行する

1. SSMS のオブジェクト エクスプローラーで、[SQL Server エージェント] を選択し、[ジョブ] を選択します。次に、右クリックして **[SSIS ジョブを ADF に移行する]** を選択します。
![スクリーンショットには、SQL Server Management Studio オブジェクト エクスプローラーが示されています。ここでは、ジョブを選択し、SSIS ジョブを ADF に移行できます。](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Azure にサインインし、[Azure サブスクリプション]、[Data Factory]、[Integration Runtime] の順に選択します。 Azure Storage は省略可能です。これは、移行する SSIS ジョブに SSIS ファイル システム パッケージがある場合、パッケージの場所のマッピング ステップで使用されます。
![メニュー](media/how-to-migrate-ssis-job-ssms/step1.png)

1. SSIS ジョブの SSIS パッケージと構成ファイルのパスを、移行されたパイプラインでアクセスできる移行先パスにマップします。 このマッピング ステップでは、次のことができます。

    1. ソース フォルダーを選択し、 **[マッピングの追加]** を選択します。
    1. ソース フォルダーのパスを更新します。 有効なパスは、パッケージのフォルダー パスまたは親フォルダー パスです。
    1. 移行先のフォルダー パスを更新します。 既定値は、ステップ 1 で選択した既定のストレージ アカウントに対する相対パスです。
    1. **[マッピングの削除]** を使用して、選択したマッピングを削除します。
![スクリーンショットには、[Map SSIS Package and Configuration Paths]\(SSIS パッケージと構成パスのマップ\) ページが示されています。ここではマッピングを追加できます。](media/how-to-migrate-ssis-job-ssms/step2.png)
![スクリーンショットには、[Map SSIS Package and Configuration Paths]\(SSIS パッケージと構成パスのマップ\) ページが示されています。ここでは、ソースと宛先のフォルダー パスを更新できます。](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. 移行する適切なジョブを選択し、対応する "*SSIS パッケージの実行アクティビティ*" の設定を構成します。

    - *[既定の設定]* は、既定で選択されるすべてのステップに適用されます。 各プロパティの詳細については、パッケージの場所が "*ファイル システム (パッケージ)* " のときの [SSIS パッケージの実行アクティビティ](how-to-invoke-ssis-package-ssis-activity.md)の *[設定] タブ* を参照してください。
    ![スクリーンショットには、[Select SSIS Jobs]\(SSIS ジョブの選択\) ページが示されています。ここでは、対応する実行済み SSIS パッケージ アクティビティの設定を構成できます。](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *[Step Setting]\(ステップの設定\)* では、選択したステップの設定を構成します。
        
        **[Apply Default Setting]\(既定の設定を適用\)** : 既定で選択されています。 選択したステップのみの設定を構成するには、オフにします。  
        他のプロパティの詳細については、パッケージの場所が "*ファイル システム (パッケージ)* " のときの [SSIS パッケージの実行アクティビティ](how-to-invoke-ssis-package-ssis-activity.md)の *[設定] タブ* を参照してください。
    ![スクリーンショットには、[Select SSIS Jobs]\(SSIS ジョブの選択\) ページが示されています。ここでは、既定の設定を適用できます。](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. ARM テンプレートを生成してデプロイします。
    1. 移行された ADF パイプラインの ARM テンプレートの出力パスを選択または入力します。 フォルダーが存在しない場合は自動的に作成されます。
    2. **[ARM テンプレートをデータ ファクトリに配置する]** オプションを選択します。
        - 既定では選択されていません。 生成された ARM テンプレートは、後ほど手動でデプロイできます。
        - 生成された ARM テンプレートをデータ ファクトリに直接デプロイする場合は選択します。
    ![スクリーンショットには、[Configure Migration]\(移行の構成\) ページが示されています。ここでは、移行された ADF パイプラインの ARM テンプレートの出力パスを選択または入力し、ARM テンプレートをデータ ファクトリにデプロイするオプションを選択できます。](media/how-to-migrate-ssis-job-ssms/step4.png)

1. 移行し、結果を確認します。
![スクリーンショットには、[Migration Result]\(移行結果\) ページが示されています。ここには移行の進行状況が表示されます。](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>次のステップ

[パイプラインを実行して監視する](how-to-invoke-ssis-package-ssis-activity.md)