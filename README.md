# Hexo

## 过程

```
$ cd blog
$ rm -rf themes/maupassant/.git*
$ echo "public/" > .gitignore
```

```
$ git init
$ git remote add origin git@github.com:dctxmei/blog.git
$ git checkout -b hexo
$ git submodule add git@github.com:dctxmei/blog.git .deploy_git
$ git add .
$ git commit -m "源文件"
$ git push -u origin hexo
```

---

## 克隆

```
$ git clone git@github.com:dctxmei/blog.git -b hexo --recursive
```

## 推送

```
$ git add .
$ git commit -m "源文件"
$ git push origin hexo
```

## Themes

### Fork 同步

```
$ git clone git@github.com:dctxmei/maupassant-hexo.git && cd maupassant-hexo
$ git remote add upstream https://github.com/tufu9441/maupassant-hexo.git
$ git fetch upstream
$ git checkout master
$ git merge upstream/master
$ git push
```

### Blog 跟进

```
$ git clone git@github.com:dctxmei/maupassant-hexo.git themes/maupassant
$ rm -rf themes/maupassant/.git*
```
