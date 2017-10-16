---
title: "Azure Stack サービス ポリシー | Microsoft Docs"
description: "Azure Stack サービス ポリシーの詳細と、サポートを受けられる状態に統合システムを維持する方法を説明します。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: f2c99f19b30d2cdfdf65dea6dd3909b88ffc15dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack サービス ポリシー

*適用対象: Azure Stack 統合システム*

この記事では、Azure Stack 統合システムのサービス ポリシーについて、およびサポートを受けられる状態にシステムを維持する必要があることについて説明します。 

## <a name="update-package-types"></a>更新プログラム パッケージの種類

統合システムの更新プログラム パッケージには、Microsoft ソフトウェアの更新プログラムと、ドライバーやファームウェアなどの OEM (original equipment manufacturer) ハードウェア ベンダーが指定する更新プログラムの、2 種類があります。 これらの更新プログラムは、個別の Azure Stack 更新プログラム パッケージとして配信され、別々に管理されます。

- **Microsoft ソフトウェア更新プログラム**。 Microsoft には、Microsoft ソフトウェア更新プログラム パッケージについて、エンド ツー エンドでサービスを提供し続ける責任があります。 これらのパッケージには、最新の Windows Server のセキュリティ更新プログラム、セキュリティ以外の更新プログラム、Azure Stack の機能の更新プログラムを含めることができます。 これらの更新プログラム パッケージは Microsoft から直接ダウンロードできます。
- **OEM ハードウェア ベンダー提供の更新プログラム**。 Azure Stack ハードウェア パートナーには、ハードウェアに関連するファームウェアおよびドライバーの更新プログラム パッケージについて、ガイドを含めエンド ツー エンドでサービスを提供し続ける責任があります。 また、Azure Stack ハードウェア パートナーは、すべてのソフトウェアとハードウェアのハードウェア ライフサイクル ホストに関するガイドを所有して管理します。 OEM ハードウェア ベンダーは、自社のダウンロード サイトでこれらの更新プログラム パッケージを管理します。

## <a name="update-package-release-cadence"></a>更新プログラム パッケージのリリース周期

Microsoft は、毎月ソフトウェア更新プログラム パッケージをリリースします。 ただし、1 か月に複数の更新プログラムをリリースすることや、1 つも更新プログラムをリリースしないこともあります。 OEM ハードウェア ベンダーは、必要に応じて随時更新プログラムをリリースします。

Microsoft 更新プログラム パッケージでは、リリース日を確認しやすくするために次のような命名規則を採用しています。

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

たとえば、2017 年 6 月 15 日にリリースされた Microsoft ソフトウェアの更新プログラムは、バージョンが "1.0.170615.1" になります。

## <a name="keep-your-system-under-support"></a>システムがサポートされる状態を維持する

ご利用のシステムでサポートを受けるには、特定の期間に継続して Azure Stack を更新する必要があります。 Microsoft ソフトウェアの更新を遅延できる期間は 3 か月とポリシーで定められています。 ご利用のシステムについて、更新期限を過ぎてから 3 か月以上経過した場合は、契約に準拠していないものとみなされます。 サポートを受けるために必要な最低限のバージョンまでは更新していただく必要があります。 

Microsoft ソフトウェア更新プログラム パッケージは累積的ではないため、前提として前のバージョンの更新パッケージが必要です。 1 つ以上の更新プログラムを先送りにする場合は、最新のバージョンを取得する際に全体のランタイムを考慮してください。

次の表で、更新プログラム パッケージのリリースおよびその前提条件の例と、ご利用のシステムで継続してサポートを受けるために必要な最低限のバージョンを示します。 この表は、Azure Stack 統合システムの最初のリリース (ビルド 1708) と、2017 年 9 月にリリースされた最初の更新プログラム パッケージ リリース (1709) に基づいています。 

| 最新の更新プログラム パッケージ (*例*) | 前提条件 | サポートを受けられる最低限のバージョン |
| -- | -- | -- |
| 1709 | ビルド 1708 | 該当なし |
| 1710 | 1709 | 該当なし |
| 1711 | 1710 | 該当なし |
| 1712 | 1711 | 1709 |
| 1801 | 1712 | 1710 |
| 1802 | 1801 | 1711 |
| 1803 | 1802 | 1712 |
| 1804 | 1803 | 1801 |
| | | 

## <a name="next-steps"></a>次のステップ

- [Azure Stack での更新の管理](azure-stack-updates.md)


