---
title: ADF での CI-CD、Azure DevOps、および GitHub の問題のトラブルシューティング
description: さまざまな方法を使用して、ADF での CI-CD の問題のトラブルシューティングを行います。
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/03/2020
ms.openlocfilehash: d96c467807af868c07be12f52d913f881b82f732
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175874"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>ADF での CI-CD、Azure DevOps、および GitHub の問題のトラブルシューティング 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ここでは、Azure Data Factory での、継続的インテグレーションと継続的デプロイ (CI-CD)、Azure DevOps、および GitHub の問題に関する一般的なトラブルシューティング方法について説明します。

ソース管理または DevOps 手法の使用に関する質問や問題がある場合は、役に立つ記事がいくつかあります。

- ADF でのソース管理については、「[Azure Data Factory のソース管理](source-control.md)」を参照してください。 
- ADF での DevOps CI-CD の詳細については、「[Azure Data Factory における継続的インテグレーションとデリバリー](continuous-integration-deployment.md)」を参照してください。
 
## <a name="common-errors-and-messages"></a>一般的なエラーとメッセージ

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>テナントが異なるため、Git リポジトリへの接続に失敗しました

#### <a name="issue"></a>問題
    
HTTP ステータス 401 のような認証の問題が発生することがあります。 特に、ゲストアカウントを持つテナントが複数ある場合は、問題がさらに複雑になる可能性があります。

#### <a name="cause"></a>原因

トークンは元のテナントから取得されましたが、ADF はゲスト テナントにあり、そのトークンを使用してゲスト テナントの DevOps にアクセスしようとしています。 これは正しい動作ではありません。

#### <a name="recommendation"></a>推奨

代わりに、ゲスト テナントから発行されたトークンを使用する必要があります。 たとえば、同じ Azure Active Directory をゲスト テナントと DevOps に割り当てる必要があります。これにより、トークンの動作を正しく設定し、正しいテナントを使用できるようになります。

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>パラメーター ファイルのテンプレート パラメーターが有効ではありません

#### <a name="issue"></a>問題

**同じ** 構成 (頻度や間隔など) でテストまたは運用ブランチで既に使用できるトリガーを Dev ブランチで削除すると、リリース パイプラインのデプロイは成功し、対応するトリガーはそれぞれの環境で削除されます。 しかし、テスト/運用環境のトリガーに対して **異なる** 構成 (頻度や間隔など) をしている場合に、Dev で同じトリガーを削除すると、デプロイは失敗し、エラーが発生します。

#### <a name="cause"></a>原因

CI/CD パイプラインは次のエラーで失敗します。

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>推奨

このエラーは、パラメーター化されたトリガーを削除することが多いために発生します。パラメーターは ARM テンプレートでは使用できなくなります (トリガーが存在しなくなったため)。 パラメーターが ARM テンプレートになくなるため、DevOps パイプラインでオーバーライドされたパラメーターを更新する必要があります。 そうしないと、ARM テンプレート内のパラメーターが変更されるたびに、DevOps パイプラインでオーバーライドされたパラメーターを更新する必要があります (デプロイ タスク)。

### <a name="updating-property-type-is-not-supported"></a>プロパティの型の更新はサポートされていません

#### <a name="issue"></a>問題

CI/CD リリース パイプラインが次のエラーで失敗しています。

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>原因

これは、ターゲット ファクトリに、同じ名前だが、型が異なる統合ランタイムがあるためです。 Integration Runtime は、デプロイ時に同じ型である必要があります。

#### <a name="recommendation"></a>推奨

- 以下の CI/CD のベスト プラクティスを参照してください。

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- 統合ランタイムは頻繁に変更されることはなく、CI/CD 内のすべてのステージで類似しているため、Data Factory は CI/CD のすべてのステージで同じ名前と型の統合ランタイムを使用すると想定します。 名前と型およびプロパティが異なる場合は、ソースとターゲットの統合ランタイム構成が一致していることを確認してから、リリース パイプラインをデプロイしてください。
- すべてのステージで統合ランタイムを共有する場合は、共有の統合ランタイムを含めるためだけに三項ファクトリを使用することを検討してください。 この共有ファクトリは、すべての環境で、リンクされた統合ランタイムの種類として使用できます。

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>参照が無効なため、ドキュメントの作成または更新に失敗しました

#### <a name="issue"></a>問題

