+++
title = "구글 엔지니어는 이렇게 일한다.(Software Engineering At Google)"
author = ["Hillfolk"]
date = 2022-06-20
lastmod = 2022-07-17T02:29:22+09:00
tags = ["구글", "엔지니어는", "이렇게", "일한다"]
categories = ["posts"]
draft = false
weight = 100
+++

이책은 구글의 엔지니어링의 전반적인 내용을 다루고 있다. 구글의 문화, 프로세스, 도구를 모두 다루고 있다. 다양한 구글 엔지니어들의 경험을 바탕으로 쓰여졌다. 다양한 이야기 중에서 팀워크와 테스트에 대한 2가지의 이야기가 가장 인상깊어다.


## 구글의 엔지니어링 문화 {#구글의-엔지니어링-문화}

구글은 구글다움이라는 것을 겸손 존중 신뢰를 드러내는 태도와 행동들을 정의 하였다.

-   모호함을 뚫고 번창한다.

    끓임없이 변화하는 환경 속에서도 상충하는 메시지와 방향을 잘 대처하고, 합의를 이끌어내고, 문제에 대한 진전을 이룰 수 있습니다.

-   피드백을 소중히 한다.

    피드백을 주고받을 때 품위와 겸손을 유지하고 개인과 팀의 발전 피드백을 주는 가치를 이해합니다.

-   저항(항상성)을 극복한다.

    다른 이들이 저항하거나 관성 때문에 움직이지 않으려 할더라도 야심 찬 목표를 세우고 밀고 나아갑니다.

-   사용자를 우선한다.

    구글 제품의 사용자 입장에서 생각하고 존중하며 그들에게 가장 도움되는 행동을 추구합니다.

-   팀에 관심을 기울인다.

    동료들의 입장에서 생각하고 존중하며 팀의 결집을 위해 누가 시키지 않더라도 적극적으로 돕습니다.

-   옳은 일을 한다.

    모든 일에 강한 윤리 의식을 갖고 힘합니다. 팀과 제품의 진정성을 지키기 위해서라면 어렵거나 불편한 결정을 내릴 수 있어야 합니다.

구글을 구글답게 하는것은 기술적인 내용보다는 가장 기본적인 문화라는 것을 이해할 수 있었다. 구글은 위 내용을 정의한 뒤에는 구글답게라는 모호한 말보다는 위 행동들을 전면에 내새우고 있다.
팀에 좋은 문화를 배양하기 노력하면서 항상 좋은 문화란 어떤 것인지 모호 했는데 내가 팀에 정착되었으면 하는 내용들이 모두 포함도어 있어 놀랐다.


## 단위 테스트 {#단위-테스트}

이책에서 구글의 문화 다음으로 가장 많은 깨우침을 준 부분은 단위 테스트 였다. 나는 프로젝트를 하면서 모든 부분을 테스트하기 위해서는 넓은 범위를 테스트 하는것이 가장 좋다고 생각했다.
그렇기 때문에 프로젝트에서 유닛 테스트를 작성하지 못하더라도 최종적으로 테스트하면 된다는 생각으로 컨트롤러 단의 테스트에 많은 공을 들였다. 하지만 프로젝트를 진행하면서 많은 부분에서 어려움에 만나게 되었다.
변경은 수시로 발생했고 여러 테스트들이 많은 부분에서 깨졌다. 빌드는 실패했고 변경을 위해서 많은 시간을 투자해야 했다. 그 문제를 해결하기 위한 방법을 여러가지 생각했다. 하지만 이책을 읽고 나서 내 생각이 틀렸음을 알았다.

이책에서는 소프트웨어를 견고하게 하기 위해서는 작은 부분의 변하지 않는 테스트를 작성하라고 권장하고 있다. 아래 핵심들을 꼭 기억하자

-   변하지 않는 테스트를 만들기 위해 노력하세요.

    엔지니어가 제품 코드를 변경하는 , 리팩토링,새로운 기능 추가, 버그 수정, 행위변경 중에 행위변경에서만 테스트의 수정이 발생해야 한다. 다른 변경에서 테스트 코드가 수정되어야 하는경우 잘못된 테스트 코드일 확률이 높다.

