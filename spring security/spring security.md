# Spring Security
> 安全框架  web安全性包含用户认证和用户授权
> 用户认证是验证用户是否是系统中的合法主体（判断用户是否可以登录系统）
> 用户授权是验证用户是否有权限执行某个操作
> 
>  **本质上是个过滤器链

## 认证
### 登录校验流程
![[Pasted image 20220829141058.png]]

### 认证核心过滤器
![[Pasted image 20220829160044.png]]
#### UsernamePasswordAuthenticationFilter
> 负责处理登录请求

#### ExceptionTranslationFilter
> 处理过滤器链中抛出的任何`AccessDeniedException`和`AuthenticationException`

#### FilterSecurityInterceptor
> 负责权限校验

#### 认证流程
![[Pasted image 20220829164516.png]]

我们只需要实现`UserDetailsService`接口，改为从数据库中查询数据对比即可
```Java
	@Service  
	public class UserDetailServiceImpl implements UserDetailsService {  
	  
	    @Autowired  
	    BUserMapper bUserMapper;  
	  
	    @Override  
	    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {  
	  
	        // 查询用户信息  
	        LambdaQueryWrapper<BUser> queryWrapper = new LambdaQueryWrapper<>();  
	        queryWrapper.eq(BUser::getUserName,username);  
	        BUser bUser = bUserMapper.selectOne(queryWrapper);  
	  
	        // null判断  
	        if (Objects.isNull(bUser)){  
	            throw new RuntimeException("账号或密码错误");  
	        }  
	  
	        // TODO 查询对应的权限信息  
	  
	        // 封装为UserDetails返回  
	  
	        return new LoginUser(bUser);  
	    }  
	}
```
返回类型为`UserDetais`，实现`UserDetails`接口，将用户实体类封装进去
```java
	@Data  
	@NoArgsConstructor  
	@AllArgsConstructor  
	public class LoginUser implements UserDetails {  
	  
	    private BUser bUser;  

		// 获取权限信息
	    @Override  
	    public Collection<? extends GrantedAuthority> getAuthorities() {  
	        return null;  
	    }  
	  
	    @Override  
	    public String getPassword() {  
	        return bUser.getPassword();  
	    }  
	  
	    @Override  
	    public String getUsername() {  
	        return bUser.getUserName();  
	    }  
		
	    // 一下方法为账户是否可用
	    @Override  
	    public boolean isAccountNonExpired() {  
	        return true;  
	    }  
	  
	    @Override  
	    public boolean isAccountNonLocked() {  
	        return true;  
	    }  
	  
	    @Override  
	    public boolean isCredentialsNonExpired() {  
	        return true;  
	    }  
	  
	    @Override  
	    public boolean isEnabled() {  
	        return true;  
	    }  
	}
```

***注意：数据库密码前需要添加标记，明文密码存储需要添加`{noop}`前缀

#### 密码加密存储
>实际项目中我们不会把密码明文存储在数据库中。
>默认使用的PasswordEncoder要求数据库中的密码格式为: `{id}password`。它会根据id去判断密码的加密方式。但是我们一般不会采用这种方式。所以就需要替换PasswordEncoder。
>我们一般使用SpringSecurity为我们提供的BCryptPasswordEncoder。
>我们只需要使用把BCryptPasswordEncoder对象注入Spring容器中，SpringSecurity就会使用该PasswordEncoder来进行密码校验。

我们可以定义一个SpringSecurity的配置类，SpringSecurity要求这个配置类要继承WebSecurityConfigurerAdapter。
 ```java
	@Configuration  
	public class SecurityConfig extends WebSecurityConfigurerAdapter{  
	    // 创建BCryptPasswordEncoder注入容器
	    @Bean  
	    public PasswordEncoder passwordEncoder(){  
	        return new BCryptPasswordEncoder();  
	    }  
	}
```
