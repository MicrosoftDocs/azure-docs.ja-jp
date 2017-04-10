---
title: "Azure Security Center でシステムの更新プログラムを適用する | Microsoft Docs"
description: "このドキュメントでは、Azure Security Center の推奨事項である &quot;**システムの更新プログラムを適用する**&quot; と &quot;**システムの更新後に再起動する**&quot; を実装する方法について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 53f4898f31ef19a39e1448235ed14c8fc7df7b3b
ms.openlocfilehash: 50cdea437db5387813c6a3905d14b6904d2aba34
ms.lasthandoff: 02/04/2017


---
# <a name="apply-system-updates-in-azure-security-center"></a>Azure Security Center でシステムの更新プログラムを適用する
Azure Security Center では、オペレーティング システムに不足している更新プログラムがないかどうかを確認するために、Windows および Linux 仮想マシン (VM) の監視が毎日行われています。 Security Center は、Windows VM に構成されているサービスに応じて、Windows Update または Windows Server Update Services (WSUS) から利用可能なセキュリティ更新プログラムと重要な更新プログラムの一覧を取得します。  また、Linux システムにおける最新の更新プログラムについても確認します。 VM でシステムの更新プログラムが不足している場合は、それらを適用するよう Security Center による推奨が行われます。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。  ステップ バイ ステップ ガイドではありません。
>
>

## <a name="implement-the-recommendation"></a>推奨事項の実装
1. **[推奨事項]** ブレードで、**[システムの更新プログラムを適用する]** を選択します。

   ![システムの更新の適用][1]
2. **[システムの更新プログラムを適用する]** ブレードが開き、システムの更新が行われていない VM の一覧が表示されます。 VM を選択します。

   ![Select a VM][2]
3. ブレードが開き、選択した VM に不足している更新プログラムの一覧が表示されます。 システムの更新プログラムを選択します。 この例では、KB3156016 を選択します。

   ![Missing security updates][3]

4. **セキュリティ更新プログラム** のブレードの手順に従い、不足している更新プログラムを適用します。

   ![Security update][4]

## <a name="reboot-after-system-updates"></a>システムの更新後に再起動する
1. **[推奨事項]** ブレードに戻ります。 システムの更新プログラムを適用した後は、 **[システムの更新後に再起動する]**という新しいエントリが生成されています。 このエントリは、システムの更新プログラムの適用プロセスを完了するために、VM の再起動が必要であることを示しています。

   ![システムの更新後に再起動する][5]
2. **[システムの更新後に再起動する]**を選択します。 これにより、 **[システムの更新を完了するために、再起動は保留中です]** ブレードが開き、システムの更新プログラムの適用プロセスを完了するために再起動が必要な VM の一覧が表示されます。

   ![Restart pending][6]

Azure から VM を再起動して、プロセスを完了します。

## <a name="see-also"></a>関連項目
セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」-- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]:./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png

