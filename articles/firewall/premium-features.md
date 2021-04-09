---
title: Azure Firewall Premium プレビューの機能
description: Azure Firewall Premium は、Azure 仮想ネットワーク リソースを保護する、クラウドベースのマネージド ネットワーク セキュリティ サービスです。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: victorh
ms.openlocfilehash: a3f72d235d6c52ce91ae351c2606ee6cf4285159
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453429"
---
# <a name="azure-firewall-premium-preview-features"></a>Azure Firewall Premium プレビューの機能

:::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="ICSA 認定のロゴ" border="false"::::::image type="content" source="media/premium-features/pci-logo.png" alt-text="PCI 認定のロゴ" border="false":::


> [!IMPORTANT]
> 現在、Azure Firewall Premium はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

 Azure Firewall Premium プレビューは、機密度が高く、規制された環境に必要な機能を備えた次世代のファイアウォールです。

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Azure Firewall Premium の概要を示す図":::

Azure Firewall Premium プレビューでは、ファイアウォール ポリシーが使用されます。これは、Azure Firewall Manager を使用してファイアウォールを一元的に管理するために使用できるグローバル リソースです。 このリリースからは、すべての新機能はファイアウォール ポリシーによってのみ構成できます。 ファイアウォール規則 (クラシック) は引き続きサポートされ、既存の Standard Firewall 機能の構成に使用できます。  ファイアウォール ポリシーは、独立的に、または Azure Firewall Manager を使用して管理できます。 1 つのファイアウォールに関連付けられているファイアウォール ポリシーには課金されません。

> [!IMPORTANT]
> 現在、Firewall Premium SKU は、セキュリティで保護されたハブのデプロイと強制トンネル構成ではサポートされていません。 

Azure Firewall Premium プレビューには次の機能が含まれています。

- **TLS 検査** - アウトバウンド トラフィックを復号化し、データを処理し、その後にデータを暗号化して宛先に送信します。
- **IDPS** - ネットワーク侵入検出と防止システム (IDPS) を使用すると、ネットワーク アクティビティを監視して悪意のあるアクティビティがないか確認し、このアクティビティに関する情報をログに記録し、それを報告して、任意でそのブロックを試みることができます。
- **URL フィルタリング** - Azure Firewall の FQDN フィルタリング機能を拡張して URL 全体が考慮対象になるようにします。 たとえば、`www.contoso.com` の代わりに `www.contoso.com/a/c` となります。
- **Web カテゴリ** - 管理者は、ギャンブルの Web サイトやソーシャル メディアの Web サイトといった Web サイト カテゴリへのユーザーのアクセスを許可または拒否できます。


## <a name="tls-inspection"></a>TLS インスペクション

Azure Firewall Premium では、アウトバウンドと東西の TLS 接続は終了されます。 インバウンドの TLS 検査は [Azure Application Gateway](../web-application-firewall/ag/ag-overview.md) でサポートされ、エンドツーエンドの暗号化を行うことができます。 Azure Firewall では必要な付加価値のセキュリティ機能が実行され、元の宛先に送信されるトラフィックが再暗号化されます。

> [!TIP]
> TLS 1.0 と 1.1 は非推奨になっていて、サポートされません。 TLS 1.0 と 1.1 のバージョンの TLS/SSL (Secure Sockets Layer) は脆弱であることが確認されています。現在、これらは下位互換性を維持するために使用可能ですが、推奨されていません。 できるだけ早く TLS 1.2 に移行してください。

Azure Firewall Premium プレビューの中間 CA 証明書の要件に関する詳細については、「[Azure Firewall Premium プレビューの証明書](premium-certificates.md)」を参照してください。

## <a name="idps"></a>IDPS

ネットワーク侵入検出と防止システム (IDPS) を使用すると、ネットワークを監視して悪意のあるアクティビティがないか確認し、このアクティビティに関する情報をログに記録し、それを報告して、必要に応じてそのブロックを試みることができます。 

