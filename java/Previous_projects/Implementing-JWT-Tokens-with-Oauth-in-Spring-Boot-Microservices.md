---
title: "Implementing JWT Tokens With Oauth in Spring Boot Microservices"
date: 2024-08-04T07:56:25Z
draft: false
---

### Detailed Implementation of JWT Tokens in a Spring Boot Application with Keycloak

**Course:** Spring Boot Security with JWT and Keycloak

**Architecture Overview:**

1. **Keycloak Setup:**
   - **Keycloak:** An open-source identity and access management solution that supports OAuth 2.0 and JWT.
   - **Configuration:** Keycloak will be used to manage authentication and issue JWT tokens. Set up Keycloak with appropriate realms, clients, and roles.

2. **Spring Boot Application:**
   - **Authentication Server:** Configure Spring Boot to use Keycloak for authentication and validate JWTs.
   - **Resource Server:** Protect your application’s resources by validating JWTs issued by Keycloak.

**Infrastructure Setup:**

1. **Set Up Keycloak:**
   - **Install Keycloak:** Deploy Keycloak on a server or use a Keycloak cloud instance.
   - **Create a Realm:** Define a realm for your application.
   - **Create a Client:** Configure a client application in Keycloak with OAuth 2.0 settings.
   - **Define Roles:** Create roles and assign them to users or clients as needed.

2. **Configure Keycloak for JWT:**
   - **JWT Issuance:** Keycloak issues JWT tokens upon successful authentication. Configure token settings like expiration and claims in Keycloak’s client settings.

3. **Integrate Keycloak with Spring Boot:**
   - **Add Dependencies:** Include dependencies for Keycloak and Spring Security in your `pom.xml` or `build.gradle`.

**Code Example:**

**1. Add Dependencies:**

```xml
<!-- pom.xml -->
<dependencies>
    <!-- Spring Boot Starter Security -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    <!-- Keycloak Spring Boot Adapter -->
    <dependency>
        <groupId>org.keycloak</groupId>
        <artifactId>keycloak-spring-boot-starter</artifactId>
    </dependency>
    <!-- Additional dependencies -->
</dependencies>
```

**2. Keycloak Configuration in `application.properties`:**

```properties
# application.properties
keycloak.auth-server-url=http://localhost:8080/auth
keycloak.realm=your-realm
keycloak.resource=your-client-id
keycloak.credentials.secret=your-client-secret
keycloak.bearer-only=true
spring.security.oauth2.client.registration.keycloak.client-id=your-client-id
spring.security.oauth2.client.registration.keycloak.client-secret=your-client-secret
```

**3. Security Configuration:**

```java
import org.keycloak.adapters.springsecurity.KeycloakSecurityConfig;
import org.keycloak.adapters.springsecurity.client.KeycloakRestTemplate;
import org.keycloak.adapters.springsecurity.config.KeycloakSpringBootConfigResolver;
import org.keycloak.adapters.springsecurity.config.KeycloakWebSecurityConfigurerAdapter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends KeycloakWebSecurityConfigurerAdapter {

    @Bean
    public KeycloakSpringBootConfigResolver keycloakConfigResolver() {
        return new KeycloakSpringBootConfigResolver();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        super.configure(http);
        http
            .authorizeRequests()
            .antMatchers("/public/**").permitAll()
            .anyRequest().authenticated();
    }
}
```

**4. Example `TransactionSender` Service:**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestTemplate;
import org.springframework.web.util.UriComponentsBuilder;

@Service
public class TransactionSender {

    @Autowired
    private RestTemplate restTemplate;

    public TransactionResponse send(TransactionRequest request) {
        String url = "http://api.example.com/transactions";
        
        // Building request with JWT token
        String jwtToken = "your-jwt-token"; // This would be dynamically obtained from authentication context
        HttpHeaders headers = new HttpHeaders();
        headers.set("Authorization", "Bearer " + jwtToken);
        headers.setContentType(MediaType.APPLICATION_JSON);

        HttpEntity<TransactionRequest> entity = new HttpEntity<>(request, headers);

        // Sending request to API
        ResponseEntity<TransactionResponse> response = restTemplate.exchange(
            url,
            HttpMethod.POST,
            entity,
            TransactionResponse.class
        );

        return response.getBody();
    }
}
```

**5. Set Up Keycloak Client and Roles:**

- **Client Configuration:** Define the OAuth 2.0 settings, such as `Access Type`, `Valid Redirect URIs`, and `Web Origins` in Keycloak for your application.
- **Role Mapping:** Assign appropriate roles to users or groups in Keycloak.

**Summary:**
1. **Keycloak:** Set up Keycloak for authentication and JWT issuance.
2. **Spring Boot:** Configure your Spring Boot application to use Keycloak for securing resources.
3. **Security Configuration:** Implement security settings to validate JWTs.
4. **Service Implementation:** Use JWT tokens to interact with protected APIs.
