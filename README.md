### GITHUB BLOG 

1. 下载 Ruby

2. cd到blog本地仓库后 用 Jekyll Bundler

   ```bash
   bundle exec jekyll serve -P 5555 --watch
   ```

3. Github 创建 Repository 
   上传到github， 先打开gitbash cd 到仓库,需要先查看一下$git remote -v$远程仓库匹不匹配

4. 基本流程

   ```bash
   git init
   git add .
   git commit -m "xxx"
   git push -u origin master
   ```

#### Jekyll Build error

可能报一些奇奇怪怪的错,例如git commit 时要先确定

```bash
git config --global user.email "usr@email.com"
git config --global user.name "usr"
```

  
