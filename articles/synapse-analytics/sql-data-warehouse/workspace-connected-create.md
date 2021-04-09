---
title: Synapse ワークスペース機能を有効にする
description: このドキュメントでは、ユーザーが既存の専用 SQL プール (以前の SQL DW) 上で Synapse ワークスペース機能を有効にするにはどうすればよいかについて説明します。
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 769ca4cf0ecbdba5fd80d977eb69c8a4f58df55f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98071189"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>専用 SQL プール (以前の SQL DW) 上で Synapse ワークスペース機能を有効にする

SQL データ ウェアハウスのすべてのユーザーが、Synapse Studio とワークスペースを介して、既存の専用 SQL プール (以前の SQL DW) インスタンスにアクセスし、それを使用できるようになりました。 ユーザーは、オートメーション、接続、ツールに影響を与えることなく、Synapse Studio およびワークスペースを使用できます。 この記事では、既存の Azure Synapse Analytics ユーザーが既存の専用 SQL プール (以前の SQL DW) 上で Synapse ワークスペース機能を有効にするにはどうすればよいかについて説明します。 ユーザーは、Synapse ワークスペースと Studio を介して提供されるようになった新しい豊富な機能を活用することで、ご自分の既存の分析ソリューションを拡張することができます。   

## <a name="prerequisites"></a>前提条件
データ ウェアハウス上で Synapse ワークスペース機能を有効にするには、事前に以下を確保しておく必要があります。
- SQL 論理サーバー上でホストされる SQL リソースを作成して管理する権限。
- Azure Synapse リソースを作成する権限。
- 論理サーバー上で識別される Azure Active Directory 管理者

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>既存の専用 SQL プール (以前の SQL DW) に対して Synapse ワークスペース機能を有効にする

サポートされているリージョンにある既存の専用 SQL プール (以前の SQL DW) 上で、Synapse ワークスペース機能を有効にすることができます。 この機能は Azure portal を通してのみ使用できます。

既存のデータ ウェアハウス用の Synapse ワークスペースを作成するには、次の手順に従います。
1. 既存の専用 SQL プール (以前の SQL DW) を選択し、[概要] ページを開きます。
2. 上部のメニュー バーまたはすぐ下のメッセージで、 **[新しいワークスペース]** を選択します。
3. **[新しい Azure Synapse ワークスペースの作成]** ページで新しい Synapse ワークスペースを介して利用できるようになったデータ ウェアハウスのリストを確認した後で **[続行]** をクリックして先に進みます。
4. [基本] ページでは、既存の専用 SQL プールの **[プロジェクトの詳細]** セクションに、論理サーバーの下にデプロイされているのと同じ **サブスクリプション** および **リソース グループ** があらかじめ設定されている必要があります。 さらに、 **[ワークスペースの詳細]** の **[ワークスペース名]** には、プロビジョニング プロセス中に新しい Synapse ワークスペースと論理サーバーの間の接続を確実に作成できるように、SQL 論理サーバーと同じ名前およびリージョンがあらかじめ設定されています。 プロビジョニング後もこの接続を保持する必要があります。そうすることで、ワークスペースと Studio を介して専用の SQL プール (以前の SQL DW) インスタンスに引き続き確実にアクセスすることができます。
5. [Data Lake Storage Gen 2 の選択] に移動します。
6. **[新規作成]** を選択するか、既存の **Data Lake Storage Gen2** を選択してから **[Next:Networking]\(次へ : ネットワーク\)** を選択します。
7. ご利用の **サーバーレス インスタンス** 用の **SQL 管理者パスワード** を選択します。 このパスワードを変更しても、論理サーバーの SQL 資格情報は更新も変更もされません。 システム定義のパスワードを使用する場合、これらのフィールドは空のままとします。 このパスワードは、新しいワークスペース内でいつでも変更できます。 管理者名は、SQL Server で使用されているものと同じにする必要があります。
8. 目的の **[ネットワークの設定]** を選択してから、 **[確認と作成]** を選択してワークスペースのプロビジョニングを開始します。
9. **[リソースに移動]** を選択して、新しいワークスペースを開きます。

    > [!NOTE]
    > 論理サーバー上でホストされているすべての専用 SQL プール (以前の SQL DW) インスタンスが、新しいワークスペースを介して使用できます。

## <a name="post-provisioning-steps"></a>プロビジョニング後の手順
既存の専用 SQL プール (以前の SQL DW) インスタンスに Synapse Studio を介して確実にアクセスできるようにするために、次の手順を行う必要があります。
1. Synapse ワークスペースの [概要] ページで、 **[接続しているサーバー]** を選択します。 **[接続しているサーバー]** を選択すると、ご利用のデータ ウェアハウスをホストする接続済みの SQL 論理サーバーに移動します。 必須メニューで、 **[接続しているサーバー**] を選択します。
2. **[ファイアウォールと仮想ネットワーク]** を開き、クライアント IP または事前に定義された IP 範囲に論理サーバーへのアクセス権があることを保証します。
3. **[Active Directory 管理者]** を開き、論理サーバー上で AAD 管理者が設定されていることを保証します。
4. 論理サーバー上でホストされている専用 SQL プール (以前の SQL DW) インスタンスのいずれかを選択します。 [概要] ページで、 **[Synapse Studio の起動]** を選択するか、または [[Synapse Studio にサインイン]](https://web.azuresynapse.net) にアクセスして、使用するワークスペースにサインインします。

5. **[データ ハブ]** を開き、オブジェクト エクスプローラーで専用 SQL プールを展開します。これにより、データ ウェアハウスにアクセスしていてクエリを確実に実行できます。

    > [!NOTE] 
    > 接続済みワークスペースはいつでも削除できます。 ワークスペースを削除しても、接続されている専用 SQL プール (以前の SQL DW) は削除されません。 削除操作が完了したら、専用 SQL プール (以前の SQL DW) でワークスペース機能を再有効化できます。

## <a name="next-steps"></a>次のステップ
[Synapse ワークスペースと Studio](../get-started.md) の概要。
