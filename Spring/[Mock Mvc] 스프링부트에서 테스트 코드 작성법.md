# MockMvc를 이용한 컨트롤러 테스트

### 단위테스트

- class단위 → JUnit, Servlet Unit, DB Unit 등…
- Mock테스트 → Mockito, Easy Mock…
- 정적 test → PMD
- 성능 test - JUnit Performance

**@ WebMvcTest** 

- 컨트롤러만 테스트
- 각자 서로의 MockMvc를 모킹하기 때문에 @SpringBootTest와 같이 사용할 수 없다.

```
@WebMvcTest
public class BoardControllerTest{

@Autowired
    private MockMvc mockMvc; //spring에서 제공하는 mock 객체

    @Test
    public void testsHello()throws Exception{
mockMvc.perform(MockMvcRequestBuilders.get("/hello").param("name","둘리"))
.andExpect(MockMvcResultMatchers.status().isOk())
.andExpect(MockMvcResultMatchers.content().string("Hello : 둘리"))
.andDo(MockMvcResultHandlers.print());
}
}
```



**@ AutoConfiguerMockMvc**   

- 컨트롤러 뿐 아니라 서비스, 리포지토리 객체도 초기화한다.

```
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)
@AutoConfigureMockMvc
public class BoardControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @Test
    public void testHello() throws Exception{
        mockMvc.perform(get("/hello").param("name", "둘리"))
                .andExpect(status().isOk())
                .andExpect(content().string("Hello : 둘리"))
                .andDo(print());
    }
}
```

