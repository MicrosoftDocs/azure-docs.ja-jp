---
title: "Configuration Manager を Log Analytics に接続する | Microsoft Docs"
description: "この記事では、Configuration Manager を Log Analytics に接続してデータの分析を開始する手順を示します。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: aca83d2de9247bedacce0fb03efe141d903d8605
ms.openlocfilehash: f93d37ad5be4bf7fdc78d83ec68ba56a427b3e35
ms.lasthandoff: 02/23/2017


---
# <a name="connect-configuration-manager-to-log-analytics"></a>Configuration Manager を Log Analytics に接続する
System Center Configuration Manager を OMS の Log Analytics に接続して、デバイス コレクション データを同期することができます。 そうすることで、Configuration Manager 階層からのデータを OMS で使用できます。

## <a name="prerequisites"></a>前提条件

Log Analytics では、System Center Configuration Manager の現在のブランチ (バージョン 1606 以降) をサポートしています。  

## <a name="configuration-overview"></a>構成の概要
次の手順は、Configuration Manager を Log Analytics に接続するプロセスをまとめたものです。  

1. Azure 管理ポータルで Configuration Manager を Web アプリケーションまたは Web API アプリとして登録し、Azure Active Directory への登録によるクライアント ID およびクライアント秘密鍵を持っていることを確認します。 この手順の詳細については、「[リソースにアクセスできる Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](../azure-resource-manager/resource-group-create-service-principal-portal.md)」を参照してください。
2. Azure 管理ポータルで、[Configuration Manager (登録した Web アプリ) に OMS へのアクセス許可を付与](#provide-configuration-manager-with-permissions-to-oms)します。
3. Configuration Manager で、[OMS 接続の追加ウィザードを使用して接続を追加](#add-an-oms-connection-to-configuration-manager)します。
4. パスワードまたはクライアント秘密鍵が期限切れになるか、それらを紛失した場合は、Configuration Manager で、[接続プロパティを更新](#update-oms-connection-properties)します。
5. OMS ポータルからの情報を使用して、Configuration Manager サービス接続ポイントのサイト システムの役割を実行しているコンピューターに、[Microsoft Monitoring Agent をダウンロードおよびインストール](#download-and-install-the-agent)します。 エージェントは、Configuration Manager のデータを OMS に送信します。
6. Log Analytics で、コンピューター グループとして [Configuration Manager からコレクションをインポート](#import-collections)します。
7. Log Analytics で、Configuration Manager からのデータを[コンピューター グループ](log-analytics-computer-groups.md)として表示します。

Configuration Manager と OMS の接続の詳細については、「[Sync data from Configuration Manager to the Microsoft Operations Management Suite (Configuration Manager のデータを Microsoft Operations Management Suite に同期する)](https://technet.microsoft.com/library/mt757374.aspx)」を参照してください。

## <a name="provide-configuration-manager-with-permissions-to-oms"></a>Configuration Manager に OMS へのアクセス許可を付与する
次の手順では、Azure 管理ポータルに OMS へのアクセス許可を付与します。 具体的には、Azure 管理ポータルでConfiguration Manager を OMS に接続できるようにするために、リソース グループ内のユーザーに "*共同作業者ロール*" を付与する必要があります。

> [!NOTE]
> OMS へのアクセス許可を Configuration Manager に指定する必要があります。 そうしないと、Configuration Manager で構成ウィザードを使用するときにエラー メッセージが表示されます。
>
>

1. [Azure Portal](https://portal.azure.com/) を開き、**[その他のサービス]** > **[Log Analytics (OMS)]** の順にクリックして、[Log Analytics (OMS)] ブレードを開きます。  
2. **[Log Analytics (OMS)]** ブレードで **[追加]** をクリックして、**[OMS ワークスペース]** ブレードを開きます。  
   ![[OMS] ブレード](./media/log-analytics-sccm/sccm-azure01.png)
3. **[OMS ワークスペース]** ブレードで以下の情報を指定し、**[OK]** をクリックします。

   * **OMS ワークスペース**
   * **サブスクリプション**
   * **[リソース グループ]**
   * **場所**
   * **価格レベル**  
     ![[OMS] ブレード](./media/log-analytics-sccm/sccm-azure02.png)  

     > [!NOTE]
     > 上の例は、新しいリソース グループを作成します。 この例では、リソース グループは Configuration Manager に OMS ワークスペースへのアクセス許可を付与するためだけに使用されます。
     >
     >
4. **[その他のサービス]** > **[リソース グループ]** の順にクリックし、**[リソース グループ]** ブレードを開きます。
5. **[リソース グループ]** ブレードで、前の手順で作成したリソース グループをクリックして、&lt;リソース グループ名&gt;の設定ブレードを開きます。  
   ![リソース グループの設定ブレード](./media/log-analytics-sccm/sccm-azure03.png)
6. &lt;リソース グループ名&gt;の設定ブレードで、[アクセス制御 (IAM)] をクリックして、&lt;リソース グループ名&gt; のユーザー ブレードを開きます。  
   ![リソース グループのユーザー ブレード](./media/log-analytics-sccm/sccm-azure04.png)  
7. &lt;リソース グループ名&gt; のユーザー ブレードの **[追加]** をクリックして、**[アクセス権の追加]** ブレードを開きます。
8. **[アクセス権の追加]** ブレードで **[ロールの選択]** をクリックし、**[共同作成者]** ロールを選択します。  
   ![ロールの選択](./media/log-analytics-sccm/sccm-azure05.png)  
9. **[ユーザーの追加]** をクリックし、Configuration Manager ユーザーを選択し、**[選択]****[OK]** の順にクリックします。  
   ![ユーザーの追加](./media/log-analytics-sccm/sccm-azure06.png)  

## <a name="add-an-oms-connection-to-configuration-manager"></a>Configuration Manager に OMS 接続を追加する
OMS 接続を追加するには、Configuration Manager 環境に[サービス接続ポイント](https://technet.microsoft.com/library/mt627781.aspx)があり、オンライン モード用に構成されている必要があります。

1. Configuration Manager の **[管理]** ワークスペースで、**[OMS コネクタ]** を選択します。 これで、**OMS 接続の追加ウィザード**が開かれます。 **[次へ]**を選択します。
2. **[全般]** 画面で、以下の操作が完了していることと、各項目の詳細を把握していることを確認し、**[次へ]** を選択します。

   1. Azure 管理ポータルで、Configuration Manager を Web アプリケーションまたは Web API アプリとして登録し、[登録からクライアント ID](../active-directory/active-directory-integrating-applications.md) を取得した。
   2. Azure 管理ポータルで、Azure Active Directory に登録したアプリのアプリ秘密鍵を作成した。  
   3. Azure 管理ポータルで、登録した Web アプリに OMS へのアクセス許可を付与した。  
      ![OMS への接続ウィザードの [全般] ページ](./media/log-analytics-sccm/sccm-console-general01.png)
3. **[Azure Active Directory]** 画面で、OMS への接続設定を構成します。**[テナント]**、**[クライアント ID]**、および **[クライアントの秘密鍵]** を指定してから、**[次へ]** を選択します。  
   ![OMS への接続ウィザードの [Azure Active Directory] ページ](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. 他の手順がすべて正常に完了している場合は、**[OMS Connection Configuration (OMS 接続の構成)]** 画面の情報がこのページに自動的に表示されます。 接続設定の情報が表示されるのは、**[Azure サブスクリプション]**、**[Azure リソース グループ]**、および **[Operations Management Suite ワークスペース]** です。  
   ![OMS への接続ウィザードの [OMS 接続] ページ](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. ウィザードは、入力された情報を使用して、OMS サービスに接続します。 OMS と同期するデバイス コレクションを選択し、**[追加]** をクリックします。  
   ![コレクションの選択](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. **[概要]** 画面で接続の設定を確認し、**[次へ]** を選択します。 **[進行状況]** 画面に接続の状態が表示され、その後 **[完了]** になります。

> [!NOTE]
> OMS を階層内の最上層サイトに接続する必要があります。 OMS をスタンドアロン プライマリ サイトに接続し、その後、環境に中央管理サイトを追加すると、新しい環境内で OMS 接続を削除して再作成しなければならなくなります。
>
>

Configuration Manager を OMS にリンクした後、コレクションを追加または削除したり、OMS 接続のプロパティを表示したりすることができます。

## <a name="update-oms-connection-properties"></a>OMS 接続プロパティを更新する
パスワードまたはクライアント秘密鍵が期限切れになるか、それらを紛失した場合は、OMS 接続プロパティを手動で更新する必要があります。

1. Configuration Manager で **[クラウド サービス]** に移動し、**[OMS コネクタ]** を選択して、**[OMS Connection Properties (OMS 接続プロパティ)]** ページを開きます。
2. このページで **[Azure Active Directory]** タブをクリックし、**[テナント]**、**[クライアント ID]**、**[Client secret key expiration (クライアントの秘密鍵の期限切れ)]** を表示します。 **クライアントの秘密鍵**が期限切れかどうかを**確認**します。

## <a name="download-and-install-the-agent"></a>エージェントのダウンロードとインストール
1. OMS ポータルで、[OMS からエージェントのセットアップ ファイルをダウンロード](log-analytics-windows-agents.md#download-the-agent-setup-file-from-oms)します。
2. Configuration Manager サービス接続ポイントのサイト システムの役割を実行しているコンピューターに、次のいずれかの方法で、エージェントをインストールおよび構成します。
   * [セットアップを使用してエージェントをインストールする](log-analytics-windows-agents.md#install-the-agent-using-setup)
   * [コマンド ラインを使用してエージェントをインストールする](log-analytics-windows-agents.md#install-the-agent-using-the-command-line)
   * [Azure Automation の DSC を使用してエージェントをインストールする](log-analytics-windows-agents.md#install-the-agent-using-dsc-in-azure-automation)

## <a name="import-collections"></a>コレクションをインポートする
OMS 接続を Configuration Manager に追加し、Configuration Manager サービス接続ポイントのサイト システムの役割を実行しているコンピューターにエージェントをインストールしたら、次の手順は Configuration Manager からのコレクションをコンピューター グループとして OMS にインポートすることです。

インポートを有効にすると、コレクションのメンバーシップを最新の状態で維持するために、コレクションのメンバーシップ情報が 3 時間ごとに取得されます。 インポートはいつでも無効にすることができます。

1. OMS ポータルで、**[設定]** をクリックします。
2. **[コンピューター グループ]** タブをクリックし、**[SCCM]** タブをクリックします。
3. **[Configuration Manager コレクション メンバーシップをインポートする]** を選択し、**[保存]** をクリックします。  
   ![コンピューター グループ - [SCCM] タブ](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Configuration Manager のデータを表示する
OMS 接続を Configuration Manager に追加し、Configuration Manager サービス接続ポイントのサイト システムの役割を実行しているコンピューターにエージェントをインストールすると、エージェントからのデータが OMS に送信されます。 OMS で、Configuration Manager コレクションが[コンピューター グループ](log-analytics-computer-groups.md)として表示されます。 グループは、**[Configuration Manager]** ページの **[コンピューター グループ]** の **[設定]** で見ることができます。

コレクションがインポートされると、コレクションのメンバーシップを持つコンピューターが何台検出されたかを確認できます。 インポートされたコレクションの数を確認することもできます。

![コンピューター グループ - [SCCM] タブ](./media/log-analytics-sccm/sccm-computer-groups02.png)

いずれかをクリックすると、[検索] が開き、インポートされたすべてのグループか、各グループに属しているすべてのコンピューターが表示されます。 [ログの検索](log-analytics-log-searches.md)を使用して、Configuration Manager のデータの詳細な分析を開始することができます。

## <a name="next-steps"></a>次のステップ
* [ログの検索](log-analytics-log-searches.md)を使用して、Configuration Manager のデータに関する詳細な情報を表示します。

