# item 9. try-finally 보다 try-with-resources를 사용하라.

자바 라이브러리에는 close 메서드를 호출해 직접 닫아줘야 하는 자원이 많다.

예) InputStream, OutputStream, java.sql.Connection

전통적으로 자원이 제대로 닫힘을 보장하는 수단으로 try-finally가 쓰였다.



### try-finally

```java
public static String firstLineOfFile(String path) throw IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));
    try {
        return br.readLine();
    } finally {
        br.close();
    }
}
```

자원을 하나 더 사용하면 코드가 너무 지저분해진다.

```java
static void copy(String src, String dst) throws IOException {
	InputStream in = new FileInputStream(src);
	try {
		OutputStream out = new FileOutputStream(dst);
		try {
			byte[] buf = new byte[BUFFER_SIZE];
			int n;
			while ((n = in.read(buf)) >= 0)
			out.write(buf, 0, n);
		} finally {
			out.close();
		}
	} finally {
		in.close();
	}
}
```



### try-with-resources

위의 코드는 try-with-resources로 해결가능하다.

이 구조를 사용하려면 해당 자원이 AutoCloseable 인터페이스를 구현해야한다.

> AutoCloseable은 단순히 void 를 반환하는 close 메서드 하나만 정의한 인터페이스이다.
>
> 자바 라이브러리와 서드파티 라이브러리들의 수많은 클래스와 인터페이스가 이미 AutoCloseable을 구현하거나 확장해뒀다.

```java
public static String firstLineOfFile(String path) throw IOException {
    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
        return br.readLine();
    } catch (Exception e) {
        return defaultVal;
    }
}
```

try-finally 와 다르게 여러 자원을 처리할때도 간단하게 처리가 가능하다.

```java
static void copy(String src, String dst) throws IOException {
	try (InputStream in = new FileInputStream(src);
		OutputStream out = new FileOutputStream(dst)) {
		byte[] buf = new byte[BUFFER_SIZE];
		int n;
		while ((n = in.read(buf)) >= 0)
		out.write(buf, 0, n);
	}
}
```
