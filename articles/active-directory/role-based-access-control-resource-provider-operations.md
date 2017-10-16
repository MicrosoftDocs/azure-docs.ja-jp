---
title: "Azure Resource Manager のプロバイダー操作 | Microsoft Docs"
description: "Microsoft Azure Resource Manager の各リソース プロバイダーで使用できる操作について詳しく説明します。"
services: active-directory
documentationcenter: 
author: jboeshart
manager: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: jaboes
ms.openlocfilehash: 95c6a1e9cfadeb19a0ec5a53fffb15ef220bfec0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager のリソース プロバイダー操作

このドキュメントでは、Microsoft Azure Resource Manager の各リソース プロバイダーで使用できる操作を示します。 これらの操作をカスタム ロールで使用することで、Azure のリソースにロールベースのアクセス制御 (RBAC) の詳細なアクセス許可を提供できます。 これは包括的な一覧ではないことに注意してください。各プロバイダーの更新に伴って、操作が追加または削除される場合があります。 操作文字列は、`Microsoft.<ProviderName>/<ChildResourceType>/<action>` の形式に従います。 最新の包括的な一覧については、`Get-AzureRmProviderOperation` (PowerShell の場合) または `azure provider operations show` (Azure CLI の場合) を使用して、Azure リソース プロバイダーの操作の一覧を表示してください。

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| 操作 | 説明 |
|---|---|
|/configuration/action|テナント構成を更新します。|
|/services/action|テナントのサービス インスタンスを更新します。|
|/configuration/write|テナント構成を作成します。|
|/configuration/read|テナント構成を読み取ります。|
|/services/write|テナントにサービス インスタンスを作成します。|
|/services/read|テナントのサービス インスタンスを読み取ります。|
|/services/delete|テナントのサービス インスタンスを削除します。|
|/services/servicemembers/action|サービスにサービス メンバー インスタンスを作成します。|
|/services/servicemembers/read|サービスのサービス メンバー インスタンスを読み取ります。|
|/services/servicemembers/delete|サービスのサービス メンバー インスタンスを削除します。|
|/services/servicemembers/alerts/read|サービス メンバーのアラートを読み取ります。|
|/services/alerts/read|サービスのアラートを読み取ります。|
|/services/alerts/read|サービスのアラートを読み取ります。|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| 操作 | Description |
|---|---|
|/generateRecommendations/action|推奨事項を生成します。|
|/suppressions/action|抑制を作成または更新します。|
|/register/action|Microsoft Advisor にサブスクリプションを登録します。|
|/generateRecommendations/read|推奨事項の生成の状態を取得します。|
|/recommendations/read|推奨事項を読み取ります。|
|/suppressions/read|抑制を取得します。|
|/suppressions/delete|抑制を削除します。|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| 操作 | Description |
|---|---|
|/servers/read|指定された分析サーバーの情報を取得します。|
|/servers/write|指定された分析サーバーを作成または更新します。|
|/servers/delete|分析サーバーを削除します。|
|/servers/suspend/action|分析サーバーを中断します。|
|/servers/resume/action|分析サーバーを再開します。|
|/servers/checkNameAvailability<br>/action|指定された分析サーバー名が有効であり、使用されていないことを確認します。|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| 操作 | 説明 |
|---|---|
|/checkNameAvailability/action|指定されたサービス名を使用できるかどうかを確認します。|
|/register/action|Microsoft.ApiManagement リソース プロバイダーにサブスクリプションを登録します。|
|/unregister/action|Microsoft.ApiManagement リソース プロバイダーへのサブスクリプションの登録を解除します。|
|/service/write|API Management サービスの新しいインスタンスを作成します。|
|/service/read|API Management サービス インスタンスのメタデータを読み取ります。|
|/service/delete|API Management サービス インスタンスを削除します。|
|/service/updatehostname/action|API Management サービスのカスタム ドメイン名を設定、更新、または削除します。|
|/service/uploadcertificate/action|API Management サービスの SSL 証明書をアップロードします。|
|/service/backup/action|ユーザーが指定したストレージ アカウント内の指定されたコンテナーに API Management サービスをバックアップします。|
|/service/restore/action|ユーザーが指定したストレージ アカウント内の指定されたコンテナーから API Management サービスを復元します。|
|/service/managedeployments/action|SKU/ユニット数を変更し、API Management サービスのリージョン デプロイを追加または削除します。|
|/service/getssotoken/action|API Management サービスの従来のポータルに管理者としてログインする際に使用できる SSO トークンを取得します。|
|/service/applynetworkconfigurationupdates/action|更新済みのネットワーク設定を選択するために、仮想ネットワークで実行されている Microsoft.ApiManagement リソースを更新します。|
|/service/operationresults/read|実行時間の長い操作の現在の状態を取得します。|
|/service/networkStatus/read|リソースのネットワーク アクセスの状態を取得します。|
|/service/loggers/read|ロガーの一覧を取得するか、ロガーの詳細を取得します。|
|/service/loggers/write|新しいロガーを追加するか、既存のロガーの詳細を更新します。|
|/service/loggers/delete|既存のロガーを削除します。|
|/service/users/read|登録済みユーザーの一覧を取得するか、ユーザーのアカウントの詳細を取得します。|
|/service/users/write|新しいユーザーを登録するか、既存のユーザーのアカウントの詳細を更新します。|
|/service/users/delete|ユーザー アカウントを削除します。|
|/service/users/generateSsoUrl/action|SSO URL を生成します。 この URL を使用して、管理ポータルにアクセスできます。|
|/service/users/subscriptions/read|ユーザー サブスクリプションの一覧を取得します。|
|/service/users/keys/read|ユーザー キーの一覧を取得します。|
|/service/users/groups/read|ユーザー グループの一覧を取得します。|
|/service/tenant/operationResults/read|操作の結果の一覧を取得するか、特定の操作の結果を取得します。|
|/service/tenant/policy/read|テナントのポリシーの構成を取得します。|
|/service/tenant/policy/write|テナントのポリシーの構成を設定します。|
|/service/tenant/policy/delete|テナントのポリシーの構成を削除します。|
|/service/tenant/configuration/save/action|レポジトリ内の指定されたブランチの構成のスナップショットを使用してコミットを作成します。|
|/service/tenant/configuration/deploy/action|指定された Git ブランチの変更をデータベース内の構成に適用するデプロイ タスクを実行します。|
|/service/tenant/configuration/validate/action|指定された Git ブランチの変更を検証します。|
|/service/tenant/configuration/operationResults/read|操作の結果の一覧を取得するか、特定の操作の結果を取得します。|
|/service/tenant/configuration/syncState/read|前回の Git 同期の状態を取得します。|
|/service/tenant/access/read|テナント アクセス情報の詳細を取得します。|
|/service/tenant/access/write|テナント アクセス情報の詳細を更新します。|
|/service/tenant/access/regeneratePrimaryKey/action|プライマリ アクセス キーを再生成します。|
|/service/tenant/access/regenerateSecondaryKey/action|セカンダリ アクセス キーを再生成します。|
|/service/identityProviders/read|ID プロバイダーの一覧を取得するか、ID プロバイダーの詳細を取得します。|
|/service/identityProviders/write|新しい ID プロバイダーを作成するか、既存の ID プロバイダーの詳細を更新します。|
|/service/identityProviders/delete|既存の ID プロバイダーを削除します。|
|/service/subscriptions/read|製品サブスクリプションの一覧を取得するか、製品サブスクリプションの詳細を取得します。|
|/service/subscriptions/write|既存のユーザーを既存の製品にサブスクライブするか、既存のサブスクリプションの詳細を更新します。 この操作を使用してサブスクリプションを更新できます。|
|/service/subscriptions/delete|サブスクリプションを削除します。 この操作を使用してサブスクリプションを削除できます。|
|/service/subscriptions/regeneratePrimaryKey/action|サブスクリプションのプライマリ キーを再生成します。|
|/service/subscriptions/regenerateSecondaryKey/action|サブスクリプションのセカンダリ キーを再生成します。|
|/service/backends/read|バックエンドの一覧を取得するか、バックエンドの詳細を取得します。|
|/service/backends/write|新しいバックエンドを追加するか、既存のバックエンドの詳細を更新します。|
|/service/backends/delete|既存のバックエンドを削除します。|
|/service/apis/read|すべての登録済み API の一覧を取得するか、API の詳細を取得します。|
|/service/apis/write|新しい API を作成するか、既存の API の詳細を更新します。|
|/service/apis/delete|既存の API を削除します。|
|/service/apis/policy/read|API のポリシーの構成の詳細を取得します。|
|/service/apis/policy/write|API のポリシーの構成の詳細を設定します。|
|/service/apis/policy/delete|API からポリシーの構成を削除します。|
|/service/apis/operations/read|既存の API 操作の一覧を取得するか、API 操作の詳細を取得します。|
|/service/apis/operations/write|新しい API 操作を作成するか、既存の API 操作を更新します。|
|/service/apis/operations/delete|既存の API 操作を削除します。|
|/service/apis/operations/policy/read|操作のポリシーの構成の詳細を取得します。|
|/service/apis/operations/policy/write|操作のポリシーの構成の詳細を設定します。|
|/service/apis/operations/policy/delete|操作からポリシーの構成を削除します。|
|/service/products/read|製品の一覧を取得するか、製品の詳細を取得します。|
|/service/products/write|新しい製品を作成するか、既存の製品の詳細を更新します。|
|/service/products/delete|既存の製品を削除します。|
|/service/products/subscriptions/read|製品サブスクリプションの一覧を取得します。|
|/service/products/apis/read|既存の製品に追加された API の一覧を取得します。|
|/service/products/apis/write|既存の API を既存の製品に追加します。|
|/service/products/apis/delete|既存の製品から既存の API を削除します。|
|/service/products/policy/read|既存の製品のポリシーの構成を取得します。|
|/service/products/policy/write|既存の製品のポリシーの構成を設定します。|
|/service/products/policy/delete|既存の製品からポリシーの構成を削除します。|
|/service/products/groups/read|製品に関連付けられている開発者グループの一覧を取得します。|
|/service/products/groups/write|既存の開発者グループを既存の製品に関連付けます。|
|/service/products/groups/delete|既存の製品への既存の開発者グループの関連付けを削除します。|
|/service/openidConnectProviders/read|OpenID Connect プロバイダーの一覧を取得するか、OpenID Connect プロバイダーの詳細を取得します。|
|/service/openidConnectProviders/write|新しい OpenID Connect プロバイダーを作成するか、既存の OpenID Connect プロバイダーの詳細を更新します。|
|/service/openidConnectProviders/delete|既存の OpenID Connect プロバイダーを削除します。|
|/service/certificates/read|証明書の一覧を取得するか、証明書の詳細を取得します。|
|/service/certificates/write|新しい証明書を追加します。|
|/service/certificates/delete|既存の証明書を削除します。|
|/service/properties/read|すべてのプロパティの一覧を取得するか、指定されたプロパティの詳細を取得します。|
|/service/properties/write|新しいプロパティを作成するか、指定されたプロパティの値を更新します。|
|/service/properties/delete|既存のプロパティを削除します。|
|/service/groups/read|グループの一覧を取得するか、グループの詳細を取得します。|
|/service/groups/write|新しいグループを作成するか、既存のグループの詳細を更新します。|
|/service/groups/delete|既存のグループを削除します。|
|/service/groups/users/read|グループのユーザーの一覧を取得します。|
|/service/groups/users/write|既存のユーザーを既存のグループに追加します。|
|/service/groups/users/delete|既存のグループから既存のユーザーを削除します。|
|/service/authorizationServers/read|承認サーバーの一覧を取得するか、承認サーバーの詳細を取得します。|
|/service/authorizationServers/write|新しい承認サーバーを作成するか、既存の承認サーバーの詳細を更新します。|
|/service/authorizationServers/delete|既存の承認サーバーを削除します。|
|/service/reports/bySubscription/read|サブスクリプションごとに集計されたレポートを取得します。|
|/service/reports/byRequest/read|要求のレポート データを取得します。|
|/service/reports/byOperation/read|操作ごとに集計されたレポートを取得します。|
|/service/reports/byGeo/read|地理的リージョンごとに集計されたレポートを取得します。|
|/service/reports/byUser/read|開発者ごとに集計されたレポートを取得します。|
|/service/reports/byTime/read|期間ごとに集計されたレポートを取得します。|
|/service/reports/byApi/read|API ごとに集計されたレポートを取得します。|
|/service/reports/byProduct/read|製品ごとに集計されたレポートを取得します。|

## <a name="microsoftappservice"></a>Microsoft.AppService

| 操作 | Description |
|---|---|
|/appidentities/Read|ゲートウェイに登録されているリソース (Web サイト) を返します。|
|/appidentities/Write|新しいアプリ ID を作成します。|
|/appidentities/Delete|既存のアプリ ID を削除します。|
|/deploymenttemplates/listMetadata/Action|API アプリ パッケージに関連付けられている UI メタデータを一覧表示します。|
|/deploymenttemplates/generate/Action|API アプリ インスタンスをプロビジョニングするデプロイ テンプレートを返します。|
|/gateways/Read|ゲートウェイ インスタンスを返します。|
|/gateways/Write|新しいゲートウェイを作成するか、既存のゲートウェイを更新します。|
|/gateways/Delete|既存のゲートウェイ インスタンスを削除します。|
|/gateways/listLoginUris/Action|トークン ストアを設定し、OAuth ログイン URI を返します。|
|/gateways/listKeys/Action|ゲートウェイのシークレットを返します。|
|/gateways/tokens/Write|指定された名前で新しい ZUMO トークンを作成します。|
|/gateways/registrations/Read|ゲートウェイに登録されているリソース (Web サイト) を返します。|
|/gateways/registrations/Write|リソース (Web サイト) をゲートウェイに登録します。|
|/gateways/registrations/Delete|ゲートウェイからリソース (Web サイト) の登録を解除します。|
|/apiapps/Read|API アプリ インスタンスを返します。|
|/apiapps/Write|新しい API アプリを作成するか、既存の API アプリを更新します。|
|/apiapps/Delete|既存の API アプリ インスタンスを削除します。|
|/apiapps/listStatus/Action|API アプリの状態を返します。|
|/apiapps/listKeys/Action|API アプリのシークレットを返します。|
|/apiapps/apidefinitions/Read|API アプリの API 定義を返します。|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| 操作 | 説明 |
|---|---|
|/elevateAccess/action|テナント スコープで、ユーザー アクセス管理者のアクセス権を呼び出し元に付与します。|
|/classicAdministrators/read|サブスクリプションの管理者を読み取ります。|
|/classicAdministrators/write|サブスクリプションの管理者を追加または変更しします。|
|/classicAdministrators/delete|サブスクリプションから管理者を削除します。|
|/locks/read|指定されたスコープのロックを取得します。|
|/locks/write|指定されたスコープのロックを追加します。|
|/locks/delete|指定されたスコープのロックを削除します。|
|/policyAssignments/read|ポリシー割り当てに関する情報を取得します。|
|/policyAssignments/write|指定されたスコープのポリシー割り当てを作成します。|
|/policyAssignments/delete|指定されたスコープのポリシー割り当てを削除します。|
|/permissions/read|指定されたスコープで呼び出し元が持つすべてのアクセス許可を一覧表示します。|
|/roleDefinitions/read|ロール定義に関する情報を取得します。|
|/roleDefinitions/write|指定されたアクセス許可と割り当て可能なスコープで、カスタムのロール定義を作成または更新します。|
|/roleDefinitions/delete|指定されたカスタムのロール定義を削除します。|
|/providerOperations/read|ロール定義で使用できる、すべてのリソース プロバイダーのための操作を取得します。|
|/policyDefinitions/read|ポリシー定義に関する情報を取得します。|
|/policyDefinitions/write|カスタムのポリシー定義を作成します。|
|/policyDefinitions/delete|ポリシー定義を削除します。|
|/roleAssignments/read|ロールの割り当てに関する情報を取得します。|
|/roleAssignments/write|指定されたスコープのロールの割り当てを作成します。|
|/roleAssignments/delete|指定したスコープにおけるロールの割り当てを削除します。|

## <a name="microsoftautomation"></a>Microsoft.Automation

| 操作 | 説明 |
|---|---|
|/automationAccounts/read|Azure Automation アカウントを取得します。|
|/automationAccounts/write|Azure Automation アカウントを作成または更新します。|
|/automationAccounts/delete|Azure Automation アカウントを削除します。|
|/automationAccounts/configurations/readContent/action|Azure Automation DSC のコンテンツを取得します。|
|/automationAccounts/hybridRunbookWorkerGroups/read|Hybrid Runbook Worker リソースを読み取ります。|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Hybrid Runbook Worker リソースを削除します。|
|/automationAccounts/jobSchedules/read|Azure Automation ジョブ スケジュールを取得します。|
|/automationAccounts/jobSchedules/write|Azure Automation ジョブ スケジュールを作成します。|
|/automationAccounts/jobSchedules/delete|Azure Automation ジョブ スケジュールを削除します。|
|/automationAccounts/connectionTypes/read|Azure Automation 接続の種類の資産を取得します。|
|/automationAccounts/connectionTypes/write|Azure Automation 接続の種類の資産を作成します。|
|/automationAccounts/connectionTypes/delete|Azure Automation 接続の種類の資産を削除します。|
|/automationAccounts/modules/read|Azure Automation モジュールを取得します。|
|/automationAccounts/modules/write|Azure Automation モジュールを作成または更新します。|
|/automationAccounts/modules/delete|Azure Automation モジュールを削除します。|
|/automationAccounts/credentials/read|Azure Automation 資格情報資産を取得します。|
|/automationAccounts/credentials/write|Azure Automation 資格情報資産を作成または更新します。|
|/automationAccounts/credentials/delete|Azure Automation 資格情報資産を削除します。|
|/automationAccounts/certificates/read|Azure Automation 証明書資産を取得します。|
|/automationAccounts/certificates/write|Azure Automation 証明書資産を作成または更新します。|
|/automationAccounts/certificates/delete|Azure Automation 証明書資産を削除します。|
|/automationAccounts/schedules/read|Azure Automation スケジュール資産を取得します。|
|/automationAccounts/schedules/write|Azure Automation スケジュール資産を作成または更新します。|
|/automationAccounts/schedules/delete|Azure Automation スケジュール資産を削除します。|
|/automationAccounts/jobs/read|Azure Automation ジョブを取得します。|
|/automationAccounts/jobs/write|Azure Automation ジョブを作成します。|
|/automationAccounts/jobs/stop/action|Azure Automation ジョブを停止します。|
|/automationAccounts/jobs/suspend/action|Azure Automation ジョブを中断します。|
|/automationAccounts/jobs/resume/action|Azure Automation ジョブを再開します。|
|/automationAccounts/jobs/runbookContent/action|ジョブ実行時に Azure Automation Runbook のコンテンツを取得します。|
|/automationAccounts/jobs/output/action|ジョブの出力を取得します。|
|/automationAccounts/jobs/read|Azure Automation ジョブを取得します。|
|/automationAccounts/jobs/write|Azure Automation ジョブを作成します。|
|/automationAccounts/jobs/stop/action|Azure Automation ジョブを停止します。|
|/automationAccounts/jobs/suspend/action|Azure Automation ジョブを中断します。|
|/automationAccounts/jobs/resume/action|Azure Automation ジョブを再開します。|
|/automationAccounts/jobs/streams/read|Azure Automation ジョブ ストリームを取得します。|
|/automationAccounts/connections/read|Azure Automation 接続資産を取得します。|
|/automationAccounts/connections/write|Azure Automation 接続資産を作成または更新します。|
|/automationAccounts/connections/delete|Azure Automation 接続資産を削除します。|
|/automationAccounts/variables/read|Azure Automation 変数資産を読み取ります。|
|/automationAccounts/variables/write|Azure Automation 変数資産を作成または更新します。|
|/automationAccounts/variables/delete|Azure Automation 変数資産を削除します。|
|/automationAccounts/runbooks/readContent/action|Azure Automation Runbook のコンテンツを取得します。|
|/automationAccounts/runbooks/read|Azure Automation Runbook を取得します。|
|/automationAccounts/runbooks/write|Azure Automation Runbook を作成または更新します。|
|/automationAccounts/runbooks/delete|Azure Automation Runbook を削除します。|
|/automationAccounts/runbooks/draft/readContent/action|Azure Automation Runbook の下書きのコンテンツを取得します。|
|/automationAccounts/runbooks/draft/writeContent/action|Azure Automation Runbook の下書きのコンテンツを作成します。|
|/automationAccounts/runbooks/draft/read|Azure Automation Runbook の下書きを取得します。|
|/automationAccounts/runbooks/draft/publish/action|Azure Automation Runbook の下書きを発行します。|
|/automationAccounts/runbooks/draft/undoEdit/action|Azure Automation Runbook の下書きの編集を元に戻します。|
|/automationAccounts/runbooks/draft/testJob/read|Azure Automation Runbook の下書きのテスト ジョブを取得します。|
|/automationAccounts/runbooks/draft/testJob/write|Azure Automation Runbook の下書きのテスト ジョブを作成します。|
|/automationAccounts/runbooks/draft/testJob/stop/action|Azure Automation Runbook の下書きのテスト ジョブを停止します。|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Azure Automation Runbook の下書きのテスト ジョブを中断します。|
|/automationAccounts/runbooks/draft/testJob/resume/action|Azure Automation Runbook の下書きのテスト ジョブを再開します。|
|/automationAccounts/webhooks/read|Azure Automation Webhook を読み取ります。|
|/automationAccounts/webhooks/write|Azure Automation Webhook を作成または更新します。|
|/automationAccounts/webhooks/delete|Azure Automation Webhook を削除します。 |
|/automationAccounts/webhooks/generateUri/action|Azure Automation Webhook の URI を生成します。|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

このプロバイダーは完全な ARM プロバイダーではないので、ARM の操作は提供されません。

## <a name="microsoftbatch"></a>Microsoft.Batch

