---
title: OPC Vault を使用して OPC UA デバイスの通信をセキュリティで保護する - Azure | Microsoft Docs
description: OPC UA アプリケーションを登録する方法と、OPC Vault を使用して OPC UA デバイスに署名済みアプリケーション証明書を発行する方法。
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: caa3717fdf915e205c1a4f82f4e199346ca29fcb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082814"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>OPC Vault 証明書管理サービスの使用

この記事では、アプリケーションを登録する方法と、OPC UA デバイスに署名済みのアプリケーション証明書を発行する方法について説明します。

## <a name="prerequisites"></a>前提条件

### <a name="deploy-the-certificate-management-service"></a>証明書管理サービスをデプロイする

まず、Azure Cloud にそのサービスをデプロイします。 詳しくは、[OPC Vault 証明書管理サービスのデプロイ](howto-opc-vault-deploy.md)に関するページを参照してください。

### <a name="create-the-issuer-ca-certificate"></a>発行者 CA 証明書を作成する

発行者 CA 証明書を作成します (まだ作成していない場合)。 詳しくは、[OPC Vault の発行者証明書を作成および管理する](howto-opc-vault-manage.md)ことに関する記事を参照してください。

## <a name="secure-opc-ua-applications"></a>OPC UA アプリケーションをセキュリティで保護する

### <a name="step-1-register-your-opc-ua-application"></a>手順 1:OPC UA アプリケーションを登録する 

> [!IMPORTANT]
> アプリケーションを登録するには、ライター ロールが必要です。

1. `https://myResourceGroup-app.azurewebsites.net` で証明書サービスを開いて、サインインします。
2. **[Register New]\(新規登録\)** に移動します。 アプリケーションの登録には、ユーザーに少なくともライター ロールが割り当てられている必要があります。
2. 入力フォームは、OPC UA の名前付け規則に従っています。 例えば下のスクリーンショットでは、OPC UA .NET Standard スタック内の [OPC UA Reference Server](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/Applications/ReferenceServer) サンプルの設定が示されています。

   ![UA Reference Server の登録のスクリーンショット](media/howto-opc-vault-secure/reference-server-registration.png "UA Reference Server の登録")

5. **[Register]\(登録\)** を選択して、アプリケーションを証明書サービス アプリケーション データベースに登録します。 このワークフローでは、ユーザーは直接、アプリケーションの署名済み証明書を要求する次の手順に進みます。

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>手順 2:CA 署名済みアプリケーション証明書を使用して、アプリケーションをセキュリティで保護する

証明書署名要求 (CSR) に基づいて署名済み証明書を発行することにより、OPC UA アプリケーションをセキュリティで保護します。 または、PFX または PEM 形式の新しい秘密キーが含まれた新しいキー ペアを要求することもできます。 お使いのアプリケーションでどの方法がサポートされているかは、使用する OPC UA デバイスのドキュメントを参照してください。 一般に、CSR の方法では通信回線を経由して秘密キーを転送する必要がないため、こちらの方法をお勧めします。

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>新しいキー ペアで新しい証明書を要求する

1. **[Applications]\(アプリケーション\)** に移動します。
3. 一覧表示されたアプリケーションの **[New Request]\(新しい要求\)** を選択します。

   ![新しい証明書の要求のスクリーンショット](media/howto-opc-vault-secure/request-new-certificate.png "新しい証明書の要求")

3. **[Request new KeyPair and Certificate]\(新しいキーペアと証明書を要求する\)** を選択し、アプリケーションの公開キーを使用して秘密キーと新しい署名済み証明書を要求します。

   ![新しいキー ペアと証明書の生成のスクリーンショット](media/howto-opc-vault-secure/generate-new-key-pair.png "新しいキー ペアの生成")

4. フォームにサブジェクト名とドメイン名を入力します。 秘密キーとして、PEM または PFX を選択し、パスワードを指定します。 **[Generate New KeyPair]\(新しいキー ペアの生成\)** を選択し、証明書の要求を作成します。

   ![証明書要求の詳細の表示のスクリーンショット](media/howto-opc-vault-secure/approve-reject.png "証明書の承認")

5. 承認では、ユーザーが承認者ロールと Azure Key Vault での署名アクセス許可を持っている必要があります。 一般的なワークフローでは、承認者ロールと要求者ロールを別々のユーザーに割り当てる必要があります。 実際のキー ペアの作成と署名操作を開始する場合は **[Approve]\(承認\)** を選択し、キャンセルする場合は **[Reject]\(拒否\)** を選択します。 新しいキー ペアが作成され、証明書の要求元からダウンロードされるまで Azure Key Vault に安全に格納されます。 公開キーを使用して生成された証明書は、CA によって署名されます。 これらの操作の完了には数秒かかることがあります。

   ![下部に承認メッセージが表示された証明書要求の詳細の表示のスクリーンショット](media/howto-opc-vault-secure/view-key-pair.png "キー ペアの表示")

