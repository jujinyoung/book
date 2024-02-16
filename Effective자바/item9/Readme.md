# 9장 try-finally 보다 try-with-resources를 사용하라.
### try-finally
```
static void copy(String src, String dst) throws IOException {
    InputStream in = new FileInputStream(src);
    try {
        OutputStream out = new FileOutputStream(dst);
        try {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0) {
                out.write(buf, 0, n);
            }
        } finally {
            out.close();
        }
    } finally {
        in.close();
    }
}
```
<b>문제점</b>
* 자원이 계속 늘어날 수록 try-finally구문의 depth는 깊어진다. (가독성이 안좋다.)
* 예외 발생 시에 마지막에 발생한 예외만 로그에 남는다. (시스템 디버깅 문제)
> ex) 만약 out.write(buf, 0, n);부분에서 예외가 발생하고 <br>
> 연쇄적으로 out.close();에서도 예외가 발생한 경우 예외로그는 out.close()에 대해서만 남기때문에 실제 예외가 시작한 지점을 디버깅하기 어려워진다. 

### try-with-resources
```
static void copy(String src, String dst) throws Exception {
    try (InputStream in = new FileInputStream(src);
        OutputStream out = new FileOutputStream(dst)) {
        byte[] buf = new byte[BUFFER_SIZE];
        int n;
        while ((n = in.read(buf)) >= 0) {
            out.write(buf, 0, n);
        }
    }
}
```
* try-with-resources를 사용하기 위해선 자원이 AutoCloseable 인터페이스를 구현해야 한다. (close 메서드)
* 자원을 해제하는 코드가 빠져서 실제 어떤 동작을 하는지 잘 보인다.