| 操作 | Description |
|---|---|
|/register/action|Batch リソース プロバイダーにサブスクリプションを登録し、Batch アカウントを作成できるようにします。|
|/batchAccounts/write|新しい Batch アカウントを作成するか、既存の Batch アカウントを更新します。|
|/batchAccounts/read|Batch アカウントを一覧表示するか、Batch アカウントのプロパティを取得します。|
|/batchAccounts/delete|Batch アカウントを削除します。|
|/batchAccounts/listkeys/action|Batch アカウントのアクセス キーを一覧表示します。|
|/batchAccounts/regeneratekeys/action|Batch アカウントのアクセス キーを再生成します。|
|/batchAccounts/syncAutoStorageKeys/action|Batch アカウント用に構成された自動ストレージ アカウントのアクセス キーを同期します。|
|/batchAccounts/applications/read|アプリケーションを一覧表示するか、アプリケーションのプロパティを取得します。|
|/batchAccounts/applications/write|新しいアプリケーションを作成するか、既存のアプリケーションを更新します。|
|/batchAccounts/applications/delete|アプリケーションを削除します。|
|/batchAccounts/applications/versions/read|アプリケーション パッケージのプロパティを取得します。|
|/batchAccounts/applications/versions/write|新しいアプリケーション パッケージを作成するか、既存のアプリケーション パッケージを更新します。|
|/batchAccounts/applications/versions/activate/action|アプリケーション パッケージをアクティブにします。|
|/batchAccounts/applications/versions/delete|アプリケーション パッケージを削除します。|
|/locations/quotas/read|指定された Azure リージョンの指定されたサブスクリプションの Batch クォータを取得します。|

## <a name="microsoftbilling"></a>Microsoft.Billing

| 操作 | 説明 |
|---|---|
|/invoices/read|使用可能な請求書を一覧表示します。|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| 操作 | Description |
|---|---|
|/mapApis/Read|読み取り操作。|
|/mapApis/Write|書き込み操作。|
|/mapApis/Delete|削除操作。|
|/mapApis/regenerateKey/action|キーを再生成します。|
|/mapApis/listSecrets/action|シークレットを一覧表示します。|
|/mapApis/listSingleSignOnToken/action|リソースのシングル サインオン認証トークンを読み取ります。|
|/Operations/read|操作の説明。|

## <a name="microsoftcache"></a>Microsoft.Cache

| 操作 | Description |
|---|---|
|/checknameavailability/action|新しい Redis Cache で名前を使用できるかどうかを確認します。|
|/register/action|"Microsoft.Cache" リソース プロバイダーをサブスクリプションに登録します。|
|/unregister/action|サブスクリプションへの "Microsoft.Cache" リソース プロバイダーの登録を解除します。|
|/redis/write|管理ポータルで Redis Cache の設定と構成を変更します。|
|/redis/read|管理ポータルで Redis Cache の設定と構成を表示します。|
|/redis/delete|Redis Cache 全体を削除します。|
|/redis/listKeys/action|管理ポータルで Redis Cache のアクセス キーの値を表示します。|
|/redis/regenerateKey/action|管理ポータルで Redis Cache のアクセス キーの値を変更します。|
|/redis/import/action|指定された形式のデータを複数の BLOB から Redis にインポートします。|
|/redis/export/action|指定された形式のプレフィックス付きストレージ BLOB に Redis データをエクスポートします。|
|/redis/forceReboot/action|キャッシュ インスタンスを強制的に再起動します。データが失われる可能性があります。|
|/redis/stop/action|キャッシュ インスタンスを停止します。|
|/redis/start/action|キャッシュ インスタンスを開始します。|
|/redis/metricDefinitions/read|Redis Cache の利用可能なメトリックを取得します。|
|/redis/firewallRules/read|Redis Cache の IP ファイアウォール規則を取得します。|
|/redis/firewallRules/write|Redis Cache の IP ファイアウォール規則を編集します。|
|/redis/firewallRules/delete|Redis Cache の IP ファイアウォール規則を削除します。|
|/redis/listUpgradeNotifications/read|キャッシュのテナントに対する最新のアップグレード通知を一覧表示します。|
|/redis/linkedservers/read|Redis Cache に関連付けられているリンク サーバーを取得します。|
|/redis/linkedservers/write|Redis Cache にリンク サーバーを追加します。|
|/redis/linkedservers/delete|Redis Cache からリンク サーバーを削除します。|
|/redis/patchSchedules/read|Redis Cache のパッチ スケジュールを取得します。|
|/redis/patchSchedules/write|Redis Cache のパッチ スケジュールを変更します。|
|/redis/patchSchedules/delete|Redis Cache のパッチ スケジュールを削除します。|

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| 操作 | Description |
|---|---|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|サービス アプリ プリンシパルのサービス プリンシパルをプロビジョニングします。|
|/validateCertificateRegistrationInformation/Action|証明書購入オブジェクトを送信せずに検証します。|
|/register/action|Microsoft Certificates リソース プロバイダーをサブスクリプションに登録します。|
|/certificateOrders/Write|新しい certificateOrder を追加するか、既存の certificateOrder を更新します。|
|/certificateOrders/Delete|既存の AppServiceCertificate を削除します。|
|/certificateOrders/Read|CertificateOrders の一覧を取得します。|
|/certificateOrders/reissue/Action|既存の certificateorder を再発行します。|
|/certificateOrders/renew/Action|既存の certificateorder を更新します。|
|/certificateOrders/retrieveCertificateActions/Action|証明書のアクションの一覧を取得します。|
|/certificateOrders/retrieveEmailHistory/Action|証明書の電子メールの履歴を取得します。|
|/certificateOrders/resendEmail/Action|証明書の電子メールを再送信します。|
|/certificateOrders/verifyDomainOwnership/Action|ドメインの所有権を検証する|
|/certificateOrders/resendRequestEmails/Action|要求の電子メールを別の電子メール アドレスに再送信します。|
|/certificateOrders/resendRequestEmails/Action|発行済みの App Service 証明書のサイト シールを取得します。|
|/certificateOrders/certificates/Write|新しい証明書を追加するか、既存の証明書を更新します。|
|/certificateOrders/certificates/Delete|既存の証明書を削除します。|
|/certificateOrders/certificates/Read|証明書の一覧を取得します。|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| 操作 | Description |
|---|---|
|/register/action|従来のコンピューティングに登録します。|
|/checkDomainNameAvailability/action|指定されたドメイン名を使用できるかどうかを確認します。|
|/moveSubscriptionResources/action|すべてのクラシック リソースを別のサブスクリプションに移動します。|
|/validateSubscriptionMoveAvailability/action|従来の移動操作でサブスクリプションが利用可能かどうかを検証します。|
|/operatingSystemFamilies/read|Microsoft Azure で利用できるゲスト オペレーティング システムのファミリと、それぞれのファミリで利用できるオペレーティング システムのバージョンを一覧表示します。
|/capabilities/read|機能を表示します。|
|/operatingSystems/read|Microsoft Azure で現在利用できるゲスト オペレーティング システムのバージョンを一覧表示します。|
|/resourceTypes/skus/read|サポートされているリソースの種類の SKU の一覧を取得します。|
|/domainNames/read|リソースのドメイン名を返します。|
|/domainNames/write|リソースのドメイン名を追加または変更します。|
|/domainNames/delete|リソースのドメイン名を削除します。|
|/domainNames/swap/action|ステージング スロットを運用スロットに切り替えます。|
|/domainNames/serviceCertificates/read|使用するサービス証明書を返します。|
|/domainNames/serviceCertificates/write|使用するサービス証明書を追加または変更します。|
|/domainNames/serviceCertificates/delete|使用するサービス証明書を削除します。|
|/domainNames/serviceCertificates/operationStatuses/read|ドメイン名のサービス証明書の操作の状態を読み取ります。|
|/domainNames/capabilities/read|ドメイン名機能を表示します。|
|/domainNames/extensions/read|ドメイン名拡張子を返します。|
|/domainNames/extensions/write|ドメイン名拡張子を追加します。|
|/domainNames/extensions/delete|ドメイン名拡張子を削除します。|
|/domainNames/extensions/operationStatuses/read|ドメイン名拡張子の操作の状態を読み取ります。|
|/domainNames/active/write|アクティブなドメイン名を設定します。|
|/domainNames/slots/read|デプロイ スロットを表示します。|
|/domainNames/slots/write|デプロイを作成または更新します。|
|/domainNames/slots/delete|指定されたデプロイ スロットを削除します。|
|/domainNames/slots/start/action|デプロイ スロットを開始します。|
|/domainNames/slots/stop/action|デプロイ スロットを中断します。|
|/domainNames/slots/operationStatuses/read|ドメイン名スロットの操作の状態を読み取ります。|
|/domainNames/slots/roles/read|デプロイ スロットのロールを取得します。|
|/domainNames/slots/roles/extensionReferences/read|デプロイ スロット ロールの拡張子参照を返します。|
|/domainNames/slots/roles/extensionReferences/write|デプロイ スロット ロールの拡張子参照を追加または変更します。|
|/domainNames/slots/roles/extensionReferences/delete|デプロイ スロット ロールの拡張子参照を削除します。|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|ドメイン名スロット ロールの拡張子参照の操作の状態を読み取ります。|
|/domainNames/slots/roles/roleInstances/read|ロール インスタンスを取得します。|
|/domainNames/slots/roles/roleInstances/restart/action|ロール インスタンスを再起動します。|
|/domainNames/slots/roles/roleInstances/reimage/action|ロール インスタンスを再イメージ化します。|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|ドメイン名スロット ロールのロール インスタンスの操作の状態を読み取ります。|
|/domainNames/slots/state/start/write|デプロイ スロットを停止状態に変更します。|
|/domainNames/slots/state/stop/write|デプロイ スロットを開始状態に変更します。|
|/domainNames/slots/upgradeDomain/write|ドメインのウォーク アップグレードを行います。|
|/domainNames/internalLoadBalancers/read|内部ロード バランサーを取得します。|
|/domainNames/internalLoadBalancers/write|新しい内部負荷分散を作成します。|
|/domainNames/internalLoadBalancers/delete|新しい内部負荷分散を削除します。|
|/domainNames/internalLoadBalancers/operationStatuses/read|ドメイン名の内部ロード バランサーの操作の状態を読み取ります。|
|/domainNames/loadBalancedEndpointSets/read|負荷分散エンドポイント セットを表示します。|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|ドメイン名の負荷分散エンドポイント セットの操作の状態を読み取ります。|
|/domainNames/availabilitySets/read|リソースの可用性セットを表示します。|
|/quotas/read|サブスクリプションのクォータを取得します。|
|/virtualMachines/read|仮想マシンの一覧を取得します。|
|/virtualMachines/write|仮想マシンを追加または変更します。|
|/virtualMachines/delete|仮想マシンを削除します。|
|/virtualMachines/start/action|仮想マシンを開始します。|
|/virtualMachines/redeploy/action|仮想マシンを再デプロイします。|
|/virtualMachines/restart/action|仮想マシンを再起動します。|
|/virtualMachines/stop/action|仮想マシンを停止します。|
|/virtualMachines/shutdown/action|仮想マシンをシャットダウンします。|
|/virtualMachines/attachDisk/action|仮想マシンにデータ ディスクを接続します。|
|/virtualMachines/detachDisk/action|仮想マシンからデータ ディスクを切断します。|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|仮想マシンの RDP ファイルをダウンロードします。|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/read|ネットワーク インターフェイスに関連付けられたネットワーク セキュリティ グループを取得します。|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/write|ネットワーク インターフェイスに関連付けられたネットワーク セキュリティ グループを追加します。|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/delete|ネットワーク インターフェイスに関連付けられたネットワーク セキュリティ グループを削除します。|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/operationStatuses/read|仮想マシンに関連付けられたネットワーク セキュリティ グループの操作の状態を読み取ります。|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|メトリック定義を取得します。|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|診断設定を取得します。|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|診断設定を追加または変更します。|
|/virtualMachines/metrics/read|メトリックを取得します。|
|/virtualMachines/operationStatuses/read|仮想マシンの操作の状態を読み取ります。|
|/virtualMachines/extensions/read|仮想マシン拡張機能を取得します。|
|/virtualMachines/extensions/write|仮想マシン拡張機能を追加します。|
|/virtualMachines/extensions/operationStatuses/read|仮想マシン拡張機能の操作の状態を読み取ります。|
|/virtualMachines/asyncOperations/read|可能な非同期操作を取得します。|
|/virtualMachines/disks/read|データ ディスクの一覧を取得します。|
|/virtualMachines/associatedNetworkSecurityGroups/read|仮想マシンに関連付けられたネットワーク セキュリティ グループを取得します。|
|/virtualMachines/associatedNetworkSecurityGroups/write|仮想マシンに関連付けられたネットワーク セキュリティ グループを追加します。|
|/virtualMachines/associatedNetworkSecurityGroups/delete|仮想マシンに関連付けられたネットワーク セキュリティ グループを削除します。|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|仮想マシンに関連付けられたネットワーク セキュリティ グループの操作の状態を読み取ります。|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| 操作 | Description |
|---|---|
|/register/action|従来のネットワークに登録します。|
|/gatewaySupportedDevices/read|サポート対象デバイスの一覧を取得します。|
|/reservedIps/read|予約済み IP を取得します。|
|/reservedIps/write|新しい予約済み IP を追加します。|
|/reservedIps/delete|予約済み IP を削除します。|
|/reservedIps/link/action|予約済み IP をリンクします。|
|/reservedIps/join/action|予約済み IP を結合します。|
|/reservedIps/operationStatuses/read|予約済み IP の操作の状態を読み取ります。|
|/virtualNetworks/read|仮想ネットワークを取得します。|
|/virtualNetworks/write|新しい仮想ネットワークを追加します。|
|/virtualNetworks/delete|仮想ネットワークを削除します。|
|/virtualNetworks/peer/action|仮想ネットワークを別の仮想ネットワークとピアリングします。|
|/virtualNetworks/join/action|仮想ネットワークに参加します。|
|/virtualNetworks/checkIPAddressAvailability/action|指定された IP アドレスを仮想ネットワーク内で使用できるかどうかを確認します。|
|/virtualNetworks/capabilities/read|機能を表示します。|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/read|サブネットに関連付けられたネットワーク セキュリティ グループを取得します。|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/write|サブネットに関連付けられたネットワーク セキュリティ グループを追加します。|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/delete|サブネットに関連付けられたネットワーク セキュリティ グループを削除します。|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/operationStatuses/read|仮想ネットワーク サブネットに関連付けられたネットワーク セキュリティ グループの操作の状態を読み取ります。|
|/virtualNetworks/operationStatuses/read|仮想ネットワークの操作の状態を読み取ります。|
|/virtualNetworks/gateways/read|仮想ネットワーク ゲートウェイを取得します。|
|/virtualNetworks/gateways/write|仮想ネットワーク ゲートウェイを追加します。|
|/virtualNetworks/gateways/delete|仮想ネットワーク ゲートウェイを削除します。|
|/virtualNetworks/gateways/startDiagnostics/action|仮想ネットワーク ゲートウェイの診断を開始します。|
|/virtualNetworks/gateways/stopDiagnostics/action|仮想ネットワーク ゲートウェイの診断を停止します。|
|/virtualNetworks/gateways/downloadDiagnostics/action|ゲートウェイ診断をダウンロードします。|
|/virtualNetworks/gateways/listCircuitServiceKey/action|回線サービス キーを取得します。|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|デバイス構成スクリプトをダウンロードします。|
|/virtualNetworks/gateways/listPackage/action|仮想ネットワーク ゲートウェイ パッケージを一覧表示します。|
|/virtualNetworks/gateways/operationStatuses/read|仮想ネットワーク ゲートウェイの操作の状態を読み取ります。|
|/virtualNetworks/gateways/packages/read|仮想ネットワーク ゲートウェイ パッケージを取得します。|
|/virtualNetworks/gateways/connections/read|接続の一覧を取得します。|
|/virtualNetworks/gateways/connections/connect/action|サイト間ゲートウェイ接続を行います。|
|/virtualNetworks/gateways/connections/disconnect/action|サイト間ゲートウェイ接続を切断します。|
|/virtualNetworks/gateways/connections/test/action|サイト間ゲートウェイ接続をテストします。|
|/virtualNetworks/gateways/clientRevokedCertificates/read|失効したクライアント証明書を読み取ります。|
|/virtualNetworks/gateways/clientRevokedCertificates/write|クライアント証明書を失効させます。|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|クライアント証明書の失効を取り消します。|
|/virtualNetworks/gateways/clientRootCertificates/read|クライアント ルート証明書を検索します。|
|/virtualNetworks/gateways/clientRootCertificates/write|新しいクライアント ルート証明書をアップロードします。|
|/virtualNetworks/gateways/clientRootCertificates/delete|仮想ネットワーク ゲートウェイ クライアント証明書を削除します。|
|/virtualNetworks/gateways/clientRootCertificates/download/action|拇印によって証明書をダウンロードします。|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|仮想ネットワーク ゲートウェイ証明書パッケージを一覧表示します。|
|/networkSecurityGroups/read|ネットワーク セキュリティ グループを取得します。|
|/networkSecurityGroups/write|新しいネットワーク セキュリティ グループを追加します。|
|/networkSecurityGroups/delete|ネットワーク セキュリティ グループを削除します。|
|/networkSecurityGroups/operationStatuses/read|ネットワーク セキュリティ グループの操作の状態を読み取ります。|
|/networkSecurityGroups/securityRules/read|セキュリティ規則を取得します。|
|/networkSecurityGroups/securityRules/write|セキュリティ規則を追加または更新します。|
|/networkSecurityGroups/securityRules/delete|セキュリティ規則を削除します。|
|/networkSecurityGroups/securityRules/operationStatuses/read|ネットワーク セキュリティ グループのセキュリティ規則の操作の状態を読み取ります。|
|/quotas/read|サブスクリプションのクォータを取得します。|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| 操作 | Description |
|---|---|
|/register/action|従来のストレージに登録します。|
|/checkStorageAccountAvailability/action|ストレージ アカウントを使用できるかどうかを確認します。|
|/capabilities/read|機能を表示します。|
|/publicImages/read|仮想マシンのパブリック イメージを取得します。|
|/images/read|イメージを返します。|
|/storageAccounts/read|特定のアカウントのストレージ アカウントを返します。|
|/storageAccounts/write|新しいストレージ アカウントを追加します。|
|/storageAccounts/delete|ストレージ アカウントを削除します。|
|/storageAccounts/listKeys/action|ストレージ アカウントのアクセス キーを一覧表示します。|
|/storageAccounts/regenerateKey/action|ストレージ アカウントの既存のアクセス キーを再生成します。|
|/storageAccounts/operationStatuses/read|リソースの操作の状態を読み取ります。|
|/storageAccounts/images/read|ストレージ アカウント イメージを返します。|
|/storageAccounts/images/delete|指定されたストレージ アカウント イメージを削除します。|
|/storageAccounts/disks/read|ストレージ アカウント ディスクを返します。|
|/storageAccounts/disks/write|ストレージ アカウント ディスクを追加します。|
|/storageAccounts/disks/delete|指定されたストレージ アカウント ディスクを削除します。|
|/storageAccounts/disks/operationStatuses/read|リソースの操作の状態を読み取ります。|
|/storageAccounts/osImages/read|ストレージ アカウント オペレーティング システム イメージを返します。|
|/storageAccounts/osImages/delete|指定されたストレージ アカウント オペレーティング システム イメージを削除します。|
|/storageAccounts/services/read|利用可能なサービスを取得します。|
|/storageAccounts/services/metricDefinitions/read|メトリック定義を取得します。|
|/storageAccounts/services/metrics/read|メトリックを取得します。|
|/storageAccounts/services/diagnosticSettings/read|診断設定を取得します。|
|/storageAccounts/services/diagnosticSettings/write|診断設定を追加または変更します。|
|/disks/read|ストレージ アカウント ディスクを返します。|
|/osImages/read|オペレーティング システム イメージを返します。|
|/quotas/read|サブスクリプションのクォータを取得します。|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| 操作 | Description |
|---|---|
|/accounts/read|API アカウントを読み取ります。|
|/accounts/write|API アカウントを書き込みます。|
|/accounts/delete|API アカウントを削除します。|
|/accounts/listKeys/action|キーを一覧表示します。|
|/accounts/regenerateKey/action|キーを再生成します。|
|/accounts/skus/read|既存のリソースの使用可能な SKU を読み取ります。|
|/accounts/usages/read|既存のリソースのクォータ使用率を取得します。|
|/Operations/read|操作の説明。|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| 操作 | 説明 |
|---|---|
|/RateCard/read|特定のサブスクリプションのプラン データ、リソース/測定メタデータ、料金を返します。|
|/UsageAggregates/read|サブスクリプションによる Microsoft Azure の利用状況を取得します。 結果には、特定の時間の範囲における利用状況データ、サブスクリプション、およびリソースに関連する情報の集計が含まれます。|

## <a name="microsoftcompute"></a>Microsoft.Compute

