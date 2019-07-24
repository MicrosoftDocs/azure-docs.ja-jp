---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/14/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 36b3fcfa90b5b1de9c9d3262da1f3e519cc99c19
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2019
ms.locfileid: "53444237"
---
### <a name="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus"></a>カスタム IPsec/IKE ポリシーはすべての Azure VPN Gateway SKU でサポートされていますか。
カスタム IPsec/IKE ポリシーは、Azure の **VpnGw1、VpnGw2、VpnGw3、Standard** および **HighPerformance** VPN ゲートウェイでサポートされています。 **Basic** SKU はサポート**されていません**。

### <a name="how-many-policies-can-i-specify-on-a-connection"></a>1 つの接続に対してポリシーはいくつ指定できますか。
ある特定の接続に対して指定できるポリシーの組み合わせは ***1 つ***だけです。

### <a name="can-i-specify-a-partial-policy-on-a-connection-for-example-only-ike-algorithms-but-not-ipsec"></a>接続に対してポリシーを部分的に指定することはできますか  (IKE アルゴリズムのみ指定し、IPsec は指定しないなど)。
いいえ。IKE (メイン モード) と IPsec (クイック モード) の両方について、すべてのアルゴリズムとパラメーターを指定する必要があります。 ポリシーを部分的に指定することはできません。

### <a name="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy"></a>カスタム ポリシーでサポートされるアルゴリズムとキーの強度を教えてください。
以下の表は、サポートされている暗号アルゴリズムと、ユーザーが構成できるキーの強度を一覧にしたものです。 各フィールドについて、オプションを 1 つ選択する必要があります。

| **IPsec/IKEv2**  | **オプション**                                                                   |
| ---              | ---                                                                           |
| IKEv2 暗号化 | AES256、AES192、AES128、DES3、DES                                             |
| IKEv2 整合性  | SHA384、SHA256、SHA1、MD5                                                     |
| DH グループ         | DHGroup24、ECP384、ECP256、DHGroup14 (DHGroup2048)、DHGroup2、DHGroup1、なし |
| IPsec 暗号化 | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128、DES3、DES、なし      |
| IPsec 整合性  | GCMAES256、GCMAES192、GCMAES128、SHA256、SHA1、MD5                            |
| PFS グループ        | PFS24、ECP384、ECP256、PFS2048、PFS2、PFS1、なし                              |
| QM SA の有効期間   | 秒 (整数: **最小 300**/既定値 27,000 秒)<br>キロバイト数 (整数: **最小 1024**/既定値 102,400,000 キロバイト)           |
| トラフィック セレクター | UsePolicyBasedTrafficSelectors ($True/$False - 既定値 $False)                 |
|                  |                                                                               |

