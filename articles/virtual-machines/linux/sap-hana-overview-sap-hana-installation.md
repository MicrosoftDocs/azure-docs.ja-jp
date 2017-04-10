---
title: "SAP HANA on Azure (L インスタンス) への SAP HANA のインストール | Microsoft Docs"
description: "SAP HANA on Azure (L インスタンス) への SAP HANA のインストール方法を説明します。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: 0d998b5347a9da6dd209b0ba106792ab5c34164d
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>SAP HANA on Azure (L インスタンス) のインストールと構成の方法

SAP HANA は自分でインストールする必要があります。新しい SAP HANA on Azure (L インスタンス) サーバーのハンドオフ後、すぐにインストールを実施できます。 ただし、SAP ポリシーに従い、SAP HANA のインストールは、認定 SAP HANA インストーラー (SAP HANA インストールの認定試験である Certified SAP Technology Associate の合格者) または SAP 認定システム インテグレーター (SI) によって実施される必要があります。

サーバー側の SAP HANA とクライアント側の SAP HANA に関しては、接続に関して特別に注意する必要がある考慮事項があります。 多くの場合、SAP HANA サーバーはそれ自体の IP アドレスをクライアントに送信します。その IP アドレスはキャッシュされ、それ以降の接続試行に使用されます。 SAP HANA on Azure (L インスタンス) は、テナント ネットワークで使用する内部サーバー IP アドレスを特定の Azure VNet 用に提供された IP アドレス範囲に NAT するので、SAP HANA データベース サーバーは &quot;内部&quot; IP アドレス範囲を送信する仕様となっています。 たとえば、ホスト名を解決する際、NAT された IP アドレスを提供する SAP HANA の代わりに、キャッシュされた内部 IP アドレスが使用されます。 したがって、SAP HANA クライアント (ODBC、JDBC など) を使用するアプリケーションは、この IP アドレスとは接続できません。 NAT された IP アドレスをクライアントに反映するよう SAP HANA サーバーに指示するためには、SAP HANA グローバル システム構成ファイル (global.ini) を編集する必要があります。

global.ini に次のコードを追加します (直接または SAP HANA Studio を使用)。
```
[public\_hostname\_resolution]
use\_default\_route = no
map\_<hostname of SAP HANA on Azure (Large Instances) tenant> = <NAT IP Address (IP address that can be accessed in Azure)>
```
詳細と例については、『[SAP HANA Administration Guide (SAP HANA 管理ガイド)](http://help.sap.com/hana/sap_hana_administration_guide_en.pdf)』の「_Mapping Host Names for Database Client Access (データベース クライアント アクセスを目的としたホスト名マッピング)_」セクションを参照してください。

クライアント側 (Azure 内の SAP アプリケーション サーバーのクライアント) では、/etc/hosts ファイル (Linux) または /system32/drivers/etc/hosts (Windows Server) を編集し、SAP HANA on Azure (L インスタンス) テナントのホスト名のエントリと、各エントリの NAT された IP アドレスを含めます。

>[!NOTE] 
>SAP HANA データベース クライアントをインストールする際、SAP HANA on Azure (L インスタンス) に接続するときにエラーが発生した場合は、ホスト名の代わりに HANA L インスタンス テナントの NAT IP アドレスを暗黙的に使用してください。

## <a name="storage"></a>Storage

SAP HANA on Azure (L インスタンス) のストレージ レイアウトは、SAP により推奨されたベスト プラクティスを使用し、SAP HANA on Azure サービス管理によって構成されています。詳細はホワイト ペーパー『[SAP HANA Storage Requirements (SAP HANA ストレージ要件)](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)』を参照してください。

異なる HANA L インスタンス向けのストレージの具体的な構成は、次のようになります。

| メモリ サイズ | HANA/data | HANA/log | HANA/shared | HANA/log/backups |
| --- | --- | --- | --- | --- |
| 768 GB | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,536 GB | 3,456 GB | 768 GB | 1,024 GB | 768 GB |
| 3,072 GB | 7,680 GB | 1,536 GB | 1,024 GB | 1,536 GB |

さらに、お客様は追加のストレージ容量を 1 TB 単位で購入することができます。 この追加のストレージは、新しいボリュームとして HANA L インスタンスに追加できます。

L インスタンス スタンプ内のストレージ コントローラーとノードは NTP サーバーに同期されます。 SAP HANA on Azure (L インスタンス) のユニットと Azure VM を NTP サーバーに対して同期する際は、Azure または L インスタンス スタンプ内のコンピューティング ユニットとインフラストラクチャの間で大きな時間のずれが発生しないようにする必要があります。

## <a name="operating-system"></a>オペレーティング システム

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana) は、SAP HANA on Azure (L インスタンス) 用にインストールする Linux ディストリビューションです。 このディストリビューションでは、SAP 特有の機能を &quot;すぐに使う&quot; ことができます (SAP on SLES を効果的に実行するための事前設定されたパラメーターを含む)。

SUSE の Web サイトの[「Resource Library (リソース ライブラリ)」内の「White Papers (ホワイト ペーパー)」](https://www.suse.com/products/sles-for-sap/resource-library#white-papers)と、SAP Community Network (SCN) の「[SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)」を参照して、SAP HANA on SLES のデプロイに関連するいくつかの便利なリソース (High Availability の設定や、SAP 運用向けのセキュリティ強化など) を確認できます。

その他の便利な SLES 関連のリンクは次のとおりです。

- [SAP HANA on SUSE Linux のサイト](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12 (SAP のベスト プラクティス: レプリケーションのエンキュー – SAP NetWeaver on SUSE Linux Enterprise 12)](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (SLES 12 for SAP Applications を含む)

SAP HANA on SLES 12 SP1 の実装に適用できる SAP サポート ノートは次のとおりです。

- [SAP サポート ノート #1944799 – SLES オペレーティング システムのインストールに関する SAP HANA ガイドライン](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [SAP サポート ノート #2205917 – SAP HANA DB: 推奨される SLES 12 for SAP Applications 向け OS 設定](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP サポート ノート #1984787 – SUSE Linux Enterprise Server 12: インストールに関する注意事項](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP サポート ノート #171356 – SAP Software on Linux: 一般情報](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP サポート ノート #1391070 – Linux UUID ソリューション](https://launchpad.support.sap.com/#/notes/1391070)

## <a name="time-synchronization"></a>時刻同期

SAP は、SAP システムを構成するさまざまなコンポーネントの時間のずれに非常に敏感です。 SAP (Basis) を長く使ってきた経験がある場合、ZDATE\_LARGE\_TIME\_DIFF というエラー タイトルで発生する SAP ABAP のショート ダンプは見慣れたものかもしれません。というのも、これらのショート ダンプは、異なるサーバーまたは VM のシステム時刻のずれが大きくなりすぎたときに発生するからです。

SAP HANA on Azure (L インスタンス) の場合、Azure で行われる時刻同期は、L インスタンス スタンプ内のコンピューティング ユニットには適用されません。 Azure (VM 上) でネイティブに SAP アプリケーションを実行する場合は、これは該当しません。Azure ではシステム時刻が適切に同期されるためです。 結果として、別のタイム サーバーを、Azure VM 上で実行される SAP アプリケーション サーバーと、HANA L インスタンス上で実行される SAP HANA データベース インスタンスで使用できるように設定する必要があります。 L インスタンス スタンプ内のストレージ インフラストラクチャの時刻は NTP サーバーと同期されます。



