---
title: トラブルシューティング ガイド - Azure DNS
description: このラーニング パスでは、Azure DNS に関する一般的な問題のトラブルシューティングを開始します
services: dns
author: rohinkoul
ms.service: dns
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: rohink
ms.openlocfilehash: fae63c61949302e25c9dee2899577fa4f0d2a975
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965580"
---
# <a name="azure-dns-troubleshooting-guide"></a>Azure DNS トラブルシューティング ガイド

この記事では、Azure DNS についてよく寄せられる質問のトラブルシューティング情報を提供します。

次の手順で問題が解決しない場合は、[コミュニティ サポートの Microsoft Q&A 質問ページ](/answers/topics/azure-virtual-network.html)で検索したり、問題を投稿したりすることもできます。 または、Azure のサポート リクエストを開くことができます。


## <a name="i-cant-create-a-dns-zone"></a>DNS ゾーンを作成できない

一般的な問題を解決するには、次の手順のうち 1 つ以上を試してください。

1.  Azure DNS 監査ログを確認して、失敗の理由を特定します。
2.  各 DNS ゾーン名は、リソース グループ内で一意である必要があります。 つまり、同じ名前の 2 つの DNS ゾーンでリソース グループを共有することはできません。 別のゾーン名 (別のリソース グループ) を使用してみてください。
3.  "サブスクリプション {サブスクリプション ID} のゾーンが最大数に達したか、最大数を超えました" というエラー メッセージが表示される場合があります。 別の Azure サブスクリプションを使用するか、ゾーンのいくつかを削除するか、Azure サポートに連絡してサブスクリプションの上限を引き上げてください。
4.  "ゾーン '{ゾーン名}' は使用できません" というエラー メッセージが表示される場合があります。 このエラーは、Azure DNS で、この DNS ゾーンのネーム サーバーを割り当てることができなかったことを示します。 別のゾーン名を使用してみてください。 または、ドメイン名の所有者である場合は、Azure サポートに連絡して、ネーム サーバーを割り当ててもらうことができます。


### <a name="recommended-articles"></a>推奨される記事

* [DNS ゾーンとレコード](dns-zones-records.md)
* [DNS ゾーンの作成](./dns-getstarted-portal.md)

## <a name="i-cant-create-a-dns-record"></a>DNS レコードを作成できない

一般的な問題を解決するには、次の手順のうち 1 つ以上を試してください。

1.  Azure DNS 監査ログを確認して、失敗の理由を特定します。
2.  レコード セットが既にありますか。  Azure DNS では、レコード *セット* を使用してレコードを管理します。レコード セットは、同じ名前で同じ種類のレコードのコレクションです。 名前と種類が同じであるレコードが既に存在する場合、別のこのようなレコードを追加するには、既存のレコード セットを編集する必要があります。
3.  DNS ゾーンの頂点 (ゾーンの "ルート") にレコードを作成しようとしていますか。 その場合、DNS の規則では、レコード名として ‘@’ 文字を使用します。 また、DNS 標準では、ゾーンの頂点に CNAME レコードを作成することは許可されていないことにも注意してください。
4.  CNAME の競合が発生していませんか。  DNS 標準では、他の種類のレコードと同じ名前の CNAME レコードは許可されていません。 既存の CNAME がある場合、別の種類の同じ名前のレコードの作成は失敗します。  同様に、別の種類の既存のレコードと名前が一致する場合にも、CNAME の作成は失敗します。 その既存のレコードを削除するか、別のレコード名を選択して競合を解決します。
5.  DNS ゾーンで許可されているレコード セット数の上限に達していませんか。 レコード セットの現在の数と最大数は、Azure Portal のゾーンの [プロパティ] に表示されます。 この上限に達している場合、一部のレコード セットを削除するか、Azure サポートに連絡してこのゾーンのレコード セットの上限を引き上げてから、もう一度試してください。 


### <a name="recommended-articles"></a>推奨される記事

* [DNS ゾーンとレコード](dns-zones-records.md)
* [DNS ゾーンの作成](./dns-getstarted-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>DNS レコードを解決できない

DNS 名前解決は数段階のプロセスであり、多くの理由で失敗する可能性があります。 次の手順を使用すると、Azure DNS でホストされているゾーンで DNS レコードの DNS 解決が失敗した理由を調査できます。

1.  Azure DNS で DNS レコードが正しく構成されていることを確認します。 Azure Portal で DNS レコードを見直して、ゾーン名、レコード名、レコードの種類が正しいことを確認します。
2.  Azure DNS ネーム サーバーで DNS レコードが正しく解決されていることを確認します。
    - DNS クエリをローカル コンピューターから作成した場合、ネーム サーバーの現在の状態を反映していないキャッシュされた結果が表示されることがあります。  また、企業ネットワークでは DNS プロキシ サーバーを使用していることがよくあるため、DNS クエリが特定のネーム サーバーに送信されないことがあります。  これらの問題を回避するには、[digwebinterface](https://digwebinterface.com) などの Web ベースの名前解決サービスを使用します。
    - Azure Portal の表示に従って、DNS ゾーンに対応した適切なネーム サーバーを必ず指定してください。
    - DNS 名が正しいことを確認し (ゾーン名を含む、完全修飾名を指定する必要があります)、レコードの種類が正しいことを確認します
3.  DNS ドメイン名が [Azure DNS ネーム サーバーに適切に委任](dns-domain-delegation.md)されていることを確認します。 [DNS 委任を検証できる多数のサード パーティ Web サイト](https://www.bing.com/search?q=dns+check+tool)があります。 このテストは "*ゾーン*" の委任テストであるため、DNS ゾーン名のみを入力し、完全修飾レコード名は入力しないでください。
4.  上記の手順が完了すると、DNS レコードが正しく解決されるようになります。 これを確認するには、既定のネーム サーバー設定を使用して、[digwebinterface](https://digwebinterface.com) をもう一度使用します。


### <a name="recommended-articles"></a>推奨される記事

* [Azure DNS へのドメインの委任](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>SRV レコードの "サービス" と "プロトコル" を指定するにはどうすればよいですか?

Azure DNS では、DNS レコードをレコード セットとして管理します。レコード セットは、同じ名前で同じ種類のレコードのコレクションです。 SRV レコード セットでは、レコード セット名の一部として "サービス" と "プロトコル" を指定する必要があります。 その他の SRV パラメーター ("優先度"、"重み"、"ポート"、"ターゲット") は、レコード セットのレコードごとに個別に指定します。

SRV レコード名の例 (サービス名が "sip"、プロトコルが "tcp") を次に示します。

- \_sip.\_tcp (レコード セットをゾーンの頂点に作成)
- \_sip.\_tcp.sipservice ("sipservice" という名前のレコード セットを作成)

### <a name="recommended-articles"></a>推奨される記事

* [DNS ゾーンとレコード](dns-zones-records.md)
* [Azure portal を使用した DNS レコード セットとレコードの作成](./dns-getstarted-portal.md)
* [SRV レコード タイプ (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>次のステップ

* [Azure DNS ゾーンとレコード](dns-zones-records.md)について学習します
* Azure DNS の使用を開始する場合は、[DNS ゾーンの作成方法](./dns-getstarted-portal.md)と [DNS レコードの作成方法](./dns-getstarted-portal.md)について確認してください。
* 既存の DNS ゾーンを移行する場合は、[DNS ゾーン ファイルのインポートとエクスポート](dns-import-export.md)の実行方法を確認してください。