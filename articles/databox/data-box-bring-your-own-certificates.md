---
title: Azure Data Box/Azure Data Box Heavy デバイスで独自の証明書を使用する
description: 独自の証明書を使用して、Data Box または Data Box Heavy デバイスのローカル Web UI および Blob Storage にアクセスする方法。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: 1836ed57305fd7e168961eb81670b56d4ce296cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545175"
---
# <a name="use-your-own-certificates-with-data-box-and-data-box-heavy-devices"></a>Data Box/Data Box Heavy デバイスで独自の証明書を使用する

注文処理中に、Data Box または Data Box Heavy デバイスのローカル Web UI と BLOB ストレージにアクセスするための自己署名証明書が生成されます。 信頼されたチャネルを介してデバイスと通信する場合は、独自の証明書を使用できます。

この記事では、独自の証明書をインストールする方法と、デバイスをデータセンターに戻す前に既定の証明書に戻す方法について説明します。 また、証明書の要件の概要も示します。

## <a name="about-certificates"></a>証明書について

証明書は、**公開キー** と、**証明機関** などの信頼されたサード パーティが **署名** (検証) したエンティティ (ドメイン名など) を結び付けるものです。  証明書は、信頼された公開暗号化キーを配布するための便利な方法を提供します。 このように、証明書によって、通信が信頼できるものであること、暗号化された情報を正しいサーバーに送信していることが確認されます。

Data Box デバイスが最初に構成されたときに、自己署名証明書が自動的に生成されます。 必要に応じて、独自の証明書を導入できます。 独自の証明書を導入する場合は、従わなければならないガイドラインがあります。

Data Box または Data Box Heavy デバイスでは、次の 2 種類のエンドポイント証明書が使用されます。

- BLOB ストレージ証明書
- ローカル UI 証明書

### <a name="certificate-requirements"></a>証明書の要件

証明書は、次の要件を満たす必要があります。

- エンドポイント証明書は、エクスポート可能な秘密キーを含む `.pfx` 形式である必要があります。
- 各エンドポイントに個別の証明書を使用することも、複数のエンドポイントにマルチドメイン証明書を使用することも、ワイルドカード エンドポイント証明書を使用することもできます。
- エンドポイント証明書のプロパティは、一般的な SSL 証明書のプロパティに似ています。
- DER 形式 (`.cer` ファイル名拡張子) の対応する証明書が、クライアント コンピューターで必要になります。
- ローカル UI 証明書をアップロードした後、ブラウザーを再起動してキャッシュをクリアする必要があります。 お使いのブラウザーの具体的な手順を参照してください。
- デバイス名または DNS ドメイン名が変更された場合は、証明書を変更する必要があります。
- エンドポイント証明書を作成するときは、次の表を使用します。

  |Type |サブジェクト名 (SN)  |サブジェクトの別名 (SAN)  |サブジェクト名の例 |
  |---------|---------|---------|---------|
  |ローカル UI| `<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`| `mydevice1.microsoftdatabox.com` |
  |BLOB ストレージ|`*.blob.<DeviceName>.<DNSdomain>`|`*.blob.< DeviceName>.<DNSdomain>`|`*.blob.mydevice1.microsoftdatabox.com` |
  |マルチ SAN の単一証明書|`<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`<br>`*.blob.<DeviceName>.<DNSdomain>`|`mydevice1.microsoftdatabox.com` |

詳細については、「[証明書の要件](../../articles/databox-online/azure-stack-edge-gpu-certificate-requirements.md)」をご覧ください。

## <a name="add-certificates-to-device"></a>証明書をデバイスに追加する

独自の証明書を使用して、ローカル Web UI にアクセスしたり、BLOB ストレージにアクセスしたりすることができます。

> [!IMPORTANT]
> デバイス名または DNS ドメインが変更された場合、新しい証明書を作成する必要があります。 次に、クライアント証明書とデバイス証明書を、新しいデバイス名と DNS ドメインを使用して更新する必要があります。

独自の証明書を追加するには、次の手順に従います。

1. **[管理]**  >  **[証明書]** に移動します。

   **[名前]** にデバイス名が表示されます。 **[DNS ドメイン]** には、DNS サーバーのドメイン名が表示されます。

   画面の下部には、現在使用されている証明書が表示されます。 新しいデバイスの場合、注文処理中に生成された自己署名証明書が表示されます。

   ![Data Box デバイスの [証明書] ページ](media/data-box-bring-your-own-certificates/certificates-manage-certs.png)

2. **[名前]** (デバイス名) または **[DNS ドメイン]** (デバイスの DNS サーバーのドメイン) を変更する必要がある場合は、証明書を追加する前にここで変更します。 次に、**[適用]** を選択します。

   デバイス名または DNS ドメイン名が変更された場合は、証明書を変更する必要があります。

   ![Data Box に新しいデバイス名と DNS ドメインを適用する](media/data-box-bring-your-own-certificates/certificates-device-name-dns.png)

