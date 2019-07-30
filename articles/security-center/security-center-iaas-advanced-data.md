---
title: Azure Security Center の Advanced Data Security for IaaS | Microsoft Docs
description: " Azure Security Center で Advanced Data Security for IaaS を有効にする方法について説明します。 "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: v-mohabe
ms.openlocfilehash: 0b83575baa2221f0b502abbf919654492c7ab6cf
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295756"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-public-preview"></a>Azure Virtual Machines の SQL Server 向け Advanced Data Security (パブリック プレビュー)
Azure Virtual Machines の SQL サーバー向け Advanced Data Security は、高度な SQL セキュリティ機能のための統合パッケージです。 現時点 (パブリック プレビュー) では、データベースの潜在的な脆弱性を検出し、軽減する機能や、データベースに対する脅威を示している可能性がある異常なアクティビティを検出する機能が含まれています。 

Azure VM SQL サーバー向けのこのセキュリティ オファリングは、[Azure SQL Database Advanced Data Security パッケージ](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)で使用されているのと同じ基礎技術に基づいています。


## <a name="overview"></a>概要

Advanced Data Security では、脆弱性の評価と Advanced Threat Protection から構成される一連の高度な SQL セキュリティ機能が提供されます。

* [脆弱性評価](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)では、データベースの潜在的な脆弱性を検出、追跡、修復できるサービスを簡単に構成できます。 セキュリティの状態を伝え、セキュリティの問題を解決してデータベースのセキュリティを強化するための手順が含まれます。
* [Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) では、SQL サーバーにアクセスしたり、SQL サーバーを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティが検出されます。 データベースでの不審なアクティビティを継続的に監視し、異常なデータベース アクセス パターンに対してアクション指向のセキュリティ通知を提供します。 このようなアラートからは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨されるアクションが提示されます。

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Azure VM の SQL 向け Advanced Data Security を使ってみる

Azure VM の SQL 向け Advanced Data Security (パブリック プレビュー) を使ってみるには、以下の手順に従います。

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Azure VM の SQL 向け Advanced Data Security を設定する

**開始する前に**:分析対象のセキュリティ ログを保存するための Log Analytics ワークスペースが必要になります。 用意していない場合は簡単に作成できます。詳細は、「[Azure ポータルで Log Analytics ワークスペースを作成する](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)」にあります。

1. SQL サーバーをホストしている VM を Log Analytics ワークスペースに接続します。 方法については、「[Windows コンピューターを Azure Monitor に接続する](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)」を参照してください。

