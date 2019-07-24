---
title: Azure Security Center で Web アプリケーション ファイアウォールを追加する | Microsoft Docs
description: このドキュメントでは、Azure Security Center の推奨事項である **"Web アプリケーション ファイアウォールを追加する"** と **"アプリケーションの保護を完了する"** を実装する方法について説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: 63852ccab842f11f30bcbe695206fedf72931911
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105045"
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Azure Security Center で Web アプリケーション ファイアウォールを追加する
Azure Security Center では、Web アプリケーションを保護するために、Microsoft パートナーの Web アプリケーション ファイアウォール (WAF) を追加することを勧める場合があります。 このドキュメントでは、この推奨事項を適用する方法の例を紹介します。

WAF の推奨事項は、開いている受信 Web ポート (80,443) にネットワーク セキュリティ グループが関連付けられている公開 IP (インスタンス レベルの IP または負荷分散された IP) に対して表示されます。

Security Center では、[Isolated](https://azure.microsoft.com/pricing/details/app-service/windows/) サービス プランでデプロイされた仮想マシンや外部の App Service Environment (ASE) 上の Web アプリケーションを対象にした攻撃に対する防御に役立てるために、WAF をプロビジョニングすることをお勧めします。 Isolated プランは、プライベートな専用の Azure 環境内のアプリをホストするため、オンプレミスのネットワークとのセキュリティ保護された接続や、追加のパフォーマンスおよびスケールが必要なアプリに最適です。 アプリは、分離された環境内にあることに加えて、外部 IP アドレスのロード バランサーが必要です。 ASE の詳細については、 [App Service 環境のドキュメント](../app-service/environment/intro.md)をご覧ください。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。  このドキュメントはステップ バイ ステップ ガイドではありません。
>
>

## <a name="implement-the-recommendation"></a>推奨事項の実装
1. **[推奨事項]** で、**[Web アプリケーション ファイアウォールを使用して、Web アプリケーションをセキュリティで保護する]** を選択します。
   ![Secure web Application][1]
2. **[Web アプリケーション ファイアウォールを使用した安全な Web アプリケーション]** で、Web アプリケーションを選択します。 **[Web アプリケーション ファイアウォールの追加]** が開きます。
   ![Web アプリケーション ファイアウォールの追加][2]
3. 利用可能な場合は、既存の Web アプリケーション ファイアウォールを使用することを選択するか、または新しい WAF を作成することができます。 この例では、利用可能な既存の WAF がないため、WAF を作成します。
4. WAF を作成するには、統合されたパートナーの一覧からソリューションを選択します。 この例では、**[Barracuda Web アプリケーション ファイアウォール]** を選択します。
5. パートナー ソリューションに関する情報を提供する **[Barracuda Web アプリケーション ファイアウォール]** が開きます。 **作成**を選択します。

   ![ファイアウォール情報ブレード][3]

6. **[新しい Web アプリケーション ファイアウォール]** が開きます。ここで **VM 構成手順**を実行して、**WAF 情報**を提供できます。 情報ブレードで **[VM 構成]** を選択します。
7. **[VM 構成]** で、WAF を実行する仮想マシンの起動に必要な情報を入力します。

   ![VM 構成][4]
   
8. **[新しい Web アプリケーション ファイアウォール]** に戻り、**[WAF 情報]** を選択します。 **[WAF 情報]** で、WAF 自体を構成します。 手順 7. では、WAF が実行される仮想マシンを構成できます。手順 8. では、WAF 自体をプロビジョニングできます。

## <a name="finalize-application-protection"></a>アプリケーション保護を完了する
1. **[推奨事項]** に戻ります。 WAF を作成した後は、**[Finalize application protection] \(アプリケーション保護を完了する)** という新しいエントリが生成されています。 このエントリは、WAF によるアプリケーションの保護を有効にするには、Azure Virtual Network 内で実際に WAF を接続するプロセスを完了する必要があることを示しています。

   ![アプリケーション保護を完了する][5]

2. **[Finalize application protection] \(アプリケーション保護を完了する)** を選択します。 新しいブレードが開きます。 トラフィックを再ルーティングする必要がある Web アプリケーションがあることが確認できます。
3. 対象の Web アプリケーションを選択します。 Web アプリケーション ファイアウォールのセットアップを完了するための手順が表示されたブレードが開きます。 手順を最後まで実行し、 **[トラフィックを制限する]** を選択します。 Security Center によって WAF が接続されます。

   ![トラフィックを制限する][6]

> [!NOTE]
> セキュリティ センターで複数の Web アプリケーションを保護するには、対象のアプリケーションを既存の WAF デプロイに追加します。
>
>

その WAF からのログは完全に統合されます。 セキュリティ センターは、重要なセキュリティ アラートを表示できるように、ログの収集と分析を自動的に開始できます。

## <a name="next-steps"></a>次の手順
このドキュメントでは、"Web アプリケーションの追加" というセキュリティ センターの推奨事項を実装する方法について説明しました。 Web アプリケーション ファイアウォールを構成する方法の詳細については、次を参照してください。

* [App Service 環境の Web アプリケーション ファイアウォール (WAF) を構成する](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」-- Azure リソースの正常性を監視する方法について説明しています。
* [Azure セキュリティ センターでのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」 -- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure セキュリティ センターでのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」 -- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure セキュリティ センターのよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