7. 生成された秘密キー (PFX または PEM) と証明書 (DER) は、選択した形式でバイナリ ファイルとしてここからダウンロードできます。 たとえば、証明書をコマンド ラインまたはテキスト エントリにコピーして貼り付けるには、base64 でエンコードされたバージョンを使用することもできます。 
8. 秘密キーをダウンロードして安全に保存したら、 **[Delete Private Key]\(秘密キーの削除\)** を選択できます。 公開キーを持つ証明書は、後で使用できるように残します。
9. CA の署名済み証明書が使用されているため、CA 証明書と証明書失効リスト (CRL) もここでダウンロードする必要があります。

これで新しいキー ペアの適用方法について、OPC UA デバイスに依存するようになりました。 通常、CA 証明書と CRL は `trusted` フォルダーにコピーされますが、アプリケーション証明書の公開キーと秘密キーは証明書ストアの `own` フォルダーに適用されます。 一部のデバイスでは、証明書の更新にサーバー プッシュが既にサポートされている場合があります。 ご利用の OPC UA デバイスのドキュメントを参照してください。

#### <a name="request-a-new-certificate-with-a-csr"></a>CSR で新しい証明書を要求する 

1. **[Applications]\(アプリケーション\)** に移動します。
3. 一覧表示されたアプリケーションの **[New Request]\(新しい要求\)** を選択します。

   ![新しい証明書の要求のスクリーンショット](media/howto-opc-vault-secure/request-new-certificate.png "新しい証明書の要求")

3. ご使用のアプリケーションの新しい署名済み証明書を要求するには、 **[Request new Certificate with Signing Request]\(署名要求で新しい証明書を要求する\)** を選択します。

   ![新しい証明書の生成のスクリーンショット](media/howto-opc-vault-secure/generate-new-certificate.png "新しい証明書の生成")

4. ローカル ファイルを選択するか、base64 でエンコードされた CSR をフォームに貼り付けて、CSR をアップロードします。 **[Generate New Certificate]\(新しい証明書の生成\)** を選択します。

   ![証明書要求の詳細の表示のスクリーンショット](media/howto-opc-vault-secure/approve-reject-csr.png "CSR の承認")

5. 承認では、ユーザーが承認者ロールと Azure Key Vault での署名アクセス許可を持っている必要があります。 実際の署名操作を開始する場合は **[Approve]\(承認\)** を選択し、キャンセルする場合は **[Reject]\(拒否\)** を選択します。 公開キーを使用して生成された証明書は、CA によって署名されます。 この操作の完了には数秒かかることがあります。

   ![下部に承認メッセージが表示された証明書要求の詳細の表示のスクリーンショット](media/howto-opc-vault-secure/view-cert-csr.png "証明書の表示")

6. 生成された証明書 (DER) は、ここからバイナリ ファイルとしてダウンロードできます。 たとえば、証明書をコマンド ラインまたはテキスト エントリにコピーして貼り付けるには、base64 でエンコードされたバージョンを使用することもできます。 
10. 証明書をダウンロードして安全に保存したら、 **[Delete Certificate]\(証明書の削除\)** を選択できます。
11. CA の署名済み証明書が使用されているため、CA 証明書と CRL もここでダウンロードする必要があります。

これで新しい証明書の適用方法について、OPC UA デバイスに依存するようになりました。 通常、CA 証明書と CRL は `trusted` フォルダーにコピーされますが、アプリケーション証明書は証明書ストアの `own` フォルダーに適用されます。 一部のデバイスでは、証明書の更新にサーバー プッシュが既にサポートされている場合があります。 ご利用の OPC UA デバイスのドキュメントを参照してください。

### <a name="step-3-device-secured"></a>手順 3:セキュリティで保護されたデバイス

これで、OPC UA デバイスは、追加の構成なしで、CA 署名済み証明書によって保護されている他の OPC UA デバイスと通信できるようになりました。

## <a name="next-steps"></a>次のステップ

ここでは、OPC UA デバイスをセキュリティで保護する方法を学習しました。次のことができます。

> [!div class="nextstepaction"]
> [セキュリティで保護された証明書管理サービスを実行する](howto-opc-vault-secure-ca.md)
