---
title: Azure Front Door Standard/Premium SKU 構成でカスタム ドメインの HTTPS を構成する
description: この記事では、Azure Front Door Standard/Premium SKU にカスタム ドメインをオンボードする方法を説明します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: c2edf11939996156c2b589b0b7876ae1b01466e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740819"
---
# <a name="configure-https-on-a-front-door-standardpremium-sku-preview-custom-domain-using-the-azure-portal"></a>Azure portal を使用して Front Door Standard/Premium SKU (プレビュー) のカスタム ドメインで HTTPS を構成する

> [!NOTE]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

Azure Front Door Standard/Premium では、カスタム ドメインを追加すると、お使いのアプリケーションに対するセキュリティで保護された TLS 配信が既定で有効になります。 カスタム ドメインで HTTPS プロトコルを使用すると、インターネット経由での送信時に機密データが TLS/SSL 暗号化でセキュリティ保護されて配信されます。 Web ブラウザーが HTTPS 経由で Web サイトに接続しているときに、Web サイトのセキュリティ証明書を検証し、正当な証明機関によって発行されていることを確認します。 このプロセスによりセキュリティを確保し、Web アプリケーションを攻撃から保護します。

Azure Front Door Standard/Premium では Azure マネージド証明書とカスタマー マネージド証明書の両方がサポートされます。 Azure Front Door では、Azure マネージド証明書を使用して、すべてのカスタム ドメインに対する HTTPS が既定で自動的に有効になります。 Azure マネージド証明書を取得するための追加の手順は必要ありません。 証明書は、ドメインの検証プロセス中に作成されます。 Azure Front Door Standard/Premium と Key Vault を統合して、独自の証明書を使用することもできます。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳細については、「[**Microsoft Azure プレビューの追加使用条件**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

* カスタム ドメインに対する HTTPS を構成する前に、まず Azure Front Door Standard/Premium プロファイルを作成する必要があります。 詳細については、「[クイックスタート : Azure Front Door Standard/Premium プロファイルを作成する](create-front-door-portal.md)」を参照してください。

* カスタム ドメインがまだない場合は、最初にカスタム ドメインをドメイン プロバイダーから購入する必要があります。 たとえば、[カスタム ドメイン名の購入](../../app-service/manage-custom-dns-buy-domain.md)に関するページを参照してください。

* Azure を使用して [DNS ドメイン](../../dns/dns-overview.md)をホストしている場合は、ドメイン プロバイダーのドメイン ネーム システム (DNS) を Azure DNS に委任する必要があります。 詳細については、「[Azure DNS へのドメインの委任](../../dns/dns-delegate-domain-azure-dns.md)」を参照してください。 DNS ドメインを処理するためにドメイン プロバイダーを使用している場合は、このようにしないと、要求された DNS TXT レコードを入力して手動でドメインを検証する必要があります。

## <a name="azure-managed-certificates"></a>Azure マネージド証明書

1. Azure Front Door Standard/Premium プロファイルの [設定] の下で、 **[ドメイン]** を選択し、 **[+ 追加]** を選択して新しいドメインを追加します。

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-new-custom-domain.png" alt-text="ドメイン構成ランディング ページのスクリーンショット。":::

1. **[Add a domain]\(ドメインの追加\)** ページで、 *[DNS management]\(DNS の管理\)* に **[Azure managed DNS]\(Azure マネージド DNS\)** オプションを選択します。 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-domain-azure-managed.png" alt-text="[Azure managed DNS]\(Azure マネージド DNS\) が選択された [Add a domain]\(ドメインの追加\) ページのスクリーンショット。":::

1. [カスタム ドメイン](how-to-add-custom-domain.md)を有効にする手順に従って、カスタム ドメインを検証してエンドポイントに関連付けます。

1. カスタム ドメインがエンドポイントに正常に関連付けられると、Azure マネージド証明書が Front Door にデプロイされます。 このプロセスが完了するまでに数分かかることがあります。

## <a name="using-your-own-certificate"></a>独自の証明書の使用

独自の TLS 証明書を使用するように選択することもできます。 この証明書は、Azure Front Door Standard/Premium で使用する前に、Azure Key Vault にインポートする必要があります。 Azure Key Vault への[証明書のインポート](../../key-vault/certificates/tutorial-import-certificate.md)に関する記事を参照してください。 

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Azure Key Vault のアカウントと証明書を準備する
 
1. カスタム HTTPS を有効にする Azure Front Door Standard/Premium と同じサブスクリプションで、Azure Key Vault アカウントを実行している必要があります。 Azure Key Vault アカウントがない場合は作成します。

    > [!WARNING]
    > Azure Front Door では、現在、Front Door 構成と同じサブスクリプションの Key Vault アカウントのみがサポートされています。 Azure Front Door Standard/Premium とは異なるサブスクリプションの Key Vault を選択すると、エラーが発生します。

1. 証明書を既にお持ちの場合には、Azure Key Vault アカウントに直接アップロードできます。 それ以外の場合は、Azure Key Vault が統合されているいずれかのパートナー証明機関から Azure Key Vault を介して、新しい証明書を直接作成します。 証明書は、**シークレット** ではなく **証明書** オブジェクトとしてアップロードします。

    > [!NOTE]
    > 独自の TLS/SSL 証明書については、Front Door は EC 暗号化アルゴリズムを使用した証明書をサポートしていません。

#### <a name="register-azure-front-door"></a>Azure Front Door を登録する

PowerShell を使用して、Azure Active Directory にアプリとして Azure Front Door 用のサービス プリンシパルを登録します。

> [!NOTE]
> この操作はグローバル管理者のアクセス許可を必要とし、テナントごとに **1 回** だけ実行する必要があります。

1. 必要があれば、PowerShell でローカル マシンに [Azure PowerShell](/powershell/azure/install-az-ps) をインストールします。

1. PowerShell で次のコマンドを実行します。

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>キー コンテナーへの Azure Front Door のアクセス権を付与する
 
Azure Key Vault アカウント内の証明書にアクセスするには、Azure Front Door のアクセス許可を付与します。

1. Key Vault アカウントで、[設定] の下の **[アクセス ポリシー]** を選択します。 次に、 **[新規作成]** を選択して新しいポリシーを作成します。

1. **[プリンシパルの選択]** で、**205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** を検索し、**[Microsoft.AzureFrontDoor-Cdn]** を選択します。 **[選択]** をクリックします。

1. **[シークレットのアクセス許可]** で **[取得]** を選択して、Front Door に証明書の取得を許可します。

1. **[証明書のアクセス許可]** で **[取得]** を選択して、Front Door に証明書の取得を許可します。

1. **[OK]** を選択します。 

#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>デプロイする Azure Front Door の証明書を選択する
 
1. ポータルで、Azure Front Door Standard/Premium に戻ります。

1. *[設定]* の下の **[シークレット]** に移動し、 **[証明書の追加]** を選択します。

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate.png" alt-text="Azure Front Door の [シークレット] ランディング ページ。":::

1. **[証明書の追加]** ぺージで、Azure Front Door Standard/Premium に追加する証明書のチェックボックスをオンにします。 バージョンの選択を [最新] のままにして、 **[追加]** を選択します。 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate-page.png" alt-text="[証明書の追加] ページのスクリーンショット。":::

1. 証明書が正常にプロビジョニングされると、新しいカスタム ドメインを追加するときにそれを使用できます。

    :::image type="content" source="../media/how-to-configure-https-custom-domain/successful-certificate-provisioned.png" alt-text="証明書がシークレットに正常に追加された場合のスクリーンショット。":::

1. *[設定]* の下の **[ドメイン]** に移動し、 **[+ 追加]** を選択して新しいカスタム ドメインを追加します。 **[ドメインの追加]** ページで、 *[HTTPS]* に [Bring Your Own Certificate (BYOC)] を選択します。 *[シークレット]* では、ドロップダウンから使用する証明書を選択します。 

    > [!NOTE]
    > 選択した証明書には、追加するカスタム ドメインと同じ共通名 (CN) が必要です。

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-custom-domain-https.png" alt-text="HTTPS を使用したカスタム ドメインの追加ページのスクリーンショット。":::

1. 画面上の手順に従って証明書を検証します。 次に、[カスタム ドメインの作成](how-to-add-custom-domain.md)に関する記事で説明されているように、新しく作成したカスタム ドメインをエンドポイントに関連付けます。

#### <a name="change-from-azure-managed-to-bring-your-own-certificate-byoc"></a>Azure マネージドから Bring Your Own Certificate (BYOC) に変更する

1. 証明書の状態 を選択して **[証明書の詳細]** ページを開き、既存の Azure マネージド証明書をユーザー マネージド証明書に変更できます。

    :::image type="content" source="../media/how-to-configure-https-custom-domain/domain-certificate.png" alt-text="[ドメイン] ランディング ページの証明書の状態のスクリーンショット。" lightbox="../media/how-to-configure-https-custom-domain/domain-certificate-expanded.png":::

1. **[証明書の詳細]** ページで、[Azure managed]\(Azure マネージド\) から [Bring Your Own Certificate (BYOC)] オプションに変更できます。 その後、前と同じ手順に従って、証明書を選択します。 **[更新]** を選択してドメインに関連付けられている証明書を変更します。

    :::image type="content" source="../media/how-to-configure-https-custom-domain/certificate-details-page.png" alt-text="[証明書の詳細] ページのスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

[Azure Front Door Standard/Premium でのキャッシュ](concept-caching.md)について学習します。
