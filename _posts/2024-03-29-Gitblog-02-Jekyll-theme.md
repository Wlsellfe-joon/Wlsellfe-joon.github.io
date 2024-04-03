---
layout: post
title:  "Gitblog 02 Jekyll theme"
date:   2024-03-29
author: Yongjun Kim
categories: Gitblog
tags:	github gitblog jekyll ruby
cover:  "/assets/korea_river_byKYJ.jpg"
---

## Gitblog 만들기 (2)

지난 gitblog posting에 이어서 Jekyll theme을 적용하는 부분에 대해서 포스팅 하고자 한다. 먼저 지난번 생성했던 폴더에서 "jekyll new ./"로 생성했던 모든 내용들을 삭제하고 진행하면 되겠다.

### 4. Jekyll theme 선정
- 링크: [<u>Link</u>][jekyll]
- 링크에서 다양한 테마들의 Demo를 진행하고, 원하는 블로그 테마를 선택!

### 5. Jekyll theme 적용
- 위 4번에서 원하는 테마를 선정했다면 해당 테마의 Source를 받아올 수 있도록 (Fork할 수 있도록) 내 Github 계정에 Repository를 생성해야 한다.

- Github home에서 Repositories부분 'New'를 선택하여 새로운 Repository 생성 (무조건 Repository name을 "[본인 계정 ID].github.io"로 설정해야 함)

- 이후, 아까 4번에서 선택해둔 테마의 Source를 나의 github Repository에 옮겨야 함, 방법은 많으니 편한 방법으로 하면 됨..

> a. 선택한 테마의 Homepage 클릭 > code 클릭 > 다운로드 *.zip > 본인 local 임시 폴더에 풀고 위에서 생성한 본인 Repository에 git push

> b. 선택한 테마의 Homepage 클릭 > code 상단의 Fork 클릭 > 본인 Repository에 git fork

> c. 선택한 테마에서 바로 다운로드 클릭 > 로컬 폴더에 압축 풀고, 본인 Repository에 git push

- 개인적으론 a가 제일 깔끔한 듯 하다.. ^^

<br>
### 6. Local serve로 확인하며 블로그 커스텀 하기
- 원하는 테마를 저장한 본인 로컬 파일에서 cmd창 혹은 gitbash를 열고, 
{% highlight html %}
{% raw %}
bundle exec jekyll serve
{% endraw %}
{% endhighlight %}
를 입력한 후, local service page를 웹에서 열면 본인 블로그 모습이 보인다! 해당 모습을 확인하면서 post, config.yml, css,,,등등 본인 취향으로 블로그를 수정하면 블로그에 바로 적용되는 모습을 확인할 수 있다!!

<br>
### 7. Gitblog 마무리
- local service를 확인하며 커스텀을 마쳤다면 본인 github page에 push해주고 "계정 이름.github.io"로 페이지를 확인하면 블로그 만들기 끝!

[jekyll]: http://jekyllthemes.org/page4/