git排除常用配置，svn与git共存时.gitignore配置



```
#idea与myeclipse配置文件全部排除
*.class

#package file
*.war
*.ear

#kdiff3 ignore
*.orig

#maven ignore
target/

#eclipse ignore
.settings/
.project
.classpath

#idea
.idea/
/idea/
*.ipr
*.iml
*.iws

#temp file
*.log
*.cache
*.diff
*.patch
*.tmp

#system ignore
.DS_Store
Thumbs.db

#svn ignore
.svn/
```



