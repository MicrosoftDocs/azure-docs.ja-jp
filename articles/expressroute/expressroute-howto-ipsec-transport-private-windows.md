---
title: Azure ExpressRoute プライベート ピアリング:IPsec トランスポート モードを構成する - Windows ホスト
description: GPO と OU を使用して、ExpressRoute プライベート ピアリング経由で Azure Windows VM とオンプレミス Windows ホストの間の IPsec トランスポート モードを有効にする方法。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: cfcc515787cee2bc90a2100e84337a3c96219d8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98017274"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>ExpressRoute プライベート ピアリング用の IPsec トランスポート モードを構成する

この記事では、トランスポート モードを使用して ExpressRoute プライベート ピアリング経由で IPsec トンネルを作成する方法について説明します。 トンネルは、Windows を実行している Azure VM とオンプレミスの Windows ホストの間に作成されます。 この記事に含まれるこの構成手順では、グループ ポリシー オブジェクトを使用します。 一方、組織単位 (OU) とグループ ポリシー オブジェクト (GPO) を使用せずにこの構成を作成することもできます。 OU と GPO を組み合わせることにより、セキュリティ ポリシーの制御が簡素化され、すばやくスケールアップできるようになります。 この記事の手順では、既に Active Directory 構成があり、OU と GPO の使用に慣れていることを前提としています。

## <a name="about-this-configuration"></a>この構成について

次の手順の構成では、ExpressRoute プライベート ピアリングで 1 つの Azure Virtual Network (VNet) を使用します。 ただし、この構成は他の Azure VNet やオンプレミス ネットワークにまたがることができます。 この記事では、Azure VM やオンプレミス ホストのグループに適用できる IPsec 暗号化ポリシーを定義する方法について説明します。 これらの Azure VM やオンプレミス ホストは、同じ OU に含まれています。 Azure VM (vm1 と vm2) と、宛先ポート 8080 を使用する HTTP トラフィック専用のオンプレミス host1 の間の暗号化を構成します。 必要に応じて、さまざまな種類の IPsec ポリシーを作成することができます。

### <a name="working-with-ous"></a>OU の使用 

OU に関連付けられているセキュリティ ポリシーは、GPO を介してコンピューターにプッシュされます。 単一ホストにポリシーを適用するよりも、OU を使用するいくつかの利点を以下に示します。

* ポリシーを OU に関連付けることにより、同じ OU に属しているコンピューターで同じポリシーを取得することが保証されます。
* OU に関連付けられているセキュリティ ポリシーを変更すると、OU 内のすべてのホストに変更が適用されます。

### <a name="diagrams"></a>ダイアグラム

次のダイアグラムは、相互接続と割り当てられている IP アドレス空間を示しています。 Azure VM とオンプレミス ホストでは、Windows 2016 が実行されています。 Azure VM とオンプレミス host1 は同じドメインの一部です。 Azure VM とオンプレミス ホストでは、DNS を使用して正しく名前を解決できます。

[![1]][1]

このダイアグラムは、ExpressRoute プライベート ピアリングでの転送中の IPsec トンネルを示しています。

[![4]][4]

### <a name="working-with-ipsec-policy"></a>IPsec ポリシーの使用

Windows では、暗号化は IPsec ポリシーに関連付けられています。 IPsec ポリシーによって、セキュリティで保護される IP トラフィックと、IP パケットに適用されるセキュリティ メカニズムが決定されます。
**IPSec ポリシー** を構成する項目は、**フィルター一覧**、**フィルター操作**、および **セキュリティ規則** です。

IPsec ポリシーを構成する場合、次の IPsec ポリシーの用語を理解することが重要です。

* **IPsec ポリシー:** 規則のコレクション。 特定の時点でアクティブにできる ("割り当てることができる") のは、1 つのポリシーのみです。 各ポリシーには 1 つ以上の規則を含めることができ、すべて同時にアクティブにできます。 特定の時点でコンピューターに割り当てることができるアクティブな IPsec ポリシーは 1 つのみです。 しかし、IPsec ポリシー内には、さまざまな状況で行われる場合がある複数の操作を定義することができます。 各 IPsec 規則セットは、規則が適用されるネットワーク トラフィックの種類に影響するフィルター一覧に関連付けられています。

