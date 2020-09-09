---
title: Azure Key Vault の証明書の更新について
description: この記事では、Azure Key Vault の証明書を更新する方法について説明します。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 3809fa9e1ce17a5a0c3cf333ac20ef543db4b5a7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588805"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Azure Key Vault の証明書の更新

Azure Key Vault を使用すると、自社ネットワーク用のデジタル証明書のプロビジョニング、管理、およびデプロイを簡単に行うことができ、アプリケーションの通信でセキュリティを確保できます。 証明書の詳細については、「[Azure Key Vault の証明書について](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)」を参照してください。

有効期限の短い証明書を使用したり、証明書のローテーション頻度を増やしたりすることで、不正なユーザーによるアプリケーションへのアクセスを防止する役に立ちます。

この記事では、Azure Key Vault の証明書を更新する方法について説明します。

## <a name="get-notified-about-certificate-expirations"></a>証明書の有効期限を知らせる通知の受け取り
証明書の有効期限が近づいてきたときに通知を受け取るには、次の手順を実行します。

最初に、PowerShell コマンドレット [Add-AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0) を使用して、ご自分のキー コンテナーに証明書の連絡先を追加します。

次に、証明書の有効期限を知らせる通知を受け取るタイミングを構成します。 証明書のライフサイクル属性の構成については、[Key Vault における証明書の自動ローテーションの構成](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)に関する記事を参照してください。

Key Vault には、3 つのカテゴリの証明書があります。
-   DigiCert や GlobalSign など、統合された証明機関 (CA) によって作成される証明書
-   統合されていない CA によって作成される証明書
-   自己署名証明書

## <a name="renew-an-integrated-ca-certificate"></a>統合された CA 証明書の更新 
Azure Key Vault では、Microsoft の信頼された証明機関 (DigiCert および GlobalSign) によって発行された証明書をエンドツーエンドでメンテナンスします。 [信頼された CA と Key Vault を統合する](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority)方法についてご確認ください。

## <a name="renew-a-nonintegrated-ca-certificate"></a>統合されていない CA 証明書の更新 
Azure Key Vault を使用すると、任意の CA から証明書をインポートできます。複数の Azure リソースと統合を行ってデプロイを容易にすることができるので便利です。 ご利用の証明書の有効期限を追跡できなくなることが心配な場合や、もっと悪いことに証明書の有効期限が既に切れているのが判明した場合、最新の状態を維持するのにキー コンテナーが役に立ちます。 統合されていない CA 証明書の場合、有効期限が近いことを知らせるメール通知をキー コンテナーで設定できます。 このような通知は、複数のユーザーに対して設定することもできます。

> [!IMPORTANT]
> 証明書はバージョン管理されるオブジェクトです。 現在のバージョンの有効期限が近づいている場合は、新しいバージョンを作成する必要があります。 概念的には、新しい各バージョンは、キーとそのキーを ID に関連付ける BLOB で構成された新しい証明書です。 連携していない CA を使用すると、キー コンテナーによってキーと値のペアが生成され、証明書署名要求 (CSR) が返されます。

統合されていない CA 証明書を更新するには、次の手順を実行します。

1. Azure portal にサインインし、更新したい証明書を開きます。
1. 証明書のペインで、 **[新しいバージョン]** を選択します。
1. **[証明書の操作]** を選択します。
1. **[CSR のダウンロード]** を選択して、CSR ファイルを自分のローカル ドライブにダウンロードします。
1. 選択した CA に CSR を送信して要求に署名します。
1. 署名した要求を戻し、同じ [証明書の操作] ペインで **[Merge CSR]\(CSR のマージ\)** を選択します。

> [!NOTE]
> 署名した CSR を、自分が作成したのと同じ CSR 要求にマージすることが重要です。 そうしないと、キーが一致しません。

新しい CSR の作成の詳細については、[Key Vault での CSR の作成とマージ]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal)に関するページを参照してください。

## <a name="renew-a-self-signed-certificate"></a>自己署名証明書を更新する

Azure Key Vault は、自己署名証明書の自動更新にも対応しています。 発行ポリシーの変更と、証明書のライフサイクル属性の更新については、[Key Vault における証明書の自動ローテーションの構成](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)に関するページを参照してください。

## <a name="troubleshoot"></a>トラブルシューティング
発行された証明書が Azure portal で "*無効*" 状態になっている場合は、 **[証明書の操作]** に移動して、その証明書のエラー メッセージを確認します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**証明書の自動ローテーション機能をテストするにはどうすればよいですか?**

**1 か月**の有効期限で証明書を作成してから、ローテーションの有効期間アクションを **1%** に設定します。 この設定にすると、証明書は 7.2 時間ごとにローテーションされます。
  
**証明書の自動更新後にタグはレプリケートされますか?**

はい。タグは自動更新後にレプリケートされます。

## <a name="next-steps"></a>次の手順
*   [Key Vault と DigiCert 証明機関の統合](how-to-integrate-certificate-authority.md)
*   [チュートリアル:Key Vault における証明書の自動ローテーションを構成する](tutorial-rotate-certificates.md)
