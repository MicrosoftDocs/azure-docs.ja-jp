---
title: Azure Blockchain Workbench のデータベース ビュー
description: Azure Blockchain Workbench Preview で使用可能な SQL DB データベース ビューの概要。
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: e715bd2297c1e051ae92b15d73c945c9e80c3d94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87003323"
---
# <a name="azure-blockchain-workbench-database-views"></a>Azure Blockchain Workbench のデータベース ビュー

Azure Blockchain Workbench Preview は、分散型台帳から *オフチェーン* SQL DB データベースにデータを配信します。 オフチェーン データベースにより、SQL や既存のツール ([SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) など) を使用して、ブロックチェーン データと対話できるようになります。

Azure Blockchain Workbench に備わった一連のデータベース ビューを使用すると、クエリの実行時に役立つデータにアクセスできます。 これらのビューは、迅速なレポートの作成や分析を簡単にするために頻繁に非正規化され、それ以外の場合は、既存のツールを使ってブロックチェーン データを使用し、データベースを再トレーニングする必要がありません。

このセクションでは、データベース ビューと、このビューに含まれるデータの概要を説明します。

> [!NOTE]
> これらのビュー内に含まれないデータベース内のデータベース テーブルは直接使用できますが、サポートされていません。
>

## <a name="vwapplication"></a>vwApplication

このビューは、Azure Blockchain Workbench にアップロードされた **アプリケーション** の詳細を示します。

| 名前                             | Type          | Null にできるか | 説明                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | INT           | いいえ          | アプリケーションの一意識別子です |
| ApplicationName                  | nvarchar(50)  | いいえ          | アプリケーションの名前 |
| ApplicationDescription           | nvarchar(255) | はい         | アプリケーションの説明 |
| ApplicationDisplayName           | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示される名前 |
| ApplicationEnabled               | bit           | いいえ          | アプリケーションが現在有効になっているかどうかを示します<br /> **注:** データベース内でアプリケーションを無効として反映できても、関連付けられたコントラクトはブロックチェーンにとどまり、これらのコントラクトに関するデータはデータベースに残されます。 |
| UploadedDtTm                     | datetime2(7)  | いいえ          | コントラクトがアップロードされた日時 |
| UploadedByUserId                 | INT           | いいえ          | アプリケーションをアップロードしたユーザーの ID です |
| UploadedByUserExternalId         | nvarchar(255) | いいえ          | アプリケーションをアップロードしたユーザーの外部識別子です。 既定では、この ID コンソーシアムの Azure Active Directory からのユーザーです。                                                                                                |
| UploadedByUserProvisioningStatus | INT           | いいえ          | ユーザーのプロビジョニング プロセスの現在の状態を示します。 次のいずれかの値になります。 <br />0 – API によってユーザーが作成されている<br />1 – キーがデータベース内のユーザーに関連付けられている<br />2 – ユーザーが完全にプロビジョニングされている                         |
| UploadedByUserFirstName          | nvarchar(50)  | はい         | コントラクトをアップロードしたユーザーの名 |
| UploadedByUserLastName           | nvarchar(50)  | はい         | コントラクトをアップロードしたユーザーの姓 |
| UploadedByUserEmailAddress       | nvarchar(255) | はい         | コントラクトをアップロードしたユーザーの電子メール アドレス |

## <a name="vwapplicationrole"></a>vwApplicationRole

このビューは、Azure Blockchain Workbench アプリケーションで定義されているロールの詳細を示します。

たとえば、*Asset Transfer* アプリケーションでは、*Buyer* や *Seller* などのロールが定義されます。

| 名前                   | Type             | Null にできるか | 説明                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | INT              | いいえ          | アプリケーションの一意識別子です           |
| ApplicationName        | nvarchar(50)     | いいえ          | アプリケーションの名前                       |
| ApplicationDescription | nvarchar(255)    | はい         | アプリケーションの説明                  |
| ApplicationDisplayName | nvarchar(255)    | いいえ          | ユーザー インターフェイスに表示される名前      |
| RoleId                 | INT              | いいえ          | アプリケーションのロールの一意識別子 |
| RoleName               | nvarchar50)      | いいえ          | ロールの名前                              |
| RoleDescription        | description(255) | はい         | ロールの説明                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

このビューは、Azure Blockchain Workbench アプリケーションで定義されているロールと、ロールに関連付けられたユーザーの詳細を示します。

たとえば、*Asset Transfer* アプリケーションでは、*John Smith* は *Buyer* ロールに関連付けられています。