| 操作 | Description |
|---|---|
|/register/action|Microsoft.Compute リソース プロバイダーにサブスクリプションを登録します。|
|/restorePointCollections/read|復元ポイント コレクションのプロパティを取得します。|
|/restorePointCollections/write|新しい復元ポイント コレクションを作成するか、既存の復元ポイント コレクションを更新します。|
|/restorePointCollections/delete|復元ポイント コレクションと、コレクションに含まれている復元ポイントを削除します。|
|/restorePointCollections/restorePoints/read|復元ポイントのプロパティを取得します。|
|/restorePointCollections/restorePoints/write|新しい復元ポイントを作成します。|
|/restorePointCollections/restorePoints/delete|復元ポイントを削除します。|
|/restorePointCollections/restorePoints/retrieveSasUris/action|復元ポイントのプロパティを BLOB SAS URI と共に取得します。|
|/virtualMachineScaleSets/read|仮想マシン スケール セットのプロパティを取得します。|
|/virtualMachineScaleSets/write|新しい仮想マシン スケール セットを作成するか、既存の仮想マシン スケール セットを更新します。|
|/virtualMachineScaleSets/delete|仮想マシン スケール セットを削除します。|
|/virtualMachineScaleSets/start/action|仮想マシン スケール セットのインスタンスを起動します。|
|/virtualMachineScaleSets/powerOff/action|仮想マシン スケール セットのインスタンスを電源オフにします。|
|/virtualMachineScaleSets/restart/action|仮想マシン スケール セットのインスタンスを再起動します。|
|/virtualMachineScaleSets/deallocate/action|仮想マシン スケール セットのインスタンスのコンピューティング リソースを電源オフにして解放します。 |
|/virtualMachineScaleSets/manualUpgrade/action|インスタンスを仮想マシン スケール セットの最新モデルに手動で更新します。|
|/virtualMachineScaleSets/scale/action|既存の仮想マシン スケール セットのスケールイン/スケールアウト インスタンス数。|
|/virtualMachineScaleSets/instanceView/read|仮想マシン スケール セットのインスタンス ビューを取得します。|
|/virtualMachineScaleSets/skus/read|既存の仮想マシン スケール セットの有効な SKU を一覧表示します。|
|/virtualMachineScaleSets/virtualMachines/read|VM スケール セット内の仮想マシンのプロパティを取得します。|
|/virtualMachineScaleSets/virtualMachines/delete|VM スケール セット内の特定の仮想マシンを削除します。|
|/virtualMachineScaleSets/virtualMachines/start/action|VM スケール セット内の仮想マシン インスタンスを起動します。|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|VM スケール セット内の仮想マシン インスタンスを電源オフにします。|
|/virtualMachineScaleSets/virtualMachines/restart/action|VM スケール セット内の仮想マシン インスタンスを再起動します。|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|VM スケール セット内の仮想マシンのコンピューティング リソースを電源オフにして解放します。|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|VM スケール セット内の仮想マシンのインスタンス ビューを取得します。|
|/images/read|イメージのプロパティを取得します。|
|/images/write|新しいイメージを作成するか、既存のイメージを更新します。|
|/images/delete|イメージを削除します。|
|/operations/read|Microsoft.Compute リソース プロバイダーで使用できる操作を一覧表示します。|
|/disks/read|ディスクのプロパティを取得します。|
|/disks/write|新しいディスクを作成するか、既存のディスクを更新します。|
|/disks/delete|ディスクを削除します。|
|/disks/beginGetAccess/action|BLOB へのアクセス用にディスクの SAS URI を取得します。|
|/disks/endGetAccess/action|ディスクの SAS URI を取り消します。|
|/snapshots/read|スナップショットのプロパティを取得します。|
|/snapshots/write|新しいスナップショットを作成するか、既存のスナップショットを更新します。|
|/snapshots/delete|スナップショットを削除します。|
|/availabilitySets/read|可用性セットのプロパティを取得します。|
|/availabilitySets/write|新しい可用性セットを作成するか、既存の可用性セットを更新します。|
|/availabilitySets/delete|可用性セットを削除します。|
|/availabilitySets/vmSizes/read|可用性セットの仮想マシンを作成または更新する際に使用できるサイズを一覧表示します。|
|/virtualMachines/read|仮想マシンのプロパティを取得します。|
|/virtualMachines/write|新しい仮想マシンを作成するか、既存の仮想マシンを更新します。|
|/virtualMachines/delete|仮想マシンを削除します。|
|/virtualMachines/start/action|仮想マシンを起動します。|
|/virtualMachines/powerOff/action|仮想マシンを電源オフにします。 仮想マシンの料金は引き続き課金されます。|
|/virtualMachines/redeploy/action|仮想マシンを再デプロイします。|
|/virtualMachines/restart/action|仮想マシンを再起動します。|
|/virtualMachines/deallocate/action|仮想マシンを電源オフにし、コンピューティング リソースを解放します。|
|/virtualMachines/generalize/action|仮想マシンの状態を [一般化] に設定し、仮想マシンのキャプチャを準備します。|
|/virtualMachines/capture/action|仮想ハード ディスクをコピーして仮想マシンをキャプチャし、テンプレートを生成します。このテンプレートを使用して、類似する仮想マシンを作成できます。|
|/virtualMachines/convertToManagedDisks/action|仮想マシンの BLOB ベースのディスクを管理ディスクに変換します。|
|/virtualMachines/vmSizes/read|仮想マシンを更新する際に使用できるサイズを一覧表示します。|
|/virtualMachines/instanceView/read|仮想マシンとそのリソースの詳細なランタイムの状態を取得します。|
|/virtualMachines/extensions/read|仮想マシン拡張機能のプロパティを取得します。|
|/virtualMachines/extensions/write|新しい仮想マシン拡張機能を作成するか、既存の仮想マシン拡張機能を更新します。|
|/virtualMachines/extensions/delete|仮想マシン拡張機能を削除します。|
|/locations/vmSizes/read|その場所で使用できる仮想マシンのサイズを一覧表示します。|
|/locations/usages/read|その場所のサブスクリプションのコンピューティング リソースについて、サービスの上限と現在の使用容量を取得します。|
|/locations/operations/read|非同期操作の状態を取得します。|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| 操作 | Description |
|---|---|
|/register/action|コンテナー レジストリ リソース プロバイダーにサブスクリプションを登録し、コンテナー レジストリを作成できるようにします。|
|/checknameavailability/read|レジストリ名が有効であり、使用されていないことを確認します。|
|/registries/read|コンテナー レジストリの一覧を返すか、指定されたコンテナー レジストリのプロパティを取得します。|
|/registries/write|指定されたパラメーターを使用してコンテナー レジストリを作成するか、指定されたコンテナー レジストリのプロパティまたはタグを更新します。|
|/registries/delete|既存のコンテナー レジストリを削除します。|
|/registries/listCredentials/action|指定されたコンテナー レジストリのログイン資格情報を一覧表示します。|
|/registries/regenerateCredential/action|指定されたコンテナー レジストリのログイン資格情報を再生成します。|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| 操作 | Description |
|---|---|
|/containerServices/subscriptions/read|サブスクリプションに基づいて、指定されたコンテナー サービスを取得します。|
|/containerServices/resourceGroups/read|リソース グループに基づいて、指定されたコンテナー サービスを取得します。|
|/containerServices/resourceGroups/ContainerServiceName/read|指定されたコンテナー サービスを取得します。|
|/containerServices/resourceGroups/ContainerServiceName/write|指定されたコンテナー サービスを追加または更新します。|
|/containerServices/resourceGroups/ContainerServiceName/delete|指定されたコンテナー サービスを削除します。|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| 操作 | Description |
|---|---|
|/updateCommunicationPreference/action|通信設定を更新します。|
|/listCommunicationPreference/action|通信設定を一覧表示します。|
|/applications/read|読み取り操作。|
|/applications/write|書き込み操作。|
|/applications/write|書き込み操作。|
|/applications/delete|削除操作。|
|/applications/listSecrets/action|シークレットを一覧表示します。|
|/applications/listSingleSignOnToken/action|シングル サインオン トークンを読み取ります。|
|/operations/read|操作を読み取ります。|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| 操作 | Description |
|---|---|
|/hubs/read|Azure Customer Insights Hub を読み取ります。|
|/hubs/write|Azure Customer Insights Hub を作成または更新します。|
|/hubs/delete|Azure Customer Insights Hub を削除します。|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|リソースの利用可能なメトリックを取得します。|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|リソースの診断設定を取得します。|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|リソースの利用可能なログを取得します。|
|/hubs/authorizationPolicies/read|Azure Customer Insights の Shared Access Signature ポリシーを読み取ります。|
|/hubs/authorizationPolicies/write|Azure Customer Insights の Shared Access Signature ポリシーを作成または更新します。|
|/hubs/authorizationPolicies/delete|Azure Customer Insights の Shared Access Signature ポリシーを削除します。|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Azure Customer Insights の Shared Access Signature ポリシーのプライマリ キーを再生成します。|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Azure Customer Insights の Shared Access Signature ポリシーのセカンダリ キーを再生成します。|
|/hubs/profiles/read|Azure Customer Insights プロファイルを読み取ります。|
|/hubs/profiles/write|Azure Customer Insights プロファイルを書き込みます。|
|/hubs/kpi/read|Azure Customer Insights の主要業績評価指標を読み取ります。|
|/hubs/kpi/write|Azure Customer Insights の主要業績評価指標を作成または更新します。|
|/hubs/kpi/delete|Azure Customer Insights の主要業績評価指標を削除します。|
|/hubs/views/read|Azure Customer Insights のアプリ ビューを読み取ります。|
|/hubs/views/write|Azure Customer Insights のアプリ ビューを作成または更新します。|
|/hubs/views/delete|Azure Customer Insights のアプリ ビューを削除します。|
|/hubs/interactions/read|Azure Customer Insights の対話を読み取ります。|
|/hubs/interactions/write|Azure Customer Insights の対話を作成または更新します。|
|/hubs/roleAssignments/read|Azure Customer Insights の RBAC の割り当てを読み取ります。|
|/hubs/roleAssignments/write|Azure Customer Insights の RBAC の割り当てを作成または更新します。|
|/hubs/roleAssignments/delete|Azure Customer Insights の RBAC の割り当てを削除します。|
|/hubs/connectors/read|Azure Customer Insights コネクタを読み取ります。|
|/hubs/connectors/write|Azure Customer Insights コネクタを作成または更新します。|
|/hubs/connectors/delete|Azure Customer Insights コネクタを削除します。|
|/hubs/connectors/mappings/read|Azure Customer Insights コネクタ マッピングを読み取ります。|
|/hubs/connectors/mappings/write|Azure Customer Insights コネクタ マッピングを作成または更新します。|
|/hubs/connectors/mappings/delete|Azure Customer Insights コネクタ マッピングを削除します。|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| 操作 | 説明 |
|---|---|
|/checkNameAvailability/action|テナントのカタログ名を使用できるかどうかを確認します。|
|/catalogs/read|サブスクリプションまたはリソース グループでカタログのプロパティを取得します。|
|/catalogs/write|カタログを作成するか、カタログのタグとプロパティを更新します。|
|/catalogs/delete|カタログを削除します。|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| 操作 | 説明 |
|---|---|
|/datafactories/read|データ ファクトリを読み取ります。|
|/datafactories/write|データ ファクトリを作成または更新します。|
|/datafactories/delete|データ ファクトリを削除します。|
|/datafactories/datapipelines/read|パイプラインを読み取ります。|
|/datafactories/datapipelines/delete|パイプラインを削除します。|
|/datafactories/datapipelines/pause/action|パイプラインを一時停止します。|
|/datafactories/datapipelines/resume/action|パイプラインを再開します。|
|/datafactories/datapipelines/update/action|パイプラインを更新します。|
|/datafactories/datapipelines/write|パイプラインを作成または更新します。|
|/datafactories/linkedServices/read|リンクされたサービスを読み取ります。|
|/datafactories/linkedServices/delete|リンクされたサービスを削除します。|
|/datafactories/linkedServices/write|リンクされたサービスを作成または更新します。|
|/datafactories/tables/read|テーブルを読み取ります。|
|/datafactories/tables/delete|テーブルを削除します。|
|/datafactories/tables/write|テーブルを作成または更新します。|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| 操作 | Description |
|---|---|
|/accounts/read|DataLakeAnalytics アカウントに関する情報を取得します。|
|/accounts/write|DataLakeAnalytics アカウントを作成または更新します。|
|/accounts/delete|DataLakeAnalytics アカウントを削除します。|
|/accounts/firewallRules/read|ファイアウォール規則に関する情報を取得します。|
|/accounts/firewallRules/write|ファイアウォール規則を作成または更新します。|
|/accounts/firewallRules/delete|ファイアウォール規則を削除します。|
|/accounts/storageAccounts/read|DataLakeAnalytics アカウントのリンクされているストレージ アカウントを取得します。|
|/accounts/storageAccounts/write|ストレージ アカウントを DataLakeAnalytics アカウントにリンクします。|
|/accounts/storageAccounts/delete|DataLakeAnalytics アカウントからストレージ アカウントのリンクを解除します。|
|/accounts/storageAccounts/Containers/read|ストレージ アカウントのコンテナーを取得します。|
|/accounts/storageAccounts/Containers/listSasTokens/action|ストレージ コンテナーの SAS トークンを一覧表示します。|
|/accounts/dataLakeStoreAccounts/read|DataLakeAnalytics アカウントのリンクされている DataLakeStore アカウントを取得します。|
|/accounts/dataLakeStoreAccounts/write|DataLakeStore アカウントを DataLakeAnalytics アカウントにリンクします。|
|/accounts/dataLakeStoreAccounts/delete|DataLakeAnalytics アカウントから DataLakeStore アカウントのリンクを解除します。|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| 操作 | Description |
|---|---|
|/accounts/read|既存の DataLakeStore アカウントに関する情報を取得します。|
|/accounts/write|新しい DataLakeStore アカウントを作成するか、既存の DataLakeStore アカウントを更新します。|
|/accounts/delete|既存の DataLakeStore アカウントを削除します。|
|/accounts/firewallRules/read|ファイアウォール規則に関する情報を取得します。|
|/accounts/firewallRules/write|ファイアウォール規則を作成または更新します。|
|/accounts/firewallRules/delete|ファイアウォール規則を削除します。|
|/accounts/trustedIdProviders/read|信頼できる ID プロバイダーに関する情報を取得します。|
|/accounts/trustedIdProviders/write|信頼できる ID プロバイダーを作成または更新します。|
|/accounts/trustedIdProviders/delete|信頼できる ID プロバイダーを削除します。|

## <a name="microsoftdevices"></a>Microsoft.Devices

| 操作 | Description |
|---|---|
|/register/action|IotHub リソース プロバイダーにサブスクリプションを登録し、IotHub リソースを作成できるようにします。|
|/checkNameAvailability/Action|IotHub 名を使用できるかどうかを確認します。|
|/usages/Read|このプロバイダーのサブスクリプションの使用状況の詳細を取得します。|
|/operations/Read|ResourceProvider のすべての操作を取得します。|
|/iotHubs/Read|IotHub リソースを取得します。|
|/iotHubs/Write|IotHub リソースを作成または更新します。|
|/iotHubs/Delete|IotHub リソースを削除します。|
|/iotHubs/listkeys/Action|すべての IotHub キーを取得します。|
|/iotHubs/exportDevices/Action|デバイスをエクスポートします。|
|/iotHubs/importDevices/Action|デバイスをインポートします。|
|/IotHubs/metricDefinitions/read|IotHub サービスの利用可能なメトリックを取得します。|
|/iotHubs/iotHubKeys/listkeys/Action|特定の名前の IotHub キーを取得します。|
|/iotHubs/iotHubStats/Read|IotHub の統計を取得します。|
|/iotHubs/quotaMetrics/Read|クォータ メトリックを取得します。|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|EventHub コンシューマー グループを作成します。|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|EventHub コンシューマー グループを取得します。|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|EventHub コンシューマー グループを削除します。|
|/iotHubs/routing/routes/$testall/Action|既存のすべてのルートに対してメッセージをテストします。|
|/iotHubs/routing/routes/$testnew/Action|指定されたテスト ルートに対してメッセージをテストします。|
|/IotHubs/diagnosticSettings/read|リソースの診断設定を取得します。|
|/IotHubs/diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/iotHubs/skus/Read|有効な IotHub SKU を取得します。|
|/iotHubs/jobs/Read|特定の IotHub で送信されたジョブの詳細を取得します。|
|/iotHubs/routingEndpointsHealth/Read|IotHub のすべてのルーティング エンドポイントの正常性を取得します。|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| 操作 | Description |
|---|---|
|/Subscription/register/action|サブスクリプションを登録します。|
|/labs/delete|ラボを削除します。|
|/labs/read|ラボを読み取ります。|
|/labs/write|ラボを追加または変更します。|
|/labs/ListVhds/action|カスタム イメージの作成に使用できるディスク イメージを一覧表示します。|
|/labs/GenerateUploadUri/action|カスタム ディスク イメージをラボにアップロードするための URI を生成します。|
|/labs/CreateEnvironment/action|ラボで仮想マシンを作成します。|
|/labs/ClaimAnyVm/action|ラボ内のランダムに要求可能な仮想マシンを要求します。|
|/labs/ExportResourceUsage/action|ラボのリソース使用量をストレージ アカウントにエクスポートします。|
|/labs/users/delete|ユーザー プロファイルを削除します。|
|/labs/users/read|ユーザー プロファイルを読み取ります。|
|/labs/users/write|ユーザー プロファイルを追加または変更します。|
|/labs/users/secrets/delete|シークレットを削除します。|
|/labs/users/secrets/read|シークレットを読み取ります。|
|/labs/users/secrets/write|シークレットを追加または変更します。|
|/labs/users/environments/delete|環境を削除します。|
|/labs/users/environments/read|環境を読み取ります。|
|/labs/users/environments/write|環境を追加または変更します。|
|/labs/users/disks/delete|ディスクを削除します。|
|/labs/users/disks/read|ディスクを読み取ります。|
|/labs/users/disks/write|ディスクを追加または変更します。|
|/labs/users/disks/Attach/action|仮想マシンにディスクを接続し、ディスクのリースを作成します。|
|/labs/users/disks/Detach/action|仮想マシンに接続されているディスクを切断し、ディスクのリースを解除します。|
|/labs/customImages/delete|カスタム イメージを削除します。|
|/labs/customImages/read|カスタム イメージを読み取ります。|
|/labs/customImages/write|カスタム イメージを追加または変更します。|
|/labs/serviceRunners/delete|サービス ランナーを削除します。|
|/labs/serviceRunners/read|サービス ランナーを読み取ります。|
|/labs/serviceRunners/write|サービス ランナーを追加または変更します。|
|/labs/artifactSources/delete|アーティファクト ソースを削除します。|
|/labs/artifactSources/read|アーティファクト ソースを読み取ります。|
|/labs/artifactSources/write|アーティファクト ソースを追加または変更します。|
|/labs/artifactSources/artifacts/read|アーティファクトを読み取ります。|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|特定のアーティファクトの ARM テンプレートを生成し、必要なファイルをストレージ アカウントにアップロードして、生成されたアーティファクトを検証します。|
|/labs/artifactSources/armTemplates/read|Azure Resource Manager テンプレートを読み取ります。|
|/labs/costs/read|コストを読み取ります。|
|/labs/costs/write|コストを追加または変更します。|
|/labs/virtualNetworks/delete|仮想ネットワークを削除します。|
|/labs/virtualNetworks/read|仮想ネットワークを読み取ります。|
|/labs/virtualNetworks/write|仮想ネットワークを追加または変更します。|
|/labs/formulas/delete|数式を削除します。|
|/labs/formulas/read|数式を読み取ります。|
|/labs/formulas/write|数式を追加または変更します。|
|/labs/schedules/delete|スケジュールを削除します。|
|/labs/schedules/read|スケジュールを読み取ります。|
|/labs/schedules/write|スケジュールを追加または変更します。|
|/labs/schedules/Execute/action|スケジュールを実行します。|
|/labs/schedules/ListApplicable/action|適用可能なすべてのスケジュールを一覧表示します。|
|/labs/galleryImages/read|ギャラリー イメージを読み取ります。|
|/labs/policySets/EvaluatePolicies/action|ラボ ポリシーを評価します。|
|/labs/policySets/policies/delete|ポリシーを削除します。|
|/labs/policySets/policies/read|ポリシーを読み取ります。|
|/labs/policySets/policies/write|ポリシーを追加または変更します。|
|/labs/virtualMachines/delete|仮想マシンを削除します。|
|/labs/virtualMachines/read|仮想マシンを読み取ります。|
|/labs/virtualMachines/write|仮想マシンを追加または変更します。|
|/labs/virtualMachines/Start/action|仮想マシンを起動します。|
|/labs/virtualMachines/Stop/action|仮想マシンの停止|
|/labs/virtualMachines/ApplyArtifacts/action|仮想マシンにアーティファクトを適用します。|
|/labs/virtualMachines/AddDataDisk/action|新規または既存のデータ ディスクを仮想マシンに接続します。|
|/labs/virtualMachines/DetachDataDisk/action|指定されたディスクを仮想マシンから切断します。|
|/labs/virtualMachines/Claim/action|既存の仮想マシンの所有権を取得します。|
|/labs/virtualMachines/ListApplicableSchedules/action|適用可能なすべてのスケジュールを一覧表示します。|
|/labs/virtualMachines/schedules/delete|スケジュールを削除します。|
|/labs/virtualMachines/schedules/read|スケジュールを読み取ります。|
|/labs/virtualMachines/schedules/write|スケジュールを追加または変更します。|
|/labs/virtualMachines/schedules/Execute/action|スケジュールを実行します。|
|/labs/notificationChannels/delete|notificationchannels を削除します。|
|/labs/notificationChannels/read|notificationchannels を読み取ります。|
|/labs/notificationChannels/write|notificationchannels を追加または変更します。|
|/labs/notificationChannels/Notify/action|指定されたチャンネルに通知を送信します。|
|/schedules/delete|スケジュールを削除します。|
|/schedules/read|スケジュールを読み取ります。|
|/schedules/write|スケジュールを追加または変更します。|
|/schedules/Execute/action|スケジュールを実行します。|
|/schedules/Retarget/action|スケジュールのターゲット リソース ID を更新します。|
|/locations/operations/read|操作を読み取ります。|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| 操作 | Description |
|---|---|
|/databaseAccountNames/read|名前を使用できるかどうかを確認します。|
|/databaseAccounts/read|データベース アカウントを読み取ります。|
|/databaseAccounts/write|データベース アカウントを更新します。|
|/databaseAccounts/listKeys/action|データベース アカウントのキーを一覧表示します。|
|/databaseAccounts/regenerateKey/action|データベース アカウントのキーをローテーションします。|
|/databaseAccounts/listConnectionStrings/action|データベース アカウントの接続文字列を取得します。|
|/databaseAccounts/changeResourceGroup/action|データベース アカウントのリソース グループを変更します。|
|/databaseAccounts/failoverPriorityChange/action|データベース アカウントのリージョンのフェールオーバーの優先度を変更します。 これは手動フェールオーバー操作を実行する際に使用します。|
|/databaseAccounts/delete|データベース アカウントを削除します。|
|/databaseAccounts/metricDefinitions/read|データベース アカウントのメトリック定義を読み取ります。|
|/databaseAccounts/metrics/read|データベース アカウントのメトリックを読み取ります。|
|/databaseAccounts/usages/read|データベース アカウントの使用状況を読み取ります。|
|/databaseAccounts/databases/collections/metricDefinitions/read|コレクションのメトリック定義を読み取ります。|
|/databaseAccounts/databases/collections/metrics/read|コレクションのメトリックを読み取ります。|
|/databaseAccounts/databases/collections/usages/read|コレクションの使用状況を読み取ります。|
|/databaseAccounts/databases/metricDefinitions/read|データベースのメトリック定義を読み取ります。|
|/databaseAccounts/databases/metrics/read|データベースのメトリックを読み取ります。|
|/databaseAccounts/databases/usages/read|データベースの使用状況を読み取ります。|
|/databaseAccounts/readonlykeys/read|データベース アカウントの読み取り専用キーを読み取ります。|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| 操作 | Description |
|---|---|
|/generateSsoRequest/Action|ドメイン コントロール センターへのサインイン要求を生成します。|
|/validateDomainRegistrationInformation/Action|ドメイン購入オブジェクトを送信せずに検証します。|
|/checkDomainAvailability/Action|ドメインを購入できるかどうかを確認します。|
|/listDomainRecommendations/Action|キーワードに基づいて、ドメインの推奨事項の一覧を取得します。|
|/register/action|Microsoft Domains リソース プロバイダーをサブスクリプションに登録します。|
|/domains/Read|ドメインの一覧を取得します。|
|/domains/Write|新しいドメインを追加するか、既存のドメインを更新します。|
|/domains/Delete|既存のドメインを削除します。|
|/domains/operationresults/Read|ドメイン操作を取得します。|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| 操作 | Description |
|---|---|
|/lcsprojects/read|ユーザーに属する Microsoft Dynamics Lifecycle Services プロジェクトを表示します。|
|/lcsprojects/write|ユーザーに属する Microsoft Dynamics Lifecycle Services プロジェクトを作成および更新します。 更新できるのは、名前プロパティと説明プロパティだけです。 プロジェクトに関連付けられているサブスクリプションと場所は、プロジェクトの作成後に更新することはできません。|
|/lcsprojects/delete|ユーザーに属する Microsoft Dynamics Lifecycle Services プロジェクトを削除します。|
|/lcsprojects/clouddeployments/read|ユーザーに属する Microsoft Dynamics Lifecycle Services プロジェクトの Microsoft Dynamics AX 2012 R3 Evaluation デプロイを表示します。|
|/lcsprojects/clouddeployments/write|ユーザーに属する Microsoft Dynamics Lifecycle Services プロジェクトの Microsoft Dynamics AX 2012 R3 Evaluation デプロイを作成します。 デプロイは Azure 管理ポータルから管理できます。|
|/lcsprojects/connectors/read|Microsoft Dynamics Lifecycle Services プロジェクトに属するコネクタを読み取ります。|
|/lcsprojects/connectors/write|Microsoft Dynamics Lifecycle Services プロジェクトに属するコネクタを作成および更新します。|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| 操作 | 説明 |
|---|---|
|/checkNameAvailability/action|特定のサブスクリプションで名前空間を使用できるかどうかを確認します。|
|/register/action|EventHub リソース プロバイダーにサブスクリプションを登録し、EventHub リソースを作成できるようにします。|
|/namespaces/write|名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグと状態は、更新できるプロパティです。|
|/namespaces/read|名前空間リソースの説明の一覧を取得します。|
|/namespaces/Delete|名前空間リソースを削除します。|
|/namespaces/metricDefinitions/read|名前空間のメトリック リソースの説明の一覧を取得します。|
|/namespaces/authorizationRules/read|名前空間の承認規則の説明の一覧を取得します。|
|/namespaces/authorizationRules/write|名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。|
|/namespaces/authorizationRules/delete|名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。 |
|/namespaces/authorizationRules/listkeys/action|名前空間への接続文字列を取得します。|
|/namespaces/authorizationRules/regenerateKeys/action|リソースのプライマリ キーまたはセカンダリ キーを再生成します。|
|/namespaces/eventhubs/write|EventHub のプロパティを作成または更新します。|
|/namespaces/eventhubs/read|EventHub リソースの説明の一覧を取得します。|
|/namespaces/eventhubs/Delete|EventHub リソースを削除する操作。|
|/namespaces/eventHubs/consumergroups/write|ConsumerGroup のプロパティを作成または更新します。|
|/namespaces/eventHubs/consumergroups/read|ConsumerGroup リソースの説明の一覧を取得します。|
|/namespaces/eventHubs/consumergroups/Delete|ConsumerGroup リソースを削除する操作。|
|/namespaces/eventhubs/authorizationRules/read| EventHub の承認規則の一覧を取得します。|
|/namespaces/eventhubs/authorizationRules/write|EventHub の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。|
|/namespaces/eventhubs/authorizationRules/delete|EventHub の承認規則を削除する操作。|
|/namespaces/eventhubs/authorizationRules/listkeys/action|EventHub への接続文字列を取得します。|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|リソースのプライマリ キーまたはセカンダリ キーを再生成します。|
|/namespaces/diagnosticSettings/read|名前空間の診断設定リソースの説明の一覧を取得します。|
|/namespaces/diagnosticSettings/write|名前空間の診断設定リソースの説明の一覧を取得します。|
|/namespaces/logDefinitions/read|名前空間のログ リソースの説明の一覧を取得します。|