* **フィルター一覧:** フィルター一覧は 1 つ以上のフィルターの集まりです。 1 つの一覧に複数のフィルターを含めることができます。 フィルターは、次の条件に基づいて、通信がブロックされるか、許可されるか、またはセキュリティによって保護されるかを定義します。IP アドレス範囲、プロトコル、または特定のポート。 各フィルターを特定の条件セットと一致させます。たとえば、特定のサブネットから所定の宛先ポートの特定のコンピューターに送信されたパケットなどです。 ネットワーク条件がそれらのフィルターの 1 つ以上と一致すると、フィルター一覧がアクティブになります。 各フィルターは、特定のフィルター一覧内で定義されます。 フィルターをフィルター一覧間で共有することはできません。 しかし、指定されたフィルター一覧をいくつかの IPsec ポリシーに組み込むことができます。 

* **フィルター操作:** セキュリティ メソッドでは、一連のセキュリティ アルゴリズム、プロトコル、および IKE ネゴシエーション中にコンピューターで提供されるキーが定義されます。 フィルター操作は、優先順にランク付けされる、セキュリティ メソッドの一覧です。  コンピューターで IPsec セッションがネゴシエートされると、フィルター操作一覧に格納されているセキュリティ設定に基づいて、提案が受け入れられるか、送信されます。

* **セキュリティ規則:** 規則によって、IPsec ポリシーでの通信の保護方法とそのタイミングが管理されます。 **フィルター一覧** と **フィルター操作** を使用して、IPsec 接続を構築するための IPsec 規則が作成されます。 各ポリシーには 1 つ以上の規則を含めることができ、すべて同時にアクティブにできます。 各規則には、IP フィルターの一覧と、そのフィルター一覧と一致したときに行われるセキュリティ操作のコレクションが含まれます。
  * IP フィルター操作
  * 認証方法
  * IP トンネルの設定
  * 接続の種類

[![5]][5]

## <a name="before-you-begin"></a>開始する前に

次の前提条件が満たされていることを確認します。

* グループ ポリシー設定を実装するために使用できる、機能している Active Directory 構成が必要です。 GPO の詳細については、「[Group Policy Objects](/previous-versions/windows/desktop/Policy/group-policy-objects)」 (グループ ポリシー オブジェクト) を参照してください。

* アクティブな ExpressRoute 回線が必要です。
  * ExpressRoute 回線の作成については、[ExpressRoute 回線の作成](expressroute-howto-circuit-arm.md)に関するページを参照してください。 
  * 接続プロバイダーによって回線が有効にされていることを確認します。 
  * 回線用に Azure プライベート ピアリングが構成されていることを確認します。 ルーティング手順については、 [ルーティングの構成](expressroute-howto-routing-arm.md) に関する記事を参照してください。 
  * VNet と仮想ネットワーク ゲートウェイが作成され、完全にプロビジョニングされていることを確認します。 指示に従って [ExpressRoute 用の仮想ネットワーク ゲートウェイを作成](expressroute-howto-add-gateway-resource-manager.md)します。 ExpressRoute 用の仮想ネットワーク ゲートウェイは、VPN ではなく GatewayType 'ExpressRoute' を使用します。

* ExpressRoute 仮想ネットワーク ゲートウェイは、ExpressRoute 回線に接続する必要があります。 詳細については、[ExpressRoute 回線への VNet の接続](expressroute-howto-linkvnet-arm.md)に関するページを参照してください。

* Azure Windows VM が VNet にデプロイされていることを確認します。

* オンプレミス ホストと Azure VM 間に接続があることを確認します。

* Azure Windows VM とオンプレミス ホストで DNS 名を使用して名前を正しく解決できることを確認します。

### <a name="workflow"></a>ワークフロー

1. GPO を作成し、それを OU に関連付けます。
2. IPsec の **フィルター操作** を定義します。
3. IPsec の **フィルター一覧** を定義します。
4. **セキュリティ規則** を使用して、IPsec ポリシーを作成します。
5. IPsec GPO を OU に割り当てます。

### <a name="example-values"></a>値の例

* **ドメイン名:** ipsectest.com

* **OU:** IPSecOU

* **オンプレミス Windows コンピューター:** host1

* **Azure Windows VM:** vm1、vm2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1.GPO を作成する

1. [グループ ポリシーの管理] スナップインを開いて、OU にリンクされている新しい GPO を作成します。 次に、GPO がリンクされる OU を見つけます。 例では、OU に **IPSecOU** という名前が付けられています。 

   [![9]][9]
