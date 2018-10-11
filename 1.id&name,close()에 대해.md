# index.jsp

회원가입 화면 디자인<br>

```jsp
<tbody>
	<tr>
		<td style="width: 110px;"><h5>아이디</h5></td>
		<td><input class="form-control" type="text" id="userID" name="userID" maxLength="20"></td>
		<td style="width: 110px;"><button class="btn btn-primary" onclick="registerCheckFunction();" type="button">중복체크</button></td>
	</tr>
```

<h3>id와 name의 차이</h3><br>
**name** <br>
>1.document.폼객체명.폼원소명.value <br>2.document.getElementsByName("name")<br>
<br>
name은 page 안에서 **중복되어 사용**이 가능하며 action에 해당하는 페이지로 전달하는 파라미터로 사용된다.<br>
GET/POST 방식으로 값을 전달하고 싶은 TAG에 사용.<br> Form 객체들(input, radio box, checkbox)에서 전송되어지는 Parameter의 Key값으로 사용이 된다.<br>
request.getParamter(parameterName) 이런식으로 값을 가져온다.<br>
<br>

**id** <br>
>1. document.all.id.value <br>2. id.value <br>3. document.getElementById("form id").value <br>
id는 page 안에서 **중복 사용이 불가**하며 주로 **JavaScript에서 다룰려고 지정**하는 것이다.<br>
name도 자바스크립트로 속성이나 값을 변경 할 수 있으나 중복값을 갖기 때문에 id로 접근하는 것을 주로 사용한다.<br>
document.getElementById(id)를 통해서 해당 엘리먼트 Object를 가져온다.<br>
XML문서(DOM Tree) 내에서 node를 식별하는 용도로 사용되어 페이지 안에 이름이 한개만 존재해야 한다.<br>
보통은 특정 tag를 지정하기 위해서 사용한다. 태그 내용을 바꾸는 html작업에서 주로 사용한다.<br>

---

# UserDAO.java

아이디 중복체크 함수<br>
```java
public int registerCheck(String userID) {
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		String SQL = "SELECT * FROM USER WHERE usreID = ?";
		try {
			pstmt = conn.prepareStatement(SQL);
			pstmt.setString(1, userID);
			rs = pstmt.executeQuery();
			if(rs.next()) {
				return 0; // 이미 존재하는 회원
			}
			else {
				return 1; //가입가능한 회원
			}
		}catch(Exception e) {
			e.printStackTrace();
		}finally {
			try {
				if(rs !=null)
					rs.close();
				if(pstmt !=null)
					pstmt.close();
			}catch(Exception e) {
				e.printStackTrace();
			}
		}
		return -1; //데이터베이스 오류
    }
```

```java
rs.close();
pstmt.close();
```
<h3>close() 해주는 이유</h3> <br>
1. Statement를 닫지 않을 경우, 생성된 Statement의 개수가 증가하여 더 이상 Statement를 생성할 수 없게 된다.<br>
2. close() 하지 않으므로 불필요한 자원(네트워크 및 메모리)을 낭비하게 된다.<br>
3. 커넥션 풀을 사용하지 않는 상황에서 Connection을 닫지 않으면 결국엔 DBMS에 연결된 새로운 Connection을 생성할 수 없게 된다.<br>


위의 문제중 첫번째와 두번째 문제는 시간이 지나면 가비지 콜렉터에 의해서 해결될 수도 있지만, 만약 커넥션 풀을 사용하고 있다면 그나마 가비지 콜렉션도 되지 않는다. 따라서 커넥션 풀을 사용하는 경우 Statement와 ResultSet은 반드시 닫아주어야만 한다.