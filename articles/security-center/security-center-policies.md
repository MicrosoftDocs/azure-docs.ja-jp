---
title: "Azure Security Center でのセキュリティ ポリシーの設定 | Microsoft Docs"
description: "このドキュメントは、Azure セキュリティ センターでのセキュリティ ポリシーを構成する場合に役立ちます。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: f4e3f74ce3f342eecf633cd748e2b7b21b2ccdd2
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---
# <a name="set-security-policies-in-azure-security-center"></a>Azure Security Center でのセキュリティ ポリシーの設定
このドキュメントでは、Security Center でセキュリティ ポリシーを構成するうえで必要な手順について詳しく説明します。

>[!NOTE] 
>2017 年 6 月上旬より、Security Center では、Microsoft Monitoring Agent を使用してデータの収集と格納を行っています。 詳細については、「[Azure Security Center のプラットフォームの移行](security-center-platform-migration.md)」を参照してください。 この記事の情報は、Microsoft Monitoring Agent に移行した後の Security Center の機能を示しています。
>

## <a name="what-are-security-policies"></a>セキュリティ ポリシーとは
セキュリティ ポリシーは、指定されたサブスクリプション内のリソースに対して推奨されるコントロールのセットを定義します。 Security Center では、セキュリティに関する会社のニーズと、各サブスクリプションでのアプリケーションの種類やデータの機密度に合わせて、Azure サブスクリプションのポリシーを定義できます。

たとえば、開発やテストに使用されるリソースは、運用アプリケーションで使用されるリソースとは異なるセキュリティ要件を持つ場合があります。 同様に、個人情報のような規制されたデータが使用されるアプリケーションには、より高いレベルのセキュリティが必要です。 Azure Security Center で有効化されているセキュリティ ポリシーは、セキュリティに関する推奨事項と監視を促進して、潜在的な脆弱性を識別し、脅威を軽減します。 使用に適しているオプションを判断する方法の詳細については、「 [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md) 」を参照してください。

## <a name="set-security-policies"></a>セキュリティ ポリシーを設定する
セキュリティ ポリシーは、サブスクリプションごとに構成できます。 セキュリティ ポリシーを変更するには、そのサブスクリプションの所有者または共同作業者である必要があります。 Azure Portal にサインインし、次の手順に従って Security Center でセキュリティ ポリシーを構成します。

1. Security Center のダッシュボードで **[ポリシー]** タイルをクリックします。
2. 開いた [セキュリティ ポリシー] ブレードで、セキュリティ ポリシーを有効にするサブスクリプションを選択します。

    ![Defining policy](./media/security-center-policies/security-center-policies-fig1-ga.png)
3. 選択したサブスクリプションの **[セキュリティ ポリシー]** ブレードが開き、オプションのセットが表示されます。 このブレードで使用できるオプションは次のとおりです。

   * **防止ポリシー**: このオプションは、サブスクリプションごとにポリシーを構成するために使用します。  
   * **電子メールの通知**: このオプションは、最初に警告が発生したとき、および重大度が高い警告に対して送信される電子メール通知を構成するために使用します。 また、電子メールの設定は、サブスクリプション ポリシーに対してのみ構成できます。 電子メールの通知を構成する方法の詳細については、「 [Azure Security Center でセキュリティ連絡先の詳細情報を指定する](security-center-provide-security-contact-details.md) 」を参照してください。
   * **価格レベル**: このオプションは、価格レベルの選択をアップグレードするために使用します。 価格オプションの詳細については、[Security Center の価格](security-center-pricing.md)に関する記事を参照してください。
4. **[仮想マシンからデータを収集する]** オプションが **[オン]** になっていることを確認します。 このオプションは、Microsoft Monitoring Agent を使用した既存のリソースと新規のリソースの自動的なログの収集を有効にします。このエージェントは、Operations Management Suite と Log Analytics サービスで使用される同じエージェントです。 このエージェントから収集されたデータは、VM の位置情報を考慮して、Azure サブスクリプションに関連付けられている既存の Log Analytics ワークスペースまたは新規のワークスペースのいずれかに格納されます。

5. **[セキュリティ ポリシー]** ブレードで、**[防止ポリシー]** をクリックして、使用可能なオプションを表示します。 このサブスクリプションに関連するセキュリティの推奨事項を有効にするには、**[オン]** をクリックします。

    ![Selecting the security policies](./media/security-center-policies/security-center-policies-fig7.png)

次の表を参照して、各オプションについて確認してください。