Data Factory に変更を発行しようとすると、次のエラー メッセージが表示されます。

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`

#### <a name="symptom"></a>症状

Git 構成をデタッチし、"リソースのインポート" フラグをオンにして再設定しました。これにより、Data Factory が "同期" として設定されます。 これは、発行する変更がないことを意味します。

#### <a name="resolution"></a>解決策

Git 構成をデタッチして再度設定し、[import existing resources]\(既存のリソースをインポートする\) チェックボックスをオンにしないようにします。

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>リソース グループから別のリソース グループに Data Factory を移動できません

#### <a name="issue"></a>問題

Data Factory をリソース グループ間で移動できないため、次のエラーが発生して失敗します。

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>解決方法

移動できるようにするには、SSIS-IR および共有 IR を削除する必要があります。 統合ランタイムを削除しない場合は、コピーとクローンのドキュメントに従ってコピーを行い、完了後に古い Data Factory を削除することをお勧めします。

###  <a name="unable-to-export-and-import-arm-template"></a>ARM テンプレートをエクスポートおよびインポートできません

#### <a name="issue"></a>問題

ARM テンプレートをエクスポートおよびインポートできません。 ポータルでエラーは発生しませんでしたが、ブラウザーのトレースでは、次のエラーが表示されます。

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>原因

ユーザーとして顧客ロールが作成されましたが、必要なアクセス許可がありませんでした。 ファクトリが UI に読み込まれると、ファクトリの一連の露出コントロール値がチェックされます。 この場合、ユーザーのアクセス ロールには、*queryFeaturesValue* API へのアクセス許可がありません。 この API にアクセスするために、グローバル パラメーター機能がオフになっています。 ARM エクスポート コード パスは、グローバル パラメーター機能に部分的に依存しています。

#### <a name="resolution"></a>解決方法

この問題を解決するには、ロールに次のアクセス許可を追加する必要があります。*Microsoft.DataFactory/factories/queryFeaturesValue/action* このアクセス許可は、既定で "Data Factory 共同作成者" ロールに含まれている必要があります。

###  <a name="automatic-publishing-for-cicd-without-clicking-publish-button"></a>[発行] ボタンをクリックしない CI/CD での自動発行  

#### <a name="issue"></a>問題

ADF ポータルでのボタン クリックによる手動発行で、自動の CI/CD 操作が有効になりません。

#### <a name="cause"></a>原因

最近まで、ADF パイプラインをデプロイのために発行する唯一の方法は、ADF ポータルでのボタン クリックを使用することでした。 現在は、プロセスを自動で行えるようになりました。 

#### <a name="resolution"></a>解決方法

CI/CD プロセスが拡張されました。 **自動発行** 機能によって、ADF UX からすべての Azure Resource Manager (ARM) テンプレート機能が取得、検証、およびエクスポートされます。 これにより、一般公開されている npm パッケージ [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) を介してそのロジックが使用可能になります。 これにより、ADF UI に移動してボタンをクリックする代わりに、プログラムによってこれらのアクションをトリガーできます。 これにより、CI/CD パイプラインで **真の** 継続的インテグレーション エクスペリエンスを利用できます。 詳細については、[ADF CI/CD 発行の機能強化](./continuous-integration-deployment-improvements.md)に関する記事に従ってください。 

###  <a name="cannot-publish-because-of-4mb-arm-template-limit"></a>4 MB の ARM テンプレート制限が原因で発行できない  

#### <a name="issue"></a>問題

4 MB の合計テンプレート サイズという Azure Resource Manager の制限に達したため、デプロイできません。 この制限を超えた後にデプロイするための解決策が必要です。 

#### <a name="cause"></a>原因

Azure Resource Manager では、テンプレート サイズは 4 MB に制限されています。 テンプレートのサイズを 4 MB に、各パラメーター ファイルのサイズを 64 KB に制限します。 4 MB の制限は、反復的なリソースの定義と変数およびパラメーターの値で拡張された後のテンプレートの最終的な状態に適用されます。 しかし、その制限を超えています。 

#### <a name="resolution"></a>解決方法

中小規模のソリューションの場合、テンプレートを 1 つにするとわかりやすく、保守も簡単になります。 すべてのリソースと値を 1 つのファイルで参照できます。 高度なシナリオの場合、リンクされたテンプレートを使用することで、対象となるコンポーネントにソリューションを分割することができます。 [リンクされた、およびネストされたテンプレートの使用](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell)に関する記事に記載されているベスト プラクティスに従ってください。

### <a name="cannot-connect-to-git-enterprise"></a>Git Enterprise に接続できない 

##### <a name="issue"></a>問題

アクセス許可の問題のため、Git Enterprise に接続できません。 "**422 - 処理できないエンティティです**" のようなエラーが表示される場合があります。

#### <a name="cause"></a>原因

ADF に OAuth が構成されていません。 URL が正しく構成されていません。

##### <a name="resolution"></a>解決方法

最初に、ADF への OAuth アクセスを許可します。 次に、正しい URL を使用して Git Enterprise に接続する必要があります。 構成は、顧客組織に設定する必要があります。 たとえば、ADF はまず *https://hostname/api/v3/search/repositories?q=user%3<customer credential>....* を試行し、失敗します。 その後、 *https://hostname/api/v3/orgs/<org>/<repo>...* を試行し、成功します。 
 
### <a name="recover-from-a-deleted-data-factory"></a>削除されたデータ ファクトリから復旧する

#### <a name="issue"></a>問題
お客様が、データ ファクトリを削除したか、またはデータ ファクトリが含まれるリソース グループを削除しました。 削除されたデータ ファクトリを復元する方法を知る必要があります。

#### <a name="cause"></a>原因

お客様がソース管理 (DevOps または Git) を構成している場合にのみ、データ ファクトリを復旧することができます。 これにより、発行された最新のリソースはすべて復旧されますが、発行されていないパイプライン、データセット、リンク サービスは復元 **されません**。

ソース管理が使用されていない場合は、サービスが削除コマンドを受け取ると、インスタンスが削除されて、バックアップは格納されないため、削除されたデータ ファクトリをバックエンドから復旧することはできません。

#### <a name="resolution"></a>解決方法

ソース管理が使用されている場合に削除されたデータ ファクトリを復旧するには、次の手順のようにします。

 * 新しい Azure データ ファクトリを作成します。

 * 同じ設定で Git を再構成しますが、既存の Data Factory リソースを選択したリポジトリにインポートし、[新しいブランチ] を選択します。

 * 変更をコラボレーション ブランチにマージして発行する pull request を作成します。

 * 削除された ADF にセルフホステッド統合ランタイムがあった場合は、新しい ADF に新しいインスタンスを作成する必要があります。また、新しいキーを取得して、オンプレミスのコンピューターや VM 上のインスタンスをアンインストールして再インストールする必要があります。 IR の設定が完了した後、お客様は、新しい IR を指すようにリンク サービスを変更して、接続をテストする必要があります。そうしないと、**無効な参照** エラーで失敗します。



## <a name="next-steps"></a>次のステップ

トラブルシューティングの詳細について、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Data Factory に関する Stack overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
