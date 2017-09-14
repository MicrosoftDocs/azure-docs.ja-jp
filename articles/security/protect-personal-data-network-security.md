---
title: "Azure ネットワーク セキュリティ機能を使って個人データを保護する | Microsoft Docs"
description: "Azure ネットワーク セキュリティ機能を使って個人データを保護する"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 81aec340d595371a314ec0c1361f77a37c406a1e
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---
# <a name="protect-personal-data-with-network-security-features-azure-application-gateway-and-network-security-groups"></a>ネットワークのセキュリティ機能を使用した個人データの保護: Azure Application Gateway とネットワーク セキュリティ グループ

この記事では、Azure Application Gateway とネットワーク セキュリティ グループを使用して個人データを保護するための情報を提供し、手順を説明します。

SQL インジェクションやクロスサイト スクリプティングなど一般的な脆弱性の悪用に対する対策は、個人データのプライバシーを保護する多階層セキュリティ戦略の重要な要素です。 機密データをセキュリティ侵害の危険性から守るには、Azure 仮想ネットワークから不要なネットワーク トラフィックを排除する必要があり、Microsoft Azure は攻撃者からデータを保護するためのツールを提供します。

## <a name="scenario"></a>シナリオ

米国に本社を置く大手クルーズ会社が、地中海、アドリア海、バルト海だけでなくイギリス諸島での旅程を提供できるように、事業を拡張しています。 この会社は、この取り組みを進めるために、イタリア、ドイツ、デンマーク、英国に拠点を置く、小規模なクルーズ会社をいくつか買収しました。

この会社では、Microsoft Azure を使用して、会社のデータをクラウドに格納し、このデータを処理し、アクセスするアプリケーションを仮想マシンで実行しています。 このデータには、世界中の顧客ベースの氏名や住所、電話番号、クレジット カード情報など、個人を特定できる情報が含まれます。 また、住所、電話番号、納税者番号など、あらゆる場所の会社の従業員に関する標準的な人事情報も含まれます。 このクルーズ会社は報酬やロイヤリティ プログラム メンバーについての大規模なデータベースも管理しており、このデータベースには現在および過去の顧客との関係を追跡できる個人情報なども含まれています。

この企業の従業員は、会社の支社からネットワークにアクセスし、世界各地に存在する旅行代理店の社員は、一部の会社リソースにアクセスでき、Azure VM 上で実行される Web ベースのアプリケーションを使用します。

## <a name="problem-statement"></a>問題の説明

会社は、ソフトウェアの脆弱性を悪用して悪意のあるコードを実行し、会社のクラウドベース アプリケーションによって格納および使用される個人データを露出しようとする攻撃者から、顧客のプライバシーと従業員の個人データ保護する必要があります。

## <a name="company-goal"></a>会社の目標

会社の目標は、企業の Azure Virtual Network およびアプリケーションとそこに存在するデータに、権限のないユーザーが一般的な脆弱性を悪用してアクセスできないようにすることです。 

## <a name="solutions"></a>解決方法

Microsoft Azure では、不要なトラフィックが Azure Virtual Network に侵入しないセキュリティ メカニズムを提供します。 受信および送信トラフィックの制御は、通常、ファイアウォールによって実行されています。 Azure では、Web アプリケーション ファイアウォールおよびネットワーク セキュリティ グループ (NSG) を使用する単純な分散型ファイアウォールとして機能する Application Gateway を使用できます。 これらのツールを使用すると、不要なネットワーク トラフィックを検出し、ブロックできます。

### <a name="application-gatewayweb-application-firewall"></a>Application Gateway/Web アプリケーション ファイアウォール

[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) の [Web アプリケーション ファイアウォール](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF) コンポーネントは、日々その数を増す、一般的な既知の脆弱性を悪用した攻撃の的になっている Web アプリケーションを保護します。 集中管理された WAF を使用すれば、Web 攻撃から防御すると同時に、アプリケーションを変更することなくセキュリティを簡単に管理できます。

Azure WAF は、SQL インジェクション、クロス サイト スクリプト、HTTP プロトコル違反や異常、bot、クローラー、スキャナー、一般的なアプリケーションの設定不備、HTTP サービス拒否、およびコマンド インジェクション、HTTP リクエスト スマグリング、HTTP レスポンス分割、リモート ファイル インクルード攻撃などの一般的な攻撃を含め、さまざまな攻撃カテゴリに対応します。 

WAF でアプリケーション ゲートウェイを作成するか、既存のアプリケーション ゲートウェイに WAF を追加できます。 いずれの場合も、Azure Application Gateway 専用のサブネットが必要です。

#### <a name="how-do-i-create-an-application-gateway-with-waf"></a>WAF でアプリケーション ゲートウェイを作成する方法 

WAF を有効にした新しいアプリケーション ゲートウェイを作成するには、次の操作を行います。

1. Azure ポータルにログインし、ポータルの **[お気に入り]** ウィンドウで **[新規]** をクリックします。

2. **[新規]** ブレードの **[ネットワーキング]** をクリックします。

3. **[Application Gateway]** をクリックします。

4. Azure ポータルに移動し、**[新規]、\>\>[ネットワーク]、[Application Gateway]** の順にクリックします。

   ![Application Gateway の作成方法](media/protect-netsec/app-gateway-01.png)

5. 表示された **[基本]** ブレードで、[名前]、[階層] (Standard または WAF) [SKU サイズ] (小、中、大)、[インスタンス数] (高可用性が必要な場合は 2)、[サブスクリプション]、[リソース グループ]、[場所] のフィールドの値を入力します

6. **[仮想ネットワーク]**の下に表示される **[設定]** ブレードで、**[仮想ネットワークの選択]** をクリックします。 [仮想ネットワークの選択] ブレードが開きます。

