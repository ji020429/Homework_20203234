# Homework_20203234 컴퓨터공학과 지수연
open source SW homework

----
## 1. getopts 명령어
### 1. 사용목적
  1) 다양한 입력 값이 존재할 때 사용자와 개발자의 편의를 보장
  2) 스크립트를 체계적으로 관리 가능
  3) 명령 실행시 옵션을 사용하는데 파라미터 형태로 전달되므로 옵션 해석 작업을 쉽게 도움
     
     (옵션: short 옵션과 long 옵션이 존재, **getopts는 short 옵션 처리**)
     
  
### 2. 사용방법
> getopts optstring varname [args...]

> ex) ```while getopts "a:b:cdef" opt```
  1) optstring(첫 번째 파라미터): 옵션으로 사용될 문자열 입력, 옵션을 정의하는 문자
  2) varname(두 번째 파라미터): 옵션으로 활용되는 변수 사용
  3) args: 명령 실행시 사용된 인수 값이 옴, 생략시 "$@" 사용

* bash 스크립트 예시
```
#!bin/bash

while getopts 'srd:f:' c 
  do 
    case $c in 
      s) ACTION=SAVE ;; 
      r) ACTION=RESTORE ;; 
      d) DB_DUMP=$OPTARG ;; 
      f) TARBALL=$OPTARG ;; 
     esac 
  done
```


### 3. 주의사항
`:` getopts는 한 개의 문자만을 구분자로 사용
`:`이 붙는다는 것은 뒤에 value가 붙게됨을 의미
인수를 받으면 `:`을 붙여주고 OPTARG 라는 쉘 변수에 실제 옵션의 값이 세팅


### 4. 기타
**<error reporting과 관련하여 모드 제공>**
||Verbose mode|Silent mode|
|-----|----------|----------|
|invalid 옵션 사용|opt 값을 ? 문자로 설정하고 OPTARG 값은 unset. 오류 메시지를 출력|	opt 값을 ? 문자로 설정하고 OPTARG 값은 해당 옵션 문자로 설정|
|옵션인수 값을 제공하지 않음|opt 값을 ? 문자로 설정하고 OPTARG 값은 unset. 오류 메시지를 출력|opt 값을 : 문자로 설정하고 OPTARG 값은 해당 옵션 문자로 설정|

참고1 https://mug896.github.io/bash-shell/getopts.html

참고2 https://systemdesigner.tistory.com/17

참고3 https://jungfo.tistory.com/159

----


## 2. getopt 명령어
### 1. 사용목적
  1) 다양한 입력 값이 존재할 때 사용자와 개발자의 편의를 보장
  2) 스크립트를 체계적으로 관리 가능
  3) 명령행 인자로 전달된 옵션을 편리하게 처리할 수 있도록 도와주는 함수
     
     
     (옵션: short 옵션과 long 옵션이 존재, **getopt는 long 옵션 처리, short 옵션도 처리 **)
 
 
### 2. 사용방법![getopt](https://user-images.githubusercontent.com/94359749/142604370-fe1d95b9-1a4f-459c-b832-333acd949ab3.PNG)
![Uploading getopt.PNG…]()

> get -o|--options shortopts와 <-l|--longoptions longopts> <-n|--name progname> <--> parameters

> ex) getopt -o d:u:f:h -l diffs:,unit:,format:,help -- $@
* ex) 실행
> $(getopt -o d:u:f:h -l diffs:,unit:,format:,help -- "$@")

  1) -o 와 --options 같기 때문에 하나 골라서 적용
  2) shortopts: 옵션으로 사용될 문자열 입력, 옵션을 정의하는 문자
  3) longopts: 긴 옵션을 정의하는 문자 (ex. diffs와 같은 긴 옵션 정의) ,(콤마)로 구분
  4) progname: 오류 발생시 리포팅할 프로그램 명칭(**현재 셀 스크립트 파일명**)
  5) parameters: 옵션에 해당하는 실제 명령 구문 (ex. 모든 파라미터를 뜻하는 $@ 사용)

```
#!/bin/sh

print_try() {
    echo "Try './datefmt3.sh -h' for more information!"
    exit 1
}

print_help() {
    echo "usage : ./datefmt3.sh -d <diffs> -u <unit> [-f format]"
    echo "usage : ./datefmt3.sh --diffs <diffs> --unit <unit> [--format format]"
    exit 1
}

options="$(getopt -o d:u:f:h -l diffs:,unit:,format:,help -- "$@")"
eval set -- $options

while true
do
    # echo "$1, $2  [$@]"
    case $1 in
        -d|--diffs)
            D=$2
            shift 2;;
        -u|--unit)
            U=$2
            shift 2;;
        -f|--format)
            F=$2
            shift 2;;
        -h|--help)
            print_help;;
        --)
            break;;
        *)
            print_try;;
    esac
done

RET=`date $F --date="$D $U"`
echo "$RET"
```

참고1 https://www.youtube.com/watch?v=DS3PV1q3dwU&ab_channel=%EC%8B%9C%EB%8B%88%EC%96%B4%EC%BD%94%EB%94%A9

참고2 https://github.com/indiflex/refs/tree/main/linux

### 3. 주의사항
`--` 의 뒤 parameters 에서
> ex) $(**getopt** -o d:u:f:h -l diffs:,unit:,format:,help -- **$@**) **"$@" 쌍따옴표가 없으면 $@가 아닌 getopt에 가짐**
 
 
#### 4. 기타
**<getopt 관련규칙>**
[규칙 3] 옵션의 이름은 한 글자여야 한다.
[규칙 4] 모든 옵션의 앞에는 하이픈(-)이 있어야한다. 이중 하이픈도 사용(--opt)
[규칙 5] 인자가 없는 옵션은 하나의 - 다음에 묶여서 올 수 있다.(예: -abc)
[규칙 6] 옵션의 첫 번째 인자는 공백이나 탭으로 띄고 입력해야 한다.(예: -l /usr/bin)
[규칙 7] 인자가 있어야 하는 옵션에서 인자를 생략할 수 없다.
[규칙 9] 모든 옵션은 명령의 인자보다 앞에 와야한다.(예: ls -a /usr/bin)
[규칙 10] 옵션의 끝을 나타내기 위해 --을 사용할 수 있다

참고1 https://mug896.github.io/bash-shell/getopts.html

참고2 https://gomudara.tistory.com/119


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


### 3. 특징
  1) 쉘, 쉘스크립트에서 파이프(|)와 같이 사용 가능
  2) 정규표현식 가능 **BUT 특수문자 앞에 역 슬래시를 붙여줘야함**
     ex) sed 's/\$man/man/g' test.txt
