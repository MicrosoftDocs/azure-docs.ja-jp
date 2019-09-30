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
ms.openlocfilehash: ae10bb3488c21b6637163e333231e95a18c652bf
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996148"
---
# <a name="how-to-use-the-opc-vault-certificate-management-service"></a>OPC Vault 証明書管理サービスの使用方法

この記事では、アプリケーションを登録する方法と、OPC UA デバイスに署名済みのアプリケーション証明書を発行する方法について説明します。

## <a name="prerequisites"></a>前提条件

### <a name="deploy-the-certificate-management-service"></a>証明書管理サービスをデプロイする

まず、サービスを Azure クラウドにデプロイする必要があります。
[OPC Vault 証明書管理サービスをデプロイ](howto-opc-vault-deploy.md)する方法を説明した記事を参照してください。

### <a name="create-the-issuer-ca-certificate"></a>発行者 CA 証明書を作成する

発行者 CA 証明書を作成します (まだ作成していない場合)。

「[OPC Vault 証明書サービスを管理する方法](howto-opc-vault-manage.md)」を説明した記事を検索します。

## <a name="secure-opc-ua-applications"></a>OPC UA アプリケーションをセキュリティで保護する

### <a name="step-1-register-your-opc-ua-application"></a>手順 1:OPC UA アプリケーションを登録する 

> [!IMPORTANT]
> アプリケーションを登録するには、"ライター" ロールが必要です。

1. `https://myResourceGroup-app.azurewebsites.net` で証明書サービスを開き、サインインします。
2. [`Register New`] ページに移動します。
1. アプリケーションの登録には、ユーザーに少なくとも "ライター" ロールが割り当てられている必要があります。
2. 入力フォームは、OPC UA ワールドの名前付け規則に従っています。 例として、下の図では、OPC UA .NetStandard スタック内の [OPC UA Reference Server](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) サンプルの設定が示されています。

   ![UA Reference Server の登録](media/howto-opc-vault-secure/reference-server-registration.png "UA Reference Server の登録")

5. [`Register`] ボタンを押して、アプリケーションを証明書サービス アプリケーション データベースに登録します。 このワークフローでは、ユーザーは直接、アプリケーションの署名済み証明書を要求する次の手順に進みます。

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>手順 2:CA 署名済みアプリケーション証明書を使用して、アプリケーションをセキュリティで保護する

OPC UA アプリケーションをセキュリティで保護するには、証明書署名要求 (CSR) に基づいて署名済み証明書を発行するか、PFX 形式または PEM 形式の新しい秘密キーも含まれる新しいキー ペアを要求します。 アプリケーションでどの方法がサポートされているかは、使用する OPC UA デバイスのドキュメントを参照してください。 一般に、CSR の方法では通信回線を経由して秘密キーを転送する必要がないため、こちらの方法をお勧めします。

