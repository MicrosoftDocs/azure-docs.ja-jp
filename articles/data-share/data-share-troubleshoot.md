---
title: Azure Data Share のトラブルシューティング
description: Azure Data Share でデータ共有を作成または受信するときに発生する、招待の問題やエラーを解決する方法について説明します。
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 08/14/2020
ms.openlocfilehash: c68c9dc961475d6916b1f00e7d4f596bfd8c77dd
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2020
ms.locfileid: "88257795"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Azure Data Share での一般的な問題のトラブルシューティング 

この記事では、Azure Data Share の一般的な問題のトラブルシューティングを行う方法を示します。 

## <a name="azure-data-share-invitations"></a>Azure Data Share の招待 

場合によっては、新しいユーザーが送信されたメール招待状の **[Accept Invitation]\(招待を承諾する\)** をクリックしたときに、空の招待リストが表示されることがあります。 

![招待なし](media/no-invites.png)

この原因としては、次の理由が考えられます。

* **Azure Data Share サービスが、Azure テナントの Azure サブスクリプションのリソース プロバイダーとして登録されていません。** この問題は、Azure テナントに Data Share リソースがない場合に発生します。 Azure Data Share リソースを作成すると、Azure サブスクリプションにリソース プロバイダーが自動的に登録されます。 また、次の手順に従って、Data Share サービスを手動で登録することもできます。 これらの手順を完了するには、Azure 共同作成者ロールを持っている必要があります。

    1. Azure portal で、 **[サブスクリプション]** に移動します。
    1. Azure Data Share リソースの作成に使用するサブスクリプションを選択します
    1. **[リソース プロバイダー]** をクリックします
    1. 「**Microsoft.DataShare**」を検索します
    1. **[登録]** をクリックします。 

    これらの手順を完了するには、Azure サブスクリプションに対する [Azure 共同作成者ロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)が必要です。 

* **Azure ログイン用メールではなくメール エイリアスに招待が送信されています。** Azure Data Share サービスを登録している、または Azure テナントに既に Data Share リソースを作成しているにもかかわらず、まだ招待が表示されない場合、その原因として、プロバイダーが受信者として Azure ログイン用メール アドレスではなくメール エイリアスを入力した可能性があります。 データ プロバイダーに問い合わせて、招待がメール エイリアスではなく Azure ログイン用メール アドレスに送信されていることを確認してください。

* **招待が既に承諾されています。** メールに記載されているリンクを使用すると、Azure portal の [データ共有への招待] ページに移動します。このページには保留中の招待のみが表示されます。 その招待を既に承諾している場合は、[データ共有への招待] ページに表示されなくなります。 招待を承諾した Data Share リソースに進み、受信した共有を表示して、ターゲットの Azure Data Explorer クラスター設定を構成します。

## <a name="error-when-creating-or-receiving-a-new-share"></a>新しい共有を作成または受信するときのエラー

"データセットを追加できませんでした"

"データセットのマップに失敗しました"

"データ共有リソース x に対して y へのアクセスを許可できません"

"x に対する適切なアクセス許可がありません"

"選択された 1 つ以上のリソースに対する書き込みアクセス許可を Azure Data Share アカウントに追加できませんでした"

新しい共有の作成時、またはデータセットのマップ時に上記のエラーのいずれかが発生した場合、その原因としては、Azure データ ストアに対するアクセス許可が不足していることが考えられます。 必要なアクセス許可については、[ロールと要件](concepts-roles-permissions.md)に関する記事をご覧ください。 

Azure データ ストアからデータを共有または受信するためには、書き込みアクセス許可が必要です。これは通常、共同作成者ロールにあります。 

初めて Azure データ ストアからデータを共有または受信する場合は、"*Microsoft.Authorization/ロールの割り当て/書き込み*" のアクセス許可も必要です。これは通常、所有者ロールにあります。 自分で Azure データ ストア リソースを作成した場合でも、自動的に自分がそのリソースの所有者になることはありません。 適切なアクセス許可を使用すると、Azure Data Share サービスによって、データ共有リソースのマネージド ID に、データ ストアへのアクセス権が自動的に付与されます。 このプロセスは、有効になるまでに数分かかる場合があります。 この遅延のためにエラーが発生する場合は、数分後にもう一度やり直してください。

SQL ベースの共有には、追加のアクセス許可が必要です。 詳細については、「SQL ベースの共有のトラブルシューティング」をご覧ください。

## <a name="troubleshooting-sql-based-sharing"></a>SQL ベースの共有のトラブルシューティング

"ユーザー x は SQL Database に存在しません"

SQL ベースのソースからデータセットを追加するときにこのエラーが発生する場合は、SQL Database で Azure Data Share マネージド ID のユーザーを作成しなかったことが原因である可能性があります。  この問題を解決するには、次のスクリプトを実行します。

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
SQL ベースのターゲットにデータセットをマップするときにこのエラーが発生する場合は、SQL Server で Azure Data Share マネージド ID のユーザーを作成しなかったことが原因である可能性があります。  この問題を解決するには、次のスクリプトを実行します。

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
*<share_acc_name>* は、ご自分の Data Share リソースの名前であることに注意してください。      

[データの共有](share-your-data.md)および[データの受け入れと受信](subscribe-to-data-share.md)に関するチュートリアルに記載されているすべての前提条件に従っていることを確認してください。

## <a name="snapshot-failed"></a>スナップショットの失敗
スナップショットは、さまざまな理由により失敗する可能性があります。 詳細なエラー メッセージを確認するには、スナップショットの開始時刻をクリックした後、各データセットの状態をクリックします。 

エラー メッセージがアクセス許可に関連している場合は、Data Share サービスに必要なアクセス許可があることを確認します。 詳細については、[ロールと要件](concepts-roles-permissions.md)に関する記事をご覧ください。 初めてスナップショットを取得する場合は、Azure データ ストアへのアクセス権が Data Share リソースに付与されるまでに数分かかることがあります。 数分待ってからもう一度やり直してください。

## <a name="next-steps"></a>次のステップ

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。 

データを受信する方法については、[データの受け入れと受信](subscribe-to-data-share.md)に関するチュートリアルをご覧ください。

