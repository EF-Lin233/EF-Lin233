### GITHUB BLOG 

1. Ruby 설치 (v2.7.4)

2. Jekyll Bundler 설치

   ```bash
   gem install jekyll bundler
   ```

3. Github Repository 만들기

4. Jekyll Themes 다운로드 후 레포에 넣기

5. 번들 설치

   ```bash
   bundle install
   ```

6. (TEST) 로컬 호스트로 실행

   ```bash
   bundle exec jekyll serve
   ```

   - http://localhost:4000

<br><br>

#### Jekyll Build error

```bash
github-pages 227 | Error: The jekyll-theme-simplex theme could not be found.
```
- local 에서 사용할 때는 theme 사용
- 배포할 때는 `_config.yml` 에서 theme 주석처리하고, remote_theme 추가</br>
  

```bash
github-pages 227 | Error:  Could not find document '{{ page.nextPart }}' in tag 'link'.
```
- local 에서는 `_layout/post.html` 내의 link tag 이용
- 배포할 때는 link tag 제거

<br><br><br>


### SETTING

#### Category 추가
- `_category` 폴더에 `.md` 파일 생성
- `_data/nav.yaml` 수정

<br><br><br>


### TO DO
- Author photo

  ```
  _author/jeykll.md
  ```
<br>

- Category logo
  - `_category` 내 `hue` 수정
  - `_data/nav.yaml` 수정

  
