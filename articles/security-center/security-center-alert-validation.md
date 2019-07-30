---
title: Azure Security Center でのアラートの検証 (EICAR テスト ファイル) | Microsoft Docs
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
ms.date: 7/02/2019
ms.author: rkarlin
ms.openlocfilehash: f65b4b74a1a91fa081bd9c0d8146d055cebb0de6
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626309"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Azure Security Center でのアラートの検証 (EICAR テスト ファイル)
このドキュメントは、Azure Security Center のアラートに対してシステムが適切に構成されているかどうかを確認する方法を学ぶのに役立ちます。

## <a name="what-are-security-alerts"></a>セキュリティの警告とは何か
アラートは、Security Center がリソース上の脅威を検出したときに生成する通知です。 アラートに優先順位を付け、問題の迅速な調査に必要な情報と共に一覧表示します。 Security Center は、攻撃をどのように修復できるかに関する推奨事項も提供します。
詳細については、「[Azure Security Center のセキュリティ アラート](security-center-alerts-overview.md)」および「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」を参照してください

## <a name="alert-validation"></a>アラートの検証

* [Windows](#validate-windows)
* [Linux](#validate-linux)

## Windows VM でのアラートの検証 <a name="validate-windows"></a>

Security Center エージェントをコンピューターにインストールした後、アラートの攻撃対象リソースとして使用するコンピューターから次の手順を実行します。

1. 実行可能ファイル (**calc.exe** など) をコンピューターのデスクトップまたは他の適切なディレクトリにコピーし、名前を **ASC_AlertTest_662jfi039N.exe** に変更します。
1. コマンド プロンプトを開き、引数 (単なるダミーの引数名) を指定してこのファイルを実行します。例: ```ASC_AlertTest_662jfi039N.exe -foo```
1. 5 ～ 10 分待って、Security Center のアラートを開きます。 以下の[例](#alert-validate)のようなアラートが表示されます。

> [!NOTE]
> Windows のこのテスト アラートを確認する場合は、 **[Arguments Auditing Enabled]\(引数の監査が有効\)** フィールドが **[true]** であることを確認してください。 それが **[false]** である場合は、コマンド ライン引数の監査を有効にする必要があります。 これを有効にするには、次のコマンド ラインを使用します。
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Linux VM でのアラートの検証 <a name="validate-linux"></a>

Security Center エージェントをコンピューターにインストールした後、アラートの攻撃対象リソースとして使用するコンピューターから次の手順を実行します。
1. 実行可能ファイルを任意の場所にコピーし、名前を **./asc_alerttest_662jfi039n** に変更します。例:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. コマンド プロンプトを開き、このファイルを実行します。

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. 5 ～ 10 分待って、Security Center のアラートを開きます。 以下の[例](#alert-validate)のようなアラートが表示されます。

### アラートの例 <a name="alert-validate"></a>

![アラートの検証の例](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 

## <a name="see-also"></a>関連項目
この記事では、アラートの検証プロセスについて説明しました。 この検証について理解できたら、次の記事をお読みください。

* [Azure Security Center でのセキュリティの警告の管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 Security Center でアラートを管理し、セキュリティ インシデントに対応する方法を説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)。 Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center のセキュリティ アラートの概要](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 さまざまな種類のセキュリティ アラートについて説明します。
* [Azure Security Center トラブルシューティング ガイド](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 Security Center における一般的な問題のトラブルシューティング方法について説明します。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)。 このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