| 名前                       | Type          | Null にできるか | 説明                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | INT           | いいえ          | アプリケーションの一意識別子です                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | いいえ          | アプリケーションの名前                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar(255) | はい         | アプリケーションの説明                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示される名前                                                                                                                                                                                          |
| ApplicationRoleId          | INT           | いいえ          | アプリケーションのロールの一意識別子                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | いいえ          | ロールの名前                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | はい         | ロールの説明                                                                                                                                                                                                             |
| UserId                     | INT           | いいえ          | ロールに関連付けられたユーザーの ID です |
| UserExternalId             | nvarchar(255) | いいえ          | ロールに関連付けられているユーザーの外部識別子。 既定では、この ID コンソーシアムの Azure Active Directory からのユーザーです。                                                                     |
| UserProvisioningStatus     | INT           | いいえ          | ユーザーのプロビジョニング プロセスの現在の状態を示します。 次のいずれかの値になります。 <br />0 – API によってユーザーが作成されている<br />1 – キーがデータベース内のユーザーに関連付けられている<br />2 – ユーザーが完全にプロビジョニングされている |
| UserFirstName              | nvarchar(50)  | はい         | ロールに関連付けられているユーザーの名 |
| UserLastName               | nvarchar(255) | はい         | ロールに関連付けられているユーザーの姓 |
| UserEmailAddress           | nvarchar(255) | はい         | ロールに関連付けられているユーザーの電子メール アドレス |

## <a name="vwconnectionuser"></a>vwConnectionUser

このビューは、Azure Blockchain Workbench で定義された接続と、接続に関連付けられたユーザーの詳細を示します。 各接続について、このビューには次のデータが含まれます。

-   関連付けられている台帳の詳細
-   関連付けられているユーザーの情報

| 名前                     | Type          | Null にできるか | 説明                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId             | INT           | いいえ          | Azure Blockchain Workbench の接続の一意識別子 |
| ConnectionEndpointUrl    | nvarchar(50)  | いいえ          | 接続のエンドポイント URL |
| ConnectionFundingAccount | nvarchar(255) | はい         | 該当する場合、接続に関連付けられている資金調達アカウント |
| LedgerId                 | INT           | いいえ          | 台帳の一意識別子 |
| LedgerName               | nvarchar(50)  | いいえ          | 台帳の名前 |
| LedgerDisplayName        | nvarchar(255) | いいえ          | UI に表示する台帳の名前 |
| UserId                   | INT           | いいえ          | 接続に関連付けられているユーザーの ID |
| UserExternalId           | nvarchar(255) | いいえ          | 接続に関連付けられているユーザーの外部識別子。 既定では、この ID コンソーシアムの Azure Active Directory からのユーザーです。 |
| UserProvisioningStatus   | INT           | いいえ          |ユーザーのプロビジョニング プロセスの現在の状態を示します。 次のいずれかの値になります。 <br />0 – API によってユーザーが作成されている<br />1 – キーがデータベース内のユーザーに関連付けられている<br />2 – ユーザーが完全にプロビジョニングされている |
| UserFirstName            | nvarchar(50)  | はい         | 接続に関連付けられているユーザーの名 |
| UserLastName             | nvarchar(255) | はい         | 接続に関連付けられているユーザーの姓 |
| UserEmailAddress         | nvarchar(255) | はい         | 接続に関連付けられているユーザーの電子メール アドレス |

## <a name="vwcontract"></a>vwContract

このビューは、デプロイされたコントラクトの詳細を示します。 各コントラクトについて、このビューには次のデータが含まれます。

-   関連付けられたアプリケーション定義
-   関連付けられたワークフロー定義
-   関数の関連付けられた台帳の実装
-   アクションを開始したユーザーについての詳細
-   ブロックチェーン ブロックとトランザクションに関連する詳細

