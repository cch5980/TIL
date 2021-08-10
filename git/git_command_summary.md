프로젝트시작(한번만 설정)
git init
==============
작업하고 나서
git add . 
git commit -m '커밋메시지는 잘 작성하기'
==============
원격저장소 설정(한번만 설정)
git remote add origin __url__ <= 저장소 처음 만들면 복사 가능
==============
원격저장소 올리기
git push orgin master

기타 명령어
git statuss
git log

원격저장소 관련
git remote -v 
git remote rm origin  

git초기설정
git config --global user.name <이름>
git config --global user.email <github등록된 이메일>