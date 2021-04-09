---
title: Azure Defender for App Service - 利点と機能
description: Azure Defender for App Service の機能と、ご自分のサブスクリプションでそれを有効にする方法について説明します
author: memildin
ms.author: memildin
ms.date: 01/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ec4ac5d355266a46b33d89fd25c2665493773f5d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100816"
---
# <a name="protect-your-web-apps-and-apis"></a>Web アプリと API の保護

## <a name="prerequisites"></a>前提条件

Security Center は App Service とネイティブで統合されており、デプロイやオンボーディングの必要がありません。統合は透過的となっています。

Azure Defender for App Service を使用して Azure App Service プランを保護するには、次のものが必要です。

- 専用のマシンに関連付けられた、サポートされている App Service プラン。 サポートされているプランは、「[可用性](#availability)」に記載されています。

- 「[クイックスタート: Azure Defender を有効にする](enable-azure-defender.md)」の説明に従って、サブスクリプションで有効にした Azure Defender。

    > [!TIP]
    > (Azure Defender for App Service のように) Azure Defender で個々のプランを必要に応じて有効にすることもできます。

## <a name="availability"></a>可用性

| 側面                       | 詳細                                                                                                                                                                                        |
|------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| リリース状態:               | 一般提供 (GA)                                                                                                                                                                      |
| 価格:                     | [Azure Defender for App Service](azure-defender.md) の課金については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」をご覧ください<br>課金は、すべてのプランのコンピューティング インスタンスの合計に基づいて行われます。       |
| サポートされている App Service プラン: | [従量課金プランの Azure Functions](../azure-functions/functions-scale.md) を除き、[すべての App Service プラン](https://azure.microsoft.com/pricing/details/app-service/plans/)がサポートされています。 |
| クラウド:                      | ![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![No](./media/icons/no-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)                                                     |
|                              |                                                                                                                                                                                                |

## <a name="what-are-the-benefits-of-azure-defender-for-app-service"></a>Azure Defender for App Service の利点

Azure App Service は、Web アプリや API の作成とホスティングに使用するフル マネージドのプラットフォームです。 フル マネージドのプラットフォームであるため、そのインフラストラクチャについて心配する必要はありません。 エンタープライズ クラスのパフォーマンス、セキュリティ、コンプライアンス要件を満たすための管理機能、監視機能、運用に関する分析情報を提供します。 詳細については、[Azure App Service](https://azure.microsoft.com/services/app-service/)に関するページを参照してください。

**Azure Defender for App Service** では、クラウドのスケールを使用して、App Service で実行されるアプリケーションをターゲットとした攻撃が識別されます。 攻撃者は、Web アプリケーションをプローブして弱点を発見し、悪用します。 Azure で実行されているアプリケーションに対する要求は、特定の環境にルーティングされる前に、複数のゲートウェイを通過し、そこで検査され、ログに記録されます。 その後、このデータは、悪用や攻撃者を特定し、後で使用される新しいパターンを学習するために使用されます。

Azure Defender for App Service を有効にするとすぐに、その Azure Defender プランによって提供される次のサービスの利点が得られます。

- **セキュア** - App Service プランの対象となるリソースが Security Center によって評価され、その結果に基づき、セキュリティ上の推奨事項が生成されます。 それらの推奨事項に記載された詳しい手順に従って、App Service リソースのセキュリティを強化することができます。

- **検出** - Azure Defender は次の情報を監視することで、App Service リソースに対するさまざまな脅威を検出します。
    - App Service が実行されている VM インスタンスとその管理インターフェイス
    - App Service アプリとの間で送受信される要求と応答
    - 基になるサンドボックスと VM
    - App Service の内部ログ (Azure がクラウド プロバイダーとして備える可視性を利用)

クラウドネイティブのソリューションである Azure Defender は、複数のターゲットに適用されている攻撃の手法を特定できます。 たとえば、単一のホストからでは、IP の小さなサブセットを起点として複数のホスト上の類似のエンドポイントをクロールする分散型の攻撃を特定することは難しいでしょう。

ログ データとインフラストラクチャを併用することで、流行している新しい攻撃からお客様のマシンのセキュリティ侵害に至るまで、そのストーリーを把握することができるのです。 したがって、Web アプリが悪用された後に Security Center がデプロイされたとしても、進行中の攻撃を検出できる可能性があります。


## <a name="what-threats-can-azure-defender-for-app-service-detect"></a>Azure Defender for App Service で検出できる脅威とは

### <a name="threats-by-mitre-attck-tactics"></a>MITRE ATT&CK 戦術による脅威

Azure Defender は、App Service リソースに対するさまざまな脅威を監視します。 このアラートは、攻撃準備からコマンド アンド コントロールまで、ほぼすべての MITRE ATT&CK 戦術をカバーしています。 Azure Defender で検出できる脅威は次のとおりです。

- **攻撃準備の脅威** - Defender は、攻撃者がアプリケーションの弱点をプローブするためによく使用する、複数のタイプの脆弱性スキャナーの実行を検出できます。

- **初期アクセスの脅威** - これらのアラートには、[Microsoft 脅威インテリジェンス](https://go.microsoft.com/fwlink/?linkid=2128684)が利用されています。たとえば、悪意のある既知の IP アドレスが Azure App Service の FTP インターフェイスに接続するとアラートがトリガーされます。

- **実行の脅威** - Defender は、高い特権で実行されるコマンドや、Windows App Service に対する Linux コマンド、ファイルレス攻撃の動作、デジタル通貨マイニング ツールなど、疑わしいコードや悪質なコードの実行アクティビティの試みを数多く検出することができます

### <a name="dangling-dns-detection"></a>未解決の DNS の検出

App Service Web サイトが使用停止されたときに DNS レジストラーに残る DNS エントリ (未解決 DNS エントリと呼ばれます) も Azure Defender for App Service によって特定されます。 Web サイトを削除したにもかかわらず、そのカスタム ドメインを DNS レジストラーから削除しなかった場合、その DNS エントリは、存在しないリソースを参照することになり、サブドメインが乗っ取りに対して無防備になります。 Azure Defender が DNS レジストラーをスキャンして "*既存*" の未解決 DNS エントリを検出することはありません。アラートが生成されるのは、App Service Web サイトの使用を停止したにもかかわらず、そのカスタム ドメイン (DNS エントリ) が削除されなかったときです。

サブドメインの乗っ取りは、組織にとって重大度の高い一般的な脅威です。 脅威アクターは未解決の DNS エントリを検出すると、宛先のアドレスに独自のサイトを作成します。 そうして組織のドメインに宛てられたトラフィックは脅威アクターのサイトに誘導され、悪意のある多様なアクティビティにそのトラフィックが利用されることとなります。

未解決の DNS の保護は、ドメイン管理に Azure DNS を使用しているか、外部のドメイン レジストラーを使用しているかに関係なく利用でき、Windows 上の App Service と Linux 上の App Service の両方に適用されます。

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="未解決の DNS エントリの検出に関する Azure Defender のアラートの例。Azure Defender for App Service を有効にすることで、このアラートを含む、環境のアラートを受け取ることができます。" lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

未解決の DNS とサブドメイン乗っ取りの脅威について詳しくは、「[未解決の DNS エントリを防ぎ、サブドメインの乗っ取りを回避する](../security/fundamentals/subdomain-takeover.md)」を参照してください。

Azure App Service のアラートを網羅した一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azureappserv)を参照してください。

> [!NOTE]
> カスタム ドメインが App Service リソースを直接参照していない場合や、未解決の DNS の保護が有効にされてから Web サイトへのトラフィックが Defender によって監視されていない場合、未解決の DNS のアラートが Defender からトリガーされない場合があります (カスタム ドメインの特定につながるログが存在しないため)。

## <a name="next-steps"></a>次の手順

この記事では、Azure Defender for App Service について説明しました。 

関連資料については、次の記事をご覧ください。 

- アラートを Azure Sentinel、サードパーティの SIEM、またはその他の外部ツールにエクスポートする場合は、「[SIEM、SOAR、または IT サービス管理ソリューションにアラートをストリーミングする](export-to-siem.md)」の手順に従ってください。
- Azure Defender for App Service のアラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azureappserv)を参照してください。
- App Service プランの詳細については、「[App Service プラン](https://azure.microsoft.com/pricing/details/app-service/plans/)」を参照してください。
> [!div class="nextstepaction"]
> [Azure Defender を有効にする](enable-azure-defender.md)