---
title: チュートリアル:Azure CDN カスタム ドメインで HTTPS を構成する
description: このチュートリアルでは、Azure CDN エンドポイント カスタム ドメインで HTTPS を有効および無効にする方法について説明します。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 275afc504a5e7b92ae3274c02372eee6b488c782
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616402"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>チュートリアル:Azure CDN カスタム ドメインで HTTPS を構成する

このチュートリアルでは、Azure CDN エンドポイントに関連付けられたカスタム ドメインの HTTPS プロトコルを有効にする方法について説明します。 

カスタム ドメイン (例: https:\//www.contoso.com) に HTTPS プロトコルを使用すると、機密データが TLS/SSL でセキュリティ保護されて配信されます。 Web ブラウザーは、HTTPS 経由で接続されている場合、Web サイトの証明書を検証します。 ブラウザーはそれが正当な証明機関によって発行されていることを確認します。 このプロセスによりセキュリティを確保し、Web アプリケーションを攻撃から保護します。

既定では、Azure CDN は、CDN エンドポイント ホスト名で HTTPS をサポートしています。 たとえば、CDN エンドポイント (例: https:\//contoso.azureedge.net) を作成すると、HTTPS が自動的に有効になります。  

カスタム HTTPS の機能の主な特性は次のとおりです。

- 追加コストなし: 証明書の取得または更新のコストや、HTTPS トラフィックの追加コストが発生しません。 CDN からの GB 送信のみ課金されます。

- シンプルな有効化: [Azure portal](https://portal.azure.com) からワン クリックのプロビジョニングを利用できます。 REST API やその他の開発者ツールを使用して機能を有効にすることもできます。

- 証明書の完全な管理:  
    * すべての証明書の調達と管理がユーザーに代わって実施されます。 
    * 証明書は自動的にプロビジョニングされ、有効期限が切れる前に更新されます。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - カスタム ドメインで HTTPS プロトコルを有効にする。
> - CDN で管理された証明書を使用する 
> - 独自の証明書を使用する
> - ドメインを検証する
> - カスタム ドメインで HTTPS プロトコルを無効にする。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

このチュートリアルの手順を完了するには、最初に CDN プロファイルと少なくとも 1 つの CDN エンドポイントを作成しておいてください。 詳細については、「[クイック スタート: Azure CDN プロファイルとエンドポイントの作成](cdn-create-new-endpoint.md)」を参照してください。

CDN エンドポイントに Azure CDN カスタム ドメインを関連付けます。 詳細については、「[チュートリアル:カスタム ドメインを Azure CDN エンドポイントに追加します](cdn-map-content-to-custom-domain.md)。

> [!IMPORTANT]
> CDN マネージド証明書は、ルートまたは頂点のドメインには使用できません。 Azure CDN カスタム ドメインがルートまたは頂点のドメインの場合、独自の証明書の持ち込み機能を使用する必要があります。 
>

---

## <a name="tlsssl-certificates"></a>TLS/SSL 証明書

Azure CDN カスタム ドメインで HTTPS を有効にするには、TLS/SSL 証明書を使用します。 Azure CDN で管理された証明書と独自の証明書のどちらを使用するかを選択できます。

# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[オプション 1 (既定): CDN で管理された証明書を使用して HTTPS を有効にする](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Azure CDN は、調達や更新などの証明書管理タスクを処理します。 この機能を有効にすると、プロセスがすぐに開始します。 

カスタム ドメインが既に CDN エンドポイントにマップされている場合、これ以上のアクションは必要ありません。 Azure CDN によって手順が処理され、要求が自動的に完了します。

カスタム ドメインが別の場所でマップされている場合は、メールを使用してドメインの所有権を検証します。

カスタム ドメインで HTTPS を有効にするには、次の手順のようにします。

1. [Azure portal](https://portal.azure.com) に移動し、Azure CDN によって管理されている証明書を検索します。 **CDN のプロファイル** を検索して選択します。 

2. 自分のプロファイルを選択します。
    * **Azure CDN Standard from Microsoft**
    * **Azure CDN Standard from Akamai**
    * **Azure CDN Standard from Verizon**
    * **Azure CDN Premium from Verizon**

3. CDN エンドポイントの一覧で、カスタム ドメインが含まれているエンドポイントを選択します。

    ![エンドポイントの一覧](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    **[エンドポイント]** ページが表示されます。

4. カスタム ドメインの一覧で、HTTPS を有効にするカスタム ドメインを選択します。

    ![自分の証明書を使用するオプションを含んだ [カスタム ドメイン] ページを示すスクリーンショット。](./media/cdn-custom-ssl/cdn-custom-domain.png)

    **[カスタム ドメイン]** ページが表示されます。

5. [証明書の管理の種類] で、 **[CDN 管理]** を選択します。

6. **[オン]** を選択して HTTPS を有効にします。

    ![カスタム ドメイン HTTPS ステータス](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. 「[ドメインを検証する](#validate-the-domain)」に進みます。


# <a name="option-2-enable-https-with-your-own-certificate"></a>[オプション 2: 独自の証明書を使用して HTTPS を有効にする](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> このオプションは、**Azure CDN from Microsoft** および **Azure CDN from Verizon** プロファイルでのみ利用できます。 
>
 
独自の証明書を使用して、HTTPS 機能を有効にできます。 このプロセスは、Azure Key Vault との統合を通じて行われます。これにより、お使いの証明書を安全に格納できます。 Azure CDN では、お使いの証明書の取得に、セキュリティで保護されたこのメカニズムが使用されます。それには、追加の手順がいくつか必要です。 TLS/SSL 証明書を作成するときには、許可された証明機関 (CA) を使用して作成する必要があります。 許可されていない CA を使用すると、要求が拒否されます。 許可された CA の一覧については、「[Allowed certificate authorities for enabling custom HTTPS on Azure CDN](cdn-troubleshoot-allowed-ca.md)」(Azure CDN でカスタム HTTPS を有効にするために許可された認証機関) を参照してください。 **Azure CDN from Verizon** の場合には、有効な CA であればどれでも利用できます。 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Azure Key Vault のアカウントと証明書を準備する
 
1. Azure Key Vault: カスタム HTTPS を有効にする Azure CDN プロファイルおよび CDN エンドポイントと同じサブスクリプションで、Azure Key Vault アカウントを実行している必要があります。 Azure Key Vault アカウントがない場合は作成します。
 
2. Azure Key Vault 証明書: 証明書を既にお持ちの場合には、Azure Key Vault アカウントに直接アップロードします。 証明書をまだお持ちでない場合には、Azure Key Vault から直接新しい証明書を作成します。

### <a name="register-azure-cdn"></a>Azure CDN を登録する

PowerShell を使用して、Azure Active Directory に Azure CDN をアプリとして登録します。

1. 必要があれば、ローカル コンピューターに [Azure PowerShell](/powershell/azure/install-az-ps) をインストールします。

2. PowerShell で次のコマンドを実行します。

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`
    > [!NOTE]
    > **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** は、**Microsoft.AzureFrontDoor-Cdn** のサービス プリンシパルです。

    ```bash  
    New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"

    Secret                : 
    ServicePrincipalNames : {205478c0-bd83-4e1b-a9d6-db63a3e1e1c8, 
                                https://microsoft.onmicrosoft.com/033ce1c9-f832-4658-b024-ef1cbea108b8}
    ApplicationId         : 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8
    ObjectType            : ServicePrincipal
    DisplayName           : Microsoft.AzureFrontDoor-Cdn
    Id                    : c87be08f-686a-4d9f-8ef8-64707dbd413e
    Type                  :
    ```
### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Azure CDN にお使いのキー コンテナーへのアクセス権を付与する
 
Azure Key Vault アカウント内の証明書 (シークレット) にアクセスするには、Azure CDN のアクセス許可を付与します。

1. **[設定]** セクションに表示されているキー コンテナーで、 **[アクセス ポリシー]** を選択します。 右側のペインで、 **[+ アクセス ポリシーの追加]** を選択します。

    :::image type="content" source="./media/cdn-custom-ssl/cdn-new-access-policy.png" alt-text="CDN 用のキーコンテナーのアクセス ポリシーを作成する" border="true":::

2. **[アクセス ポリシーの追加]** ページで、 **[プリンシパルの選択]** の隣にある **[選択されていません]** を選択します。 **[プリンシパル]** ページで「**205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**」と入力します。 **[Microsoft.AzureFrontdoor-Cdn]** を選択します。  **[選択]** を選択します。

2. **[プリンシパルの選択]** で、**205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** を検索し、 **[Microsoft.AzureFrontDoor-Cdn]** を選択します。 **[選択]** を選択します。

    :::image type="content" source="./media/cdn-custom-ssl/cdn-access-policy-settings.png" alt-text="Azure CDN のサービス プリンシパルを選択する" border="true":::
    
3. **[証明書のアクセス許可]** を選択します。 **[取得]** と **[一覧]** のチェック ボックスをオンにします。これにより、CDN に証明書の取得および一覧表示のためのアクセス許可が付与されます。

4. **[シークレットのアクセス許可]** を選択します。 **[取得]** と **[一覧]** のチェック ボックスをオンにします。これにより、CDN にシークレットの取得および一覧表示のためのアクセス許可が付与されます。

    :::image type="content" source="./media/cdn-custom-ssl/cdn-vault-permissions.png" alt-text="キーコンテナーに対する CDN のアクセス許可を選択する" border="true":::

5. **[追加]** を選択します。 

> [!NOTE]
> Azure CDN は、このキー コンテナーと、このキー コンテナーに格納されている証明書 (シークレット) にアクセスできるようになりました。 このサブスクリプションに作成されたすべての CDN インスタンスは、このキー コンテナー内の証明書にアクセスできます。 

 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>デプロイする Azure CDN の証明書を選択する
 
1. Azure CDN ポータルに戻り、プロファイルおよびカスタム HTTPS を有効にする CDN エンドポイントを選択します。 

2. カスタム ドメインの一覧で、HTTPS を有効にするカスタム ドメインを選択します。

    **[カスタム ドメイン]** ページが表示されます。

3. [証明書の管理の種類] で、 **[Use my own certificate]\(独自の証明書を使用する\)** を選択します。 

    ![証明書の構成](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. キー コンテナー、証明書 (シークレット)、証明書のバージョンを選択します。

    Azure CDN に次の情報が一覧表示されます。 
    - サブスクリプション ID に対するキー コンテナー アカウント。 
    - 選択したキー コンテナーの下の証明書 (シークレット)。 
    - 利用可能な証明書バージョン。 
 
5. **[オン]** を選択して HTTPS を有効にします。
  
6. 独自の証明書を使用する場合には、ドメインの検証は必要ありません。 「[伝達を待機する](#wait-for-propagation)」に進んでください。

---

## <a name="validate-the-domain"></a>ドメインを検証する

CNAME レコードでカスタム エンドポイントにマップされた使用中のカスタム ドメインがある場合、または独自の証明書を使用している場合には、[CDN エンドポイントにマップされているカスタム ドメイン](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record)に関するセクションに進んでください。 

そうではなく、エンドポイントの CNAME レコード エントリがもう存在しない場合、またはそこに cdnverify サブドメインが含まれている場合は、「[カスタム ドメインが CDN エンドポイントにマップされていない](#custom-domain-isnt-mapped-to-your-cdn-endpoint)」に進んでください。

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>カスタム ドメインが CNAME レコードによって CDN エンドポイントにマップされている

カスタム ドメインをエンドポイントに追加するときに、CDN エンドポイントのホスト名にマッピングする CNAME レコードを DNS ドメイン レジストラーに作成しました。 

この CNAME レコードがまだ存在し、そこに cdnverify サブドメインが含まれていない場合は、DigiCert CA は、これを使用して自動でカスタム ドメインの所有権を検証します。 

独自の証明書を使用している場合には、ドメインの検証は必要ありません。

CNAME レコードは、次の形式にする必要があります。

* "*名前*" はカスタム ドメイン名です。
* "*値*" は CDN エンドポイントのホスト名です。

| 名前            | 種類  | 値                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

CNAME レコードの詳細については、[CNAME DNS レコードの作成](./cdn-map-content-to-custom-domain.md)に関するセクションを参照してください。

CNAME レコードが正しい形式である場合、DigiCert は自動的にそのカスタム ドメイン名を検証し、ご利用のドメインに使用する証明書を作成します。 DigiCert から検証電子メールが送信されないため、要求を承認する必要はありません。 この証明書は 1 年間有効で、有効期限が切れる前に自動更新されます。 「[伝達を待機する](#wait-for-propagation)」に進んでください。 

自動検証には通常、数時間かかります。 24 時間以内にドメインが確認されない場合は、サポート チケットを開いてください。

>[!NOTE]
>Certificate Authority Authorization (CAA) レコードに DNS プロバイダーが登録されている場合、そこには有効な CA として DigiCert が含まれている必要があります。 ドメイン所有者は CAA レコードで、その DNS プロバイダーとともに、ドメインの証明書を発行する権限のある CA を指定できます。 CA は、ドメインの証明書の依頼を受け取っても、そのドメインに CAA レコードがあり、そこに認証された発行者としてその CA がリストされていない場合は、そのドメインまたはサブドメインへの証明書の発行が禁じられます。 CAA レコードの管理については、「[Manage CAA records](https://support.dnsimple.com/articles/manage-caa-record/)」(CAA レコードを管理する) をご覧ください。 CAA レコード ツールについては、「[CAA Record Helper](https://sslmate.com/caa/)」(CAA レコード ヘルパー) をご覧ください。

### <a name="custom-domain-isnt-mapped-to-your-cdn-endpoint"></a>カスタム ドメインが CDN エンドポイントにマップされていない

>[!NOTE]
>**Azure CDN from Akamai** を使用している場合は、次の CNAME を、自動ドメイン検証が有効になるように設定する必要があります。 "_acme-challenge.&lt;custom domain hostname&gt; -> CNAME -> &lt;custom domain hostname&gt;.ak-acme-challenge.azureedge.net"

CNAME レコード エントリに cdnverify サブドメインが含まれている場合は、この手順の残りの部分に従ってください。

DigiCert は、次のメール アドレスに確認メールを送信します。 次のアドレスのいずれかから直接承認できることを確認してください。

* **admin@your-domain-name.com** 
* **administrator@your-domain-name.com** 
* **webmaster@your-domain-name.com** 
* **hostmaster@your-domain-name.com**  
* **postmaster@your-domain-name.com**  

数分以内に、要求の承認を求めるメールを受け取ります。 スパム フィルターを使っている場合は、verification@digicert.com をその許可リストに追加してください。 24 時間以内にメールが届かない場合は、Microsoft のサポートに問い合わせてください。
    
![ドメイン検証メール](./media/cdn-custom-ssl/domain-validation-email.png)

承認リンクを選択すると、次のオンライン承認フォームが表示されます。 
    
![ドメイン検証フォーム](./media/cdn-custom-ssl/domain-validation-form.png)

フォームの指示に従います。2 つの検証オプションがあります。

- contoso.com などの同じルート ドメインに対して同じアカウントを使って、今後行われるすべての依頼を承認することができます。 同じルート ドメインの他のカスタム ドメインを追加する予定の場合は、このアプローチを使用することをお勧めします。

- この要求で使われる特定のホスト名のみを承認できます。 その後の要求では追加の承認が必要になります。

承認後、カスタム ドメイン名に使用される証明書の作成が完了します。 この証明書は 1 年間有効で、有効期限が切れる前に自動更新されます。

## <a name="wait-for-propagation"></a>伝達を待機する

ドメイン名の検証後、カスタム ドメインの HTTPS 機能がアクティブになるまでに最大 6 ～ 8 時間がかかります。 プロセスが完了したら、Azure portal の [カスタム HTTPS] の状態が **[有効]** に変更されます。 [カスタム ドメイン] ダイアログの 4 つの操作ステップが完了とマークされます。 カスタム ドメインは、HTTPS を使う準備ができました。

![HTTPS ダイアログを有効にする](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>操作の進行

次の表は、HTTPS を有効にするときの操作の進行を示したものです。 HTTPS を有効にした後、[カスタム ドメイン] ダイアログには 4 つの操作ステップが表示されます。 各ステップがアクティブになると、進行状況に合わせてステップの下に他のサブステップの詳細が表示されます。 これらのサブステップのすべてが発生するわけではありません。 ステップが正常に完了すると、横に緑色のチェック マークが表示されます。 

| 操作ステップ | 操作サブステップの詳細 | 
| --- | --- |
| 1 要求の送信 | 要求を送信しています |
| | HTTPS 要求を送信しています。 |
| | HTTPS 要求を正常に送信しました。 |
| 2 ドメインの検証 | CNAME で CDN エンドポイントにマップされている場合、ドメインは自動的に検証されます。 そうでない場合、登録レコードに記載されているメール アドレス (WHOIS 登録者) に、検証要求が送信されます。|
| | ドメインの所有権が正常に検証されました。 |
| | ドメインの所有権の検証要求が期限切れになりました (お客様から 6 日以内に返答がなかったようです)。 ドメインで HTTPS が有効になることはありません。 * |
| | ドメインの所有権の検証要求が、お客様により拒否されました。 ドメインで HTTPS が有効になることはありません。 * |
| 3 証明書のプロビジョニング | 証明機関は現在、ドメインで HTTPS を有効にする際に必要な証明書の発行処理を進めています。 |
| | 証明書の発行が完了しました。現在、証明書を CDN ネットワークにデプロイしています。 これには最大 6 時間かかることがあります。 |
| | CDN ネットワークに証明書をデプロイしました。 |
| 4 完了 | ドメインで HTTPS を有効にしました。 |

\* このメッセージは、エラーが発生しない限り表示されません。 

要求送信前にエラーが発生した場合は、次のエラー メッセージが表示されます。

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>リソースのクリーンアップ - HTTPS を無効にする

このセクションでは、カスタム ドメインの HTTPS を有効にする方法について説明します。

### <a name="disable-the-https-feature"></a>HTTPS 機能を無効にする 

1. [Azure portal](https://portal.azure.com) で、**CDN のプロファイル** を検索して選択します。 

2. お使いの **Azure CDN Standard from Microsoft**、**Azure CDN Standard from Verizon**、または **Azure CDN Premium from Verizon** プロファイルを選択します。

3. エンドポイントの一覧で、カスタム ドメインを含むエンドポイントをクリックします。

4. HTTPS を無効にするカスタム ドメインを選択します。

    ![カスタム ドメイン リスト](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. **[オフ]** を選択して HTTPS を無効にした後、 **[適用]** を選択します。

    ![カスタム HTTPS ダイアログ](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>伝達を待機する

カスタム ドメインの HTTPS 機能を無効にした後、適用されるまでには最大 6 から 8 時間かかります。 プロセスが完了したら、Azure portal の [カスタム HTTPS] の状態が **[無効]** に変更されます。 [カスタム ドメイン] ダイアログの 3 つの操作ステップが完了とマークされます。 カスタム ドメインは HTTPS を使うことができなくなります。

![[HTTPS の無効化] ダイアログ](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>操作の進行

次の表は、HTTPS を無効にするときの操作の進行を示したものです。 HTTPS を無効にした後、[カスタム ドメイン] ダイアログには 3 つの操作ステップが表示されます。 1 つのステップがアクティブになると、ステップの下に詳細が表示されます。 ステップが正常に完了すると、横に緑色のチェック マークが表示されます。 

| 操作の進行 | 操作の詳細 | 
| --- | --- |
| 1 要求の送信 | 要求を送信しています |
| 2 証明書のプロビジョニング解除 | 証明書を削除しています |
| 3 完了 | 証明書が削除されました |

## <a name="frequently-asked-questions"></a>よく寄せられる質問

1. *証明書プロバイダーはだれですか。どのような種類の証明書が使用されますか。*

    次の場合、カスタム ドメインには、DigiCert によって提供される専用の証明書が使用されます。
    
    * **Azure CDN from Verizon**
    * **Microsoft Azure CDN**

2. *IP ベースと SNI のどちらの TLS/SSL を使用しますか。*

    **Azure CDN from Verizon** と **Azure CDN Standard from Microsoft** のどちらも、SNI TLS/SSL が使用されます。

3. *DigiCert からドメインの検証電子メールが送られて来ない場合はどうすればよいでしょうか。*

    *cdnverify* サブドメインを使用しておらず、CNAME エントリがエンドポイントのホスト名の場合、ドメインの検証メールが送られてくることはありません。
     
    検証は自動的に行われます。 そうではなく、CNAME エントリがないうえに 24 時間以内にメールが届かなかった場合、Microsoft のサポートに問い合わせてください。

4. *SAN 証明書を使用すると専用証明書の場合よりも安全性が低くなるでしょうか。*
    
    SAN 証明書は、専用証明書と同じ暗号化およびセキュリティ標準に従っています。 発行されるすべての TLS/SSL 証明書には、サーバーのセキュリティを強化するために SHA-256 が使用されます。

5. *DNS プロバイダーに Certificate Authority Authorization レコードが必要ですか。*

    現在、Certificate Authority Authorization レコードは必要ありません。 ただし、所持している場合は、そこには有効な CA として DigiCert が含められている必要があります。

6. *2018 年 6 月 20 日以降、Verizon の Azure CDN で、SNI TLS/SSL による専用証明書が既定で使用されます。SAN (Subject Alternative Name: サブジェクトの別名) 証明書と IP ベースの TLS/SSL を使用している既存のカスタム ドメインはどうなるのでしょうか。*

    Microsoft の分析の結果、アプリケーションに対する要求が SNI クライアント要求だけであることがわかった場合、既にあるドメインは今後数か月内に単一の証明書へと徐々に移行されます。 
    
    非 SNI クライアントが検出された場合、ドメインは SAN 証明書と IP ベースの TLS/SSL のままになります。 SNI 非対応のサービスやクライアントに対する要求には影響ありません。

7. *証明書の更新では、独自の証明書の持ち込みをどのように処理しますか。*

    PoP インフラストラクチャに新しい証明書がデプロイされるようにするには、新しい証明書を Azure Key Vault にアップロードします。 Azure CDN の TLS 設定で、最新のバージョンの証明書を選択し、[保存] を選択してください。 Azure CDN によって、新しい更新された証明書が反映されます。 

8. *エンドポイントの再起動後に HTTPS を再度有効にする必要がありますか。*

    はい。 **Azure CDN from Akamai** を使用している場合、エンドポイントを停止して再起動したときは、HTTPS 設定を再度有効にする必要があります (前にこの設定が有効だった場合)。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> - カスタム ドメインで HTTPS プロトコルを有効にする。
> - CDN で管理された証明書を使用する 
> - 独自の証明書を使用する
> - ドメインを検証する。
> - カスタム ドメインで HTTPS プロトコルを無効にする。

次のチュートリアルに進み、CDN エンドポイントでキャッシュを構成する方法を学習してください。

> [!div class="nextstepaction"]
> [チュートリアル:Azure CDN キャッシュ規則の設定](cdn-caching-rules-tutorial.md)