## <a name="microsoftfeatures"></a>Microsoft.Features

| 操作 | Description |
|---|---|
|/providers/features/read|指定されたリソース プロバイダーのサブスクリプションの機能を取得します。|
|/providers/features/register/action|指定されたリソース プロバイダーのサブスクリプションの機能を登録します。|
|/features/read|サブスクリプションの機能を取得します。|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| 操作 | Description |
|---|---|
|/clusters/write|HDInsight クラスターを作成または更新します。|
|/clusters/read|HDInsight クラスターの詳細を取得します。|
|/clusters/delete|HDInsight クラスターを削除します。|
|/clusters/changerdpsetting/action|HDInsight クラスターの RDP 設定を変更します。|
|/clusters/configurations/action|HDInsight クラスターの構成を更新します。|
|/clusters/configurations/read|HDInsight クラスターの構成を取得します。|
|/clusters/roles/resize/action|HDInsight クラスターのサイズを変更します。|
|/locations/capabilities/read|サブスクリプション機能を取得します。|
|/locations/checkNameAvailability/read|名前を使用できるかどうかを確認します。|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| 操作 | Description |
|---|---|
|/register/action|Import/Export リソース プロバイダーにサブスクリプションを登録し、インポート/エクスポート ジョブを作成できるようにします。|
|/jobs/write|指定されたパラメーターを使用してジョブを作成するか、指定されたジョブのプロパティまたはタグを更新します。|
|/jobs/read|指定されたジョブのプロパティを取得するか、ジョブの一覧を返します。|
|/jobs/listBitLockerKeys/action|指定されたジョブの BitLocker キーを取得します。|
|/jobs/delete|既存のジョブを削除します。|
|/locations/read|指定された場所のプロパティを取得するか、場所の一覧を返します。|

## <a name="microsoftinsights"></a>Microsoft.Insights

| 操作 | 説明 |
|---|---|
|/Register/Action|Microsoft Insights プロバイダーを登録します。|
|/AlertRules/Write|アラート ルールの構成に書き込みます。|
|/AlertRules/Delete|アラート ルールの構成を削除します。|
|/AlertRules/Read|アラート ルールの構成を読み取ります。|
|/AlertRules/Activated/Action|アラート ルールがアクティブ化されました。|
|/AlertRules/Resolved/Action|アラート ルールが解決されました。|
|/AlertRules/Throttled/Action|アラート ルールが調整されました。|
|/AlertRules/Incidents/Read|アラート ルール インシデントの構成を読み取ります。|
|/MetricDefinitions/Read|メトリック定義を読み取ります。|
|/eventtypes/values/Read|イベントの種類の管理の値を読み取ります。|
|/eventtypes/digestevents/Read|イベントの種類の管理のダイジェストを読み取ります。|
|/Metrics/Read|メトリックを読み取ります。|
|/LogProfiles/Write|ログ プロファイルの構成に書き込みます。|
|/LogProfiles/Delete|ログ プロファイルの構成を削除します。|
|/LogProfiles/Read|ログ プロファイルを読み取ります。|
|/AutoscaleSettings/Write|自動スケール設定の構成に書き込みます。|
|/AutoscaleSettings/Delete|自動スケール設定の構成を削除します。|
|/AutoscaleSettings/Read|自動スケール設定の構成を読み取ります。|
|/AutoscaleSettings/Scaleup/Action|自動スケールのスケールアップ操作。|
|/AutoscaleSettings/Scaledown/Action|自動スケールのスケールダウン操作。|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|メトリック定義を読み取ります。|
|/ActivityLogAlerts/Activated/Action|アクティビティ ログ アラートをトリガーしました。|
|/DiagnosticSettings/Write|診断設定の構成に書き込みます。|
|/DiagnosticSettings/Delete|診断設定の構成を削除します。|
|/DiagnosticSettings/Read|診断設定の構成を読み取ります。|
|/LogDefinitions/Read|ログ定義を読み取ります。|
|/ExtendedDiagnosticSettings/Write|拡張診断設定の構成に書き込みます。|
|/ExtendedDiagnosticSettings/Delete|拡張診断設定の構成を削除します。|
|/ExtendedDiagnosticSettings/Read|拡張診断設定の構成を読み取ります。|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| 操作 | Description |
|---|---|
|/register/action|サブスクリプションを登録します。|
|/checkNameAvailability/read|Key Vault 名が有効であり、使用されていないことを確認します。|
|/vaults/read|Key Vault のプロパティを表示します。|
|/vaults/write|新しい Key Vault を作成するか、既存の Key Vault のプロパティを更新します。|
|/vaults/delete|Key Vault を削除します。|
|/vaults/deploy/action|Azure リソースのデプロイ時に、Key Vault 内のシークレットにアクセスできるようにします。|
|/vaults/secrets/read|シークレットの値ではなく、プロパティを表示します。|
|/vaults/secrets/write|新しいシークレットを作成するか、既存のシークレットの値を更新します。|
|/vaults/accessPolicies/write|既存のアクセス ポリシーをマージまたは置換して更新するか、資格情報コンテナーに新しいアクセス ポリシーを追加します。|
|/deletedVaults/read|論理的に削除された Key Vault のプロパティを表示します。|
|/locations/operationResults/read|長時間実行された操作の結果を確認します。|
|/locations/deletedVaults/read|論理的に削除された Key Vault のプロパティを表示します。|
|/locations/deletedVaults/purge/action|論理的に削除された Key Vault を消去します。|

## <a name="microsoftlogic"></a>Microsoft.Logic

| 操作 | 説明 |
|---|---|
|/workflows/read|ワークフローを読み取ります。|
|/workflows/write|ワークフローを作成または更新します。|
|/workflows/delete|ワークフローを削除します。|
|/workflows/run/action|ワークフローの実行を開始します。|
|/workflows/disable/action|ワークフローを無効にします。|
|/workflows/enable/action|ワークフローを有効にします。|
|/workflows/validate/action|ワークフローを検証します。|
|/workflows/move/action|ワークフローを既存のサブスクリプション ID、リソース グループ、または名前から、別のサブスクリプション ID、リソース グループ、または名前に移動します。|
|/workflows/listSwagger/action|ワークフローの Swagger 定義を取得します。|
|/workflows/regenerateAccessKey/action|アクセス キーのシークレットを再生成します。|
|/workflows/listCallbackUrl/action|ワークフローのコールバック URL を取得します。|
|/workflows/versions/read|ワークフローのバージョンを読み取ります。|
|/workflows/versions/triggers/listCallbackUrl/action|トリガーのコールバック URL を取得します。|
|/workflows/runs/read|ワークフロー実行を読み取ります。|
|/workflows/runs/cancel/action|ワークフローの実行を取り消します。|
|/workflows/runs/actions/read|ワークフロー実行アクションを読み取ります。|
|/workflows/runs/operations/read|ワークフロー実行操作の状態を読み取ります。|
|/workflows/triggers/read|トリガーを読み取ります。|
|/workflows/triggers/run/action|トリガーを実行します。|
|/workflows/triggers/listCallbackUrl/action|トリガーのコールバック URL を取得します。|
|/workflows/triggers/histories/read|トリガーの履歴を読み取ります。|
|/workflows/triggers/histories/resubmit/action|ワークフロー トリガーを再送信します。|
|/workflows/accessKeys/read|アクセス キーを読み取ります。|
|/workflows/accessKeys/write|アクセス キーを作成または更新します。|
|/workflows/accessKeys/delete|アクセス キーを削除します。|
|/workflows/accessKeys/list/action|アクセス キーのシークレットを一覧表示します。|
|/workflows/accessKeys/regenerate/action|アクセス キーのシークレットを再生成します。|
|/locations/workflows/validate/action|ワークフローを検証します。|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| 操作 | Description |
|---|---|
|/register/action|Machine Learning Web サービス リソース プロバイダーにサブスクリプションを登録し、Web サービスを作成できるようにします。|
|/webServices/action|サポートされているリージョンのリージョン固有の Web サービス プロパティを作成します。|
|/commitmentPlans/read|Machine Learning コミットメント プランを読み取ります。|
|/commitmentPlans/write|Machine Learning コミットメント プランを作成または更新します。|
|/commitmentPlans/delete|Machine Learning コミットメント プランを削除します。|
|/commitmentPlans/join/action|Machine Learning コミットメント プランに加入します。|
|/commitmentPlans/commitmentAssociations/read|Machine Learning コミットメント プランの関連付けを読み取ります。|
|/commitmentPlans/commitmentAssociations/move/action|Machine Learning コミットメント プランの関連付けを移動します。|
|/Workspaces/read|Machine Learning ワークスペースを読み取ります。|
|/Workspaces/write|Machine Learning ワークスペースを作成または更新します。|
|/Workspaces/delete|Machine Learning ワークスペースを削除します。|
|/Workspaces/listworkspacekeys/action|Machine Learning ワークスペースのキーを一覧表示します。|
|/Workspaces/resyncstoragekeys/action|Machine Learning ワークスペース用に構成されたストレージ アカウントのキーを再同期します。|
|/webServices/read|Machine Learning Web サービスを読み取ります。|
|/webServices/write|Machine Learning Web サービスを作成または更新します。|
|/webServices/delete|Machine Learning Web サービスを削除します。|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| 操作 | Description |
|---|---|
|/agreements/offers/plans/read|指定された Marketplace の項目の契約を返します。|
|/agreements/offers/plans/sign/action|指定された Marketplace の項目の契約に署名します。|
|/agreements/offers/plans/cancel/action|指定された Marketplace の項目の契約を取り消します。|

## <a name="microsoftmedia"></a>Microsoft.Media

| 操作 | 説明 |
|---|---|
|/mediaservices/read||
|/mediaservices/write||
|/mediaservices/delete||
|/mediaservices/regenerateKey/action||
|/mediaservices/listKeys/action||
|/mediaservices/syncStorageKeys/action||

## <a name="microsoftnetwork"></a>Microsoft.Network

