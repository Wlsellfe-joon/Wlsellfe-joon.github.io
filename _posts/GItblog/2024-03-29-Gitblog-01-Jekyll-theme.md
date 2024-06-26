---
layout: post
title:  "Gitblog 01 Jekyll theme"
date:   2024-03-29
author: Yongjun Kim
categories: Gitblog
tags:	github gitblog jekyll ruby
cover:  "/assets/korea_river_byKYJ_gray.jpg"
---

## Gitblog 만들기 (1)

Gitblog를 처음 생성할때 몇가지 에러가 발생했었는데, blog 만드는 과정과 에러 해결 과정들을 기록하려 한다. 정리가 잘 된 글들이 많지만 해당 게시글의 전체 과정과 에러 기록들 또한 참고하셔서 빠르게 블로그 만드시길 바란다...^^;;

Gitblog는 간단하게 본인 계정 github에 소스코드 및 posting 글만 올려두면 기록도 하고 여러 사람들이 찾아볼 수도 있음. 광고도 붙일 수 있어서 뭐 나름 좋은듯.

### 1. github 계정 생성<br>
### 2. Ruby설치 및 Local Service Test 
해당 과정은 blog의 테마(jekyll)를 선정하고 블로그를 만들기 전에 거의 필수인 과정이다,, 테마를 하나도 수정하지 않고 그대로 사용하겠다면 생략해도 무방하지만 조금의 커스텀은... 나쁘지 않다고 봄..ㅎㅎ

따라서 테마 커스텀을 하려면 어쨋든 코드를 수정하고 깃허브에 올려서 블로그를 확인해야 할텐데, github에 올리고 동기화 되기까지는 조금의 delay가 발생함.. 따라서 계속 수정하고 확인하고 하려면 병걸릴 수 있음.

그래서 github에 올리기 전에 자신의 local PC에서 임의로 블로그가 service되는 모습을 바로바로 확인할 수 있도록 지원해주는 도구임

그냥 하는게 좋음!

설치과정:

- Ruby 다운로드 링크 "[Here][ruby]"에서 Ruby 권장 사항으로 설치 진행

- 마지막 설치 페이지에서 Run..어쩌고 하는 박스 체크해서 업데이트 창 진입

- 1번과 2번에 대해서 Enter키 누르고 업데이트

- 설치 후, Start Command Prompt with Ruby 관리자 권한으로 실행

- encoding error 있을 경우 실행
{% highlight html %}
{% raw %}
chcp 65001
{% endraw %}
{% endhighlight %}


- 아래 명령으로 jekyll 설치
{% highlight html %}
{% raw %}
gem install jekyll bundler
{% endraw %}
{% endhighlight %}

이제 Local service test를 진행하면 된다,, 임의로 jekyll posting 파일을 만들고 해당 파일 내부에서 cmd로 진행하면 됨

{% highlight html %}
{% raw %}
jekyll new ./
bundle install
bundle exec jekyll serve
{% endraw %}
{% endhighlight %}

여기서 "Serve address: http://127.0.0.1:4000" 확인하면 성공! 해당 페이지 접속해서 local service를 확인하고 해당 폴더는 테스트용이므로 삭제하면 된다.
<br><br><br>

### 3. Error 관련 (Encoding error: UTF-8)
여기서 많이들 에러가 발생하는 것 같던데 **"포스트 파일 경로 및 Ruby 설치파일 경로"**에 한글이 없어야 한다.. 한글이 있을 경우 보통 UTF-8 encoding error가 발생한다;;;

이 외에도 config.yml 에서 encoding:UTF-8을 선언하거나 Ruby 설치파일에 포함된 _sass에 encoding관련 내용을 추가하여 에러를 해결한 사람도 많이 있는 것 같다.

참고로 vscode에서 gitbash terminal 연결해서 하면 개편함!!!<br><br><br>

[ruby]: https://rubyinstaller.org/downloads/
