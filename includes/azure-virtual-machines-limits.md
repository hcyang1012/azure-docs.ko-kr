---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334725"
---
| 리소스 | 제한 |
| --- | --- |
| 클라우드 서비스당 [가상 머신](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)<sup>1</sup> |50 |
| 클라우드 서비스당 입력 엔드포인트<sup>2</sup> |150 |

<sup>1개</sup> Azure 리소스 관리자 대신 클래식 배포 모델을 사용하여 만든 가상 시스템은 클라우드 서비스에 자동으로 저장됩니다. 부하 분산 및 가용성을 위해 해당 클라우드 서비스에 더 많은 가상 머신을 추가할 수 있습니다. 

<sup>2</sup>입력 엔드포인트를 사용하여 가상 컴퓨터의 클라우드 서비스 외부에서 가상 컴퓨터에 통신할 수 있습니다. 같은 클라우드 서비스나 가상 네트워크에 있는 가상 머신은 서로 간에 자동으로 통신할 수 있습니다. 자세한 내용은 [가상 시스템에 끝점을 설정하는 방법을](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)참조하십시오. 