1. Azure Marketplace から、[SQL Advanced Data Security ソリューション](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity)に移動します。
(次の画像のように、マーケットプレースの検索オプションで検索できます。) **[SQL Advanced Data Security]** ページが開きます。

    ![IaaS 向けの Advanced Data Security](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. **Create** をクリックしてください。 ワークスペースが表示されます。

    ![Advanced Data Security の [作成]](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. 使用するワークスペースを選択し、 **[作成]** をクリックします。

   ![ワークスペースを選択](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. [VM の SQL サーバー](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017)を再起動します。


## <a name="explore-and-investigate-security-alerts"></a>セキュリティ通知について調査する

現在のセキュリティ通知を表示し、管理できます。

1. **[Security Center]**  > 、 **[セキュリティ通知]** の順にクリックし、アラートをクリックします。

    ![アラートの検索](./media/security-center-advanced-iaas-data/find-alert.png)

1. **[攻撃されたリソース]** 列で、攻撃されたリソースをクリックします。

1. 現在の脅威について調査し、将来の脅威に対処するため、アラートの詳細とアクションを表示するには、 **[全般情報]** ページを下にスクロールし、 **[修復の手順]** セクションで **[調査手順]** リンクをクリックします。

    ![修復の手順](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. アラートのトリガーに関連付けられているログを表示するには、 **[Log Analytics ワークスペース]** に移動し、次の手順を実行します。

     > [!NOTE]
     > **[Log Analytics ワークスペース]** が左のメニューに表示されない場合、 **[すべてのサービス]** をクリックし、 **[Log Analytics ワークスペース]** を検索します。

    1. **[価格レベル]** 列と **[ワークスペース ID]** 列が表示されるようにします。 ( **[Log Analytics ワークスペース]**  > 、 **[列の編集]** の順に選択し、 **[価格レベル]** と **[ワークスペース ID]** を追加します。)

     ![列の編集](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. アラート ログのあるワークスペースをクリックします。

    1. **[全般]** メニューで **[ログ]** をクリックします。

    1. **SQLAdvancedThreatProtection** テーブルの横にある眼をクリックします。 ログが一覧表示されます。

     ![ログの表示](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>ATP アラートのメール通知を設定する 

ASC アラートの生成時にメール通知を受け取る受信者の一覧を設定できます。 メールには、Azure Security Center におけるアラートの直接リンクと関連するすべての詳細が含まれます。 

1. **[Security Center]**  >  **[Pricing & settings]\(価格と設定\)** に移動し、適切なサブスクリプションをクリックします。

    ![サブスクリプション設定](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. **[設定]** メニューから、 **[メール通知]** をクリックします。 
1. **[メール アドレス]** テキスト ボックスに、通知を受け取るメール アドレスを入力します。 メール アドレスをコンマ (,) で区切ることで複数のメール アドレスを入力できます。  たとえば、「admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com」のようにします。

      ![電子メールの設定](./media/security-center-advanced-iaas-data/email-settings.png)

1. **[メール通知]** 設定で、次のオプションを設定します。
  
    * **重要度 - 高についてアラートの電子メール通知を送信する**:すべてのアラートに対してメールを送信する代わりに、重要度が高のアラートに対してのみ送信します。
    * **サブスクリプションの所有者にも電子メール通知を送信する**:サブスクリプションの所有者にも通知を送信します。

1. **[メール通知]** 画面の一番上にある **[保存]** をクリックします。

  > [!NOTE]
  > ウィンドウを閉じる前に必ず **[保存]** をクリックしてください。クリックしない場合、新しい**メール通知**設定が保存されません。

## <a name="explore-vulnerability-assessment-reports"></a>脆弱性評価レポートについて調べる

脆弱性評価ダッシュボードには、すべてのデータベースを対象とする評価結果の概要が表示されます。 SQL Server バージョンによるデータベースのディストリビューション、失格となったデータベースと合格となったデータベースの比較概要、リスク ディストリビューションによるチェック不合格の全体的まとめを確認できます。

脆弱性評価の結果とレポートは、Log Analytics から直接表示できます。

1. Advanced Data Security ソリューションで Log Analytics ワークスペースに移動します。
1. **[ソリューション]** に移動し、 **[SQL の脆弱性評価]** ソリューションを選択します。
1. **[概要]** ウィンドウで、 **[概要の表示]** をクリックし、 **[SQL Vulnerability Assessment Report]\(SQL の脆弱性評価レポート\)** を選択します。

    ![SQL 評価レポート](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    レポート ダッシュボードが読み込まれます。 時間枠が少なくとも **[過去 7 日間]** に設定されていることを確認してください。これは、脆弱性評価はデータベースで、7 日間に 1 回という固定のスケジュールで実行されるためです。

    ![過去 7 日間を設定します](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. 詳細を見るには、いずれかのダッシュボード要素をクリックします。 例:

   1. **[Failed checks summary]\(失敗したチェックのまとめ\)** セクションの脆弱性チェックをクリックすると、Log Analytics テーブルと、すべてのデータベースに対して行ったこのチェックの結果が表示されます。 結果が含まれるものが最初に表示されます。

   1. 次に、クリックして、該当するデータベースの脆弱性の説明と影響、状態、関連リスク、実際の結果を含む、各脆弱性の詳細を表示します。 このチェックのために実行された実際のクエリとこの脆弱性を解決するための修復情報も確認できます。

    ![ワークスペースを選択](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![ワークスペースを選択](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. 脆弱性評価の結果データに Log Analytics クエリを実行し、ニーズに基づいてデータを詳細に分析できます。

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Azure VM の SQL サーバー向け Advanced Threat Protection のアラート
普段は見られない、潜在的に有害な SQL Server へのアクセスが試行されると、あるいは SQL Server の悪用が試行されると、アラートが生成されます。 これらのイベントにより、次のアラートがトリガーされる場合があります。

### <a name="anomalous-access-pattern-alerts-supported-in-public-preview"></a>(パブリック プレビューでサポートされている) 異常なアクセス パターンのアラート

* **通常とは異なる場所からのアクセス:** このアラートは、だれかが通常とは異なる地理的な場所から SQL サーバーにログオンしたことで SQL Server へのアクセス パターンに変化が生じたときにトリガーされます。 考えられる原因は次のとおりです。
     * 攻撃者または悪意のある元社員が SQL Server にアクセスしました。
     * 正当なユーザーが新しい場所から SQL Server にアクセスしました。
* **潜在的に有害なアプリケーションからのアクセス**: このアラートは、データベースにアクセスするために潜在的に有害なアプリケーションが使用されたときにトリガーされます。 考えられる原因は次のとおりです。
     * 一般的な攻撃ツールで攻撃者が SQL を侵害しようとしています。
     * 正当な侵入テストが行われています。
* **Access from unfamiliar principal (通常とは異なるプリンシパルからのアクセス)** :このアラートは、だれかが通常とは異なるプリンシパル (SQL ユーザー) を使用して SQL サーバーにログオンしたことで SQL サーバーへのアクセス パターンに変化が生じたときにトリガーされます。 考えられる原因は次のとおりです。
     * 攻撃者または悪意のある元社員が SQL Server にアクセスしました。 
     * 正当なユーザーが新しいプリンシパルで SQL Server にアクセスしました。
* **Brute force SQL credentials (SQL 資格情報に対するブルート フォース攻撃)** :このアラートは、異なる資格情報でログインに失敗した回数が異常に多いときにトリガーされます。 考えられる原因は次のとおりです。
     * ブルート フォースで攻撃者が SQL を侵害しようとしています。
     * 正当な侵入テストが行われています。

### <a name="potential-sql-injection-attacks-coming"></a>潜在的な SQL インジェクション攻撃 (準備中)

* **Vulnerability to SQL Injection (SQL インジェクションにつながる脆弱性)** :このアラートは、アプリケーションがデータベースにエラーのある SQL ステートメントを生成したときにトリガーされます。 このアラートは、SQL インジェクション攻撃に対する脆弱性が存在する可能性を示すものです。 考えられる原因は次のとおりです。
     * アプリケーション コードの欠陥により、エラーのある SQL 文が作成される
     * アプリケーション コードまたはストアド プロシージャが、SQL インジェクションに悪用される可能性があるエラーのある SQL ステートメントを作成するときにユーザー入力をサニタイズしない
* **Potential SQL injection (SQL インジェクションの可能性)** :このアラートは、SQL インジェクションに対する特定されたアプリケーションの脆弱性に対してアクティブな悪用が発生したときにトリガーされます。 これは、攻撃者が脆弱なアプリケーション コードまたはストアド プロシージャを使用して悪意のある SQL 文を挿入しようとしていることを意味します。
