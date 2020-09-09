---
title: 一般的なエラーのトラブルシューティング - Azure Database for MySQL
description: Azure Database for MySQL サービスの新しいユーザーが遭遇する一般的な移行エラーのトラブルシューティングの方法について説明します
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: ebe9f936e3d0dfafec23842fcdbfd225995d546b
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719785"
---
# <a name="common-errors"></a>一般的なエラー

Azure Database for MySQL は、MySQL のコミュニティ バージョンを利用したフル マネージド サービスです。 マネージド サービス環境での MySQL エクスペリエンスは、独自の環境で MySQL を実行する場合と異なることがあります。 この記事では、初めて Azure Database for MySQL サービスへの移行や開発を行うときにユーザーが遭遇する可能性のある一般的なエラーについて説明します。

## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>SUPER 権限と DBA ロールの欠如によるエラー

SUPER 権限と DBA ロールはサービスでサポートされていません。 その結果、次のような一般的なエラーが発生する可能性があります。

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>ERROR 1419 (エラー 1419):  (SUPER 権限を持っておらず、バイナリ ログが有効になっています (より安全度の低い log_bin_trust_function_creators 変数を使用 "*することもできます*"))

上記のエラーは、以下のような関数およびトリガーの作成中、またはスキーマのインポート中に発生する可能性があります。 CREATE FUNCTION や CREATE TRIGGER などの DDL ステートメントはバイナリ ログに書き込まれるため、セカンダリ レプリカでそれらを実行することができます。 レプリカ SQL スレッドには完全な特権があり、これを利用して特権を昇格できます。 バイナリ ログが有効になっているサーバーのこの危険を防ぐために、MySQL エンジンでは、ストアド関数の作成者が通常の CREATE ROUTINE 特権に加えて SUPER 特権を保有していることが求められます。 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**解決方法**:このエラーを解決するには、ポータルの [[サーバー パラメーター]](howto-server-parameters.md) ブレードから log_bin_trust_function_creators を 1 に設定し、DDL ステートメントを実行するか、スキーマをインポートして目的のオブジェクトを作成し、作成後に log_bin_trust_function_creators パラメーターを以前の値に戻します。

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>ERROR 1227 (42000) at line 101 (エラー 1227 (42000)、行 101): Access denied; you need (at least one of) the SUPER privilege(s) for this operation. (アクセスが拒否されました。この操作には、(少なくとも 1 つの) SUPER 権限が必要です。) Operation failed with exitcode 1 (終了コード 1 で操作に失敗しました)

上記のエラーは、ダンプ ファイルのインポート中、または [definer](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html) を含むプロシージャの作成中に発生する可能性があります。 

**解決方法**:このエラーを解決するために、管理者ユーザーは、次の例のように GRANT コマンドを実行して、プロシージャを作成または実行する権限を付与できます。

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
または、次に示すように、definer を、インポート プロセスを実行している管理者ユーザーの名前に置き換えることもできます。

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```

## <a name="next-steps"></a>次の手順
探していた回答が見つからない場合は、次のことを検討してください。
- [Microsoft Q&A 質問ページ](https://docs.microsoft.com/answers/topics/azure-database-mysql.html)または [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql) で質問を投稿する。
- Azure Database for MySQL チーム [@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com) に電子メールを送信する。 このメール アドレスはテクニカル サポートのエイリアスではありません。
- Azure サポートに連絡し、[Azure portal からチケットを申請する](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 アカウントを使用して問題を修正するには、Azure Portal で[サポート要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出します。
- フィードバックを提供したり、新しい機能を要求したりするには、[UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) でエントリを作成します。
