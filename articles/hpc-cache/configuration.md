---
title: Azure HPC Cache 設定の構成
description: キャッシュに MTU、カスタム NTP、DNS 構成などの追加設定を構成する方法、および Azure Blob Storage ターゲットから高速スナップショットにアクセスする方法について説明します。
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/17/2021
ms.author: v-erkel
ms.openlocfilehash: 6e1e1283cb82dcb900da6473de65ef087a5cea82
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773234"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Azure HPC Cache の追加設定を構成する

Azure portal の **[ネットワーク]** ページには、いくつかの設定をカスタマイズするためのオプションがあります。 ほとんどのユーザーは、これらの設定を既定値から変更する必要はありません。

この記事では、Azure Blob Storage ターゲットに対してスナップショット機能を使用する方法についても説明します。 スナップショット機能には、構成可能な設定はありません。

設定を表示するには、Azure portal でキャッシュの **[ネットワーク]** ページを開きます。

![Azure portal の[ネットワーク] ページのスクリーンショット](media/networking-page.png)

> [!NOTE]
> 以前のバージョンのこのページには、キャッシュレベルのルート スカッシュ設定が含まれていましたが、この設定は[クライアント アクセス ポリシー](access-policies.md)に移動しました。

<!-- >> [!TIP]
> The [Managing Azure HPC Cache video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) shows the networking page and its settings. -->

## <a name="adjust-mtu-value"></a>MTU 値を調整する
<!-- linked from troubleshoot-nas article -->

**[MTU サイズ]** のラベルが付いたドロップダウン メニューを使用して、キャッシュの最大転送ユニット サイズを選択できます。

既定値は 1,500 バイトですが、1,400 に変更できます。

> [!NOTE]
> キャッシュの MTU サイズを小さくする場合は、キャッシュと通信するクライアントと記憶域システムの MTU 設定が同じか、小さい値になっていることを確認します。

キャッシュの MTU 値を小さくすると、キャッシュのネットワークの残りの部分でパケット サイズの制限を回避するのに役立ちます。 たとえば、一部の VPN では、フルサイズの 1,500 バイトのパケットを正常に転送できません。 VPN 経由で送信されるパケットのサイズを小さくすると、その問題が解消される可能性があります。 ただし、キャッシュの MTU 設定を下げると、そのキャッシュと通信するその他のコンポーネント (クライアントやストレージ システムなど) の MTU 設定も下げて、通信の問題を回避する必要があることにご注意ください。

他のシステム コンポーネントの MTU 設定を変更したくない場合は、キャッシュの MTU 設定を下げないでください。 VPN パケット サイズの制限を回避するための解決策は他にもあります。 この問題の診断と解決に関する詳細については、NAS のトラブルシューティング記事の「[VPN パケット サイズの制限を調整する](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions)」を参照してください。

Azure 仮想ネットワークの MTU 設定の詳細については、「[Azure VM の TCP/IP パフォーマンス チューニング](../virtual-network/virtual-network-tcpip-performance-tuning.md)」をご覧ください。

## <a name="customize-ntp"></a>NTP をカスタマイズする

キャッシュは既定で、Azure ベースのタイム サーバー time.microsoft.com を使用します。 キャッシュで別の NTP サーバーが使用されるようにするには、 **[NTP の構成]** セクションで指定します。 完全修飾ドメイン名または IP アドレスを使用します。

## <a name="set-a-custom-dns-configuration"></a>カスタム DNS 構成を設定する

> [!CAUTION]
> キャッシュの DNS 構成は、必要でないかぎり変更しないでください。 構成を誤ると、深刻な影響が発生する場合があります。 構成で Azure サービス名を解決できない場合、HPC キャッシュ インスタンスは永続的に到達できなくなります。

Azure HPC Cache は、安全で便利な Azure DNS システムを使用するように自動的に構成されます。 ただし、いくつかの特殊な構成では、Azure システムではなく、オンプレミスの別の DNS システムをキャッシュで使用することが求められる場合があります。 このようなシステムを指定するには、 **[ネットワーク]** ページの **[DNS の構成]** セクションを使用します。