Azure Firewall Premium プレビューにはシグネチャベースの IDPS が用意され、ネットワーク トラフィック内のバイト シーケンスやマルウェアによって使用される既知の悪意のある命令シーケンスなど、特定のパターンを探すことによって攻撃を迅速に検出できます。 IDPS シグネチャはフル マネージドであり、継続的に更新されます。

Azure Firewall シグネチャ/ルールセットには次のものが含まれます。
- 実際のマルウェア、コマンド アンド コントロール、エクスプロイト キット、さらには従来の防止方法では見逃されていた、実害の出ている悪意のあるアクティビティについて、フィンガープリントを重視。
- 50 以上のカテゴリで 35000 を超えるルール。
    - カテゴリには、マルウェアのコマンド アンド コントロール、DoS 攻撃、ボットネット、情報イベント、エクスプロイト、脆弱性、SCADA ネットワークプロトコル、エクスプロイト キット アクティビティなどが含まれます。
- 20 ～ 40 以上の新しいルールが毎日リリースされます。
- 最先端のマルウェア サンドボックスとグローバル センサー ネットワーク フィードバック ループを使用した、低い擬陽性率。

IDPS を使用すると、暗号化されていないトラフィックですべてのポートとプロトコルの攻撃を検出できます。 ただし、HTTPS トラフィックを検査する必要がある場合、Azure Firewall ではその TLS 検査機能を使用してトラフィックを復号化し、悪意のあるアクティビティをより的確に検出できます。  

IDPS バイパス一覧を使用すると、バイパス一覧に指定された IP アドレス、範囲、サブネットへのトラフィックをフィルター処理しないようにすることができます。 

## <a name="url-filtering"></a>URL フィルタリング

URL フィルタリングは、Azure Firewall の FQDN フィルタリング機能を拡張して URL 全体が考慮対象になるようにします。 たとえば、`www.contoso.com` の代わりに `www.contoso.com/a/c` となります。  

URL フィルタリングは、HTTP と HTTPS の両方のトラフィックに適用できます。 Azure Firewall Premium プレビューでは、HTTPS トラフィックを検査する際にその TLS 検査機能を使用してトラフィックを復号化し、ターゲット URL を抽出してアクセスが許可されているかどうかを検証できます。 TLS 検査には、アプリケーション規則レベルでのオプトインが必要です。 有効にすると、URL を使用して HTTPS でのフィルター処理ができるようになります。 

## <a name="web-categories"></a>Web カテゴリ

Web カテゴリでは、管理者は、ギャンブルの Web サイトやソーシャル メディアの Web サイトなどの Web サイト カテゴリへのユーザーのアクセスを許可または拒否できます。 Web カテゴリは Azure Firewall Standard にも含まれますが、Azure Firewall Premium プレビューではさらに細かく調整されます。 FQDN に基づくカテゴリと照合する Standard SKU の Web カテゴリ機能とは異なり、Premium SKU では、HTTP と HTTPS の両方のトラフィックについて URL 全体に従ってカテゴリを照合します。 

たとえば、Azure Firewall が `www.google.com/news` の HTTPS 要求をインターセプトする場合、次のような分類が想定されます。 

- Firewall Standard - FQDN 部分のみが検証されるため、`www.google.com` は "*検索エンジン*" として分類されます。 

- Firewall Premium - URL 全体が検証されるため、`www.google.com/news` は "*ニュース*" として分類されます。

カテゴリは、 **[責任]** 、 **[高帯域幅]** 、 **[ビジネス利用]** 、 **[生産性の低下]** 、 **[一般的なネット サーフィン]** 、 **[未分類]** の下で重要度に基づいて整理されています。

### <a name="category-exceptions"></a>カテゴリの例外

Web カテゴリの規則の例外を作成できます。 規則コレクション グループ内で、より優先度の高い個別の許可または拒否の規則コレクションを作成します。 たとえば、優先度が 100 で `www.linkedin.com` を許可する規則コレクションと、優先順位が 200 で **ソーシャル ネットワーキング** を拒否する規則コレクションを構成できます。 これにより、定義済みの **ソーシャル ネットワーキング** Web カテゴリの例外が作成されます。

