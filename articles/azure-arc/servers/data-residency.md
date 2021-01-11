---
title: データの保存場所
description: データの保存場所と Azure Arc 対応サーバー (プレビュー) に関する情報。
ms.topic: reference
ms.date: 08/25/2020
ms.custom: references_regions
ms.openlocfilehash: 8f207f5889c1764eebcc6081960ff70c0d5bca3a
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048858"
---
# <a name="azure-arc-enabled-servers-preview-data-residency"></a>Azure Arc 対応サーバー (プレビュー): データの保存場所

この記事では、データの保存場所の概念と、この概念を Azure Arc 対応サーバー (プレビュー) に適用する方法について説明します。

Azure Arc 対応サーバー (プレビュー) は、**米国、ヨーロッパ、またはアジア太平洋**の **[プレビューで使用可能](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** です。

## <a name="data-residency"></a>データの保存場所

Azure Arc 対応サーバー (プレビュー) は、[Azure VM 拡張機能](manage-vm-extensions.md)の構成設定 (つまり、プロパティ値) を格納します。この構成設定で拡張機能を指定した後、接続されているマシンで有効にする必要があります。 たとえば、Log Analytics VM 拡張機能を有効にすると、Log Analytics の**ワークスペース ID** と**主キー**が要求されます。

接続されているマシンに関するメタデータ情報も収集されます。 具体的な内容は次のとおりです。

* オペレーティング システムの名前とバージョン
* コンピューター名
* コンピューターの完全修飾ドメイン名 (FQDN)
* Connected Machine エージェントのバージョン

Arc 対応サーバー (プレビュー) では、データが格納されるリージョンを指定することができます。 Microsoft は、データの回復性を確保するために他のリージョンにレプリケートを行う場合がありますが、地理的外部のデータのレプリケートまたは移動が Microsoft によって行われることはありません。 このデータは、Azure Arc マシン リソースが構成されているリージョンに格納されます。 たとえば、マシンが米国東部リージョンの Arc に登録されている場合、このデータは米国リージョンに格納されます。

リージョンの回復性とコンプライアンス対応のサポートの詳細については、「[Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies/)」を参照してください。

## <a name="next-steps"></a>次の手順

[Azure の回復性](/azure/architecture/reliability/architect)の設計について詳細を確認する。