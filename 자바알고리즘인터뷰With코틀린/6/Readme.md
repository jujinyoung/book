# 5장 문자열 처리
### 유효한 팰린드롬
#### 팰린드롬: 앞뒤가 똑같은 단어나 문장, 뒤집어도 같은 말이 되는 단어
**주요 메소드**
* Character.toLowerCase('G') -> 'g' -> 소문자 변환
* Character.isLetterOrDigit('G') -> true -> 문자나 숫자면 true (유효한 문자체크)
```
public boolean isPalindrome(String s) {
    int start = 0;
    int end = s.length() - 1;
    
    //서로 중앙으로 이동해 나가다 겹치는 지점에 도달하면 종료
    while (start < end) {
        //영숫자인지 판별하고 유효하지 않으면 한 칸씩 이동
        if (!Character.isLetterOrDigit(s.charAt(start))) {
            start++;
        } else if (!Character.isLetterOrDigit(s.charAt(end))) {
            end--;
        } else {
            if (Character.toLowerCase(s.charAt(start)) != Character.toLowerCase(s.charAt(end))) {
                //하나라도 일치하지 않는다면 팰린드롬이 아니므로 false 리턴
                return false;
            }
            //앞쪽 문자는 한 칸 뒤로, 뒤쪽 문자는 한 칸 앞으로 이동
            start++;
            end--;
        }
    }
    //무사히 종료될 경우 팰린드롬이므로 true 리턴
    return true;
}
```
> 문자 단위로 추출해서 처리하는 로직
### 문자열 뒤집기
### 로그 파일 재정렬
### 가장 흔한 단어
### 그룹 애너그램
### 가장 긴 팰린드롬 부분 문자열