| 名前                                     | Type           | Null にできるか | 説明                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId                             | INT            | いいえ          | Azure Blockchain Workbench の接続の一意識別子。                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | いいえ          | 接続のエンドポイント URL |
| ConnectionFundingAccount                 | nvarchar(255)  | はい         | 該当する場合、接続に関連付けられている資金調達アカウント |
| LedgerId                                 | INT            | いいえ          | 台帳の一意識別子 |
| LedgerName                               | nvarchar(50)   | いいえ          | 台帳の名前 |
| LedgerDisplayName                        | nvarchar(255)  | いいえ          | UI に表示する台帳の名前 |
| ApplicationId                            | INT            | いいえ          | アプリケーションの一意識別子です |
| ApplicationName                          | nvarchar (50)  | いいえ          | アプリケーションの名前 |
| ApplicationDisplayName                   | nvarchar (255) | いいえ          | ユーザー インターフェイスに表示される名前 |
| ApplicationEnabled                       | bit            | いいえ          | アプリケーションが現在有効になっているかどうかを示します。<br /> **注:** データベース内でアプリケーションを無効として反映できても、関連付けられたコントラクトはブロックチェーンにとどまり、これらのコントラクトに関するデータはデータベースに残されます。  |
| WorkflowId                               | INT            | いいえ          | コントラクトに関連付けられているワークフローの一意識別子 |
| WorkflowName                             | nvarchar(50)   | いいえ          | コントラクトに関連付けられているワークフローの名前 |
| WorkflowDisplayName                      | nvarchar(255)  | いいえ          | ユーザー インターフェイスに表示するためにコントラクトに関連付けられているワークフローの名前 |
| WorkflowDescription                      | nvarchar(255)  | はい         | コントラクトに関連付けられているワークフローの説明 |
| ContractCodeId                           | INT            | いいえ          | コントラクトに関連付けられているコントラクト コードの一意識別子 |
| ContractFileName                         | INT            | いいえ          | このワークフローのスマート コントラクト コードを含むファイルの名前。 |
| ContractUploadedDtTm                     | INT            | いいえ          | コントラクト コードがアップロードされた日時 |
| ContractId                               | INT            | いいえ          | コントラクトの一意識別子 |
| ContractProvisioningStatus               | INT            | いいえ          | コントラクトのプロビジョニング プロセスの現在の状態を示します。 次のいずれかの値になります。 <br />0 – データベースで API によってコントラクトが作成されている<br />1 – コントラクトが台帳に送信されている<br />2 – コントラクトが台帳に正常にデプロイされている<br />3 または 4 - 台帳へのコントラクトのデプロイが失敗した<br />5 – コントラクトが台帳に正常にデプロイされた <br /><br />バージョン 1.5 以降、値 0 ～ 5 がサポートされています。 現在のリリースでの下位互換性のためには、値 0 ～ 2 のみをサポートするビュー **vwContractV0** を使用できます。 |
| ContractLedgerIdentifier                 | nvarchar (255) |             | コントラクトをデプロイしたユーザーの電子メール アドレス |
| ContractDeployedByUserId                 | INT            | いいえ          | コントラクトをデプロイしたユーザーの外部識別子。 既定では、この ID はユーザーの Azure Active Directory ID を表す guid です。                                                                                                          |
| ContractDeployedByUserExternalId         | nvarchar(255)  | いいえ          | コントラクトをデプロイしたユーザーの外部識別子。 既定では、この ID はユーザーの Azure Active Directory ID を表す guid です。                                                                                                         |
| ContractDeployedByUserProvisioningStatus | INT            | いいえ          | ユーザーのプロビジョニング プロセスの現在の状態を示します。 次のいずれかの値になります。 <br />0 – API によってユーザーが作成されている<br />1 – キーがデータベース内のユーザーに関連付けられている <br />2 – ユーザーが完全にプロビジョニングされている                     |
| ContractDeployedByUserFirstName          | nvarchar(50)   | はい         | コントラクトをデプロイしたユーザーの名 |
| ContractDeployedByUserLastName           | nvarchar(255)  | はい         | コントラクトをデプロイしたユーザーの姓 |
| ContractDeployedByUserEmailAddress       | nvarchar(255)  | はい         | コントラクトをデプロイしたユーザーの電子メール アドレス |

## <a name="vwcontractaction"></a>vwContractAction

このビューは、コントラクトで行われたアクションに関連する情報の大半を表し、一般的なレポート シナリオを簡単にするために設計されています。 行った各アクションについて、このビューには次のデータが含まれます。

-   関連付けられたアプリケーション定義
-   関連付けられたワークフロー定義
-   関連付けられているスマート コントラクト関数とパラメーター定義
-   関数の関連付けられた台帳の実装
-   パラメーターに指定された特定のインスタンスの値
-   アクションを開始したユーザーについての詳細
-   ブロックチェーン ブロックとトランザクションに関連する詳細

