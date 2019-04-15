---
title: SSH で SFTP サーバーに接続する - Azure Logic Apps | Microsoft Docs
description: SSH と Azure Logic Apps を使用して、SFTP サーバーのファイルの監視、作成、管理、および受信を行うタスクを自動化します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
tags: connectors
ms.date: 01/15/2019
ms.openlocfilehash: 660d785baf12052bddf5206d8641116c9ac606aa
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58575098"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>SSH と Azure Logic Apps を使用して SFTP ファイルの監視、作成、および管理を行う

[Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) プロトコルを使用して[セキュア ファイル転送プロトコル (SFTP)](https://www.ssh.com/ssh/sftp/) サーバー上のファイルを監視、作成、送信、および受信するタスクを自動化するために、Azure Logic Apps と SFTP-SSH コネクタを使用して、統合ワークフローの構築と自動化を行えます。 SFTP は、任意の信頼性の高いデータ ストリームを通して、ファイル アクセス、ファイル転送、およびファイル管理を提供するネットワーク プロトコルです。 次に、自動化できるタスクの例をいくつか示します。 

* ファイルの追加または変更を監視します。
* ファイルの取得、作成、コピー、名前変更、更新、一覧、および削除を行います。
* フォルダーを作成します。
* ファイルの内容とメタデータを取得します。
* アーカイブをフォルダーに抽出します。

SFTP サーバー上のイベントを監視し、出力を他のアクションで使用できるようにするトリガーを使用できます。 SFTP サーバーで各種のタスクを実行するアクションを使用できます。 ロジック アプリ内の他のアクションでも、SFTP アクションからの出力を使用するようにできます。 たとえば、SFTP サーバーからファイルを定期的に取得する場合は、Office 365 Outlook コネクタまたは Outlook.com コネクタを使用して、これらのファイルやその内容に関するメールのアラートを送信できます。
ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

## <a name="limits"></a>制限

* SFTP-SSH アクションでは、1 GB ではなく "*50 MB 単位*" でデータを管理することで、"*1 GB 以下*" のファイルを読み書きできます。

* ファイルが "*1 GB を超える*" 場合、アクションで[メッセージ チャンク](../logic-apps/logic-apps-handle-large-messages.md)を使用できます。 現在、SFTP-SSH トリガーではチャンクはサポートされていません。

その他の違いについては、次のセクションの「[SFTP-SSH と SFTP を比較する](#comparison)」で確認してください。

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>SFTP-SSH と SFTP を比較する

以下に、SFTP-SSH コネクタと SFTP-SSH コネクタのその他の主な違いを、SFTP コネクタは以下の機能を備えているという形で示します。

* .NET をサポートする Secure Shell (SSH) オープン ソース ライブラリである <a href="https://github.com/sshnet/SSH.NET" target="_blank">**SSH.NET** </a>ライブラリを使用します。

  > [!NOTE]
  >
  > SFTP-SSH コネクタは、以下に示す秘密キー、形式、アルゴリズム、およびフィンガープリント*のみ*をサポートしています。
  >
  > * **秘密キーの形式**: OpenSSH 形式と ssh.com 形式の両方の RSA (Rivest Shamir Adleman) キーと DSA (Digital Signature Algorithm) キー
  > * **暗号化アルゴリズム**: DES-EDE3-CBC、DES-EDE3-CFB、DES-CBC、AES-128-CBC、AES-192-CBC、AES-256-CBC
  > * **フィンガープリント**: MD5

* SFTP コネクタと比べて、アクションでは、"*1 GB まで*" のファイルを読み書きできます。ただし、データは 1 GB 単位ではなく 50 MB 単位で処理されます。 ファイルが 1 GB を超える場合、アクションで[メッセージ チャンク](../logic-apps/logic-apps-handle-large-messages.md)を使用することもできます。 現在、SFTP-SSH トリガーではチャンクはサポートされていません。

* SFTP サーバーの指定されたパスにフォルダーを作成する**フォルダーの作成**アクションを提供します。

* SFTP サーバーのファイルの名前を変更する**ファイル名の変更**アクションを提供します。

* SFTP サーバーへの接続を*最大 1 時間*キャッシュします。これによりパフォーマンスが向上し、サーバーへの接続試行数が減少します。 このキャッシュ動作の期間を設定するには、SFTP サーバーの SSH 構成で、<a href="https://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank">**ClientAliveInterval**</a> プロパティを編集します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。 

* SFTP サーバーのアドレスとアカウントの資格情報。これにより、ロジック アプリが SFTP アカウントにアクセスできます。 SSH 秘密キーと SSH 秘密キーのパスワードにもアクセスできる必要があります。 

  > [!IMPORTANT]
  >
  > SFTP-SSH コネクタは、以下に示す秘密キー形式、アルゴリズム、およびフィンガープリント*のみ*をサポートしています。
  > 
  > * **秘密キーの形式**: OpenSSH 形式と ssh.com 形式の両方の RSA (Rivest Shamir Adleman) キーと DSA (Digital Signature Algorithm) キー
  > * **暗号化アルゴリズム**: DES-EDE3-CBC、DES-EDE3-CFB、DES-CBC、AES-128-CBC、AES-192-CBC、AES-256-CBC
  > * **フィンガープリント**: MD5
  >
  > ロジック アプリを作成している場合は、必要な SFTP-SSH トリガーまたはアクションを追加した後、SFTP サーバーの接続情報を指定する必要があります。 
  > SSH 秘密キーを使おうとしている場合は、SSH 秘密キー ファイルからキーを***コピー***し、そのキーを接続の詳細に***貼り付ける***ようにしてください。***キーは手動で入力または編集しないでください***。そのようにすると、接続が失敗することがあります。 
  > 詳細については、この記事で後述する手順を参照してください。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

* SFTP アカウントにアクセスするロジック アプリ。 SFTP-SSH トリガーで開始するには、[空のロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 SFTP-SSH アクションを使用するには、**繰り返し**トリガーなど、別のトリガーでロジック アプリを開始します。

## <a name="connect-to-sftp-with-ssh"></a>SSH で SFTP に接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure portal](https://portal.azure.com) にサインインし、ロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. 空のロジック アプリの場合は、検索ボックスに、フィルターとして「sftp ssh」と入力します。 トリガーの一覧で、目的のトリガーを選択します。 

   または

   既存のロジック アプリの場合、アクションを追加する最後のステップの下で、**[新しいステップ]** を選択します。 
   検索ボックスに、フィルターとして「sftp ssh」と入力します。 
   アクションの一覧で、目的のアクションを選択します。

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 
   表示されるプラス記号 (**+**) を選択し、**[アクションの追加]** を選択します。

1. 接続に必要な詳細を指定します。

   > [!IMPORTANT] 
   >
   > **SSH 秘密キー** プロパティに SSH 秘密キーを入力する場合は、次の追加の手順に従ってください。これは、このプロパティの完全で正しい値を確実に指定する助けになります。 
   > 無効なキーを指定すると、接続が失敗します。
   
   任意のテキスト エディターを使用できますが、次に例として、Notepad.exe を使用してキーを正しくコピーして貼り付ける方法を示す手順の例を示します。
    
   1. テキスト エディターで SSH 秘密キーのファイルを開きます。 
   これらの手順では、例としてメモ帳を使用します。

   1. メモ帳の **[編集]** メニューで、**[すべて選択]** を選択します。

   1. **[編集]** > **[コピー]** を選択します。

   1. 追加した SFTP-SSH トリガーまたはアクションで、コピーした*完全な*キーを **SSH 秘密キー** プロパティ (これは、複数行をサポートします) に貼り付けます。 
   キーは***貼り付けるようにしてください***。 ***キーを手動で入力または編集しないでください***。

1. 接続の詳細の入力を完了したら、**[作成]** を選択します。

1. ここから、選択したトリガーまたはアクションのために必要な詳細を指定し、ロジック アプリのワークフローの構築を続けます。

## <a name="trigger-limits"></a>トリガーの制限

SFTP-SSH は、SFTP ファイル システムをポーリングし、前回のポーリング以降に変更されたファイルを検索することで動作をトリガーします。 一部のツールでは、ファイルが変更されたときにタイムスタンプを保持できます。 これらの場合は、トリガーが動作できるように、この機能を無効にする必要があります。 一般的な設定をいくつか次に示します。

| SFTP クライアント | Action | 
|-------------|--------| 
| Winscp | **[オプション]** > **[ユーザー設定]** > **[転送]** > **[編集]** > **[Preserve timestamp] (タイムスタンプを保持する)** > **[無効]** に移動する |
| FileZilla | **[転送]** > **[Preserve timestamps of transferred files] (転送されたファイルのタイムスタンプを保持する)** > **[無効]** に移動する | 
||| 

トリガーによって新しいファイルが検出されると、トリガーはその新しいファイルが完了していて、すべて書き込まれていることを確認します。 たとえば、トリガーがファイル サーバーを確認している際に、ファイルの進行状態が変化する場合があります。 部分的に書き込まれたファイルが返されることなないように、トリガーは最新の変更を含むファイルのタイムスタンプをメモしますが、すぐにそのファイルを返すことはありません。 トリガーは、もう一度サーバーをポーリングしてファイルを返します。 場合によっては、この動作によって、最大でトリガーのポーリング間隔が 2 倍となる遅延が生じる場合があります。 

ファイルの内容を要求するとき、トリガーでは 50 MB より大きいファイルは取得されません。 50 MB より大きいファイルを取得するには、次のパターンに従います。 

* **ファイルが追加または変更されたとき (プロパティのみ)** といった、ファイルのプロパティを返すトリガーを使用します。

* **パスを使用してファイルの内容を取得する**といった、完全なファイルを読み取るアクションを使用するトリガーに従い、アクションで[メッセージ チャンク](../logic-apps/logic-apps-handle-large-messages.md)を使用します。

## <a name="examples"></a>例

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - SSH トリガー: When a file is added or modified (ファイルの追加または変更時)

このトリガーは、SFTP サーバーでファイルが追加または変更されたときにロジック アプリ ワークフローを開始します。 たとえば、ファイルの内容をチェックし、内容が指定された条件を満たすかどうかに基づいてその内容を取得する条件を追加できます。 その後、ファイルの内容を取得し、その内容を SFTP サーバーのフォルダーに格納するアクションを追加できます。 

**企業での使用例**: このトリガーを使用して、SFTP フォルダーに顧客からの注文を表す新しいファイルがあるかどうかを監視できます。 その後、さらに処理するために注文の内容を取得し、その注文を注文データベース内に格納できるように、**ファイルの内容を取得する**などの SFTP アクションを使用できます。

ファイルの内容を要求するとき、トリガーでは 50 MB より大きいファイルは取得されません。 50 MB より大きいファイルを取得するには、次のパターンに従います。 

* **ファイルが追加または変更されたとき (プロパティのみ)** といった、ファイルのプロパティを返すトリガーを使用します。

* **パスを使用してファイルの内容を取得する**といった、完全なファイルを読み取るアクションを使用するトリガーに従い、アクションで[メッセージ チャンク](../logic-apps/logic-apps-handle-large-messages.md)を使用します。

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP - SSH アクション: パスを使用してコンテンツを取得する

このアクションは、SFTP サーバー上のファイルの内容を取得します。 そのため、たとえば前の例のトリガーと、ファイルの内容が満たす必要がある条件を追加できます。 条件が true であれば、内容を取得するアクションを実行できます。 

ファイルの内容を要求するとき、トリガーでは 50 MB より大きいファイルは取得されません。 50 MB より大きいファイルを取得するには、次のパターンに従います。 

* **ファイルが追加または変更されたとき (プロパティのみ)** といった、ファイルのプロパティを返すトリガーを使用します。

* **パスを使用してファイルの内容を取得する**といった、完全なファイルを読み取るアクションを使用するトリガーに従い、アクションで[メッセージ チャンク](../logic-apps/logic-apps-handle-large-messages.md)を使用します。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、コネクタの[リファレンス ページ](/connectors/sftpconnector/)を参照してください。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
