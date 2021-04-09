---
title: Configuration Manager を Azure Monitor に接続する | Microsoft Docs
description: この記事では、Configuration Manager を Azure Monitor のワークスペースに接続してデータの分析を開始する手順を示します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/30/2020
ms.openlocfilehash: 7302f61fce6763fdbb41c2b4899dea749ac3225f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031074"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Configuration Manager を Azure Monitor に接続する
お使いの Microsoft Endpoint Configuration Manager 環境を Azure Monitor に接続して、デバイス コレクション データを同期し、Azure Monitor と Azure Automation でこれらのコレクションを参照することができます。  

> [!IMPORTANT]
> Configuration Manager バージョン 2010 以降では、この機能は非推奨とされます。<!-- 8269855 --> 詳しくは、「[Configuration Manager から削除された機能と非推奨の機能](/mem/configmgr/core/plan-design/changes/deprecated/removed-and-deprecated-cmfeatures)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure Monitor では、Configuration Manager の現在のブランチ バージョン 1606 以降がサポートされます。

>[!NOTE]
>Configuration Manager と Log Analytics ワークスペースを接続する機能は任意であり、既定では有効になっていません。 この機能は、使用する前に有効にする必要があります。 詳細については、「[Enable optional features from updates](/configmgr/core/servers/manage/install-in-console-updates#bkmk_options)」 (更新プログラムのオプション機能の有効化) を参照してください。

## <a name="configuration-overview"></a>構成の概要

次の手順は、Configuration Manager と Azure Monitor の統合を構成する手順をまとめたものです。  

1. Azure Active Directory で Configuration Manager を Web アプリケーションまたは Web API アプリとして登録し、Azure Active Directory への登録によるクライアント ID およびクライアント秘密鍵を持っていることを確認します。 この手順の詳細については、「[リソースにアクセスできる Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](../../active-directory/develop/howto-create-service-principal-portal.md)」を参照してください。

2. Azure Active Directory で、[Configuration Manager (登録した Web アプリ) に Azure Monitor へのアクセス許可を付与](#grant-configuration-manager-with-permissions-to-log-analytics)します。

3. Configuration Manager で、**Azure Services** ウィザードを使用して接続を追加します。

4. Configuration Manager サービス接続ポイントのサイト システムの役割を実行しているコンピューターに、[Windows 用 Log Analytics エージェントをダウンロードしてインストール](#download-and-install-the-agent)します。 エージェントは、Configuration Manager のデータを Azure Monitor の Log Analytics ワークスペースに送信します。

5. Azure Monitor で、コンピューター グループとして [Configuration Manager からコレクションをインポート](#import-collections)します。

6. Azure Monitor で、Configuration Manager からのデータを[コンピューター グループ](computer-groups.md)として表示します。

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Configuration Manager に Log Analytics へのアクセス許可を付与する

次の手順では、前に Configuration Manager 用に作成した AD アプリケーションとサービス プリンシパルに、Log Analytics ワークスペースの "*共同作成者*" ロールを付与します。 まだワークスペースがない場合は、続行する前に [Azure Monitor でのワークスペースの作成](./quick-create-workspace.md)に関するページを参照してください。 これにより、Configuration Manager は認証を行って Log Analytics ワークスペースに接続できます。  

> [!NOTE]
> Log Analytics ワークスペースへのアクセス許可を Configuration Manager に指定する必要があります。 そうしないと、Configuration Manager で構成ウィザードを使用するときにエラー メッセージが表示されます。
>

1. Azure Portal の左上隅にある **[すべてのサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。

2. Log Analytics ワークスペースの一覧で、変更するワークスペースを選びます。

3. 左側のウィンドウで **[アクセス制御 (IAM)]** を選びます。

4. [アクセス制御 (IAM)] ページで、 **[ロールの割り当ての追加]** をクリックすると、 **[ロールの割り当ての追加]** ウィンドウが表示されます。

5. **[ロールの割り当ての追加]** ウィンドウの **[ロール]** ドロップダウン リストで、 **[共同作成者]** ロールを選びます。  

6. **[アクセスの割り当て先]** ドロップダウン リストで、前に AD で作成した Configuration Manager アプリケーションを選び、 **[OK]** をクリックします。  

## <a name="download-and-install-the-agent"></a>エージェントのダウンロードとインストール

[Azure Monitor への Windows コンピューターの接続](../agents/agent-windows.md)に関する記事を読み、Configuration Manager サービス接続ポイントのサイト システムの役割をホストしているコンピューターに Windows 用 Log Analytics エージェントをインストールする方法を把握してください。  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Configuration Manager を Log Analytics ワークスペースに接続する

>[!NOTE]
> Log Analytics 接続を追加するには、Configuration Manager 環境でオンライン モード用に[サービス接続ポイント](/configmgr/core/servers/deploy/configure/about-the-service-connection-point)を構成しておく必要があります。

> [!NOTE]
> 階層の最上層サイトを Azure Monitor に接続する必要があります。 スタンドアロン プライマリ サイトを Azure Monitor に接続した後で、環境に中央管理サイトを追加する場合は、接続を削除し、新しい階層内で接続を再作成する必要があります。

1. Configuration Manager の **[管理]** ワークスペースで、 **[Cloud Services]** を選択し、 **[Azure サービス]** を選択します。 

2. **[Azure サービス]** を右クリックし、 **[Azure サービスの構成]** を選択します。 **[Azure サービスの構成]** ページが表示されます。 
   
3. **[全般]** 画面で、以下の操作が完了していることと、各項目の詳細を把握していることを確認し、 **[次へ]** を選択します。

4. Azure サービス ウィザードの [Azure サービス] ページで次の操作を行います。

    1. Configuration Manager でオブジェクトの **[名前]** を指定します。
    2. 任意で **[説明]** を指定します。これはサービスの識別に役立ちます。
    3. Azure サービスの **[OMS コネクタ]** を選択します。

    >[!NOTE]
    >OMS は、Log Analytics と呼ばれるようになりました。これは Azure Monitor の機能です。

5. **[次へ]** を選択し、Azure サービス ウィザードの Azure アプリ プロパティ ページに進みます。

6. Azure サービス ウィザードの **[アプリ]** ページで、まず、一覧から Azure 環境を選択し、次に **[インポート]** をクリックします。

7. **[アプリのインポート]** ページで、次の情報を指定します。

    1. アプリの **[Azure AD テナント名]** を指定します。

    2. **[Azure AD テナント ID]** に Azure AD テナントを指定します。 この情報は Azure Active Directory の **[プロパティ]** ページにあります。 

    3. **[アプリケーション名]** にアプリケーション名を指定します。

    4. **[クライアント ID]** に、前に作成した Azure AD アプリのアプリケーション ID を指定します。

    5. **[秘密鍵]** に、作成した Azure AD アプリのクライアント秘密鍵を指定します。

    6. **[秘密鍵の有効期限]** に鍵の有効期限を指定します。

    7. **[アプリケーション ID/URI]** に、前に作成した Azure AD アプリのアプリケーション ID/URI を指定します。

    8. **[確認]** を選択すると、右に **[検証が正常に完了しました]** と結果が表示されるはずです。

8. **[構成]** ページで情報を見直し、 **[Azure サブスクリプション]** 、 **[Azure リソース グループ]** 、 **[Operations Management Suite workspace]\(Operations Management Suite ワークスペース\)** という各フィールドにデータが事前入力されており、リソース グループで Azure AD アプリケーションに十分な権限が与えられていることを確認します。 フィールドが空の場合、アプリケーションに必要な権限が与えられていないことを示します。 収集してワークスペースに転送するデバイス コレクションを選択し、 **[追加]** を選択します。

9. **[設定の確認]** ページでオプションを確認し、 **[次へ]** を選択し、接続の作成と構成を開始します。

10. 構成が完了すると、 **[完了]** ページが表示されます。 **[閉じる]** を選択します。 

Configuration Manager を Azure Monitor にリンクした後、コレクションを追加または削除したり、接続のプロパティを表示したりすることができます。

## <a name="update-log-analytics-workspace-connection-properties"></a>Log Analytics ワークスペースの接続プロパティを更新する

パスワードまたはクライアント秘密鍵が期限切れになるか、それらを紛失した場合は、Log Analytics の接続プロパティを手動で更新する必要があります。

1. Configuration Manager の **[管理]** ワークスペースで **[Cloud Services]** を選択し、 **[OMS コネクタ]** を選択して **[OMS Connection Properties]\(OMS 接続プロパティ\)** ページを開きます。
2. このページで **[Azure Active Directory]** タブをクリックし、 **[テナント]** 、 **[クライアント ID]** 、 **[Client secret key expiration (クライアントの秘密鍵の期限切れ)]** を表示します。 **クライアントの秘密鍵** が期限切れかどうかを **確認** します。

## <a name="import-collections"></a>コレクションをインポートする

Log Analytics 接続を Configuration Manager に追加し、Configuration Manager サービス接続ポイントのサイト システムの役割を実行しているコンピューターにエージェントをインストールしたら、その次の手順は Configuration Manager からのコレクションをコンピューター グループとして Azure Monitor にインポートすることです。

初期構成を完了して階層からデバイス コレクションをインポートした後は、メンバーシップを最新に保つために、コレクションの情報が 3 時間ごとに取得されます。 この処理はいつでも無効にすることができます。

1. Azure Portal の左上隅にある **[すべてのサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics ワークスペース]** を選択します。
2. Log Analytics ワークスペースの一覧で、Configuration Manager が登録されているワークスペースを選びます。  
3. **[詳細設定]** を選択します。
4. **[コンピューター グループ]** を選び、 **[SCCM]** を選びます。  
5. **[Configuration Manager コレクション メンバーシップをインポートする]** を選択し、 **[保存]** をクリックします。  
   
    ![[Configuration Manager コレクション メンバーシップをインポートする] のオプションを含む SCCM のコンピューター グループ詳細設定のスクリーンショット。](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Configuration Manager のデータを表示する

Log Analytics 接続を Configuration Manager に追加し、Configuration Manager サービス接続ポイントのサイト システムの役割を実行しているコンピューターにエージェントをインストールすると、エージェントからのデータが Azure Monitor の Log Analytics ワークスペースに送信されます。 Azure Monitor で、Configuration Manager コレクションが[コンピューター グループ](./computer-groups.md)として表示されます。 グループは、 **[Configuration Manager]** ページの **[設定] > [コンピューター グループ]** で見ることができます。

コレクションがインポートされると、コレクションのメンバーシップを持つコンピューターが何台検出されたかを確認できます。 インポートされたコレクションの数を確認することもできます。

![[Configuration Manager コレクション メンバーシップをインポートする] のオプションが選択されていることを示す SCCM のコンピューター グループ詳細設定のスクリーンショット。](./media/collect-sccm/sccm-computer-groups02.png)

いずれかをクリックすると、ログ クエリ エディターが開き、インポートされたすべてのグループか、各グループに属しているすべてのコンピューターが表示されます。 [ログ検索](./log-query-overview.md)を使用すると、コレクション メンバーシップ データをさらに詳しく分析できます。

## <a name="next-steps"></a>次のステップ

[ログの検索](./log-query-overview.md)を使用して、Configuration Manager のデータに関する詳細な情報を表示します。