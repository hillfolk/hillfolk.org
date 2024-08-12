+++
title = "org-mode에서 디렉토리 설정하기"
author = ["Hillfolk"]
lastmod = 2024-08-12T14:24:33+09:00
tags = ["org-mode", "doom", "emacs", "org-directory"]
categories = ["posts"]
draft = false
weight = 100
+++

---

doom emacs 3.0 버전에서 org 모드의 디렉토리 설정을 하면서 찾아봤던 내용을 기록으로 남긴다.
 [Doom Emacs](https://github.com/doomemacs)
 [org-mode](https://edykim.com/ko/post/start-with-orgmode-basics/)


## org-directory 설정 하기 {#org-directory-설정-하기}

org-mode 에서는 여러가지 글쓰기 기능이 있다. 그중에 가장 편리한 것이 할일 목록 관리인데 그동안 수동적으로 사용하다가 아젠다나 Todo 목록을 글로벌하게 사용하기 위해서 org-directory 설정을 하여 사용하기로 결심하였다.

config.el 파일에 경로를 아래와 같이 지정하였다.

```nil
(setq org-directory "~/Org/")
```

해당 폴더의 todo 목록은 잘 지정되었으나 하위 폴더의 TODO가 나타나지 않았다 검색해 보니 아래와 같은 방법으로 설정이 가능하였다.


### org-agenda-files 에 포함된 파일 확인하기 {#org-agenda-files-에-포함된-파일-확인하기}

해당 폴더가 지정될 경우 루트 경로의 todo 내역은 잘 나오기 때문에 하위 파일이 포함되지 않았다고 판단하고 해당 부분을 확인하는 명령을 찾아 봤다.
아래 명력을 통해서 파일 목록을 확인이 가능했다. 내 하위 파일이 없는 것을 확인 하였고 방법을 찾아 보았다.

1.  M + :
2.  org-agenda-files
3.  파일 목록 출력


### 하위 디렉토리 포함하기 {#하위-디렉토리-포함하기}

config.el 파일에 하위 org 파일을 추가하는 아래 코드를 추가하게 되었다.

```nil
(setq org-directory "~/Org/")
(setq org-agenda-files (directory-files-recursively "~/Org/" "\\.org$"))
```

위 코드 추가 후 다시 한번 org-agenda-files 목록을 확인하면 하위 파일들 까지 잘 들어있는것을 확인할 수 있다.
