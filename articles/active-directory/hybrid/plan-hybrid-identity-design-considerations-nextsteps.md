---
title: Azure Active Directory ハイブリッド ID の設計上の考慮事項 - 次の段階 | Microsoft Docs
description: ハイブリッド ID の設計上の考慮事項ガイドを読んだ後の概要と次の手順
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 02d48768-ea9e-4bfe-ae54-b54c4bd0a789
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41741249e9b1a142d75392025236a4d333b67666
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201484"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations--next-steps"></a>Azure Active Directory ハイブリッド ID の設計上の考慮事項 - 次の段階
要件を定義し、モバイル デバイスのすべての選択肢を検討したので、次の段階ではユーザーと組織に最適なサポート インフラストラクチャをデプロイします。

## <a name="hybrid-identity-solutions"></a>ハイブリッド ID ソリューション
-ニーズに最適なソリューション シナリオを活用することは、モバイル デバイス管理インフラストラクチャ デプロイの詳細を確認し、計画する優れた方法です。 次のソリューションでは、最も一般的なモバイル デバイス管理シナリオをいくつか紹介します。

* [「企業環境のモバイル デバイスと PC を管理する」ソリューション](https://technet.microsoft.com/library/dn582037.aspx)は、Microsoft Intune を利用し、オンプレミスのSystem Center 2012 Configuration Manager インフラストラクチャをクラウドに展開することでモバイル デバイスを管理する際に役立ちます。 このハイブリッド インフラストラクチャは、中規模または大規模の環境で IT 専門家が BYOD とリモート アクセスを導入する場合に役立ちます。同時に管理の複雑性が緩和されます。
* [「Configuration Manager 2007 でモバイル デバイスを管理する」ソリューション](https://technet.microsoft.com/library/dn508400.aspx) は、System Center Configuration Manager 2007 を基盤にインフラストラクチャを構築している場合のモバイル デバイス管理に役立ちます。 このソリューションでは、System Center 2012 Configuration Manager を実行する 1 台のサーバーを構築し、Microsoft Intune を実行し、その MDM 機能を最大限に活用する方法が紹介されています。
* [「小規模環境でモバイル デバイスを管理する」ソリューション](https://technet.microsoft.com/library/dn715906.aspx) は、MDM の導入が必要な小規模ビジネス向けです。 Microsoft Intune を使用して現在のインフラストラクチャを拡張子、モバイル デバイス管理と BYOD をサポートする方法が説明されています。 このソリューションでは、Microsoft Intune をスタンドアロンで利用し、ローカル サーバーのないクラウドだけの構成という最も単純なシナリオについて説明されています。

## <a name="hybrid-identity-documentation"></a>ハイブリッド ID のドキュメント
モバイル デバイス管理ソリューションを実装するとき、コンセプトに基づく手続型の計画、デプロイメント、管理コンテンツが役立ちます。

* [Microsoft System Center](https://technet.microsoft.com/library/cc507089.aspx) ソリューションは、IT スタッフが管理が簡単なシステムを構築し、操作を自動化できるように、インフラストラクチャ、ポリシー、プロセス、ベスト プラクティスに関する知識を集め、まとめる際に役立ちます。
* [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx) はクラウドベースのデバイス管理サービスであり、コンピューターとモバイル デバイスを管理し、コンピューターの情報を守る際に役立ちます。
* [MDM for Office 365](https://technet.microsoft.com/library/ms.o365.cc.devicepolicy.aspx) を利用すれば、Office 365 機構に接続されているモバイル デバイスを管理し、保護できます。 MDM for Office 365 を利用し、デバイスのセキュリティ ポリシーとアクセス ルールを設定し、紛失または盗難時にモバイル デバイスのデータを消去できます。

## <a name="hybrid-identity-resources"></a>ハイブリッド ID リソース
次のリソースには、モバイル デバイス管理ソリューションに関する最新ニュースと更新が頻繁に掲載されます。

* [Microsoft Enterprise Mobility ブログ](https://cloudblogs.microsoft.com/ENTERPRISEMOBILITY/)
* [Microsoft In The Cloud ブログ](https://blogs.technet.com/b/in_the_cloud/)
* [Microsoft Intune ブログ](https://blogs.technet.com/b/microsoftintune/)
* [Microsoft System Center Configuration Manager ブログ](https://blogs.technet.com/b/configurationmgr/)
* [Microsoft System Center Configuration Manager Team ブログ](https://blogs.technet.com/b/configmgrteam/)

## <a name="see-also"></a>関連項目
[設計上の考慮事項の概要](plan-hybrid-identity-design-considerations-overview.md)

