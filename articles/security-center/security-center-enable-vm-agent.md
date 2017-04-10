---
title: "Azure Security Center での VM エージェントの有効化 | Microsoft Docs"
description: "このドキュメントでは、&quot;VM エージェントの有効化&quot; という Azure Security Center の推奨事項を実装する方法について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 5b431c25-4241-45b7-9556-cf2a1956f3da
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 83328eb874485d4541ff2131f736a30f350a0eb6


---
# <a name="enable-vm-agent-in-azure-security-center"></a>Azure Security Center での VM エージェントの有効化
VM エージェントは、 [データ収集を有効にする](security-center-enable-data-collection.md)ために、仮想マシン (VM) にインストールする必要があります。  Azure Security Center では、VM エージェントを必要とする VM を確認することができ、それらの VM で VM エージェントを有効にするよう推奨されます。

既定では、Azure Marketplace からデプロイされた VM に VM エージェントがインストールされます。 「 [VM エージェントと拡張機能 – パート 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) 」の記事には、VM エージェントのインストール方法が記載されています。

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。 ステップ バイ ステップ ガイドではありません。
>
>

## <a name="implement-the-recommendation"></a>推奨事項の実装
1. **[推奨事項]** ブレードで、**[VM エージェントを有効にする]** を選択します。
   ![VM エージェントの有効化][1]
2. **[VM エージェントが見つからないか、応答していません]**ブレードが開きます。 このブレードには、VM エージェントを必要とする VM が一覧表示されます。 ブレードの指示に従って、VM エージェントをインストールします。
   ![VM エージェントが見つからない][2]

## <a name="see-also"></a>関連項目
セキュリティ センターの詳細については、次を参照してください。

* 「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* 「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。
* 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)-- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png



<!--HONumber=Dec16_HO2-->


