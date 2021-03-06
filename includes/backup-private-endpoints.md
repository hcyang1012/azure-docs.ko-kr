---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81539616"
---
이제 프라이빗 [엔드포인트를](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) 사용하여 가상 네트워크 내의 서버에서 복구 서비스 자격 증명 모음으로 데이터를 안전하게 백업할 수 있습니다. 개인 끝점은 볼트에 대한 VNET 주소 공간의 IP를 사용합니다. 가상 네트워크 내부의 리소스와 볼트 사이의 네트워크 트래픽은 가상 네트워크와 Microsoft 백본 네트워크의 개인 링크를 통해 이동합니다. 이렇게 하면 공용 인터넷의 노출이 제거됩니다. 개인 엔드포인트는 Azure VM 내에서 실행되는 SQL 및 SAP HANA 데이터베이스를 백업하고 복원하는 데 사용할 수 있습니다. MARS 에이전트를 사용하여 온-프레미스 서버에도 사용할 수 있습니다.

Azure VM 백업에는 인터넷 연결이 필요하지 않으므로 네트워크 격리를 허용하기 위해 개인 끝점이 필요하지 않습니다.

>[!NOTE]
> 이 기능은 현재 사용 가능 여부가 제한되어 있으며 미국 중서부, 미국 중남부, 미국 서부 2 및 미국 동부(따라올 다른 Azure 리전의 가용성)에서 사용할 수 있습니다. [Azure](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) 백업에 대한 비공개 azbackupnetsec@microsoft.com 엔드포인트 사용에 관심이 있는 경우 이 설문 조사를 작성하고 이메일을 보내주십시오. 이 기능을 사용할 수 있는 기능은 Azure Backup 서비스의 승인을 받아야 합니다.
