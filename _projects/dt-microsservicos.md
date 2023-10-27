---
layout: project
title: "Desafio Técnico: Microsserviços em Java"
date: 2023-06-31
ready: true
src: "https://github.com/djudju12"
details: "Autenticação"
---

Esse projeto foi desenvolvido para o programa de bolsas da Compass UOL. Consiste em uma API que simula um e-commerce e permite o gerenciamento de produtos, pedidos e autenticação do usuário. 

**Muito do que foi desenvolvido será omitido por simplicidade. Verifique o código fonte para mais detalhes.**
# Autenticação

A autenticação do usuário foi feita utilizando JWT (você pode checar meu post sobre jwt aqui -> [coming son]()). Vou falar um pouco sobre o processo de autenticação e como o token é gerado.

Primeiramente, vamos dar uma olhada para a classe do usuário:

```java
@Data
@Entity
@Table(name = "users")
public class UserEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String firstName;

    private String lastName;

    @Column(nullable = false, unique = true)
    private String username;

    @Column(nullable = false)
    private String password;
}
```

O usuário foi modelado de maneira simples, afinal o foco do projeto era mais didático. A senha é armazenada no banco de dados de maneira criptografada utilizando o BCrypt. Para utilizar o BCrypt é necessário apenas criar o seguinte bean em uma classe de configuração:

```java
@Configuration
public class SecurityConfig {
    @Bean
    public static PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
}
```

Para gerar o token foi utilizada a biblioteca [io.jsonwebtoken](https://mvnrepository.com/artifact/io.jsonwebtoken/jjwt-api). A classe é a seguinte:

```java
@Component
public class JwtTokenProvider {
    public String generateJwtToken(Authentication authentication, String secret, long expirationMs) {
        // Dados do usuário que está sendo autenticado
        String username = authentication.getName();
        Date currentDate = new Date();
        Date expiredDate = new Date(currentDate.getTime() + expirationMs);

        // Gerando o token
        return Jwts.builder()
                .setSubject(username)
                .setIssuedAt(currentDate)
                .setExpiration(expiredDate)
                .signWith(key(secret))
                .compact();
    }

    private Key key(String jwtSecret) {
        return Keys.hmacShaKeyFor(
                Decoders.BASE64.decode(jwtSecret)
        );
    }
}
```

Com o habilidade de gerar um token, podemos criar um endpoint para autenticar o usuário:

```java
@RestController
public class SecurityController {

    private final AuthService authService;

    public SecurityController(AuthService authService) {
        this.authService = authService;
    }

    @PostMapping(value = "/login", produces = "application/json")
    public ResponseEntity<JwtDto> login(@Valid @RequestBody LoginDto loginDto){
        // public class LoginDto {
        //     private String username;
        //     private String password;
        // }
        
        // Autenticando o usuário
        String token = authService.login(loginDto);
        
        // public class JwtDto {
        //     private String accessToken;
        //     private final String tokenType = "Bearer";
        // }

        // DTO para retornar o token
        JwtDto jwtAuthResponse = new JwtDto();
        jwtAuthResponse.setAccessToken(token);

        return new ResponseEntity<>(jwtAuthResponse, HttpStatus.CREATED);
    }
}
```

O controller é responsável por receber as requisições e retornar as respostas. Quem fará de fato o trabalho de autenticação é o service:

```java
@Service
@RequiredArgsConstructor
public class AuthServiceImpl implements AuthService {
    private AuthenticationManager authenticationManager;
    private JwtTokenProvider jwtTokenProvider;

    @Value("${app.jwt.Secret}")
    private String jwtSecret;

    @Value("${app.jwt.ExpirationMs}")
    private long jwtExpirationMs;

    @Override
    public String login(LoginDto loginDto) {
        Authentication authentication = authenticationManager.authenticate(
                new UsernamePasswordAuthenticationToken(
                        loginDto.getUsername(),
                        loginDto.getPassword()));

        SecurityContextHolder.getContext()
                             .setAuthentication(authentication);
        
        return jwtTokenProvider.generateJwtToken(
                    authentication, jwtSecret, jwtExpirationMs);
    }
}
```

Concluindo, o usuário envia as credenciais para o endpoint de login, o service autentica o usuário e gera o token. O token é retornado para o usuário e ele pode utilizá-lo para acessar os endpoints protegidos.
