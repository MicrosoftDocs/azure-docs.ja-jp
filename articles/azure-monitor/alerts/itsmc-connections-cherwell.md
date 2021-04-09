---
title: Cherwell を IT Service Management Connector に接続する
description: この記事では、Cherwell を Azure Monitor の IT Service Management Connector (ITSMC) に接続して、ITSM 作業項目を一元的に監視して管理する方法に関する情報を提供します。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 0f488b047cf1323619d9603204877a55b413c295
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802473"
---
# <a name="connect-cherwell-with-it-service-management-connector"></a>Cherwell を IT Service Management Connector に接続する

この記事では、Cherwell インスタンスと Log Analytics の IT Service Management Connector (ITSMC) の間の接続を構成して、作業項目を一元的に管理する方法に関する情報を提供します。

> [!NOTE]
> 2020 年 10 月 1 日以降、新規のお客様が Cherwell ITSM を Azure Alert と統合することはできなくなります。 新しい ITSM 接続はサポートされません。
> 既存の ITSM 接続はサポートされます。

以降のセクションでは、Cherwell 製品を Azure の ITSMC に接続する方法について詳細に説明します。

## <a name="prerequisites"></a>前提条件

次の前提条件が満たされていることを確認してください。

- ITSMC がインストールされている。 詳細情報: [IT Service Management Connector ソリューションの追加](./itsmc-definition.md#add-it-service-management-connector)。
- クライアント ID が生成されている。 詳細情報: [Cherwell のクライアント ID を生成する](#generate-client-id-for-cherwell)。
- ユーザー ロール: 管理者。

## <a name="connection-procedure"></a>接続手順

Cherwell 接続を作成するには、次の手順に従います。

1. Azure Portal で、 **[すべてのリソース]** に移動し、**ServiceDesk(YourWorkspaceName)** を探します

2. **[ワークスペースのデータ ソース]** で、 **[ITSM 接続]** をクリックします。
    ![新しい接続](/azure/azure-monitor/alerts/media/itsmc-connections-scsm/add-new-itsm-connection.png)

3. 右側のウィンドウの上部にある **[追加]** をクリックします。

4. 以下の表に示す情報を入力し、 **[OK]** をクリックして接続を作成します。

> [!NOTE]
> これらのパラメーターはすべて必須です。

| **フィールド** | **説明** |
| --- | --- |
| **Connection Name**   | ITSMC に接続する Cherwell インスタンスの名前を入力します。  この名前は、後で ITSM の作業項目を構成したり、詳細なログ分析を確認したりするときに使用します。 |
| **パートナーの種類**   | **[Cherwell]** を選択します。 |
| **ユーザー名**   | ITSMC に接続できる Cherwell ユーザー名を入力します。 |
| **パスワード**   | このユーザー名に関連付けられているパスワードを入力します。 **注:** ユーザー名とパスワードは、認証トークンを生成するためにのみ使用され、ITSMC サービス内のどこにも格納されません。|
| **[サーバー URL]**   | ITSMC に接続する Cherwell インスタンスの URL を入力します。 |
| **クライアント ID**   | Cherwell インスタンスで生成した、この接続を認証するためのクライアント ID を入力します。   |
| **データ同期スコープ**   | ITSMC 経由で同期する Cherwell 作業項目を選択します。  これらの作業項目は、ログ分析にインポートされます。   **オプション:** インシデント、変更要求。 |
| **データの同期** | 過去何日分のデータを同期するのかについて、日数を入力します。 **上限**: 120 日。 |
| **Create new configuration item in ITSM solution (ITSM ソリューション内の新しい構成項目の作成)** | ITSM 製品で構成項目を作成する場合は、このオプションを選択します。 選択すると、ITSMC は影響を受ける CI を、サポートされている ITSM システムの構成項目として作成します (CI が存在しない場合)。 **既定**: 無効。 |

![Cherwell 接続](media/itsmc-connections-cherwell/itsm-connections-cherwell-latest.png)

**正常に接続され、同期された場合**:

- この Cherwell インスタンスで選択した作業項目は、Azure の **Log Analytics** にインポートされます。 IT Service Management Connector タイルで、これらの作業項目の概要を確認できます。

- この Cherwell インスタンスでは、Log Analytics アラート、ログ レコード、または Azure アラートからインシデントを作成できます。

詳細情報:[Azure アラートから ITSM 作業項目を作成する](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)。

### <a name="generate-client-id-for-cherwell"></a>Cherwell のクライアント ID を生成する

Cherwell のクライアント ID とキーを生成するには、次の手順に従います。

1. Cherwell インスタンスに管理者としてログインします。
2. **[セキュリティ]**  >  **[Edit REST API client settings] \(REST API クライアント設定の編集)** の順にクリックします。
3. **[Create new client] \(クライアントの新規作成)**  >  **[クライアント シークレット]** の順に選択します。

    ![Cherwell ユーザー ID](media/itsmc-connections-cherwell/itsmc-cherwell-client-id.png)

## <a name="next-steps"></a>次のステップ

* [ITSM Connector の概要](itsmc-overview.md)
* [Azure アラートから ITSM 作業項目を作成する](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [ITSM Connector での問題のトラブルシューティング](./itsmc-resync-servicenow.md)
