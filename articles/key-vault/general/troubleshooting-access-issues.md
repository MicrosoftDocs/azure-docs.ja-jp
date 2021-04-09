---
title: Azure Key Vault のアクセス ポリシーの問題のトラブルシューティング
description: Azure Key Vault のアクセス ポリシーの問題のトラブルシューティング
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: c5fab8b856ff9c82a0de887dc9c322dbf541348b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791409"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Azure Key Vault のアクセス ポリシーの問題のトラブルシューティング

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="i-am-not-able-to-list-or-get-secretskeyscertificate-i-am-seeing-something-went-wrong-error"></a>シークレット、キー、または証明書を一覧表示または取得できません。 "問題が発生しました.." と表示されています。エラーが発生します。
シークレットの表示、取得、作成、またはアクセスに関して問題が発生した場合は、その操作を実行するためのアクセス ポリシーが定義されていることを確認してください。[キー コンテナー アクセス ポリシー](./assign-access-policy-cli.md)

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>キー コンテナーが、いつ、どのようにアクセスされているのかを確認するにはどうすればよいですか?

1 つまたは複数のキー コンテナーを作成したら、いつ、どのように、誰によってキー コンテナーがアクセスされるのかを監視するのが一般的です。 Azure Key Vault のログを有効にすることによって、監視を行うことができます。ログを有効にするためのステップバイステップ ガイドについては、[こちら](./logging.md)をご覧ください。

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>キー コンテナーの可用性、サービスの待ち時間、または他のパフォーマンス メトリックを監視するにはどうすればよいですか?

サービスのスケーリングを開始すると、キー コンテナーに送信される要求の数が増加します。 それにより要求の待機時間が長くなる可能性があり、極端な場合には要求がスロットルされてサービスのパフォーマンスに影響を与えます。 キー コンテナーのパフォーマンス メトリックを監視し、特定のしきい値についてアラートを受け取ることができます。監視を構成するためのステップバイステップ ガイドについては、[こちら](./alert.md)をご覧ください。

### <a name="i-am-not-able-to-modify-access-policy-how-can-it-be-enabled"></a>アクセス ポリシーを変更できません。どのような方法で有効にできますか。
アクセス ポリシーを変更するために必要な AAD アクセス許可がユーザーに与えられている必要があります。 この場合、上位の共同作成者ロールをユーザーに与える必要があります。

### <a name="i-am-seeing-unknown-policy-error-what-does-that-mean"></a>"Unknown Policy (不明なポリシー)" というエラーが表示されます。 これはどういう意味ですか。
[不明] セクションにアクセス ポリシーが表示されることには 2 つの異なる可能性があります。
* 前のユーザーにアクセスが与えられていた可能性がありますが、何らかの理由でそのユーザーが存在しません。
* アクセス ポリシーが PowerShell 経由で追加されるとき、サービス プリンシパルではなく、アプリケーション オブジェクト ID に対してアクセス ポリシーが追加されます。

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>キー コンテナー オブジェクトごとにアクセス制御を割り当てるにはどうすればよいですか? 

シークレット、キー、または証明書ごとのアクセス制御機能の提供状況が通知されます。詳細については、[こちら](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/32213176-per-secret-key-certificate-access-control)をご覧ください。

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>アクセス制御ポリシーを使用して Key Vault の認証を提供するにはどうすればよいですか?

Key Vault に対してクラウドベースのアプリケーションの認証を行うための最も簡単な方法は、マネージド ID を使用することです。詳細については、「[Azure Key Vault に対する認証](authentication.md)」を参照してください。
オンプレミス アプリケーションを作成する場合、ローカル開発を行う場合、またはマネージド ID を使用できない場合は、代わりにサービス プリンシパルを手動で登録し、アクセス制御ポリシーを使用してキー コンテナーへのアクセスを提供することができます。 [アクセス制御ポリシーの割り当て](assign-access-policy-portal.md)に関する記事を参照してください。

### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>AD グループにキー コンテナーへのアクセス権を付与するにはどうすればよいですか?

Azure CLI の `az keyvault set-policy` コマンド、または Azure PowerShell の Set-AzKeyVaultAccessPolicy コマンドレットを使用して、AD グループにキー コンテナーに対するアクセス許可を付与します。 [CLI でのアクセス ポリシーの割り当て](assign-access-policy-cli.md)および [PowerShell でのアクセス ポリシーの割り当て](assign-access-policy-powershell.md)に関する記事を参照してください。

またアプリケーションには、キー コンテナーに割り当てられた IAM (Identity and Access Management: ID とアクセス管理) ロールが少なくとも 1 つ必要となります。 それがないとログインすることができず、サブスクリプションにアクセスする権限の不足でエラーになります。 マネージド ID を持つ Azure AD グループでは、トークンを更新して有効になるまでに、最大で 8 時間かかる場合があります。

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>既存のアクセス ポリシーを削除せずに、ARM テンプレートを使用して Key Vault を再デプロイするにはどうすればよいですか?

現在、Key Vault の再デプロイでは、Key Vault のアクセス ポリシーが削除され、ARM テンプレートのアクセス ポリシーに置き換えられます。 Key Vault のアクセス ポリシーに増分オプションはありません。 Key Vault のアクセス ポリシーを保持するには、Key Vault の既存のアクセス ポリシーを読み取り、ARM テンプレートにそれらのポリシーを設定して、アクセスの停止を回避する必要があります。

このシナリオに役立つ可能性があるもう 1 つの選択肢は、アクセス ポリシーの代替として Azure RBAC とロールを利用することです。 Azure RBAC を使用すると、ポリシーを再度指定しなくても、キー コンテナーを再デプロイできます。 このソリューションの詳細は[こちら](./rbac-guide.md)でご覧いただけます。

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>次のエラーの種類に対して推奨されるトラブルシューティングの手順

* HTTP 401:認証されていない要求 - [トラブルシューティングの手順](rest-error-codes.md#http-401-unauthenticated-request)
* HTTP 403:アクセス許可が不十分 - [トラブルシューティングの手順](rest-error-codes.md#http-403-insufficient-permissions)
* HTTP 429:要求が多すぎる - [トラブルシューティングの手順](rest-error-codes.md#http-429-too-many-requests)
* キー コンテナーに対する削除アクセス許可があるかどうかを確認します。[CLI でのアクセス ポリシーの割り当て](assign-access-policy-cli.md)、[PowerShell でのアクセス ポリシーの割り当て](assign-access-policy-powershell.md)、または[ポータルでのアクセス ポリシーの割り当て](assign-access-policy-portal.md)に関する記事を参照してください。
* コードでキー コンテナーへの認証に問題がある場合は[認証 SDK](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html) を使用してください

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>キー コンテナーが調整されているときに実装する必要があるベスト プラクティスは何ですか?
[こちら](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)に記載されているベスト プラクティスに従ってください。

## <a name="next-steps"></a>次の手順

キー コンテナー認証エラーのトラブルシューティング方法について学習します: [Key Vault トラブルシューティング ガイド](rest-error-codes.md)