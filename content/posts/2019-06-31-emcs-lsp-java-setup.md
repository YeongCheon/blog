+++
title = "이맥스에서 lsp-mode를 이용해 자바 개발환경 셋팅하기"
author = ["yeongcheon"]
date = 2019-06-30T21:00:00+09:00
lastmod = 2019-11-02T22:18:37+09:00
tags = ["emacs", "java", "lsp"]
draft = false
+++

이맥스에서 자바 개발환경을 셋팅해보자. 우선 기본적으로 이맥스와 자바, gradle 등등은 설치가 되어있다고 가정한다. 포스트 작성 기준은 아래와 같다.

-   OS: `ubuntu 18.04 LTS`
-   emacs: `GNU Emacs 25.2.2`
-   java: `openjdk version "1.8.0_212"`
-   gradle: `Gradle 4.10.2`

이 포스트에선 개발환경 셋팅을 위해서 [lsp-mode](https://github.com/emacs-lsp/lsp-mode)를 이용할 예정이다. 본격적으로 셋팅하기에 앞어서 lsp가 무엇인지 간단하게 한번 알아보자


## LSP란? {#lsp란}

lsp는 [Language Server Protocol](https://microsoft.github.io/language-server-protocol/)의 약자이다(링크를 보는게 훨씬 도움이 된다). 간단히 말하자면 각 개발툴마다 제각각 다뤄왔던 자동완성, jump to definition 등등의 기능을 하나의 프로토콜로 표준화 하여 하나의 language server를 여러 개발툴에서 동일하게 사용할 수 있도록 하는 규약(프로토콜)을 의미한다. (~~번역기 발췌~~)


## lsp-mode 설치하기 {#lsp-mode-설치하기}

lsp의 뜻을 알아봤으니 그럼 이제 본격적으로 셋팅작업에 들어가보자. 이맥스의 lsp-mode는 기본적으로 [여기](https://github.com/emacs-lsp/lsp-mode)를 베이스로 한다. 이번 포스트는 java 환경을 구축하는게 목적이니 [lsp-java](https://github.com/emacs-lsp/lsp-java)를 설치해주자. 설치하는 방법은 링크의 [READMD.md](https://github.com/emacs-lsp/lsp-java/blob/master/README.md)에 자세하게 나와있다. 내가 실행한 명령어는 아래와 같다.

```nil
M-x package-install use-package
M-x package-install lsp-java
M-x package-install dash
```

총 3개의 패키지를 설치한다.

-   [use-package](https://github.com/jwiegley/use-package)
-   [lsp-java](https://github.com/emacs-lsp/lsp-java)
-   [dash](https://github.com/magnars/dash.el)

dash 패키지의 경우 공식 가이드에서 설치하라는 설명은 없었지만 셋팅이 제대로 진행되지 않아 나름 구글링을 해본 결과 얻어낸 답이다.

```elisp
(require 'lsp-java)
(add-hook 'java-mode-hook #'lsp)
(add-hook 'java-mode-hook 'flycheck-mode)
(add-hook 'java-mode-hook 'company-mode)

(require 'cc-mode)

(use-package projectile :ensure t)
(use-package yasnippet :ensure t)
(use-package lsp-mode :ensure t)
(use-package hydra :ensure t)
(use-package company-lsp :ensure t)
(use-package lsp-ui :ensure t)
(use-package lsp-java :ensure t :after lsp
  :config (add-hook 'java-mode-hook 'lsp))

(use-package dap-mode
  :ensure t :after lsp-mode
  :config
  (dap-mode t)
  (dap-ui-mode t))

(use-package dap-java :after (lsp-java))
```

모든 과정을 끝냈다면 이제 기본적인 셋팅은 마무리가 된 상태다. 그럼 이제 실제로 자바 프로젝트를 생성한 후 이맥스에서 자동완성, jump to difinition 등등이 제대로 동작하는지 확인해보자. 자바프로젝트 생성은 gradle를 이용한다. 아래의 명령어를 터미널에 입력해보자.

```bash
gradle init --type java-library
```

프로젝트가 정상적으로 프로젝트가 생성되었다면 이맥스에서 src/main/java/Library.java 파일을 열어보자. 셋팅 후 최초로 자바파일을 열었을 경우 [Eclipse JDT Language Server](https://projects.eclipse.org/projects/eclipse.jdt.ls)를 설치할건지 묻는 메세지가 뜬다. y를 입력해서 language server를 설치해주도록 하자. 참고로 이 내용은 [공식 가이드](https://github.com/emacs-lsp/lsp-java#eclipse-jdt-language-server)에서도 나오는 내용이다(~~그니깐 이거 말고 공식 가이드 읽으세요~~).

설치를 마치고 이맥스를 재시작 한 후 다시 Library.java를 열어서 이것저것 코드를 입력해보자.

{{< figure src="/ox-hugo/2019-06-30_22-39-45.png" >}}

이제 기본적인 셋팅이 마무리 되었다. 코드 실행, 디버깅 방법은 이후 별도의 포스팅에서 다루도록 하겠다.
