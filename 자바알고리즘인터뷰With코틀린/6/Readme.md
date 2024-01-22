# 5장 문자열 처리
### 유효한 팰린드롬
#### 팰린드롬: 앞뒤가 똑같은 단어나 문장, 뒤집어도 같은 말이 되는 단어
1. 문자단위로 추출해서 처리<br>
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

2. 문자열 직접 비교<br>
**주요 메소드**
* replaceAll : 정규식 지원
* toLowerCase : 소문자 변경
* reverse : 문자열 뒤집기
```
public boolean isPalindrome(String s) {
    //정규식으로 유효한 문자만 추출한 다음 모두 소문자로 변경
    String s_filtered = s.replaceAll("[^A-Za-z0-9]", "").toLowerCase();
    //문자열을 뒤집은 다음 String으로 변경
    String s_reversed = new StringBuilder(s_filtered).reverse().toString();
    //두 문자열이 동일한지 비교
    return s_filtered.equals(s_reversed);
}
```
> 코드가 훨씬 간결하지만 성능이 안좋음
### 문자열 뒤집기
```
public void reverseString(char[] s) {
    int start = 0;
    int end = s.length - 1;
    //서로 중앙으로 이동해 나가다 겹치는 지점에 도달하면 종료
    while (start < end) {
        //임시 변수를 이용해 값 스왑
        char temp = s[start];
        s[start] = s[end];
        s[end] = temp;

        // 앞쪽 문자는 한 칸 뒤로, 뒤쪽 문자는 한 칸 앞으로 이동
        start++;
        end--;
    }
}
```
### 로그 파일 재정렬
### 가장 흔한 단어
### 그룹 애너그램
### 가장 긴 팰린드롬 부분 문자열