| [ポリシー] | 状態がオンの場合 |
| --- | --- |
| システムの更新プログラム |Windows Update または Windows Server Update Services から、利用できる毎日のセキュリティ更新プログラムと重要な更新プログラムの一覧を取得します。 取得される一覧は、その仮想マシンで構成されているサービスによって異なります。この一覧によって、不足している更新プログラムを適用するよう推奨されます。 Linux システムの場合、ポリシーはディストリビューション提供のパッケージ管理システムを利用し、使用できる更新プログラムが含まれているパッケージを特定します。 また、[Azure Cloud Services](../cloud-services/cloud-services-how-to-configure.md) 仮想マシンのセキュリティ更新プログラムと重要な更新プログラムについても確認します。 |
| OS の脆弱性 |オペレーティング システム構成を毎日分析し、攻撃に対する仮想マシンの脆弱性を引き起こすおそれのある問題を特定します。 また、このポリシーによって、これらの脆弱性に対応するための構成の変更が推奨されます。 監視対象のこの特定の構成に関する詳細は、 [推奨される基準の一覧](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) を参照してください。 (現時点では、Windows Server 2016 は完全にはサポートされていません。) |
| エンドポイント保護 |ウイルス、スパイウェア、およびその他の悪意のあるソフトウェアを特定して削除するため、すべての Windows 仮想マシンにエンドポイント保護をプロビジョニングすることをお勧めします。 |
| ディスクの暗号化 |静止データの保護を強化するために、ディスク暗号化はすべての仮想マシンで有効にすることをお勧めします。 |
| ネットワーク セキュリティ グループ |パブリック エンドポイントがある VM への受信トラフィックと送信トラフィックを制御するように、 [ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md) を構成することを推奨します。 サブネットで構成されたネットワーク セキュリティ グループは、他に指定しない限り、すべての仮想マシンのネットワーク インターフェイスによって継承されます。 このポリシーは、ネットワーク セキュリティ グループが構成されていることをチェックするほか、受信セキュリティ規則を評価して、受信トラフィックを許可する規則を特定します。 |
| Web アプリケーション ファイアウォール |次のいずれかにあてはまる場合に、Web アプリケーション ファイアウォールを仮想マシンにプロビジョニングすることを推奨します。 </br></br>[インスタンスレベル パブリック IP](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP) が使用されていて、関連するネットワーク セキュリティ グループの受信セキュリティ規則が、ポート 80/443 にアクセスできるように構成されている。</br></br>負荷分散された IP が使用されていて、関連する負荷分散規則と受信ネットワーク アドレス変換 (NAT) 規則が、ポート 80/443 にアクセスできるように構成されている  (詳細については、[Azure Resource Manager によるロード バランサーのサポート](../load-balancer/load-balancer-arm.md)に関するページを参照してください)。 |
| 次世代のファイアウォール |Azure に組み込まれているネットワーク セキュリティ グループの適用範囲外までネットワーク保護を拡張します。 次世代ファイアウォールの利用が推奨されるデプロイが Security Center によって検出されると、仮想アプライアンスをプロビジョニングできるようになります。 |
| SQL 監査と脅威検出 |コンプライアンスのために、また高度な脅威検出の調査のために、Azure データベースへのアクセスの監査を有効にすることを推奨します。 |
| SQL の暗号化 |Azure SQL データベース、関連付けられたバックアップ、トランザクション ログ ファイルに対し、REST での暗号化を有効にすることを推奨します。 セキュリティ侵害を受けた場合でも、データが読み取られることはありません。 |
| 脆弱性評価 |VM に脆弱性評価ソリューションをインストールすることをお勧めします。 |
| ストレージ暗号化 |現在この機能は、Azure BLOB と Azure Files で利用できます。 [Storage サービスの暗号化] を有効にした後、暗号化されるのは新しいデータのみであり、このストレージ アカウントにある既存のファイルは暗号化されません。 |
| JIT ネットワーク アクセス |ジャスト イン タイムが有効になっている場合、Security Center では NSG ルールの作成により Azure VM への受信トラフィックがロックダウンされます。 ユーザーは VM 上の受信トラフィックをロックダウンする必要があるポートを選択します。 詳細については、「[Manage virtual machine access using just in time (ジャスト イン タイムを使用して仮想マシンへのアクセスを管理する)](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)」を参照してください。 |

すべてのオプションの構成が完了したら、推奨事項が表示されている **[セキュリティ ポリシー]** ブレードで **[OK]** をクリックし、初期設定が表示されている **[セキュリティ ポリシー]** ブレードで **[保存]** をクリックします。

> [!NOTE]
> リソース グループ レベルには、価格レベルが引き続き適用されます。 詳細については、[価格](https://azure.microsoft.com/pricing/details/security-center/)に関するページを参照してください。
>
>

## <a name="see-also"></a>関連項目
このドキュメントでは、Azure セキュリティ センターでのセキュリティ ポリシーの構成方法について説明しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)。 Azure Security Center を導入するための設計上の考慮事項を計画し、理解する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)。 セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)。 パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

