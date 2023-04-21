# Git



## start a working area&#x20;

(see also: git help tutorial)

&#x20;  **clone**     Clone a repository into a new directory&#x20;

* 원격 저장소(remote repository)에 있는 Git 저장소를 복사해서 로컬 저장소(local repository)로 가져오기&#x20;

```bash
git clone https://github.com/[사용자 이름]/[저장소 이름].git
```

&#x20;  **init**      Create an empty Git repository or reinitialize an existing one

* 로컬 디렉토리를 Git 저장소(repository)로 초기화, 새로운 Git 저장소를 생성,  `master` 브랜치가 생성
* `git init` 명령어를 실행하면, 현재 디렉토리에 `.git`이라는 하위 디렉토리가 생성
* &#x20;이 디렉토리에 Git 저장소에 필요한 파일들이 생성.(Git으로 버전 관리하기 위한 파일 및 폴더, Git에서 커밋이나 브랜치, 태그 등의 작업을 할 때 이 파일들을 이용하여 관리함)\




## work on the current change&#x20;

(see also: git help everyday)

&#x20;  **add**       Add file contents to the index

&#x20;  mv        Move or rename a file, a directory, or a symlink

&#x20;  restore   Restore working tree files

&#x20;  rm        Remove files from the working tree and from the index

\




## examine the history and state&#x20;

(see also: git help revisions)

&#x20;  bisect    Use binary search to find the commit that introduced a bug

&#x20;  diff      Show changes between commits, commit and working tree, etc

&#x20;  grep      Print lines matching a pattern

&#x20;  log       Show commit logs

&#x20;  show      Show various types of objects

&#x20;  status    Show the working tree status

\




## grow, mark and tweak your common history

&#x20;  branch    List, create, or delete branches

&#x20;  commit    Record changes to the repository

&#x20;  merge     Join two or more development histories together

&#x20;  rebase    Reapply commits on top of another base tip

&#x20;  reset     Reset current HEAD to the specified state

&#x20;  switch    Switch branches

&#x20;  tag       Create, list, delete or verify a tag object signed with GPG

\




## collaborate&#x20;

(see also: git help workflows)

&#x20;  fetch     Download objects and refs from another repository

&#x20;  pull      Fetch from and integrate with another repository or a local branch

&#x20;  push      Update remote refs along with associated object





{% embed url="https://git-scm.com/book/ko/v2/%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-Git-%EA%B8%B0%EC%B4%88" %}