| 名前                                     | Type          | Null にできるか | 説明                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | INT           | いいえ          | アプリケーションの一意識別子です |
| ApplicationName                          | nvarchar(50)  | いいえ          | アプリケーションの名前 |
| ApplicationDisplayName                   | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示される名前 |
| ApplicationEnabled                       | bit           | いいえ          | このフィールドは、アプリケーションが現在有効になっているかどうかを示します。 注 – データベース内でアプリケーションを無効として反映できても、関連付けられたコントラクトはブロックチェーンにとどまり、これらのコントラクトに関するデータはデータベースに残されます。                                                  |
| WorkflowId                               | INT           | いいえ          | ワークフローの一意識別子 |
| WorkflowName                             | nvarchar(50)  | いいえ          | ワークフローの名前 |
| WorkflowDisplayName                      | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示するワークフローの名前 |
| WorkflowDescription                      | nvarchar(255) | はい         | ワークフローの説明 |
| ContractId                               | INT           | いいえ          | コントラクトの一意識別子 |
| ContractProvisioningStatus               | INT           | いいえ          | コントラクトのプロビジョニング プロセスの現在の状態を示します。 次のいずれかの値になります。 <br />0 – データベースで API によってコントラクトが作成されている<br />1 – コントラクトが台帳に送信されている<br />2 – コントラクトが台帳に正常にデプロイされている<br />3 または 4 - 台帳へのコントラクトのデプロイが失敗した<br />5 – コントラクトが台帳に正常にデプロイされた <br /><br />バージョン 1.5 以降、値 0 ～ 5 がサポートされています。 現在のリリースでの下位互換性のためには、値 0 ～ 2 のみをサポートするビュー **vwContractActionV0** を使用できます。 |
| ContractCodeId                           | INT           | いいえ          | コントラクトのコード実装の一意識別子 |
| ContractLedgerIdentifier                 | nvarchar(255) | はい         | 特定の分散型台帳のスマート コントラクトのデプロイされたバージョンに関連付けられている一意識別子。 Ethereum など。 |
| ContractDeployedByUserId                 | INT           | いいえ          | コントラクトをデプロイしたユーザーの一意識別子 |
| ContractDeployedByUserFirstName          | nvarchar(50)  | はい         | コントラクトをデプロイしたユーザーの名 |
| ContractDeployedByUserLastName           | nvarchar(255) | はい         | コントラクトをデプロイしたユーザーの姓 |
| ContractDeployedByUserExternalId         | nvarchar(255) | いいえ          | コントラクトをデプロイしたユーザーの外部識別子。 既定では、この ID はコンソーシアム Azure Active Directory の識別子を表す guid です。                                                                                                                                                |
| ContractDeployedByUserEmailAddress       | nvarchar(255) | はい         | コントラクトをデプロイしたユーザーの電子メール アドレス |
| WorkflowFunctionId                       | INT           | いいえ          | ワークフロー関数の一意識別子 |
| WorkflowFunctionName                     | nvarchar(50)  | いいえ          | 関数の名前 |
| WorkflowFunctionDisplayName              | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示される関数の名前 |
| WorkflowFunctionDescription              | nvarchar(255) | いいえ          | 関数の説明 |
| ContractActionId                         | INT           | いいえ          | コントラクト アクションの一意の識別子 |
| ContractActionProvisioningStatus         | INT           | いいえ          | コントラクト アクションのプロビジョニング プロセスの現在の状態を示します。 次のいずれかの値になります。 <br />0 – データベースで API によってコントラクト アクションが作成されている<br />1 – コントラクト アクションが台帳に送信されている<br />2 – コントラクト アクションが台帳に正常にデプロイされている<br />3 または 4 - 台帳へのコントラクトのデプロイが失敗した<br />5 – コントラクトが台帳に正常にデプロイされた <br /><br />バージョン 1.5 以降、値 0 ～ 5 がサポートされています。 現在のリリースでの下位互換性のためには、値 0 ～ 2 のみをサポートするビュー **vwContractActionV0** を使用できます。 |
| ContractActionTimestamp                  | datetime(2,7) | いいえ          | コントラクト アクションのタイムスタンプ |
| ContractActionExecutedByUserId           | INT           | いいえ          | コントラクト アクションを実行したユーザーの一意識別子 |
| ContractActionExecutedByUserFirstName    | INT           | はい         | コントラクト アクションを実行したユーザーの名 |
| ContractActionExecutedByUserLastName     | nvarchar(50)  | はい         | コントラクト アクションを実行したユーザーの姓 |
| ContractActionExecutedByUserExternalId   | nvarchar(255) | はい         | コントラクト アクションを実行したユーザーの外部識別子。 既定では、この ID はコンソーシアム Azure Active Directory の識別子を表す guid です。 |
| ContractActionExecutedByUserEmailAddress | nvarchar(255) | はい         | コントラクト アクションを実行したユーザーの電子メール アドレス |
| WorkflowFunctionParameterId              | INT           | いいえ          | 関数のパラメーターの一意識別子 |
| WorkflowFunctionParameterName            | nvarchar(50)  | いいえ          | 関数のパラメーターの名前 |
| WorkflowFunctionParameterDisplayName     | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示される関数パラメーターの名前 |
| WorkflowFunctionParameterDataTypeId      | INT           | いいえ          | ワークフロー関数パラメーターに関連付けられているデータ型の一意識別子 |
| WorkflowParameterDataTypeName            | nvarchar(50)  | いいえ          | ワークフロー関数パラメーターに関連付けられているデータ型の名前 |
| ContractActionParameterValue             | nvarchar(255) | いいえ          | スマート コントラクトに格納されているパラメーターの値です |
| BlockHash                                | nvarchar(255) | はい         | ブロックのハッシュ |
| BlockNumber                              | INT           | はい         | 台帳のブロックの数 |
| BlockTimestamp                           | datetime(2,7) | はい         | ブロックのタイムスタンプ |
| TransactionId                            | INT           | いいえ          | トランザクションの一意の識別子 |
| TransactionFrom                          | nvarchar(255) | はい         | トランザクションを開始したパーティ |
| TransactionTo                            | nvarchar(255) | はい         | トランザクション対象のパーティ |
| TransactionHash                          | nvarchar(255) | はい         | トランザクションのハッシュ |
| TransactionIsWorkbenchTransaction        | bit           | はい         | トランザクションが Azure Blockchain Workbench トランザクションかどうかを示すビット |
| TransactionProvisioningStatus            | INT           | はい         | トランザクションのプロビジョニング プロセスの現在の状態を示します。 次のいずれかの値になります。 <br />0 – データベースで API によってトランザクションが作成されている<br />1 – トランザクションが台帳に送信されている<br />2 – トランザクションが台帳に正常にデプロイされている                 |
| TransactionValue                         | decimal(32,2) | はい         | トランザクションの値 |

