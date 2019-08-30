---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5df0fe23183b53f13d6c6545e248724324434cde
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029815"
---
ストレージ アカウントのレプリケーション オプションには、次のようなものがあります。

* [ローカル冗長ストレージ (LRS)](../articles/storage/common/storage-redundancy-lrs.md):シンプルな低コストのレプリケーション戦略です。 データがプライマリ リージョン内で 3 回、同期的にレプリケートされます。
* [ゾーン冗長ストレージ (ZRS)](../articles/storage/common/storage-redundancy-zrs.md): 高可用性を必要とするシナリオ向けのレプリケーション。 データは、プライマリ リージョンの 3 つの Azure 可用性ゾーン間で同期的にレプリケートされます。
* [geo 冗長ストレージ (GRS)](../articles/storage/common/storage-redundancy-grs.md):リージョン全体のサービス停止に備えたリージョン間レプリケーションです。 データは、プライマリ リージョンで 3 回同期的にレプリケートされた後、セカンダリ リージョンに非同期的にレプリケートされます。 セカンダリ リージョンのデータへの読み取りアクセスのために、読み取りアクセス geo 冗長ストレージ (RA-GRS) を有効にします。
* [geo ゾーン冗長ストレージ (GZRS) (プレビュー)](../articles/storage/common/storage-redundancy-gzrs.md):高可用性と最大限の持続性の両方を必要とするシナリオ向けのレプリケーションです。 データは、プライマリ リージョンの 3 つの Azure 可用性ゾーン間で同期的にレプリケートされた後、セカンダリ リージョンに非同期的にレプリケートされます。 セカンダリ リージョンのデータへの読み取りアクセスのために、読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS) を有効にします。