7. **[新規作成]** をクリックして **[仮想ネットワークの作成]** ブレードを開きます。

8. [名前]、[アドレス空間]、[サブネット名]、[サブネット アドレス範囲] に値を入力します。 **[OK]**をクリックします。

9. **[フロントエンド IP 構成]** の **[設定]** ブレードで、[IP アドレスの種類] を選択します。

10. **[パブリック IP アドレスの選択]** をクリックし、**[新規作成]** をクリックします。

11. 既定値のままにし、**[OK]** をクリックします。

12. **[リスナー構成]** の **[設定]** ブレードの、**[プロトコル]** で [HTTP] または [HTTPS] を選択します。 HTTPS を使用するには証明書が必要です。

13. WAF 固有の設定として、**[ファイアウォールの状態]** (**有効**) および **[ファイアウォール モード]** (**防止**) を構成します。 モードとして **[検出]** を選択した場合、トラフィックのみが記録されます。

14. **[概要]** ページを確認し、**[OK]** をクリックします。 これで、アプリケーション ゲートウェイがキューに登録され、作成されます。

アプリケーション ゲートウェイが作成されたら、ポータルでそのゲートウェイに移動し、アプリケーション ゲートウェイの構成を続けます。

![アプリケーション ゲートウェイの作成](media/protect-netsec/adatum-app-gateway.png)

#### <a name="how-do-i-add-waf-to-an-existing-application"></a>既存のアプリケーションに WAF を追加する方法

防止モードで WAF をサポートするように既存のアプリケーション ゲートウェイを更新するには、次の操作を実行します。

1. Azure Portal の **[お気に入り]** ウィンドウで **[すべてのリソース]** をクリックします。

2. **[すべてのリソース]** ブレードで Application Gateway をクリックします。 
>[!NOTE]
注: 選択したサブスクリプションに既存のリソースが複数ある場合は、[名前でフィルター] ボックスに名前を入力すると、 目的の DNS ゾーンがすぐに見つかります。
3. **[Web アプリケーション ファイアウォール]** をクリックし、アプリケーション ゲートウェイ設定を、**[WAF 層へのアップグレード]** (オン)、**[ファイアウォールの状態]** (有効)、**[ファイアウォール モード]** (防止) に更新します。 また、規則セットと、無効にした規則を構成する必要があります。

WAF のあるアプリケーション ゲートウェイを新規に作成する方法と、既存のアプリケーション ゲートウェイに WAF を追加する方法の詳細については、「[ポータルを使用した、Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイの作成](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)」を参照してください。

### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ

[ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) には、[Azure Virtual Networks](https://docs.microsoft.com/azure/virtual-network/) (VNet) に接続されたリソースへのネットワーク トラフィックを許可または拒否する一連のセキュリティ規則が含まれています。 NSG はサブネットまたは個々の仮想マシンに関連付けることができます。 NSG をサブネットに関連付けた場合、そのサブネットに接続されているすべてのリソースにその NSG のルールが適用されます。 加えて VM や NIC にも NSG を関連付けることで、トラフィックをさらに制限することができます。

NSG には、名前、リージョン、リソース グループ、およびルールの 4 つのプロパティが含まれます。
>[!Note]
NSG はリソース グループに存在しますが、リソースが NSG と同じ Azure リージョン内にあれば、任意のリソース グループ内のリソースに関連付けることができます。

NSG 規則には、名前、プロトコル (TCP、UDP、または ICMP、UDP および TCP を含む \*)、発信元ポート範囲、宛先ポート範囲、発信元アドレス プレフィックス、宛先アドレス プレフィックス、方向 (受信または送信)、優先度 (100 ～ 4096) とアクセスの種類 (許可または拒否) の 9 つのプロパティが含まれます。 すべての NSG には、削除、または作成した規則によって上書きできる既定の規則が含まれています。

#### <a name="how-do-i-implement-nsgs"></a>NSG の実装方法

NSG を実装するには、計画を立て、設計に関する検討事項を考慮に入れる必要があります。 これには、サブスクリプションあたりの NSG 数や NSG あたりのルール数の制限、VNet とサブネットの設計、特別な規則、ICMP トラフィック、サブネットと階層の分離、ロード バランサーなどが挙げられます。

NSG の計画および実装に関するガイダンスの詳細と、サンプル デプロイメント シナリオについては、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)」を参照してください。

#### <a name="how-do-i-create-rules-in-an-nsg"></a>NSG で規則を作成する方法

既存の NSG で受信規則を作成するには、次の操作を行います。

1. **[参照]**、**[ネットワーク セキュリティ グループ]** の順にクリックします。

2. NSG の一覧で、**[NSG-FrontEnd]**、**[受信セキュリティ規則]** の順にクリックします。

3. [受信セキュリティ規則] の一覧で **[追加]** をクリックします。

4. [名前]、[優先度]、[ソース]、[プロトコル]、[ソース範囲]、[宛先]、[宛先ポート範囲]、[操作] のフィールドに値を入力します。

数秒後に、NSG の新しい規則が表示されます。

![ネットワーク セキュリティ規則](media/protect-netsec/inbound-security.png)

サブネット内に NSG を作成する、ルールを作成、NSG をフロント エンドおよびバック エンドサブネットに関連付ける方法の詳細については、「[Azure Portal を使用したネットワーク セキュリティ グループの作成](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure のネットワーク セキュリティ](https://azure.microsoft.com/blog/azure-network-security/)

[Azure のネットワーク セキュリティに関するベスト プラクティス](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)

[ネットワーク セキュリティ グループ規則に関する情報を取得する](https://docs.microsoft.com/rest/api/network/virtualnetwork/get-information-about-a-network-security-group)

[Web アプリケーション ファイアウォール (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)