## <a name="vwcontractproperty"></a>vwContractProperty

このビューは、コントラクトに関連付けられたプロパティに関する情報のマジョリティを表し、一般的なレポート シナリオを促進するために設計されています。 行った各プロパティについて、このビューには次のデータが含まれます。

-   関連付けられたアプリケーション定義
-   関連付けられたワークフロー定義
-   ワークフローをデプロイしたユーザーの詳細
-   関連付けられているスマート コントラクト プロパティの定義
-   プロパティの特定のインスタンス値
-   コントラクトの状態プロパティの詳細

| 名前                               | Type          | Null にできるか | 説明                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | INT           | いいえ          | アプリケーションの一意識別子です |
| ApplicationName                    | nvarchar(50)  | いいえ          | アプリケーションの名前 |
| ApplicationDisplayName             | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示される名前 |
| ApplicationEnabled                 | bit           | いいえ          | アプリケーションが現在有効になっているかどうかを示します。<br />**注:** データベース内でアプリケーションを無効として反映できても、関連付けられたコントラクトはブロックチェーンにとどまり、これらのコントラクトに関するデータはデータベースに残されます。                      |
| WorkflowId                         | INT           | いいえ          | ワークフローの一意識別子 |
| WorkflowName                       | nvarchar(50)  | いいえ          | ワークフローの名前 |
| WorkflowDisplayName                | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示するワークフローの名前 |
| WorkflowDescription                | nvarchar(255) | はい         | ワークフローの説明 |
| ContractId                         | INT           | いいえ          | コントラクトの一意識別子 |
| ContractProvisioningStatus         | INT           | いいえ          | コントラクトのプロビジョニング プロセスの現在の状態を示します。 次のいずれかの値になります。 <br />0 – データベースで API によってコントラクトが作成されている<br />1 – コントラクトが台帳に送信されている<br />2 – コントラクトが台帳に正常にデプロイされている<br />3 または 4 - 台帳へのコントラクトのデプロイが失敗した<br />5 – コントラクトが台帳に正常にデプロイされた <br /><br />バージョン 1.5 以降、値 0 ～ 5 がサポートされています。 現在のリリースでの下位互換性のためには、値 0 ～ 2 のみをサポートするビュー **vwContractPropertyV0** を使用できます。 |
| ContractCodeId                     | INT           | いいえ          | コントラクトのコード実装の一意識別子 |
| ContractLedgerIdentifier           | nvarchar(255) | はい         | 特定の分散型台帳のスマート コントラクトのデプロイされたバージョンに関連付けられている一意識別子。 Ethereum など。 |
| ContractDeployedByUserId           | INT           | いいえ          | コントラクトをデプロイしたユーザーの一意識別子 |
| ContractDeployedByUserFirstName    | nvarchar(50)  | はい         | コントラクトをデプロイしたユーザーの名 |
| ContractDeployedByUserLastName     | nvarchar(255) | はい         | コントラクトをデプロイしたユーザーの姓 |
| ContractDeployedByUserExternalId   | nvarchar(255) | いいえ          | コントラクトをデプロイしたユーザーの外部識別子。 既定では、この ID はコンソーシアム Azure Active Directory の識別子を表す guid です。 |
| ContractDeployedByUserEmailAddress | nvarchar(255) | はい         | コントラクトをデプロイしたユーザーの電子メール アドレス |
| WorkflowPropertyId                 | INT           |             | ワークフローのプロパティの一意識別子 |
| WorkflowPropertyDataTypeId         | INT           | いいえ          | プロパティのデータ型の ID |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | いいえ          | プロパティのデータ型の名前 |
| WorkflowPropertyName               | nvarchar(50)  | いいえ          | ワークフロー プロパティの名前 |
| WorkflowPropertyDisplayName        | nvarchar(255) | いいえ          | ワークフロー プロパティの表示名 |
| WorkflowPropertyDescription        | nvarchar(255) | はい         | プロパティの説明 |
| ContractPropertyValue              | nvarchar(255) | いいえ          | コントラクトのプロパティの値 |
| StateName                          | nvarchar(50)  | はい         | このプロパティにコントラクトの状態が含まれる場合、これは状態の表示名です。 状態に関連付けられていない場合、値は null になります。 |
| StateDisplayName                   | nvarchar(255) | いいえ          | このプロパティに状態が含まれる場合、これは状態の表示名です。 状態に関連付けられていない場合、値は null になります。 |
| StateValue                         | nvarchar(255) | はい         | このプロパティに状態が含まれる場合、これは状態の値です。 状態に関連付けられていない場合、値は null になります。 |

## <a name="vwcontractstate"></a>vwContractState

このビューは、特定のコントラクトの状態に関する情報のマジョリティを表し、一般的なレポート シナリオを促進するために設計されています。 このビュー内の各レコードには、次のデータが含まれています。

