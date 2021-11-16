# Homework_20203234 컴퓨터공학과 지수연
open source SW homework

## 1. getopt 명령어
### 1. 사용목적
  1) 다양한 입력 값이 존재할 때 사용자와 개발자의 편의를 보장
  2) 스크립트를 체계적으로 관리 가능
  3) 명령 실행시 옵션을 사용하는데 파라미터 형태로 전달되므로 옵션 해석 작업을 쉽게 도움
     
     (옵션: short 옵션과 long 옵션이 존재, getopts는 short 옵션 처리)
     
     
### 2. 사용방법
> getopts optstring varname [args...]

> ex) while getopts "a:b:ij" opt
  1) optstring(첫 번째 파라미터): 옵션으로 사용될 문자열 입력
  2) varname(두 번째 파라미터): 옵션으로 활용되는 변수 사용
  3) args: 명령 실행시 사용된 인수 값이 옴, 생략시 "$@" 사용


### 3. 주의사항
`:` getopt는 한 개의 문자만을 구분자로 사용
`:`이 붙는다는 것은 뒤에 value가 붙게됨을 의미
인수를 받으면 `:`을 붙여주고 OPTARG 라는 쉘 변수를 사용


### 4. error reporting과 관련하여 모드 제공
||Verbose mode|Silent mode|
|-----|----------|----------|
|invalid 옵션 사용|opt 값을 ? 문자로 설정하고 OPTARG 값은 unset. 오류 메시지를 출력|	opt 값을 ? 문자로 설정하고 OPTARG 값은 해당 옵션 문자로 설정|
|옵션인수 값을 제공하지 않음|opt 값을 ? 문자로 설정하고 OPTARG 값은 unset. 오류 메시지를 출력|opt 값을 : 문자로 설정하고 OPTARG 값은 해당 옵션 문자로 설정|

참고 https://mug896.github.io/bash-shell/getopts.html
