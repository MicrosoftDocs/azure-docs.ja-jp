---
title: 仮想マシンのオファーを計画する - Microsoft コマーシャル マーケットプレース
description: この記事では、仮想マシンのオファーを Azure Marketplace に発行するための要件について説明します。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 9169d691b58ad4a59796123c64f856128a0f8c83
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879328"
---
# <a name="how-to-plan-a-virtual-machine-offer"></a>仮想マシンのオファーを計画する方法

この記事では、仮想マシン (VM) のオファーをコマーシャル マーケットプレースに発行するためのさまざまなオプションと要件について説明します。 VM オファーは、Azure Marketplace を通じてデプロイおよび課金される取引可能なオファーです。

開始する前に、[パートナー センターでコマーシャル マーケットプレース アカウントを作成](./partner-center-portal/create-account.md)し、アカウントがコマーシャル マーケットプレース プログラムに登録されていることを確認します。

### <a name="technical-fundamentals"></a>技術的基礎

オファーの設計、作成、テストのプロセスには時間がかかり、Azure プラットフォームと、オファーの作成に使用される技術の両方に関する専門知識が必要です。 エンジニアリング チームは、[Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)、[Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)、[Azure ネットワーク](https://azure.microsoft.com/services/?filter=networking#networking)に関する実用的な知識を持ち、[Azure アプリケーションの設計とアーキテクチャ](https://azure.microsoft.com/solutions/architecture/)に習熟している必要があります。 次の追加のテクニカル リソースを参照してください。 

- チュートリアル
  - [Linux VM](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Windows VM](../virtual-machines/windows/tutorial-manage-vm.md)

- サンプル
  - [Linux VM 用 Azure CLI サンプル](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Linux VM 用 Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)
  - [Windows VM 用 Azure CLI サンプル](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Windows VM 用 Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

## <a name="technical-requirements"></a>技術的な要件

VM オファーには、次の技術的な要件があります。

- 1 つのオペレーティング システム仮想ハード ディスク (VHD) を準備する必要があります。 データ ディスク VHD はオプションです。 これについては、以下で詳しく説明します。
- 顧客は、いつでもオファーをキャンセルできます。
- オファーに対して少なくとも 1 つのプランを作成する必要があります。 プランの価格は、選択した[ライセンス オプション](#licensing-options)に基づいて決まります。
   > [!IMPORTANT]
   > プラン内のどの VM イメージにも、同じ数のデータ ディスクが存在する必要があります。

VM には、次の 2 つのコンポーネントが含まれています。

- **オペレーティング VHD** - オファーでデプロイされるオペレーティング システムとソリューションが含まれています。 VHD の準備プロセスは、それが Linux、Windows、またはカスタムのいずれをベースにした VM であるかによって異なります。
- **データ ディスク VHD** (オプション) – VM 専用の永続的ストレージ。 永続的な情報の格納には、オペレーティング システム VHD (C: ドライブなど) を使用しないでください。 
    - 最大 16 個のデータ ディスクを含めることができます。
    - ディスクが空の場合でも、データ ディスクごとに 1 つの VHD を使用してください。

    > [!NOTE]
    > 使用するオペレーティング システムに関係なく、ソリューションに必要な最小数のデータ ディスクのみを追加します。 お客様はデプロイ時にイメージの一部であるディスクを削除することはできませんが、デプロイ中またはデプロイ後にいつでもディスクを追加できます。

技術資産を準備する手順の詳細については、[承認済みのベースを使用した Azure 仮想マシンの作成](azure-vm-create-using-approved-base.md)または[独自のイメージを使用した仮想マシンの作成](azure-vm-create-using-own-image.md)に関するページを参照してください。

## <a name="preview-audience"></a>プレビュー対象ユーザー

プレビュー対象ユーザーは、Azure Marketplace で公開される前に VM オファーにアクセスして、公開前にエンドツーエンドの機能をテストできます。 **[プレビュー対象ユーザー]** ページで、限定されたプレビュー対象ユーザーを定義できます。 

> [!NOTE]
> プレビュー対象ユーザーはプライベート プランとは異なります。 プライベート プランは、選択された特定の対象ユーザーのみが利用できます。 これにより、カスタム プランについて、特定の顧客と協議できます。 詳細については、次のセクションを参照してください。参照してください。

Microsoft アカウント (MSA) または Azure Active Directory (Azure AD) の電子メール アドレスに招待状を送信できます。 手動で最大 10 個のメール アドレスを追加するか、.csv ファイルを使用して最大 20 個をインポートします。 オファーが既に公開されている場合も、オファーの変更や更新をテストするためにプレビュー対象ユーザーを定義することができます。

## <a name="plans-and-pricing"></a>プランと価格

VM オファーには、1 つ以上のプランが必要です。 プランでは、ソリューションのスコープと制限、および関連する価格を定義します。 オファーに対して複数のプランを作成し、顧客にさまざまな技術やライセンスのオプション、および無料試用版を提供することができます。 価格モデル、無料試用版、プライベート プランなど、プランに関する一般的なガイダンスについては、「[コマーシャル マーケットプレース オファーのプランと価格](plans-pricing.md)」を参照してください。 

VM は商取引が可能で、従量課金制またはライセンス持ち込み (BYOL) のライセンス モデルを使用します。 商用トランザクションは Microsoft がホストし、公開元に代わって顧客に請求します。 公開元にとっては、顧客と Microsoft との間で推奨される支払い関係 (Enterprise Agreement など) を利用できる利点があります。 詳細については、「[コマーシャル マーケットプレースの販売機能](./marketplace-commercial-transaction-capabilities-and-considerations.md)」を参照してください。

> [!NOTE]
> Enterprise Agreement に関連付けられている Azure 前払い (旧称: 年額コミットメント) は、VM の Azure 利用には使えますが、公開元のソフトウェア ライセンス料金には使えません。

### <a name="licensing-options"></a>ライセンス オプション

新しい VM オファーを公開する準備を行う際、選択するライセンス オプションを決定する必要があります。 これにより、後でパートナー センターでオファーを作成する際に提供する必要がある追加情報が決まります。

VM オファーで使用可能なライセンス オプションは次のとおりです。

| ライセンス オプション | トランザクション プロセス |
| --- | --- |
| 無料試用版 | 1、3、または 6 か月の無料試用版を顧客に提供します。 |
| 体験版 | このオプションを使用すると、顧客は追加コストなしで VM を評価できます。 試用版を体験するには、既存の Azure 顧客である必要はありません。 詳細については、「[体験版とは](./what-is-test-drive.md)」を参照してください。 |
| BYOL | ライセンス持ち込みオプションを使用すると、顧客は既存のソフトウェア ライセンスを Azure に持ち込むことができます。\* |
| 使用量ベース | 従量課金制とも呼ばれるこのオプションを使用すると、顧客は 1 時間ごとの支払いが可能になります。 |
| 対話型デモ  | 対話型デモを使って、ソリューションのガイド付きエクスペリエンスを顧客に提供します。 そのベネフィットは、複雑なソリューションの複雑なセットアップなしで試用エクスペリエンスを提供できることです。 |
|

\* 公開元は、ソフトウェア ライセンス トランザクションのすべての側面 (注文、フルフィルメント、使用状況測定、課金、請求、支払い、収集を含みますが、これらに限定されません) をサポートします。

次の例は、使用量ベースの価格が設定された Azure Marketplace の VM オファーを示しています。

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="サンプル VM オファーの画面。":::

### <a name="private-plans"></a>プライベート プラン

イメージと価格をプライベート プランとして発行することで、VM の検出とデプロイを特定の顧客に限定できます。 プライベート プランとすることで、公開元が近しい顧客に排他的なオファーを作成し、カスタマイズされたソフトウェアと条件を提供できるようになります。 条件をカスタマイズすることによって､公開元は特別な価格と条件によるフィールド主導の取引､さらには限定的なソフトウェア リリースの利用などのさまざまなシナリオにスポットライトを当てることができます。 プライベート プランにより、限られた顧客に具体的な価格や製品を提供できます。

詳細については、「[コマーシャル マーケットプレース オファーのプランと価格](plans-pricing.md)」および「[Microsoft コマーシャル マーケットプレースでのプライベート オファー](private-offers.md)」を参照してください。

## <a name="test-drives"></a>体験版

VM の体験版を有効にすることを選択できます。 体験版を使用すると、顧客は、一定期間、構成済み環境にアクセスできます。 どの公開オプションでも体験版を有効にできますが、この機能には追加の要件があります。 体験版の詳細については、「[体験版とは](what-is-test-drive.md)」を参照してください。 さまざまな種類の体験版の構成については、「[体験版の技術的な構成](test-drive-technical-configuration.md)」を参照してください。

> [!TIP]
> 体験版は、[無料試用版](plans-pricing.md#free-trials)とは異なります。 体験版、無料試用版、またはその両方を提供できます。 これらは両方とも、ソリューションを一定期間、顧客に提供します。 ただし、体験版には、実際の実装シナリオの中で製品の主な機能や利点を体験できる実践的なセルフガイド ツアーも含まれます。

## <a name="customer-leads"></a>潜在顧客

オファーをカスタマー リレーションシップ マネジメント (CRM) システムに接続して、顧客情報を収集する必要があります。 顧客は、自分の情報を共有する許可を求められます。 これらの顧客の詳細は、オファーの名前と ID のほか、顧客がオファーを見つけたオンライン ストアと共に、お客様が構成した CRM システムに送信されます。 コマーシャル マーケットプレースでは、Azure テーブルを使用したり、Power Automate を使用して HTTPS エンドポイントを構成したりするためのオプションと共に、さまざまな CRM システムがサポートされています。

CRM の接続は、オファーの作成時または作成後にいつでも追加または変更できます。 詳細なガイダンスについては、「[コマーシャル マーケットプレース オファーからの潜在顧客](partner-center-portal/commercial-marketplace-get-customer-leads.md)」を参照してください。

## <a name="legal-contracts"></a>法的契約

顧客の調達プロセスを簡素化し、ソフトウェア ベンダーの法務の複雑さを軽減するため、Microsoft では、コマーシャル マーケットプレースでオファーに使用できる標準契約を用意しています。 標準契約の下でソフトウェアを提供すると、顧客はそれを読んで一度承諾するだけで済み、提供元は独自の使用条件を作成する必要はありません。

標準契約の使用を選択する場合、標準契約に、ユニバーサル修正条件を追加するオプションと、最大 10 個のカスタム修正を追加するオプションがあります。 また、標準契約ではなく、独自の使用条件を使用することもできます。 これらの詳細は、 **[プロパティ]** ページで管理します。 詳細については、「[Microsoft コマーシャル マーケットプレースの標準契約](standard-contract.md)」を参照してください。

> [!NOTE]
> コマーシャル マーケットプレースの標準契約を使用してオファーを公開した後に、独自のカスタム使用条件を使用することはできません。 これは、"または" のシナリオです。 ソリューションは、標準契約または独自の使用条件のいずれかの下で提供します。 標準契約の条件を変更する必要がある場合は、Standard Contract Amendments (標準契約の修正) を使用して変更することができます。

## <a name="cloud-solution-providers"></a>クラウド ソリューション プロバイダー

パートナー センターでオファーを作成するときに、 **[CSP を通じた再販]** タブが表示されます。このオプションを使用すると、Microsoft クラウド ソリューション プロバイダー (CSP) プログラムに参加しているパートナーは、バンドルされたオファーの一部として VM を再販できます。 すべてのライセンス持ち込み (BYOL) プランは、自動的にこのプログラムにオプトインされます。 BYOL 以外のプランをオプトインすることもできます。 詳細については、「[クラウド ソリューション プロバイダー プログラム](cloud-solution-providers.md)」を参照してください。 

> [!NOTE]
> クラウド ソリューション プロバイダー (CSP) のパートナー チャネル オプトインが利用できるようになりました。 Microsoft CSP パートナー チャネルを通じたオファーのマーケティングの詳細については、「[**クラウド ソリューション プロバイダー**](./cloud-solution-providers.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Marketplace で仮想マシン オファーを作成する](azure-vm-create.md)
- [承認済みのベースを使用した Azure 仮想マシンの作成](azure-vm-create-using-approved-base.md)または[独自のイメージを使用した仮想マシンの作成](azure-vm-create-using-own-image.md)
- [オファーの掲載のベスト プラクティス](gtm-offer-listing-best-practices.md)