-   関連付けられたアプリケーション定義
-   関連付けられたワークフロー定義
-   ワークフローをデプロイしたユーザーの詳細
-   関連付けられているスマート コントラクト プロパティの定義
-   コントラクトの状態プロパティの詳細

| 名前                               | Type          | Null にできるか | 説明                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | INT           | いいえ          | アプリケーションの一意識別子です |
| ApplicationName                    | nvarchar(50)  | いいえ          | アプリケーションの名前 |
| ApplicationDisplayName             | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示される名前 |
| ApplicationEnabled                 | bit           | いいえ          | アプリケーションが現在有効になっているかどうかを示します。<br />**注:** データベース内でアプリケーションを無効として反映できても、関連付けられたコントラクトはブロックチェーンにとどまり、これらのコントラクトに関するデータはデータベースに残されます。 |
| WorkflowId                         | INT           | いいえ          | ワークフローの一意識別子 |
| WorkflowName                       | nvarchar(50)  | いいえ          | ワークフローの名前 |
| WorkflowDisplayName                | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示される名前 |
| WorkflowDescription                | nvarchar(255) | はい         | ワークフローの説明 |
| ContractLedgerImplementationId     | nvarchar(255) | はい         | 特定の分散型台帳のスマート コントラクトのデプロイされたバージョンに関連付けられている一意識別子。 Ethereum など。 |
| ContractId                         | INT           | いいえ          | コントラクトの一意識別子 |
| ContractProvisioningStatus         | INT           | いいえ          |コントラクトのプロビジョニング プロセスの現在の状態を示します。 次のいずれかの値になります。 <br />0 – データベースで API によってコントラクトが作成されている<br />1 – コントラクトが台帳に送信されている<br />2 – コントラクトが台帳に正常にデプロイされている<br />3 または 4 - 台帳へのコントラクトのデプロイが失敗した<br />5 – コントラクトが台帳に正常にデプロイされた <br /><br />バージョン 1.5 以降、値 0 ～ 5 がサポートされています。 現在のリリースでの下位互換性のためには、値 0 ～ 2 のみをサポートするビュー **vwContractStateV0** を使用できます。 |
| ConnectionId                       | INT           | いいえ          | ワークフローのデプロイ先のブロックチェーン インスタンスの一意識別子 |
| ContractCodeId                     | INT           | いいえ          | コントラクトのコード実装の一意識別子 |
| ContractDeployedByUserId           | INT           | いいえ          | コントラクトをデプロイしたユーザーの一意識別子 |
| ContractDeployedByUserExternalId   | nvarchar(255) | いいえ          | コントラクトをデプロイしたユーザーの外部識別子。 既定では、この ID はコンソーシアム Azure Active Directory の識別子を表す guid です。 |
| ContractDeployedByUserFirstName    | nvarchar(50)  | はい         | コントラクトをデプロイしたユーザーの名 |
| ContractDeployedByUserLastName     | nvarchar(255) | はい         | コントラクトをデプロイしたユーザーの姓 |
| ContractDeployedByUserEmailAddress | nvarchar(255) | はい         | コントラクトをデプロイしたユーザーの電子メール アドレス |
| WorkflowPropertyId                 | INT           | いいえ          | ワークフロー プロパティの一意識別子 |
| WorkflowPropertyDataTypeId         | INT           | いいえ          | ワークフロー プロパティのデータ型の ID |
| WorkflowPropertyDataTypeName       | nvarchar(50)  | いいえ          | ワークフロー プロパティのデータ型の名前 |
| WorkflowPropertyName               | nvarchar(50)  | いいえ          | ワークフロー プロパティの名前 |
| WorkflowPropertyDisplayName        | nvarchar(255) | いいえ          | UI に表示するプロパティの表示名 |
| WorkflowPropertyDescription        | nvarchar(255) | はい         | プロパティの説明 |
| ContractPropertyValue              | nvarchar(255) | いいえ          | コントラクトに格納されているプロパティの値 |
| StateName                          | nvarchar(50)  | はい         | このプロパティに状態が含まれる場合、これは状態の表示名です。 状態に関連付けられていない場合、値は null になります。 |
| StateDisplayName                   | nvarchar(255) | いいえ          | このプロパティに状態が含まれる場合、これは状態の表示名です。 状態に関連付けられていない場合、値は null になります。 |
| StateValue                         | nvarchar(255) | はい         | このプロパティに状態が含まれる場合、これは状態の値です。 状態に関連付けられていない場合、値は null になります。 |

## <a name="vwuser"></a>vwUser

このビューは、Azure Blockchain Workbench を使用するためにプロビジョニングされているコンソーシアム メンバーの詳細を示します。 既定では、ユーザーの初回のプロビジョニングを通じてデータが設定されます。

