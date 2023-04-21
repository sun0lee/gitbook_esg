# Git

`origin` 원격 저장소를 지칭

`origin/main` 원격 저장소의 main 브랜치&#x20;

`origin/HEAD` 원격 저장소의 현재 체크아웃된 커밋.&#x20;



## start a working area&#x20;

(see also: git help tutorial)

&#x20;  **clone**     Clone a repository into a new directory&#x20;

```bash
git clone https://github.com/[사용자 이름]/[저장소 이름].git
```

* 원격 저장소(remote repository)에 있는 Git 저장소를 복사해서 로컬 저장소(local repository)로 가져오기&#x20;



&#x20;  **init**      Create an empty Git repository or reinitialize an existing one

* 로컬 디렉토리를 Git 저장소(repository)로 초기화, 새로운 Git 저장소를 생성,  `master` 브랜치가 생성
* `git init` 명령어를 실행하면, 현재 디렉토리에 `.git`이라는 하위 디렉토리가 생성
* &#x20;이 디렉토리에 Git 저장소에 필요한 파일들이 생성.(Git으로 버전 관리하기 위한 파일 및 폴더, Git에서 커밋이나 브랜치, 태그 등의 작업을 할 때 이 파일들을 이용하여 관리함)\




## work on the current change&#x20;

(see also: git help everyday)

&#x20;  **add**       Add file contents to the index

```bash
#변경된 모든 파일을 스테이징에 추가
git add .

#해당 경로에 특정 파일을 스테이징에 추가
git add _gmv/ra.md 

#해당 경로의 변경된 모든 파일을 스테이징에 추가
git add _gmv
```

* 파일의 추가, 수정, 삭제 등의 변경사항을 스테이징 영역(커밋할 파일을 선별하는 작업공간)에 추가



&#x20;  **mv**        Move or rename a file, a directory, or a symlink

```bash
# git mv <원래 파일 경로/이름> <새 파일 경로/이름>
git mv myproject/oldfile.txt myproject/newfile.txt
```

* `oldfile.txt` 파일이 `newfile.txt`로 이름이 변경되고, 이 변경사항이 추적됨.&#x20;



&#x20;  **restore**   Restore working tree files

```bash
# 변경내용 취소 
git restore --staged myproj/main.txt 

# 삭제파일 복원
git restore myproj/main.txt 
```

* 변경내용 취소 / 작업 트리에서 삭제된 파일 복구 : 변경되거나 삭제된 파일을 이전 커밋 상태로 되돌리기&#x20;



&#x20;  **rm**        Remove files from the working tree and from the index

```bash
# 작업트리와 git에서 모두 삭제 
git rm myproj/main.txt 

# Git에서만 삭제하고 작업트리에는 그대로 두기 
git rm --cached myproj/main.txt 
```



## examine the history and state&#x20;

(see also: git help revisions)

&#x20;  **bisect**    Use binary search to find the commit that introduced a bug&#x20;



&#x20;  **diff**      Show changes between commits, commit and working tree, etc

```bash
# 이전 커밋id, 브랜치 이름 
git diff <commit> 
```



&#x20;  **grep**      Print lines matching a pattern

```bash
# epv를 포함하는 모든 파일 검색 
git grep "epv" 
```



&#x20;  **log**       Show commit logs

```bash
# 한줄씩 간략하게 
git log --oneline 

# lee가 작성한 모든 커밋 출력 
git log --author ="lee" 
```

* Git 저장소의 커밋 로그를 출력. Git 저장소에 커밋된 내용을 시간순으로 확인할 수 있으며, 커밋 메시지, 커밋한 사람, 커밋한 시간 등의 정보를 제공.
* 현재 브랜치의 커밋 로그.&#x20;



&#x20;  **show**      Show various types of objects

```bash
# git show 해시값 (특정 커밋버전)
git show 6f01eda  
```

* 이전에 커밋된 변경내용을 보여주는 명령어, 특정 커밋에서 변경된 사항을 상세하게 볼 때 씀&#x20;
  * 커밋 메시지
  * 커밋한 시간과 작성자 정보
  * 해당 커밋에서 변경된 파일 목록과 내용
  * 변경 내용의 diff



&#x20;  **status**    Show the working tree status

<figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

* 현재 작업 중인 디렉토리나 파일의 상태 (어떤 파일이 변경되었고, 어떤 파일이 스테이지 영역에 추가되었는지, 커밋되었는지 등의 정보를 확인)
  * 작업 중인 브랜치의 이름
  * 변경된 파일의 목록 (추가, 수정, 삭제된 파일)
  * 스테이지 영역에 추가된 파일의 목록
  * 아직 스테이지 영역에 추가되지 않은 수정된 파일의 목록
  * 아직 스테이지 영역에 추가되지 않은 새로 생성된 파일의 목록



## grow, mark and tweak your common history

\=> 내 local에서 일어나는 일.&#x20;

&#x20;  **branch**    List, create, or delete branches 작업중인 브랜치 보기&#x20;

```bash
git branch

* dev. # 현재 선택된 브랜치를 알려줌 
  main
  stage1
  stage2
```



&#x20;  **commit**    Record changes to the repository&#x20;

```bash
# 반드시 커밋 메세지와 함께 커밋해야 함. 
git commit -m "커밋 메세지"
```

* 변경내용을 로컬 저장소에 커밋함 -> 내 로컬에 저장 !



&#x20;  **merge**     Join two or more development histories together

```bash
# git merge 합칠브랜치이름 (target)
# 지금 내가 작업중 dev 
# 통합하고 싶은 브랜치 main 
 
git merge main

# dev = main 동기화 됨. 
```

* 두 개의 브랜치에 있는 커밋들을 하나의 브랜치로 합치기

```bash
# merge 후 더 이상 브랜치가 필요 없는 경우 삭제 
git breanch -d dev  
```



&#x20;  **rebase**    Reapply commits on top of another base tip

<details>

<summary>rebase vs. merge</summary>

`merge`와 `rebase`는 모두 브랜치를 합치는 명령어입니다.

`merge`는 두 개 이상의 브랜치를 하나의 새로운 커밋으로 합치는 방법입니다. `merge`를 실행하면 브랜치의 모든 변경 사항이 포함된 새로운 커밋이 생성됩니다. 이 새로운 커밋은 두 개 이상의 부모 커밋을 가지고 있으며, 모든 부모 커밋의 변경 내용이 포함되어 있습니다. 따라서 기존의 브랜치들은 변경되지 않습니다.

`rebase`는 다른 브랜치의 변경 사항을 현재 브랜치에 적용하는 방법입니다. `rebase`를 실행하면 현재 브랜치가 선택한 브랜치의 최신 커밋에 기반하여 재작성됩니다. 이렇게 재작성된 커밋은 새로운 커밋으로 취급됩니다. 따라서 브랜치의 커밋 히스토리가 변경될 수 있습니다.

따라서 `merge`는 브랜치를 합치는 가장 간단한 방법이고, `rebase`는 브랜치를 깨끗하게 유지하면서 변경 사항을 적용하는 방법입니다.

</details>

* dev 브랜치에서 작업하다가 main 브랜치 변경사항 반영하기&#x20;
  1. `dev` 브랜치에서 원격 저장소의 변경사항을 가져오기(`git fetch`)
  2. `dev` 브랜치로 체크아웃
  3. `git rebase origin/main` 명령어를 사용하여 `main` 브랜치의 변경사항을 `dev` 브랜치에 반영&#x20;
  4. 이렇게 하면 `dev` 브랜치에서 작업한 커밋들이 `main` 브랜치의 변경사항 위에 올라가게 됩니다.



&#x20;  **reset**     Reset current HEAD to the specified state

&#x20;  **switch**    Switch branches 작업중인 브랜치 바꾸기&#x20;

&#x20;  **tag**       Create, list, delete or verify a tag object signed with GPG

\




## collaborate&#x20;

(see also: git help workflows)

&#x20;  fetch     Download objects and refs from another repository

&#x20;  pull      Fetch from and integrate with another repository or a local branch

&#x20;  **push**      Update remote refs along with associated object

로컬 저장소의 변경내용을 원격 저장소에 업로드 하는 명령어 (다른 사람과 공유)





{% embed url="https://git-scm.com/book/ko/v2/%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-Git-%EA%B8%B0%EC%B4%88" %}