> [!IMPORTANT]
> 1. DHGroup2048 および PFS2048 は、IKE および IPsec PFS の Diffie-Hellman グループ **14** と同じです。 完全なマッピングについては、[Diffie-Hellman グループ](#DH)に関するセクションを参照してください。
> 2. GCMAES アルゴリズムでは、IPSec 暗号化と整合性の両方に同じ GCMAES アルゴリズムとキーの長さを指定する必要があります。
> 3. Azure VPN ゲートウェイでは、IKEv2 メイン モード SA の有効期間が 28,800 秒に固定されます。
> 4. QM SA の有効期間は省略可能なパラメーターです。 指定されていない場合は、既定値の 27,000 秒 (7.5 時間) および 102,400,000 キロバイト (102 GB) が使用されます。
> 5. UsePolicyBasedTrafficSelector は接続に関する省略可能なパラメーターです。 "UsePolicyBasedTrafficSelectors" については、次の FAQ 項目を参照してください。

### <a name="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations"></a>Azure VPN Gateway のポリシーとオンプレミス VPN デバイスの構成とが、すべて一致してる必要はありますか。
ご使用のオンプレミス VPN デバイスの構成は、Azure IPsec/IKE ポリシーで指定した次のアルゴリズムおよびパラメーターと一致している (または含んでいる) 必要があります。

* IKE 暗号化アルゴリズム
* IKE 整合性アルゴリズム
* DH グループ
* IPsec 暗号化アルゴリズム
* IPsec 整合性アルゴリズム
* PFS グループ
* トラフィック セレクター (*)

SA の有効期間は、ローカルの指定のみとなります。一致している必要はありません。

**UsePolicyBasedTrafficSelectors** を有効にした場合は、Azure Virtual Network プレフィックスとの間で双方向に、オンプレミス ネットワーク (ローカル ネットワーク ゲートウェイ) プレフィックスのすべての組み合わせに対応するトラフィック セレクターがご使用の VPN デバイスに定義されている必要があります (any-to-any は不可)。 たとえば、オンプレミス ネットワークのプレフィックスが 10.1.0.0/16 と 10.2.0.0/16 で、仮想ネットワークのプレフィックスが 192.168.0.0/16 と 172.16.0.0/16 である場合、次のトラフィック セレクターを指定する必要があります。
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

詳細については、[複数のオンプレミスのポリシーベース VPN デバイスへの接続](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)に関する記事を参照してください。

### <a name ="DH"></a>どの Diffie-Hellman グループがサポートされていますか。
次の表に、IKE (DHGroup) と IPsec (PFSGroup) でサポートされている Diffie-Hellman グループを示します。

| **Diffie-Hellman グループ**  | **DHGroup**              | **PFSGroup** | **キーの長さ** |
| ---                       | ---                      | ---          | ---            |
| 1                         | DHGroup1                 | PFS1         | 768 ビット MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 ビット MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 ビット MODP  |
| 19                        | ECP256                   | ECP256       | 256 ビット ECP    |
| 20                        | ECP384                   | ECP384       | 384 ビット ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 ビット MODP  |
|                           |                          |              |                |

詳細については、[RFC3526](https://tools.ietf.org/html/rfc3526) および [RFC5114](https://tools.ietf.org/html/rfc5114) を参照してください。

### <a name="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways"></a>Azure VPN ゲートウェイに対する既定の IPsec/IKE ポリシー セットは、カスタム ポリシーによって置き換えられるのでしょうか。
はい。特定の接続に対してカスタム ポリシーが指定されると、Azure VPN ゲートウェイは、IKE イニシエーターと IKE レスポンダーのどちらとして機能する場合も、その接続に対してそのポリシーのみを使用するようになります。

### <a name="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected"></a>カスタム IPsec/IKE ポリシーを削除した場合、接続は保護されなくなるのですか。
いいえ。接続は引き続き IPsec/IKE によって保護されます。 接続からカスタム ポリシーが削除されると、Azure VPN ゲートウェイは、[既定の IPsec/IKE 候補リスト](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md)に復帰し、オンプレミス VPN デバイスとの間で再度 IKE ハンドシェイクを開始します。

### <a name="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection"></a>IPsec/IKE ポリシーを追加または更新した場合、VPN 接続は中断されますか。
はい。短時間 (数秒) の中断が生じる可能性があります。Azure VPN ゲートウェイは、既存の接続を破棄します。そのうえで、IKE ハンドシェイクを再開し、新しい暗号アルゴリズムとパラメーターで IPsec トンネルを再度確立します。 中断を最小限に抑えるために、対応するアルゴリズムおよびキーの強度を使ってオンプレミス VPN デバイスが構成されていることを確認してください。

### <a name="can-i-use-different-policies-on-different-connections"></a>接続ごとに異なるポリシーを使用することはできますか。
はい。 カスタム ポリシーは接続ごとに適用されます。 接続ごとに異なる IPsec/IKE ポリシーを作成して適用することができます。 また、接続の一部に対してカスタム ポリシーを適用することもできます。 それ以外の部分では、Azure の既定の IPsec/IKE ポリシー セットが使用されます。

### <a name="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well"></a>カスタム ポリシーは VNet 間接続にも使用できますか。
はい。カスタム ポリシーは、IPsec クロスプレミス接続と VNet 間接続のどちらにでも適用できます。

### <a name="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources"></a>VNet 間接続の両方のリソースに同じポリシーを指定する必要はありますか。
はい。 VNet 間トンネルは、Azure 内の 2 つの接続リソースで構成されます (1 方向につき 1 つ)。 両方の接続リソースに同じポリシーを適用する必要があります。それ以外の場合、VNet 間接続は確立されません。

### <a name="does-custom-ipsecike-policy-work-on-expressroute-connection"></a>ExpressRoute 接続でカスタム IPsec/IKE ポリシーは機能しますか。
いいえ。 IPsec/IKE ポリシーは、Azure VPN ゲートウェイを介した VNet 間接続と S2S VPN 接続でのみ機能します。

### <a name="where-can-i-find-more-configuration-information-for-ipsec"></a>IPsec の詳細な構成情報はどこで入手できますか。
[S2S または VNet-to-VNet 接続の IPsec/IKE ポリシーの構成](../articles/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell.md)に関するページを参照してください
