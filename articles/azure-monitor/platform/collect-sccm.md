---
title: Configuration Manager を Azure Monitor に接続する | Microsoft Docs
description: この記事では、Configuration Manager を Azure Monitor のワークスペースに接続してデータの分析を開始する手順を示します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: e5cf89b7fe01946de9944a7026ec448cd55dd6dc
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630668"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Configuration Manager を Azure Monitor に接続する
お使いの System Center Configuration Manager 環境を Azure Monitor に接続して、デバイス コレクション データを同期し、Azure Monitor と Azure Automation でこれらのコレクションを参照することができます。  

## <a name="prerequisites"></a>前提条件

Azure Monitor では、System Center Configuration Manager の現在のブランチ バージョン 1606 以降がサポートされます。  

## <a name="configuration-overview"></a>構成の概要
次の手順は、Configuration Manager と Azure Monitor の統合を構成する手順をまとめたものです。  

1. Azure Portal で Configuration Manager を Web アプリケーションまたは Web API アプリとして登録し、Azure Active Directory への登録によるクライアント ID およびクライアント秘密鍵を持っていることを確認します。 この手順の詳細については、「[リソースにアクセスできる Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](../../active-directory/develop/howto-create-service-principal-portal.md)」を参照してください。
2. Azure portal で、[Configuration Manager (登録した Web アプリ) に Azure Monitor へのアクセス許可を付与](#grant-configuration-manager-with-permissions-to-log-analytics)します。
3. Configuration Manager で、OMS 接続の追加ウィザードを使用して接続を追加します。
4. パスワードまたはクライアント秘密鍵が期限切れになるか、それらを紛失した場合は、Configuration Manager で、接続プロパティを更新します。
5. Configuration Manager サービス接続ポイントのサイト システムの役割を実行しているコンピューターに、[Microsoft Monitoring Agent をダウンロードしてインストール](#download-and-install-the-agent)します。 エージェントは、Configuration Manager のデータを Azure Monitor の Log Analytics ワークスペースに送信します。
6. Azure Monitor で、コンピューター グループとして [Configuration Manager からコレクションをインポート](#import-collections)します。
7. Azure Monitor で、Configuration Manager からのデータを[コンピューター グループ](computer-groups.md)として表示します。

Configuration Manager を Azure Monitor に接続する方法の詳細については、[Configuration Manager から Microsoft Log Analytics ワークスペースにデータを同期する方法](https://technet.microsoft.com/library/mt757374.aspx)に関するページを参照してください。

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Configuration Manager に Log Analytics へのアクセス許可を付与する
次の手順では、前に Configuration Manager 用に作成した AD アプリケーションとサービス プリンシパルに、Log Analytics ワークスペースの "*共同作成者*" ロールを付与します。  まだワークスペースがない場合は、続行する前に [Azure Monitor でのワークスペースの作成](../../azure-monitor/learn/quick-create-workspace.md)に関するページを参照してください。  これにより、Configuration Manager は認証を行って Log Analytics ワークスペースに接続できます。  

> [!NOTE]
> Log Analytics ワークスペースへのアクセス許可を Configuration Manager に指定する必要があります。 そうしないと、Configuration Manager で構成ウィザードを使用するときにエラー メッセージが表示されます。
>

1. Azure Portal の左上隅にある **[すべてのサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。
2. Log Analytics ワークスペースの一覧で、変更するワークスペースを選びます。
3. 左側のウィンドウで **[アクセス制御 (IAM)]** を選びます。
4. [アクセス制御 (IAM)] ページで、**[ロールの割り当ての追加]** をクリックすると、**[ロールの割り当ての追加]** ウィンドウが表示されます。
5. **[ロールの割り当ての追加]** ウィンドウの **[ロール]** ドロップダウン リストで、**[共同作成者]** ロールを選びます。  
6. **[アクセスの割り当て先]** ドロップダウン リストで、前に AD で作成した Configuration Manager アプリケーションを選び、**[OK]** をクリックします。  

## <a name="download-and-install-the-agent"></a>エージェントのダウンロードとインストール
[Azure Monitor への Windows コンピューターの接続](agent-windows.md)に関する記事を読み、Configuration Manager サービス接続ポイントのサイト システムの役割をホストしているコンピューターに Microsoft Monitoring Agent をインストールする方法を把握してください。  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>Configuration Manager に Log Analytics 接続を追加する
Log Analytics 接続を追加するには、Configuration Manager 環境に[サービス接続ポイント](https://technet.microsoft.com/library/mt627781.aspx)があり、オンライン モード用に構成されている必要があります。

1. Configuration Manager の **[管理]** ワークスペースで、**[OMS コネクタ]** を選択します。 これで、**Log Analytics 接続の追加ウィザード**が開かれます。 **[次へ]** を選択します。

   >[!NOTE]
   >OMS は、Log Analytics と呼ばれるようになりました。これは Azure Monitor の機能です。
   
2. **[全般]** 画面で、以下の操作が完了していることと、各項目の詳細を把握していることを確認し、**[次へ]** を選択します。

   1. Azure Portal で、Configuration Manager を Web アプリケーションまたは Web API アプリとして登録し、[登録からクライアント ID](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md) を取得した。
   2. Azure Portal で、Azure Active Directory に登録したアプリのアプリ秘密鍵を作成した。  
   3. Azure portal で、登録した Web アプリに Azure Monitor の Log Analytics ワークスペースへのアクセス許可を付与した。  
      ![Log Analytics への接続ウィザードの [全般] ページ](./media/collect-sccm/sccm-console-general01.png)
3. **[Azure Active Directory]** 画面で、Log Analytics ワークスペースへの接続設定を構成します。**[テナント]**、**[クライアント ID]**、および **[クライアントの秘密鍵]** を指定してから、**[次へ]** を選びます。  
   ![Log Analytics への接続ウィザードの [Azure Active Directory] ページ](./media/collect-sccm/sccm-wizard-tenant-filled03.png)
4. 他の手順がすべて正常に完了している場合は、**[OMS Connection Configuration (OMS 接続の構成)]** 画面の情報がこのページに自動的に表示されます。 接続設定の情報が表示されるのは、**[Azure サブスクリプション]**、**[Azure リソース グループ]**、および **[Operations Management Suite ワークスペース]** です。  
   ![Log Analytics への接続ウィザードの [Log Analytics Connection]\(Log Analytics 接続\) ページ](./media/collect-sccm/sccm-wizard-configure04.png)
5. 入力された情報がウィザードにより使用され、Log Analytics ワークスペースに接続されます。 サービスと同期するデバイス コレクションを選び、**[追加]** をクリックします。  
   ![コレクションの選択](./media/collect-sccm/sccm-wizard-add-collections05.png)
6. **[概要]** 画面で接続の設定を確認し、**[次へ]** を選択します。 **[進行状況]** 画面に接続の状態が表示され、その後 **[完了]** になります。

> [!NOTE]
> 階層の最上層サイトを Azure Monitor に接続する必要があります。 スタンドアロン プライマリ サイトを Azure Monitor に接続した後で、環境に中央管理サイトを追加する場合は、接続を削除し、新しい階層内で接続を再作成する必要があります。
>
>

Configuration Manager を Azure Monitor にリンクした後、コレクションを追加または削除したり、接続のプロパティを表示したりすることができます。

## <a name="update-log-analytics-workspace-connection-properties"></a>Log Analytics ワークスペースの接続プロパティを更新する
パスワードまたはクライアント秘密鍵が期限切れになるか、それらを紛失した場合は、Log Analytics の接続プロパティを手動で更新する必要があります。

1. Configuration Manager で **[Cloud Services]** に移動し、**[OMS コネクタ]** を選択して、**[OMS Connection Properties]\(OMS 接続プロパティ\)** ページを開きます。
2. このページで **[Azure Active Directory]** タブをクリックし、**[テナント]**、**[クライアント ID]**、**[Client secret key expiration (クライアントの秘密鍵の期限切れ)]** を表示します。 **クライアントの秘密鍵**が期限切れかどうかを**確認**します。

## <a name="import-collections"></a>コレクションをインポートする
Log Analytics 接続を Configuration Manager に追加し、Configuration Manager サービス接続ポイントのサイト システムの役割を実行しているコンピューターにエージェントをインストールしたら、その次の手順は Configuration Manager からのコレクションをコンピューター グループとして Azure Monitor にインポートすることです。

初期構成を完了して階層からデバイス コレクションをインポートした後は、メンバーシップを最新に保つために、コレクションのメンバーシップ情報が 3 時間ごとに取得されます。 この処理はいつでも無効にすることができます。

1. Azure Portal の左上隅にある **[すべてのサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics ワークスペース]** を選択します。
2. Log Analytics ワークスペースの一覧で、Configuration Manager が登録されているワークスペースを選びます。  
3. **[詳細設定]** を選択します。
4. **[コンピューター グループ]** を選び、**[SCCM]** を選びます。  
5. **[Configuration Manager コレクション メンバーシップをインポートする]** を選択し、**[保存]** をクリックします。  
   ![コンピューター グループ - [SCCM] タブ](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Configuration Manager のデータを表示する
Log Analytics 接続を Configuration Manager に追加し、Configuration Manager サービス接続ポイントのサイト システムの役割を実行しているコンピューターにエージェントをインストールすると、エージェントからのデータが Azure Monitor の Log Analytics ワークスペースに送信されます。 Azure Monitor で、Configuration Manager コレクションが[コンピューター グループ](../../azure-monitor/platform/computer-groups.md)として表示されます。 グループは、**[Configuration Manager]** ページの **[設定] > [コンピューター グループ]** で見ることができます。

コレクションがインポートされると、コレクションのメンバーシップを持つコンピューターが何台検出されたかを確認できます。 インポートされたコレクションの数を確認することもできます。

![コンピューター グループ - [SCCM] タブ](./media/collect-sccm/sccm-computer-groups02.png)

いずれかをクリックすると、[検索] が開き、インポートされたすべてのグループか、各グループに属しているすべてのコンピューターが表示されます。 [ログの検索](../../azure-monitor/log-query/log-query-overview.md)を使用して、Configuration Manager のデータの詳細な分析を開始することができます。

## <a name="next-steps"></a>次の手順
* [ログの検索](../../azure-monitor/log-query/log-query-overview.md)を使用して、Configuration Manager のデータに関する詳細な情報を表示します。
