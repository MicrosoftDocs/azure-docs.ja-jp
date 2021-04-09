---
title: Azure Arc 対応サーバー エージェントの接続に関する問題のトラブルシューティング
description: この記事では、サービスに接続しようとすると Azure Arc 対応サーバーで発生する Connected Machine エージェントの問題のトラブルシューティングと解決を行う方法について説明します。
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 36feb6a65ec52d99dfd664ae54cb099ea6a7e239
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90900687"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>Connected Machine エージェントの接続の問題のトラブルシューティング

この記事では、Windows または Linux 向けの Azure Arc 対応サーバー Connected Machine エージェントを構成しようとすると発生することがある問題のトラブルシューティングと解決を行う方法を提供します。 サービスへの接続を構成するときの対話型インストール手法と大規模インストール手法の両方が含まれます。 全般的な情報については、[Arc 対応サーバーの概要](./overview.md)に関するページを参照してください。

## <a name="agent-verbose-log"></a>エージェントの詳細ログ

この記事の後半で説明するトラブルシューティングの手順に従う前に、必要になる最小限の情報は詳細ログです。 詳細 (-v) 引数が使用されている場合は、**azcmagent** ツール コマンドの出力が含まれます。 ログ ファイルは Windows の場合は `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` に、Linux の場合は `/var/opt/azcmagent/log/azcmagent.log` に書き込まれます。

### <a name="windows"></a>Windows

次は、対話型インストールを実行するとき、Windows 向け Connected Machine エージェントで詳細ログを有効にするためのコマンドの例です。

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

次は、サービス プリンシパルを使用して大規模インストールを実行するとき、Windows 向け Connected Machine エージェントで詳細ログを有効にするためのコマンドの例です。

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

次に、対話型インストールを実行するとき、Linux 向け Connected Machine エージェントで詳細ログを有効にするためのコマンドの例を示します。

>[!NOTE]
>**Azcmagent** を実行するには、Linux マシンに対する *ルート* アクセス許可が必要です。

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

次に、サービス プリンシパルを使用して大規模インストールを実行するとき、Linux 向け Connected Machine エージェントで詳細ログを有効にするためのコマンドの例を示します。

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>サービスへのエージェントの接続の問題

次の表は、既知のエラーとそれらを解決する方法に関する提案をいくつかまとめたものです。

|Message |エラー |考えられる原因 |解決策 |
|--------|------|---------------|---------|
|認証トークン デバイス フローを取得できませんでした |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |`login.windows.net` エンドポイントに到達できません | エンドポイントへの接続を確認します。 |
|認証トークン デバイス フローを取得できませんでした |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |プロキシまたはファイアウォールによって `login.windows.net` エンドポイントへのアクセスがブロックされています。 | エンドポイントへの接続と、それがファイアウォールまたはプロキシ サーバーによってブロックされていないことを確認します。 |
|認証トークン デバイス フローを取得できませんでした  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | グループ ポリシー オブジェクト *[コンピューターの構成]\[管理用テンプレート]\[システム]\[ユーザー プロファイル]\[システム再起動時に指定した日数を経過しているユーザー プロファイルを削除する]* が有効です。 | GPO が有効になっていること、および影響を受けるコンピューターがターゲットになっていることを確認します。 詳細については、脚注 <sup>[1](#footnote1)</sup> を参照してください。 |
|SPN から認証トークンを取得できませんでした |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |プロキシまたはファイアウォールによって `login.windows.net` エンドポイントへのアクセスがブロックされています。 |エンドポイントへの接続と、それがファイアウォールまたはプロキシ サーバーによってブロックされていないことを確認します。 |
|SPN から認証トークンを取得できませんでした |`Invalid client secret is provided` |サービス プリンシパル シークレットが間違っているか、無効です。 |サービス プリンシパル シークレットを確認します。 |
| SPN から認証トークンを取得できませんでした |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |サービス プリンシパルまたはテナント ID が間違っています。 |サービス プリンシパルまたはテナント ID を確認します。|
|ARM リソース応答を取得する |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |資格情報またはアクセス許可が間違っています |本人またはサービス プリンシパルが **Azure Connected Machine のオンボード** ロールに属していることを確認します。 |
|AzcmagentConnect ARM リソースに接続できませんでした |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Azure リソース プロバイダーが登録されていません。 |[リソース プロバイダー](./agent-overview.md#register-azure-resource-providers)を登録します。 |
|AzcmagentConnect ARM リソースに接続できませんでした |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |プロキシ サーバーまたはファイアウォールによって `management.azure.com` エンドポイントへのアクセスがブロックされています。 |エンドポイントへの接続と、それがファイアウォールまたはプロキシ サーバーによってブロックされていないことを確認します。 |

<a name="footnote1"></a><sup>1</sup> この GPO が有効であり、Connected Machine エージェントがあるコンピューターに適用されている場合は、*himds* サービスに対して指定された組み込みアカウントに関連付けられているユーザー プロファイルが削除されます。 その結果、ローカル証明書ストアに 30 日間キャッシュされている、サービスとの通信に使用される認証証明書も削除されます。 30 日間の制限を超える前に、証明書の更新が試行されます。 この問題を解決するには、[コンピューターの登録を解除する](manage-agent.md#unregister-machine)ための手順に従った後、`azcmagent connect` を実行しているサービスに再登録します。

## <a name="next-steps"></a>次のステップ

該当する問題がここにない場合、または問題を解決できない場合は、追加のサポートを受けるために、次のいずれかのチャネルをお試しください。

* [Microsoft Q&A](/answers/topics/azure-arc.html) を通じて、Azure 専門家からの回答を得ることができます。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure サポートにより、Azure コミュニティの回答、サポート、エキスパートと結び付けられます。

* Azure サポート インシデントを送信する。 [Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートを受ける]** を選択します。
