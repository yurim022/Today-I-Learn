
Enum이 있는 코드에 대해서 DB에 넣기전에 선작업, 꺼내고 후작업 하는게 번거로워 TypeHandler를 사용해보기로 했다. 

## ENUM

```
 @Getter
    public enum DELIVERY_TYPE {

        PICK_UP("P"),
        DELIVERY("D");

        @JsonValue
        private String code;

        DELIVERY_TYPE(String code) {
            this.code = code;
        }

    }
```


## TypeHandler

```
package org.apache.ibatis.type;
.....
public interface TypeHandler<T> {
 
  void setParameter(PreparedStatement ps, int i, T parameter, JdbcType jdbcType) throws SQLException;
 
  T getResult(ResultSet rs, String columnName) throws SQLException;
 
  T getResult(ResultSet rs, int columnIndex) throws SQLException;
 
  T getResult(CallableStatement cs, int columnIndex) throws SQLException;
 
}
```
이 타입핸들러를 구현해줘야 하는데, 모든 ENUM에 각각 적용하기는 귀찮기 때문에 공용으로 쓸 놈을 만들어주자. 



## DB에 저장될 코드 인터페이스

```
public interface CodeEnum {
    public String getCode();
}
```

## Custom 타입핸들러

```
public abstract class CodeEnumTypeHandler <E extends Enum <E>> implements TypeHandler <CodeEnum> {
 
    private Class <E> type;
 
    public CodeEnumTypeHandler(Class <E> type) {
        this.type = type;
    }
 
    @Override
    public void setParameter(PreparedStatement ps, int i, CodeEnum parameter, JdbcType jdbcType) throws SQLException {
        ps.setString(i, parameter.getCode());
    }
 
    @Override
    public CodeEnum getResult(ResultSet rs, String columnName) throws SQLException {
        String code = rs.getString(columnName);
        return getCodeEnum(code);
    }
 
    @Override
    public CodeEnum getResult(ResultSet rs, int columnIndex) throws SQLException {
        String code = rs.getString(columnIndex);
        return getCodeEnum(code);
    }
 
    @Override
    public CodeEnum getResult(CallableStatement cs, int columnIndex) throws SQLException {
        String code = cs.getString(columnIndex);
        return getCodeEnum(code);
    }
 
     private CodeEnum getCodeEnum(String code) {
        return EnumSet.allOf(type)
                .stream()
                .filter(value -> value.getCode().equals(code))
                .findFirst()
                .orElseGet(null);
    }
}
```

## TypeHandler 적용

```
 @Getter
    public enum DELIVERY_TYPE implements CodeEnum{

        PICK_UP("P"),
        DELIVERY("D");

        @JsonValue
        private String code;

        DELIVERY_TYPE(String code) {
            this.code = code;
        }
        
        @MappedTypes(DELIVERY_TYPE.class)
    public static class TypeHandler extends CodeEnumTypeHandler<PaymentMethodType> {
        public TypeHandler() {
            super(DELIVERY_TYPE.class);
        }
      }

    }
```

Java Config로 myBatis의 TypeHandler를 설정하기 위해서는 @MappedTypes(DELIVERY_TYPE.class) 와 같이 명시적으로 정의해주어야 한다.



## SpringBoot 에 등록

```
@Bean
public SqlSessionFactory sqlSessionFactoryBean(DataSource dataSource) throws Exception {
    SqlSessionFactoryBean sessionFactoryBean = new SqlSessionFactoryBean();
    sessionFactoryBean.setDataSource(dataSource);
    sessionFactoryBean.setTypeAliasesPackage(MyBatisProperties.TYPE_ALIASES_PACKAGE);
    sessionFactoryBean.setConfigLocation(applicationContext.getResource(myBatisProperties.getConfigLocation()));
    sessionFactoryBean.setMapperLocations(applicationContext.getResources(myBatisProperties.getMapperLocations()));
    sessionFactoryBean.setTypeHandlers(new TypeHandler[] {
        new BooleanTypeHandler(),
        new DELIVERY_TYPE.TypeHandler()
    });
 
    return sessionFactoryBean.getObject();
}
```

참고:
https://www.holaxprogramming.com/2015/11/12/spring-boot-mybatis-typehandler/
https://amagrammer91.tistory.com/115?category=1009448
