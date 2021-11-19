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

> ex) `while getopts "a:b:cdef" opt`
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
 
 
### 2. 사용방법
!<img src="https://user-images.githubusercontent.com/94359749/142604370-fe1d95b9-1a4f-459c-b832-333acd949ab3.PNG" width="75%" height="75%">


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
> sed -n '/abd/p' list.txt 
list.txt 파일을 한줄씩 읽으면서 abd 문자를 찾으면 그 줄을 출력(p)
'-n 의미: 읽은 것을 출력하지 않음'
> sed -n '1p' <파일>
첫 라인만 출력
> sed -n '1,3p' <파일>
첫 번째 행부터 세 번째 행까지 출력
> sed -n '8,$p' <파일>
여덟 번째 행부터 끝 행까지 출력

(2) 치환 
> sed 's/addrass/address/' list.txt
addrass를 address로 바꿈 **BUT, 원본파일을 바꾸지 않고 출력만 바꿈**

> sed 's/\t/\ /' list.txt 
탭문자를 엔터로 변환


(3) 추가
> scriptfile - s/

(4) 삭제
>   sed '/없애버릴 글자/d' <파일>
예: sed '/TD/d' 1.html
TD 문자가 포함된 줄을 삭제후 출력

> sed '/Src/!d' 1.html
Src 문자가 있는 줄만 지우지 않음

> sed '1,2d' 1.html
처음 1,2줄 지움

(5) 파이프(|)와 사용
> who | sed -e 's; .*$;;'



참고 https://blog.wonizz.tk/2019/03!


### 3. 특징
  1) 쉘, 쉘스크립트에서 파이프(|)와 같이 사용 가능
  2) 정규표현식 가능 **BUT 특수문자 앞에 역 슬래시를 붙여줘야함**
     ex) sed 's/\$man/man/g' test.txt
  **3) 원본을 건드리지 않음**


### 4. sed subcommand 명령어 종류와 의미
|subcommand|의미|
|-------|-----------------|
| a\ | 현재 행에 하나 이상의 새로운 행을 추가 |
| c\ | 현재 행의 내용을 새로운 내용을 교체 |
| d | 행을 삭제 |
| i/ | 현재 행의 위에 텍스트 삽입 |
| h | 패턴 스페이스의 내용을 홀드 스페이스에 복사 |
| H | 패턴 스페이스의 내용을 홀드 스페이스에 추가 |
| g | 홀드 스페이스의 내용을 패턴 스페이스에 복사 (패턴 스페이스가 비어있지 않은 경우: 덮어쓰기) |
| G | 홀드 스페이스의 내용을 패턴 스페이스에 복사 (패턴 스페이스가 비어있지 않은 경우: 그 뒤에 추가) |
| l | 출력되지 않는 특수문자를 명확하게 출력 |
| p | 행을 출력 |
| n | 다음 입력 행을 첫 번째 명령어가 아닌 다음 명령어에서 처리하게 함 |
| q | sed를 종료 |
| r | 파일로부터 행을 읽어옴 |
| ! | 선택된 행을 제외한 나머지 전체 행에 명령어 적용 |
| s | 문자열을 치환 |

참고 https://jhnyang.tistory.com/287


----


## 4. awk 명령어
### 1. 사용목적
  1) 지정된 파일로부터 데이터를 분류한 다음,
     분류된 텍스트 데이터를 바탕으로 패턴 매칭 여부를 검사하거나 데이터 조작 및 연산 등의 액션을 수행, 
     그 결과 출력
 
 <awk 명령으로 할 수 있는 일>
- 텍스트 파일의 전체 내용 출력
- 파일의 특정 필드만 출력
- 특정 필드에 문자열을 추가해서 출력
- 패턴이 포함된 레코드 출력
- 특정 필드에 연산 수행 결과 출력
- 필드 값 비교에 따라 레코드 출력


!<img src="https://user-images.githubusercontent.com/94359749/142647921-43fb2268-a339-46c9-b553-1b5e58a6cc21.PNG" width="70%" height="70%">


### 2. 사용방법
> awk [OPTION...] [awk program] [ARGUMENT...]
      OPTION
        -F        : 필드 구분 문자 지정.
        -f        : awk program 파일 경로 지정.
        -v        : awk program에서 사용될 특정 variable값 지정.
      awk program
        -f 옵션이 사용되지 않은 경우, awk가 실행할 awk program 코드 지정.
      ARGUMENT
        입력 파일 지정 또는 variable 값 지정.


### 3. 특징
  1) 기본적으로 입력 데이터를 라인 단위의 레코드로 인식 (각 레코드에 들어 있는 텍스트는 공백문자(space, tab)로 구분된 필드들로 분류)
  2) 식별된 레코드 및 필드 값들은 awk 프로그램에 의해 패턴 매칭 및 다양한 액션의 파라미터로 사용
  3) awk program은 스크립트 형식의 프로그래밍 언어로 작성되기 때문에 작성 방법이 다양
     awk program 기본 구조: 'pattern { action }' 
     즉, awk 사용방법 > awk [OPTION...] 'pattern { action }' [ARGUMENT...] 
     주의) 'pattern { action }' 생략 가능, pattern 생략시: 모든 레코드가 적용, action 생략시: print 적용
     `
     # pattern 생략
      $ awk '{ print }' ./file.txt      # file.txt의 모든 레코드 출력.

     # action 생략
      $ awk '/p/' ./file.txt            # file.txt에서 p를 포함하는 레코드 출력.
    `


### 4. awk 명령 사용 예
| awk 사용 예 | 명령어 옵션 |
|----------|------------------------|
| 파일의 전체 내용 출력 | 	awk '{ print }' [FILE] |
| 필드 값 출력 | awk '{ print $1 }' [FILE] |
| 필드 값에 임의 문자열을 같이 출력 | awk '{print "STR"$1, "STR"$2}' [FILE] |
| 지정된 문자열을 포함하는 레코드만 출력 | awk '/STR/' [FILE] |
| 특정 필드 값 비교를 통해 선택된 레코드만 출력 | 	awk '$1 == 10 { print $2 }' [FILE] |
| 특정 필드들의 합 | awk '{sum += $3} END { print sum }' [FILE] |
| 여러 필드들의 합| awk '{ for (i=2; i<=NF; i++) total += $i }; END { print "TOTAL : "total }' [FILE] |
| 레코드 단위로 필드 합 및 평균 값 구하기 | awk '{ sum = 0 } {sum += ($3+$4+$5) } { print $0, sum, sum/3 }' [FILE] |
| 필드에 연산을 수행한 결과 출력 | awk '{print $1, $2, $3+2, $4, $5}' [FILE] |
| 레코드 또는 필드의 문자열 길이 검사 | 사	awk ' length($0) > 20' [FILE] |
| 파일에 저장된 awk program 실행 | 	awk -f [AWK FILE] [FILE] |
| 필드 구분 문자 변경 | 	awk -F ':' '{ print $1 }' [FILE] |
| awk 실행 결과 레코드 정렬 | 	awk '{ print $0 }' [FILE] |
| 특정 레코드만 출력 | awk 'NR == 2 { print $0; exit }' [FILE] |
| 출력 필드 너비 지정 | awk '{ printf "%-3s %-8s %-4s %-4s %-4s\n", $1, $2, $3, $4, $5}' [FILE] |
| 필드 중 최대 값 출력 | awk '{max = 0; for (i=3; i<NF; i++) max = ($i > max) ? $i : max ; print max}' [FILE] |