- デバイスで新しいキー ペアを発行する必要がある場合は、[手順 3a](##step-3a-request-a-new-certificate-with-a-new-keypair) に従ってください。
- ご利用のデバイスで証明書署名要求 (CSR) の発行がサポートされている場合は、[手順 3b](#step-3b-request-a-new-certificate-with-a-csr) に従ってください。

### <a name="step-3a-request-a-new-certificate-with-a-new-keypair"></a>手順 3a:新しいキー ペアで新しい証明書を要求する

1. [`Applications`] ページに移動します。
3. 一覧表示されたアプリケーションで `New Request` を選択して、証明書の要求ワークフローを開始します。

   ![新しい証明書の要求](media/howto-opc-vault-secure/request-new-certificate.png "新しい証明書の要求")

3. [Request new KeyPair and Certificate]\(新しいキーペアと証明書を要求する\) を押し、アプリケーションの公開キーを使用して秘密キーと新しい署名済み証明書を要求します。

   ![新しいキー ペアの生成](media/howto-opc-vault-secure/generate-new-key-pair.png "新しいキー ペアの生成")

4. フォームにサブジェクト名とドメイン名を入力し、PEM または PFX を選択して、秘密キーのパスワードを指定します。 [`Generate New Certificate`] ボタンを押して、証明書の要求を作成します。

   ![証明書の承認](media/howto-opc-vault-secure/approve-reject.png "証明書の承認")

5. 承認手順では、ユーザーが "承認者" ロールと Azure Key Vault での署名アクセス許可を持っている必要があります。 一般的なワークフローでは、承認者ロールと要求者ロールを別々のユーザーに割り当てる必要があります。
6. 実際のキー ペアの作成と署名操作を開始する場合は証明書の要求を承認し、キャンセルする場合は要求を拒否します。 新しいキー ペアが作成され、証明書の要求元からダウンロードされるまで Azure Key Vault に安全に格納されます。 公開キーを使用して生成された証明書は、CA によって署名されます。 これらの操作の完了には数秒かかることがあります。

   ![キー ペアの表示](media/howto-opc-vault-secure/view-key-pair.png "キー ペアの表示")

7. 生成された秘密キー (PFX または PEM) と証明書 (DER) は、選択した形式でバイナリ ファイルとしてここからダウンロードできます。 たとえば、証明書をコマンド ラインまたはテキスト エントリにコピーして貼り付けるには、base64 でエンコードされたバージョンを使用することもできます。 
8. 秘密キーがダウンロードされ、安全に保存されたら、[`Delete Private Key`] ボタンを使用してサービスから削除できます。 公開キーを持つ証明書は、後で使用できるように残します。
9. CA の署名済み証明書が使用されているため、CA 証明書と CRL もここでダウンロードする必要があります。
10. これで新しいキー ペアの適用方法について、OPC UA デバイスに依存するようになりました。 通常、CA 証明書と CRL は `trusted` フォルダーにコピーされますが、アプリケーション証明書の公開キーと秘密キーは証明書ストアの `own` フォルダーに適用されます。 一部のデバイスでは、証明書の更新に "サーバー プッシュ" が既にサポートされている場合があります。 ご利用の OPC UA デバイスのドキュメントを参照してください。

### <a name="step-3b-request-a-new-certificate-with-a-csr"></a>手順 3b:CSR で新しい証明書を要求する 

1. [`Applications`] ページに移動します。
3. 一覧表示されたアプリケーションで `New Request` を選択して、証明書の要求ワークフローを開始します。

   ![新しい証明書の要求](media/howto-opc-vault-secure/request-new-certificate.png "新しい証明書の要求")

3. ご使用のアプリケーションの新しい署名済み証明書を要求するには、[Request new Certificate with signing request]\(署名要求で新しい証明書を要求する\) を押します。

   ![新しい証明書の生成](media/howto-opc-vault-secure/generate-new-certificate.png "新しい証明書の生成")

4. ローカル ファイルを選択するか、base64 でエンコードされた CSR をフォームに貼り付けて、CSR をアップロードします。 [`Generate New Certificate`] ボタンを押して、証明書の要求を作成します。

   ![CSR の承認](media/howto-opc-vault-secure/approve-reject-csr.png "CSR の承認")

5. 承認手順では、ユーザーが "承認者" ロールと Azure Key Vault での署名アクセス許可を持っている必要があります。 実際の署名操作を開始する場合は証明書の要求を承認し、キャンセルする場合は要求を拒否します。 公開キーを使用して生成された証明書は、CA によって署名されます。 この操作の完了には数秒かかることがあります。

   ![証明書の表示](media/howto-opc-vault-secure/view-cert-csr.png "証明書の表示")

6. 生成された証明書 (DER) は、ここからバイナリ ファイルとしてダウンロードできます。 たとえば、証明書をコマンド ラインまたはテキスト エントリにコピーして貼り付けるには、base64 でエンコードされたバージョンを使用することもできます。 
10. 証明書がダウンロードされ、安全に保存されたら、[`Delete Certificate`] ボタンを使用してサービスから削除できます。
11. CA の署名済み証明書が使用されているため、CA 証明書と CRL もここでダウンロードする必要があります。
12. これで新しい証明書の適用方法について、OPC UA デバイスに依存するようになりました。 通常、CA 証明書と CRL は `trusted` フォルダーにコピーされますが、アプリケーション証明書は証明書ストアの `own` フォルダーに適用されます。 一部のデバイスでは、証明書の更新に "サーバー プッシュ" が既にサポートされている場合があります。 ご利用の OPC UA デバイスのドキュメントを参照してください。

### <a name="step-4-device-secured"></a>手順 4:セキュリティで保護されたデバイス

これで、OPC UA デバイスは、追加の構成なしで、CA 署名済み証明書によって保護されている他の OPC UA デバイスと通信できるようになりました。 機能を有効にご活用ください。


## <a name="next-steps"></a>次の手順

ここでは、OPC UA デバイスをセキュリティで保護する方法を学習しました。次に以下の記事を読むことをお勧めします。

> [!div class="nextstepaction"]
> [セキュリティで保護された証明書管理サービスを実行する](howto-opc-vault-secure-ca.md)