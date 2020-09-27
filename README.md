Steps to implement OAuth2.0 implementation

Follow the tutorial :
https://www.devglan.com/spring-security/spring-boot-security-oauth2-example

## Spring Security Implementation

1. First simply include the spring security functionality 

```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>

```

2. Use anotation `@EnableWebSecurity` to enable spring security and extend `WebSecurityConfigurerAdapter`
and override the following method
```
 @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()
                .anyRequest()
                .authenticated()
                .and()
                .httpBasic();
    }
```

3. Introduce the in memory user 
```

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
                .passwordEncoder(getPasswordEncoder())
                .withUser("zeb")
                .password(getPasswordEncoder().encode("zeb"))
                .authorities("ROLE_USER");
    }
```
Note : here we must encrypt the password of the user. We are using the bean like:
```
    @Bean
    PasswordEncoder getPasswordEncoder() {
        return new BCryptPasswordEncoder();
    }
```
After that you are able to hit the api's with username & password.
If it's wrong spring security will not authenticate you to access the reousrce

## Implementation of OAuth2 

1. Include dependency
```
        <dependency>
            <groupId>org.springframework.security.oauth</groupId>
            <artifactId>spring-security-oauth2</artifactId>
            <version>2.3.4.RELEASE</version>
        </dependency>
```

2. Make config with annotation `@EnableAuthorizationServer`  and override the method :
```
@Override
    public void configure(ClientDetailsServiceConfigurer configurer) throws Exception {
        configurer
                .inMemory()
                .withClient(CLIENT_ID)
                .secret(passwordEncoder.encode(CLIENT_SECRET))
                .authorities("ROLE_USER")
                .authorizedGrantTypes(GRANT_TYPE, REFRESH_TOKEN);
    }
```
After than aouth token will be required to access the resource.

For-example
```bash
curl --location --request POST 'http://localhost:8080/oauth/token' \
--header 'Authorization: Basic emViLWNsaWVudDp6ZWItY2xpZW50' \
--form 'grant_type=client_credentials' \
--form 'scope=read'
```