| 操作 | Description |
|---|---|
|/register/action|サブスクリプションを登録します。|
|/unregister/action|サブスクリプションの登録を解除します。|
|/checkTrafficManagerNameAvailability/action|Traffic Manager の相対 DNS 名を使用できるかどうかを確認します。|
|/dnszones/read|DNS ゾーンを JSON 形式で取得します。 ゾーンのプロパティには、タグ、ETag、numberOfRecordSets、maxNumberOfRecordSets があります。 このコマンドでは、ゾーン内のレコード セットは取得されません。|
|/dnszones/write|リソース グループ内の DNS ゾーンを作成または更新します。  DNS ゾーン リソースのタグを更新する際に使用します。 このコマンドを使用して、ゾーン内のレコード セットを作成または更新することはできません。|
|/dnszones/delete|JSON 形式の DNS ゾーンを削除します。 ゾーンのプロパティには、タグ、ETag、numberOfRecordSets、maxNumberOfRecordSets があります。|
|/dnszones/MX/read|"MX" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。|
|/dnszones/MX/write|DNS ゾーン内の "MX" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。|
|/dnszones/MX/delete|指定された名前の "MX" タイプのレコード セットを DNS ゾーンから削除します。|
|/dnszones/NS/read|NS タイプの DNS レコード セットを取得します。|
|/dnszones/NS/write|NS タイプの DNS レコード セットを作成または更新します。|
|/dnszones/NS/delete|NS タイプの DNS レコード セットを削除します。|
|/dnszones/AAAA/read|"AAAA" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。|
|/dnszones/AAAA/write|DNS ゾーン内の "AAAA" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。|
|/dnszones/AAAA/delete|指定された名前の "AAAA" タイプのレコード セットを DNS ゾーンから削除します。|
|/dnszones/CNAME/read|"CNAME" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、TTL、タグ、ETag が含まれます。|
|/dnszones/CNAME/write|DNS ゾーン内の "CNAME" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。|
|/dnszones/CNAME/delete|指定された名前の "CNAME" タイプのレコード セットを DNS ゾーンから削除します。|
|/dnszones/SOA/read|SOA タイプの DNS レコード セットを取得します。|
|/dnszones/SOA/write|SOA タイプの DNS レコード セットを作成または更新します。|
|/dnszones/SRV/read|"SRV" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。|
|/dnszones/SRV/write|SRV タイプのレコード セットを作成または更新します。|
|/dnszones/SRV/delete|指定された名前の "SRV" タイプのレコード セットを DNS ゾーンから削除します。|
|/dnszones/PTR/read|"PTR" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。|
|/dnszones/PTR/write|DNS ゾーン内の "PTR" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。|
|/dnszones/PTR/delete|指定された名前の "PTR" タイプのレコード セットを DNS ゾーンから削除します。|
|/dnszones/A/read|"A" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。|
|/dnszones/A/write|DNS ゾーン内の "A" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。|
|/dnszones/A/delete|指定された名前の "A" タイプのレコード セットを DNS ゾーンから削除します。|
|/dnszones/TXT/read|"TXT" タイプのレコード セットを JSON 形式で取得します。 レコード セットには、レコードの一覧と、TTL、タグ、および ETag が含まれます。|
|/dnszones/TXT/write|DNS ゾーン内の "TXT" タイプのレコード セットを作成または更新します。 レコード セットの現在のレコードが指定されたレコードに置き換えられます。|
|/dnszones/TXT/delete|指定された名前の "TXT" タイプのレコード セットを DNS ゾーンから削除します。|
|/dnszones/recordsets/read|各タイプの DNS レコード セットを取得します。|
|/networkInterfaces/read|ネットワーク インターフェイスの定義を取得します。 |
|/networkInterfaces/write|ネットワーク インターフェイスを作成するか、既存のネットワーク インターフェイスを更新します。 |
|/networkInterfaces/join/action|仮想マシンをネットワーク インターフェイスに接続します。|
|/networkInterfaces/delete|ネットワーク インターフェイスを削除します。|
|/networkInterfaces/effectiveRouteTable/action|VM のネットワーク インターフェイスで構成されているルート テーブルを取得します。|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|VM のネットワーク インターフェイスで構成されているネットワーク セキュリティ グループを取得します。|
|/networkInterfaces/loadBalancers/read|ネットワーク インターフェイスが含まれているすべてのロード バランサーを取得します。|
|/networkInterfaces/ipconfigurations/read|ネットワーク インターフェイスの IP 構成定義を取得します。 |
|/publicIPAddresses/read|パブリック IP アドレス定義を取得します。|
|/publicIPAddresses/write|パブリック IP アドレスを作成するか、既存のパブリック IP アドレスを更新します。 |
|/publicIPAddresses/delete|パブリック IP アドレスを削除します。|
|/publicIPAddresses/join/action|パブリック IP アドレスに接続します。|
|/routeFilters/read|ルート フィルター定義を取得します。|
|/routeFilters/join/action|ルート フィルターを結合します。|
|/routeFilters/delete|ルート フィルター定義を削除します。|
|/routeFilters/write|ルート フィルターを作成するか、既存のルート フィルターを更新します。|
|/routeFilters/rules/read|ルート フィルター規則の定義を取得します。|
|/routeFilters/rules/write|ルート フィルター規則を作成するか、既存のルート フィルター規則を更新します。|
|/routeFilters/rules/delete|ルート フィルター規則の定義を削除します。|
|/networkWatchers/read|Network Watcher の定義を取得します。|
|/networkWatchers/write|Network Watcher を作成するか、既存の Network Watcher を更新します。|
|/networkWatchers/delete|Network Watcher を削除します。|
|/networkWatchers/configureFlowLog/action|ターゲット リソースのフロー ログを構成します。|
|/networkWatchers/ipFlowVerify/action|特定の宛先との間で送受信されるパケットを許可するか拒否するかを返します。|
|/networkWatchers/nextHop/action|指定されたターゲットと宛先 IP アドレスについて、次ホップの種類と次ホップの IP アドレスを返します。|
|/networkWatchers/queryFlowLogStatus/action|リソースのフロー ログの状態を取得します。|
|/networkWatchers/queryTroubleshootResult/action|実行済みまたは現在実行中のトラブルシューティング操作のトラブルシューティングの結果を取得します。|
|/networkWatchers/securityGroupView/action|VM に適用されている構成済みの有効なネットワーク セキュリティ グループ規則を表示します。|
|/networkWatchers/topology/action|リソース グループ内のリソースとその関係のネットワーク レベル ビューを取得します。|
|/networkWatchers/troubleshoot/action|Azure のネットワーク リソースでトラブルシューティングを開始します。|
|/networkWatchers/packetCaptures/queryStatus/action|パケット キャプチャ リソースのプロパティと状態に関する情報を取得します。|
|/networkWatchers/packetCaptures/stop/action|実行中のパケット キャプチャ セッションを停止します。|
|/networkWatchers/packetCaptures/read|パケット キャプチャ定義を取得します。|
|/networkWatchers/packetCaptures/write|パケット キャプチャを作成します。|
|/networkWatchers/packetCaptures/delete|パケット キャプチャを削除します。|
|/loadBalancers/read|ロード バランサー定義を取得します。|
|/loadBalancers/write|ロード バランサーを作成するか、既存のロード バランサーを更新します。|
|/loadBalancers/delete|ロード バランサーを削除します。|
|/loadBalancers/networkInterfaces/read|ロード バランサーの下のすべてのネットワーク インターフェイスへの参照を取得します。|
|/loadBalancers/loadBalancingRules/read|ロード バランサーの負荷分散規則の定義を取得します。|
|/loadBalancers/backendAddressPools/read|ロード バランサーのバックエンド アドレス プールの定義を取得します。|
|/loadBalancers/backendAddressPools/join/action|ロード バランサーのバックエンド アドレス プールを接続します。|
|/loadBalancers/inboundNatPools/read|ロード バランサーの受信 NAT プールの定義を取得します。|
|/loadBalancers/inboundNatPools/join/action|ロード バランサーの受信 NAT プールを接続します。|
|/loadBalancers/inboundNatRules/read|ロード バランサーの受信 NAT 規則の定義を取得します。|
|/loadBalancers/inboundNatRules/write|ロード バランサーの受信 NAT 規則を作成するか、既存の受信 NAT 規則を更新します。|
|/loadBalancers/inboundNatRules/delete|ロード バランサーの受信 NAT 規則を削除します。|
|/loadBalancers/inboundNatRules/join/action|ロード バランサーの受信 NAT 規則を接続します。|
|/loadBalancers/outboundNatRules/read|ロード バランサーの送信 NAT 規則の定義を取得します。|
|/loadBalancers/probes/read|ロード バランサー プローブを取得します。|
|/loadBalancers/virtualMachines/read|ロード バランサーの下のすべての仮想マシンへの参照を取得します。|
|/loadBalancers/frontendIPConfigurations/read|ロード バランサーのフロントエンド IP 構成定義を取得します。|
|/trafficManagerGeographicHierarchies/read|地理的トラフィック ルーティング方法で使用できるリージョンを含む、Traffic Manager の地理的階層を取得します。|
|/bgpServiceCommunities/read|BGP サービス コミュニティを取得します。|
|/applicationGatewayAvailableWafRuleSets/read|アプリケーション ゲートウェイの利用可能な Waf ルール セットを取得します。|
|/virtualNetworks/read|仮想ネットワークの定義を取得します。|
|/virtualNetworks/write|仮想ネットワークを作成するか、既存の仮想ネットワークを更新します。|
|/virtualNetworks/delete|仮想ネットワークを削除します。|
|/virtualNetworks/peer/action|仮想ネットワークを別の仮想ネットワークとピアリングします。|
|/virtualNetworks/virtualNetworkPeerings/read|仮想ネットワーク ピアリングの定義を取得します。|
|/virtualNetworks/virtualNetworkPeerings/write|仮想ネットワーク ピアリングを作成するか、既存の仮想ネットワーク ピアリングを更新します。|
|/virtualNetworks/virtualNetworkPeerings/delete|仮想ネットワーク ピアリングを削除します。|
|/virtualNetworks/subnets/read|仮想ネットワーク サブネットの定義を取得します。|
|/virtualNetworks/subnets/write|仮想ネットワーク サブネットを作成するか、既存の仮想ネットワーク サブネットを更新します。|
|/virtualNetworks/subnets/delete|仮想ネットワーク サブネットを削除します。|
|/virtualNetworks/subnets/join/action|仮想ネットワークに参加します。|
|/virtualNetworks/subnets/joinViaServiceTunnel/action|ストレージ アカウントや SQL Database などのリソースを、サービス トンネリングが有効なサブネットに参加させます。|
|/virtualNetworks/subnets/virtualMachines/read|仮想ネットワーク サブネットのすべての仮想マシンへの参照を取得します。|
|/virtualNetworks/checkIpAddressAvailability/read|指定された仮想ネットワークで IP アドレスを使用できるかどうかを確認します。|
|/virtualNetworks/virtualMachines/read|仮想ネットワークのすべての仮想マシンへの参照を取得します。|
|/expressRouteServiceProviders/read|Express Route サービス プロバイダーを取得します。|
|/dnsoperationresults/read|DNS 操作の結果を取得します。|
|/localnetworkgateways/read|LocalNetworkGateway を取得します。|
|/localnetworkgateways/write|LocalNetworkGateway を作成するか、既存の LocalNetworkGateway を更新します。|
|/localnetworkgateways/delete|LocalNetworkGateway を削除します。|
|/trafficManagerProfiles/read|Traffic Manager プロファイル構成を取得します。 これには、DNS 設定、トラフィック ルーティング設定、エンドポイント監視設定、この Traffic Manager プロファイルによってルーティングされるエンドポイントの一覧が含まれます。|
|/trafficManagerProfiles/write|Traffic Manager プロファイルを作成するか、既存の Traffic Manager プロファイルの構成を変更します。 これには、プロファイルの有効化または無効化と、DNS 設定、トラフィック ルーティング設定、またはエンドポイント監視設定の変更が含まれます。 Traffic Manager プロファイルによってルーティングされるエンドポイントを追加、削除、有効化、または無効化できます。|
|/trafficManagerProfiles/delete|Traffic Manager プロファイルを削除します。 Traffic Manager プロファイルに関連付けられているすべての設定が失われ、プロファイルを使用してトラフィックをルーティングすることはできなくなります。|
|/dnsoperationstatuses/read|DNS 操作の状態を取得します。 |
|/operations/read|使用可能な操作を取得します。|
|/expressRouteCircuits/read|ExpressRouteCircuit を取得します。|
|/expressRouteCircuits/write|ExpressRouteCircuit を作成するか、既存の ExpressRouteCircuit を更新します。|
|/expressRouteCircuits/delete|ExpressRouteCircuit を削除します。|
|/expressRouteCircuits/stats/read|ExpressRouteCircuit の統計を取得します。|
|/expressRouteCircuits/peerings/read|ExpressRouteCircuit のピアリングを取得します。|
|/expressRouteCircuits/peerings/write|ExpressRouteCircuit のピアリングを作成するか、既存のピアリングを更新します。|
|/expressRouteCircuits/peerings/delete|ExpressRouteCircuit のピアリングを削除します。|
|/expressRouteCircuits/peerings/arpTables/action|ExpressRouteCircuit のピアリングの ArpTable を取得します。|
|/expressRouteCircuits/peerings/routeTables/action|ExpressRouteCircuit のピアリングの RouteTable を取得します。|
|/expressRouteCircuits/peerings/<br>routeTablesSummary/action|ExpressRouteCircuit のピアリングの RouteTable の概要を取得します。|
|/expressRouteCircuits/peerings/stats/read|ExpressRouteCircuit のピアリングの統計を取得します。|
|/expressRouteCircuits/authorizations/read|ExpressRouteCircuit の承認を取得します。|
|/expressRouteCircuits/authorizations/write|ExpressRouteCircuit の承認を作成するか、既存の承認を更新します。|
|/expressRouteCircuits/authorizations/delete|ExpressRouteCircuit の承認を削除します。|
|/connections/read|VirtualNetworkGatewayConnection を取得します。|
|/connections/write|VirtualNetworkGatewayConnection を作成するか、既存の VirtualNetworkGatewayConnection を更新します。|
|/connections/delete|VirtualNetworkGatewayConnection を削除します。|
|/connections/sharedKey/read|VirtualNetworkGatewayConnection の SharedKey を取得します。|
|/connections/sharedKey/write|VirtualNetworkGatewayConnection の SharedKey を作成するか、既存の SharedKey を更新します。|
|/networkSecurityGroups/read|ネットワーク セキュリティ グループの定義を取得します。|
|/networkSecurityGroups/write|ネットワーク セキュリティ グループを作成するか、既存のネットワーク セキュリティ グループを更新します。|
|/networkSecurityGroups/delete|ネットワーク セキュリティ グループを削除します。|
|/networkSecurityGroups/join/action|ネットワーク セキュリティ グループに参加します。|
|/networkSecurityGroups/defaultSecurityRules/read|既定のセキュリティ規則の定義を取得します。|
|/networkSecurityGroups/securityRules/read|セキュリティ規則の定義を取得します。|
|/networkSecurityGroups/securityRules/write|セキュリティ規則を作成するか、既存のセキュリティ規則を更新します。|
|/networkSecurityGroups/securityRules/delete|セキュリティ規則を削除します。|
|/applicationGateways/read|アプリケーション ゲートウェイを取得します。|
|/applicationGateways/write|アプリケーション ゲートウェイを作成するか、アプリケーション ゲートウェイを更新します。|
|/applicationGateways/delete|アプリケーション ゲートウェイを削除します。|
|/applicationGateways/backendhealth/action|アプリケーション ゲートウェイのバックエンドの正常性を取得します。|
|/applicationGateways/start/action|アプリケーション ゲートウェイを起動します。|
|/applicationGateways/stop/action|アプリケーション ゲートウェイを停止します。|
|/applicationGateways/backendAddressPools/join/action|アプリケーション ゲートウェイのバックエンド アドレス プールを接続します。|
|/routeTables/read|ルート テーブルの定義を取得します。|
|/routeTables/write|ルート テーブルを作成するか、既存のルート テーブルを更新します。|
|/routeTables/delete|ルート テーブルの定義を削除します。|
|/routeTables/join/action|ルート テーブルを結合します。|
|/routeTables/routes/read|ルート定義を取得します。|
|/routeTables/routes/write|ルートを作成するか、既存のルートを更新します。|
|/routeTables/routes/delete|ルート定義を削除します。|
|/locations/operationResults/read|非同期の POST 操作または DELETE 操作の結果を取得します。|
|/locations/checkDnsNameAvailability/read|指定された場所で DNS ラベルを使用できるかどうかを確認します。|
|/locations/usages/read|リソースの使用状況メトリックを取得します。|
|/locations/operations/read|非同期操作の状態を表す操作リソースを取得します。|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| 操作 | Description |
|---|---|
|/register/action|NotifciationHubs リソース プロバイダーにサブスクリプションを登録し、名前空間と NotificationHubs を作成できるようにします。|
|/CheckNamespaceAvailability/action|指定された名前空間リソース名を NotificationHub サービス内で使用できるかどうかを確認します。|
|/Namespaces/write|名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグと状態は、更新できるプロパティです。|
|/Namespaces/read|名前空間リソースの説明の一覧を取得します。|
|/Namespaces/Delete|名前空間リソースを削除します。|
|/Namespaces/authorizationRules/action|名前空間の承認規則の説明の一覧を取得します。|
|/Namespaces/CheckNotificationHubAvailability/action|指定された NotificationHub 名を名前空間内で使用できるかどうかを確認します。|
|/Namespaces/authorizationRules/write|名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。|
|/Namespaces/authorizationRules/read|名前空間の承認規則の説明の一覧を取得します。|
|/Namespaces/authorizationRules/delete|名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。 |
|/Namespaces/authorizationRules/listkeys/action|名前空間への接続文字列を取得します。|
|/Namespaces/authorizationRules/regenerateKeys/action|名前空間の承認規則では、Primary/SecondaryKey を再生成します。再生成する必要があるキーを指定します。|
|/Namespaces/NotificationHubs/write|Notification Hub を作成し、そのプロパティを更新します。 プロパティには、主に、PNS 資格情報、 承認規則、TTL が含まれます。|
|/Namespaces/NotificationHubs/read|Notification Hub リソースの説明の一覧を取得します。|
|/Namespaces/NotificationHubs/Delete|Notification Hub リソースを削除します。|
|/Namespaces/NotificationHubs/authorizationRules/action|Notification Hub の承認規則の一覧を取得します。|
|/Namespaces/NotificationHubs/pnsCredentials/action|Notification Hub の PNS の資格情報をすべて取得します。 これには、WNS、MPNS、APNS、GCM、Baidu の各資格情報が含まれます。|
|/Namespaces/NotificationHubs/debugSend/action|テスト プッシュ通知を送信します。|
|/Namespaces/NotificationHubs/metricDefinitions/read|名前空間のメトリック リソースの説明の一覧を取得します。|
|/Namespaces/NotificationHubs/<br>authorizationRules/write|Notification Hub の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。|
|/Namespaces/NotificationHubs/<br>authorizationRules/read|Notification Hub の承認規則の一覧を取得します。|
|/Namespaces/NotificationHubs/<br>authorizationRules/delete|Notification Hub の承認規則を削除します。|
|/Namespaces/NotificationHubs/<br>authorizationRules/listkeys/action|Notification Hub への接続文字列を取得します。|
|/Namespaces/NotificationHubs/<br>authorizationRules/regenerateKeys/action|Notification Hub の承認規則では、Primary/SecondaryKey を再生成します。再生成する必要があるキーを指定します。|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| 操作 | Description |
|---|---|
|/register/action|リソース プロバイダーにサブスクリプションを登録します。|
|/linkTargets/read|Azure サブスクリプションに関連付けられていない既存のアカウントを一覧表示します。 この Azure サブスクリプションをワークスペースにリンクするには、この操作で返される顧客 ID を、"ワークスペースの作成" 操作の顧客 ID プロパティで使用します。|
|/workspaces/write|新しいワークスペースを作成するか、既存のワークスペースの顧客 ID を指定して既存のワークスペースにリンクします。|
|/workspaces/read|既存のワークスペースを取得します。|
|/workspaces/delete|ワークスペースを削除します。 ワークスペースが、作成時に既存のワークスペースにリンクされていた場合、リンク先のワークスペースは削除されません。|
|/workspaces/generateregistrationcertificate/action|ワークスペースの登録証明書を生成します。 この証明書を使用して、Microsoft System Center Operation Manager をワークスペースに接続します。|
|/workspaces/sharedKeys/action|ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。|
|/workspaces/search/action|検索クエリを実行します。|
|/workspaces/datasources/read|ワークスペースのデータソースを取得します。|
|/workspaces/datasources/write|ワークスペースのデータソースを作成または更新します。|
|/workspaces/datasources/delete|ワークスペースのデータソースを削除します。|
|/workspaces/managementGroups/read|このワークスペースに接続されている System Center Operations Manager 管理グループの名前とメタデータを取得します。|
|/workspaces/schema/read|ワークスペースの検索スキーマを取得します。  検索スキーマには、公開されているフィールドとその種類が含まれます。|
|/workspaces/usages/read|ワークスペースで読み取られたデータの量など、ワークスペースの使用状況データを取得します。|
|/workspaces/intelligencepacks/read|指定されたワークスペースで表示されるすべてのインテリジェンス パックを一覧表示します。また、そのワークスペースでパックが有効になっているか無効になっているかも示します。|
|/workspaces/intelligencepacks/enable/action|指定されたワークスペースのインテリジェンス パックを有効にします。|
|/workspaces/intelligencepacks/disable/action|指定されたワークスペースのインテリジェンス パックを無効にします。|
|/workspaces/sharedKeys/read|ワークスペースの共有キーを取得します。 これらのキーを使用して、Microsoft Operational Insights エージェントをワークスペースに接続します。|
|/workspaces/savedSearches/read|保存された検索クエリを取得します。|
|/workspaces/savedSearches/write|保存された検索クエリを作成します。|
|/workspaces/savedSearches/delete|保存された検索クエリを削除します。|
|/workspaces/storageinsightconfigs/write|新しいストレージ構成を作成します。 これらの構成を使用して、既存のストレージ アカウント内の場所からデータを取得します。|
|/workspaces/storageinsightconfigs/read|ストレージ構成を取得します。|
|/workspaces/storageinsightconfigs/delete|ストレージ構成を削除します。 これにより、Microsoft Operational Insights はストレージ アカウントからデータを読み取らなくなります。|
|/workspaces/configurationScopes/read|構成スコープを取得します。|
|/workspaces/configurationScopes/write|構成スコープを設定します。|
|/workspaces/configurationScopes/delete|構成スコープを削除します。|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| 操作 | Description |
|---|---|
|/register/action|リソース プロバイダーにサブスクリプションを登録します。|
|/solutions/write|新しい OMS ソリューションを作成します。|
|/solutions/read|既存の OMS ソリューションを取得します。|
|/solutions/delete|既存の OMS ソリューションを削除します。|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| 操作 | Description |
|---|---|
|/Vaults/backupJobsExport/action|ジョブをエクスポートします。|
|/Vaults/write|"コンテナーの作成" 操作では、"コンテナー" 型の Azure リソースを作成します。|
|/Vaults/read|"コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。|
|/Vaults/delete|"コンテナーの削除" 操作では、指定された "コンテナー" 型の Azure リソースを削除します。|
|/Vaults/refreshContainers/read|コンテナーの一覧を更新します。|
|/Vaults/backupJobsExport/operationResults/read|"ジョブのエクスポート" 操作の結果を返します。|
|/Vaults/backupOperationResults/read|Recovery Services コンテナーに対するバックアップ操作の結果を返します。|
|/Vaults/monitoringAlerts/read|Recovery Services コンテナーのアラートを取得します。|
|/Vaults/monitoringAlerts/{uniqueAlertId}/read|アラートの詳細を取得します。|
|/Vaults/backupSecurityPIN/read|Recovery Services コンテナーのセキュリティ PIN 情報を返します。|
|/vaults/replicationEvents/read|イベントを読み取ります。|
|/Vaults/backupProtectableItems/read|すべての保護可能な項目の一覧を返します。|
|/vaults/replicationFabrics/read|ファブリックを読み取ります。|
|/vaults/replicationFabrics/write|ファブリックを作成または更新します。|
|/vaults/replicationFabrics/remove/action|ファブリックを削除します。|
|/vaults/replicationFabrics/checkConsistency/action|ファブリックの一貫性を確認します。|
|/vaults/replicationFabrics/delete|ファブリックをすべて削除します。|
|/vaults/replicationFabrics/renewcertificate/action||
|/vaults/replicationFabrics/deployProcessServerImage/action|プロセス サーバー イメージをデプロイします。|
|/vaults/replicationFabrics/reassociateGateway/action|ゲートウェイを再関連付けします。|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>read|Recovery Services プロバイダーを読み取ります。|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>remove/action|Recovery Services プロバイダーを削除します。|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>削除|Recovery Services プロバイダーをすべて削除します。|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>refreshProvider/action|プロバイダーを更新します。|
|/vaults/replicationFabrics/replicationStorageClassifications/read|ストレージの分類を読み取ります。|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/read|ストレージ分類マッピングを読み取ります。|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/write|ストレージ分類マッピングを作成または更新します。|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/delete|ストレージ分類マッピングをすべて削除します。|
|/vaults/replicationFabrics/replicationvCenters/read|ジョブを読み取ります。|
|/vaults/replicationFabrics/replicationvCenters/write|ジョブを作成または更新します。|
|/vaults/replicationFabrics/replicationvCenters/delete|ジョブをすべて削除します。|
|/vaults/replicationFabrics/replicationNetworks/read|ネットワークを読み取ります。|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/read|ネットワーク マッピングを読み取ります。|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/write|ネットワーク マッピングを作成または更新します。|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/delete|ネットワーク マッピングをすべて削除します。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>read|保護コンテナーを読み取ります。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>discoverProtectableItem/action|保護可能な項目を検出します。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>write|保護コンテナーを作成または更新します。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>remove/action|保護コンテナーを削除します。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>switchprotection/action|保護コンテナーを切り替えます。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectableItems/read|保護可能な項目を読み取ります。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/read|保護コンテナー マッピングを読み取ります。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/write|保護コンテナー マッピングを作成または更新します。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/remove/action|保護コンテナー マッピングを削除します。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/delete|保護コンテナー マッピングをすべて削除します。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/read|保護された項目を読み取ります。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/write|保護された項目を作成または更新します。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/delete|保護された項目をすべて削除します。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/remove/action|保護された項目を削除します。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/plannedFailover/action|計画されたフェールオーバー。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/unplannedFailover/action|フェールオーバー|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailover/action|テスト フェールオーバー|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailoverCleanup/action|テスト フェールオーバーのクリーンアップ。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/failoverCommit/action|フェールオーバーのコミット。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/reProtect/action|保護された項目を再保護します。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/updateMobilityService/action|モビリティ サービスを更新します。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/repairReplication/action|レプリケーションを修復します。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/applyRecoveryPoint/action|復旧ポイントを適用します。|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/recoveryPoints/read|レプリケーションの復旧ポイントを読み取ります。|
|/vaults/replicationPolicies/read|ポリシーを読み取ります。|
|/vaults/replicationPolicies/write|ポリシーを作成または更新します。|
|/vaults/replicationPolicies/delete|ポリシーをすべて削除します。|
|/vaults/replicationRecoveryPlans/read|復旧計画を読み取ります。|
|/vaults/replicationRecoveryPlans/write|復旧計画を作成または更新します。|
|/vaults/replicationRecoveryPlans/delete|復旧計画をすべて削除します。|
|/vaults/replicationRecoveryPlans/plannedFailover/action|計画されたフェールオーバーの復旧計画。|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|フェールオーバーの復旧計画。|
|/vaults/replicationRecoveryPlans/testFailover/action|テスト フェールオーバーの復旧計画。|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|テスト フェールオーバーのクリーンアップの復旧計画。|
|/vaults/replicationRecoveryPlans/failoverCommit/action|フェールオーバーのコミットの復旧計画。|
|/vaults/replicationRecoveryPlans/reProtect/action|再保護の復旧計画。|
|/Vaults/extendedInformation/read|"拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。|
|/Vaults/extendedInformation/write|"拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。|
|/Vaults/extendedInformation/delete|"拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。|
|/Vaults/backupManagementMetaData/read|Recovery Services コンテナーのバックアップ管理メタデータを返します。|
|/Vaults/backupProtectionContainers/read|サブスクリプションに属するすべてのコンテナーを返します。|
|/Vaults/backupFabrics/operationResults/read|操作の状態を返します。|
|/Vaults/backupFabrics/protectionContainers/read|すべての登録済みコンテナーを返します。|
|/Vaults/backupFabrics/protectionContainers/<br>operationResults/read|保護コンテナーに対して実行された操作の結果を取得します。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/read|保護された項目のオブジェクトの詳細を返します。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/write|バックアップ保護項目を作成します。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/delete|保護された項目を削除します。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/backup/action|保護された項目のバックアップを実行します。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationResults/read|保護された項目に対して実行された操作の結果を取得します。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationStatus/read|保護された項目に対して実行された操作の状態を返します。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/read|保護された項目の復旧ポイントを取得します。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>restore/action|保護された項目の復旧ポイントを復元します。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>provisionInstantItemRecovery/action|保護された項目のインスタント項目回復をプロビジョニングします。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>revokeInstantItemRecovery/action|保護された項目のインスタント項目回復を取り消します。|
|/Vaults/usages/read|Recovery Services コンテナーの使用状況の詳細を返します。|
|/vaults/usages/read|コンテナーの使用状況を読み取ります。|
|/Vaults/certificates/write|"リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。|
|/Vaults/tokenInfo/read|Recovery Services コンテナーのトークン情報を返します。|
|/vaults/replicationAlertSettings/read|アラート設定を読み取ります。|
|/vaults/replicationAlertSettings/write|アラート設定を作成または更新します。|
|/Vaults/backupOperations/read|Recovery Services コンテナーに対するバックアップ操作の状態を返します。|
|/Vaults/storageConfig/read|Recovery Services コンテナーのストレージ構成を返します。|
|/Vaults/storageConfig/write|Recovery Services コンテナーのストレージ構成を更新します。|
|/Vaults/backupUsageSummaries/read|Recovery Services の保護された項目と保護されたサーバーの概要を返します。|
|/Vaults/backupProtectedItems/read|すべての保護された項目の一覧を返します。|
|/Vaults/backupconfig/vaultconfig/read|Recovery Services コンテナーの構成を返します。|
|/Vaults/backupconfig/vaultconfig/write|Recovery Services コンテナーの構成を更新します。|
|/Vaults/registeredIdentities/write|"サービス コンテナーを登録" 操作を使用すると、コンテナーを Recovery Services に登録できます。|
|/Vaults/registeredIdentities/read|" コンテナーを取得" 操作を使用すると、リソースの登録済みコンテナーを取得できます。|
|/Vaults/registeredIdentities/delete|"コンテナーの登録を解除" 操作を使用すると、コンテナーの登録を解除できます。|
|/Vaults/registeredIdentities/operationResults/read|"操作結果を取得" 操作を使用すると、非同期で送信された操作の状態と結果を取得できます。|
|/vaults/replicationJobs/read|ジョブを読み取ります。|
|/vaults/replicationJobs/cancel/action|ジョブを取り消します。|
|/vaults/replicationJobs/restart/action|ジョブを再実行します。|
|/vaults/replicationJobs/resume/action|ジョブを再開します。|
|/Vaults/backupPolicies/read|すべての保護ポリシーを返します。|
|/Vaults/backupPolicies/write|保護ポリシーを作成します。|
|/Vaults/backupPolicies/delete|保護ポリシーを削除します。|
|/Vaults/backupPolicies/operationResults/read|ポリシー操作の結果を取得します。|
|/Vaults/backupPolicies/operationStatus/read|ポリシー操作の状態を取得します。|
|/Vaults/vaultTokens/read|"コンテナー トークン" 操作を使用すると、コンテナー レベルのバックエンド操作のコンテナー トークンを取得できます。|
|/Vaults/monitoringConfigurations/notificationConfiguration/read|Recovery Services コンテナーの通知構成を取得します。|
|/Vaults/backupJobs/read|すべてのジョブ オブジェクトを返します。|
|/Vaults/backupJobs/cancel/action|ジョブを取り消します。|
|/Vaults/backupJobs/operationResults/read|ジョブ操作の結果を返します。|
|/locations/allocateStamp/action|AllocateStamp は、サービスによって使用される内部操作です。|
|/locations/allocatedStamp/read|GetAllocatedStamp は、サービスによって使用される内部操作です。|