2. [グループ ポリシーの管理] スナップインで、OU を選択して右クリックします。 ドロップダウンで、 **[このドメインに GPO を作成し、このコンテナーにリンクする]** を選択します。

   [![10]][10]
3. 後で簡単に見つけられるように、GPO にわかりやすい名前を付けます。 **[OK]** を選択し、GPO を作成してリンクします。

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2.GPO リンクを有効にする

OU に GPO を適用するには、GPO を OU にリンクするだけでなく、リンクを有効にする必要もあります。

1. 作成した GPO を見つけて右クリックし、ドロップダウンから **[編集]** を選択します。
2. OU に GPO を適用するには、 **[リンクの有効化]** を選択します。

   [![12]][12]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3.IP フィルター操作を定義する

1. ドロップダウンで **[IP Security Policy on Active Directory]\(IP セキュリティ ポリシー (Active Directory)\)** を右クリックしてから、 **[IP フィルター一覧とフィルター操作の管理]** を選択します。

   [![15]][15]
2. **[フィルター操作の管理]** タブで、 **[追加]** を選択します。

   [![16]][16]

3. **IP セキュリティ フィルター操作ウィザード** で、 **[次へ]** を選択します。

   [![17]][17]
4. 後で見つけられるように、フィルター操作にわかりやすい名前を付けます。 この例では、フィルター操作に **myEncryption** という名前が付いています。 説明を追加することもできます。 次に、 **[次へ]** を選択します。

   [![18]][18]
5. **セキュリティのネゴシエート** では、別のコンピューターで IPsec を確立できない場合の動作を定義することができます。 **[セキュリティのネゴシエート]** を選択してから、 **[次へ]** を選択します。

   [![19]][19]
6. **[IPsec をサポートしないコンピューターとの通信]** ページで、 **[Do not allow unsecured communication]\(セキュリティで保護されていない通信を許可しない\)** を選択してから **[次へ]** を選択します。

   [![20]][20]
7. **[IP Traffic and Security]\(IP トラフィック セキュリティ\)** ページで、 **[カスタム]** を選択してから **[設定]** を選択します。

   [![21]][21]
8. **[カスタム セキュリティ メソッドの設定]** ページで、 **[データの整合性と暗号化 (ESP)] からSHA1、3DES** を選択します。 **[OK]** をクリックします。

   [![22]][22]
9. **[フィルター操作の管理]** ページで、**myEncryption** フィルターが正常に追加されたことを確認できます。 **[閉じる]** を選択します。

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4.IP フィルター一覧を定義する

宛先ポート 8080 で暗号化された HTTP トラフィックを指定するフィルター一覧を作成します。

1. 暗号化する必要があるトラフィックの種類を特定するには、**IP フィルター一覧** を使用します。 **[IP フィルター一覧の管理]** タブで、 **[追加]** を選択して新しい IP フィルター一覧を追加します。

   [![24]][24]
2. **[名前]** フィールドで、IP フィルター一覧の名前を入力します。 たとえば、「**azure-onpremises-HTTP8080**」と入力します。 その後、 **[追加]** を選択します。

   [![25]][25]
3. **[IP フィルターの説明とミラー化のプロパティ]** ページで、 **[ミラー化]** を選択します。 ミラー化の設定では両方向のパケットを一致させます。これにより、双方向通信が可能になります。 **[次へ]** を選択します。

   [![26]][26]
4. **[IP トラフィックの発信元]** ページの **[発信元アドレス]** ドロップダウンから、 **[A specific IP Address or Subnet]\(特定の IP アドレスまたはサブネット\)** を選択します。 

   [![27]][27]
5. IP トラフィックの発信元アドレスの **[IP Address or Subnet]\(IP アドレスまたはサブネット\)** を指定してから、 **[次へ]** を選択します。

   [![28]][28]
6. **[送信先アドレス]** としてIP アドレスまたはサブネットを指定します。 次に、 **[次へ]** を選択します。

   [![29]][29]
7. **[IP プロトコルの種類]** ページで、 **[TCP]** を選択します。 次に、 **[次へ]** を選択します。

   [![30]][30]
8. **[IP プロトコルのポート]** ページで、 **[任意の発信ポート]** と **[次の宛先ポート]** を選択します。 テキスト ボックスに「**8080**」と入力します。 これらの設定では、宛先ポート 8080 の HTTP トラフィックのみが暗号化されるように指定します。 次に、 **[次へ]** を選択します。

   [![31]][31]
