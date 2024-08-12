+++
title = "org-mode에서 디렉토리 설정하기"
author = ["Hillfolk"]
lastmod = 2024-08-12T18:26:06+09:00
tags = ["org-mode", "doom", "emacs", "org-directory"]
categories = ["posts"]
draft = false
weight = 100
+++

---


## Doom Emacs 3.0에서 Org-mode의 디렉토리 설정하기 {#doom-emacs-3-dot-0에서-org-mode의-디렉토리-설정하기}

최근 Doom Emacs 3.0 버전으로 업데이트한 후, Org-mode의 강력한 기능들을 활용해 할 일 목록을 좀 더 체계적으로 관리하고자 org-directory를 설정하기로 결심했습니다. 이 과정에서 제가 참고했던 자료들과, 설정을 진행하면서 겪었던 문제점 및 해결 방법을 기록으로 남겨봅니다.


### 참고한 자료 {#참고한-자료}

-   [Doom Emacs GitHub Repository](https://github.com/doomemacs)
-   [Org-mode 기초 사용법](https://edykim.com/ko/post/start-with-orgmode-basics/)


### org-directory 설정하기 {#org-directory-설정하기}

Org-mode는 다양한 글쓰기 기능을 제공하는데, 그중에서도 가장 유용한 기능 중 하나가 할 일 목록 관리입니다. 그동안은 수동으로 할 일 목록을 관리해왔으나, 이제는 전역적으로 Agenda와 TODO 목록을 활용하기 위해 \`org-directory\`를 설정해보기로 했습니다.

먼저, Doom Emacs의 \`config.el\` 파일에 아래와 같이 경로를 지정했습니다:

```emacs-lisp
(setq org-directory "~/Org/")
```

이 설정을 통해 기본 Org 파일들이 저장될 디렉토리를 지정했습니다. 초기 설정 후, 해당 폴더의 TODO 목록은 잘 표시되었지만, 하위 폴더에 있는 TODO 항목들은 나타나지 않는 문제가 발생했습니다. 이에 대한 해결 방법을 검색해본 결과, 다음과 같은 설정으로 문제를 해결할 수 있었습니다.


### org-agenda-files에 포함된 파일 확인하기 {#org-agenda-files에-포함된-파일-확인하기}

먼저, \`org-agenda-files\`에 포함된 파일 목록을 확인해보기로 했습니다. 루트 디렉토리의 TODO 목록은 잘 나오는 것으로 보아, 하위 파일들이 \`org-agenda-files\`에 포함되지 않은 것이라 판단했습니다.

다음 명령을 통해 파일 목록을 확인할 수 있었습니다:

1.  \`M + :\` (eval-expression 호출)
2.  \`org-agenda-files\` 입력
3.  파일 목록 출력 확인

이 과정을 통해 하위 디렉토리의 Org 파일들이 목록에 포함되지 않은 것을 확인했습니다.


### 하위 디렉토리 포함하기 {#하위-디렉토리-포함하기}

하위 디렉토리에 있는 Org 파일들을 \`org-agenda-files\`에 포함시키기 위해, \`config.el\` 파일에 다음과 같은 코드를 추가했습니다:

```emacs-lisp
(setq org-directory "~/Org/")
(setq org-agenda-files (directory-files-recursively "~/Org/" "\\.org$"))
```

이 설정을 추가한 후, 다시 \`org-agenda-files\` 목록을 확인해보니 하위 파일들까지 모두 포함된 것을 확인할 수 있었습니다. 이제 하위 디렉토리의 TODO 항목들도 Agenda와 TODO 목록에 잘 표시됩니다.

---

위와 같은 방법으로 Org-mode의 할 일 관리 기능을 확장하여, 좀 더 체계적으로 일정을 관리할 수 있게 되었습니다. 이 설정은 지속적으로 유용하게 사용할 것 같습니다.
\`\`\`

이 내용을 Org-mode 파일에 추가하시면 됩니다. 각종 링크와 코드 블록도 Org-mode에서 적절히 렌더링될 것입니다.
