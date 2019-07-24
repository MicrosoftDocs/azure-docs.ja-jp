---
title: Azure Security Center でのアラートの検証 | Microsoft Docs
description: このドキュメントは、Azure Security Center でセキュリティ アラートを検証する場合に役立ちます。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: 009f5fe7243b8ce597c2be9f9c6874cdb56d103c
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108547"
---
# <a name="alerts-validation-in-azure-security-center"></a>Azure Security Center でのアラートの検証
このドキュメントは、Azure Security Center のアラートに対してシステムが適切に構成されているかどうかを確認する方法を学ぶのに役立ちます。

## <a name="what-are-security-alerts"></a>セキュリティの警告とは何か
Security Center は、脅威を検出して警告するために、Azure のリソース、ネットワーク、および接続されているパートナー ソリューション (ファイアウォールやエンドポイント保護ソリューションなど) からログ データを自動的に収集、分析、および統合します。 セキュリティ アラートの詳細については、「[Azure Security Center でのセキュリティの警告の管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)」を参照してください。さまざまなアラートのタイプについては、「[Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)」を参照してください。

## <a name="alert-validation"></a>アラートの検証
Security Center エージェントをコンピューターにインストールした後、アラートの攻撃対象リソースとして使用するコンピューターから次の手順を実行します。

1. 実行可能ファイル (calc.exe など) をコンピューターのデスクトップまたは他の適切なディレクトリにコピーします。
2. このファイルの名前を **ASC_AlertTest_662jfi039N.exe** に変更します。
3. コマンド プロンプトを開き、引数 (単なるダミーの引数名) を指定してこのファイルを実行します。例: *ASC_AlertTest_662jfi039N.exe -foo*
4. 5 ～ 10 分待って、Security Center のアラートを開きます。 次のようなアラートが表示されます。

    ![アラートの検証](./media/security-center-alert-validation/security-center-alert-validation-fig2.png)

このアラートを確認する際に、[Arguments Auditing Enabled]\(引数の監査が有効\) フィールドの値が [true] になっていることを確認してください。 値が [false] になっている場合は、コマンド ライン引数の監査を有効にする必要があります。 このオプションを有効にするには、次のコマンド ラインを使用します。

*reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"*


> [!NOTE]
> この機能のデモンストレーションについては、動画「[Alert Validation in Azure Security Center (Azure Security Center でのアラート検証)](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Alert-Validation-in-Azure-Security-Center)」をご覧ください。

## <a name="see-also"></a>関連項目
この記事では、アラートの検証プロセスについて説明しました。 この検証について理解できたら、次の記事をお読みください。

* [Azure Security Center でのセキュリティの警告の管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 Security Center でアラートを管理し、セキュリティ インシデントに対応する方法を説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 さまざまな種類のセキュリティ アラートについて説明します。
* [Azure Security Center トラブルシューティング ガイド](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 Security Center における一般的な問題のトラブルシューティング方法について説明します。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