9. IP フィルター一覧を表示します。  IP フィルター一覧 **azure-onpremises-HTTP8080** の構成により、次の条件に一致するすべてのトラフィックの暗号化がトリガーされます。

   * 10.0.1.0/24 (Azure Subnet2) の任意の発信元アドレス
   * 10.2.27.0/25 (オンプレミス サブネット) のすべての宛先アドレス
   * TCP プロトコル
   * 宛先ポート 8080

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5.IP フィルター一覧を編集する

オンプレミス ホストから Azure VM への同じ種類のトラフィックを暗号化するには、2 番目の IP フィルターが必要です。 最初の IP フィルターを設定するときに使用したのと同じ手順に従い、新しい IP フィルターを作成します。 唯一の違いは、発信元サブネットと宛先サブネットです。

1. IP フィルター一覧に新しい IP フィルターを追加するには、 **[編集]** を選択します。

   [![33]][33]
2. **[IP フィルター一覧]** ページで、 **[追加]** を選択します。

   [![34]][34]
3. 次の例の設定を使用して、2 番目の IP フィルターを作成します。

   [![35]][35]
4. 2 番目の IP フィルターを作成した後、IP フィルター一覧は次のようになります。

   [![36]][36]

アプリケーションを保護するためにオンプレミスの場所と Azure サブネットの間で暗号化が必要な場合は、 既存の IP フィルター一覧を変更するのではなく、新しい IP フィルター一覧を追加できます。 2 つ以上の IP フィルター一覧を同じ IPsec ポリシーに関連付けると、柔軟性が向上します。 IP フィルター一覧は、他の IP フィルター一覧に影響を与えずに変更または削除できます。

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6.IPsec セキュリティ ポリシーを作成する 

セキュリティ規則を使用して、IPsec ポリシーを作成します。

1. OU に関連付けられている **[IPSecurity Policies on Active directory]\(IP セキュリティ ポリシー (Active Directory)\)** を選択します。 右クリックして **[IP セキュリティ ポリシーの作成]** を選択します。

   [![37]][37]
2. セキュリティ ポリシーに名前を付けます。 たとえば、**policy-azure-onpremises** という名前を付けます。 次に、 **[次へ]** を選択します。

   [![38]][38]
3. チェック ボックスをオンにせずに、 **[次へ]** を選択します。

   [![39]][39]
4. **[プロパティの編集]** チェック ボックスがオンになっていることを確認してから、 **[完了]** を選択します。

   [![40]][40]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7.IPsec セキュリティ ポリシーを編集する

IPsec ポリシーに、先ほど構成した **IP フィルター一覧** と **フィルター操作** を追加します。

1. [HTTP policy Properties]\(HTTP ポリシーのプロパティ\) の **[規則]** タブで、 **[追加]** を選択します。

   [![41]][41]
2. [ようこそ] ページで **[次へ]** をクリックします。

   [![42]][42]
3. 規則では、IPsec モード (トンネル モードまたはトランスポート モード) を定義するためのオプションが提供されます。

   * トンネル モードの場合、元のパケットが一連の IP ヘッダーでカプセル化されます。 トンネル モードでは、元のパケットの IP ヘッダーを暗号化することで、内部ルーティング情報を保護します。 トンネル モードは、サイト間 VPN シナリオにおけるゲートウェイ間で広く実装されています。 トンネル モードは、ほとんどの場合、ホスト間のエンドツーエンド暗号化で使用されます。

   * トランスポート モードではペイロードと ESP トレーラーのみが暗号化されます。元のパケットの IP ヘッダーは暗号化されません。 トランスポート モードの場合、パケットの IP 発信元と IP 宛先は変わりません。

   **[この規則ではトンネルを指定しない]** を選択してから、 **[次へ]** を選択します。

   [![43]][43]
4. **[ネットワークの種類]** では、セキュリティ ポリシーに関連付けるネットワーク接続を定義します。 **[すべてのネットワーク接続]** を選択してから、 **[次へ]** を選択します。

   [![44]][44]
5. 先ほど作成した IP フィルター一覧 **azure-onpremises-HTTP8080** を選択し、 **[次へ]** を選択します。

   [![45]][45]