## <a name="microsoftrelay"></a>Microsoft.Relay

| 操作 | Description |
|---|---|
|/checkNamespaceAvailability/action|特定のサブスクリプションで名前空間を使用できるかどうかを確認します。|
|/register/action|Relay リソース プロバイダーにサブスクリプションを登録し、リレー リソースを作成できるようにします。|
|/namespaces/write|名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグと状態は、更新できるプロパティです。|
|/namespaces/read|名前空間リソースの説明の一覧を取得します。|
|/namespaces/Delete|名前空間リソースを削除します。|
|/namespaces/authorizationRules/write|名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。|
|/namespaces/authorizationRules/delete|名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。 |
|/namespaces/authorizationRules/listkeys/action|名前空間への接続文字列を取得します。|
|/namespaces/HybridConnections/write|HybridConnection プロパティを作成または更新します。|
|/namespaces/HybridConnections/read|HybridConnection リソースの説明の一覧を取得します。|
|/namespaces/HybridConnections/Delete|HybridConnection リソースを削除する操作。|
|/namespaces/HybridConnections/authorizationRules/write|HybridConnection の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。|
|/namespaces/HybridConnections/authorizationRules/delete|HybridConnection の承認規則を削除する操作。|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|HybridConnection への接続文字列を取得します。|
|/namespaces/WcfRelays/write|WcfRelay のプロパティを作成または更新します。|
|/namespaces/WcfRelays/read|WcfRelay リソースの説明の一覧を取得します。|
|/namespaces/WcfRelays/Delete|WcfRelay リソースを削除する操作。|
|/namespaces/WcfRelays/authorizationRules/write|WcfRelay の承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。|
|/namespaces/WcfRelays/authorizationRules/delete|WcfRelay の承認規則を削除する操作。|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|WcfRelay への接続文字列を取得します。|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| 操作 | 説明 |
|---|---|
|/AvailabilityStatuses/read|指定されたスコープのすべてのリソースの利用状況を取得します。|
|/AvailabilityStatuses/current/read|指定されたリソースの利用状況を取得します。|

## <a name="microsoftresources"></a>Microsoft.Resources

| 操作 | Description |
|---|---|
|/checkResourceName/action|リソース名の妥当性を確認します。|
|/providers/read|プロバイダーの一覧を取得します。|
|/subscriptions/read|サブスクリプションの一覧を取得します。|
|/subscriptions/operationresults/read|サブスクリプション操作の結果を取得します。|
|/subscriptions/providers/read|リソース プロバイダーを取得または一覧表示します。|
|/subscriptions/tagNames/read|サブスクリプション タグを取得または一覧表示します。|
|/subscriptions/tagNames/write|サブスクリプション タグを追加します。|
|/subscriptions/tagNames/delete|サブスクリプション タグを削除します。|
|/subscriptions/tagNames/tagValues/read|サブスクリプション タグの値を取得または一覧表示します。|
|/subscriptions/tagNames/tagValues/write|サブスクリプション タグの値を追加します。|
|/subscriptions/tagNames/tagValues/delete|サブスクリプション タグの値を削除します。|
|/subscriptions/resources/read|サブスクリプションのリソースを取得します。|
|/subscriptions/resourceGroups/read|リソース グループを取得または一覧表示します。|
|/subscriptions/resourceGroups/write|リソース グループを作成または更新します。|
|/subscriptions/resourceGroups/delete|リソース グループとそのすべてのリソースを削除します。|
|/subscriptions/resourceGroups/moveResources/action|リソース グループ間でリソースを移動します。|
|/subscriptions/resourceGroups/validateMoveResources/action|リソース グループ間でのリソースの移動を検証します。|
|/subscriptions/resourcegroups/resources/read|リソース グループのリソースを取得します。|
|/subscriptions/resourcegroups/deployments/read|デプロイを取得または一覧表示します。|
|/subscriptions/resourcegroups/deployments/write|デプロイを作成または更新します。|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|デプロイ操作の状態を取得または一覧表示します。|
|/subscriptions/resourcegroups/deployments/operations/read|デプロイ操作を取得または一覧表示します。|
|/subscriptions/locations/read|サポートされる場所の一覧を取得します。|
|/links/read|リソース リンクを取得または一覧表示します。|
|/links/write|リソース リンクを作成または更新します。|
|/links/delete|リソース リンクを削除します。|
|/tenants/read|テナントの一覧を取得します。|
|/resources/read|フィルターに基づいてリソースの一覧を取得します。|
|/deployments/read|デプロイを取得または一覧表示します。|
|/deployments/write|デプロイを作成または更新します。|
|/deployments/delete|デプロイを削除します。|
|/deployments/cancel/action|デプロイを取り消します。|
|/deployments/validate/action|デプロイを検証します。|
|/deployments/operations/read|デプロイ操作を取得または一覧表示します。|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| 操作 | Description |
|---|---|
|/jobcollections/read|ジョブ コレクションを取得します。|
|/jobcollections/write|ジョブ コレクションを作成または更新します。|
|/jobcollections/delete|ジョブ コレクションを削除します。|
|/jobcollections/enable/action|ジョブ コレクションを有効にします。|
|/jobcollections/disable/action|ジョブ コレクションを無効にします。|
|/jobcollections/jobs/read|ジョブを取得します。|
|/jobcollections/jobs/write|ジョブを作成または更新します。|
|/jobcollections/jobs/delete|ジョブを削除します。|
|/jobcollections/jobs/run/action|ジョブを実行します。|
|/jobcollections/jobs/generateLogicAppDefinition/action|Scheduler ジョブに基づいてロジック アプリ定義を生成します。|
|/jobcollections/jobs/jobhistories/read|ジョブ履歴を取得します。|

## <a name="microsoftsearch"></a>Microsoft.Search

| 操作 | Description |
|---|---|
|/register/action|検索リソース プロバイダーにサブスクリプションを登録し、検索サービスを作成できるようにします。|
|/checkNameAvailability/action|サービス名を使用できるかどうかを確認します。|
|/searchServices/write|検索サービスを作成または更新します。|
|/searchServices/read|検索サービスを読み取ります。|
|/searchServices/delete|検索サービスを削除します。|
|/searchServices/start/action|検索サービスを開始します。|
|/searchServices/stop/action|検索サービスを停止します。|
|/searchServices/listAdminKeys/action|管理者キーを読み取ります。|
|/searchServices/regenerateAdminKey/action|管理者キーを再生成します。|
|/searchServices/createQueryKey/action|クエリ キーを作成します。|
|/searchServices/queryKey/read|クエリ キーを読み取ります。|
|/searchServices/queryKey/delete|クエリ キーを削除します。|

## <a name="microsoftsecurity"></a>Microsoft.Security

| 操作 | Description |
|---|---|
|/jitNetworkAccessPolicies/read|Just-In-Time ネットワーク アクセス ポリシーを取得します。|
|/jitNetworkAccessPolicies/write|新しい Just-In-Time ネットワーク アクセス ポリシーを作成するか、既存のポリシーを更新します。|
|/jitNetworkAccessPolicies/initiate/action|Just-In-Time ネットワーク アクセス ポリシーを開始します。|
|/securitySolutionsReferenceData/read|セキュリティ ソリューションの参照データを取得します。|
|/securityStatuses/read|Azure リソースのセキュリティ正常性状態を取得します。|
|/webApplicationFirewalls/read|Web アプリケーション ファイアウォールを取得します。|
|/webApplicationFirewalls/write|新しい Web アプリケーション ファイアウォールを作成するか、既存のWeb アプリケーション ファイアウォールを更新します。|
|/webApplicationFirewalls/delete|Web アプリケーション ファイアウォールを削除します。|
|/securitySolutions/read|セキュリティ ソリューションを取得します。|
|/securitySolutions/write|新しいセキュリティ ソリューションを作成するか、既存のセキュリティ ソリューションを更新します。|
|/securitySolutions/delete|セキュリティ ソリューションを削除します。|
|/tasks/read|利用可能なすべてのセキュリティ推奨事項を取得します。|
|/tasks/dismiss/action|セキュリティ推奨事項を無視します。|
|/tasks/activate/action|セキュリティ推奨事項をアクティブにします。|
|/policies/read|セキュリティ ポリシーを取得します。|
|/policies/write|セキュリティ ポリシーを更新します。|
|/applicationWhitelistings/read|アプリケーション ホワイトリストを取得します。|
|/applicationWhitelistings/write|新しいアプリケーション ホワイトリストを作成するか、既存のアプリケーション ホワイトリストを更新します。|

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

| 操作 | 説明 |
|---|---|
|/subscriptions/write|サブスクリプションを作成または更新します。|
|/gateways/write|ゲートウェイを作成または更新します。|
|/gateways/delete|ゲートウェイを削除します。|
|/gateways/read|ゲートウェイを取得します。|
|/gateways/regenerateprofile/action|ゲートウェイ プロファイルを再生成します。|
|/gateways/upgradetolatest/action|ゲートウェイを最新バージョンにアップグレードします。|
|/nodes/write|ノードを作成または更新します。|
|/nodes/delete|ノードを削除します。|
|/nodes/read|ノードを取得します。|
|/sessions/write|セッションを作成または更新します。|
|/sessions/read|セッションを取得します。|
|/sessions/delete|セッションを削除します。|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| 操作 | 説明 |
|---|---|
|/checkNameAvailability/action|特定のサブスクリプションで名前空間を使用できるかどうかを確認します。|
|/register/action|ServiceBus リソース プロバイダーにサブスクリプションを登録し、ServiceBus リソースを作成できるようにします。|
|/namespaces/write|名前空間リソースを作成し、そのプロパティを更新します。 名前空間のタグと状態は、更新できるプロパティです。|
|/namespaces/read|名前空間リソースの説明の一覧を取得します。|
|/namespaces/Delete|名前空間リソースを削除します。|
|/namespaces/metricDefinitions/read|名前空間のメトリック リソースの説明の一覧を取得します。|
|/namespaces/authorizationRules/write|名前空間レベルの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。|
|/namespaces/authorizationRules/read|名前空間の承認規則の説明の一覧を取得します。|
|/namespaces/authorizationRules/delete|名前空間の承認規則を削除します。 名前空間の既定の承認規則は削除できません。 |
|/namespaces/authorizationRules/listkeys/action|名前空間への接続文字列を取得します。|
|/namespaces/authorizationRules/regenerateKeys/action|リソースのプライマリ キーまたはセカンダリ キーを再生成します。|
|/namespaces/diagnosticSettings/read|名前空間の診断設定リソースの説明の一覧を取得します。|
|/namespaces/diagnosticSettings/write|名前空間の診断設定リソースの説明の一覧を取得します。|
|/namespaces/queues/write|キューのプロパティを作成または更新します。|
|/namespaces/queues/read|キュー リソースの説明の一覧を取得します。|
|/namespaces/queues/Delete|キュー リソースを削除する操作。|
|/namespaces/queues/authorizationRules/write|キューの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。|
|/namespaces/queues/authorizationRules/read| キューの承認規則の一覧を取得します。|
|/namespaces/queues/authorizationRules/delete|キューの承認規則を削除する操作。|
|/namespaces/queues/authorizationRules/listkeys/action|キューへの接続文字列を取得します。|
|/namespaces/queues/authorizationRules/regenerateKeys/action|リソースのプライマリ キーまたはセカンダリ キーを再生成します。|
|/namespaces/logDefinitions/read|名前空間のログ リソースの説明の一覧を取得します。|
|/namespaces/topics/write|トピックのプロパティを作成または更新します。|
|/namespaces/topics/read|トピック リソースの説明の一覧を取得します。|
|/namespaces/topics/Delete|トピック リソースを削除する操作。|
|/namespaces/topics/authorizationRules/write|トピックの承認規則を作成し、そのプロパティを更新します。 承認規則のアクセス権、プライマリ キー、セカンダリ キーを更新できます。|
|/namespaces/topics/authorizationRules/read| トピックの承認規則の一覧を取得します。|
|/namespaces/topics/authorizationRules/delete|トピックの承認規則を削除する操作。|
|/namespaces/topics/authorizationRules/listkeys/action|トピックへの接続文字列を取得します。|
|/namespaces/topics/authorizationRules/regenerateKeys/action|リソースのプライマリ キーまたはセカンダリ キーを再生成します。|
|/namespaces/topics/subscriptions/write|TopicSubscription のプロパティを作成または更新します。|
|/namespaces/topics/subscriptions/read|TopicSubscription リソースの説明の一覧を取得します。|
|/namespaces/topics/subscriptions/Delete|TopicSubscription リソースを削除する操作。|
|/namespaces/topics/subscriptions/rules/write|規則のプロパティを作成または更新します。|
|/namespaces/topics/subscriptions/rules/read|規則リソースの説明の一覧を取得します。|
|/namespaces/topics/subscriptions/rules/Delete|規則リソースを削除する操作。|

## <a name="microsoftsql"></a>Microsoft.Sql

| 操作 | Description |
|---|---|
|/servers/read|サブスクリプションのリソース グループ内のサーバーの一覧を返します。|
|/servers/write|サブスクリプションのリソース グループに新しいサーバーを作成するか、リソース グループ内の既存のサーバーのプロパティを変更します。|
|/servers/delete|サーバーおよびすべての包含データベースとエラスティック プールを削除します。|
|/servers/import/action|サーバーに新しいデータベースを作成するか、DacPac パッケージからスキーマとデータをデプロイします。|
|/servers/upgrade/action|最新バージョンのサーバーで使用できる新しい機能を有効にし、データベース エディション変換マップを指定します。|
|/servers/VulnerabilityAssessmentScans/action|サーバーの脆弱性評価スキャンを実行します。|
|/servers/operationResults/read|この操作を使用して、古いバージョンから新しいバージョンへのサーバーのアップグレードの進行状況を追跡します。|
|/servers/operationResults/delete|サーバー バージョンの進行中のアップグレードを中止します。|
|/servers/securityAlertPolicies/read|指定されたサーバーで構成されているサーバーの脅威検出ポリシーの詳細を取得します。|
|/servers/securityAlertPolicies/write|指定されたサーバーの脅威検出を変更します。|
|/servers/securityAlertPolicies/operationResults/read|サーバーの脅威検出ポリシーの設定操作の結果を取得します。|
|/servers/administrators/read|サーバー管理者の詳細を取得します。|
|/servers/administrators/write|サーバー管理者を作成または更新します。|
|/servers/administrators/delete|サーバーからサーバー管理者を削除します。|
|/servers/recoverableDatabases/read|この操作は、最新の既知の良好なバックアップ ポイントにデータベースを復元するために、ライブ データベースのディザスター リカバリーに使用します。 最新の良好なバックアップに関する情報が返されますが、データベースを実際に復元するわけではありません。|
|/servers/serviceObjectives/read|指定されたサーバーで利用可能なサービス レベルの目標 (パフォーマンス レベルとも呼ばれます) の一覧を取得します。|
|/servers/firewallRules/read|サーバー ファイアウォール規則の詳細を取得します。|
|/servers/firewallRules/write|サーバーへの接続に使用できる IP アドレスの範囲を制御するサーバー ファイアウォール規則を作成または更新します。|
|/servers/firewallRules/delete|サーバーからファイアウォール規則を削除します。|
|/servers/administratorOperationResults/read|サーバー管理者の操作の結果を取得します。|
|/servers/recommendedElasticPools/read|リソース使用率の履歴に基づいて、コストを削減したり、パフォーマンスを向上させたりするために、エラスティック データベース プールの推奨事項を取得します。|
|/servers/recommendedElasticPools/metrics/read|指定されたサーバーの推奨エラスティック データベース プールのメトリックを取得します。|
|/servers/recommendedElasticPools/databases/read|指定されたサーバーの推奨エラスティック データベース プールに追加する必要があるデータベースを取得します。|
|/servers/elasticPools/read|指定されたサーバーのエラスティック データベース プールの詳細を取得します。|
|/servers/elasticPools/write|新しいエラスティック データベース プールを作成するか、既存のエラスティック データベース プールのプロパティを変更します。|
|/servers/elasticPools/delete|既存のエラスティック データベース プールを削除します。|
|/servers/elasticPools/operationResults/read|指定されたエラスティック データベース プール操作の詳細を取得します。|
|/servers/elasticPools/providers/Microsoft.Insights/<br>metricDefinitions/read|エラスティック データベース プールの利用可能なメトリックの種類を返します。|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/read|リソースの診断設定を取得します。|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/servers/elasticPools/metrics/read|エラスティック データベース プールのリソース使用率メトリックを返します。|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|エラスティック データベース プールに含まれる指定されたデータベースのアクティビティと詳細を取得します。|
|/servers/elasticPools/advisors/read|エラスティック プールで使用できる Advisor の一覧を返します。|
|/servers/elasticPools/advisors/write|エラスティック プール レベルでの Advisor の自動実行の状態を更新します。|
|/servers/elasticPools/advisors/recommendedActions/read|エラスティック プールの指定された Advisor の推奨アクションの一覧を返します。|
|/servers/elasticPools/advisors/recommendedActions/write|エラスティック プールに推奨アクションを適用します。|
|/servers/elasticPools/elasticPoolActivity/read|指定されたエラスティック データベース プールのアクティビティと詳細を取得します。|
|/servers/elasticPools/databases/read|指定されたサーバー上のエラスティック データベース プールに含まれるデータベースの一覧と詳細を取得します。|
|/servers/auditingPolicies/read|指定されたサーバーで構成されている既定のサーバー テーブル監査ポリシーの詳細を取得します。|
|/servers/auditingPolicies/write|指定されたサーバーの既定のサーバー テーブル監査を変更します。|
|/servers/disasterRecoveryConfiguration/operationResults/read|ディザスター リカバリーの構成操作の結果を取得します。|
|/servers/advisors/read|サーバーで使用できる Advisor の一覧を返します。|
|/servers/advisors/write|サーバー レベルでの Advisor の自動実行の状態を更新します。|
|/servers/advisors/recommendedActions/read|サーバーの指定された Advisor の推奨アクションの一覧を返します。|
|/servers/advisors/recommendedActions/write|サーバーに推奨アクションを適用します。|
|/servers/usages/read|サーバーの DTU クォータと、サーバー内のすべてのデータベースによる現在の DTU 消費量を返します。|
|/servers/elasticPoolEstimates/read|このサーバー用に既に作成されているエラスティック プール推定値の一覧を返します。|
|/servers/elasticPoolEstimates/write|提供されているデータベースの一覧用に、新しいエラスティック プール推定値を作成します。|
|/servers/auditingSettings/read|指定されたサーバーで構成されているサーバー BLOB 監査ポリシーの詳細を取得します。|
|/servers/auditingSettings/write|指定されたサーバーのサーバー BLOB 監査を変更します。|
|/servers/auditingSettings/operationResults/read|サーバー BLOB 監査ポリシーの設定操作の結果を取得します。|
|/servers/backupLongTermRetentionVaults/read|この操作を使用して、バックアップ長期リテンション期間コンテナーを取得します。 このサーバーに登録されているコンテナーに関する情報が返されます。|
|/servers/backupLongTermRetentionVaults/write|バックアップ長期リテンション期間コンテナーを登録します。|
|/servers/restorableDroppedDatabases/read|指定されたサーバーで破棄されたデータベースのうち、まだリテンション期間ポリシーの範囲内にあるデータベースの一覧を取得します。 この操作では、データベースおよび関連するメタデータ (削除日など) の一覧を返します。|
|/servers/databases/read|サブスクリプションのリソース グループ内のサーバーの一覧を返します。|
|/servers/databases/write|サブスクリプションのリソース グループに新しいサーバーを作成するか、リソース グループ内の既存のサーバーのプロパティを変更します。|
|/servers/databases/delete|サーバーおよびすべての包含データベースとエラスティック プールを削除します。|
|/servers/databases/export/action|サーバーに新しいデータベースを作成するか、DacPac パッケージからスキーマとデータをデプロイします。|
|/servers/databases/VulnerabilityAssessmentScans/action|データベースの脆弱性評価スキャンを実行します。|
|/servers/databases/pause/action|DataWarehouse エディション データベースを一時停止します。|
|/servers/databases/resume/action|DataWarehouse エディション データベースを再開します。|
|/servers/databases/operationResults/read|この操作を使用して、実行時間の長いデータベース操作 (スケールなど) の進行状況を追跡します。|
|/servers/databases/replicationLinks/read|特定のデータベース用に確立されたレプリケーション リンクの詳細を返します。|
|/servers/databases/replicationLinks/delete|レプリケーション関係を強制的に終了します。データが失われる可能性があります。|
|/servers/databases/replicationLinks/unlink/action|レプリケーション関係を強制的に終了するか、パートナーとの同期後に終了します。|
|/servers/databases/replicationLinks/failover/action|プライマリからすべての変更を同期した後にフェールオーバーします。このデータベースがレプリケーション関係のプライマリになり、リモート プライマリがセカンダリになります。|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|即座にフェールオーバーします。このデータベースがレプリケーション関係のプライマリになり、リモート プライマリがセカンダリになります。データが失われる可能性があります。|
|/servers/databases/replicationLinks/updateReplicationMode/action|リンクのレプリケーション モードを同期モードまたは非同期モードに更新します。|
|/servers/databases/replicationLinks/operationResults/read|データベース レプリケーション リンク上の実行時間の長い操作の状態を取得します。|
|/servers/databases/dataMaskingPolicies/read|指定されたデータベースで構成されているデータ マスク ポリシーの詳細を取得します。|
|/servers/databases/dataMaskingPolicies/write|指定されたデータベースのデータ マスク ポリシーを変更します。|
|/servers/databases/dataMaskingPolicies/rules/read|指定されたデータベースで構成されているデータ マスク ポリシー ルールの詳細を取得します。|
|/servers/databases/dataMaskingPolicies/rules/write|指定されたデータベースのデータ マスク ポリシー ルールを変更します。|
|/servers/databases/securityAlertPolicies/read|指定されたデータベースで構成されている脅威検出ポリシーの詳細を取得します。|
|/servers/databases/securityAlertPolicies/write|指定されたデータベースの脅威検出ポリシーを変更します。|
|/servers/databases/providers/Microsoft.Insights/<br>metricDefinitions/read|データベースの利用可能なメトリックの種類を返します。|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/read|リソースの診断設定を取得します。|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/write|リソースの診断設定を作成または更新します。|
|/servers/databases/providers/Microsoft.Insights/<br>logDefinitions/read|データベースの利用可能なログを取得します。|
|/servers/databases/topQueries/read|選択した期間の選択したクエリの集計されたランタイム統計を返します。|
|/servers/databases/topQueries/queryText/read|選択したクエリ ID の Transact-SQL テキストを返します。|
|/servers/databases/topQueries/statistics/read|選択した期間の選択したクエリの集計されたランタイム統計を返します。|
|/servers/databases/connectionPolicies/read|指定されたデータベースで構成されている接続ポリシーの詳細を取得します。|
|/servers/databases/connectionPolicies/write|指定されたデータベースの接続ポリシーを変更します。|
|/servers/databases/metrics/read|データベースのリソース使用率メトリックを返します。|
|/servers/databases/auditRecords/read|データベースの BLOB 監査レコードを取得します。|
|/servers/databases/transparentDataEncryption/read|指定されたデータベースの Transparent Data Encryption セキュリティ機能の状態と詳細を取得します。|
|/servers/databases/transparentDataEncryption/write|指定されたデータベースの Transparent Data Encryption を有効または無効にします。|
|/servers/databases/transparentDataEncryption/operationResults/read|指定されたデータベースの Transparent Data Encryption セキュリティ機能の状態と詳細を取得します。|
|/servers/databases/auditingPolicies/read|指定されたデータベースで構成されているテーブル監査ポリシーの詳細を取得します。|
|/servers/databases/auditingPolicies/write|指定されたデータベースのテーブル監査ポリシーを変更します。|
|/servers/databases/dataWarehouseQueries/read|選択したクエリ ID のデータ ウェアハウス分散クエリ情報を返します。|
|/servers/databases/dataWarehouseQueries/<br>dataWarehouseQuerySteps/read|選択したステップ ID のデータ ウェアハウス クエリの分散クエリ ステップ情報を返します。|
|/servers/databases/serviceTierAdvisors/read|パフォーマンスを向上させたり、コストを削減したりするために、クエリ実行の統計に基づいて、データベースのスケールアップまたはスケールダウンに関する提案を返します。|
|/servers/databases/advisors/read|データベースで使用できる Advisor の一覧を返します。|
|/servers/databases/advisors/write|データベース レベルでの Advisor の自動実行の状態を更新します。|
|/servers/databases/advisors/recommendedActions/read|データベースの指定された Advisor の推奨アクションの一覧を返します。|
|/servers/databases/advisors/recommendedActions/write|データベースに推奨アクションを適用します。|
|/servers/databases/usages/read|データベース サイズの上限と、データが占有している現在のサイズを返します。|
|/servers/databases/queryStore/read|データベースのクエリ ストア設定の現在の値を返します。|
|/servers/databases/queryStore/write|データベースのクエリ ストア設定を更新します。|
|/servers/databases/auditingSettings/read|指定されたデータベースで構成されている BLOB 監査ポリシーの詳細を取得します。|
|/servers/databases/auditingSettings/write|指定されたデータベースの BLOB 監査ポリシーを変更します。|
|/servers/databases/schemas/tables/recommendedIndexes/read|データベースのインデックス推奨事項の一覧を取得します。|
|/servers/databases/schemas/tables/recommendedIndexes/write|インデックス推奨事項を適用します。|
|/servers/databases/schemas/tables/columns/read|テーブルの列の一覧を取得します。|
|/servers/databases/missingindexes/read|クエリ パフォーマンスを向上させるために、作成、変更、または削除するデータベース インデックスに関する提案を返します。|
|/servers/databases/missingindexes/write|特定のデータベースでデータベース インデックス推奨事項を使用します。|
|/servers/databases/importExportOperationResults/read|ストレージ アカウントにある DacPac からのデータベースのインポート/エクスポート操作の詳細を返します。|
|/servers/importExportOperationResults/read|指定されたサーバーでのストレージ アカウントからのデータベースのインポート操作の詳細が含まれた一覧を返します。|

