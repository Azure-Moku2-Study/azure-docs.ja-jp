---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: 922f4d5dd8c71bc9365523b1d539d1b0754fff15
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580941"
---
geo 冗長ストレージ (GRS) は、プライマリ リージョンから数百マイル離れたセカンダリ リージョンにデータをレプリケートすることで、通年で少なくとも 99.99999999999999% (シックスティーンナイン) の持続性をオブジェクトに確保するように設計されています。 ご使用のストレージ アカウントで GRS が有効になっている場合は、地域的な停電やプライマリ リージョンが復旧できない災害が発生しても、データは保持されます。

GRS を選択する場合は、関連する 2 つの選択肢から選択できます。

* GRS は、セカンダリ リージョンの別のデータセンターにデータをレプリケートしますが、Microsoft がプライマリ リージョンからセカンダリ リージョンへのフェールオーバーを開始する場合にのみ、そのデータを読み取ることができます。 
* 読み取りアクセス geo 冗長ストレージ (RA-GRS) は GRS に基づいています。 RA-GRS は、セカンダリ リージョンの別のデータセンターにデータをレプリケートします。また、セカンダリ リージョンから読み取るオプションもあります。 RA-GRS を使用すると、Microsoft がプライマリからセカンダリへのフェールオーバーを開始するかどうかにかかわらず、セカンダリから読み取ることができます。 

GRS または RA-GRS が有効なストレージ アカウントでは、すべてのデータが最初にローカル冗長ストレージ (LRS) でレプリケートされます。 更新は、まずプライマリの場所にコミットされ、LRS を使用してレプリケートされます。 更新は、GRS を使用してセカンダリ リージョンに非同期にレプリケートされます。 データがセカンダリの場所に書き込まれると、LRS を使用してその場所内にもレプリケートされます。 

プライマリ リージョンとセカンダリ リージョンの両方が、ストレージ スケール ユニット内の異なる障害ドメインとアップグレード ドメイン間でレプリカを管理します。 ストレージ スケール ユニットは、データセンター内の基本的なレプリケーション ユニットです。 このレベルのレプリケーションは LRS で提供されています。詳細については、「[ローカル冗長ストレージ (LRS): Azure Storage の低コストのデータ冗長性](../articles/storage/common/storage-redundancy-lrs.md)」を参照してください。

使用するレプリケーション オプションを決定するときは、次の点に注意してください。

* ゾーン冗長ストレージ (ZRS) は同期レプリケーションの可用性を高めるため、シナリオによっては GRS または RA-GRS よりも適した選択肢となります。 ZRS の詳細については、[ZRS](../articles/storage/common/storage-redundancy-zrs.md) に関するページを参照してください。
* 非同期レプリケーションには遅延が伴うため、地域的な災害が発生した場合にデータをプライマリ リージョンから復旧できないと、セカンダリ リージョンにまだレプリケートされていない変更は失われます。
* GRS では、Microsoft がセカンダリ リージョンへのフェールオーバーを開始しない限り、レプリカは利用できません。 Microsoft がセカンダリ リージョンへのフェールオーバーを開始した場合、フェールオーバーの完了後、そのデータへの読み取り/書き込みアクセスができるようになります。 詳細については、「[障害復旧のガイダンス](../articles/storage/common/storage-disaster-recovery-guidance.md)」を参照してください。
* アプリケーションがセカンダリ リージョンから読み取る必要がある場合は、RA-GRS を有効にします。