6. 先ほど作成した、既存のフィルター操作 **myEncryption** を選択します。

   [![46]][46]
7. Windows では 4 種類の認証がサポートされています。Kerberos、証明書、NTLMv2、および事前共有キーです。 ここではドメインに参加しているホストを使用しているため、 **[Active Directory 既定値 (Kerberos V5 プロトコル)]** を選択して、 **[次へ]** を選択します。

   [![47]][47]
8. 新しいポリシーでは **azure-onpremises-HTTP8080** というセキュリティ規則が作成されます。 **[OK]** を選択します。

   [![48]][48]

IPsec ポリシーでは、宛先ポート 8080 のすべての HTTP 接続で IPsec トランスポート モードを使用することが求められます。 HTTP はクリア テキストのプロトコルであるため、セキュリティ ポリシーを有効にすることで、データは ExpressRoute プライベート ピアリング経由で転送されるときに確実に暗号化されます。 Active Directory の IPsec ポリシーは、セキュリティが強化された Windows ファイアウォールよりも構成が複雑になります。 ただし、IPsec 接続をさらにカスタマイズすることができます。

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8.IPsec GPO を OU に割り当てる

1. ポリシーを表示します。 セキュリティ グループ ポリシーは定義されていますが、まだ割り当てられていません。

   [![49]][49]
2. OU (**IPSecOU**) にセキュリティ グループ ポリシーを割り当てるには、セキュリティ ポリシーを右クリックして **[割り当て]** を選択します。
   OU に属しているすべてのコンピューターに、セキュリティ グループ ポリシーが割り当てられます。

   [![50]][50]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>トラフィックの暗号化を確認する

OU で適用された暗号化 GPO を確認するには、すべての Azure VM および host1 に IIS をインストールします。 すべての IIS は、ポート 8080 で HTTP 要求に応答するためにカスタマイズされます。
暗号化を確認するために、OU 内のすべてのコンピューターで (Wireshark などの) ネットワーク スニファーをインストールできます。
PowerShell スクリプトは、ポート 8080 で HTTP 要求を生成する HTTP クライアントとして動作します。

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
次のネットワーク キャプチャには、オンプレミス host1 の結果が、暗号化されたトラフィックのみと一致させる表示フィルター ESP と共に示されています。

[![51]][51]

オンプレミスで PowerShell スクリプト (HTTP クライアント) を実行すると、Azure VM のネットワーク キャプチャで同様のトレースが示されます。

## <a name="next-steps"></a>次のステップ

ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "ExpressRoute 経由の IPsec トランスポート モードのネットワーク ダイアグラム"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPsec の興味深いトラフィック"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Windows IPsec ポリシー"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "グループ ポリシー内の組織単位"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "OU に関連付けられている GPO を作成する"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "OU に関連付けられている GPO に名前を割り当てる"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "GPO を編集する"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "IP フィルター一覧とフィルター操作の管理"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "フィルター操作を追加する"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "操作ウィザード"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "フィルター操作名"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "フィルター操作"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "セキュリティで保護されていない接続が確立されている場合の動作を指定する"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "セキュリティ メカニズム"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "カスタム セキュリティ メソッド"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "フィルター操作一覧"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "新しい IP フィルター一覧を追加する"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "IP フィルターに HTTP トラフィックを追加する"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "両方向のパケットを一致させる"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "発信元サブネットの選択"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "発信元ネットワーク"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "宛先ネットワーク"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "プロトコル"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "発信元ポートと宛先ポート"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "フィルター一覧"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "HTTP トラフィックが示された IP フィルター一覧"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "2 番目の IP フィルターの追加"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP フィルター一覧の 2 番目のエントリ"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP フィルター一覧の 2 番目のエントリ"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "IP セキュリティ ポリシーを作成する"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "IPsec ポリシーの名前"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "IPsec ポリシー ウィザード"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "IPsec ポリシーの編集"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "IPsec ポリシーに新しいセキュリティ規則を追加する"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "新しいセキュリティ規則を作成する"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "トランスポート モード"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "ネットワークの種類"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "既存の IP フィルター一覧の選択"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "既存のフィルター操作の選択"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "認証方法の選択"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "セキュリティ ポリシーの作成プロセスの終了"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "GPO にリンクされているが、割り当てられていない IPsec ポリシー"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "GPO に割り当てられている IPsec ポリシー"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "IPsec の暗号化されたトラフィックのキャプチャ"