Azure の担当者に確認するか、Microsoft サービスおよびサポートに問い合わせて、カスタム キャッシュ DNS 構成を使用する必要があるかどうかを判断してください。

使用する Azure HPC Cache の独自のオンプレミス DNS システムを構成する場合は、構成で Azure サービスの Azure エンドポイント名を解決できることを確認する必要があります。 特定の名前解決要求を Azure DNS または必要に応じて別のサーバーに転送するように、カスタム DNS 環境を構成する必要があります。

Azure HPC Cache に使用する前に、DNS 構成でこれらの項目を正常に解決できることを確認します。

* ``*.core.windows.net``
* 証明書失効リスト (CRL) のダウンロードとオンライン証明書ステータス プロトコル (OCSP) の検証サービス。 この [AZURE TLS 記事](../security/fundamentals/tls-certificate-changes.md)の最後にある[ファイアウォール規則の項目](../security/fundamentals/tls-certificate-changes.md#will-this-change-affect-me)にはリスト一部分がありますが、すべての要件を理解するには、Microsoft の技術担当者に問い合わせてください。
* NTP サーバーの完全修飾ドメイン名 (time.microsoft.com またはカスタム サーバー)

キャッシュ用にカスタム DNS サーバーを設定する必要がある場合は、指定されたフィールドを使用します。

* **[DNS 検索ドメイン]** (省略可能) - 検索ドメインを入力します (例: ``contoso.com``)。 1 つの値を指定することも、空白のままにすることもできます。
* **[DNS サーバー]** - 最大 3 台の DNS サーバーを入力します。 IP アドレスで指定します。

<!-- 
  > [!NOTE]
  > The cache will use only the first DNS server it successfully finds. -->

DNS セットアップを運用環境で使用する前に、テスト キャッシュを使用して確認および調整することを検討してください。

### <a name="refresh-storage-target-dns"></a>ストレージ ターゲット DNS を更新する

DNS サーバーが IP アドレスを更新すると、関連付けられている NFS ストレージ ターゲットが一時的に使用できなくなります。 [ストレージ ターゲットの編集](hpc-cache-edit-storage.md#update-ip-address-custom-dns-configurations-only)に関するページで、カスタム DNS システム IP アドレスを更新する方法を参照してください。

## <a name="view-snapshots-for-blob-storage-targets"></a>BLOB ストレージ ターゲットのスナップショットを表示する

Azure HPC Cache では、Azure Blob Storage ターゲットのストレージ スナップショットが自動的に保存されます。 スナップショットは、バックエンド ストレージ コンテナーのコンテンツに対してクイック リファレンス ポイントを提供します。

スナップショットは、データ バックアップに代わるものではなく、キャッシュ データの状態に関する情報は含まれていません。

> [!NOTE]
> このスナップショット機能は、NetApp または Isilon ストレージ ソフトウェアに含まれているスナップショット機能とは異なります。 これらのスナップショットの実装によって、スナップショットを取得する前に、キャッシュからバックエンド ストレージ システムに変更がフラッシュされます。
>
> 効率を高めるために、Azure HPC Cache スナップショットでは、最初に変更をフラッシュせず、BLOB コンテナーに書き込まれたデータのみが記録されます。 このスナップショットはキャッシュ データの状態を表していないので、最近の変更が含まれていない可能性があります。

この機能は、Azure Blob Storage ターゲットでのみ使用でき、その構成を変更することはできません。

スナップショットは 8 時間ごと (UTC 0:00、08:00、16:00) に取得されます。

Azure HPC Cache では、毎日、毎週、毎月のスナップショットが、新しいスナップショットに置き換えられるまで保存されます。 スナップショットの保持期間の制限は次のとおりです。

* 毎日のスナップショットは最大 20 個
* 毎週のスナップショットは最大 8 個
* 毎月のスナップショットは最大 3 個

マウントされた Blob ストレージ ターゲットのルートにある `.snapshot` ディレクトリからスナップショットにアクセスします。