-   공객 API를 통해 테스트하세요.

    공개 API는 어떤 코드에서 외부에 노출된 인터페이스를 뜻한다. 외부 사용자나 서드파티 모듈등과 같이 다양한 방식으로 노출된 인터페이스를 모두 공개 API 라고 할수 있다.  결론적으로 테스트는 사용자의 관점에서 항상 이루어 져야한다.

-   상호작용이 아닌, 상태를 테스트하세요.

    테스트는 어떤 함수의 호출 여부를 테스트 해서는 안되면 함수가 사용된 이후 상태를 검증해야 한다.

-   테스트를 완전하고 명확하게 만드세요.

    테스트가 실패한 엔지니어는 가장 먼저 실패한 이유에 대해서 조사하게 된다. 이일을 얼마나 빠르게 마치느냐는 테스트의 명확성에 달렸습니다. 명확한 테스트는 존재 이유와 실패 원인을 엔지니어가 곧바로 알아차릴 수 있는 테스트를 말합니다.

-   메서드가 아닌, 행위를 테스트하세요.

    제품의 각 메서드에 테스트를 하나씩 두는 방식은 처음에는 편리하지만 시간이 지날수록 문제를 일으킨다. 대생 메서드가 복잡해 질수록 테스트도 함께 복잡해져서 실패해도 원인을 파악하기 어려워지기 때문이다. 위에서 말하는 해우이는 특정 상태에서 특정한 일련의 입력을 받았을 때 시스템이 보장하는 반응을 뜻합니다.

-   행위가 부각되게끔 테스트를 구성하세요.

    모든 행위는 given, when, then 3가지 요소로 구성된다. given 은 시스템의 설정을 정의하며 when 요소는 시스템이 수행할 작업을 정의한다. 마지막으로 then 은 결과값을 검증한다.  이러한 방식에서 주의할 점은 여러 행위를 동시에 검증해서는 한되며 한 테스트에서는 한가지 행위만 검증하도록 해야 합니다.

-   테스트 이름은 검사하는 행위가 잘 드러나게 지으세요.

    테스트의 이름은 검사하려는 행위를 명확하게 표현하여야 합니다. 때로는 시스템의 상태나 사전 조건을 추가 하기도 합니다. 이름를 나타내기 쉽지 않다면 should 를 사용해 보세요.

-   테스트에 로직을 넣지 마세요.

    제품 코드는 복잡하기 때문에 이를 검증하기 위해 테스트를 작성해야 합니다. 테스트 코드에는 이런 사치가 필요 없습니다. 테스트를 검증하는 테스트를 작성해봐야 할 것 같은 느낌이 든다면 우언가 잘못된 것이니다.

-   실패 메시지를 명확하게 작성하세요.

    테스트의 명확성을 높이기 위해서는 테스트가 실패했을때 전달할 메시지가 명확해야 합니다. 좋은 실패 메시지는 기대한 상태와 실제 상태를 명확히 구분해 추고 결과가 만들어진 맥락 정보도 제공해야 합니다.

-   테스트들이 코드를 공유할 때는 DRY 보다는 DAMP 를 우선하세요.

    테스트에서는 반복하지 않도록 하는 효율성을 중요시 하기 보다는 테스트의 의미 전달을 위해 코드의 중복을 허용하여 작성하는 것이 더 좋습니다.

이 책에는 더 좋은 내용도 많을 것이다. 위에 2가지를 가장 인상깊게 읽은 이유는 현재 내가 가장 관심있어 하는 부분들이였다. 내용중의 대규모 인력 리소스를 사용하거나 구글에서만 사용 할수 있는 리소스가 있는 경우는 조금 이해가 쉽지 않았다. 하지만 대부분의 내용은 엔지니어링에 일반적인 내용이라 많은 도움이 될것 같다.