3. 証明書を追加するには、 **[証明書の追加]** を選択して **[証明書の追加]** パネルを開きます。 次に、 **[証明書の種類]** で **[BLOB ストレージ]** か **[ローカル Web UI]** のどちらかを選択します。

   ![Data Box デバイスの [証明書] ページの [証明書の追加] パネル](media/data-box-bring-your-own-certificates/certificates-add-certificate-cert-type.png)

4. 証明書ファイル (`.pfx` 形式) を選択し、証明書のエクスポート時に設定したパスワードを入力します。 次に、 **[検証と追加]** を選択します。

   ![BLOB エンドポイント証明書を Data Box に追加するための設定](media/data-box-bring-your-own-certificates/certificates-add-blob-cert.png)

   証明書が正常に追加されると、 **[証明書]** 画面に新しい証明書の拇印が表示されます。 証明書の状態は **[有効]** です。

   ![正常に追加された有効な新しい証明書](media/data-box-bring-your-own-certificates/certificates-view-new-certificate.png)

5. 証明書の詳細を表示するには、証明書の名前を選択してクリックします。 証明書は 1 年後に期限切れになります。

   ![Data Box デバイスの証明書の詳細を表示する](media/data-box-bring-your-own-certificates/certificates-cert-details.png)

   <!--If you changed the local web UI certificate, you'll see the following error. This error will go away when you install the new certificate on the client computer.

   ![Error after a new Local web UI certificate is added to a Data Box device](media/data-box-bring-your-own-certificates/certificates-unable-to-communicate-error.png) TEST. RESTORE IF ERROR IS REPRODUCED.-->

6. ローカル Web UI の証明書を変更した場合は、ブラウザーを再起動してからローカル Web UI を再起動する必要があります。 この手順は、SSL キャッシュの問題を回避するために必要です。

  <!-- TESTING THIS - The communication error should be gone from the **Certificates** screen.-->

7. ローカル Web UI にアクセスするために使用しているクライアント コンピューターに新しい証明書をインストールします。 手順については、以下の「[クライアントに証明書をインポートする](#import-certificates-to-client)」をご覧ください。


## <a name="import-certificates-to-client"></a>クライアントに証明書をインポートする

Data Box デバイスに証明書を追加した後、デバイスへのアクセスに使用するクライアント コンピューターに証明書をインポートする必要があります。 ローカル コンピューターの信頼されたルート証明機関ストアに証明書をインポートします。

Windows クライアントに証明書をインポートするには、次の手順を実行します。

1. ファイル エクスプローラーで、証明書ファイル (`.cer` 形式) を右クリックし、 **[証明書のインストール]** を選択します。 このアクションにより、証明書のインポート ウィザードが開始されます。

    ![証明書のインポート 1](media/data-box-bring-your-own-certificates/import-cert-01.png)

2. **[ストアの場所]** で **[ローカル マシン]** を選択し、 **[次へ]** を選択します。

    ![証明書のインポート ウィザードで、保管場所として [ローカル コンピューター] を選択します](media/data-box-bring-your-own-certificates/import-cert-02.png)

3. **[すべての証明書を次のストアに配置]** を選択し、 **[信頼されたルート証明機関]** を選択して、 **[次へ]** を選択します。

   ![証明書のインポート ウィザードで信頼されたルート証明機関ストアを選択します](media/data-box-bring-your-own-certificates/import-cert-03.png)

4. 設定を確認し、 **[完了]** を選択します。 インポートが正常に完了したことを示すメッセージが表示されます。

   ![証明書の設定を確認し、証明書のインポート ウィザードを終了します](media/data-box-bring-your-own-certificates/import-cert-04.png)

## <a name="revert-to-default-certificates"></a>既定の証明書に戻す

デバイスを Azure データセンターに戻す前に、注文処理中に生成された元の証明書に戻す必要があります。

注文処理中に生成された証明書に戻すには、次の手順を実行します。

1. **[管理]**  >  **[証明書]** に移動して、 **[証明書を復元]** を選択します。

   証明書を復元すると、注文処理中に生成された自己署名証明書の使用に戻ります。 独自の証明書はデバイスから削除されます。

   ![Data Box デバイスの [証明書の管理] の [証明書を復元] オプション](media/data-box-bring-your-own-certificates/certificates-revert-certificates.png)

2. 証明書の復元が正常に完了したら、 **[シャットダウンまたは再起動]** に移動して、 **[再起動]** を選択します。 この手順は、SSL キャッシュの問題を回避するために必要です。

   ![Data Box デバイスの証明書を元に戻した後で、ローカル Web UI をシャットダウンまたは再起動する](media/data-box-bring-your-own-certificates/certificates-restart-ui.png)

   数分待ってから、ローカル Web UI に再度サインインします。
