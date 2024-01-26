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
[문제풀이](https://github.com/jujinyoung/CodingTest/blob/master/leetcode/_937.java) <br>
1. 문자 로그와 숫자 로그를 분리해서 List에 저장
2. 문자 로그 list를 compartor를 구현해서 순서대로 정렬
3. 문자가 같을 경우 식별자 비교해서 정렬
4. 나머지 숫자 로그를 문자 로그에 더해서 출력

**주요 메소드**
1. list.sort(compartor...) : compartor를 구현해서 정렬 기준 만들기
2. String.compareTo(String) : 문자의 사전 순으로 비교해서 작으면 -1, 같으면 0, 크면 1 return
3. list.addAll(List) : 리스트에 전체 add
4. letter.toArray(new String[0]) : list -> String[]로 반환
### 가장 흔한 단어
### 그룹 애너그램
### 가장 긴 팰린드롬 부분 문자열