## <a name="microsoftstorage"></a>Microsoft.Storage

| 操作 | Description |
|---|---|
|/register/action|ストレージ リソース プロバイダーにサブスクリプションを登録し、ストレージ アカウントを作成できるようにします。|
|/checknameavailability/read|アカウント名が有効であり、使用されていないことを確認します。|
|/storageAccounts/write|指定されたパラメーターを使用してストレージ アカウントを作成するか、プロパティまたはタグを更新します。または、指定されたストレージ アカウントのカスタム ドメインを追加します。|
|/storageAccounts/delete|既存のストレージ アカウントを削除します。|
|/storageAccounts/listkeys/action|指定されたストレージ アカウントのアクセス キーを返します。|
|/storageAccounts/regeneratekey/action|指定されたストレージ アカウントのアクセス キーを再生成します。|
|/storageAccounts/read|ストレージ アカウントの一覧を返すか、指定されたストレージ アカウントのプロパティを取得します。|
|/storageAccounts/listAccountSas/action|指定されたストレージ アカウントのアカウント SAS トークンを返します。|
|/storageAccounts/listServiceSas/action|ストレージ サービス SAS トークン。|
|/storageAccounts/services/diagnosticSettings/write|ストレージ アカウントの診断設定を作成または更新します。|
|/skus/read|Microsoft.Storage でサポートされている SKU を一覧表示します。|
|/usages/read|指定されたサブスクリプションのリソースの制限と現在の使用回数を返します。|
|/operations/read|非同期操作の状態をポーリングします。|
|/locations/deleteVirtualNetworkOrSubnets/action|仮想ネットワークまたはサブネットを削除していることを Microsoft.Storage に通知します。|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| 操作 | Description |
|---|---|
|/managers/clearAlerts/action|デバイス マネージャーに関連付けられているすべてのアラートをクリアします。|
|/managers/getActivationKey/action|デバイス マネージャーのアクティブ化キーを取得します。|
|/managers/regenerateActivationKey/action|デバイス マネージャーのアクティブ化キーを再生成します。|
|/managers/regenarateRegistationCertificate/action|デバイス マネージャーの登録証明書を再生成します。|
|/managers/getEncryptionKey/action|デバイス マネージャーの暗号化キーを取得します。|
|/managers/read|デバイス マネージャーを一覧表示または取得します。|
|/managers/delete|デバイス マネージャーを削除します。|
|/managers/write|デバイス マネージャーを作成または更新します。|
|/managers/configureDevice/action|デバイスを構成します。|
|/managers/listActivationKey/action|StorSimple デバイス マネージャーのアクティブ化キーを取得します。|
|/managers/listPublicEncryptionKey/action|StorSimple デバイス マネージャーの公開暗号化キーを一覧表示します。|
|/managers/listPrivateEncryptionKey/action|StorSimple デバイス マネージャーの秘密暗号化キーを取得します。|
|/managers/provisionCloudAppliance/action|新しいクラウド アプライアンスを作成します。|
|/Managers/write|"コンテナーの作成" 操作では、"コンテナー" 型の Azure リソースを作成します。|
|/Managers/read|"コンテナーの取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトを取得します。|
|/Managers/delete|"コンテナーの削除" 操作では、指定された "コンテナー" 型の Azure リソースを削除します。|
|/managers/storageAccountCredentials/write|ストレージ アカウントの資格情報を作成または更新します。|
|/managers/storageAccountCredentials/read|ストレージ アカウントの資格情報を一覧表示または取得します。|
|/managers/storageAccountCredentials/delete|ストレージ アカウントの資格情報を削除します。|
|/managers/storageAccountCredentials/listAccessKey/action|ストレージ アカウントの資格情報のアクセス キーを一覧表示します。|
|/managers/accessControlRecords/read|アクセス制御レコードを一覧表示または取得します。|
|/managers/accessControlRecords/write|アクセス制御レコードを作成または更新します。|
|/managers/accessControlRecords/delete|アクセス制御レコードを削除します。|
|/managers/metrics/read|メトリックを一覧表示または取得します。|
|/managers/bandwidthSettings/read|帯域幅設定を一覧表示します (8000 シリーズのみ)。|
|/managers/bandwidthSettings/write|新しい帯域幅設定を作成するか、帯域幅設定を更新します (8000 シリーズのみ)。|
|/managers/bandwidthSettings/delete|既存の帯域幅設定を削除します (8000 シリーズのみ)。|
|/Managers/extendedInformation/read|"拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。|
|/Managers/extendedInformation/write|"拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。|
|/Managers/extendedInformation/delete|"拡張情報の取得" 操作では、"コンテナー" 型の Azure リソースを表すオブジェクトの拡張情報を取得します。|
|/managers/alerts/read|アラートを一覧表示または取得します。|
|/managers/storageDomains/read|ストレージ ドメインを一覧表示または取得します。|
|/managers/storageDomains/write|ストレージ ドメインを作成または更新します。|
|/managers/storageDomains/delete|ストレージ ドメインを削除します。|
|/managers/devices/scanForUpdates/action|デバイスで更新プログラムをスキャンします。|
|/managers/devices/download/action|デバイスの更新プログラムをダウンロードします。|
|/managers/devices/install/action|デバイスに更新プログラムをインストールします。|
|/managers/devices/read|デバイスを一覧表示または取得します。|
|/managers/devices/write|デバイスを作成または更新します。|
|/managers/devices/delete|デバイスを削除します。|
|/managers/devices/deactivate/action|デバイスを非アクティブ化します。|
|/managers/devices/publishSupportPackage/action|Microsoft サポートによるトラブルシューティングのために、デバイスのサポート パッケージを発行します。|
|/managers/devices/failover/action|デバイスをフェールオーバーします。|
|/managers/devices/sendTestAlertEmail/action|構成済みの電子メール受信者にテスト アラート メールを送信します。|
|/managers/devices/installUpdates/action|デバイスに更新プログラムをインストールします。|
|/managers/devices/listFailoverSets/action|既存のデバイスのフェールオーバー セットを一覧表示します。|
|/managers/devices/listFailoverTargets/action|デバイスのフェールオーバー ターゲットを一覧表示します。|
|/managers/devices/publicEncryptionKey/action|デバイス マネージャーの公開暗号化キーを一覧表示します。|
|/managers/devices/hardwareComponentGroups/<br>read|ハードウェア コンポーネント グループを一覧表示します。|
|/managers/devices/hardwareComponentGroups/<br>changeControllerPowerState/action|ハードウェア コンポーネント グループのコントローラーの電源の状態を変更します。|
|/managers/devices/metrics/read|メトリックを一覧表示または取得します。|
|/managers/devices/chapSettings/write|CHAP 設定を作成または更新します。|
|/managers/devices/chapSettings/read|CHAP 設定を一覧表示または取得します。|
|/managers/devices/chapSettings/delete|CHAP 設定を削除します。|
|/managers/devices/backupScheduleGroups/read|バックアップ スケジュール グループを一覧表示または取得します。|
|/managers/devices/backupScheduleGroups/write|バックアップ スケジュール グループを作成または更新します。|
|/managers/devices/backupScheduleGroups/delete|バックアップ スケジュール グループを削除します。|
|/managers/devices/updateSummary/read|更新の概要を一覧表示または取得します。|
|/managers/devices/migrationSourceConfigurations/<br>import/action|移行のためにソース構成をインポートします。|
|/managers/devices/migrationSourceConfigurations/<br>startMigrationEstimate/action|移行プロセスの継続時間を推定するジョブを開始します。|
|/managers/devices/migrationSourceConfigurations/<br>startMigration/action|ソース構成を使用して移行を開始します。|
|/managers/devices/migrationSourceConfigurations/<br>confirmMigration/action|移行が正常に完了したことを確認し、移行をコミットします。|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationEstimate/action|移行推定ジョブの状態を取得します。|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationStatus/action|移行の状態を取得します。|
|/managers/devices/migrationSourceConfigurations/<br>fetchConfirmMigrationStatus/action|移行の確認の状態を取得します。|
|/managers/devices/alertSettings/read|アラート設定を一覧表示または取得します。|
|/managers/devices/alertSettings/write|アラート設定を作成または更新します。|
|/managers/devices/networkSettings/read|ネットワーク設定を一覧表示または取得します。|
|/managers/devices/networkSettings/write|新しいネットワーク設定を作成するか、ネットワーク設定を更新します。|
|/managers/devices/jobs/read|ジョブを一覧表示または取得します。|
|/managers/devices/jobs/cancel/action|実行中のジョブを取り消します。|
|/managers/devices/metricsDefinitions/read|メトリック定義を一覧表示または取得します。|
|/managers/devices/volumeContainers/write|新しいボリューム コンテナーを作成するか、ボリューム コンテナーを更新します (8000 シリーズのみ)。|
|/managers/devices/volumeContainers/read|ボリューム コンテナーを一覧表示します (8000 シリーズのみ)。|
|/managers/devices/volumeContainers/delete|既存のボリューム コンテナーを削除します (8000 シリーズのみ)。|
|/managers/devices/volumeContainers/listEncryptionKeys/action|ボリューム コンテナーの暗号化キーを一覧表示します。|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|ボリューム コンテナーの暗号化キーをロールオーバーします。|
|/managers/devices/volumeContainers/metrics/read|メトリックを一覧表示します。|
|/managers/devices/volumeContainers/volumes/read|ボリュームを一覧表示します。|
|/managers/devices/volumeContainers/volumes/write|新しいボリュームを作成するか、ボリュームを更新します。|
|/managers/devices/volumeContainers/volumes/delete|既存のボリュームを削除します。|
|/managers/devices/volumeContainers/volumes/metrics/read|メトリックを一覧表示します。|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|メトリック定義を一覧表示します。|
|/managers/devices/volumeContainers/metricsDefinitions/read|メトリック定義を一覧表示します。|
|/managers/devices/iscsiservers/read|iSCSI サーバーを一覧表示または取得します。|
|/managers/devices/iscsiservers/write|iSCSI サーバーを作成または更新します。|
|/managers/devices/iscsiservers/delete|iSCSI サーバーを削除します。|
|/managers/devices/iscsiservers/backup/action|iSCSI サーバーのバックアップを作成します。|
|/managers/devices/iscsiservers/metrics/read|メトリックを一覧表示または取得します。|
|/managers/devices/iscsiservers/disks/read|ディスクを一覧表示または取得します。|
|/managers/devices/iscsiservers/disks/write|ディスクを作成または更新します。|
|/managers/devices/iscsiservers/disks/delete|ディスクを削除します。|
|/managers/devices/iscsiservers/disks/metrics/read|メトリックを一覧表示または取得します。|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|メトリック定義を一覧表示または取得します。|
|/managers/devices/iscsiservers/metricsDefinitions/read|メトリック定義を一覧表示または取得します。|
|/managers/devices/backups/read|バックアップ セットを一覧表示または取得します。|
|/managers/devices/backups/delete|バックアップ セットを削除します。|
|/managers/devices/backups/restore/action|バックアップ セットからすべてのボリュームを復元します。|
|/managers/devices/backups/elements/clone/action|バックアップ要素を使用して、共有またはボリュームを複製します。|
|/managers/devices/backupPolicies/write|新しいバックアップ ポリシーを作成するか、バックアップ ポリシーを更新します (8000 シリーズのみ)。|
|/managers/devices/backupPolicies/read|バックアップ ポリシーを一覧表示します (8000 シリーズのみ)。|
|/managers/devices/backupPolicies/delete|既存のバックアップ ポリシーを削除します (8000 シリーズのみ)。|
|/managers/devices/backupPolicies/backup/action|手動バックアップを実行して、ポリシーで保護されているすべてのボリュームのオンデマンド バックアップを作成します。|
|/managers/devices/backupPolicies/schedules/write|新しいスケジュールを作成するか、スケジュールを更新します。|
|/managers/devices/backupPolicies/schedules/read|スケジュールを一覧表示します。|
|/managers/devices/backupPolicies/schedules/delete|既存のスケジュールを削除します。|
|/managers/devices/securitySettings/update/action|セキュリティ設定を更新します。|
|/managers/devices/securitySettings/read|セキュリティ設定を一覧表示します。|
|/managers/devices/securitySettings/<br>syncRemoteManagementCertificate/action|デバイスのリモート管理証明書を同期します。|
|/managers/devices/securitySettings/write|新しいセキュリティ設定を作成するか、セキュリティ設定を更新します。|
|/managers/devices/fileservers/read|ファイル サーバーを一覧表示または取得します。|
|/managers/devices/fileservers/write|ファイル サーバーを作成または更新します。|
|/managers/devices/fileservers/delete|ファイル サーバーを削除します。|
|/managers/devices/fileservers/backup/action|ファイル サーバーのバックアップを作成します。|
|/managers/devices/fileservers/metrics/read|メトリックを一覧表示または取得します。|
|/managers/devices/fileservers/shares/write|共有を作成または更新します。|
|/managers/devices/fileservers/shares/read|共有を一覧表示または取得します。|
|/managers/devices/fileservers/shares/delete|共有を削除します。|
|/managers/devices/fileservers/shares/metrics/read|メトリックを一覧表示または取得します。|
|/managers/devices/fileservers/shares/metricsDefinitions/read|メトリック定義を一覧表示または取得します。|
|/managers/devices/fileservers/metricsDefinitions/read|メトリック定義を一覧表示または取得します。|
|/managers/devices/timeSettings/read|時刻設定を一覧表示または取得します。|
|/managers/devices/timeSettings/write|新しい時刻設定を作成するか、時刻設定を更新します。|
|/Managers/certificates/write|"リソース証明書を更新" 操作では、リソース/コンテナー資格情報証明書を更新します。|
|/managers/cloudApplianceConfigurations/read|クラウド アプライアンスでサポートされている構成を一覧表示します。|
|/managers/metricsDefinitions/read|メトリック定義を一覧表示または取得します。|
|/managers/encryptionSettings/read|暗号化設定を一覧表示または取得します。|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| 操作 | 説明 |
|---|---|
|/streamingjobs/Start/action|Stream Analytics ジョブを開始します。|
|/streamingjobs/Stop/action|Stream Analytics ジョブを停止します。|
|/streamingjobs/Read|Stream Analytics ジョブを読み取ります。|
|/streamingjobs/Write|Stream Analytics ジョブを書き込みます。|
|/streamingjobs/Delete|Stream Analytics ジョブを削除します。|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|streamingjobs の利用可能なメトリックを取得します。|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|診断設定を読み取ります。|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|診断設定を書き込みます。|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|streamingjobs の利用可能なログを取得します。|
|/streamingjobs/transformations/Read|Stream Analytics ジョブ変換を読み取ります。|
|/streamingjobs/transformations/Write|Stream Analytics ジョブ変換を書き込みます。|
|/streamingjobs/transformations/Delete|Stream Analytics ジョブ変換を削除します。|
|/streamingjobs/inputs/Read|Stream Analytics ジョブ入力を読み取ります。|
|/streamingjobs/inputs/Write|Stream Analytics ジョブ入力を書き込みます。|
|/streamingjobs/inputs/Delete|Stream Analytics ジョブ入力を削除します。|
|/streamingjobs/outputs/Read|Stream Analytics ジョブ出力を読み取ります。|
|/streamingjobs/outputs/Write|Stream Analytics ジョブ出力を書き込みます。|
|/streamingjobs/outputs/Delete|Stream Analytics ジョブ出力を削除します。|

## <a name="microsoftsupport"></a>Microsoft.Support

| 操作 | Description |
|---|---|
|/register/action|サポート リソース プロバイダーに登録します。|
|/supportTickets/read|サポート チケットの詳細 (ステータス、重大度、連絡先の詳細、やり取りなど) を取得するか、サブスクリプション全体のサポート チケットの一覧を取得します。|
|/supportTickets/write|サポート チケットを作成または更新します。 技術、課金、クォータ、またはサブスクリプション管理に関する問題のサポート チケットを作成できます。 既存のサポート チケットの重大度、連絡先の詳細、やり取りを更新できます。|

## <a name="microsoftweb"></a>Microsoft.Web

