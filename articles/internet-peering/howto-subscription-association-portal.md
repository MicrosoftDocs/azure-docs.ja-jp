---
title: ポータルを使用してピア ASN を Azure サブスクリプションに関連付ける
titleSuffix: Azure
description: ポータルを使用してピア ASN を Azure サブスクリプションに関連付ける
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/18/2020
ms.author: derekol
ms.openlocfilehash: 22cb179925f95fd0762371cc904fcbd02675339a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91540270"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>ポータルを使用してピア ASN を Azure サブスクリプションに関連付ける

インターネット サービス プロバイダーまたはインターネット エクスチェンジ プロバイダーは、ピアリング要求を送信する前に、次の手順に従って、最初に ASN を Azure サブスクリプションに関連付ける必要があります。

必要に応じて、[PowerShell](howto-subscription-association-powershell.md) を使用してこのガイドを完了することもできます。

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>PeerAsn を作成して ASN を Azure サブスクリプションに関連付ける

### <a name="sign-in-to-the-portal"></a>ポータルにサインインする
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>ピアリング リソース プロバイダーに登録する
以下の手順のようにして、サブスクリプションのピアリング リソース プロバイダーに登録します。 これを実行しないと、ピアリングのセットアップに必要な Azure リソースにアクセスできません。

1. ポータルの左上隅にある **[サブスクリプション]** をクリックします。 表示されない場合は、 **[その他のサービス]** をクリックして検索します。

    > [!div class="mx-imgBorder"]
    > ![サブスクリプションを開く](./media/rp-subscriptions-open.png)

1. ピアリングに使用するサブスクリプションをクリックします。

    > [!div class="mx-imgBorder"]
    > ![サブスクリプションを起動する](./media/rp-subscriptions-launch.png)

1. サブスクリプションが開いたら、左側の **[リソース プロバイダー]** をクリックします。 次に、右側のペインの検索ウィンドウで "*ピアリング*" を探すか、スクロール バーを使用して **Microsoft.Peering** を見つけ、 **[状態]** を確認します。 状態が **_[登録済み]_ *_ の場合は、以下の手順をスキップして、「_* PeerAsn を作成する**」セクションに進みます。 状態が **_[未登録]_ *_ の場合は、_* Microsoft.Peering** を選択して、 **[登録]** をクリックします。

    > [!div class="mx-imgBorder"]
    > ![登録の開始](./media/rp-register-start.png)

1. 状態が ***[Registering]\(登録中\)*** に変わることを確認します。

    > [!div class="mx-imgBorder"]
    > ![登録進行中](./media/rp-register-progress.png)

1. 登録が完了するまで 1 分程度待ちます。 その後、 **[最新の情報に更新]** をクリックして、状態が **_[登録済み]_** になっていることを確認します。

    > [!div class="mx-imgBorder"]
    > ![登録完了](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>PeerAsn を作成する
インターネット サービス プロバイダーまたはインターネット エクスチェンジ プロバイダーは、[[Associate a Peer ASN]\(ピア ASN の関連付け)](https://go.microsoft.com/fwlink/?linkid=2129592) ページで、Azure サブスクリプションに自律システム番号 (ASN) を関連付けるための新しい PeerAsn リソースを作成できます。 関連付ける必要がある ASN ごとに **PeerAsn** を作成することにより、複数の ASN を 1 つのサブスクリプションに関連付けることができます。

1. **[Associate a Peer ASN]\(ピア ASN の関連付け\)** ページの **[基本]** タブで、次に示すようにフィールドを設定します。

    > [!div class="mx-imgBorder"]
    > ![PeerAsn の [基本] タブ](./media/peerasn-basics-tab.png)

    * **[名前]** は、リソース名に対応し、任意の名前を選択できます。  
    * ASN を関連付ける必要がある **[サブスクリプション]** を選択します。
    * **[ピア名]** は会社の名前に対応しており、PeeringDB プロファイルにできるだけ近いものにする必要があります。 この値では、文字 a-z、A-Z、およびスペースのみがサポートされていることに注意してください
    * **[ピア ASN]** フィールドに、ASN を入力します。
    * **[新規作成]** をクリックし、ネットワーク オペレーションセンター (NOC) の **メール アドレス** と **電話番号** を入力します
1. 次に、 **[確認と作成]** をクリックし、入力した情報の基本的な検証がポータルで実行されていることを確認します。 上部のリボンに、"*最終検証を実行しています...* " と表示されます。

    > [!div class="mx-imgBorder"]
    > ![[ピア ASN の関連付け] の [基本] タブが表示されているスクリーンショット。](./media/peerasn-review-tab-validation.png)

1. リボンのメッセージが "*検証に成功しました*" に変わったら、情報を確認し、 **[作成]** をクリックして要求を送信します。 検証が成功しなかった場合は、 **[前へ]** をクリックし、上記の手順を繰り返して要求を変更し、入力した値にエラーがないことを確認します。

    > [!div class="mx-imgBorder"]
    > ![[ピア ASN の関連付け] の [基本] タブが表示されているスクリーンショット。検証に成功したことが示されています。](./media/peerasn-review-tab.png)

1. 要求を送信した後、デプロイが完了するまで待ちます。 デプロイが失敗した場合は、[Microsoft ピアリング](mailto:peering@microsoft.com)にお問い合わせください。 デプロイが正常に行われると、次のように表示されます。

    > [!div class="mx-imgBorder"]
    > ![PeerAsn 成功](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>PeerAsn の状態を表示する
PeerAsn リソースが正常にデプロイされたら、Microsoft で関連付け要求が承認されるまで待つ必要があります。 承認には、最大で 12 時間かかる場合があります。 承認されると、上のセクションで入力したメール アドレスで通知を受け取ります。

> [!IMPORTANT]
> ValidationState が "Approved" になるのを待ってから、ピアリング要求を送信します。 この承認には、最大で 12 時間かかる場合があります。

## <a name="modify-peerasn"></a>PeerASN を変更する
現在、PeerAsn の変更はサポートされていません。 変更する必要がある場合は、[Microsoft ピアリング](mailto:peering@microsoft.com)にお問い合わせください。

## <a name="delete-peerasn"></a>PeerASN を削除する
現在、PeerAsn の削除はサポートされていません。 PeerAsn を削除する必要がある場合は、[Microsoft ピアリング](mailto:peering@microsoft.com)にお問い合わせください。

## <a name="next-steps"></a>次のステップ

* [ポータルを使用して Direct ピアリングを作成または変更する](howto-direct-portal.md)
* [ポータルを使用して従来の Direct ピアリングを Azure リソースに変換する](howto-legacy-direct-portal.md)
* [ポータルを使用して Exchange ピアリングを作成または変更する](howto-exchange-portal.md)
* [ポータルを使用してレガシの Exchange ピアリングを Azure リソースに変換する](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>その他のリソース

詳細については、「[インターネット ピアリングのよくあるご質問](faqs.md)」を参照してください