| 名前               | Type          | Null にできるか | 説明                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| id                 | INT           | いいえ          | ユーザーの一意識別子 |
| ExternalID         | nvarchar(255) | いいえ          | ユーザーの外部識別子です。 既定では、この ID はユーザーの Azure Active Directory ID を表す guid です。 |
| ProvisioningStatus | INT           | いいえ          |ユーザーのプロビジョニング プロセスの現在の状態を示します。 次のいずれかの値になります。 <br />0 – API によってユーザーが作成されている<br />1 – キーがデータベース内のユーザーに関連付けられている<br />2 – ユーザーが完全にプロビジョニングされている |
| FirstName          | nvarchar(50)  | はい         | ユーザーの名 |
| LastName           | nvarchar(50)  | はい         | ユーザーの姓 |
| EmailAddress       | nvarchar(255) | はい         | ユーザーのメール アドレス |

## <a name="vwworkflow"></a>vwWorkflow

このビューは、コア ワークフロー メタデータの詳細と、ワークフローの関数とパラメーターを表します。 レポート用に設計されたこのビューには、ワークフローに関連付けられているアプリケーションについてのメタデータも含まれています。 このビューには、ワークフローのレポートを容易にするために複数の基になるテーブルのデータが含まれています。 各ワークフローについて、このビューには次のデータが含まれます。

-   関連付けられたアプリケーション定義
-   関連付けられたワークフロー定義
-   関連付けられたワークフロー開始状態の情報

| 名前                              | Type          | Null にできるか | 説明                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | INT           | いいえ          | アプリケーションの一意識別子です |
| ApplicationName                   | nvarchar(50)  | いいえ          | アプリケーションの名前 |
| ApplicationDisplayName            | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示される名前 |
| ApplicationEnabled                | bit           | いいえ          | アプリケーションが有効になっているかどうかを示します |
| WorkflowId                        | INT           | はい         | ワークフローの一意識別子 |
| WorkflowName                      | nvarchar(50)  | いいえ          | ワークフローの名前 |
| WorkflowDisplayName               | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示される名前 |
| WorkflowDescription               | nvarchar(255) | はい         | ワークフローの説明。 |
| WorkflowConstructorFunctionId     | INT           | いいえ          | ワークフローのコンス トラクターとして機能するワークフロー関数の識別子 |
| WorkflowStartStateId              | INT           | いいえ          | 状態の一意識別子 |
| WorkflowStartStateName            | nvarchar(50)  | いいえ          | 状態の名前 |
| WorkflowStartStateDisplayName     | nvarchar(255) | いいえ          | 状態のユーザー インターフェイスに表示される名前 |
| WorkflowStartStateDescription     | nvarchar(255) | はい         | ワークフローの状態の説明 |
| WorkflowStartStateStyle           | nvarchar(50)  | はい         | この値は、ワークフローがこの状態にある完了率を示します |
| WorkflowStartStateValue           | INT           | いいえ          | 状態の値 |
| WorkflowStartStatePercentComplete | INT           | いいえ          | UI でこの状態をどのように表示するかクライアントにヒントを提供する説明テキストです。 サポートされている状態には、"*成功*" と "*失敗*" があります。 |

## <a name="vwworkflowfunction"></a>vwWorkflowFunction

このビューは、コア ワークフロー メタデータの詳細と、ワークフローの関数とパラメーターを表します。 レポート用に設計されたこのビューには、ワークフローに関連付けられているアプリケーションについてのメタデータも含まれています。 このビューには、ワークフローのレポートを容易にするために複数の基になるテーブルのデータが含まれています。 各ワークフロー関数について、このビューには次のデータが含まれます。

-   関連付けられたアプリケーション定義
-   関連付けられたワークフロー定義
-   ワークフロー関数の詳細

| 名前                                 | Type          | Null にできるか | 説明                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | INT           | いいえ          | アプリケーションの一意識別子です |
| ApplicationName                      | nvarchar(50)  | いいえ          | アプリケーションの名前 |
| ApplicationDisplayName               | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示される名前 |
| ApplicationEnabled                   | bit           | いいえ          | アプリケーションが有効になっているかどうかを示します |
| WorkflowId                           | INT           | いいえ          | ワークフローの一意識別子 |
| WorkflowName                         | nvarchar(50)  | いいえ          | ワークフローの名前 |
| WorkflowDisplayName                  | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示するワークフローの名前 |
| WorkflowDescription                  | nvarchar(255) | はい         | ワークフローの説明 |
| WorkflowFunctionId                   | INT           | いいえ          | 関数の一意識別子 |
| WorkflowFunctionName                 | nvarchar(50)  | はい         | 関数の名前 |
| WorkflowFunctionDisplayName          | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示される関数の名前 |
| WorkflowFunctionDescription          | nvarchar(255) | はい         | ワークフロー関数の説明 |
| WorkflowFunctionIsConstructor        | bit           | いいえ          | これは、ワークフロー関数がワークフローのコンストラクターであるかどうかを示します |
| WorkflowFunctionParameterId          | INT           | いいえ          | 関数のパラメーターの一意識別子 |
| WorkflowFunctionParameterName        | nvarchar(50)  | いいえ          | 関数のパラメーターの名前 |
| WorkflowFunctionParameterDisplayName | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示される関数パラメーターの名前 |
| WorkflowFunctionParameterDataTypeId  | INT           | いいえ          | ワークフロー関数パラメーターに関連付けられているデータ型の一意識別子 |
| WorkflowParameterDataTypeName        | nvarchar(50)  | いいえ          | ワークフロー関数パラメーターに関連付けられているデータ型の名前 |

