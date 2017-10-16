---
title: "Azure Active Directory の Cloud App Discovery のセットアップ | Microsoft Docs"
description: "Cloud App Discovery でアプリケーションを検索および管理する利点と機能について説明します。"
services: active-directory
keywords: "Cloud App Discovery, アプリケーションの管理"
documentationcenter: 
author: curtand
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: curtand
ms.reviewer: nigu
ms.openlocfilehash: af54b77dc985f2ca6abeab29165278dfa598f5e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-cloud-app-discovery-in-azure-ad"></a>Azure Active Directory の Cloud App Discovery のセットアップ

Azure AD の新しい Cloud App Discovery 拡張機能が、Azure Active Directory Premium P1 ライセンスで利用できるようになりました。 これらの拡張機能は、Microsoft Cloud App Security との統合に基づいています。 Cloud App Discovery では、15,000 個以上のクラウド アプリの Cloud App Security カタログとトラフィック ログを比較して、クラウドの使用状況とシャドウ IT に関する継続的な情報を提供します。 

## <a name="prerequisites"></a>前提条件

組織でこの製品を使用するには、Azure AD Premium P1 ライセンスが必要です。 詳細については、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」をご覧ください。

Cloud App Discovery をセットアップするには、Azure Active Directory のグローバル管理者またはセキュリティ閲覧者である必要があります。 管理者ロールが割り当てられているユーザーは、組織がサブスクライブしているすべてのクラウド アプリで同じアクセス許可を持ちます。

## <a name="setup-steps"></a>セットアップの手順

1. ログの形式を調べてログから Cloud App Discovery に提供されている情報が有用であるかどうかを確認できるように、[スナップショット レポートをセットアップ](cloudappdiscovery-set-up-snapshots.md)します。 スナップショット レポートでは、ファイアウォールやプロキシ サーバーから手動でアップロードしたトラフィック ログをアドホックに可視化することもできます。

