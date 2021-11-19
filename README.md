# Homework_20203234 컴퓨터공학과 지수연
open source SW homework

## 1. getopts 명령어
### 1. 사용목적
  1) 다양한 입력 값이 존재할 때 사용자와 개발자의 편의를 보장
  2) 스크립트를 체계적으로 관리 가능
  3) 명령 실행시 옵션을 사용하는데 파라미터 형태로 전달되므로 옵션 해석 작업을 쉽게 도움
     
     (옵션: short 옵션과 long 옵션이 존재, **getopts는 short 옵션 처리**)
     
  
### 2. 사용방법
> getopts optstring varname [args...]

> ex) ```while getopts "a:b:cdef" opt```
  1) optstring(첫 번째 파라미터): 옵션으로 사용될 문자열 입력
  2) varname(두 번째 파라미터): 옵션으로 활용되는 변수 사용
  3) args: 명령 실행시 사용된 인수 값이 옴, 생략시 "$@" 사용

* bash 스크립트 예시
```
#!bin/bash

while getopts ":a:b:cdef" opt; do
      case $opt in
        a ) APPLE="$OPTARG";;
        b ) BANANA="$OPTARG";;
        c ) CHERRY="$OPTARG";;
        d ) DFRUIT="$OPTARG";;
        e ) EGGPLANT="$OPTARG";;
        f ) FIG="$OPTARG";;
        \?) echo "Invalid option: -"$OPTARG"" >&2
            exit 1;;
        : ) echo "Option -"$OPTARG" requires an argument." >&2
            exit 1;;
      esac
    done
echo "Apple is "$APPLE""
echo "Banana is "$BANANA""
echo "Cherry is "$CHERRY""
echo "Dfruit is "$DFRUIT""
echo "Eggplant is "$EGGPLANT""
echo "Fig is "$FIG""
```

* bash 스크립트 출력
``` bash script.sh -a apple -b banana -c cherry -d dfruit -e eggplant -f fig```

* 결과
```
Apple is apple
Banana is banana
Cherry is 
Dfruit is 
Eggplant is 
Fig is
```


### 3. 주의사항
`:` getopts는 한 개의 문자만을 구분자로 사용
`:`이 붙는다는 것은 뒤에 value가 붙게됨을 의미
인수를 받으면 `:`을 붙여주고 OPTARG 라는 쉘 변수에 실제 옵션의 값이 세팅


### 4. error reporting과 관련하여 모드 제공
||Verbose mode|Silent mode|
|-----|----------|----------|
|invalid 옵션 사용|opt 값을 ? 문자로 설정하고 OPTARG 값은 unset. 오류 메시지를 출력|	opt 값을 ? 문자로 설정하고 OPTARG 값은 해당 옵션 문자로 설정|
|옵션인수 값을 제공하지 않음|opt 값을 ? 문자로 설정하고 OPTARG 값은 unset. 오류 메시지를 출력|opt 값을 : 문자로 설정하고 OPTARG 값은 해당 옵션 문자로 설정|

참고 https://mug896.github.io/bash-shell/getopts.html

----


## 2. getopt 명령어
### 1. 사용목적
### 2. 사용방법
> 


----
## 3. sed 명령어
### 1. 사용목적
  1) 편집(수정, 찾기, 출력 치환, 삭제, 글추가)
  2) 일렬의 명령, 명령 파일을 기반으로 파일의 여러 줄 내용을 수정 가능


### 2. 사용방법
>sed [-e script][-f script-file][file...]

(1) 찾기, 출력
>sed -n '/abd/p' list.txt 
list.txt 파일을 한줄씩 읽으면서 abd 문자를 찾으면 그 줄을 출력(p)
'-n 의미: 읽은 것을 출력하지 않음'

(2) 치환 
>sed 's/addrass/address/' list.txt
addrass를 address로 바꿈 **BUT, 원본파일을 바꾸지 않고 출력만 바꿈**

>sed 's/\t/\ /' list.txt 
탭문자를 엔터로 변환


(3) 추가
>scriptfile - s/

(4) 삭제
>   sed '/없애버릴 글자/d' 파일
예: sed '/TD/d' 1.html
TD 문자가 포함된 줄을 삭제후 출력

> sed '/Src/!d' 1.html
Src 문자가 있는 줄만 지우지 않음

>sed '1,2d' 1.html
처음 1,2줄 지움

(5) 파이프(|)와 사용
> who | sed -e 's; .*$;;'

참고: https://blog.wonizz.tk/2019/03!

(https://user-images.githubusercontent.com/94359749/142359582-e09280e5-ca04-41ca-8c23-0df118fe1b7a.png)
/05/%EB%AA%85%EB%A0%B9%EC%96%B4-sed-%EB%B0%8F-awk-%EC%82%AC%EC%9A%A9%EB%B2%95/


### 3. 특징
  1) 쉘, 쉘스크립트에서 파이프(|)와 같이 사용 가능
  2) 정규표현식 가능 **BUT 특수문자 앞에 역 슬래시를 붙여줘야함**
     ex) sed 's/\$man/man/g' test.txt
