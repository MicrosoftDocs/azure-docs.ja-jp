



Azure SKU 間で計算 (CPU) パフォーマンスを比較する手段を提供するため、Azure Compute Unit (ACU) の概念が作成されています。 これは、パフォーマンス ニーズを満たす可能性が最も高い SKU を簡単に見つけるのに役立ちます。  現在、ACU は小さい (Standard_A1) VM を 100 として標準化されており、他のすべての SKU についてはその SKU が標準ベンチマークをそれよりどれくらい速く実行できるかが表されます。 

> [!IMPORTANT]
> ACU はガイドラインに過ぎません。  ワークロードの結果は異なる場合があります。 
> 
> 

<br>

| SKU ファミリ | ACU/コア |
| --- | --- |
| [A0](../articles/virtual-machines/windows/sizes-general.md) |50 |
| [A1 ～ A4](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A5 ～ A7](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A1_v2 ～ A8_v2](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A2m_v2 ～ A8m_v2](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A8 ～ A11](../articles/virtual-machines/windows/sizes-hpc.md) |225* |
| [D1 ～ D14](../articles/virtual-machines/windows/sizes-general.md) |160 |
| [D1_v2 ～ D15_v2](../articles/virtual-machines/windows/sizes-general.md) |210 - 250* |
| [DS1 ～ DS14](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 |
| [DS1_v2 ～ DS15_v2](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |210-250* |
| [F1 ～ F16](../articles/virtual-machines/windows/sizes-compute.md) |210-250* |
| [F1s ～ F16s](../articles/virtual-machines/windows/sizes-compute.md) |210-250* |
| [G1 ～ G5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 ～ 240* |
| [GS1 ～ GS5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 ～ 240* |
| [H](../articles/virtual-machines/windows/sizes-hpc.md) |290 ～ 300* |
| [L4s ～ L32s](../articles/virtual-machines/windows/sizes-storage.md) |180 ～ 240* |

* が付いている ACU は、Intel® Turbo テクノロジを使用して CPU 周波数を上げ、パフォーマンスを増強します。  増強量は、VM のサイズ、ワークロード、および同じホストで実行されている他のワークロードによって変化します。