| 操作 | 説明 |
|---|---|
|/unregister/action|サブスクリプションへの Microsoft.Web リソース プロバイダーの登録を解除します。|
|/validate/action|検証します。|
|/register/action|Microsoft.Web リソース プロバイダーをサブスクリプションに登録します。|
|/hostingEnvironments/Read|App Service Environment のプロパティを取得します。|
|/hostingEnvironments/Write|新しい App Service Environment を作成するか、既存の App Service Environment を更新します。|
|/hostingEnvironments/Delete|App Service Environment を削除します。|
|/hostingEnvironments/reboot/Action|App Service Environment 内のすべてのマシンを再起動します。|
|/hostingenvironments/resume/action|ホスティング環境を再開します。|
|/hostingenvironments/suspend/action|ホスティング環境を中断します。|
|/hostingenvironments/metricdefinitions/read|ホスティング環境のメトリック定義を取得します。|
|/hostingEnvironments/workerPools/Read|App Service Environment のワーカー プールのプロパティを取得します。|
|/hostingEnvironments/workerPools/Write|App Service Environment に新しいワーカー プールを作成するか、既存のワーカー プールを更新します。|
|/hostingenvironments/workerpools/metricdefinitions/read|ホスティング環境のワーカー プールのメトリック定義を取得します。|
|/hostingenvironments/workerpools/metrics/read|ホスティング環境のワーカー プールのメトリックを取得します。|
|/hostingenvironments/workerpools/skus/read|ホスティング環境のワーカー プールの SKU を取得します。|
|/hostingenvironments/workerpools/usages/read|ホスティング環境のワーカー プールの使用状況を取得します。|
|/hostingenvironments/sites/read|ホスティング環境の Web アプリを取得します。|
|/hostingenvironments/serverfarms/read|ホスティング環境の App Service プランを取得します。|
|/hostingenvironments/usages/read|ホスティング環境の使用状況を取得します。|
|/hostingenvironments/capacities/read|ホスティング環境の容量を取得します。|
|/hostingenvironments/operations/read|ホスティング環境の操作を取得します。|
|/hostingEnvironments/multiRolePools/Read|App Service Environment のフロントエンド プールのプロパティを取得します。|
|/hostingEnvironments/multiRolePools/Write|App Service Environment に新しいフロントエンド プールを作成するか、既存のフロントエンド プールを更新します。|
|/hostingenvironments/multirolepools/metricdefinitions/read|ホスティング環境のマルチロール プールのメトリック定義を取得します。|
|/hostingenvironments/multirolepools/metrics/read|ホスティング環境のマルチロール プールのメトリックを取得します。|
|/hostingenvironments/multirolepools/skus/read|ホスティング環境のマルチロール プールの SKU を取得します。|
|/hostingenvironments/multirolepools/usages/read|ホスティング環境のマルチロール プールの使用状況を取得します。|
|/hostingenvironments/diagnostics/read|ホスティング環境の診断を取得します。|
|/publishingusers/read|発行ユーザーを取得します。|
|/publishingusers/write|発行ユーザーを更新します。|
|/checknameavailability/read|リソース名を使用できるかどうかを確認します。|
|/geoRegions/Read|地理的リージョンの一覧を取得します。|
|/sites/Read|Web アプリのプロパティを取得します。|
|/sites/Write|新しい Web アプリを作成するか、既存の Web アプリを更新します。|
|/sites/Delete|既存の Web アプリの削除|
|/sites/backup/Action|Web アプリの新しいバックアップを作成します。|
|/sites/publishxml/Action|Web アプリの発行プロファイル XML を取得します。|
|/sites/publish/Action|Web アプリを発行します。|
|/sites/restart/Action|Web アプリを再起動します。|
|/sites/start/Action|Web アプリを起動します。|
|/sites/stop/Action|Web アプリを停止します。|
|/sites/slotsswap/Action|Web アプリのデプロイ スロットを入れ替えます。|
|/sites/slotsdiffs/Action|Web アプリとスロットの構成の違いを取得します。|
|/sites/applySlotConfig/Action|ターゲット スロットの Web アプリ スロット構成を現在の Web アプリに適用します。|
|/sites/resetSlotConfig/Action|Web アプリの構成をリセットします。|
|/sites/functions/action|Web アプリの関数。|
|/sites/listsyncfunctiontriggerstatus/action|Web アプリの同期関数のトリガーの状態を一覧表示します。|
|/sites/networktrace/action|Web アプリのネットワーク トレース。|
|/sites/newpassword/action|Web アプリの Newpassword。|
|/sites/sync/action|Web アプリを同期します。|
|/sites/operationresults/read|Web アプリの操作の結果を取得します。|
|/sites/webjobs/read|Web アプリの Web ジョブを取得します。|
|/sites/backup/read|Web アプリのバックアップを取得します。|
|/sites/backup/write|Web アプリのバックアップを更新します。|
|/sites/metricdefinitions/read|Web アプリのメトリック定義を取得します。|
|/sites/metrics/read|Web アプリのメトリックを取得します。|
|/sites/continuouswebjobs/delete|Web アプリの継続的な Web ジョブを削除します。|
|/sites/continuouswebjobs/read|Web アプリの継続的な Web ジョブを取得します。|
|/sites/continuouswebjobs/start/action|Web アプリの継続的な Web ジョブを開始します。|
|/sites/continuouswebjobs/stop/action|Web アプリの継続的な Web ジョブを停止します。|
|/sites/domainownershipidentifiers/read|Web アプリのドメイン所有権識別子を取得します。|
|/sites/domainownershipidentifiers/write|Web アプリのドメイン所有権識別子を更新します。|
|/sites/premieraddons/delete|Web アプリのプレミア アドオンを削除します。|
|/sites/premieraddons/read|Web アプリのプレミア アドオンを取得します。|
|/sites/premieraddons/write|Web アプリのプレミア アドオンを更新します。|
|/sites/triggeredwebjobs/delete|Web アプリのトリガーされた Web ジョブを削除します。|
|/sites/triggeredwebjobs/read|Web アプリのトリガーされた Web ジョブを取得します。|
|/sites/triggeredwebjobs/run/action|Web アプリのトリガーされた Web ジョブを実行します。|
|/sites/hostnamebindings/delete|Web アプリのホスト名バインドを削除します。|
|/sites/hostnamebindings/read|Web アプリのホスト名バインドを取得します。|
|/sites/hostnamebindings/write|Web アプリのホスト名バインドを更新します。|
|/sites/virtualnetworkconnections/delete|Web アプリの仮想ネットワーク接続を削除します。|
|/sites/virtualnetworkconnections/read|Web アプリの仮想ネットワーク接続を取得します。|
|/sites/virtualnetworkconnections/write|Web アプリの仮想ネットワーク接続を更新します。|
|/sites/virtualnetworkconnections/gateways/read|Web アプリの仮想ネットワーク接続のゲートウェイを取得します。|
|/sites/virtualnetworkconnections/gateways/write|Web アプリの仮想ネットワーク接続のゲートウェイを更新します。|
|/sites/publishxml/read|Web アプリの発行 XML を取得します。|
|/sites/hybridconnectionrelays/read|Web アプリのハイブリッド接続リレーを取得します。|
|/sites/perfcounters/read|Web アプリのパフォーマンス カウンターを取得します。|
|/sites/usages/read|Web アプリの使用状況を取得します。|
|/sites/slots/Write|新しい Web アプリ スロットを作成するか、既存の Web アプリ スロットを更新します。|
|/sites/slots/Delete|既存の Web アプリ スロットを削除します。|
|/sites/slots/backup/Action|Web アプリ スロットの新しいバックアップを作成します。|
|/sites/slots/publishxml/Action|Web アプリ スロットの発行プロファイル XML を取得します。|
|/sites/slots/publish/Action|Web アプリ スロットを発行します。|
|/sites/slots/restart/Action|Web アプリ スロットを再起動します。|
|/sites/slots/start/Action|Web アプリ スロットを起動します。|
|/sites/slots/stop/Action|Web アプリ スロットを停止します。|
|/sites/slots/slotsswap/Action|Web アプリのデプロイ スロットを入れ替えます。|
|/sites/slots/slotsdiffs/Action|Web アプリとスロットの構成の違いを取得します。|
|/sites/slots/applySlotConfig/Action|ターゲット スロットの Web アプリ スロット構成を現在のスロットに適用します。|
|/sites/slots/resetSlotConfig/Action|Web アプリ スロット構成をリセットします。|
|/sites/slots/Read|Web アプリのデプロイ スロットのプロパティを取得します。|
|/sites/slots/newpassword/action|Web アプリとスロットの Newpassword。|
|/sites/slots/sync/action|Web アプリとスロットを同期します。|
|/sites/slots/operationresults/read|Web アプリとスロットの操作の結果を取得します。|
|/sites/slots/webjobs/read|Web アプリとスロットの Web ジョブを取得します。|
|/sites/slots/backup/write|Web アプリとスロットのバックアップを更新します。|
|/sites/slots/metricdefinitions/read|Web アプリとスロットのメトリック定義を取得します。|
|/sites/slots/metrics/read|Web アプリとスロットのメトリックを取得します。|
|/sites/slots/continuouswebjobs/delete|Web アプリとスロットの継続的な Web ジョブを削除します。|
|/sites/slots/continuouswebjobs/read|Web アプリとスロットの継続的な Web ジョブを取得します。|
|/sites/slots/continuouswebjobs/start/action|Web アプリとスロットの継続的な Web ジョブを開始します。|
|/sites/slots/continuouswebjobs/stop/action|Web アプリとスロットの継続的な Web ジョブを停止します。|
|/sites/slots/premieraddons/delete|Web アプリとスロットのプレミア アドオンを削除します。|
|/sites/slots/premieraddons/read|Web アプリとスロットのプレミア アドオンを取得します。|
|/sites/slots/premieraddons/write|Web アプリとスロットのプレミア アドオンを更新します。|
|/sites/slots/triggeredwebjobs/delete|Web アプリとスロットのトリガーされた Web ジョブを削除します。|
|/slots/triggeredwebjobs/delete|Web アプリとスロットのトリガーされた Web ジョブを取得します。|
|/sites/slots/triggeredwebjobs/run/action|Web アプリとスロットのトリガーされた Web ジョブを実行します。|
|/sites/slots/hostnamebindings/delete|Web アプリとスロットのホスト名バインドを削除します。|
|/sites/slots/hostnamebindings/read|Web アプリとスロットのホスト名バインドを取得します。|
|/sites/slots/hostnamebindings/write|Web アプリとスロットのホスト名バインドを更新します。|
|/sites/slots/phplogging/read|Web アプリとスロットの Phplogging を取得します。|
|/sites/slots/virtualnetworkconnections/delete|Web アプリとスロットの仮想ネットワーク接続を削除します。|
|/sites/slots/virtualnetworkconnections/read|Web アプリとスロットの仮想ネットワーク接続を取得します。|
|/sites/slots/virtualnetworkconnections/write|Web アプリとスロットの仮想ネットワーク接続を更新します。|
|/sites/slots/virtualnetworkconnections/gateways/write|Web アプリとスロットの仮想ネットワーク接続のゲートウェイを更新します。|
|/sites/slots/usages/read|Web アプリとスロットの使用状況を取得します。|
|/sites/slots/hybridconnection/delete|Web アプリとスロットのハイブリッド接続を削除します。|
|/sites/slots/hybridconnection/read|Web アプリとスロットのハイブリッド接続を取得します。|
|/sites/slots/hybridconnection/write|Web アプリとスロットのハイブリッド接続を更新します。|
|/sites/slots/config/Read|Web アプリ スロットの構成設定を取得します。|
|/sites/slots/config/list/Action|Web アプリ スロットのセキュリティに関する設定 (発行資格情報、アプリ設定、接続文字列など) を一覧表示します。|
|/sites/slots/config/Write|Web アプリ スロットの構成設定を更新します。|
|/sites/slots/config/delete|Web アプリとスロットの構成を削除します。|
|/sites/slots/instances/read|Web アプリとスロットのインスタンスを取得します。|
|/sites/slots/instances/processes/read|Web アプリとスロットのインスタンスのプロセスを取得します。|
|/sites/slots/instances/deployments/read|Web アプリとスロットのインスタンスのデプロイを取得します。|
|/sites/slots/sourcecontrols/Read|Web アプリ スロットのソース管理の構成設定を取得します。|
|/sites/slots/sourcecontrols/Write|Web アプリ スロットのソース管理の構成設定を更新します。|
|/sites/slots/sourcecontrols/Delete|Web アプリ スロットのソース管理の構成設定を削除します。|
|/sites/slots/restore/read|Web アプリとスロットの復元を取得します。|
|/sites/slots/analyzecustomhostname/read|Web アプリとスロットのカスタム ホスト名の分析を取得します。|
|/sites/slots/backups/Read|Web アプリ スロットのバックアップのプロパティを取得します。|
|/sites/slots/backups/list/action|Web アプリとスロットのバックアップを一覧表示します。|
|/sites/slots/backups/restore/action|Web アプリとスロットのバックアップを復元します。|
|/sites/slots/deployments/delete|Web アプリとスロットのデプロイを削除します。|
|/sites/slots/deployments/read|Web アプリとスロットのデプロイを取得します。|
|/sites/slots/deployments/write|Web アプリとスロットのデプロイを更新します。|
|/sites/slots/deployments/log/read|Web アプリとスロットのデプロイのログを取得します。|
|/sites/hybridconnection/delete|Web アプリのハイブリッド接続を削除します。|
|/sites/hybridconnection/read|Web アプリのハイブリッド接続を取得します。|
|/sites/hybridconnection/write|Web アプリのハイブリッド接続を更新します。|
|/sites/recommendationhistory/read|Web アプリの推奨事項の履歴を取得します。|
|/sites/recommendations/Read|Web アプリの推奨事項の一覧を取得します。|
|/sites/recommendations/disable/action|Web アプリの推奨事項を無効にします。|
|/sites/config/Read|Web アプリの構成設定を取得します。|
|/sites/config/list/Action|Web アプリのセキュリティに関する設定 (発行資格情報、アプリ設定、接続文字列など) を一覧表示します。|
|/sites/config/Write|Web アプリの構成設定を更新します。|
|/sites/config/delete|Web アプリの構成を削除します。|
|/sites/instances/read|Web アプリのインスタンスを取得します。|
|/sites/instances/processes/delete|Web アプリのインスタンスのプロセスを削除します。|
|/sites/instances/processes/read|Web アプリのインスタンスのプロセスを取得します。|
|/sites/instances/deployments/read|Web アプリのインスタンスのデプロイを取得します。|
|/sites/sourcecontrols/Read|Web アプリのソース管理の構成設定を取得します。|
|/sites/sourcecontrols/Write|Web アプリのソース管理の構成設定を更新します。|
|/sites/sourcecontrols/Delete|Web アプリのソース管理の構成設定を削除します。|
|/sites/restore/read|Web アプリの復元を取得します。|
|/sites/analyzecustomhostname/read|カスタム ホスト名を分析します。|
|/sites/backups/Read|Web アプリのバックアップのプロパティを取得します。|
|/sites/backups/list/action|Web アプリのバックアップを一覧表示します。|
|/sites/backups/restore/action|Web アプリのバックアップを復元します。|
|/sites/snapshots/read|Web アプリのスナップショットを取得します。|
|/sites/functions/delete|Web アプリの関数を削除します。|
|/sites/functions/listsecrets/action|Web アプリの関数のシークレットを一覧表示します。|
|/sites/functions/read|Web アプリの関数を取得します。|
|/sites/functions/write|Web アプリの関数を更新します。|
|/sites/deployments/delete|Web アプリのデプロイを削除します。|
|/sites/deployments/read|Web アプリのデプロイを取得します。|
|/sites/deployments/write|Web アプリのデプロイを更新します。|
|/sites/deployments/log/read|Web アプリのデプロイのログを取得します。|
|/sites/diagnostics/read|Web アプリの診断を取得します。|
|/sites/diagnostics/workerprocessrecycle/read|Web アプリの診断のワーカー プロセスのリサイクルを取得します。|
|/sites/diagnostics/workeravailability/read|Web アプリの診断の Workeravailability を取得します。|
|/sites/diagnostics/runtimeavailability/read|Web アプリの診断の実行時の可用性を取得します。|
|/sites/diagnostics/cpuanalysis/read|Web アプリの診断の Cpuanalysis を取得します。|
|/sites/diagnostics/servicehealth/read|Web アプリの診断のサービス正常性を取得します。|
|/sites/diagnostics/frebanalysis/read|Web アプリの診断の FREB 分析を取得します。|
|/availablestacks/read|使用可能なスタックを取得します。|
|/isusernameavailable/read|ユーザー名を使用できるかどうかを確認します。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Read|API の一覧を取得します。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Write|新しい API を追加するか、既存の API を更新します。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Delete|既存の API を削除します。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Read|接続の一覧を取得します。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Write|新しい接続を保存するか、既存の接続を更新します。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Delete|既存の接続を削除します。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Read|ConnectionAcls を読み取ります。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Write|ConnectionAcl を追加または更新します。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Delete|ConnectionAcl を削除します。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connectionAcls/Read|API の ConnectionAcls を読み取ります。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Read|ConnectionAcls を読み取ります。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Write|API ACL を作成または更新します。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Delete|API ACL を削除します。|
|/serverfarms/Read|App Service プランのプロパティを取得します。|
|/serverfarms/Write|新しい App Service プランを作成するか、既存の App Service プランを更新します。|
|/serverfarms/Delete|既存の App Service プランの削除|
|/serverfarms/restartSites/Action|App Service プランのすべての Web アプリを再起動します。|
|/serverfarms/operationresults/read|App Service プランの操作の結果を取得します。|
|/serverfarms/capabilities/read|App Service プランの機能を取得します。|
|/serverfarms/metricdefinitions/read|App Service プランのメトリック定義を取得します。|
|/serverfarms/metrics/read|App Service プランのメトリックを取得します。|
|/serverfarms/hybridconnectionplanlimits/read|App Service プランのハイブリッド接続プランの制限を取得します。|
|/serverfarms/virtualnetworkconnections/read|App Service プランの仮想ネットワーク接続を取得します。|
|/serverfarms/virtualnetworkconnections/routes/delete|App Service プランの仮想ネットワーク接続のルートを削除します。|
|/serverfarms/virtualnetworkconnections/routes/read|App Service プランの仮想ネットワーク接続のルートを取得します。|
|/serverfarms/virtualnetworkconnections/routes/write|App Service プランの仮想ネットワーク接続のルートを更新します。|
|/serverfarms/virtualnetworkconnections/gateways/write|App Service プランの仮想ネットワーク接続のゲートウェイを更新します。|
|/serverfarms/firstpartyapps/settings/delete|App Service プランのファースト パーティ アプリの設定を削除します。|
|/serverfarms/firstpartyapps/settings/read|App Service プランのファースト パーティ アプリの設定を取得します。|
|/serverfarms/firstpartyapps/settings/write|App Service プランのファースト パーティ アプリの設定を更新します。|
|/serverfarms/sites/read|App Service プランの Web アプリを取得します。|
|/serverfarms/workers/reboot/action|App Service プランの worker を再起動します。|
|/serverfarms/hybridconnectionrelays/read|App Service プランのハイブリッド接続リレーを取得します。|
|/serverfarms/skus/read|App Service プランの SKU を取得します。|
|/serverfarms/usages/read|App Service プランの使用状況を取得します。|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|App Service プランのハイブリッド接続の名前空間のリレーの Web アプリを取得します。|
|/ishostnameavailable/read|ホスト名を使用できるかどうかを確認します。|
|/connectionGateways/Read|接続ゲートウェイの一覧を取得します。|
|/connectionGateways/Write|接続ゲートウェイを作成または更新します。|
|/connectionGateways/Delete|接続ゲートウェイを削除します。|
|/connectionGateways/Join/Action|接続ゲートウェイを接続します。|
|/classicmobileservices/read|従来の Mobile Services を取得します。|
|/skus/read|SKU を取得します。|
|/certificates/Read|証明書の一覧を取得します。|
|/certificates/Write|新しい証明書を追加するか、既存の証明書を更新します。|
|/certificates/Delete|既存の証明書を削除します。|
|/operations/read|操作を取得します。|
|/recommendations/Read|サブスクリプションの推奨事項の一覧を取得します。|
|/ishostingenvironmentnameavailable/read|ホスティング環境名を使用できるかどうかを取得します。|
|/apiManagementAccounts/Read|ApiManagementAccounts の一覧を取得します。|
|/apiManagementAccounts/Write|新しい ApiManagementAccount を追加するか、既存の ApiManagementAccount を更新します。|
|/apiManagementAccounts/Delete|既存の ApiManagementAccount を削除します。|
|/apiManagementAccounts/connectionAcls/Read|接続 ACL の一覧を取得します。|
|/apiManagementAccounts/apiAcls/Read|ConnectionAcls を読み取ります。|
|/connections/Read|接続の一覧を取得します。|
|/connections/Write|接続を作成または更新します。|
|/connections/Delete|接続を削除します。|
|/connections/Join/Action|接続に参加します。|
|/connections/confirmconsentcode/action|接続の同意コードを確認します。|
|/connections/listconsentlinks/action|接続の同意リンクを一覧表示します。|
|/deploymentlocations/read|デプロイの場所を取得します。|
|/sourcecontrols/read|ソース管理を取得します。|
|/sourcecontrols/write|ソース管理を更新します。|
|/managedhostingenvironments/read|管理対象のホスティング環境を取得します。|
|/managedhostingenvironments/sites/read|管理対象のホスティング環境の Web アプリを取得します。|
|/managedhostingenvironments/serverfarms/read|管理対象のホスティング環境の App Service プランを取得します。|
|/locations/managedapis/read|マネージ API の場所を取得します。|
|/locations/apioperations/read|API 操作の場所を取得します。|
|/locations/connectiongatewayinstallations/read|接続ゲートウェイのインストールの場所を取得します。|
|/listSitesAssignedToHostName/Read|ホスト名に割り当てられたサイトの名前を取得します。|

## <a name="next-steps"></a>次のステップ

- [カスタム ロールを作成する](role-based-access-control-custom-roles.md)方法を確認します。

- [組み込みの RBAC ロール](role-based-access-built-in-roles.md)を確認します。

- アクセス権の割り当てを[ユーザーごと](role-based-access-control-manage-assignments.md)または[リソースごと](role-based-access-control-configure.md)に管理する方法を確認します。 
