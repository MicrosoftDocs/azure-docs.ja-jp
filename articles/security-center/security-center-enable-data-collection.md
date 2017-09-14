---
title: "Azure Security Center のデータ収集を有効にする | Microsoft Docs"
description: " Azure Security Center のデータ収集を有効にする方法について説明します。 "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 7e9ad8cd8c77c57c37dc208b86b3727a4e1dc7b5
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017


---
# <a name="enable-data-collection-in-azure-security-center"></a>Azure Security Center のデータ収集を有効にする

> [!NOTE]
> 2017 年 6 月上旬以降、Security Center では、Microsoft Monitoring Agent を使用してデータの収集と格納を行います。 詳細については、「[Azure Security Center のプラットフォームの移行](security-center-platform-migration.md)」を参照してください。 この記事の情報は、Microsoft Monitoring Agent に移行した後の Security Center の機能を示しています。
>
>

Security Center では、仮想マシン (VM) のセキュリティ状態へのアクセス、セキュリティ推奨事項の提供、脅威についての警告を行うために、その仮想マシンからデータを収集します。 初めて Security Center にアクセスするときに、サブスクリプション内のすべての VM に対してデータ収集を有効にすることもできます。 データ収集が有効になっていない場合、そのサブスクリプションのセキュリティ ポリシーでデータ収集を有効にすることが推奨されます。

データ収集が有効になっている場合、Security Center は、サポートされている既存の Azure 仮想マシンと新規に作成される仮想マシンすべてに、Microsoft Monitoring Agent をプロビジョニングします。 Microsoft Monitoring Agent は、セキュリティ関連のさまざまな構成をスキャンします。 さらに、オペレーティング システムでは、イベント ログ イベントが発生します。 このようなデータの例として、オペレーティング システムの種類とバージョン、オペレーティング システムのログ (Windows イベント ログ)、実行中のプロセス、コンピューター名、IP アドレス、ログイン ユーザー、テナント ID などがあります。 Microsoft Monitoring Agent は、イベント ログ エントリと構成を読み取り、分析のためにデータをワークスペースにコピーします。 また、クラッシュ ダンプ ファイルもワークスペースにコピーします。

Security Center の Free レベルを使用している場合は、セキュリティ ポリシーでデータ収集を無効にすることで、仮想マシンからのデータ収集を無効にできます。 データ収集を無効にすると、VM のセキュリティ評価が制限されます。 詳細については、「[データ収集の無効化](#disabling-data-collection)」を参照してください。 データ収集が無効になっていても、VM ディスクのスナップショットとアーティファクトの収集は有効です。 データ収集は、Security Center の Standard レベルのサブスクリプションでは必須の機能です。

> [!NOTE]
> Security Center の Free レベルと Standard レベルの詳細については、[価格レベル](security-center-pricing.md)に関するページを参照してください。
>
>

## <a name="implement-the-recommendation"></a>推奨事項の実装

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。 このドキュメントはステップ バイ ステップ ガイドではありません。
>
>

1. **[推奨事項]** ブレードで、**[Enable data collection for subscriptions] \(サブスクリプションのデータ収集を有効にする)** を選択します。  **[Turn on data collection] \(データ収集の有効化)** ブレードが表示されます。
   ![Recommendations blade][2]
2. **[Turn on data collection] \(データ収集の有効化)** ブレードで、該当するサブスクリプションを選択します。 対応するサブスクリプションの **[セキュリティ ポリシー]** ブレードが表示されます。
3. **[セキュリティ ポリシー]** ブレードで、**[データ収集]** の下の **[オン]** を選択すると、自動的にログが収集されます。 データの収集をオンにすると、サブスクリプションの既存の VM と、サポートされる新しい VM のすべてで、監視拡張機能がプロビジョニングされるようになります。
4. [ **保存**] を選択します。
5. **[OK]**を選択します。

## <a name="disabling-data-collection"></a>データ収集の無効化
Security Center の Free レベルを使用している場合は、セキュリティ ポリシーでデータ収集を無効にすることで、いつでも仮想マシンからのデータ収集を無効にできます。 データ収集は、Security Center の Standard レベルのサブスクリプションでは必須の機能です。

1. **[セキュリティ センター]** ブレードに戻り、**[ポリシー]** タイルを選択します。 **[Security policy-Define policy per subscription]\(セキュリティ ポリシー - サブスクリプションごとにポリシーを定義する\)** ブレードが表示されます。
   ![Select the policy tile][5]
2. **[Security policy-Define policy per subscription]\(セキュリティ ポリシー - サブスクリプションごとにポリシーを定義する\)** ブレードで、データ収集を無効にするサブスクリプションを選択します。
3. 対応するサブスクリプションの **[セキュリティ ポリシー]** ブレードが表示されます。  [データ収集] の下の **[オフ]** を選択します。
4. 上部のリボンで **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ
この記事では、"データ収集の有効化" という Security Center の推奨事項を実践する方法について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure セキュリティ センターでのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」 -- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* 「[Azure セキュリティ センターでのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。
* 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。
- 「[Azure Security Center のデータ セキュリティ](security-center-data-security.md)」 - Security Center でデータがどのように管理および保護されているかを説明しています。
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)-- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png

