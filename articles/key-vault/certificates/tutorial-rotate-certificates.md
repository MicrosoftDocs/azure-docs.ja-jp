---
title: チュートリアル - Key Vault における証明書の自動ローテーション頻度を更新する | Microsoft Docs
description: Azure portal を使用して、Azure Key Vault における証明書の自動ローテーション頻度を更新する方法について紹介するチュートリアル。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: eeceb1279579055bfff33f0a4413f0798418faed
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201507"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>チュートリアル:Key Vault における証明書の自動ローテーションを構成する

Azure Key Vault を使用すると、デジタル証明書のプロビジョニング、管理、およびデプロイを簡単に行うことができます。 証明機関 (CA) によって署名された、パブリックおよびプライベートの Secure Sockets Layer (SSL)/Transport Layer Security (TLS) 証明書、または自己署名証明書を使用できます。 Key Vault は、CA とのパートナーシップを通じて証明書を要求したり更新したりすることもでき、証明書のライフサイクル管理のための堅牢なソリューションを実現します。 このチュートリアルでは、証明書の有効期間、自動ローテーション頻度、および CA 属性を更新します。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * Azure portal を使用して証明書を管理する。
> * CA プロバイダー アカウントを追加する。
> * 証明書の有効期間を更新する。
> * 証明書の自動ローテーション頻度を更新する。
> * Azure PowerShell を使用して証明書の属性を更新する。

始める前に、[Key Vault の基本的な概念](../general/basic-concepts.md)を確認してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-vault"></a>コンテナーの作成

操作を実行するためのキー コンテナーを作成するか、既存のコンテナーを選択します ([キー コンテナーを作成する手順](../quick-create-portal.md)に関するページを参照してください)。 この例では、**Example-Vault** というキー コンテナー名を使用します。

![キー コンテナーの作成が完了した後の出力](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Key Vault に証明書を作成する

証明書を作成するか、証明書をキー コンテナーにインポートします ([Key Vault で証明書を作成する手順](../quick-create-portal.md)に関するページを参照してください)。 この例では、**ExampleCertificate** という証明書を操作します。

## <a name="update-certificate-lifecycle-attributes"></a>証明書のライフサイクル属性を更新する

Azure Key Vault では、証明書の作成前と作成後の両方のタイミングで証明書のライフサイクル属性を更新できます。

Key Vault には、次の証明書を作成できます。

- 自己署名証明書。
- Key Vault と提携している CA で作成された証明書。
- Key Vault と提携していない CA による証明書。

次の CA は、現在 Key Vault と提携しているプロバイダーです。

- DigiCert:Key Vault は、OV TLS/SSL 証明書を提供します。
- GlobalSign:Key Vault は、OV TLS/SSL 証明書を提供します。

Key Vault では、CA との確立されたパートナーシップを通じて証明書が自動的にローテーションされます。 Key Vault はパートナーシップを通じて証明書を自動的に要求および更新するため、Key Vault と提携していない CA で作成された証明書には、自動ローテーション機能が適用されません。

> [!NOTE]
> CA プロバイダーのアカウント管理者は、Key Vault が TLS/SSL 証明書を作成、更新、および使用するために使用する資格情報を作成します。
![証明機関](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>作成時に証明書のライフサイクル属性を更新する

1. Key Vault のプロパティ ページで、 **[証明書]** を選択します。
1. **[Generate/Import]\(生成/インポート\)** を選択します。
1. **[証明書の作成]** 画面で、次の値を更新します。

   - **有効期間**: 月単位の値を入力します。 セキュリティ プラクティスとして、作成する証明書の有効期間は短くすることをお勧めします。 新しく作成された証明書の有効期間は、既定では 12 か月です。
   - **有効期間のアクション タイプ**: 証明書の自動更新とアラート アクションを選択し、 **[有効期間の割合]** または **[有効期限までの日数]** を更新します。 既定では、証明書の自動更新が、その有効期間の 80% に設定されます。 ドロップダウン メニューから次のいずれかのオプションを選択します。

        |  特定のタイミングで自動的に更新する| 特定のタイミングですべての連絡先にメールを送信する |
        |-----------|------|
        |このオプションを選択すると、自動ローテーションが "*オンになります*"。 | このオプションを選択すると、自動ローテーションが "*実行されません*"。連絡先にアラートが送信されるだけです。|

1. **［作成］** を選択します

![証明書のライフサイクル](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>格納されている証明書のライフサイクル属性を更新する

1. キー コンテナーを選択します。
1. Key Vault のプロパティ ページで、 **[証明書]** を選択します。
1. 更新する証明書を選択します。 この例では、**ExampleCertificate** という証明書を操作します。
1. 上部のメニュー バーから **[発行ポリシー]** を選択します。

   ![証明書のプロパティ](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. **[発行ポリシー]** 画面で、次の値を更新します。

   - **有効期間**: 月単位の値を更新します。
   - **有効期間のアクション タイプ**: 証明書の自動更新とアラート アクションを選択し、 **[有効期間の割合]** または **[有効期限までの日数]** を更新します。

   ![証明書のプロパティ](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. **[保存]** を選択します。

> [!IMPORTANT]
> 証明書の有効期間のアクション タイプを変更すると、既存の証明書の変更がすぐに記録されます。


### <a name="update-certificate-attributes-by-using-powershell"></a>PowerShell を使用して証明書の属性を更新する

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> 一連の証明書に対する更新ポリシーを変更するには、次の例のように `VaultName,CertName` が含まれた `File.csv` を入力します。
> <br/>
 `vault1,Cert1` <br/>
>  `vault2,Cert2`
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
パラメーターの詳細については、[az keyvault certificate](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes) に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Key Vault に関する他のチュートリアルは、このチュートリアルに基づいています。 これらのチュートリアルを実行する場合は、これらの既存のリソースをそのまま残しておくことをお勧めします。
不要になったら、リソース グループを削除します。これにより、キー コンテナーと関連リソースが削除されます。

ポータルを使用してリソース グループを削除するには:

1. ポータルの上部にある**検索**ボックスにリソース グループの名前を入力します。 このクイックスタートで使用されているリソース グループが検索結果に表示されたら、それを選択します。
1. **[リソース グループの削除]** を選択します。
1. **[リソース グループ名を入力してください:]** ボックスにリソース グループの名前を入力し、 **[削除]** を選択します。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、証明書のライフサイクル属性を更新しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault での証明書作成の管理](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)の詳細をご覧ください。
- [Key Vault の概要](../general/overview.md)を確認してください。