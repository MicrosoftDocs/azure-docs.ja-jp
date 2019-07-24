---
title: Azure Security Center で Endpoint Protection の問題を管理する | Microsoft Docs
description: Azure Security Center で Endpoint Protection の問題を管理する方法について説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: rkarlin
ms.openlocfilehash: 882d4e0592b74e8af30ff5bf110a41e403c3bf7d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789653"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Azure Security Center での Endpoint Protection の問題の管理
Azure Security Center では、マルウェア保護の状態を監視し、[Endpoint protection issues]\(Endpoint Protection の問題\) ブレードでこれを報告します。 Security Center では、仮想マシン (VM) やコンピューターをマルウェアの脅威に対して脆弱にする可能性のある問題 (検出された脅威や不十分な保護など) が示されます。 **[Endpoint protection issues]\(Endpoint Protection の問題\)** に表示される情報を使用して、特定された問題に対処する計画を立てることができます。

Security Center では、次の Endpoint Protection の問題が報告されます。

- Azure VM に Endpoint Protection がインストールされていない - サポートされているマルウェア対策ソリューションがこれらの Azure VM にインストールされていません。
- 非 Azure コンピューターに Endpoint Protection がインストールされていない - サポートされているマルウェア対策がこれらの非 Azure コンピューターにインストールされていません。
- Endpoint Protection の正常性:

  - シグネチャが古い - これらの VM とコンピューターにはマルウェア対策ソリューションがインストールされていますが、ソリューションに最新のマルウェア対策シグネチャがありません。
  - リアルタイム保護がない - これらの VM とコンピューターにはマルウェア対策ソリューションがインストールされていますが、リアルタイム保護を使用するようにソリューションが構成されていません。   サービスが無効になっている可能性があります。または、ソリューションがサポートされていないために、Security Center が状態を取得できない可能性があります。 サポートされているソリューションの一覧については、[パートナー統合](security-center-os-coverage.md#supported-endpoint-protection-solutions)に関する記事をご覧ください。
  - 報告なし - マルウェア対策ソリューションはインストールされていますが、データが報告されていません。
  - 不明 - マルウェア対策ソリューションはインストールされていますが、状態が不明であるか、不明なエラーが報告されています。

    > [!NOTE]
    > Security Center に統合された Endpoint Protection セキュリティ ソリューションの一覧については、[セキュリティ ソリューションの統合](security-center-os-coverage.md#supported-endpoint-protection-solutions)に関するページを参照してください。
    >
    >

## <a name="implement-the-recommendation"></a>推奨事項の実装
Endpoint Protection の問題は、Security Center に推奨事項として表示されます。  環境がマルウェアの脅威に対して脆弱である場合、**[推奨事項]** と **[コンピューティング]** にこの推奨事項が表示されます。 **[Endpoint protection issues]\(Endpoint Protection の問題\) ダッシュボード**を表示するには、コンピューティング ワークフローに従う必要があります。

この例では、**[コンピューティング]** を使用します。  Azure VM と非 Azure コンピューターにマルウェア対策をインストールする方法を説明します。

## <a name="install-antimalware-on-azure-vms"></a>Azure VM へのマルウェア対策のインストール

1. Security Center のメイン メニューの **[計算とアプリ]** を選択するか、**[概要]** を選択します。

   ![[コンピューティング] を選択する][1]

2. **[コンピューティング]** で、**[Endpoint protection issues]\(Endpoint Protection の問題\)** を選択します。 **[Endpoint protection issues]\(Endpoint Protection の問題\)** ダッシュボードが開きます。

   ![[Endpoint protection issues]\(Endpoint Protection の問題\) を選択する][2]

   ダッシュボードの上部に次の情報が表示されます。

   - [Installed endpoint protection providers]\(インストール済みの Endpoint Protection プロバイダー\) - Security Center によって特定された各種プロバイダーが表示されます。
   - [Installed endpoint protection health state]\(インストール済みの Endpoint Protection の正常性状態\) - Endpoint Protection ソリューションがインストールされている VM とコンピューターの正常性状態が表示されます。 グラフには、正常な VM とコンピューターの数、および保護が不十分な VM とコンピューターの数が示されます。
   - [Malware detected]\(検出されたマルウェア\) - Security Center によってマルウェアの検出が報告されている VM とコンピューターの数が表示されます。
   - [攻撃されたコンピューター] - Security Center によってマルウェアによる攻撃が報告されている VM とコンピューターの数が表示されます。

   ダッシュボードの下部にある Endpoint Protection の問題の一覧には、次の情報が含まれています。  

   - **[合計]** - 問題の影響を受けた VM とコンピューターの数。
   - 問題の影響を受けた VM とコンピューターの数を集計したバー。 バーの色は優先度を示しています。

      - 赤 - 優先度が高く、直ちに対処する必要があります。
      - オレンジ - 優先度は中程度であり、できるだけ速やかに対処する必要があります

3. **[Endpoint protection not installed on Azure VMs]\(Azure VM に Endpoint Protection がインストールされていません\)** を選択します。

   ![[Endpoint protection not installed on Azure VMs]\(Azure VM に Endpoint Protection がインストールされていません\) を選択する][3]

4. **[Endpoint protection not installed on Azure VMs]\(Azure VM に Endpoint Protection がインストールされていません\)** に、マルウェア対策がインストールされていない Azure VM の一覧が表示されます。  一覧のすべての VM にマルウェア対策をインストールすることも、特定の VM をクリックして、マルウェア対策をインストールする個々の VM を選択することもできます。
5. **[Endpoint Protection の選択]** で、使用する Endpoint Protection ソリューションを選択します。 この例では、**[Microsoft マルウェア対策]** を選択します。
6. Endpoint Protection ソリューションに関する追加情報が表示されます。 **作成** を選択します。

## <a name="install-antimalware-on-non-azure-computers"></a>非 Azure コンピューターへのマルウェア対策のインストール

1. **[Endpoint protection issues]\(Endpoint Protection の問題\)** に戻り、**[Endpoint protection not installed on non-Azure computers]\(非 Azure コンピューターに Endpoint Protection がインストールされていません\)** を選択します。

   ![[Endpoint protection not installed on non-Azure computers]\(非 Azure コンピューターに Endpoint Protection がインストールされていません\) を選択する][4]

2. **[Endpoint protection not installed on non-Azure computers]\(非 Azure コンピューターに Endpoint Protection がインストールされていません\)** で、ワークスペースを選択します。 ワークスペースでフィルター処理された Azure Monitor ログ検索クエリが開き、マルウェア対策がインストールされていないコンピューターの一覧が表示されます。 一覧でコンピューターを選択して詳細を確認します。

   ![Azure Monitor ログ検索][5]

そのコンピューターだけを対象にフィルター処理された情報が含まれた別の検索結果が表示されます。

  ![Azure Monitor ログ検索][6]

> [!NOTE]
> ウイルス、スパイウェア、その他の悪意のあるソフトウェアを特定して削除できるように、すべての VM に Endpoint Protection をプロビジョニングすることをお勧めします。
>
>

## <a name="next-steps"></a>次の手順
この記事では、"Endpoint Protection をインストールします" という Security Center の推奨事項を実装する方法について説明しました。 Azure での Microsoft マルウェア対策の有効化の詳細については、次のドキュメントを参照してください。

* [Cloud Services および Virtual Machines 向け Microsoft マルウェア対策](../security/azure-security-antimalware.md) - Microsoft マルウェア対策をデプロイする方法を説明しています。

Security Center の詳細については、次のドキュメントを参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md) 」-- セキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」-- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
