# git config 

> git 동작에 있어서 설정

* global, local 등으로 옵션을 줄 수 있다.

## 지정된 설정 확인하기

```bash
$ git config --global -l
user.email=edutak.ssafy@gmail.com
user.name=tak
core.editor=code --wait
```

## 초기 설정

```bash
$ git config --global user.name "유저이름"
$ git config --global user.email "이메일"
```

* 커밋을 작성하는 사람이 누구인지 설정해야 함.



## 커밋 편집기 변경

```bash
$ git config --global core.editor "code --wait"
```

* 해당 명령어는 반드시 vs code가 설치되어 있어야 함.

![image-20210604113940149](md-images/image-20210604113940149.png)

* 창을 종료하면,

  ```bash
  $ git commit
  [master 9d7eaf7] Update 초중학교 이력
   1 file changed, 1 insertion(+)
  
  $ git log
  commit 9d7eaf7b87a77448985816e1cbcebc172d0c04b1 (HEAD -> master)
  Author: tak <edutak.ssafy@gmail.com>
  Date:   Fri Jun 4 11:39:13 2021 +0900
  
      Update 초중학교 이력
  
      * 중학교 어디어디 몇년몇월
      * 초등학교 어디어디 몇년몇월
      
  $ git log --oneline -1
  9d7eaf7 (HEAD -> master) Update 초중학교 이력
  
  ```

  