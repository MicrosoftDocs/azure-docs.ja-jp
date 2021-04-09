---
title: Cloud Services (延長サポート) の拡張機能
description: Cloud Services (延長サポート) の拡張機能
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: f9029a36dc3b778e139b4553524e8e2ca6b4bbad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98757171"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Cloud Services (延長サポート) の拡張機能

拡張機能は、ロールでのデプロイ後の構成および自動化タスクを提供する小さなアプリケーションです。 たとえば、リモート デスクトップ拡張機能を使用して、クラウド サービス (延長サポート) のデプロイ中にロールでリモート デスクトップ接続を有効にすることができます。  

## <a name="remote-desktop-extension"></a>リモート デスクトップ拡張機能

リモート デスクトップを使用して、Azure で実行されているロールのデスクトップにアクセスできます。 リモート デスクトップ接続を使用すると、アプリケーションの問題をその実行中にトラブルシューティングしたり、診断したりできます。

サービス定義にリモート デスクトップ モジュールを含めるか、またはリモート デスクトップ拡張機能を使用することによって、開発中にロールでリモート デスクトップ接続を有効にすることができます。 

詳細については、[Azure portal からのリモート デスクトップの構成](enable-rdp.md)に関するページを参照してください。

## <a name="windows-azure-diagnostics-extension"></a>Windows Azure Diagnostics 拡張機能

どのクラウド サービスでも主要なパフォーマンス メトリックを監視することができます。 各クラウド サービス ロールは最小限のデータ (CPU 使用率、ネットワーク使用状況、ディスク使用率) を収集します。 クラウド サービスでロールに Microsoft.Azure.Diagnostics 拡張機能が適用されている場合、そのロールは追加のデータ ポイントを収集できます。 

ベーシックな監視機能では、ロール インスタンスのパフォーマンス カウンター データは、3 分間隔でサンプリングされて収集されます。 このベーシックな監視のデータはストレージ アカウントには格納されないため、追加のコストが伴うことはありません。 

高度な監視機能では、5 分、1 時間、12 時間の間隔で追加のメトリックがサンプリングされて収集されます。 集計されたデータはストレージ アカウントにテーブルとして格納され、10 日後に消去されます。 使用されるストレージ アカウントはロールごとに構成されます。ロールによって異なるストレージ アカウントを使用できます。 

詳細については、「[Cloud Services (延長サポート) で Windows Azure Diagnostics 拡張機能を適用する](enable-wad.md)」を参照してください。


## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認します。
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.md)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用してクラウド サービス (延長サポート) をデプロイします。
