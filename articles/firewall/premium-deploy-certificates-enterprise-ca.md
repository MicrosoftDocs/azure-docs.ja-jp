---
title: Azure Firewall Premium プレビュー用にエンタープライズ CA 証明書をデプロイおよび構成する
description: Azure Firewall Premium プレビュー用にエンタープライズ CA 証明書をデプロイおよび構成する方法について説明します。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 03/09/2021
ms.author: victorh
ms.openlocfilehash: fba95214a6bbb0482166eab8f77f30911986fbb7
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102525455"
---
# <a name="deploy-and-configure-enterprise-ca-certificates-for-azure-firewall-preview"></a>Azure Firewall プレビュー用にエンタープライズ CA 証明書をデプロイおよび構成する

> [!IMPORTANT]
> 現在、Azure Firewall Premium はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


Azure Firewall Premium プレビューには、証明書認証チェーンを必要とする TLS 検査機能が含まれています。 運用環境のデプロイでは、エンタープライズ PKI を使用して Azure Firewall Premium で使用する証明書を生成する必要があります。 この記事を使用して、Azure Firewall Premium プレビュー用の中間 CA 証明書を作成して管理します。

Azure Firewall Premium プレビューで使用される証明書に関する詳細については、「[Azure Firewall Premium プレビューの証明書](premium-certificates.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

エンタープライズ CA を使用して Azure Firewall Premium プレビューで使用する証明書を生成するには、次のリソースが必要です。 

- Active Directory フォレスト 
- Web 登録が有効になっている Active Directory 証明書サービスのルート CA 
- Premium レベルのファイアウォール ポリシーを使用した Premium Azure Firewall 
- Azure Key Vault 
- Key Vault アクセス ポリシーで定義されている "**証明書とシークレット**" に対する読み取りアクセス許可を持つマネージド ID 

## <a name="request-and-export-a-certificate"></a>証明書の要求とエクスポート

1. ルート CA の Web 登録サイト (通常は `https://<servername>/certsrv`) にアクセスし、 **[証明書の要求]** を選択します。
1. **[証明書の要求の詳細設定]** を選択します。
1. **[この CA への要求を作成し送信する]** リンクを選択します。
1. 下位証明機関テンプレートを使用して、示されているようにフォームに入力します。
1. 要求を送信し、証明書をインストールします。
1. この要求が Internet Explorer を使用して Windows Server から行われた場合は、 **[インターネット オプション]** を開きます。
1. **[コンテンツ]** タブに移動し、 **[証明書]** を選択します。
1. 発行したばかりの証明書を選択し、 **[エクスポート]** を選択します。
1. **[次へ]** を選択してウィザードを開始します。 **[はい、秘密キーをエクスポートします]** を選択し、 **[次へ]** を選択します。
1. 既定では、.pfx ファイル形式が選択されています。 **[可能であれば、証明書パスにあるすべての証明書を含む]** をオフにします。 証明書チェーン全体をエクスポートすると、Azure Firewall へのインポート処理が失敗します。
1. キーを保護するためのパスワードを割り当てて確認し、 **[次へ]** を選択します。
1. ファイル名とエクスポート先を選択し、 **[次へ]** を選択します。
1. **[完了]** を選択し、エクスポートした証明書を安全な場所に移動します。

## <a name="add-the-certificate-to-a-firewall-policy"></a>証明書をファイアウォール ポリシーに追加する

1. Azure portal で、Key Vault の [証明書] ページに移動し、 **[生成/インポート]** を選択します。
1. 作成方法として **[インポート]** を選択し、証明書に名前を付け、エクスポートした .pfx ファイルを選択して、パスワードを入力し、 **[作成]** を選択します。
1. ファイアウォール ポリシーの **[TLS 検査 (プレビュー)]** ページに移動し、マネージド ID、Key Vault、および証明書を選択します。 
1. **[保存]** を選択します。
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/tls-inspection.png" alt-text="TLS インスペクション":::

## <a name="validate-tls-inspection"></a>TLS 検査の検証

1. 選択した宛先 URL または FQDN に対する TLS 検査を使用して、アプリケーション ルールを作成します。  (例: `*bing.com`)。
1. ルールのソース範囲内にあるドメイン参加済みのマシンから、宛先に移動し、ブラウザーのアドレス バーの横にあるロック記号を選択します。 証明書には、パブリック CA ではなく、エンタープライズ CA によって発行されたことが表示されます。
1. 証明書を表示して、証明書のパスなどの詳細情報を表示します。
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/certificate-details.png" alt-text="証明書の詳細":::
1. Log Analytics で、次の KQL クエリを実行して、TLS 検査の対象となったすべての要求を返します。
   ```
   AzureDiagnostics 
   | where ResourceType == "AZUREFIREWALLS" 
   | where Category == "AzureFirewallApplicationRule" 
   | where msg_s contains "Url:" 
   | sort by TimeGenerated desc
   ```
   結果には、検査されたトラフィックの完全な URL が表示されます。:::image type="content" source="media/premium-deploy-certificates-enterprise-ca/kql-query.png" alt-text="KQL クエリ":::

## <a name="next-steps"></a>次のステップ

[Azure portal での Azure Firewall Premium プレビュー](premium-portal.md)