## <a name="vwworkflowproperty"></a>vwWorkflowProperty

このビューは、ワークフローに定義されたプロパティを表します。 各プロパティについて、このビューには次のデータが含まれます。

-   関連付けられたアプリケーション定義
-   関連付けられたワークフロー定義
-   ワークフロー プロパティの詳細

| 名前                         | Type          | Null にできるか | 説明                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | INT           | いいえ          | アプリケーションの一意識別子です |
| ApplicationName              | nvarchar(50)  | いいえ          | アプリケーションの名前 |
| ApplicationDisplayName       | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示される名前 |
| ApplicationEnabled           | bit           | いいえ          | アプリケーションが現在有効になっているかどうかを示します。<br />**注:** データベース内でアプリケーションを無効として反映できても、関連付けられたコントラクトはブロックチェーンにとどまり、これらのコントラクトに関するデータはデータベースに残されます。 |
| WorkflowId                   | INT           | いいえ          | ワークフローの一意識別子 |
| WorkflowName                 | nvarchar(50)  | いいえ          | ワークフローの名前 |
| WorkflowDisplayName          | nvarchar(255) | いいえ          | ユーザー インターフェイスのワークフローに表示される名前 |
| WorkflowDescription          | nvarchar(255) | はい         | ワークフローの説明 |
| WorkflowPropertyID           | INT           | いいえ          | ワークフローのプロパティの一意識別子 |
| WorkflowPropertyName         | nvarchar(50)  | いいえ          | プロパティの名前 |
| WorkflowPropertyDescription  | nvarchar(255) | はい         | プロパティの説明 |
| WorkflowPropertyDisplayName  | nvarchar(255) | いいえ          | ユーザー インターフェイスに表示される名前 |
| WorkflowPropertyWorkflowId   | INT           | いいえ          | このプロパティが関連付けられているワークフローの ID |
| WorkflowPropertyDataTypeId   | INT           | いいえ          | プロパティに定義されたデータ型の ID |
| WorkflowPropertyDataTypeName | nvarchar(50)  | いいえ          | プロパティに定義されたデータ型の名前 |
| WorkflowPropertyIsState      | bit           | いいえ          | このフィールドは、このワークフロー プロパティにワークフローの状態が含まれているかどうかを示します |

## <a name="vwworkflowstate"></a>vwWorkflowState

このビューは、ワークフローに関連付けられたプロパティを表します。 各コントラクトについて、このビューには次のデータが含まれます。

-   関連付けられたアプリケーション定義
-   関連付けられたワークフロー定義
-   ワークフローの状態の情報

| 名前                         | Type          | Null にできるか | 説明                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | INT           | いいえ          | アプリケーションの一意識別子です |
| ApplicationName              | nvarchar(50)  | いいえ          | アプリケーションの名前 |
| ApplicationDisplayName       | nvarchar(255) | いいえ          | アプリケーションの説明 |
| ApplicationEnabled           | bit           | いいえ          | アプリケーションが現在有効になっているかどうかを示します。<br />**注:** データベース内でアプリケーションを無効として反映できても、関連付けられたコントラクトはブロックチェーンにとどまり、これらのコントラクトに関するデータはデータベースに残されます。 |
| WorkflowId                   | INT           | いいえ          | ワークフローの一意識別子 |
| WorkflowName                 | nvarchar(50)  | いいえ          | ワークフローの名前 |
| WorkflowDisplayName          | nvarchar(255) | いいえ          | ワークフローのユーザー インターフェイスに表示される名前 |
| WorkflowDescription          | nvarchar(255) | はい         | ワークフローの説明 |
| WorkflowStateID              | INT           | いいえ          | 状態の一意識別子 |
| WorkflowStateName            | nvarchar(50)  | いいえ          | 状態の名前 |
| WorkflowStateDisplayName     | nvarchar(255) | いいえ          | 状態のユーザー インターフェイスに表示される名前 |
| WorkflowStateDescription     | nvarchar(255) | はい         | ワークフローの状態の説明 |
| WorkflowStatePercentComplete | INT           | いいえ          | この値は、ワークフローがこの状態にある完了率を示します |
| WorkflowStateValue           | nvarchar(50)  | いいえ          | 状態の値 |
| WorkflowStateStyle           | nvarchar(50)  | いいえ          | UI でこの状態をどのように表示するかクライアントにヒントを提供する説明テキストです。 サポートされている状態には、"*成功*" と "*失敗*" があります。 |