2. Cloud App Security のログ コレクターを使用してネットワークから転送されるすべてのログを分析するように、[継続的レポートをセットアップ](https://docs.microsoft.com/cloud-app-security/discovery-docker)します。 これらのレポートを使用して、新しいアプリや使用傾向を特定できます。

3. ログが現時点でサポートされていない場合は、Cloud App Discovery でログを分析できるように[カスタム ログ パーサーをセットアップ](https://docs.microsoft.com/en-us/cloud-app-security/custom-log-parser)します。
  
## <a name="log-processing-flow"></a>ログ処理フロー

データ量に応じて、レポートの生成には数分から数時間かかることがあります。 フローは次のとおりです。

* **アップロード**: ネットワークの Web トラフィック ログがポータルにアップロードされます。
* **解析**: Cloud App Security が、データ ソースごとに専用のパーサーを使用してトラフィック ログを解析し、トラフィック ログからトラフィック データを抽出します。
* **分析**: トラフィック データを Cloud App Security カタログと比較して分析することで、15,000 個以上のクラウド アプリが識別されます。 分析の一環として、アクティブ ユーザーと IP アドレスも特定されます。
* **レポートの生成**: Cloud App Security は、ログ ファイルから抽出されたデータのレポートを生成し、Cloud App Discovery で使用できるようにします。

> [!NOTE]
> * 継続的レポートのデータは 1 日に 2 回分析されます。
> * データは、アップロードされる前にログ コレクターによって圧縮されます。 ログ コレクターの送信トラフィックは、受信したトラフィック ログのサイズの約 10% になります。

## <a name="using-traffic-logs-for-cloud-app-discovery"></a>Cloud App Discovery のトラフィック ログの使用

Cloud App Discovery では、トラフィック ログのデータを使用します。 ログに追加できる詳細情報が多いほど、可視性が向上します。 Cloud App Discovery では、次の属性を含む Web トラフィック データを必要とします。

* トランザクションの日付
* 送信元 IP アドレス
* 送信元ユーザー (**推奨**)
* 宛先 IP アドレス
* 宛先 URL (**推奨**) (URL を使用すると、IP アドレスよりもクラウド アプリの検出の精度が向上します)
* データの総量
* アップロードまたはダウンロードされたデータの量 (クラウド アプリの使用状況のパターンに関する洞察が得られます)
* 実行されたアクション (許可/ブロック)

Cloud App Discovery では、ログに含まれていない属性を表示したり、分析したりすることはできません。 たとえば、**Cisco ASA Firewall** の標準のログ形式には、**トランザクションごとにアップロードされたバイト数**、**ユーザー名**、**ターゲット URL** は含まれておらず、宛先 IP アドレスだけが含まれています。 そのため、このデータ ソースではクラウド アプリの可視性が低下する可能性があります。 Cisco ASA Firewall では、情報レベルを 6.1 に設定します。

Cloud App Discovery レポートを正常に生成するには、トラフィック ログが次の条件を満たしている必要があります。

1.  データ ソースが、[サポートされているファイアウォールまたはプロキシ サーバー](#supported-firewalls-and-proxies)である。
2.  ログ形式が必要な標準の形式に一致する。 これはアップロード時に確認されます。 ログ形式を最適化する方法については、[Cloud App Discovery のスナップショット レポートの作成](cloudappdiscovery-set-up-snapshots.md)に関する記事をご覧ください。
3.  イベントが 90 日以上前のものではない。
4.  ログ ファイルが有効であり、送信トラフィック情報が含まれている。

## <a name="supported-firewalls-and-proxy-servers"></a>サポートされているファイアウォールとプロキシ サーバー

* Barracuda - Web アプリ ファイアウォール (W3C)
* Blue Coat Proxy SG - アクセス ログ (W3C)
* Check Point
* Cisco ASA Firewall (情報レベルを 6 に設定)
* Cisco IronPort WSA
* Cisco ScanSafe
* Cisco Meraki - URL ログ
* Clavister NGFW (Syslog)
* Dell SonicWall
* Fortinet FortiGate
* Juniper SRX
* Juniper SSG
* McAfee Secure Web Gateway
* Microsoft Forefront Threat Management Gateway (W3C)
* Palo Alto Firewall シリーズ
* Sophos SG
* Sophos Cyberoam
* Squid (共通)
* Squid (ネイティブ)
* Websense - Web Security Solutions - 調査の詳細レポート (CSV)
* Websense - Web Security Solutions - インターネットのアクティビティ ログ (CEF)
* Zscaler

> [!NOTE]
> Cloud App Discovery では、IPv4 と IPv6 の両方のアドレスをサポートしています。

ログがサポートされていない場合は、**[データ ソース]** として **[その他]** を選択し、デバイスとアップロードしようとしているログを指定します。 ログは、Cloud App Security クラウド アナリスト チームによって確認されます。 ご使用のログの種類のサポートが追加されると通知されます。ただし、ご使用のログ形式に適合するカスタム パーサーを定義することもできます。 詳細については、「[カスタム ログ パーサーの使用](https://docs.microsoft.com/cloud-app-security/custom-log-parser)」をご覧ください。

## <a name="data-attributes-according-to-vendor-documentation"></a>(ベンダーのドキュメントに基づく) データ属性

| データ ソース         | 対象のアプリの URL | 対象のアプリの IP アドレス | ユーザー名 | 送信元 IP アドレス | 合計トラフィック | アップロードされたバイト数 |
|-----------------------------------------|----------------|---------------|----------|-----------|---------------|----------------|
| Barracuda                               | **はい**        | **はい**       | **はい**  | **はい**   | いいえ            | いいえ             |
| Blue Coat                               | **はい**        | いいえ            | **はい**  | **はい**   | **はい**       | **はい**        |
| チェックポイント                              | いいえ             | **はい**       | いいえ       | **はい**   | いいえ            | いいえ             |
| Cisco ASA                               | いいえ             | **はい**       | いいえ       | **はい**   | **はい**       | いいえ             |
| Cisco FWSM                              | いいえ             | **はい**       | いいえ       | **はい**   | **はい**       | いいえ             |
| Cisco IronPort WSA                      | **はい**        | **はい**       | **はい**  | **はい**   | **はい**       | **はい**        |
| Cisco Meraki                            | **はい**        | **はい**       | いいえ       | **はい**   | いいえ            | いいえ             |
| Clavister NGFW (Syslog)                 | **はい**        | **はい**       | **はい**  | **はい**   | **はい**       | **はい**        |
| Dell SonicWALL                          | **はい**        | **はい**       | いいえ       | **はい**   | **はい**       | **はい**        |
| FortiGate                               | いいえ             | **はい**       | いいえ       | **はい**   | **はい**       | **はい**        |
| Juniper SRX                             | いいえ             | **はい**       | いいえ       | **はい**   | **はい**       | **はい**        |
| Juniper SSG                             | いいえ             | **はい**       | いいえ       | **はい**   | **はい**       | **はい**        |
| McAfee SWG                              | **はい**        | いいえ            | いいえ       | **はい**   | **はい**       | **はい**        |
| MS TMG                                  | **はい**        | いいえ            | **はい**  | **はい**   | **はい**       | **はい**        |
| Palo Alto Networks                      | **はい**        | **はい**       | **はい**  | **はい**   | **はい**       | **はい**        |
| Sophos                                  | **はい**        | **はい**       | **はい**  | **はい**   | **はい**       | いいえ             |
| Squid (共通)                          | **はい**        | いいえ            | **はい**  | **はい**   | いいえ            | **はい**        |
| Squid (ネイティブ)                          | **はい**        | いいえ            | **はい**  | **はい**   | いいえ            | **はい**        |
| Websense - 調査レポート (CSV)   | **はい**        | **はい**       | **はい**  | **はい**   | **はい**       | **はい**        |
| Websense - インターネットのアクティビティ ログ (CEF)  | **はい**        | **はい**       | **はい**  | **はい**   | **はい**       | **はい**        |
| Zscaler                                 | **はい**        | **はい**       | **はい**  | **はい**   | **はい**       | **はい**        |


## <a name="next-steps"></a>次のステップ
次のリンクを使用して、Azure AD の Cloud App Discovery のセットアップを続行します。

* [スナップショット レポートの作成](cloudappdiscovery-set-up-snapshots.md)
* [継続的なレポートの構成](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [カスタム ログ パーサーを使用する](https://docs.microsoft.comcommit/cloud-app-security/custom-log-parser)