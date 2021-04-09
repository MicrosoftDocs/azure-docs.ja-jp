---
title: Azure AD Connect クラウド同期のエラー コードと説明
description: クラウド同期エラー コードのリファレンス記事
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/14/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b1dbd9064e24327f129e8b8f957414d9162386
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650942"
---
# <a name="azure-ad-connect-cloud-sync-error-codes-and-descriptions"></a>Azure AD Connect クラウド同期のエラー コードと説明
エラー コードとその説明の一覧を次に示します。


## <a name="error-codes"></a>エラー コード

|エラー コード|詳細|シナリオ|解像度|
|-----|-----|-----|-----|
|[TimeOut]|エラー メッセージ:オンプレミスのエージェントにアクセスして構成を同期するときに、要求タイムアウト エラーが検出されました。 クラウド同期エージェントに関連するその他の問題については、トラブルシューティング ガイダンスを参照してください。|HIS への要求がタイムアウトになりました。現在のタイムアウト値は 10 分です。|[トラブルシューティング ガイダンス](how-to-troubleshoot.md)を参照してください。|
|HybridSynchronizationActiveDirectoryInternalServerError|エラー メッセージ:この時点で、この要求を処理できませんでした。 この問題が解決しない場合は、サポートに連絡し、次のジョブ ID を提供してください。AD2AADProvisioning.30b500eaf9c643b2b78804e80c1421fe.5c291d3c-d29f-4570-9d6b-f0c2fa3d5926. 追加情報:HTTP 要求の処理中に例外が発生しました。 |検索要求に対して SCIM 要求で受信したパラメーターを処理できませんでした。|詳細については、この例外の 'Response' プロパティによって返される HTTP 応答を参照してください。|
|HybridIdentityServiceNoAgentsAssigned|エラー メッセージ:同期しようとしているドメインでアクティブなエージェントが見つかりません。エージェントが削除されているかをご確認ください。 その場合は、エージェントを再インストールします。|実行中のエージェントはありません。 エージェントが削除された可能性があります。 新しいエージェントを登録します。|この場合、ポータルでドメインに割り当てられているエージェントは表示されません。|
|HybridIdentityServiceNoActiveAgents|エラー メッセージ:同期しようとしているドメインでアクティブなエージェントが見つかりません。エージェントがインストールされているサーバーに移動して、エージェントが実行されているかどうかを確認し、[サービス] で "Microsoft Azure AD クラウド同期エージェント" が実行されているかどうかをご確認ください。|エージェントが ServiceBus エンドポイントをリッスンしていません。 [エージェントは、Service Bus への接続を許可しないファイアウォールの内側にあります。](../../active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers.md#use-the-outbound-proxy-server)|
|HybridIdentityServiceInvalidResource|エラー メッセージ:この時点で、この要求を処理できませんでした。 この問題が解決しない場合は、サポートに連絡し、次のジョブ ID を提供してください。AD2AADProvisioning.3a2a0d8418f34f54a03da5b70b1f7b0c.d583d090-9cd3-4d0a-aee6-8d666658c3e9. 追加情報:クラウド同期のセットアップに問題があるようです。 オンプレミスの AD ドメインにクラウド同期エージェントを再登録し、Azure portal から構成を再起動してください。|アクセス先のエージェントが HIS によって認識されるように、リソース名を設定する必要があります。|オンプレミスの AD ドメインにクラウド同期エージェントを再登録し、Azure portal から構成を再起動してください。|
|HybridIdentityServiceAgentSignalingError|エラー メッセージ:この時点で、この要求を処理できませんでした。 この問題が解決しない場合は、サポートに連絡し、次のジョブ ID を提供してください。AD2AADProvisioning.92d2e8750f37407fa2301c9e52ad7e9b.efb835ef-62e8-42e3-b495-18d5272eb3f9. 追加情報:この時点で、この要求を処理できませんでした。 この問題が解決しない場合は、(構成の状態ウィンドウから) ジョブ ID を使用してサポートにお問い合わせください。|Service Bus からエージェントにメッセージを送信できません。 Service Bus が停止しているか、エージェントが応答していない可能性があります。|この問題が解決しない場合は、(構成の状態ウィンドウから) ジョブ ID を使用してサポートにお問い合わせください。|
|AzureDirectoryServiceServerBusy|エラー メッセージ:エラーが発生しました。 Error Code:81。 エラーの説明:Azure Active Directory は現在ビジー状態です。 この操作は自動的に再試行されます。 24 時間を超えてこの問題が解決しない場合は、テクニカル サポートにお問い合わせください。 追跡 ID:8a4ab3b5-3664-4278-ab64-9cff37fd3f4f サーバー名:|Azure Active Directory は現在ビジー状態です。|24 時間を超えてこの問題が解決しない場合は、テクニカル サポートにお問い合わせください。|
|AzureActiveDirectoryInvalidCredential|エラー メッセージ:Azure AD Connect クラウド同期の実行に使用されるサービス アカウントに問題が見つかりました。クラウド サービス アカウントを修復するには、[こちら](./how-to-troubleshoot.md)の手順に従ってください。 このエラーが解決しない場合は、(構成の状態ウィンドウから) ジョブ ID を使用してサポートにお問い合わせください。 その他のエラーの詳細:CredentialsInvalid AADSTS50034:ユーザー アカウント {EmailHidden} が skydrive365.onmicrosoft.com ディレクトリに存在しません。 このアプリケーションにサインインするには、そのアカウントがディレクトリに追加されている必要があります。 トレース ID:14b63033-3bc9-4bd4-b871-5eb4b3500200 関連付け ID:57d93ed1-be4d-483c-997c-a3b6f03deb00 タイムスタンプ:2021-01-12 21:08:29Z |このエラーは、同期サービス アカウント ADToAADSyncServiceAccount がテナントに存在しない場合にスローされます。 アカウントが誤って削除されたことが原因である可能性があります。|[Repair-AADCloudSyncToolsAccount](reference-powershell.md#repair-aadcloudsynctoolsaccount) を使用して、サービス アカウントを修復します。|
|AzureActiveDirectoryExpiredCredentials|エラー メッセージ:この時点で、この要求を処理できませんでした。 この問題が解決しない場合は、(構成の状態ウィンドウから) ジョブ ID を使用してサポートにお問い合わせください。 その他のエラーの詳細:CredentialsExpired AADSTS50055:パスワードの有効期限が切れています。 トレース ID:989b1841-dbe5-49c9-ab6c-9aa25f7b0e00 関連付け ID:1c69b196-1c3a-4381-9187-c84747807155 タイムスタンプ:2021-01-12 20:59:31Z | 応答状態コードは成功を示していません:401 (未承認)。|AAD 同期サービス アカウントの資格情報の有効期限が切れています。|クラウド サービス アカウントを修復するには、 https://go.microsoft.com/fwlink/?linkid=2150988 の手順に従ってください。 このエラーが解決しない場合は、(構成の状態ウィンドウから) ジョブ ID を使用してサポートにお問い合わせください。  その他のエラーの詳細:管理 Azure Active Directory テナントの資格情報が、期限切れになった OAuth トークンと交換されました。|
|AzureActiveDirectoryAuthenticationFailed|エラー メッセージ:この時点で、この要求を処理できませんでした。 この問題が解決しない場合は、サポートに連絡し、次のジョブ ID を提供してください。AD2AADProvisioning.60b943e88f234db2b887f8cb91dee87c.707be0d2-c6a9-405d-a3b9-de87761dc3ac. 追加情報:この時点で、この要求を処理できませんでした。 この問題が解決しない場合は、(構成の状態ウィンドウから) ジョブ ID を使用してサポートにお問い合わせください。 その他のエラーの詳細:UnexpectedError.|不明なエラー。|この問題が解決しない場合は、(構成の状態ウィンドウから) ジョブ ID を使用してサポートにお問い合わせください。|

## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)