### <a name="categorization-change"></a>分類の変更

次の場合に、分類の変更を要求できます。

 - FQDN または URL が別のカテゴリの下にある必要があると思われる 
 
or 

- 分類されていない FQDN または URL に対して推奨されるカテゴリがある

[https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) で要求を送信してください。
 

## <a name="known-issues"></a>既知の問題

Azure Firewall Premium プレビューには、次の既知の問題があります。

|問題  |説明  |対応策  |
|---------|---------|---------|
|TLS 検査が HTTPS 標準ポートでのみサポートされる|TLS 検査では、HTTPS/443 のみがサポートされます。 |[なし] : その他のポートは GA でサポートされる予定です。|
|HTTPS の FQDN 解決での ESNI のサポート|暗号化された SNI は HTTPS ハンドシェイクではサポートされていません。|現在、Firefox のみでカスタム構成を通じて ESNI がサポートされています。 回避策として、この機能を無効にすることをお勧めします。|
|クライアント証明書 (TLS)|クライアント証明書は、クライアントとサーバー間で相互の ID の信頼を構築するために使用されます。 クライアント証明書は、TLS ネゴシエーション中に使用されます。 Azure Firewall がサーバーとの接続を再ネゴシエートしていて、クライアント証明書の秘密キーにアクセスできません。|None|
|QUIC/HTTP3|QUIC は、新たなメジャー バージョンの HTTP です。 これは、80 (PLAN) と 443 (SSL) を介する UDP ベースのプロトコルです。 FQDN/URL/TLS 検査はサポートされません。|ネットワーク規則として UDP 80/443 を通るように構成します。|
|Premium ではセキュリティで保護されたハブと強制トンネリングはサポートされない|現在、Firewall Premium SKU は、セキュリティで保護されたハブのデプロイと強制トンネル構成ではサポートされていません。|修正プログラムが一般提供される予定です。|
顧客の署名入り証明書が信頼されない|顧客の署名入り証明書は、イントラネット ベースの Web サーバーから受信されると、ファイアウォールによって信頼されません。|修正プログラムが一般提供される予定です。
|TLS 検査での IDPS のアラート内で送信元と宛先の IP アドレスが間違っている。|TLS 検査を有効にしていて、IDPS によって新しいアラートが発行されるときに、表示される送信元と宛先の IP アドレスが間違っています (元の IP アドレスの代わりに内部 IP アドレスが表示されます)。|修正プログラムが一般提供される予定です。|
|HTTP (TLS 検査なし) での IDPS によるアラートの送信元 IP アドレスが間違っている。|プレーンテキスト HTTP トラフィックが使用されていて、IDPS によって新しいアラートが発行され、宛先がパブリック IP アドレスであるときに、表示される送信元の IP アドレスが間違っています (元の IP アドレスの代わりに内部 IP アドレスが表示されます)。|修正プログラムが一般提供される予定です。|
|証明書伝達|CA 証明書がファイアウォールに適用された後、証明書が有効になるまでに 5 分から 10 分かかることがあります。|修正プログラムが一般提供される予定です。|
|IDPS バイパス|IDPS バイパスは TLS 終了のトラフィックでは機能せず、送信元 IP アドレスと送信元 IP グループはサポートされません。|修正プログラムが一般提供される予定です。|
|TLS 1.3 のサポート|TLS 1.3 は部分的にサポートされています。 クライアントからファイアウォールへの TLS トンネルは TLS 1.2 に基づいており、ファイアウォールから外部 Web サーバーへは TLS 1.3 に基づいています。|更新を調査中です。|




## <a name="next-steps"></a>次のステップ

- [Azure Firewall Premium の証明書の詳細を確認する](premium-certificates.md)
- [Azure Firewall Premium プレビューをデプロイして構成する](premium-deploy.md)
- [Azure Firewall Premium プレビューへの移行](premium-migrate.md)
